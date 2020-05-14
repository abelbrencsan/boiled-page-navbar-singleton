# Boiled Page navbar singleton

Navbar SCSS singleton for Boiled Page frontend framework. It is intended to create a horizontal navigation bar with dropdown subnavigations, which becomes a vertical offset navigation on medium or smaller breakpoint.

## Install

Place `_navbar.scss` file to `/assets/css/singletons` directory, and add its path to singleton block in `assets/css/app.scss` file. You will also need to add dropdown script to make it working properly.

- Dropdown script: <https://www.github.com/abelbrencsan/boiled-page-dropdown-script>

## Usage

### Classes

Class name | Description | Example
---------- | ----------- | -------
`navbar` | Applies navigation bar. | `<header class="navbar"></header>`
`navbar-menu` | Applies a navigation menu inside navigation bar which contains a button that opens offset navigation on medium or smaller breapoint. | `<div class="navbar-menu"></div>`
`navbar-nav` | Applies a navigation inside navigation bar. | `<nav class="navbar-nav"></nav>`
`navbar-nav-links` | Applies a list of navigation links inside navigation. | `<ul class="navbar-nav-links"></ul>`
`navbar-nav-close` | Applies a close button inside navigation that closes offset navigation on medium or smaller breapoints. | `<button class="navbar-menu"></button>`

### Examples

#### Example 1

The following example shows a navigation bar with dropdown subnavigations. `data-navbar-subnav` and `data-offset-nav` attributes are used for dropdown scripts.

```html
<header class="navbar" data-offset-nav>
  <div class="container">
    <a href="#" class="navbar-logo">My Company</a>
    <div class="navbar-menu">
      <button type="button" data-offset-nav-trigger>Navigation</button>
    </div>
    <nav class="navbar-nav" data-offset-nav-element>
      <ul class="navbar-nav-links">
        <li>
          <a href="#">Home</a>
        </li>
        <li data-navbar-subnav>
          <button type="button" data-navbar-subnav-trigger>Services</button>
          <ul data-navbar-subnav-element>
            <li>
              <a href="#">Web design</a>
            </li>
            <li>
              <a href="#">Web development</a>
            </li>
            <li>
              <a href="#">Graphic design</a>
            </li>
          </ul>
        </li>
        <li data-navbar-subnav>
          <button type="button" data-navbar-subnav-trigger>Products</button>
          <ul data-navbar-subnav-element>
            <li>
              <a href="#">CMS systems</a>
            </li>
            <li>
              <a href="#">CRM systems</a>
            </li>
            <li>
              <a href="#">Servers</a>
            </li>
          </ul>
        </li>
        <li>
          <a href="#">Blog</a>
        </li>
        <li>
          <a href="#">Contact</a>
        </li>
      </ul>
      <button type="button" class="navbar-nav-close" data-offset-nav-close>Close</button>
    </nav>
  </div>
</header>
```

Add `navbar` property to `app` object in `assets/js/app.js`.

```js
navbar: {
  subnavs: [],
  offsetNav: null
}
```

Place the following code inside `assets/js/app.js` to initalize element with `data-offset-nav` attribute as a dropdown for navigation bar offset navigation.

```js
// Initialize navbar offset navigation
var offsetNavElem = document.querySelector('[data-offset-nav]');
if (offsetNavElem) {
  app.navbar.offsetNav = new Dropdown({
    trigger: offsetNavElem.querySelector('[data-offset-nav-trigger]'),
    element: offsetNavElem.querySelector('[data-offset-nav-element]'),
    closeButton: offsetNavElem.querySelector('[data-offset-nav-close]'),
    isIndependent: true,
    setHeight: false
  });
  app.navbar.offsetNav.init();

  // Remove "aria-hidden" attribute on medium and larger breakpoint
  if (!window.matchMedia(app.breakpoints.medium).matches) {
    app.navbar.offsetNav.element.setAttribute('aria-hidden','false');
  }
}
```

Place the following code inside `assets/js/app.js` to initalize elements with `data-navbar-subnav` attribute as dropdowns for subnavigations.

```js
// Initialize navbar subnavigations
var subnavElements = document.querySelectorAll('[data-navbar-subnav]');
for (var i = 0; i < subnavElements.length; i++) {
  app.navbar.subnavs[i] = new Dropdown({
    trigger: subnavElements[i].querySelector('[data-navbar-subnav-trigger]'),
    element: subnavElements[i].querySelector('[data-navbar-subnav-element]')
  });
  app.navbar.subnavs[i].init();
}
```

Place the following code inside the `onBreakpointChange` function in `assets/js/app.js` to close navigation bar offset navigation and all navigation bar subnavigations when switching between horizontal and vertical navigation bar. This code also recalculates maximum height of opened navigation bar subnavigation's element when breakpoint changes.

```js
// Switching between horizontal and vertical navbar
if (mediaQuery.media == app.breakpoints.medium && app.navbar.offsetNav !== null) {
  for (var i = 0; i < app.navbar.subnavs.length; i++) {
    app.navbar.subnavs[i].close();
  }
  if (mediaQuery.matches) {
    app.navbar.offsetNav.element.setAttribute('aria-hidden','true');
  }
  else {
    if (app.navbar.offsetNav.getIsOpened()) {
      app.navbar.offsetNav.close();
    }
    app.navbar.offsetNav.element.setAttribute('aria-hidden','false');
  }
}

// Recalculate maximum height of opened navbar subnavigation's element
for (var i = 0; i < app.navbar.subnavs.length; i++) {
  app.navbar.subnavs[i].recalcHeight();
}
```
