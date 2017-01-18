---
name: Post checklist
---

# Python2 script I use to walk me through publishing new blog posts, podcast episodes on the web.

```
veryTopOfBlogPostMessage = 'Listen to this story here, or scroll down to read it.'

messageAboveNewsletterSignUpBlog = 'Follow me on [Twitter](https://twitter.com/levibostian) and enter your email below to get notified when I release new content related to products and life.'

raw_input('Is your blog post ready to publish? [YES]')
raw_input('Is your podcast episode ready to publish? [YES]')
raw_input('Upload and release your latest podcast episode. [OK]')
embedPlayer = raw_input('Paste embed player link to podcast episode: ')
podcastShareLink = raw_input('Paste simple sharing link to episode: ')
linkToPodcast = raw_input('Enter podcast.levibostian.com link: ')
raw_input('Paste following into top of blog post: \n\n' + veryTopOfBlogPostMessage + '\n' + embedPlayer + '\n\n[OK]')
blogPostTags = raw_input('Enter comma seprated list of tags for blog post: ')

checkOutTagsBlogPostFooter = 'You can find more posts I have written about '
for tag in blogPostTags.split(','):
    tag = tag.strip()
    checkOutTagsBlogPostFooter += '[' + tag.replace('-', ' ') + '](http://levibostian.com/blog/tag/' + tag + ') '

raw_input('Past this on the bottom of your blog post just above newsletter signup: ' + messageAboveNewsletterSignUpBlog + '\n\n' + checkOutTagsBlogPostFooter + '\n\n[OK]')
blogPostLink = raw_input('Publish blog post. Paste link to it after you do.')
titleBlogPost = raw_input('Paste title of post. [OK]')
shortDescription = raw_input('Enter very short desc of post. Take away. [OK]')

raw_input('Go to medium and start new story. https://medium.com/new-story \n\n ' + titleBlogPost + '\n\n[OK]')
raw_input('Paste this: \n\n' + 'Listen to this story here, or scroll down to read it.\n[OK]')
raw_input('Now, paste this as an embed link: \n' + podcastShareLink + '\n\n [OK]')

raw_input('Paste in your story into the medium post. [OK]')

bottomMediumPost = checkOutTagsBlogPostFooter + '\n\nI build mobile apps, web apps, online courses, and other things I think are fun. <a href="https://coaching.levibostian.com/life-of-levi">Enter your email here</a> to get notified when I launched new products and *how* I built them. \n\n *Originally posted on my blog ' + blogPostLink + '* \n\nTwitter <a href="https://twitter.com/levibostian">@levibostian</a> \n\nProductHunt <a href="https://www.producthunt.com/@levibostian">@levibostian</a>'
raw_input('Paste the following into bottom medium post: \n\n' + bottomMediumPost + '\n\n[OK]')
raw_input('Publish to medium [OK]')

raw_input('Go to linkedin and start new story. https://www.linkedin.com/post/new \n\n ' + titleBlogPost + '\n\n[OK]')
raw_input('Paste this: \n\n' + 'Listen to this story here, or scroll down to read it.\n[OK]')
raw_input('Now, paste this as an embed link: \n' + podcastShareLink + '\n\n [OK]')
raw_input('Paste in your story into the linkedin post. [OK]')
raw_input('Paste the following into bottom linkedin post: \n\n' + bottomMediumPost + '\n\n[OK]')
raw_input('Publish to linkedin [OK]')

raw_input('submit to producthunt https://www.producthunt.com/posts/new \n\n ' + titleBlogPost + '\n\n' + shortDescription + '\n\n' + linkToPodcast + '\n\n[OK]')
raw_input('add description to top of post submission. [OK]')

raw_input('add following to levibostian.com and lifeoflevi.xyz: \n\n' + '+article("' + titleBlogPost + '", "' + shortDescription + '", "' + blogPostLink + '", "' + linkToPodcast + '")')

raw_input('Add new blog, podcast to 2017 launch list. [OK]')
raw_input('Send out newsletter to coach fans [OK]')
```
