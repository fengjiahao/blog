# JavaScript 中的数组常用方法

## 导航

- [forEach](##forEach)
- [fliter](##fliter)
- [map](##map)

## forEach()

`forEach()` 方法对数组的每个元素执行一次提供的函数。

> * `forEach()` 方法对数组的每个元素执行一次提供的函数。
> * 不像 `map()` 或者 `reduce()`，返回值为 `undefined`。
> * 不改变原数组

### 参数

`forEach(item, index, array)`

- item   当前元素
- index  当前元素的索引。
- array  操作的数组。

### 示例

```javascirpt
var array = ['a', 'b', 'c'];
array.forEach((i, k, j)=> {
  console.log(i, k, j);
});

// a 0 [ 'a', 'b', 'c' ]
// b 1 [ 'a', 'b', 'c' ]
// c 2 [ 'a', 'b', 'c' ]
```

## fliter()

`filter()` 方法创建一个新数组, 其包含通过所提供函数实现的测试的所有元素。

> * `filter()` 不会改变原数组，它返回过滤后的新数组。

### 参数

`filter(item, index, array)`

- item   当前元素
- index  当前元素的索引。
- array  操作的数组。

### 示例

```javascirpt
var words = ['spray', 'limit', 'elite', 'exuberant', 'destruction', 'present'];

const result = words.filter( word => {
    word.length > 6
});

console.log(result);

// [ 'exuberant', 'destruction', 'present' ]
```

## map()

`map()` 方法创建一个新数组，其结果是该数组中的每个元素都调用一个提供的函数后返回的结果。

> * `map()` 不修改调用它的原数组本身
> * 可以在 callback 执行时改变原数组
> * 方法执行的过程中，原数组中新增加的元素将不会被 callback 访问到

### 参数

`filter(item, index, array)`

- item   当前元素
- index  当前元素的索引。
- array  操作的数组。

### 示例

```javascirpt
var words = ['spray', 'limit', 'elite', 'exuberant', 'destruction', 'present'];

const result = words.filter( word => {
    word.length > 6
});

console.log(result);

// [ 
