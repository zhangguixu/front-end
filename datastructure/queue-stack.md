# 队列与栈

队列和栈有着广泛的实际应用，例如内存管理、消息队列等等。

在js中，使用数组可以很轻松的实现队列和栈。

## 队列

队列(Queue)：也是运算受限的线性表。是一种先进先出(First In First Out ，简称FIFO)的线性表。只允许在表的一端front进行插入，而在另一端rear进行删除。

* 队首(front) ：允许进行删除的一端称为队首。
* 队尾(rear) ：允许进行插入的一端称为队尾。

队列的基本操作有：

1. 入队列
2. 出队列
3. 是否为空
4. 长度

```javascript
class Queue {
	constructor (arr) {
		this._queue = arr || [];
	}
	isEmpty () {
		return (this._queue.length == 0 );
	}
	size () {
		return this._queue.length;
	}
	in (val) {
		this._queue.push(val);
	}
	out () {
		return this._queue.shift();
	}
	print () {
		let out = " ",
			length = this.size(),
			i = 0;
		while (i < length) {
			out += this._queue[i++] + " ";
		}
		console.log(out);	
	}
}

var q = new Queue([1,2,3,4]);
q.print();
q.in(5);
q.print();
console.log(q.out());
q.print();
```

## 栈

栈的特点在于先进后出，基本操作有：

1. 入栈
2. 出栈
3. 是否为空
4. 长度

```javascript
class Stack {
	constructor (arr) {
		this._stack = arr || [];
	}
	isEmpty () {
		return (this._stack.length == 0 );
	}
	size () {
		return this._stack.length;
	}
	in (val) {
		this._stack.push(val);
	}
	out () {
		return this._stack.pop();
	}
	print () {
		let out = " ",
			length = this.size(),
			i = 0;
		while (length--) {
			out += this._stack[length] + " ";
		}
		console.log(out);	
	}
}

var s = new Stack([1,2,3,4]);
s.print();
s.in(5);
s.print();
console.log(s.out());
s.print();
```