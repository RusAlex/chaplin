# Chaplin 0.8.0 (31 March 2013)
* Added `Chaplin.helpers` component. It contains Chaplin-related
  functions. `Chaplin.utils` will contain generic functions.
    * `helpers.reverse` allows to get route URL by its name and params.
* Improved `Chaplin.Application`:
    * Separated router initialisation and start of listening for routing.
      The first one as before resides in `Application#initRouter`.
      `Application#startRouting`. You need to launch both.
      This is breaking change and without it your app will not start routing.
* Improved `Chaplin.Controller`:
    * All actions are now initialised with `params, route, options`
      instead of `params, options`. New `route` argument contains
      information about current route (`controller, action, name, path`)
      and about previous (`route.previous`) and `options` just contain
      options, passed to `Backbone.history.navigate`.
    * When using redirection in actions, controller will automatically
      dispose redirected controller.
* Improved `Chaplin.Router`:
    * All routes now have default names in format of
      (controller + '#' + action).
    * `Router#reverse` will now prepend mount point.
    * Removed RegExp routes. Use `constraints` route param and strings instead.
* Improved `Chaplin.Layout`:
    * Allowed registering regions.
    * Added `Layout#isExternalLink` that is used when clicking on any event
      and checks if current one is application-related.
* Improved `Chaplin.View`:
    * If `autoAttach` option is false, view will not be added to container.
    * Empty-selector regions are now considered as bound to root view element.
* Improved overall `View` and `CollectionView` performance for common cases.
* Improved internal API:
    * Renamed `matchRoute` global event to `router:match`
    * Renamed `startupController` global event to `dispatcher:dispatch`
    * Changed signatures of many `Dispatcher` methods, they now
      pass `route` too.

# Chaplin 0.7.0 (19 February 2013)
* Added support of regions and regions composition with `Chaplin.Composer`.
  Composer grants the ability for views (and related data) to be
  persisted beyond one controller action.
* Improved `Chaplin.Controller`:
    * Query string params are now passed to controllers
      (a feature removed from Backbone 0.9.9).
    * Controller actions will now receive an `options` hash
      as second argument, that contains `path`, `previousControllerName`
      and routing options. Previously, the second argument was just
      a `previousControllerName` string.
    * Fixed `Controller#redirectTo` signature (`url, options`).
    * `Controller#dispose` will now unbind all events bound by `listenTo` method.
* Improved `Chaplin.Dispatcher`:
    * Stop waiting for a Promise returned by a before action when another route is dispatched
      or the same is dispatched again.
* Improved `Chaplin.Router`:
    * The `params` and `options` objects are copied instead of changed to prevent conflicts.
      If you pass `params` and `options` along with the `!router:route` event,
      the controller action will receive a copy of them. Same for `Dispatcher`.
    * Fixed `root` option.
    * Fixed route reversals on empty patterns (for example, the top-level route).
* Improved `Chaplin.Collection`:
    * `Collection#dispose` will now unbind all events bound by `listenTo` method.
    * Removed `Collection#addAtomic` as it was barely used.
* Improved `Chaplin.Model`:
    * `Model#serialize` can be overridden on `Backbone.Model`s.
      Chaplin will use it, if available, and `Model#toJSON` if not.
    * `Model#dispose` will now unbind all events bound by `listenTo` method.
    * Improved time complexity of `Model#serialize` from O(n) to amortized O(1).
* Improved `Chaplin.utils`:
    * Added `utils.getAllPropertyVersions` that allows to gather all
      property versions from object’s prototypes.
    * Added `utils.escapeRegExp` that escapes all regular expression characters
    in string.
    * Removed `utils.wrapMethod`.
