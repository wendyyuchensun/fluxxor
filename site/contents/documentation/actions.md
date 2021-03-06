---
title: Actions
template: page.ejs
---

Actions
=======

Actions, which are simply combinations of a string `type` and an object `payload`, represent the intent to perform some manipulation of the data in a Flux application. They are dispatched via the aptly named dispatcher to every store the dispatcher knows about. The only way to update stores is to send them actions.

To make programming with Flux more semantic, the [Flux](/documentation/flux.html) constructor takes an object with function values; these functions can call `this.dispatch(type, payload)` to dispatch an action to the dispatcher. Dispatching an action then looks like a normal method call. As an example, imagine you want to dispatch a method to add a URL at a certain index. Dispatching manually, you would write

```javascript
flux.dispatcher.dispatch({type: "ADD_URL", payload: {url: "http://google.com", index: 3}});
```

By using the `actions` hash, it would look more like this:

```javascript
var actions = {
  addUrl: function(url, index) {
    this.dispatch("ADD_URL", {url: url, index: index});
  }
};

var flux = new Fluxxor.Flux(stores, actions);

// somewhere later...
flux.actions.addUrl("http://google.com", 3);
```

Actions can be added to a `Flux` instance programmatically using `Flux#addAction` or `Flux#addActions`; see the [Flux documentation](/documentation/flux.html) for more details.

Accessing the Flux Instance
---------------------------

The [Flux](/documentation/flux.html) instance the action generators are bound to is available via `this.flux` from inside the functions.

```javascript
var actions = {
  addUrl: function(url) {
    var someData = this.flux.store("someStore").getData();
    this.dispatch("ADD_URL", {url: url, additionalData: someData});
  }
};

var stores = {
  someStore: new SomeStore(...);
};

var flux = new Fluxxor.Flux(stores, actions);
```

Namespaced Actions
------------------

Fluxxor will iterate over objects in your actions definition, allowing for namespaced actions:

```javascript
var actions = {
  user: {
    login: function() { this.dispatch(...); },
    logout: function() { this.dispatch(...); }
  },
  post: {
    add: function() { this.dispatch(...); },
    remove: function() { this.dispatch(...); }
  }
};

var flux = new Fluxxor.Flux(stores, actions);

// somewhere later...
flux.actions.user.login(...);
```

Responding to Actions
---------------------

[Stores](/documentation/stores.html) respond to specific action types via the `actions` object in their spec or via calls to `bindActions` during initialization.

```javascript
var UrlStore = Fluxxor.createStore({
  actions: {
    "ADD_URL": "handleAddUrl"
  },

  // ...

  handleAddUrl: function(payload, type) {
    this.urls.splice(payload.index, 0, payload.url);
    this.emit("change");
  }
});
```

See the documentation on [stores](/documentation/stores.html) for more information.
