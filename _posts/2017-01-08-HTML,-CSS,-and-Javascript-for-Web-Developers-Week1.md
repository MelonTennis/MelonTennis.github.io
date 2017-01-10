---
layout: post
title: HTML, CSS, and Javascript for Web Developers Week1
subtitle: Basics and environment setup.
date: 2017-01-08
categories: Coursera
tags: [Web]
catalog: true
---

###  Basics knowledges and environment set-up

一时兴起想学学web development, 随便找来一门课来听听，大概是觉得以后可能不会选Web development这门课了吧... 

> [Source code and examples and slides](https://github.com/jhu-ep-coursera/fullstack-course4)
>
>  版权全归他们不归我2333

#### HTML？

Hyptertext Markup Language (超文本标记语言)

- Annotates content. 

**Three technologies that drives the Web.**

HTML -> Sructure. 

CSS -> Style.

Javascript -> Behavior.

#### Anatomy(结构) of an HTML tag

* Tag:  \<opening tag\> content \<closing tag\>

```html
<p>Content</p>
```

\<br\>, \<hr\> only have open tags -> for HTML5 all tags should have open tags.

* Attribute: name and value, value should be **unique**

```html
<p id = "myID"></p>
```

id is Attribute name and "myId" is attribute value.

Attribute is specified only on **opening tags**.

* Space

**No space** after '\<', must **at least one space** between tag and attribute, other spaces will be ignored.

* Quotes

Using double and single quotes interchanably.

* Self-closing tag

tag without any content

```html
<p/>
```

for HTML5, use👇, not  👆

```html
<p></p>
```

#### Basic HTML document structure

 这老师上课之前涂腮红了呢🙄

```html
<!doctype html> -> this is declaration, no space after <!
<html> -> html tag, contains the entire html document
  <head> -> describe main content of the page
    <meta charset = "utf - 8"> -> stand alone open tag, communicate information to he browser
    <titel>This is title!</titel> -> required!
  </head>
  <body> -> all content visiable to user
    <p>This is body!</p>
  </body>
</html>
```

Notice:

With out declaration, the page will be in [quirks mode](https://zh.wikipedia.org/wiki/怪异模式).

Close the last opened tag before you close its parent tag.

Sequential (top to bottom) rendering.

#### HTML Content model

**Traditional models:**

| Block-level elements                     | Inline Elements                        |
| ---------------------------------------- | -------------------------------------- |
| Render to begin on a new line(by default) | Render on the same line(by default)    |
| May contain inline or other block-level elements | May only contain other inline elements |
| Roughly Flow Content (HTML5)             | Roughly Pharsing Content( HTML5)       |

🌰：

\<div\>\</div>: most generic block-level element

\<span\>\</span>: super generic inline element.

```html
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <title>div and span elements</title>
</head>
<body>
  <div>*** DIV 1: Some content here ***</div>
  <div>*** DIV 2: Following right after div 1 ***</div>
  <span>*** SPAN 1: Following right after div 2 ***</span>
  <div>
    *** DIV 3: Following right after span 1 
    <span>*** SPAN 2: INSIDE div 3 ***</span>
    Continue content of div 3 ***
  </div>
</body>
</html>
```

网页长这个样子：

\*** DIV 1: Some content here ***

\*** DIV 2: Following right after div 1 ***

\*** SPAN 1: Following right after div 2 ***

\*** DIV 3: Following right after span 1 \*** SPAN 2: INSIDE div 3 \*** Continue content of div 3 ***

👇图示不错

> [W3C Kinds of Conetent](https://www.w3.org/TR/html5/dom.html#kinds-of-content)

#### Essential HTML5 tags

**Semantic html element**: all block - level

- heading  -> \<h1>\</h1> ~ \<h6>\</h6> heading tags, h1 is the most important.


- header -> header information 


- section
- article -> articles are always in section or other articles


- aside -> some related information


- footer -> footer information

🌰：

```html
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <title>Heading Elements</title>
</head>
<body>
  <header>
    header element - Some header information goes here. Usually consists of company logo, some tag line, etc. Sometimes, navigation is contained in the header as well.
    <nav>nav (short for navigation) element - Usually contains links to different parts of the web site.</nav>
  </header>
  <h1>Main Heading of the Page (hard not to have it)</h1>
  <section>
    Section 1
    <article>Article 1</article>
    <article>Article 2</article>
    <article>Article 3</article>
  </section>
  <section>
    Section 2
    <article>Article 4</article>
    <article>Article 5</article>
    <article>Article 6</article>
    <div>Regular DIV element</div>
  </section>
  <aside>
    ASIDE - Some information that relates to the main topic, i.e., related posts.
  </aside>

  <footer>
    JHU Copyright 2015
  </footer>
</body>
</html>
```

**Lists：**

```html
<ul> -> unorder list
  <li> -> only things allowed in ul tag</li>
</ul>
<ol> -> order list
  <li> ->items in list </li>
</ol>
```

**Character entity(实体) references:**

Always escape:  ~~🙄打不出来~~

> < : shuold be & lt;

> \> : should be & gt;

> &: should be & amp;

> ": & quit;

Not breaking space:  & nbsp; -> used this instead of space between words that do not want to be broken

**Creating Links:**

```html
<a herf = "relative URL" title = "same dir link"></a>
```

Notice: \<a>\</a> is both flow and pharsing tag. 

- Internal linking to other pages in the site -> absolute URL links

  ```html
  <section>
      We can link to a file in the same directory as this HTML file like this:
      <a href="same-directory.html" title="same dir link">Linking to a file in the same directory</a>

      <a href="same-directory.html" title="same dir link">
        <div> DIV Linking to a file in the same directory</div>
      </a>
    </section>
  ```

- External linking to other web sites -> URL

  ```html
  <section>
      <p>
        Let's link to a Facebook Fan page I created for this course!
        <!-- link to Facebook page WITH TARGET-->
        <a href="http://www.facebook.com/CourseraWebDev" 
        target="_blank" title="Like Our Page!">Course Facebook Page</a>
      </p>
    </section>
  ```

Using target="_blank": open another new page, keep the old page

- Linking to the sections of a document 

  ```html
  <section>
      <ul>
        <!-- Link to every section in the page -->
        <li><a href="#section1">#section1</a></li>
        <li><a href="#section2">#section2</a></li>
        <li><a href="#section3">#section3</a></li>
        <li><a href="#section4">#section4</a></li>
        <li><a href="#section5">#section5</a></li>
        <li><a href="#section6">#section6</a></li>
      </ul>
  </section>
  <section id="section1">
      <h3>(#section1) Section 1</h3>
      <p>Section 1 content</p>
  </section>
    <div>
      <h2><a name="section6">(#section6) Section 6</a></h2>
      <p>
        Back to top: <a href="#top">Back to Top</a>
      </p>
    </div>
  ```

**Displacing images:**

Comment:

```html
<!— This is comment —>
```

Image: inline elements

```html
<img src="URL of image both absolute and website is all right" width="400" height = "235" alt="this used by screen readers help people with visual impairment">
```

Notice: always using width and height to save space for image.

几个不错的参考网站

[Jsfiddle.net](https://jsfiddle.net/)

[CSS - Tricks](https://css-tricks.com/)

[codepen](http://codepen.io/)

[W3School](http://www.w3schools.com/)

[Can I Use](http://caniuse.com/)

[W3C -html](https://www.w3.org/TR/html5/dom.html)