<div align="center">
  <img width="200" height="200" src="https://cdn.worldvectorlogo.com/logos/javascript.svg">
  <a href="https://webpack.js.org/">
    <img width="200" height="200" vspace="" hspace="25" src="https://cdn.rawgit.com/webpack/media/e7485eb2/logo/icon-square-big.svg">
  </a>
  <h1>mini-css-extract-plugin</h1>
</div>

[![npm][npm]][npm-url]
[![node][node]][node-url]
[![deps][deps]][deps-url]
[![tests][tests]][tests-url]
[![coverage][cover]][cover-url]
[![chat][chat]][chat-url]
[![size][size]][size-url]

# mini-css-extract-plugin

This plugin extracts CSS into separate files. It creates a CSS file per JS file which contains CSS. It supports On-Demand-Loading of CSS and SourceMaps.

It builds on top of a new webpack v4 feature (module types) and requires webpack 4 to work.

Compared to the extract-text-webpack-plugin:

- Async loading
- No duplicate compilation (performance)
- Easier to use
- Specific to CSS

## Getting Started

To begin, you'll need to install `mini-css-extract-plugin`:

```bash
npm install --save-dev mini-css-extract-plugin
```

It's recommended to combine `mini-css-extract-plugin` with the [`css-loader`](https://github.com/webpack-contrib/css-loader)

Then add the loader and the plugin to your `webpack` config. For example:

**style.css**

```css
body {
  background: green;
}
```

**component.js**

```js
import './style.css';
```

**webpack.config.js**

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  plugins: [new MiniCssExtractPlugin()],
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [MiniCssExtractPlugin.loader, 'css-loader'],
      },
    ],
  },
};
```

## Options

### Plugin Options

|                 Name                  |         Type         |       Default       | Description                                              |
| :-----------------------------------: | :------------------: | :-----------------: | :------------------------------------------------------- |
|      **[`filename`](#filename)**      | `{String\|Function}` |    `[name].css`     | This option determines the name of each output CSS file  |
| **[`chunkFilename`](#chunkFilename)** | `{String\|Function}` | `based on filename` | This option determines the name of non-entry chunk files |
|   **[`ignoreOrder`](#ignoreOrder)**   |     `{Boolean}`      |       `false`       | Remove Order Warnings                                    |

#### `filename`

Type: `String|Function`
Default: `[name].css`

This option determines the name of each output CSS file.

Works like [`output.filename`](https://webpack.js.org/configuration/output/#outputfilename)

#### `chunkFilename`

Type: `String|Function`
Default: `based on filename`

> i Specifying `chunkFilename` as a `function` is only available in webpack@5

This option determines the name of non-entry chunk files.

Works like [`output.chunkFilename`](https://webpack.js.org/configuration/output/#outputchunkfilename)

#### `ignoreOrder`

Type: `Boolean`
Default: `false`

Remove Order Warnings.
See [examples](#remove-order-warnings) below for details.

### Loader Options

|              Name               |         Type         |              Default               | Description                                                                       |
| :-----------------------------: | :------------------: | :--------------------------------: | :-------------------------------------------------------------------------------- |
| **[`publicPath`](#publicPath)** | `{String\|Function}` | `webpackOptions.output.publicPath` | Specifies a custom public path for the external resources like images, files, etc |
|   **[`esModule`](#esModule)**   |     `{Boolean}`      |               `true`               | Use ES modules syntax                                                             |
|    **[`modules`](#modules)**    |      `{Object}`      |            `undefined`             | Configuration CSS Modules                                                         |

#### `publicPath`

Type: `String|Function`
Default: the `publicPath` in `webpackOptions.output`

Specifies a custom public path for the external resources like images, files, etc inside `CSS`.
Works like [`output.publicPath`](https://webpack.js.org/configuration/output/#outputpublicpath)

##### `String`

**webpack.config.js**

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  plugins: [
    new MiniCssExtractPlugin({
      // Options similar to the same options in webpackOptions.output
      // both options are optional
      filename: '[name].css',
      chunkFilename: '[id].css',
    }),
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              publicPath: '/public/path/to/',
            },
          },
          'css-loader',
        ],
      },
    ],
  },
};
```

