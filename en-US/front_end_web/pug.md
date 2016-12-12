---
name: Pug
---

# Create a pug site

* Install pug

```
$ npm install pug # or yarn add pug
```

* Create a pug file (or you can create directories and put the pug files in it. Doesn't matter.):

```
$> touch index.pug # or mkdir site; touch sites/index.pug
```

* Put pug code into that new file:

```
doctype html
html(lang='en')
    head
        meta(charset='utf-8')
        title Yo.
        link(rel='stylesheet', href='https://unpkg.com/tachyons@4.5.5/css/tachyons.min.css')
    body
        p Yo.

```

* Compile that pug file into HTML:

```
$> npm install -g pug-cli # or yarn global add pug-cli
$> pug index.pug # if you put code into a directory, do `pug nameofdirectory`

$> pug -w index.pug # you can add the -w flag for pug to watch a directory and rebuild on save for you.
```

* Go view your new index.html page in your browser!

That is the very simple setup for how to you create a pug site. You can use tools such as `gulp` to make this easier for you if you choose.
