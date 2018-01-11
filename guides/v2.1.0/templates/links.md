## The `{{link-to}}` Helper

You create a link to a route using the `{{link-to}}` helper.

```app/router.js
Router.map(function() {
  this.route('photos', function(){
    this.route('edit', { path: '/:photo_id' });
  });
});
```

```app/templates/photos.hbs
<ul>
  {{#each photos as |photo|}}
    <li>{{#link-to "photos.edit" photo}}{{photo.title}}{{/link-to}}</li>
  {{/each}}
</ul>
```

If the model for the `photos` template is a list of three photos, the
rendered HTML would look something like this:

```html
<ul>
  <li><a href="/photos/1">Happy Kittens</a></li>
  <li><a href="/photos/2">Puppy Running</a></li>
  <li><a href="/photos/3">Mountain Landscape</a></li>
</ul>
```

The `{{link-to}}` helper takes one or two arguments:

* The name of a route. In this example, it would be `index`, `photos`, or
  `photos.edit`.
* At most one model for each [dynamic segment](../../routing/defining-your-routes/#toc_dynamic-segments).
  By default, Ember.js will replace each segment with the value of the corresponding object's `id` property.
  In the example above, the second argument is each `photo` object, and the `id` property is used to fill in
  the dynamic segment with either `1`, `2`, or `3`. If there is no model to pass to the helper, you can provide
  an explicit value instead:

```app/templates/photos.hbs
{{#link-to "photos.edit" 1}}
  First Photo Ever
{{/link-to}}
```

When the rendered link matches the current route, and the same
object instance is passed into the helper, then the link is given
`class="active"`. For example, if you were at the URL `/photos/2`,
the first example above would render as:

```html
<ul>
  <li><a href="/photos/1">Happy Kittens</a></li>
  <li><a href="/photos/2" class="active">Puppy Running</a></li>
  <li><a href="/photos/3">Mountain Landscape</a></li>
</ul>
```

### Example for Multiple Segments

If the route is nested, you can supply a model or an identifier for each dynamic
segment.

```app/router.js
Router.map(function() {
  this.route('photos', function(){
    this.route('photo', { path: '/:photo_id' }, function(){
      this.route('comments');
      this.route('comment', { path: '/comments/:comment_id' });
    });
  });
});
```

```app/templates/photo/index.hbs
<div class="photo">
  {{body}}
</div>

<p>{{#link-to "photos.photo.comment" primaryComment}}Main Comment{{/link-to}}</p>
```

If you specify only one model, it will represent the innermost dynamic segment `:comment_id`.
The `:photo_id` segment will use the current photo.

Alternatively, you could pass both a photo's ID and a comment to the helper:

```app/templates/photo/index.hbs
<p>
  {{#link-to 'photo.comment' 5 primaryComment}}
    Main Comment for the Next Photo
  {{/link-to}}
</p>
```

In the above example, the model hook for `PhotoRoute` will run with `params.photo_id = 5`.  The `model` hook for
`CommentRoute` _won't_ run since you supplied a model object for the `comment` segment. The comment's id will
populate the url according to `CommentRoute`'s `serialize` hook.

### Using link-to as an inline helper

In addition to being used as a block expression, the `link-to` helper
can also be used in inline form by specifying the link text as the first
argument to the helper:

```handlebars
A link in {{#link-to "index"}}Block Expression Form{{/link-to}},
and a link in {{link-to "Inline Form" "index"}}.
```

The output of the above would be:

```html
A link in <a href="/">Block Expression Form</a>,
and a link in <a href="/">Inline Form</a>.
```

### Adding additional attributes on a link

When generating a link you might want to set additional attributes for it. You can do this with additional
arguments to the `link-to` helper:

```handlebars
<p>
  {{link-to "Edit this photo" "photo.edit" photo class="btn btn-primary"}}
</p>
```

Many of the common HTML properties you would want to use like `class`, and `rel` will work. When
adding class names, Ember will also apply the standard `ember-view` and possibly `active` class names.

### Replacing history entries

The default behavior for `link-to` is to add entries to the browser's history
when transitioning between the routes. However, to replace the current entry in
the browser's history you can use the `replace=true` option:

```handlebars
<p>
  {{#link-to "photo.comment" 5 primaryComment replace=true}}
    Main Comment for the Next Photo
  {{/link-to}}
</p>
```