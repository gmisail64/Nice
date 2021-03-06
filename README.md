![Logo](/NiceLogo.png)

## What is Nice?

Nice is a simple static site generator that makes it ~~easy~~ easier for developers to generate static webpages. 

## Installation

Nice is distributed through haxelib. To install, type into terminal:

`haxelib install nice`

Once you have Nice installed, you need to set up your website. Nice makes it very easy to hit the ground running with a new project.

```
mkdir MyNewSite
cd MyNewSite
haxelib run Nice create project
haxelib run Nice build
```

First, you will create a new folder. Inside of this folder, you are going to create a new project and then build it. Easy, right? If you want to see your site in action, you can start a web server inside of the `_public` folder.

```
cd _public
python -m SimpleHTTPServer  # python 2
python3 -m http.server      # python 3
```

### API

If you do not want to use the default frontend, you can use the external Javascript interface. You can install it through NPM:

`npm install nice-site`

```
var api = require('nice-site')

api.createProject();
api.createLayout("another-layout")
api.createPost("my-post")
api.createPage("another-page");
api.build()
```

Using the API, you can do what you can do with the command line interface: create projects, posts, pages, and layouts, as well as build the project. 

## Commands

Nice comes with a catalog of commands that make it incredibly easy to create, build, and manage your project.

```
haxelib run Nice build 
haxelib run Nice create <project/post/page/layout> <name>
haxelib run Nice delete <post/page/layout> <name>
```

It is encouraged that you create an alias to the `haxelib run Nice` command, since it a burden to type out. Because the `nice` command is a default Unix command, consider assigning `haxelib run Nice` to something like `nice-util` or `nicelib`.

## Theming

Nice does not come with a pre-installed theme or design. The user must design their own theme using HTML & Mustache.

You can use the following variables in your Mustache templates:

- title - Title of the current post / page.
- body - Body content (In your template, you MUST use triple braces ({{{body}}})! Without them, the page will not render properly)
- pages - Array of your site's pages.
- posts - Array of your site's posts.
- date - the current post's Date object
- date.day - the current post's day (integer from 1-31)
- date.month - the current post's month (integer from 1 - 12)
- date.year - the current post's year (4 digit integer)

### _layout/index.html Example

```
<html>
    <body>
        <h1>My cool blog!</h1>
        <h3>{{title}}</h3>
        <p>{{{body}}}</p>
        <hr>
        <h2>Other posts</h2>
        <ul>
    	    {{#posts}}
            	<li><a href="/_posts/{{_name}}">{{_title}}</a></li>
    	    {{/posts}}
        </ul>
    </body>
</html>
```

> Note:
> All variables within the Post / Page object are prefixed with an underscore.

Posts and pages are exposed to layouts as an Array of posts. You can change the order by modifying the `sort` property in the configuration file.

## Posts

Posts are very easy to create; all you have to do is create a new file in the `_posts` folder with a HTML extension. You can do this manually or by using the command `haxelib run nice create post PutYourPostNameHere`.

A normal post will look like this:

```
---
title: My Post Title!
date: 2001-01-23
---

<p>
Hello!
</p>
```

Dates follow the [YAML standard.](https://github.com/mikestead/hx-yaml) The values that you use in posts are exposed to the layouts using the Date object (the `date` variable.)

## Pages

Pages are very similar to posts, however they are not sorted chronologically.

To create a new page, all you have to do is create a new file in the `_pages` folder with a HTML extension. Much like posts, there are two ways to do this: manually or with the command line. Using the command line, you will need to run the `haxelib run nice create page PutYourPageNameHere` command.

A normal page will look like this:

```
---
title: About Me
---

<p>
Let me tell you about myself...
</p>
```

## Configuration Files

Configuration files allow you to alter the build process and define variables that you can use in your layout files. 

```
paths:
  assets: _assets
  layouts: _layouts
  pages: _pages
  posts: _posts
  output: _public

variables:
  title: john's site
  name:
    first: john
    last: smith
   
sort:
    posts: newest-to-oldest
    pages: order
```

To use the variables declared in `config.yaml`, you use the Mustache expression `{{variables.NameOfVariable}}`.

There are 3 different sorting styles: `newest-to-oldest`, `oldest-to-newest`, and `order`. If a sorting style is not specified, posts and pages will be in the same order as they are in the `_posts` and `_pages` folder.

## Plugins

Plugins are written in a Haxe's official scripting language, hscript. They allow the user to customize the build process without having to recompile the Nice library itself. Installing plugins is incredibly easy; all you need to do is create a folder named `_plugins` in the root directory of your project and add `.hscript` files to it. 

```
for(post in posts)
{
    var title = post.getTitle(); 
    var title_chars = title.split(''); 
    title_chars.reverse(); 
    post.setTitle(title_chars.join(''));
}
```

In this example, we are looping through every post in the posts folder, getting the name, reversing it, and then setting it to the reversed version. Although not very practical, it represents what can be done using the plugins system. 
