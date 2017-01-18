---
layout: post
title: HTML, CSS, and Javascript for Web Developers Week3
subtitle: 
date: 2017-01-11
categories: Coursera
tags: [Web]
catalog: true
---

### Visit with the client & Setup overview

Bring examples of other sites to help figure out what they want.

Encourage client to use less information.

Client should invest the project.

Client should have one person as the decider.

Limit the numebr of revisions.

Involve others to help produce a great product.

### Coding the navigation Bar of the site

Using the bootstrap.

🌰：

```html
<!-- base class and sub class -->    
<nav id="header-nav" class="navbar navbar-default">
  <!-- container is fluid, and stretches all the edges of browser -->
  <!-- so that the content of navbar will stay middle of the screem -->
      <div class="container">
        <div class="navbar-header">
          <!-- a element is clickable -->
          <!-- pull left means float left -->
          <!-- only md and lg can this item visiable -->
          <a href="index.html" class="pull-left visible-md visible-lg">
            <div id="logo-img"></div>
          </a>
          <div class="navbar-brand">           
            <a href="index.html"><h1>David Chu's China Bistro</h1></a>
            <p>
              <img src="images/star-k-logo.png" alt="Kosher certification">
              <span>Kosher Certified</span>
            </p>
          </div>
        </div>
      </div>
    </nav>
```

#### Coding the Button

```html
<!-- collapsed signify a plugin in bootstrap -->
          <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#collapsable-nav" aria-expanded="false">
            <!-- sr for screen readers -->
            <span class="sr-only">Toggle navigation</span>
            <span class="icon-bar"></span>
            <span class="icon-bar"></span>
            <span class="icon-bar"></span>
          </button>
```

```html
<div id="collapsable-nav" class="collapse navbar-collapse">
           <ul id="nav-list" class="nav navbar-nav navbar-right">
            <li>
              <a href="menu-categories.html">
               <!-- when xs hid this break --> 
                <span class="glyphicon glyphicon-cutlery"></span><br class="hidden-xs"> Menu</a>
            </li>
          </ul><!-- #nav-list -->
        </div><!-- .collapse .navbar-collapse -->
```

#### Fix Layout Text and download menus

Different code for different size devices

```css
/********** Medium devices only **********/
@media (min-width: 992px) and (max-width: 1199px) {
  /* Header */
  #logo-img {
    background: url('../images/restaurant-logo_medium.png') no-repeat;
    width: 100px;
    height: 100px;
    margin: 5px 5px 5px 0;
  }
  /* End Header */
}
```

```css
  .navbar-brand h1 { /* Restaurant name */
    padding-top: 10px;
    font-size: 5vw; /* 1vw = 1% of viewport width */
  }  
 #collapsable-nav a { /* Collapsed nav menu text */
    font-size: 1.2em;
  }
  #collapsable-nav a span { /* Collapsed nav menu glyph */
    font-size: 1em;
    margin-right: 5px;
  }
```

### Coding the homepage and footer

 #### Coding the Jumbotron

```html
<div class="jumbotron">
      <img src="images/jumbotron_768.jpg" alt="Picture of restaurant" class="img-responsive visible-xs">
    </div>
```

```css
/* HOME PAGE */
.container .jumbotron {
  box-shadow: 0 0 50px #3F0C1F;
  border: 2px solid #3F0C1F;
}
/********** Small devices only **********/
@media (min-width: 768px) and (max-width: 991px) {
  /* Home Page */
  .container .jumbotron {
    background: url('../images/jumbotron_768.jpg') no-repeat;
    height: 432px;
  }
  /* End Home Page */
}
/* Extra small devices only */
.container .jumbotron {
    margin-top: 30px;
    padding: 0;
  }
/* medium devices only */
.container .jumbotron {
    background: url('../images/jumbotron_992.jpg') no-repeat;
    height: 558px;
  }
```

#### Coding navigation tiles

```html
<div id="home-tiles" class="row">
      <div class="col-md-4 col-sm-6 col-xs-12">
        <a href="menu-categories.html"><div id="menu-tile"><span>menu</span></div></a>
      </div>
      <div class="col-md-4 col-sm-6 col-xs-12">
        <!-- following devices will be as wide as the uper device -->
        <a href="single-category.html"><div id="specials-tile"><span>specials</span></div></a>
      </div>
      <div class="col-md-4 col-sm-12 col-xs-12">
        <!-- open a new tab -->
        <a href="" target="_blank">
          <div id="map-tile">
            <iframe src="" width="100%" height="250" frameborder="0" style="border:0" allowfullscreen></iframe>
            <span>map</span>
          </div>
        </a>
      </div>
    </div>
  </div>

```

```css
#menu-tile:hover, #specials-tile:hover, #map-tile:hover {
  box-shadow: 0 1px 5px 1px #cccccc;
}
#menu-tile {
  /* .. means upper file */
  background: url('../images/menu-tile.jpg') no-repeat;
  background-position: center;
}
#specials-tile {
  background: url('../images/specials-tile.jpg') no-repeat;
  background-position: center;
}
/* Home Page - for small page */  
  .container .jumbotron {
    margin-top: 30px;
    padding: 0;
  }
  #menu-tile, #specials-tile {
    width: 360px;
    margin: 0 auto 15px;
  }
}

/********** Super extra small devices Only :-) (e.g., iPhone 4) **********/
@media (max-width: 479px) {
  /* Header */
  .navbar-brand h1 { /* Restaurant name */
    padding-top: 5px;
    font-size: 6vw;
  }
  /* End Header */
  /* Home page */
  #menu-tile, #specials-tile {
    width: 280px;
    margin: 0 auto 15px;
  }
}
```

