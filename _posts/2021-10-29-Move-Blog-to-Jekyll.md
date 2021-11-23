---

title : Move Blog to Jekyll

# layout: "post"

excerpt: "Blog를 tistory에서 Jekyll로 옮긴 이유"

categories:
  - Blog Jekyll
tags:
  - [Blog, jekyll, Github, Git]
classes : wide
toc: true
toc_sticky: true
---


Hi , this is the my first post in the Jekyll blog. I have written my CS blog in tistory.  I  have thought tistory is uncomfortable to write CS blog. So , is there any alternative ?  Yes,  it's the Jekyll

<!-- {: class="table-of-content"}
* TOC
{:toc} -->

---

## What is Jekyll


## How to make github.io with Jekyll?

### From Scratch

First, you install `ruby` and `gem`

```bash
sudo apt-get install ruby-full
```
If you want to know details, go to [link](https://www.ruby-lang.org/en/documentation/installation/)

I know `ruby` and `gem` are installed when installing ruby full environment

You can check whether it is installed properly by checking version
```bash
ruby -v
gem -v
```
Then , you can get output something like this.(Version can be different).
```bash
ruby 2.7.0p0 (2019-12-25 ..)
3.1.2
```

Then, it's time to install `jekyll`
```bash
gem install jekyll bundler
```
Then , we can make `jekyll` site.
```bash
jekyll new woongjoon.github.io
```
In my case, i will use this site as a github blog. Move to `blog` directory
Then , run below code.
```bash
bundle exec jekyll serve
```
When you run this code first time, you do not have to run `bundle exec`




### Fork from template
## Make link github.io
First , you make a new repo without `.gitignore` and `README.md` . You should name your repo `username.github.io`.
Go to your blog directory(woongjoonchoi.github.io in my case) , run `git init`. In repo, you see the command how to push existing directory to repo. Follow the instructions.
If you suceed , you can go `Settings` in your repo. And , click the `pages`
![image](https://user-images.githubusercontent.com/50165842/139464390-1da7ebd4-c61d-41d6-944a-93e680d53aab.png)
![image](https://user-images.githubusercontent.com/50165842/139464599-048f69d9-3839-4219-8590-81bc709f8b02.png)
![image](https://user-images.githubusercontent.com/50165842/139464767-5e64ed6d-360e-4894-ba08-c21d716b2d9f.png)
Here is your github.io! You can edit your blog in Github.!!

## Make Post

Now, we can make a simple post.

In my case , there is a directory `_posts` in `woongjoon.github.io`There is a post named like `2021-10-29-welcome-to-jekyll.markdown`
It's your first post. 

Very awesome

There are some rules when posting on your `blog`!you should name your post `YYYY-MM-DD-TITLE.md(or markdown)`
`Hint.If you name your post future time than present , the post is not visible to you!!!`


## Front matter

If you posted a simple one on blog, it seems different from that you expected.
You need to set Front matter . I think Front matter is `yaml` or `json` . It is `key-value` type. It is configuration in my think.

You can adjust your Front matter like this.

```
---
layout : "post"
title : "New title"
date:   2021-10-05 09:25:45 +0900
categories: cst
permalink : /:categories/:day/:year/:month/:title.html
---
```
Then , when we go to site again my post address becomes  `https://woongjoonchoi.github.io/cst/05/2021/10/welcome-to-jekyll.html` .

You can set layout `page` , `home` ..etc.



## Make Page

I think `page` in jekyll is like `link`

It 's very easy to make it. By default, there is page `About`.
In directory `woongjoonchoi.github.io` (in my case) , there is md file `about.md`. We gonna make page `CV`

```
---

layout : "page"
title : CV

---

```
Make `CV.md` file in `woongjoonchoi.github.io` .

## Include

we gonna make header for link to `CV`

First, we make `_includes` directory.

The site theme is default `minima`.

So , I get a `header.html` from [minima repo](https://github.com/jekyll/minima)

In , `_includes` directory , copy `header.html` . Then , put it in to your `_includes` directory.

```html
<header class="site-header">

  <div class="wrapper">
    {%- assign default_paths = site.pages | map: "path" -%}
    {%- assign page_paths = site.header_pages | default: default_paths -%}
    {%- assign titles_size = site.pages | map: 'title' | join: '' | size -%}
    <a class="site-title" rel="author" href="{{ "/" | relative_url }}">{{ site.title | escape }}</a>

    {%- if titles_size > 0 -%}
      <nav class="site-nav">
        <input type="checkbox" id="nav-trigger" class="nav-trigger" />
        <label for="nav-trigger">
          <span class="menu-icon">
            <svg viewBox="0 0 18 15" width="18px" height="15px">
              <path d="M18,1.484c0,0.82-0.665,1.484-1.484,1.484H1.484C0.665,2.969,0,2.304,0,1.484l0,0C0,0.665,0.665,0,1.484,0 h15.032C17.335,0,18,0.665,18,1.484L18,1.484z M18,7.516C18,8.335,17.335,9,16.516,9H1.484C0.665,9,0,8.335,0,7.516l0,0 c0-0.82,0.665-1.484,1.484-1.484h15.032C17.335,6.031,18,6.696,18,7.516L18,7.516z M18,13.516C18,14.335,17.335,15,16.516,15H1.484 C0.665,15,0,14.335,0,13.516l0,0c0-0.82,0.665-1.483,1.484-1.483h15.032C17.335,12.031,18,12.695,18,13.516L18,13.516z"/>
            </svg>
          </span>
        </label>

        <div class="trigger">
          {%- for path in page_paths -%}
            {%- assign my_page = site.pages | where: "path", path | first -%}
            {%- if my_page.title -%}
            <a class="page-link" href="{{ my_page.url | relative_url }}">{{ my_page.title | escape }}</a>
            {%- endif -%}
          {%- endfor -%}
        </div>
      </nav>
    {%- endif -%}
  </div>
</header>

```

It is default html file in minima repo

```html
          <div class="trigger">
            {%- for path in page_paths -%}
              {%- assign my_page = site.pages | where: "path", path | first -%}
              {%- if my_page.title != "CV" -%}
              <a class="page-link" href="{{ my_page.url | relative_url }}">{{ my_page.title | escape }}</a>
              {%- endif -%}
              {%- if my_page.title == "CV" -%}
              <a class="page-link" href="https://woongjoonchoi.github.io/resume-template/"> CV </a>
              {%- endif -%}
            {%- endfor -%}

            
          </div>

```
We change `trigger` class like this. 

## How to make portfolio page?

we download the [repo](https://github.com/jglovier/resume-template)
`Do not fork Repo`
Then, we make a new empty repository. repo name does not matter.Run command that is shown in new repo.

## How to customize portfolio page?


## References
[1] https://www.youtube.com/playlist?list=PLLAZ4kZ9dFpOPV5C5Ay0pHaa0RJFhcmcB  
[2] https://github.com/jglovier/resume-template  
[3] https://github.com/sproogen/modern-resume-theme

