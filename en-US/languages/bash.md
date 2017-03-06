---
name: Bash
---

# Check OS you ae on

```
if [[ "$OSTYPE" == "linux-gnu" ]]; then
        # ...
elif [[ "$OSTYPE" == "darwin"* ]]; then
        # Mac OSX
elif [[ "$OSTYPE" == "cygwin" ]]; then
        # POSIX compatibility layer and Linux environment emulation for Windows
elif [[ "$OSTYPE" == "msys" ]]; then
        # Lightweight shell and GNU utilities compiled for Windows (part of MinGW)
elif [[ "$OSTYPE" == "win32" ]]; then
        # I'm not sure this can happen.
elif [[ "$OSTYPE" == "freebsd"* ]]; then
        # ...
else
        # Unknown.
fi
```

# Sending arguments to bash script

If you create a bash script that allows you to enter command line arguments to it, you can...

* Refer to them individually via `$1` for the first arg, `$2` for the second arg, etc. Dollar sign, and number.

* Refer to all of them: `$*` will pass them all. 
