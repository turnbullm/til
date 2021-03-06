Jquery plugin scaffold
======================

Simple:

```
(function ($) {

    var privateValue = 'shh',
        publicValue = 'hello everyone';

    var privateFunction = function() {
        //...
    };

    var publicFunction = function() {
      //...
    };

    // Method 1:

    $.namespace = {}
    $.namespace.publicFunction = publicFunction
    $.namespace.publicValue = publicValue

    //Method 2: (personal preference)

    $.extend({
        namespace: {
            publicValue: publicValue,
            publicFunction: publicFunction
        }
    });

})(jQuery);

console.log(jQuery.namespace.publicFunction())
console.log(jQuery.namespace.publicValue)

```

Advanced:

```
/**
 * $.examplePlugin.globalPublicFunction()
 *
 * $('selector').examplePlugin({
 *     optionKey: 'optionValue'
 * });
 *
 * pluginInstance = $('selector').data('examplePlugin');
 *
 * $('selector')
 *     .data('examplePlugin')
 *     .instancePublicFunction()
 *     .instancePublicFunction();
 */
(function($) {

    var PLUGIN_NAME = 'examplePlugin',
        instanceDefaultOptions = {
            key: 'value'
        },
        instances = [];

    var globalApi = {
        instances: instances,
        globalPublicFunction: globalPublicFunction
    };

    function pluginInstance(el, options) {

        var options = $.extend(true, {}, instanceDefaultOptions, options),
            $el = $(el);

        init();

        var instanceApi = {
            el: el,
            $el: $el,
            instancePublicFunction: instancePublicFunction
        };
        return instanceApi;

        function init() {
            instancePrivateFunction();
        }

        function instancePublicFunction() {
            instancePrivateFunction();
            return instanceApi; //chaining
        }

        function instancePrivateFunction()
        {
            //...
            return instanceApi; //chaining
        }

    }

    function globalPublicFunction()
    {
        globalPrivateFunction();
        return globalApi; //chaining
    }

    function globalPrivateFunction()
    {
        //...
        return globalApi; //chaining
    }

    //jQuery namespace
    $[PLUGIN_NAME] = globalApi;

    //jQuery prototype
    //Stores plugin instance within element.data(PLUGIN_NAME) and $[PLUGIN_NAME].instances
    $.fn[PLUGIN_NAME] = function(options) {
        return this.each(function() {
            var $this = $(this);
            if ($this.data(PLUGIN_NAME) === undefined) {
                var newPluginInstance = new pluginInstance(this, options);
                $this.data(PLUGIN_NAME, newPluginInstance);
                instances.push(newPluginInstance);
            }
        });
    };

})(jQuery);
```

Coffeescript:

```
(($) ->

  $.ExampleClass = class ExampleClass

    DEFAULT_OPTIONS =
      option_one: 'default value'

    constructor: (@$el, options = {}) ->
      @options = $.extend(true, {}, DEFAULT_OPTIONS, options)

  $.fn.exampleClass = (options = {}) ->
    return @.each ->
      $el = $(@)
      instance = new $.ExampleClass($el, options)
      $el.data('example-class', instance)

)(jQuery)

(($) ->
  $ ->
    $('element').exampleClass()

    $('element').exampleClass
      option_one: 'new value'
)(jQuery)
```