##### `Function`

**webpack.config.js**

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  plugins: [
    new MiniCssExtractPlugin({
      // Options similar to the same options in webpackOptions.output
      // both options are optional
      filename: '[name].css',
      chunkFilename: '[id].css',
    }),
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              publicPath: (resourcePath, context) => {
                return path.relative(path.dirname(resourcePath), context) + '/';
              },
            },
          },
          'css-loader',
        ],
      },
    ],
  },
};
```

#### `esModule`

Type: `Boolean`
Default: `true`

By default, `mini-css-extract-plugin` generates JS modules that use the ES modules syntax.
There are some cases in which using ES modules is beneficial, like in the case of [module concatenation](https://webpack.js.org/plugins/module-concatenation-plugin/) and [tree shaking](https://webpack.js.org/guides/tree-shaking/).

You can enable a CommonJS syntax using:

**webpack.config.js**

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  plugins: [new MiniCssExtractPlugin()],
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              esModule: false,
            },
          },
          'css-loader',
        ],
      },
    ],
  },
};
```

#### `modules`

Type: `Object`
Default: `undefined`

Configuration CSS Modules.

##### `namedExport`

Type: `Boolean`
Default: `false`

Enables/disables ES modules named export for locals.

> ⚠ Names of locals are converted to `camelCase`.

> ⚠ It is not allowed to use JavaScript reserved words in css class names.

> ⚠ Options `esModule` and `modules.namedExport` in `css-loader` and `MiniCssExtractPlugin.loader` should be enabled.

**styles.css**

```css
.foo-baz {
  color: red;
}
.bar {
  color: blue;
}
```

**index.js**

```js
import { fooBaz, bar } from './styles.css';

console.log(fooBaz, bar);
```

You can enable a ES module named export using:

**webpack.config.js**

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  plugins: [new MiniCssExtractPlugin()],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              esModule: true,
              modules: {
                namedExport: true,
              },
            },
          },
          {
            loader: 'css-loader',
            options: {
              esModule: true,
              modules: {
                namedExport: true,
                localIdentName: 'foo__[name]__[local]',
              },
            },
          },
        ],
      },
    ],
  },
};
```

## Examples

### Minimal example

**webpack.config.js**

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  plugins: [
    new MiniCssExtractPlugin({
      // Options similar to the same options in webpackOptions.output
      // all options are optional
      filename: '[name].css',
      chunkFilename: '[id].css',
      ignoreOrder: false, // Enable to remove warnings about conflicting order
    }),
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              // you can specify a publicPath here
              // by default it uses publicPath in webpackOptions.output
              publicPath: '../',
            },
          },
          'css-loader',
        ],
      },
    ],
  },
};
```

### Common use case

`mini-css-extract-plugin` is more often used in `production` mode to get separate css files.
For `development` mode (including `webpack-dev-server`) you can use `style-loader`, because it injects CSS into the DOM using multiple <style></style> and works faster.

> i Do not use together `style-loader` and `mini-css-extract-plugin`.

**webpack.config.js**

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const devMode = process.env.NODE_ENV !== 'production';

const plugins = [];
if (!devMode) {
  // enable in production only
  plugins.push(new MiniCssExtractPlugin());
}

module.exports = {
  plugins,
  module: {
    rules: [
      {
        test: /\.(sa|sc|c)ss$/,
        use: [
          devMode ? 'style-loader' : MiniCssExtractPlugin.loader,
          'css-loader',
          'postcss-loader',
          'sass-loader',
        ],
      },
    ],
  },
};
```

### The `publicPath` option as function

**webpack.config.js**

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  plugins: [
    new MiniCssExtractPlugin({
      // Options similar to the same options in webpackOptions.output
      // both options are optional
      filename: '[name].css',
      chunkFilename: '[id].css',
    }),
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              publicPath: (resourcePath, context) => {
                // publicPath is the relative path of the resource to the context
                // e.g. for ./css/admin/main.css the publicPath will be ../../
                // while for ./css/main.css the publicPath will be ../
                return path.relative(path.dirname(resourcePath), context) + '/';
              },
            },
          },
          'css-loader',
        ],
      },
    ],
  },
};
```

