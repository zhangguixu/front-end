# Yielding Processes

浏览器在JavaScript运行时间上采取了限制,此类限制有两个

1. 调用栈尺寸限制
2. 长时间脚本限制（50ms以上）

在任何一种情况下，创建一个定时器造成UI线程暂停，如同它从一个任务切换到下一个任务。因此，`定时器代码复位所有相关的浏览器限制`，包括长运行脚本时间。此外，调用栈也在定时器代码中复位为零。这一特性使得定时器成为长运行JavaScript代码理想的跨浏览器解决方案。

例如在处理大规模的数组的时候，可以采用下列的方式来进行处理。

```javascript
function timedProcessArray(items,process,callback){
    var todo=items.concat();
    setTimeout(function(){
        var start=+new Date(); //小技巧，new Date()和+new Date()是不一样的
        do{
            process(todo.shift())  //进行批处理
        }while(todo.length>0&&(+new Date()-start)<50);

        if(todo.length>0){
            setTimeout(arguments.callee,25);
        }else{
            callback(items);
        }
    },25);
}
```