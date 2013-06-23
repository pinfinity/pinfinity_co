---
title: "Unzipping, Extracting, and Dissecting an iBooks File"
description: "Let's dive into the .books file and see what we can learn about Apple's iBooks platform."
page_intro: "Multi-touch books, medicine, and education. <span class=\"sub\">It's what we do.</span>"
page_id: blog-post
author_name: "Kris Walker"
author_url: "https://plus.google.com/100601805957701195662?rel=author"
media_image: "/assets/figures/sunk-truck-w300h300.jpg"
_template: post
---

At Pinfinity we're driven by the desire to deliver the
[best learning material](/blog/thank-you-for-coming)
that [technology will allow](/blog/creating-publishing-and-marketing-high-quality-ibooks).
As the lead engineer and software developer, it's my job to understand current web and ebook
technologies as best I can, so that we can move the needle on our vision.

Right now, we're focused on iBooks and iBooks Author, so I thought I would break apart an .ibooks
file and see what I could learn. As it turns out, I learned quite a lot, and I'd like to share it
with you. This post is pretty geeky, but even if you don't think you'll ever be a programmer,
as a book author and consumer, I think you'll find some of this stuff interesting.

<figure class="float-left">
	<img src="/assets/figures/sunk-truck-w700h600.jpg" width="700px" alt="A sunken truck" />
	<figcaption>
		An "italics" truck. Original by <a href="http://www.flickr.com/photos/lgrphotos/">LGR Photos</a>.
	</figcaption>
</figure>

## It's just a zip file.
The first thing to note about .ibooks files is that they are just a zip file (or zip "archive",
if you want to be technical about it). So, to see the interesting stuff we'll need to unzip
it and have a look around.