### Advanced configuration example

This plugin should not be used with `style-loader` in the loaders chain.

Here is an example to have both HMR in `development` and your styles extracted in a file for `production` builds.

(Loaders options left out for clarity, adapt accordingly to your needs.)

You should not use `HotModuleReplacementPlugin` plugin if you are using a `webpack-dev-server`.
`webpack-dev-server` enables / disables HMR using `hot` option.

**webpack.config.js**

```js
const webpack = require('webpack');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const devMode = process.env.NODE_ENV !== 'production';

const plugins = [
  new MiniCssExtractPlugin({
    // Options similar to the same options in webpackOptions.output
    // both options are optional
    filename: devMode ? '[name].css' : '[name].[contenthash].css',
    chunkFilename: devMode ? '[id].css' : '[id].[contenthash].css',
  }),
];
if (devMode) {
  // only enable hot in development
  plugins.push(new webpack.HotModuleReplacementPlugin());
}

module.exports = {
  plugins,
  module: {
    rules: [
      {
        test: /\.(sa|sc|c)ss$/,
        use: [
          MiniCssExtractPlugin.loader,
          'css-loader',
          'postcss-loader',
          'sass-loader',
        ],
      },
    ],
  },
};
```

### Hot Module Reloading (HMR)

Note: HMR is automatically supported in webpack 5. No need to configure it. Skip the following:

The `mini-css-extract-plugin` supports hot reloading of actual css files in development.
Some options are provided to enable HMR of both standard stylesheets and locally scoped CSS or CSS modules.
Below is an example configuration of mini-css for HMR use with CSS modules.

You should not use `HotModuleReplacementPlugin` plugin if you are using a `webpack-dev-server`.
`webpack-dev-server` enables / disables HMR using `hot` option.

**webpack.config.js**

```js
const webpack = require('webpack');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

const plugins = [
  new MiniCssExtractPlugin({
    // Options similar to the same options in webpackOptions.output
    // both options are optional
    filename: devMode ? '[name].css' : '[name].[contenthash].css',
    chunkFilename: devMode ? '[id].css' : '[id].[contenthash].css',
  }),
];
if (devMode) {
  // only enable hot in development
  plugins.push(new webpack.HotModuleReplacementPlugin());
}

module.exports = {
  plugins,
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {},
          },
          'css-loader',
        ],
      },
    ],
  },
};
```

### Minimizing For Production

To minify the output, use a plugin like [optimize-css-assets-webpack-plugin](https://github.com/NMFR/optimize-css-assets-webpack-plugin).
Setting `optimization.minimizer` overrides the defaults provided by webpack, so make sure to also specify a JS minimizer:

**webpack.config.js**

```js
const TerserJSPlugin = require('terser-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin');

module.exports = {
  optimization: {
    minimizer: [new TerserJSPlugin({}), new OptimizeCSSAssetsPlugin({})],
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: '[name].css',
      chunkFilename: '[id].css',
    }),
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader'],
      },
    ],
  },
};
```

### Using preloaded or inlined CSS

The runtime code detects already added CSS via `<link>` or `<style>` tag.
This can be useful when injecting CSS on server-side for Server-Side-Rendering.
The `href` of the `<link>` tag has to match the URL that will be used for loading the CSS chunk.
The `data-href` attribute can be used for `<link>` and `<style>` too.
When inlining CSS `data-href` must be used.

### Extracting all CSS in a single file

The CSS can be extracted in one CSS file using `optimization.splitChunks.cacheGroups`.

**webpack.config.js**

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  optimization: {
    splitChunks: {
      cacheGroups: {
        styles: {
          name: 'styles',
          test: /\.css$/,
          chunks: 'all',
          enforce: true,
        },
      },
    },
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: '[name].css',
    }),
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader'],
      },
    ],
  },
};
```

### Extracting CSS based on entry

You may also extract the CSS based on the webpack entry name.
This is especially useful if you import routes dynamically but want to keep your CSS bundled according to entry.
This also prevents the CSS duplication issue one had with the ExtractTextPlugin.

```js
const path = require('path');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

