---
name: Date footer
---

You know those sites that put the copyright date on the bottom of their site but it's hard coded? No. We are going to make ours auto updating.

Here is a pre-made solution for you using pug. Put this in your footer.

```
h5 &copy; #[span(id='date-footer') DATE]
script(src='/js/date_footer.js')
```

And then create the file `/js/date_footer.js` and put this in it:

```
document.getElementById('date-footer').innerHTML = new Date().getFullYear();
```
