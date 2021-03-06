# Amphora HTML

The HTML renderer for Clay components that use [Handlebars](http://handlebarsjs.com/) templates.

## Install
`$ npm install --save amphora-html`

## The Why

HTML rendering was controlled entirely by [Amphora](https://github.com/nymag/amphora) in v2.x, which meant that Amphora was responsible for a lot of heavy lifting. Furthermore, rendering was handled by a module called [Multiplex Templates](https://www.npmjs.com/package/multiplex-templates) which did some :crystal_ball: dark magic :sparkles: to synchronously render component templates. This required some affordances from Amphora which added to the weight of the middleware. By separating renderers out into separate modules which can be plugged into Amphora >v2.x we can create renderers for specific requirements (XML, Amp, etc.) _and_ the rendering process can be moved to separate machines to create more maintainable systems.

## Integration

First, ensure that you have a compatible version of Amphora installed (v3.x or greater) and require `amphora-html` at the from wherever you are running Amphora.

```javascript
const amphoraHtml = require('amphora-html');
```


Second, register a `rootPath` with the renderer. This will allow the renderer to reference your components directory and static assets directory properly. Usually this is the root of your project, but that may not be the case for your implementation.

```javascript
// Register a root path for Amphora HTML
amphoraHtml.addRootPath(path.dirname(path.resolve('./package.json')));
```

If your templates require any custom [Handlebars Helpers](http://handlebarsjs.com/block_helpers.html) you can register them with the renderer's Handlebars instance. Simply pass in an object whose keys are the names of your helpers and whose values are the helper themselves. Like so:

```javascript
// My helpers
const helpers = {
  // set up handlebars helpers that rely on internal services
  'nameOfHelper': () => {
    // helper that does something you need.
    return true;
  }
};

// Register helpers
amphoraHtml.addHelpers(helpers);
```

Now that you have registered your helpers and provided a root path which Amphora HTML can work from, you can register your renderer with Amphora. Registering consists of providing a `renderers` object whose keys are the extension of an HTTP request and whose values are the renderer. You can also specify a `default` property whose value is the extension that Amphora should default to when rendering. This is handy for rendering routes who don't end in extensions, such as `mycoolsite.com/about/`.

```javascript
return amphora({
  app: app,
  renderers: {
    html: amphoraHtml,
    default: 'html'
  },
  providers: ['apikey', amphoraProvider],
  sessionStore: redisStore,
  plugins: [
    amphoraSearch
  ]
});
```

## Contributing
Want a feature or find a bug? Create an issue or a PR and someone will get on it.