But, even after changing the file extension from .books to .zip, I still
wasn't able to use the Finder to unzip it. I later learned that this was
because I didn't have any zip utility software on my machine (like
[Stuffit](http://www.stuffit.com/mac-expander.html)) which allows you do do
this. Thanks to [Steve Dickie](https://plus.google.com/103412747657062364551/about) for the
[tip](https://plus.google.com/100601805957701195662/posts/5BABXpLMwyF).

Geek confession here: As a programmer, I actually do most of my work from the
terminal, rather than using the Finder, since, once you learn it, it is so
much faster for moving files around, unzipping, and compressing them. So,
naturally, the series of steps for uncompressing an ibooks file on the
terminal is really simple:

You won't need to change the file
extension from .ibooks to .zip. Simply navigate
to the folder containing your book using the `cd` command and then run this command:

	unzip history-of-printing-press.ibooks

That command will "explode" your ibook and allow you to explore the contents.

So from the Finder, or your terminal, you can unzip an .ibooks file without
too much trouble. And once we have the .ibooks file extracted, this is how the
directory and file "tree" is structured. If you are familiar with ePub, then
this will look really similar:

<script src="https://gist.github.com/kixxauth/5627361.js?file=filestructure.txt"></script>

Inside the META-INF folder is a single xml file which defines the "container" for this book. There
is also a mimetype file which tells the ebook reader (the iBooks iPad app, in this case) what kind of
book to expect. The iTunesArtwork file is a binary file containing artwork for the iTunes store. In fact,
if you add the .png file extension to get `iTunesArtwork.png`, you'll be able to open it and see the cover
art.

Ok, so the exciting stuff is actually all inside that OPS folder, and inside there you'll find the main
content files, with the .xhtml extention, the glossary, and the structure files; .ncx and .opf. You'll
also find the assets folder which contains all the images, stylesheets, and a couple other nuggets used
to make the book look good.

## Ahhhhhh XML. Yuck.
So, ePub files, and iBooks files are actually a collection of XML documents, stylesheets, and images.
I know, that's scary to most of us, but as a programmer I create and work with these files on a daily
basis, and knowing a little bit about them will give you inside knowledge about how interactive books,
and the web itself, really work.

For the next part, we're going to need a text editor, and even though you could just use Text Edit,
which comes with your Mac, I prefer
[Sublime Text](http://www.sublimetext.com/), and really recommend it if you're going to be
editing text files on a regular basis. In fact, this very blog post was created with Sublime Text 2.

Also, when you open any of the files in the OPS folder, you'll notice that they
are very hard to read, since all the contents are stuffed onto one long running
line with no tabs or spaces. That can be cleaned up with
[various XML cleaning tools](http://xmlprettyprint.com/), but
for your convenience I cleaned them up for you and posted them to Github
where you can
[have a look at them](https://github.com/pinfinity/ebooks_experiments/tree/master/ibooks/cleaned/history-of-printing-press/OPS)
without a text editor or cleaning tools.

## Binding The Book
So, in the world of interactive books, the contents are held together with a sort of virtual binding
in the form of, you guessed it, more XML. In the case of iBooks, the binding is all inside the
[ibooks.ncx file](https://github.com/pinfinity/ebooks_experiments/blob/master/ibooks/cleaned/history-of-printing-press/OPS/ibooks.ncx).
That file (click on the link to see it) contains the title of the book, and the
basic table of contents, so the iBooks app knows what is contained.

Moving on to the [ibooks.opf file](https://github.com/pinfinity/ebooks_experiments/blob/master/ibooks/cleaned/history-of-printing-press/OPS/ibooks.opf), we can see that the ebook "binding" process
continues here. In the .opf file is a "manifest", or complete listing of every file contained in the book,
as well as a unique ID, assigned by iBooks Author, to each file. This opf file tells the iBooks app
where to find all the pages, images, stylesheets, and other information needed to display your book.

## Content: This is where your book lives.
This is the exciting part for me; all the content of your iBook lives in XHTML files. The geeky significance
of that is that those are the same files used to create websites, and as a web developer, I know
them very well. If you open one of the content.xhtml files in your web browser, it will render, but
looks very strange, since the web browser does not follow the same rules as the iBooks app does.
Even so, this is going to be a fun concept to play around with in the future.

When you take a look at the [content1.xhtml file](https://github.com/pinfinity/ebooks_experiments/blob/master/ibooks/cleaned/history-of-printing-press/OPS/content1.xhtml)
you'll see some really familiar stuff if you've ever worked on a website before. Here is a code snippet
of the head of the document:

<script src="https://gist.github.com/kixxauth/5627361.js?file=content1-snippet.xhtml"></script>

First, notice that the stylesheets are listed at the top of the document,
outside of the head section.  Although perfectly valid usage of XML, this is
something that is unique to ibooks files, and honestly, I'm not sure why Apple
decided to do it that way.

Secondly, in the body section, you'll notice that's where all of your content
lives. It is all laid out using "p" tags (`<p> ... </p>`), which in the XHTML
language, means "paragraph". Nice, so that's starting to make sense.

## iBooks Navigation: Getting complicated.
iBooks has a unique navigational feature you're all familiar with: You can touch, pinch, and zoom the 
chapter and section tiles at the bottom of the book on the iPad to navigate through it. This is unique
to Apple, but it seems the technology used to do it is not.

If you have a look at the snippet above from the content1.xhtml file, you'll see a some "svg" tags.
SVG is an
[interesting bit of technology](http://en.wikipedia.org/wiki/Scalable_Vector_Graphics)
and one which, it seems, Apple is making liberal use of in the iBooks platform.
SVG essentially allows you to use an XML language to define vector shapes,
drawings, and animations. Without getting too deeply into it, it seems, from
looking at the guts of an iBook, that apple is using SVG and CSS animation
transforms to create the fancy pinch and pull navigation, among other cool
interactive features.

I'd love somebody to confirm or deny this for me.

## Moving On: Where to go from here?
So, there is a lot more to talk about, and if you'd like to know more
feel free to ping me on
[Twitter](https://twitter.com/kixxauth)
or
[Google+](https://plus.google.com/100601805957701195662/about/).
But, for the sake of brevity, I'm going to leave it off here.

This has been a really useful exercise for me in trying to determine some
ways we can push the iBooks, and interactive ebook platforms in general, to do
things that maybe the original designers did not intend, or didn't think of
themselves. This is important, because, our vision at [Pinfinity](/) is to do
exactly that: push the limits of technology to deliver learning material that
otherwise, we could not have even imagined. Since we are not able to build our
own technology platforms, we need to push the current platforms (Apple,
Android, Kindle) to continue to innovate.

I hope you've learned something useful, even if it is just having the ability to speak
intelligently about the iBooks platform. Personally, I can't wait to start putting
this knowledge to use creating and improving [our books](/books) at Pinfinity.

<div class="author-footer">

<p>
Kris Walker is a full stack web engineer and the lead developer at
<a href="/" title="Pinfity Website">Pinfinity</a>. You can also learn more about him at
his <a href="http://www.kixx.name" title="Kris Walker's website">personal website</a>.
</p>

<p>
Follow Kris on <a href="http://www.linkedin.com/in/kixx7/">LinkedIn</a> and
<a href="https://twitter.com/kixxauth">Twitter</a>, and feel free to bug him about
web development and eBook formats.
</p>

<p>
And, read more from Pinfinity on our
<a href="/">blog</a>.
</p>
</div>
