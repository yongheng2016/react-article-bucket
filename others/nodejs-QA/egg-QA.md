#### 1.egg-mysql需要指定主键来更新
报错信息如下:
<pre>
unhandledRejectionError: Can not auto detect update condition, please set options.where, or make sure obj.id exists
</pre>
解决方法:
```js
 yield scope.app.mysql.update("data_json_markdown", {
    id:componentMap.id,
    //这里必须指定，对应于数据的primary key
    gmt_create: new Date(),
    gmt_modified: new Date()
    })
```

#### 2.egg不管如何对于查询(Query)操作都是返回空但是不报错
比如下面的例子:
```js
function processData(scope,x,x){
  var componentMap = yield scope.app.mysql.get('data_json_markdown', { id: 42 });
  //此时你无法再访问数据库了
}
var update = function(scope) {
  //自动执行我们的regenerator函数
co.wrap(function*(scope) {
   //代码片段1
   //var componentMap = yield scope.app.mysql.get('data_json_markdown', { id: 42 });
   //在这里直接yield是可以访问到数据库的
   //下面是代码片段2
   //processData(scope,x,x)
 })
}
module.exports = function*() {
  update(this);
};
```
总之，在egg中如果要访问到数据库，必须通过`yield`来完成，而通过外部的processData函数直接拿到scope这种方式是不行的，后续再对这一部分内容进行深入研究。
