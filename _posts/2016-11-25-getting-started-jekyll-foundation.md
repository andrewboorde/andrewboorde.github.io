---
layout: post
title:  "Getting Started with Jekyll and Foundation"
date:   2016-11-25 12:30:00
comments: true
categories: jekyll foundation
---

Over the past few weeks I have come to love working with Foundation 6 as a front-end framework 
on a fairly large project. With a new framework working its way into my toolset, I figured I 
would integrate it into the personal blog I am starting. At the end of this article you should
have a Github Pages blog running with Jekyll and Foundation 6.
<!-- more -->

We will start from the ground up and by the end hopefully have a skeleton where you can get up 
and running making your own Github Page. This blog and skeleton uses Matt Swansons Lagom theme 
as inspiration (which can be found [here][lagom-link]. If you want to skip to the finished product, 
the source is available [HERE][source-link].



## Prerequsites

* Ruby
* Terminal or Powershell
* [Jekyll][jekyll-link]
* [Github Pages][github-pages-link]
* [Foundation 6][foundation-link]
* [Node.js][node-link]

## Making a new project with Jekyll and Github Pages

First we will need to make a repository on github, so head over there and make a repository that 
has the name `USERNAME.github.io` . This will allow you to automatically have a page deployed of the same name. 

Now we're ready to setup Jekyll. Clone and cd into the repository via your terminal. Once there run
{% highlight text %}
> gem install jekyll bundler
> jekyll new my-blog
> mv my-blog/* .
> rm my-blog
> bundle exec jekyll serve
{% endhighlight %}

This creates a Jekyll site and moves it to the repository root to be deployed when commited. 
The last command runs the jekyll site locally at `localhost:4000` .

Jekyll creates a page that utilizes markdown and their own default styling. While markdown is 
nice to create blog posts, I find having the page constructed in html and sass to be much more 
familiar. From here, we will construct a different skeleton while integrating Foundation.

## Refactoring Jekyll with Foundation

First we will need to add to the folder structure in order to override jekyll defaults and have 
a place for some foundation assets. In the root of your project you will need to add:
* assets/
* _includes/
* _layouts/
* _sass/
* css/
* js/

The simplest way I have found to grab Foundation will be running these commands from your repository:
{% highlight text %}
> npm install foundation-sites
> mv node_modules/foundation-sites/scss _sass/
> mv node_modules/foundation-sites/dist/foundation.min.js js/
> mv node_modules/jquery/dist/jquery.min.js js/
> rm node_modules/
> touch css/main.scss
{% endhighlight %}

In the newly created `css/main.scss` file you will need to include foundation and the 
hyphen notation to ensure the files run through the SASS processor: 

**css/main.scss**
{% highlight scss %}
---
---

@import '../_sass/scss/foundation';
@include foundation-everything;
{% endhighlight %}

Make sure to add this to your `_config.yml` to ensure SASS processing:

**_config.yml**
{% highlight yaml %}
sass:
   sass_dir: _sass
   style: compressed
{% endhighlight %}

Now delete `about.md` and `index.md` and create `index.html` and `_layouts/default.html`. 
Our default layout will form the structure for our blog and the index.html content will be 
the basis for the content on the main page. 

Add this simple code to the new `_layouts/default.html` to setup our html and grid: 

**_layouts/default.html**
{% highlight html %}
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width initial-scale=1" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>{% raw %}{% if page.title %}{{ page.title }}{% else %}{{ site.title }}{% endif %}{% endraw %}</title>
    <meta name="description" content="{% raw %}{{ site.description }}{% endraw %}">
    <script type="text/javascript" src="/js/jquery.min.js"></script>
    <script src="https://use.fontawesome.com/22c6262fac.js"></script>
    <link rel="stylesheet" href="{% raw %}{{ "/css/main.css" | prepend: site.baseurl }}{% endraw %}">
    <link rel="canonical" href="{% raw %}{{ page.url | replace:'index.html','' | prepend: site.baseurl | prepend: site.url }}{% endraw %}">
  </head>
  <body>
    <div class="row">
      <div class="medium-3 show-for-medium columns" data-sticky-container>
        <div data-sticky data-margin-top="1">
          <div>
            {% raw %}{% include sidebar.html %}{% endraw %}
          </div>
        </div>
      </div>
      <div class="medium-9 columns">
        <main>
          {% raw %}{{ content }}{% endraw %}
        </main>
        {% raw %}{% include footer.html %}{% endraw %}
      </div>
    </div>
  </body>
</html>
{% endhighlight %}

And this snippet is to be added to `index.html` to give us content:

**index.html**
{% highlight html %}
---
layout: default
--
<div>
  <h1>Posts</h1>
  <ul>
    {% raw %}{% for post in site.posts %}{% endraw %}
      <li>  
        <h4>
          <a href="{% raw %}{{ post.url | prepend: site.baseurl }}{% endraw %}">{% raw %}{{ post.title }}{% endraw %}</a>
        </h4>
        <i class="fa fa-calendar" aria-hidden="true"></i>
        <span>{% raw %}{{ post.date | date: "%b %-d, %Y" }}{% endraw %}</span>
        {% raw %}{% if post.content contains '<!-- more -->' %}{% endraw %}
          <p>
            {% raw %}{{ post.content | split:'<!-- more -->' | first }}{% endraw %}
          </p>
          <a href="{% raw %}{{ post.url }}{% endraw %}"class="post-list__button">Read More</a>
        {% raw %}{% else %}{% endraw %}
          <a href="{% raw %}{{ post.url }}{% endraw %}"class="post-list__button">Read More</a>
        {% raw %}{% endif %}{% endraw %}
      </li>
    {% raw %}{% endfor %}{% endraw %}
  </ul>
</div>
{% endhighlight %}

From here, we should create the two files I reference in the default layout: 
`_includes/sidebar.html` and `_includes/footer.html`. This will give us 
a column sidebar and a footer that initalizes Foundation.

**_includes/sidebar.html**
{% highlight html %}
<h1>Bork.</h1>
<img src="http://placehold.it/100x100" id="logo" alt="Blog logo"/>
<h2>I'm <a href="/">Andrew Boorde</a>.</h2>
<p>Musings of a Mediocre Coder</p>
{% endhighlight %} 

**_includes/footer.html**
{% highlight html %}
<div>
  <p>
    The postings on this site are my own and don't necessarily represent my 
    employer’s positions, strategies or opinions.
  </p>
  <p>
    © 2016 &mdash; built with <a href="http://jekyllrb.com/">Jekyll</a>
  </p>
  <script src="/js/foundation.min.js"></script>
  <script>
    $(document).foundation();
  </script>
</div>
{% endhighlight %} 

We are almost done! Now we just need to create a layout for our 
individual posts. Let's create `_layouts/post.html` and add:

**_layouts/post.html**
{% highlight html %}
---
layout: default
---
<div>
  <header>
    <h3>{% raw %}{{ page.title }}{% endraw %}</h3>
    <a href="/" class="hide-for-medium">
      <i class="home fa fa-home"></i>
    </a>
    <p>
      {% raw %}{{ page.date | date: "%b %-d, %Y" }}{% if page.author %} • 
      {{ page.author }}{% endif %}{% if page.meta %} • 
      {{ page.meta }}{% endif %}{% endraw %}
    </p>
  </header>

  <article>
    {% raw %}{{ content }}{% endraw %}
  </article>
</div>
{% endhighlight %}

Finished! Here are some shots of the finished product: 
 
![alt text](/assets/getting-started-jekyll-foundation/finishHome.JPG "finished home page")
![alt text](/assets/getting-started-jekyll-foundation/finishPost.JPG "finihsed post page")
Now I know it isn't pretty by any means but that's for you 
to do! I have included a [finished website][aboorde-link] made from this blog as well. 
The blog I currently use is a variation of this skeleton I have created. Feel free to use 
anything you find useful.

[jekyll-link]: https://jekyllrb.com/
[github-pages-link]: https://pages.github.com/
[foundation-link]: http://foundation.zurb.com/sites.html
[source-link]: https://github.com/aboorde/aboorde.github.io
[node-link]: https://nodejs.org/en/
[lagom-link]: https://github.com/swanson/lagom
[aboorde-link]: https://aboorde.github.io/