function recursiveIssuer(m) {
  if (m.issuer) {
    return recursiveIssuer(m.issuer);
  } else if (m.name) {
    return m.name;
  } else {
    return false;
  }
}

module.exports = {
  entry: {
    foo: path.resolve(__dirname, 'src/foo'),
    bar: path.resolve(__dirname, 'src/bar'),
  },
  optimization: {
    splitChunks: {
      cacheGroups: {
        fooStyles: {
          name: 'foo',
          test: (m, c, entry = 'foo') =>
            m.constructor.name === 'CssModule' && recursiveIssuer(m) === entry,
          chunks: 'all',
          enforce: true,
        },
        barStyles: {
          name: 'bar',
          test: (m, c, entry = 'bar') =>
            m.constructor.name === 'CssModule' && recursiveIssuer(m) === entry,
          chunks: 'all',
          enforce: true,
        },
      },
    },
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: '[name].css',
    }),
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader'],
      },
    ],
  },
};
```

### Filename Option as function

With the `filename` option you can use chunk data to customize the filename.
This is particularly useful when dealing with multiple entry points and wanting to get more control out of the filename for a given entry point/chunk.
In the example below, we'll use `filename` to output the generated css into a different directory.

**webpack.config.js**

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  plugins: [
    new MiniCssExtractPlugin({
      filename: ({ chunk }) => `${chunk.name.replace('/js/', '/css/')}.css`,
    }),
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader'],
      },
    ],
  },
};
```

### Long Term Caching

For long term caching use `filename: "[contenthash].css"`. Optionally add `[name]`.

**webpack.config.js**

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  plugins: [
    new MiniCssExtractPlugin({
      filename: '[name].[contenthash].css',
      chunkFilename: '[id].[contenthash].css',
    }),
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader'],
      },
    ],
  },
};
```

### Remove Order Warnings

For projects where css ordering has been mitigated through consistent use of scoping or naming conventions, the css order warnings can be disabled by setting the ignoreOrder flag to true for the plugin.

**webpack.config.js**

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  plugins: [
    new MiniCssExtractPlugin({
      ignoreOrder: true,
    }),
  ],
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [MiniCssExtractPlugin.loader, 'css-loader'],
      },
    ],
  },
};
```

### Media Query Plugin

If you'd like to extract the media queries from the extracted CSS (so mobile users don't need to load desktop or tablet specific CSS anymore) you should use one of the following plugins:

- [Media Query Plugin](https://github.com/SassNinja/media-query-plugin)
- [Media Query Splitting Plugin](https://github.com/mike-diamond/media-query-splitting-plugin)

## Contributing

Please take a moment to read our contributing guidelines if you haven't yet done so.

[CONTRIBUTING](./.github/CONTRIBUTING.md)

## License

[MIT](./LICENSE)

[npm]: https://img.shields.io/npm/v/mini-css-extract-plugin.svg
[npm-url]: https://npmjs.com/package/mini-css-extract-plugin
[node]: https://img.shields.io/node/v/mini-css-extract-plugin.svg
[node-url]: https://nodejs.org
[deps]: https://david-dm.org/webpack-contrib/mini-css-extract-plugin.svg
[deps-url]: https://david-dm.org/webpack-contrib/mini-css-extract-plugin
[tests]: https://github.com/webpack-contrib/mini-css-extract-plugin/workflows/mini-css-extract-plugin/badge.svg
[tests-url]: https://github.com/webpack-contrib/mini-css-extract-plugin/actions
[cover]: https://codecov.io/gh/webpack-contrib/mini-css-extract-plugin/branch/master/graph/badge.svg
[cover-url]: https://codecov.io/gh/webpack-contrib/mini-css-extract-plugin
[chat]: https://badges.gitter.im/webpack/webpack.svg
[chat-url]: https://gitter.im/webpack/webpack
[size]: https://packagephobia.now.sh/badge?p=mini-css-extract-plugin
[size-url]: https://packagephobia.now.sh/result?p=mini-css-extract-plugin
