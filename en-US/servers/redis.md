---
name: Redis
---

# Install

You install Redis from source. Here are instructions for how to install on a Ubuntu machine.

```
$> sudo apt-get update
$> sudo apt-get install build-essential tcl
$> cd /tmp
$> curl -O http://download.redis.io/redis-stable.tar.gz
$> tar xzvf redis-stable.tar.gz
$> cd redis-stable
$> make
$> make test
$> sudo make install
```

Configure Redis

```
$> sudo mkdir /etc/redis
$> sudo cp /tmp/redis-stable/redis.conf /etc/redis
```

Next time to configure the file:

```
$> sudo nano /etc/redis/redis.conf
```

Look in the file for `supervised no` and change it to `supervised systemd`.
Look for `dir /` and set to `dir /var/lib/redis`.

Create a systemd file:

```
$> sudo nano /etc/systemd/system/redis.service
```

Here is the file:

```
[Unit]
Description=Redis In-Memory Data Store
After=network.target

[Service]
User=redis
Group=redis
ExecStart=/usr/local/bin/redis-server /etc/redis/redis.conf
ExecStop=/usr/local/bin/redis-cli shutdown
Restart=always

[Install]
WantedBy=multi-user.target
```

Create a new user and group, `redis`, that we specified above.

```
$> sudo adduser --system --group --no-create-home redis
$> sudo mkdir /var/lib/redis
$> sudo chown redis:redis /var/lib/redis
$> sudo chmod 770 /var/lib/redis
```

Start it up!

```
$> sudo systemctl start redis
$> sudo systemctl status redis
```

You should see a line that reads: `Active: active (running)`

```
$> redis-cli
127.0.0.1:6379> ping
```

You should see: `PONG`.

```
127.0.0.1:6379> exit
```

```
$> sudo systemctl enable redis
```

# Securing Redis

```
$> sudo nano /etc/redis/redis.conf
```

Uncomment line: `bind 127.0.0.1`. This only allows connections to Redis from localhost.

Find `requirepass foobared`, uncomment it, and set `foobared` to a new value. This will be your password to login to Redis.

Restart Redis: `sudo service redis-server restart`.

Test the password works:

```
$> redis-cli
127.0.0.1:6379> set key1 10
(error) NOAUTH Authentication required.
127.0.0.1:6379> auth your_redis_password
OK
127.0.0.1:6379> exit
```

* Disable destructive commands.

You can rename or disable destructive commands.

`sudo nano  /etc/redis/redis.conf`

```
# In the SECURITY section of the file, add these lines *if you wish*.
rename-command FLUSHDB ""
rename-command FLUSHALL ""
rename-command DEBUG ""

# You can rename commands to make it harder for others to guess, but dont forget them!
rename-command SHUTDOWN SHUTDOWN_MENOT
rename-command CONFIG ASC12_CONFIG
```

Careful when renaming commands:

![](/docs/images/redis_change_commands_warning.png)

Restart server: `sudo service redis-server restart`.

Next, we will make sure the Linux file structure is owned and operated by the `redis` user.

```
$> sudo chmod 700 /var/lib/redis
$> sudo chown redis:root /etc/redis/redis.conf
$> sudo chmod 600 /etc/redis/redis.conf
$> sudo service redis-server restart
```
