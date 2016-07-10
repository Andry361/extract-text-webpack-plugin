# extract text plugin for webpack

## Usage example with css

``` javascript
var ExtractTextPlugin = require("extract-text-webpack-plugin");
module.exports = {
	module: {
		loaders: [
			{ test: /\.css$/, loader: ExtractTextPlugin.extract("style-loader", "css-loader") }
		]
	},
	plugins: [
		new ExtractTextPlugin("styles.css")
	]
}
```

It moves every `require("style.css")` in entry chunks into a separate css output file. So your styles are no longer inlined into the javascript, but separate in a css bundle file (`styles.css`). If your total stylesheet volume is big, it will be faster because the stylesheet bundle is loaded in parallel to the javascript bundle.

Advantages:

* Fewer style tags (older IE has a limit)
* CSS SourceMap (with `devtool: "source-map"` and `css-loader?sourceMap`)
* CSS requested in parallel
* CSS cached separate
* Faster runtime (less code and DOM operations)

Caveats:

* Additional HTTP request
* Longer compilation time
* More complex configuration
* No runtime public path modification
* No Hot Module Replacement

## API

``` javascript
new ExtractTextPlugin([id: string], filename: string, [options])
```

* `id` Unique ident for this plugin instance. (For advanced usage only; by default, automatically generated)
* `filename` the filename of the result file. May contain `[name]`, `[id]` and `[contenthash]`.
  * `[name]` the name of the chunk
  * `[id]` the number of the chunk
  * `[contenthash]` a hash of the content of the extracted file
* `options`
  * `allChunks` extract from all additional chunks too (by default it extracts only from the initial chunk(s))
  * `disable` disables the plugin
  * `filenamefilter` function to modify path and filename before files are emitted
    
    ```
    {
      filenamefilter: function(filename) {
        // modify filename
        return filename;
      }
    }
    ```

The `ExtractTextPlugin` generates an output file per entry, so you must use `[name]`, `[id]` or `[contenthash]` when using multiple entries.

``` javascript
ExtractTextPlugin.extract([notExtractLoader], loader, [options])
```

Creates an extracting loader from an existing loader.

* `notExtractLoader` (optional) the loader(s) that should be used when the css is not extracted (i.e. in an additional chunk when `allChunks: false`)
* `loader` the loader(s) that should be used for converting the resource to a css exporting module.
* `options`
  * `publicPath` override the `publicPath` setting for this loader.

There is also an `extract` function on the instance. You should use this if you have more than one ExtractTextPlugin.

```javascript
let ExtractTextPlugin = require('extract-text-webpack-plugin');

// multiple extract instances
let extractCSS = new ExtractTextPlugin('stylesheets/[name].css');
let extractLESS = new ExtractTextPlugin('stylesheets/[name].less');

module.exports = {
  ...
  module: {
    loaders: [
      {test: /\.scss$/i, loader: extractCSS.extract(['css','sass'])},
      {test: /\.less$/i, loader: extractLESS.extract(['css','less'])},
      ...
    ]
  },
  plugins: [
    extractCSS,
    extractLESS
  ]
};
```

## Maybe a temp repo
This maybe a temporary repository. Once ```extract-text-webpack-plugin``` merge my pull request, please stick to the origin repo.

## License

MIT (http://www.opensource.org/licenses/mit-license.php)
