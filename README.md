# grunt-pandoc-site

This repository is a fork of [grunt-markdown-site](https://github.com/juliandoucette/grunt-markdown-site/blob/master/LICENSE.txt).

## Why?

I would like to build on top of this work but rather than using the [marked](https://www.npmjs.com/package/marked) package for compiling markdown, I would like to use [pandoc](http://pandoc.org/).

Once pandoc is being used rather than marked, we should be able to convert to files other than HTML, maybe PDFs (using LaTeX) or DOCX.

We could also potentially use files other than markdown as source files.

## How's it going

### Pandoc

If you have Pandoc installed on your system, you should now be able to run `grunt build` to build the site now. It will use pandoc to convert the markdown files into HTML5 files. The options object now accepts a pandoc key with a string of pandoc options.

### URL Structure

There is also now a path option. This accepts the following options:

 * Directory:	The file blog/post1.md will produce the url blog/post1/
 * Path:		The file blog/post1.md will produce the url blog/post1.html

 There is currently no error checking here. There is the potential for errors with a directory like the following

```
 -
 |- blog.md
 |- blog/
 |    |- index.md
```

In this example, both files will try to write to the file /blog/index.html

For this reason, it is recommended that you don't give files the same name as a directory in the same parent directory

### Hierarchy

The global Docs object now contains `childPages` and `parentPages`.


### Automatic Archive Pages

There is now an option called autoArchive which, when true, will automatically create an object in the site structure for a folder with sub pages but without an index.md file in it directly. This defaults to true.

For example, if you have the structure below, there will be an object for the blog directory that can be dealt with in the templating system. The url it is given would be `/blog/` and who's contents would be written to `/blog/index.html`, even though there isn't an index.html file directly under blog.

```
-index.md
|-blog
|  |-article1.md
|  |-article2.md
|  |-article3.md
|
|-contact.md
```

This will be far more useful once the default template behaviour is improved.



## ToDo

 - [x] Use Pandoc rather than marked for conversion
 - [x] Validate pandoc option string
 * [x] Give option for url structure
 * [x] Add hierarchy to pages so in global docs object
 * [x] Add option to automatically generate archive pages
 * [ ] Build system for generating menus
 * [ ] Improve templating system
 - [ ] Give the option to compile to things other than HTML
 - [ ] Look for files other than .md as source files
 - [ ] Run Pandoc command asynchronous. 

### Notes

This section is for my reference. I will put notes here for how I plan on doing things. They might not make sense to people other than me

#### Default template

It would be good if the "default" template changed based on different conditions.

For example, I would like any folder's index.html to do try the following templates in order (If one doesn't exist, move to the next). I will demonstrate with an example for the file `/blog/index.md`

`blog/_archive.html` -> `_archive.html` -> `_default.html`

The file `/blog/post1.md` should try these templates

`blog/post1.html` -> `blog/_single.html` -> `_single.html` -> `_default.html`

The generic templates should start with an underscore as it is unlikely that a md file will start with an underscore. In the future, this might be configurable.

The searcher will work up the folder hierarchy looking for the generic templates.

The file `/blog/catagory1/post1.md` will search for 

`blog/catagory1/post1.html` -> `blog/catagory1/_single.html` -> `blog/_single.html` -> `_single.html` -> `blog/catagory1/_default.html` -> `blog/_default.html` -> `_default.html`



# Original grunt-markdown-site README

The easiest way to create a markdown website with grunt

## Overview

Using the `site` task, you can create a website using markdown from HTML templates

<table>
<thead>
<tr>
  <th style="text-align:left;vertical-align:top;">template.html</th>
  <th style="text-align:left;vertical-align:top;">post.md</th>
  <th style="text-align:left;vertical-align:top;">post.html</th>
</tr>
</thead>
<tbody>
<tr>
  <td style="text-align:left;vertical-align:top;"><pre>
&lt;DOCTYPE html&gt;<br/>&lt;html&gt;<br/>  &lt;head&gt;<br/>    &lt;title&gt;&lt;%= title %&gt;&lt;/title&gt;<br/>  &lt;/head&gt;<br/>  &lt;body&gt;<br/>    &lt;%= content %&gt;<br/>  &lt;/body&gt;<br/>&lt;/html&gt;
  </pre></td>
  <td style="text-align:left;vertical-align:top;"><pre><code>---
title: Post title
template: template.html
---
# Post heading
Post content
</code></pre></td>
  <td style="text-align:left;vertical-align:top;"><pre>
&lt;DOCTYPE html&gt;<br/>&lt;html&gt;<br/>  &lt;head&gt;<br/>    &lt;title&gt;Post title&lt;/title&gt;<br/>  &lt;/head&gt;<br/>  &lt;body&gt;<br/>    &lt;h1&gt;Post heading&lt;/h1&gt;<br/>    &lt;p&gt;Post content&lt;/p&gt;<br/>  &lt;/body&gt;<br/>&lt;/html&gt;
  </pre></td>
</tr>
</tbody>
</table>

## Getting Started

This plugin requires Grunt `~0.4.0`

If you haven't used [Grunt](http://gruntjs.com/) before, be sure to check out the [Getting Started](http://gruntjs.com/getting-started) guide, as it explains how to create a [Gruntfile](http://gruntjs.com/sample-gruntfile) as well as install and use Grunt plugins. Once you're familiar with that process, you may install this plugin with this command:

```shell
npm install grunt-site --save-dev
```

Once the plugin has been installed, it may be enabled inside your Gruntfile with this line of JavaScript:

```js
grunt.loadNpmTasks('grunt-site');
```
## The site task

```js
site: { //site task
  example: { //multi task name (EG: example)
    options: {
      site: {}, //optional global variables available in all templates (EG: site.title, site.url)
      extend: {}, //optional extend the scope object available in all templates (EG: By adding a utility library like momentjs)
      marked: {}, //optional marked options, see https://github.com/chjj/marked for a detailed list of options
      templates: 'src/templates', //required template directory (all partials and templates must be located inside this directory)
      defaultTemplate: 'default.html' //required default template (used whenever "template" is not defined in a markdown document)
    },
    src: 'src/markdown', //required directory that markdown documents will be loaded from
    dest: 'dest' //required directory that html documents will be output to
  }
}
```

Although it is possible to customize these locations via the site task options,
it is recommended that your directory structure looks something like this:

```
public //dest directory
includes //template directory
content //src directory
```

## Writing markdown

[Markdown](http://daringfireball.net/projects/markdown/) documents within the
site src directory are parsed by [marked](https://www.npmjs.com/package/marked)
and [yaml-front-matter](https://www.npmjs.com/package/yaml-front-matter).

#### Basic document example

```markdown
---
title: Example title # example of a property that will be available inside the template as <%= title %> or <%= doc.title %>
template: default.html # optional template property (this will default to the defaultTemplate if not provided)
---

# Example Headline

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
```

## Writing templates

Templates (inside the `templates` directory) are compiled using
[lodash templates](https://lodash.com/docs#template).

Each template is provided the following scope:

```js
//All templates and partials have access to the following properties as globals and via the scope object
var scope = {
  _: _, //lodash utility library
  path: path, //nodejs stdlib path module
  partial: function (template, scope)  //render a template using the passed or default scope (templates are relative to the templates directory)
  //... : ... //All options.extend properties provided inside of Gruntfile
  //... : ... //All yaml-front-matter properties will be available here EG: title
  content: '...', //the HTML content of the document that is currently being rendered,
  doc: document, //the currently rendering document (including all yaml-front-matter and the content property)
  docs: documents, //all site documents: in the same format as document. This is ideal for creating archives, navs, lists, etc
};
```

### Basic template example

##### default.html

```html
<%= partial('header.ejs') %>
<main>
  <%= content %>
</main>
<%= partial('footer.ejs') %>
```

##### header.html

```html
<!DOCTYPE html>
<html>
  <head>
    <title><%= title %></title>
  </head>
  <body>
```

##### footer.html

```html
  </body>
</html>
```

### Basic archive example

##### default.html

```html
<%= partial('header.ejs') %>
<main>
    <h1><%= title %></h1>
    <nav>
        <ul>
            <% _.each(_.where(docs, {"category": "archive"}), function (_doc) { %>
            <li><a href="/<%= _doc.dest %>"><%= _doc.title %></a></li>
            <% }); %>
        </ul>
    </nav>
</main>
<%= partial('footer.ejs') %>
```

##### header.html

```html
<!DOCTYPE html>
<html>
  <head>
    <title><%= title %></title>
  </head>
  <body>
```

##### footer.html

```html
  </body>
</html>
```

## Exporting

Documents will be excluded from the export proceedure if:

- They do not include a title within yaml-front-matter
- They define `exclude: true` within yaml-front-matter
