Webpack Remove Block
===================

Webpack loader to remove blocks of code marked by special comment tags. Useful for removing code that you don't want in your production webpack bundle (e.g. verbose console warnings, etc).

Support multiple block types.

### Example:

In your client js source files:

```javascript
/* debug:start */
console.log('debug');
/* debug:end */

var makeFoo = function(bar, baz) {
    // The following code will be removed with our webpack loader
    /* develblock:start */
    if (bar instanceof Bar !== true) {
        throw new Error('makeFoo: bar param is required and must be instance of Bar');
    }
    /* develblock:end */

    // This code would remain
    return new Foo(bar, baz);
}

```

```html
<div>
    <!-- develblock:start -->
    <div class="debug">

    </div>
    <!-- develblock:end -->
</div>
```

In your webpack config, specify the loader:

```javascript
var blocks = [{
    block: 'develblock',
    start: '/*',
    end: '*/'
}, {
    block: 'develblock',
    start: '<!--',
    end: '-->'
}, {
    block: 'develblock2',
    start: '//'
}, 'develblock3'];

if (process.env.NODE_ENV === 'development') {
    blocks.push({
        block: 'debug',
        start: '/*',
        end: '*/'
    });
}

module.exports = {
    module: {
        rules: [
            {
                test: /\.js$/,
                enforce: 'pre',
                exclude: /(node_modules|bower_components|\.spec\.js)/,
                use: [{
                    loader: 'webpack-remove-blocks',
                    options: {
                        blocks: ['develblock', {
                            block: 'develblock',
                            start: '//'
                        }]
                    }
                }]
            }, {
                test: /\.html$/,
                enforce: 'pre',
                use: [{
                    loader: 'webpack-remove-blocks',
                    options: {
                        blocks: blocks
                    }
                }]
            }
        ]
    }
};
```

## Laravel Mix sample

```
let mix = require( 'laravel-mix' );

/*
 |--------------------------------------------------------------------------
 | Webpack Remove Blocks
 |--------------------------------------------------------------------------
 |
 | Here you can define your custom remove tags. For example, you may use:
 | [ 'develblock', 'debug' ]
 | in order to remove "debug:start" and "debug:end" as well
 |
 */

const blocks = mix.inProduction() ? [ 'develblock' ] : null;

mix.webpackConfig( {
  module  : {
    rules : [
      {
        test    : /\.js$/,
        enforce : 'pre',
        exclude : /(node_modules|bower_components|\.spec\.js)/,
        use     : [
          {
            loader  : 'webpack-remove-blocks',
            options : {
              blocks : blocks
            }
          }
        ]
      }
    ]
  }
} );

```
