---
name: Documentation git repo structure
---

Peachdocs uses git repos for it's data to display on the website. Here is how you create the git repo.

```
mkdir nameOfYourDocumentationHere # Doesn't matter what you name it. I use wiki or something.
cd nameOfYourDocumentationHere

touch TOC.ini
mkdir en-US

mkdir images # where you will put all of your image files.

mkdir en-US/dogs
touch en-US/dogs/README.md
```
And this is your file structure:
```
├── en-US
│   └── dogs
│       └── README.md
└── TOC.ini
```
Really, we are done with the structure now. We created a TOC.ini file, a directory called dogs with a README.md file inside that is housed inside the en-US directory.

The `en-US` directory is because peachdocs supports many languages. We are only supporting english which is why there is only 1 directory here. If you want to support other languages, you can do so.

For example if you want to support Chinese, create the `zh-CN` directory and also create a `dogs` directory. The contents of each language folder should be exactly the same structure, then in each file of those directories just write it in the language of your choice.

This will change your folder structure to:
```
├── en-US
│   └── dogs
│       └── README.md
├── zh-CN
│   └── dogs
│       └── README.md
└── TOC.ini
```

Anyway, assuming we are only doing English lets move on.

`TOC.ini` is the "table of contents" file. Because we have a `dogs/` directory we are choosing to talk about dogs. Create a directory for each "group" or "category" you want to talk about. Dogs, cats, web dev, UX, design, whatever.

Lets talk about this file.
```
-: dogs

[dogs]
-: README
```
This is the format you use to create the table of contents of the web documentation. The top of the file is the intro part. You list out all of the categories/groups in the table of contents, in this case we only have [dogs] so we list that and it automatically displays to the user the README file (the first file listed under [dogs]). Then we list all of the .md files listed under the dogs/ directory.

Whatever is in the TOC.ini will be shown to the user. Whatever is not in there will not be shown.

That is it for file structure. Each .md file has a couple rules on how to set them up. Read about that [here](file_structure).

Your site should now be up and ready to run however. Try it out.
```
cd nameofWebsite.peach
peach web
```
Site is running on whatever port you set in `custom/app.ini`.

We probably want to use a custom domain for the peachdocs instead of IP address of our server. The NGINX docs on [proxying websites]('../nginx/proxy_site') will go over that.

To run the website now you much use `peach web` command. To do it automatically and on boot, Docs on server [running program on boot]('../servers/run_program_boot') will give all the details. The contents of the startup script by the way should look like this:
```
#!/bin/sh

if [ $(ps -e -o uid,cmd | grep $UID | grep node | grep -v grep | wc -l | tr -s "\n") -eq 0 ]
then
	cd /path/to/peach/site.peach
	/usr/bin/peach web &
fi
```
