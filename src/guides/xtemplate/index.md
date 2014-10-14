#XTemplate基本介绍

xtemplate 是新一代的模板引擎，可通过 kissy gallery 使用，也可以独立使用不依赖任何KISSY其他的配置不用事先加载KISSY的种子文件，也可以在 node 环境中使用，具体的用法请看 [xtemplate on github](https://github.com/kissyteam/xtemplate)。下面仅介绍通过kissy gallery使用xtemplate，以及它的api和模板语法。
## 基本 api

### 构造器参数


<table class="table table-bordered table-striped">
    <thead>
    <tr>
        <th style="width: 100px;">name</th>
        <th style="width: 50px;">type</th>
        <th>description</th>
    </tr>
    </thead>
    <tbody>
    <tr>
        <td>content</td>
        <td>String</td>
        <td>模板字符串</td>
    </tr>
    <tr>
            <td>config</td>
            <td>Object</td>
            <td>
            对象属性含义：
            <table class="table table-bordered table-striped">
                    <thead>
                    <tr>
                        <th style="width: 100px;">name</th>
                        <th style="width: 50px;">type</th>
                        <th>description</th>
                    </tr>
                    </thead>
                    <tbody>
                    <tr>
                        <td>name</td>
                        <td>String</td>
                        <td>模板名字，用于编译时报错</td>
                    </tr>
                    <tr>
                        <td>commands</td>
                        <td>Object</td>
                        <td>命令定义，详见下文</td>
                        </tr>
                    </tbody>
                </table></td>
        </tr>
    </tbody>
</table>


### Methods


```javascript
String render(data:Object, callback:Function) // 渲染数据，参数含义如下
```

<table class="table table-bordered table-striped">
    <thead>
    <tr>
        <th style="width: 100px;">name</th>
        <th style="width: 50px;">type</th>
        <th>description</th>
    </tr>
    </thead>
    <tbody>
    <tr>
        <td>data</td>
        <td>Object</td>
        <td>数据对象</td>
    </tr>
    <tr>
            <td>callback</td>
            <td>Function</td>
            <td>完毕回调，第一个参数为 error，第二个参数为渲染结果。如果不提供，同步命令下 render 返回渲染结果。</td>
        </tr>
    </tbody>
</table>



## 浏览器端通过KISSY gallery 使用

注 ： KISSY@1.4.6 以上版本可这么使用xtemplate。
### 安装xtemplate

    npm install xtemplate

### 使用 gulp 来编译，gulpfile内容如下:

    var gulpXTemplate = require('gulp-xtemplate');
    var gulp = require('gulp');
    var xtemplate = require('xtemplate');
    gulp.task('default', function () {
        gulp.src('xtpl/**/*').pipe(gulpXTemplate({
            XTemplate: xtemplate
        })).pipe(gulp.dest('build'))
    });

### 引入KISSY的种子文件

    <script src="http://g.assets.daily.taobao.net/kissy/edge/2014.10.13/seed.js"></script>

### 配置包路径并使用

    <script>
        require.config({
            packages: {
                xtpl: {
                    base: './build'
                }
            }
        });
        require('xtpl/a-render', function (S, aRender) {
            console.log(aRender({
                x: 1,
                y: 2
            })); // 12
        });
    </script>

## 语法

### 基本类型

支持 true false null undefined number string map array

### map
值为基本类型

```
{{#with({
  x:2
})}}
{{x}}  // => 2
{{/with}}
```

### array
元素项为基本类型

```
{{#each([1,2,4])}}
{{this}}  // => 1 2 4
{{/each}}
```

### 转义

#### 使用 {{%%}}

```
{{%

{{x}}

%}}  // => {{x}}
```

#### 使用 \\{{

```
\{{a}}  // -> {{a}}
```

#### angularjs

如果是 angularjs 的话，可以直接配置 angularjs 使用不同的标记

[http://stackoverflow.com/questions/13671701/angularjs-twig-conflict-with-double-curly-braces](http://stackoverflow.com/questions/13671701/angularjs-twig-conflict-with-double-curly-braces)

### 注释


```
{{! zhu shi }}
```

### 变量渲染

转义：

```
{{x}}
```

非转义:

```
{{{x}}}
```

### 根数据访问

```javascript
var x = {name:1,arr:[{name:2}]}
```

```
{{#each(arr)}}
{{root.name}}{{name}} {{! 12 }}
{{/each}}
```

### 支持变量属性获取


```javascript
var x = {
    y: 1
};
var y = [1, 2, 3];
var z = {
    q: 1
};
var x = 'q';
```

```
{{x.y}} // 1
{{y[1]}} // 2
{{z[x]}} // 1
```

### 调用变量方法

注意：该用法会影响性能，推荐自定义命令

```javascript
var x = [1, 2, 3];
```

```
{{#each(x.slice(1))}}{{this}} {{/each}} // => 2 3
```

### 变量运算

支持 + - * / %

```
{{x+y}}
{{x + "1"}}
{{ y - 1 }}
```

### 比较操作

支持 if elseif else === !=== > >= < <=

```
{{#if( x===1 )}}
1
{{elseif (x===2)}}
2
{{else}}
3
{{/if}}

{{#if ( (x+1) > 2 )}}
{{/if}}
```

### 逻辑操作

支持 || &&

```
{{#if(x>1 && y<2)}}
{{/if}}
```

```
{{#if(!x)}}
{{/if}}
```

### 循环

可以对数组或对象进行循环操作，默认获取循环对象值为 {{this}}，键为 {{xindex}} , 也可以指定.

```javascript
var x = ['a', 'b'];
```

```
{{#each(x)}}
{{xindex}} {{this}} // 0 a 1 b
{{/each}}

{{#each(x,"value","key")}}
{{key}} {{value}} // 0 a 1 b
{{/each}}
```

### 范围循环

可以对 start 和 end(不包含) 范围内的数字进行循环

```
{{#each(range(0,3))}}{{this}}{{/each}} // 012
{{#each(range(3,0))}}{{this}}{{/each}} // 321
{{#each(range(3,0,2))}}{{this}}{{/each}} // 31
```

### 设置操作


```
{{set(x=1)}}
{{set(y=3,z=2)}}
{{x}} // 1
{{y+z}} // 5
```

### 宏


```
// 声明
{{#macro("test","param" default=1)}}param is {{param}} {{default}}{{/macro}}

// 调用宏
{{macro("test","2")}} // => param is 2 1

{{macro("test", "2", 2)}} // => param is 2 2
```

### 包含操作

x.xtpl

```
{{z}}
```

y.xtpl

```
{{include("x")}}
```

### 继承

layout.xtpl

```html
<!doctype html>
<html>
    <head>
        <meta name="charset" content="utf-8" />
        <title>{{title}}</title>
        {{{block ("head")}}} // 坑
    </head>
    <body>
        {{{include ("./header")}}}
        {{{block ("body")}}}  // 坑
        {{{include ("./footer")}}}
    </body>
</html>
```

index.xtpl

```html
{{extend ("./layout1")}}

// 填
{{#block ("head")}}
    <link type="text/css" href="test.css" rev="stylesheet" rel="stylesheet"/>
{{/block}}

// 填
{{#block ("body")}}
    <h2>{{title}}</h2>
{{/block}}
```

### 自定义命令


#### nodejs 全局命令


同步调用行内：

```javascript
var xtpl = require('xtpl');
xtpl.XTemplate.addCommand('xInline',function(scope, option){
  return option.params[0]+'1';
});
```

此时模板中可通过 {{}} 来转义命令返回的内容.


或使用 buffer (详见下面 Buffer api)

```javascript
var xtpl = require('xtpl');
xtpl.XTemplate.addCommand('xInline',function(scope, option, buffer){
  return buffer.write(option.params[0] + 1);
});
```

此时模板不能控制命令返回内容是否转义.

```
{{xInline(1)}} // => 2
```

同步调用块级：

```javascript
var xtpl = require('xtpl');
xtpl.XTemplate.addCommand('xBlock',function(scope, option, buffer){
  return option.fn(scope, buffer).write(option.params[0]); //option.fn(scope, buffer)返回的是块级标签内的内容，例如下面例子的 '2'
});
```

```
{{#xBlock(1)}}
2
{{/xBlock}}
// => 21
```

异步调用行内

```javascript
var xtpl = require('xtpl');
xtpl.XTemplate.addCommand('xInline',function(scope, option,buffer){
  buffer = buffer.async(function(newBuffer){
    setTimeout(function(){
        newBuffer.write(option.params[0] + 1).end();
    },10);
  });
  return buffer;
});
```

```
{{xInline(1)}} // => 2
```

异步调用块级：

```javascript
var xtpl = require('xtpl');
xtpl.XTemplate.addCommand('xInline',function(scope, option,buffer){
  buffer = buffer.async(function(newBuffer){
    setTimeout(function(){
        var newScope = xtpl.XTemplate.Scope({ret:2});
        newScope.setParent(scope);
        option.fn(newScope, newBuffer);
    },10);
  });
  buffer.write(option.params[0]);
  return buffer;
});
```

```
{{#xBlock(1)}}
{{ret}}
{{/xBlock}}
// => 21
```

#### 浏览器命令


全局：

```javascript
require('xtemplate/runtime',function(XTemplate){
    XTemplate.addCommand(...) // 同 nodejs
});
```

局部：

x-xtpl.html:

```
{{x()}}
```

```javascript
require('xtemplate/runtime, x-xtpl',function(XTemplate,x){
    new XTemplate(x, {
        commands:{
            x:function(){
                // ... 同 node
            }
        }
    })...
});
```

### Buffer api

#### Methods


```javascript
Buffer write(data:String, escape:Boolean) // 写数据到缓冲区
```

<table class="table table-bordered table-striped">
    <thead>
    <tr>
        <th style="width: 100px;">name</th>
        <th style="width: 50px;">type</th>
        <th>description</th>
    </tr>
    </thead>
    <tbody>
    <tr>
        <td>data</td>
        <td>String</td>
        <td>将要写到缓冲区的字符串</td>
    </tr>
    <tr>
            <td>escape</td>
            <td>Boolean</td>
            <td>是否转义</td>
        </tr>
    </tbody>
</table>



```javascript
Buffer async(fn:Function) // 产生新的异步缓冲区，新的缓冲区为 fn 回调函数的第一个参数

Buffer end(data, escape) // 参数含义同 write 函数。 标志缓冲区数据填充完毕，用于通知异步缓冲区的结束。

Buffer error(reason) // 触发 render 异步回调为失败。 reason 为回调的第一个参数.
```

### Scope api


#### Members


```javascript
parent // 上级作用域

root // 顶层作用域
```

#### Methods


```javascript
void setParent(scope: Scope) // 设置当前作用域的上级作用域

void setData(data) // 设置当前作用域内数据

var getData() // 获取当前作用域内数据

void set(name, value) // 设置当前作用域内附属数据

void get(name) // 获取当前作用域内数据值（包括附属数据）
```

## Reserved words

**Xtemplate内置以下命令，请避免重复定义同名命令**

<table class="table table-bordered table-striped">
    <tr>
        <td>block</td><td>debugger</td><td>each</td><td>extend</td>
    </tr>
    <tr>
        <td>if</td><td>include</td><td>marco</td><td>parse</td>
    </tr>
    <tr>
        <td>range</td><td>set</td><td>with</td><td></td>
    </tr>
</table>