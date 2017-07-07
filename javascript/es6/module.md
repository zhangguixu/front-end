# Module

通常模块化有requirejs,seajs,commonjs等等，直接看ES6的模块化是什么样子的

在`module.js`，有

```javascript
export var port = 3000;
export function getAccounts(url){
    //...
}
```

将其导入到`main.js`中，

```javascript
import {port,getAccounts} from 'module';
console.log(port); //3000
```

也可以把整个模块导入，并且重新命名为`serivce`

```javascript
import * as service from 'module';
console.log(service.port); //3000
```