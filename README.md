# Rails Gotchas
### Q: How do I add my own styling to a jQuery plugin installed with yarn and WebPack?
**A**: Create a Sass file for your component as usual. At the top of your file, import the CSS from the component's distributed files. Underneath that, write your own rules which can override the distributed rules.

* Make sure **not** to write a tilde (`~`) at the start of the import string - otherwise, WebPack [will ](https://github.com/webpack-contrib/sass-loader#resolving-import-at-rules) that you are trying to do a non-relative import.
* **Why?** If you import the 3rd-party distributed CSS in your *JavaScript* files which get packed into one of your JavaScript packs, your custom CSS will be overridden by this later import. That's why you should import it at the top of your *Sass* file instead.

#### Example:

```scss
@import "jquery-bar-rating/dist/themes/bars-pill.css";

.br-theme-bars-pill .br-widget {
    white-space: nowrap;
    display: flex;
    justify-content: center;
    a {
      flex-grow: 1;
    }
}
```

### Q: My Rails Ajax form/link is not making an Ajax request (`remote: true`)
**A:** Make sure that `app/assets/javascdripts/application.js` contains this line:

```javascript
//= require rails-ujs
```
* [Rails UJS](https://github.com/rails/jquery-ujs) is what picks up on the `remote: true` forms or links, and adds the header to accept a response in JavaScript, instead of the regular HTML. If Rails UJS is missing, this functionality will not be implemented.

### Q: Webpack is serving an older version of my JavaScript, even when I delete my browser cache.
**A:** Run `ruby bin/webpack` to have [Webpack](https://webpack.js.org/) rebuild its JavaScript pack for your application. See [here](https://github.com/rails/webpacker#development) for more information on developing with Webpack in Rails.

### Q: In JavaScript, calling `submit()` on an 'Ajax' form submits it as a regular HTTP request, instead of an Ajax call
**A:** Calling `submit()` bypasses [Rails UJS](https://github.com/rails/jquery-ujs), which otherwise intercepts form submits and turns them into Ajax requests. Instead of calling `submit()`, do one of the following:
```javascript
// Option 1:
form = document.querySelector('form');
form.dispatchEvent(new Event('submit', {bubbles: true})); // you can specify more options in `Event()` for reliability across different browsers.

// Option 2:
// Use rails-ujs to submit the form
form = document.querySelector('form');
Rails.fire(form, 'submit');
```
* See more information [here](https://github.com/rails/rails/issues/29546#issuecomment-313981539).

### Q: I have more than one MapBox map on my page. When I display a map that was previously hidden, it renders as a small square. How do I fix this?
**A:** Once the map has been displayed (eg. when an event is triggered), be sure to call
```javascript
map.resize()
```
You will need a reference to the Mapbox map object, so that you can call `.resize()` on it.

### Q: How do I track changes to an ActiveRecord model during its update?
**A:** See [this article](http://ruby-journal.com/how-to-track-changes-with-after-callbacks-in-rails-3-or-newer/)

### Q: I added new elements to the DOM after the page loaded. How can I get my existing event listeners to trigger for events on these new elements?
**A:** See [this article](https://davidwalsh.name/event-delegate) on event listening for DOM nodes added after the page has loaded, eg. jQuery dropdown plugins 