* Improved `Chaplin.View`:
    * Added `View#listen` property that allows to declaratively listen to
      model / collection / mediator / view events.
      Just like Backbone’s `View#events`, which is only for DOM events.
    * Added new `autoAttach` option which determines whether
      view should be automatically attached to DOM after render.
    * Renamed `View#afterRender` to `View#attach`.
    * Removed `View#afterInitialize`.
    * Removed `View#pass`. Please use
      [stickit](http://nytimes.github.com/backbone.stickit/) instead
      for advanced model-view binding.
    * Switched to `$el.toggle()` instead of manual CSS `display` setting.
    Which means non-block elements will behave correctly.
    * Switched to `Backbone.$` reference for DOM manipulation.
      This will automatically use jQuery, Zepto or Ender as DOM library.
    * Early error is now thrown when `View#events` is a function.
* Improved `Chaplin.CollectionView`:
    * Renamed `CollectionView#itemsResetted` to `CollectionView#itemsReset`.
    * Renamed `CollectionView#getView` to `CollectionView#initItemView`.
    * Renamed `CollectionView#showHideFallback` to `CollectionView#toggleFallback`.
    * Renamed `CollectionView#showHideLoadingIndicator` to `CollectionView#toggleLoadingIndicator`.
    * Removed `CollectionView#renderAndInsertItem`.
    * Item views will now be called with `autoRender: false`, which prevents rendering them twice.
    * Item views will now emit `addedToParent` event instead of `addedToDOM`
    when they are appended to collection view.
    * Optimised performance by not calling jQuery / Zepto `css` / `animate` when animations are disabled.

# Chaplin 0.6.0 (30 December 2012)
* Updated required Backbone version to 0.9.9+.
* Improved `Chaplin.Collection`:
    * Removed `Collection#update` since this function is now provided
      by Backbone itself. The `deep` option is now called `merge` and it
      defaults to true.
* Improved `Chaplin.Controller`:
    * Added Rails-like before action filters to `Controller`s.
    * Added `Controller#redirectToRoute` which works like
      `Controller#redirectTo`, but accepts route name instead of URL.
    * Added flexible `Controller#adjustTitle` method which sets window title.
    * Added `Backbone.Events` mix-in.
    * Removed `Controller#title` and `Controller#historyURL`.
    * Removed ability of redirecting to standalone controllers and action names
      in `Controller#redirectTo`.
* Improved `Chaplin.Router`:
    * Added support for named routes.
    * Added new global `!router:routeByName` event, which allows to
      navigate to some route by its reverse name.
    * Added new global `!router:reverse` event, which allows to get
      URL of route by its name.
    * Added `names` option to `Router#match`, which allows to name
      route’s regular expression matches.
    * Removed global `!startupController` event.
* Improved `Chaplin.View`:
    * Removed `View#modelBind`, `View#modelUnbind` and `View#modelUnbindAll`,
      since Backbone now implements superior `Events.listenTo` API.
    * Chaplin will now fix incorrect inheritance of view DOM events,
      bound in declarative manner (with `events` hash).
    * Moved `View#wrapMethod` to `Chaplin.utils.wrapMethod`.
    * `View#dispose` will now throw an error if
      `View#initialize` was called without `super`.
* Improved `Chaplin.CollectionView`:
    * `CollectionView#getTemplateData` no longer returns `items` property,
    which increases performance.
* Router options are now allowed to be passed in many places.
  New signatures are:
    * `!router:route` global event: path, *options*, callback
      (old sig is supported too)
    * `Dispatcher#matchRoute`: route, params, *options*
    * `Controller#redirectTo`: path or
      (controllerName, action, params, *options*)

# Chaplin 0.5.0 (15 November 2012)
* Improved and stabilized codebase.
* Moved `Chaplin.Subscriber` to `Chaplin.EventBroker`, which now mixins
  `publishEvent` method to children.
* Added `Chaplin.Delayer`, which sets unique and named timeouts and intervals
  so they can be cleared afterwards when disposing objects.
* Added `autoRender` option to `Chaplin.CollectionView`, like `Chaplin.View`.
  Defaults to true, replaces old `render` option.
* Added `serialize` method to collections
* Removed `CollectionView#viewsByCid` and `CollectionView#removeView` in favor
of consistent `View#subview` and `View#removeSubview`.
* Removed `CollectionView#initSyncMachine`.
* Removed `trigger`, `on` and `off` methods from `Chaplin.mediator`.
* Allowed passing of multiple event types to `View#delegate`.
* Made various aspects of `Chaplin.Layout` configurable.

# Chaplin 0.4.0 (28 June 2012)
* A lot of various global changes.
* Added tests for all components.
* Chaplin now can be used as a standalone library.

# Chaplin 0.3.0 (23 March 2012)
* Bug fix: In CollectionView, get the correct item position when rendering
the item view. Fixes the rendering of sorted Collections. Before the fix,
the item views might have been displayed in the wrong order. (@rendez)

# Chaplin 0.2.0 (9 March 2012)
* Fixed correct unsubscribing of global handlers when disposing a collection.
* The codebase now uses consistent code style (@paulmillr).

# Chaplin 0.1.0 (26 February 2012)
* Initial release.