#### Coding the Footer

```html
<footer class="panel-footer">
    <div class="container">
      <div class="row">
        <section id="hours" class="col-sm-4">
          <span>Hours:</span><br>
          Sun-Thurs: 11:15am - 10:00pm<br>
          Fri: 11:15am - 2:30pm<br>
          Saturday Closed
          <!-- only show up when extra small -->
          <!-- hr -> horiontal rule -->
          <hr class="visible-xs">
        </section>
        <section id="address" class="col-sm-4">
          <span>Address:</span><br>
          7105 Reisterstown Road<br>
          Baltimore, MD 21215
          <p>* Delivery area within 3-4 miles, with minimum order of $20 plus $3 charge for all deliveries.</p>
          <hr class="visible-xs">
        </section>
        <section id="testimonials" class="col-sm-4">
          <p>"The best Chinese restaurant I've been to! And that's saying a lot, since I've been to many!"</p>
          <p>"Amazing food! Great service! Couldn't ask for more! I'll be back again and again!"</p>
        </section>
      </div>
      <div class="text-center">&copy; Copyright David Chu's China Bistro 2016</div>
    </div>
  </footer>
```

```css
/* FOOTER */
.panel-footer {
  margin-top: 30px;
  padding-top: 35px;
  padding-bottom: 30px;
  background-color: #222;
  border-top: 0;
}
.panel-footer div.row {
  margin-bottom: 35px;
}
#hours, #address {
  line-height: 2;
}
#hours > span, #address > span {
  font-size: 1.3em;
}
#address p {
  color: #557c3e;
  font-size: .8em;
  line-height: 1.8;
}
#testimonials {
  font-style: italic;
}
#testimonials p:nth-child(2) {
  margin-top: 25px;
}
/* END FOOTER */
/* Footer - only for extra small devices*/
  .panel-footer section {
    margin-bottom: 30px;
    text-align: center;
  }
  .panel-footer section:nth-child(3) {
    margin-bottom: 0; /* margin already exists on the whole row */
  }
  .panel-footer section hr {
    width: 50%;
  }
  /* End Footer */
```

### Coding Meuns

#### Coding Menu categories

```html
<!-- different size with different devices -->
<div class="col-md-3 col-sm-4 col-xs-6 col-xxs-12">
        <a href="single-category.html">
          <div class="category-tile">
            <img width="200" height="200" src="images/menu/B/B.jpg" alt="Lunch">
            <span>Lunch</span>
          </div>
        </a>
      </div>
```

```css
/* clickable -> when the mouse is float on that element */
.category-tile:hover {
  box-shadow: 0 1px 5px 1px #cccccc;
}
/* next slibing of this class */
#menu-categories-title + div {
  margin-bottom: 50px;
}
/* define an extra extra small device */
  .col-xxs-12 {
    position: relative;
    min-height: 1px;
    padding-right: 15px;
    padding-left: 15px;
    float: left;
    width: 100%;
  }
```

#### Coding the single category page

```html
    <div class="menu-item-tile col-md-6">
        <div class="row">
          <div class="col-sm-5">
            <div class="menu-item-photo">
              <div>D01</div>
              <img class="img-responsive" width="250" height="150" src="images/menu/B/B-1.jpg" alt="Item">
            </div>
            <div class="menu-item-price">$10.95<span> (pint)</span> $14.95 <span>(quart)</span></div>
          </div>
          <div class="menu-item-description col-sm-7">
            <h3 class="menu-item-title">Veal with Mixed Vegetables</h3>
            <p class="menu-item-details">...</p>
          </div>
        </div>
        <hr class="visible-xs">
      </div>
```

```css
/* SINGLE CATEGORY PAGE */
.menu-item-tile {
  margin-bottom: 25px;
}
.menu-item-tile hr {
  width: 80%;
}
.menu-item-tile .menu-item-price {
  font-size: 1.1em;
  text-align: right;
  margin-top: -15px;
  margin-right: -15px;
}
.menu-item-tile .menu-item-price span {
  font-size: .6em;
}
.menu-item-photo {
  position: relative;
  border: 2px solid #3F0C1F; 
  overflow: hidden;
  padding: 0;
  margin-right: -15px;
  margin-left: auto;
  margin-bottom: 20px;
  max-width: 250px;
}
.menu-item-photo div {
  position: absolute;
  bottom: 0;
  right: 0;
  width: 80px;
  background-color: #557c3e;
  text-align: center;
}
.menu-item-description {
  padding-right: 30px;
}
h3.menu-item-title {
  margin: 0 0 10px;
}
.menu-item-details {
  font-size: .9em;
  font-style: italic;
}
/* END SINGLE CATEGORY PAGE */
```

```html
<!-- when the previous div has long descriptions -->     
<!-- Add after every 2nd menu-item-tile -->
      <div class="clearfix visible-lg-block visible-md-block">
```

```html
            <li class="visible-xs">
              <a href="index.html">
                <span class="glyphicon glyphicon-home"></span> Home</a>
            </li>
            <li class="active">
              <a href="menu-categories.html">
                <span class="glyphicon glyphicon-cutlery"></span><br class="hidden-xs"> Menu</a>
            </li>
```



