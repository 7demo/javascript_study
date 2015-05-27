###requirejs


**基本介绍**
requrejs是AMD规范的实践，有利于代码模块化。

**基本用法**
页面通过<code><script src="js/main.js" src='js/require.js'></script></code>.引入requirejs与js的入口文件。一个页面只有只能引用一次requirejs，只能有一个入口文件，如强行引用多个requirejs与入口文件，则只会加载顺序最前面的那个。

模块js的加载的路径，是相对于baseurl的，默认值与data-main的属性值一致。如上的baseUrl为`/lib/js`。如果为进行`config`配置baseurl，则使用默认值。

进行引用脚本时候，不需要带`.js`。不过当引用路径为以下三种时候，则路径是相当于当前的html:
·以`.js`结束
·以`/`开始
·包含`http https`协议。

关于文件夹的架构，官网不建议多级嵌套目录来组织代码结构，而建议都放在baseurl中，要么是一个项目库/第三方库的扁平结构。paths可以配置模块，也可以配置模块路径。



入口文件即requirejs开始先要进行配置（配置文件的翻译会另外记录）.
<pre>
    require.config({

    })
</pre>

配置为全局。即开始位置进行或者主文件进行配置，其他文件模块即可使用。由于requirejs加载的模块为异步加载，则页面正常通过src加载的js则不能保证顺序。

通过define定义方法定义模块。可以返回一切对象。若define需要依赖其他模块，则第一个则依赖其他模块。则第一个参数，为依赖模块的数组，第二个为回调函数。参数为依赖模块的注入，需要注意顺序。define的第一个参数还可以为字符串，此时为模块id，但是不建议自己设定，应有第三方工具设定。

并且requirejs可以包装commonjs规范的模块。如下：
`commonjs`模块
<pre>
    var foo = function () {};
    exports = foo;
</pre>
改成如下：
<pre>
    define(function (require, exports, module) {
        var foo = function () {};
        exports = foo;
    })
</pre>
注意就可以被其他requirejs模块引用。以上方法也可以通过`require`引用其他模块。

<pre>
    define(function (require, exports, module) {
        var  a = require('a')
        var aa = function () {
            return 'commonjs'
        }
        exports.aa = aa;
    })
</pre>

知道模块名字但是不知道路径的时候，可以通过依赖`require`使用同文件下的模块。

<pre>
    define(['require'], function (require) {
        var aa = require('./c')
    })
</pre>

可以直接写成：

<pre>
     define(function (require) {
        var aa = require('./c')
    })
</pre>

下列方法可以拿到模块的相对路径

<pre>
    define(function(require) {
        var url = require.toUrl('./c');
    })
</pre>

**循环依赖**

如果a依赖b,b依赖a。有可能，在a中调用b的时候，a为undefined。则需要为b定义好后再用require方法获取。<strong>待解决</strong>

**jsonp**

jsonp的请求地址中的callback设定为define,则可定义为define，则返回数据可以作为一个模块。

<pre>
    require(['http://example.com/api/data.json?callback=define'], function (data) {

    })
</pre>

需要注意的是，无法处理超时。data也必须为object。也不能处理长连接，因为require会缓存第一个请求的数据。

require有undef模块，暂定。

require会把所有依赖的模块通过<code>head.appendChild</code>为每个添加script标签。

或者是 直接把配置写到html页面如下：
<pre>

     <script type="text/javascript" src='/lib/js/require.js'></script>
     <script type="text/javascript">
        require.config({
            baseUrl : '/lib/js/page1',
            paths : {
                page2 : '../page2'
            }
        })
        require(['a', 'b', 'page2/c', 'page2/e'], function (a, b, c, e) {
            console.log(a)
            console.log(b)
            console.log(c)
        })
     </script>
</pre>

或者作为全局变量提前进行提前声明：



<pre>

    <s>
        var require = {
            deps: ["some/module1", "my/module2", "a.js", "b.js"],
            callback: function(module1, module2) {
                //This function will be called when all the dependencies
                //listed above in deps are loaded. Note that this
                //function could be called before the page is loaded.
                //This callback is optional.
            }
        };
    </s>
    <s src="scripts/require.js"></s>
    <s type="text/javascript">
        requirejs.config({
            bundles: {
                'primary': ['main', 'util', 'text', 'text!template.html'],
                'secondary': ['text!secondary.html']
            }
        });

        require(['util', 'text'], function(util, text) {
            //The script for module ID 'primary' was loaded,
            //and that script included the define()'d
            //modules for 'util' and 'text'
        });
    </s>
</pre>

**压缩**




