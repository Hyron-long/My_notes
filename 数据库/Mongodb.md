# 创建文档

## 1.1 insertOne()

### 1.1.1 不指定 _id

```markdown
db.products.insertOne({
  "name": "xiaomi",
  "price": 799,
  "releaseDate": ISODate("2019-05-14"),
  "spec": { "ram": 4, "screen": 6.5, "cpu": 2.66 },
  "color": ["white", "black"],
  "storage": [64, 128, 256]
});
```

输出：

```json
{
  acknowledged: true,
  insertedId: ObjectId("65413081059fcf9ea192cda6")
}
```

### 1.1.2 指定 _id

例：

```js
db.products.insertOne({
  "_id": 2,
  "name": "huawei",
  "price": 899,
  "releaseDate": ISODate("2021-09-01"),
  "spec": { "ram": 16, "screen": 9.5, "cpu": 3.66 },
  "color": ["white", "black", "purple"],
  "storage": [128, 256, 512]
});
```

输出：

```json
{
  acknowledged: true,
  insertedId: 2
}
```

### 1.1.3 _id 冲突

例：

```js
db.products.insertOne({
  "_id": 2,
  "name": "sanxing",
  "price": 1299,
  "releaseDate": ISODate("2018-01-14"),
  "spec": { "ram": 12, "screen": 9.7, "cpu": 3.66 },
  "color": ["blue"],
  "storage": [16, 64, 128]
});
```

输出：

```
[MongoServerError]: E11000 duplicate key error collection: course.products index: _id_ dup key: { _id: 2 }
```

## 1.2 insertMany()

### 1.2.1 顺序插入

例：

```js
db.products.insertMany([
  { "_id": 1, "name": "xiaomi", "price": 799, "releaseDate": ISODate("2021-05-14"), "spec": { "ram": 4, "screen": 6.5, "cpu": 2.66 }, "color": ["white", "black"], "storage": [64, 128, 256] },
  { "_id": 2, "name": "huawei", "price": 899, "releaseDate": ISODate("2022-09-01"), "spec": { "ram": 16, "screen": 9.5, "cpu": 3.66 }, "color": ["white", "black", "purple"], "storage": [128, 256, 512] },
  { "_id": 3, "name": "sanxing", "price": 899, "releaseDate": ISODate("2023-01-14"), "spec": { "ram": 12, "screen": 9.7, "cpu": 3.66 }, "color": ["blue"], "storage": [16, 64, 128] }
]);
```

输出：

```
[MongoServerError]: E11000 duplicate key error collection: course.products index: _id_ dup key: { _id: 1 }
```

说明：使用了顺序插入，在 _id 有冲突的情况下，所有文档均未插入。

### 1.2.2 无序插入

例：

```js
db.products.insertMany([
  { "_id": 1, "name": "xiaomi", "price": 799, "releaseDate": ISODate("2021-05-14"), "spec": { "ram": 4, "screen": 6.5, "cpu": 2.66 }, "color": ["white", "black"], "storage": [64, 128, 256] },
  { "_id": 2, "name": "huawei", "price": 899, "releaseDate": ISODate("2022-09-01"), "spec": { "ram": 16, "screen": 9.5, "cpu": 3.66 }, "color": ["white", "black", "purple"], "storage": [128, 256, 512] },
  { "_id": 3, "name": "sanxing", "price": 899, "releaseDate": ISODate("2023-01-14"), "spec": { "ram": 12, "screen": 9.7, "cpu": 3.66 }, "color": ["blue"], "storage": [16, 64, 128] },
  { "_id": 4, "name": "SmartPad", "price": 699, "releaseDate": ISODate("2020-05-14"), "spec": { "ram": 8, "screen": 9.7, "cpu": 1.66 }, "color": ["white", "orange", "gold", "gray"], "storage": [128, 256, 1024] },
  { "_id": 5, "name": "SmartPhone", "price": 599, "releaseDate": ISODate("2022-09-14"), "spec": { "ram": 4, "screen": 9.7, "cpu": 1.66 }, "color": ["white", "orange", "gold", "gray"], "storage": [128, 256] }
], { ordered: false });
```

输出：

```
[MongoBulkWriteError]: E11000 duplicate key error collection: course.products index: _id_ dup key: { _id: 1 }
```

但是，_id 为 3 的文档已经插入。

# 查找文档

## 2.1 findOne()

例：查找 products 集合中的第一个文档

```js
db.products.findOne();
```

输出：

```json
{
  _id: ObjectId("65413081059fcf9ea192cda6"),
  name: 'xiaomi',
  price: 799,
  releaseDate: 2019-05-14T00:00:00.000Z,
  spec: { ram: 4, screen: 6.5, cpu: 2.66 },
  color: [ 'white', 'black' ],
  storage: [ 64, 128, 256 ]
}
```

例：查找 _id 为 3 的文档

```js
db.products.findOne({ _id: 3 });
```

结果：

```json
{
  _id: 3,
  name: 'sanxing',
  price: 899,
  releaseDate: 2023-01-14T00:00:00.000Z,
  spec: { ram: 12, screen: 9.7, cpu: 3.66 },
  color: [ 'blue' ],
  storage: [ 16, 64, 128 ]
}
```

例：查找 _id 等于 2 的文档，只返回 _id 和 name

```js
db.products.findOne({ _id: 2 }, { name: 1 });
```

输出：

```json
{
  _id: 2,
  name: 'huawei'
}
```

## 2.2 find()

例：查找 products 的所有文档

```js
db.products.find();
```

返回 products 中所有文档（默认 20 个文档一页，通过 it 来翻页）。

例：查找 name 为 huawei 的文档

```js
db.products.find({ name: 'huawei' });
```

结果：

```json
{
  _id: 2,
  name: 'huawei',
  price: 899,
  releaseDate: 2021-09-01T00:00:00.000Z,
  spec: { ram: 16, screen: 9.5, cpu: 3.66 },
  color: [ 'white', 'black', 'purple' ],
  storage: [ 128, 256, 512 ]
}
```

例：查找 name 为 xiaomi 的文档，返回 _id，price 和 spec 中的 ram

```js
db.products.find({ name: 'xiaomi' }, { _id: 1, price: 1, 'spec.ram': 1 });
```

输出：

```json
{
  _id: ObjectId("65413081059fcf9ea192cda6"),
  price: 799,
  spec: { ram: 4 }
}
```

# 3 查询

## 3.1 比较运算符

### 3.1.1 $eq

例：查找价格为 899 的手机信息

```js
db.products.find({ price: { $eq: 899 } }, { _id: 1, name: 1 });
```

输出：

```json
{ _id: 2, name: 'huawei' }
{ _id: 3, name: 'sanxing' }
```

例：$eq 的等价写法

```js
db.products.find({ price: 899 }, { _id: 1, name: 1 });
```

例：查找嵌套文档中 ram 为 8 的文档

```js
db.products.find({ 'spec.ram': { $eq: 8 } }, { _id: 1 });
```

输出：

```json
{ _id: 4 }
```

### 3.1.2 $gt

例：

```js
db.products.find({ 'spec.ram': { $gt: 4 } }, { _id: 1, name: 1, 'spec.ram': 1 });
```

输出：

```json
{ _id: 2, name: 'huawei', spec: { ram: 16 } }
{ _id: 1, name: 'sanxing', spec: { ram: 12 } }
{ _id: 3, name: 'sanxing', spec: { ram: 12 } }
{ _id: 4, name: 'SmartPad', spec: { ram: 8 } }
```

### 3.1.3 $gte

例：查找 ram 大于等于 4 的文档

```js
db.products.find({ 'spec.ram': { $gte: 4 } }, { _id: 1, name: 1, 'spec.ram': 1 });
```

输出： 

```json
{ _id: ObjectId("65413081059fcf9ea192cda6"), name: 'xiaomi', spec: { ram: 4 } }
{ _id: 2, name: 'huawei', spec: { ram: 16 } }
{ _id: 1, name: 'sanxing', spec: { ram: 12 } }
{ _id: 3, name: 'sanxing', spec: { ram: 12 } }
{ _id: 4, name: 'SmartPad', spec: { ram: 8 } }
{ _id: 5, name: 'SmartPhone', spec: { ram: 4 } }
```

### 3.1.4 $lt

例：查找 ram 小于 8 的文档

```js
db.products.find({ 'spec.ram': { $lt: 8 } }, { _id: 1, name: 1, 'spec.ram': 1 });
```

输出：

```json
{ _id: ObjectId("65413081059fcf9ea192cda6"), name: 'xiaomi', spec: { ram: 4 } }
{ _id: 5, name: 'SmartPhone', spec: { ram: 4 } }
```

例：查找 ram 大于 4，小于 16 的文档

```js
db.products.find({ 'spec.ram': { $lt: 16, $gt: 4 } }, { _id: 1, name: 1, 'spec.ram': 1 });
```

输出：

```json
{ _id: 1, name: 'sanxing', spec: { ram: 12 } }
{ _id: 3, name: 'sanxing', spec: { ram: 12 } }
{ _id: 4, name: 'SmartPad', spec: { ram: 8 } }
```

例：查找 ram 大于 4，小于 16，且 name 为 sanxing 的文档

```js
db.products.find({ 'spec.ram': { $lt: 16, $gt: 4 }, 'name': { $eq: 'sanxing' } }, { _id: 1, name: 1, 'spec.ram': 1 });
```

输出：

```json
{ _id: 1, name: 'sanxing', spec: { ram: 12 } }
{ _id: 3, name: 'sanxing', spec: { ram: 12 } }
```

### 3.1.5 $lte

例：查找日期早于 2019-5-1 的文档

```js
db.products.find({ 'releaseDate': { $lte: new ISODate('2019-05-01') } }, { _id: 1, name: 1, releaseDate: 1 });
```

输出：

```json
{ _id: 1, name: 'sanxing', releaseDate: 2018-01-14T00:00:00.000Z }
```

### 3.1.6 $ne

例：查找 ram <> 12 的文档信息

```js
db.products.find({ 'spec.ram': { $ne: 12 } }, { _id: 1, name: 1, 'spec.ram': 1 });
```

输出：

```json
{ _id: ObjectId("65413081059fcf9ea192cda6"), name: 'xiaomi', spec: { ram: 4 } }
{ _id: 2, name: 'huawei', spec: { ram: 16 } }
{ _id: 4, name: 'SmartPad', spec: { ram: 8 } }
{ _id: 5, name: 'SmartPhone', spec: { ram: 4 } }
```

### 3.1.7 $in

例：查找 ram 是 4 或 8 的文档信息

```js
db.products.find({ 'spec.ram': { $in: [4, 8] } }, { _id: 1, name: 1, 'spec.ram': 1 });
```

结果：

```json
{ _id: ObjectId("65413081059fcf9ea192cda6"), name: 'xiaomi', spec: { ram: 4 } }
{ _id: 4, name: 'SmartPad', spec: { ram: 8 } }
{ _id: 5, name: 'SmartPhone', spec: { ram: 4 } }
```

### 3.1.8 $nin

例：查找 ram 不是 4 和 8 的文档信息

```js
db.products.find({ 'spec.ram': { $nin: [4, 8] } }, { _id: 1, name: 1, 'spec.ram': 1 });
```

结果：

```json
{ _id: 2, name: 'huawei', spec: { ram: 16 } }
{ _id: 1, name: 'sanxing', spec: { ram: 12 } }
{ _id: 3, name: 'sanxing', spec: { ram: 12 } }
```

## 3.2 逻辑运算符

### 3.2.1 $and 运算符

例：查找价格小于 800，且颜色为 black 或者 blue 的文档

```js
db.products.find({ $and: [{ price: { $lt: 800 } }, { color: { $in: ['white', 'blue'] } }] }, { _id: 1, name: 1, price: 1 });
```

输出：

```json
{ _id: ObjectId("65413081059fcf9ea192cda6"), name: 'xiaomi', price: 799 }
{ _id: 4, name: 'SmartPad', price: 699 }
{ _id: 5, name: 'SmartPhone', price: 599 }
```

### 3.2.2 $or 运算符

例：查找价格小于 800，或颜色为 black 的文档

```js
db.products.find({ $or: [{ price: { $lt: 800 } }, { color: { $eq: 'black' } }] }, { _id: 1, name: 1, price: 1 });
```

输出：

```json
{ _id: ObjectId("65413081059fcf9ea192cda6"), name: 'xiaomi', price: 799 }
{ _id: 2, name: 'huawei', price: 899 }
{ _id: 4, name: 'SmartPad', price: 699 }
```

### 3.2.3 $not 运算符

例：输出所有颜色不是 black 的文档

```js
db.products.find({ color: { $not: { $eq: 'black' } } }, { _id: 1, name: 1, price: 1 });
```

输出：

```json
{ _id: 1, name: 'sanxing', price: 1299 }
{ _id: 3, name: 'sanxing', price: 899 }
{ _id: 4, name: 'SmartPad', price: 699 }
{ _id: 5, name: 'SmartPhone', price: 599 }
```

### 3.2.4 $nor 运算符

例：查找价格不是 799 且颜色不是 blue 的文档

```js
db.products.find({ $nor: [{ price: 799 }, { color: 'blue' }] }, { _id: 1, name: 1, price: 1 });
```

输出：

```json
{ _id: 2, name: 'huawei', price: 899 }
{ _id: 4, name: 'SmartPad', price: 699 }
{ _id: 5, name: 'SmartPhone', price: 599 }
```

## 3.3 元素运算符

### 3.3.1 $exists 运算符

数据准备：

```js
db.products.insertMany([
  { "_id": 6, "name": "kupai", "spec": { "ram": 64, "screen": 9.7, "cpu": 3.66 }, "color": [ "black" ], "storage": [ 1024 ] },
  { "_id": 7, "name": "oppo", "price": null, "spec": { "ram": 64, "screen": 6.7, "cpu": 3.66 }, "color": [ "black", "white" ], "storage": [ 128 ] }
]);
```

例：查找包含 price 字段的文档

```js
db.products.find({ price: { $exists: true } }, { _id: 1, name: 1 });
```

输出：

```json
{ _id: ObjectId("65413081059fcf9ea192cda6"), name: 'xiaomi' }
{ _id: 2, name: 'huawei' }
{ _id: 1, name: 'sanxing' }
{ _id: 3, name: 'sanxing' }
{ _id: 4, name: 'SmartPad' }
{ _id: 5, name: 'SmartPhone' }
{ _id: 7, name: 'oppo' }
```

例：查找包含 price 字段且价格大于 700 的文档

```js
db.products.find({ price: { $exists: true, $gt: 700 } }, { _id: 1, name: 1 });
```

输出：

```json
{ _id: ObjectId("65413081059fcf9ea192cda6"), name: 'xiaomi' }
{ _id: 2, name: 'huawei' }
{ _id: 1, name: 'sanxing' }
{ _id: 3, name: 'sanxing' }
```

### 3.3.3 $type 运算符

数据准备：

```js
db.products.insertMany([
  { "_id": 8, "name": "iPad", "price": NumberLong(899), "releaseDate": ISODate("2022-09-14T00:00:00Z"), "spec": { "ram": 4, "screen": 9.7, "cpu": 1.66 }, "color": [ "white", "orange", "gold", "gray" ], "storage": [ 128, 256 ] },
  { "_id": 9, "name": "iPhone", "price": "799", "releaseDate": ISODate("2011-05-14T00:00:00Z"), "spec": { "ram": 4, "screen": 6.5, "cpu": 2.66 }, "color": [ "white", "black" ], "storage": [ 64, 128, 256 ] }
]);
```

例：查找 price 字段为 string 类型的文档

```js
db.products.find({ price: { $type: 'string' } }, { name: 1, price: 1 });
```

输出：

```json
{
  _id: 9,
  name: 'iPhone',
  price: '799'
}
```

例：查找 price 字段为 string 或 number 类型（包括 int，long 或者 double）的文档

```js
db.products.find({ price: { $type: ['number', 'string'] } }, { name: 1, price: 1 });
```

输出：

```json
{ _id: ObjectId("65413081059fcf9ea192cda6"), name: 'xiaomi' }
{ _id: 2, name: 'huawei' }
{ _id: 1, name: 'sanxing' }
{ _id: 3, name: 'sanxing' }
{ _id: 4, name: 'SmartPad' }
{ _id: 5, name: 'SmartPhone' }
{ _id: 8, name: 'ipad' }
{ _id: 9, name: 'iphone' }
```

## 3.4 数组运算符

### 3.4.1 $size 运算符

例：查找出 storage 有 2 种的文档

```js
db.products.find({ storage: { $size: 2 } }, { _id: 1, name: 1, storage: 1 });
```

输出：

```json
{ _id: 5, name: 'SmartPhone', storage: [ 128, 256 ] }
{ _id: 8, name: 'iPad', storage: [ 128, 256 ] }
```

### 3.4.2 $all 运算符

例：查找出 storage 中包含 256 和 512 的文档

```js
db.products.find({ storage: { $all: [256, 512] } }, { _id: 1, name: 1, storage: 1 });
```

输出：

```json
{ _id: 2, name: 'huawei', storage: [ 128, 256, 512 ] }
```

等价于：

```js
db.products.find({ $and: [{ storage: 256 }, { storage: 512 }] }, { _id: 1, name: 1, storage: 1 });
```

### 3.4.3 $elemMatch 运算符

例：

```js
db.products.find({ storage: { $elemMatch: { $lt: 64 } } }, { _id: 1, name: 1, storage: 1 });
```

输出：

```json
{ _id: 1, name: 'sanxing', storage: [ 16, 64, 128 ] }
{ _id: 3, name: 'sanxing', storage: [ 16, 64, 128 ] }
```

## 3.5 结果排序

### 3.5.1 单一字段排序

例：查找包含 price 的文档，按照 price 升序排列

```js
db.products.find({ price: { $exists: true } }, { _id: 1, name: 1, price: 1 }).sort({ price: 1 });
```

输出：

```json
{ _id: 7, name: 'oppo', price: null }
{ _id: 5, name: 'SmartPhone', price: 599 }
{ _id: 4, name: 'SmartPad', price: 699 }
{ _id: ObjectId("65413081059fcf9ea192cda6"), name: 'xiaomi', price: 799 }
{ _id: 2, name: 'huawei', price: 899 }
{ _id: 3, name: 'sanxing', price: 899 }
{ _id: 8, name: 'iPad', price: 899 }
{ _id: 1, name: 'sanxing', price: 1299 }
{ _id: 9, name: 'iPhone', price: '799' }
```

例：查找包含 price，且 price > 700 的文档，按照 name 降序排列

```js
db.products.find({ price: { $exists: true, $gt: 700 } }, { _id: 1, name: 1, price: 1 }).sort({ name: -1 });
```

输出：

```json
{ _id: ObjectId("65413081059fcf9ea192cda6"), name: 'xiaomi', price: 799 }
{ _id: 1, name: 'sanxing', price: 1299 }
{ _id: 3, name: 'sanxing', price: 899 }
{ _id: 8, name: 'iPad', price: 899 }
{ _id: 2, name: 'huawei', price: 899 }
```

### 3.5.2 多字段排序

例：查找包含 price，且 price > 700 的文档，按照 price 升序，name 降序排列

```js
db.products.find({ price: { $exists: true, $gt: 700 } }, { _id: 1, name: 1, price: 1 }).sort({ price: 1, name: -1 });
```

输出：

```json
{ _id: ObjectId("65413081059fcf9ea192cda6"), name: 'xiaomi', price: 799 }
{ _id: 3, name: 'sanxing', price: 899 }
{ _id: 8, name: 'iPad', price: 899 }
{ _id: 2, name: 'huawei', price: 899 }
{ _id: 1, name: 'sanxing', price: 1299 }
```

## 3.6 limit()

例：查找最便宜的手机

```js
db.products.find({ price: { $exists: true, $gte: 0 } }, { _id: 1, name: 1, price: 1 }).sort({ price: 1 }).limit(1);
```

输出：

```json
{ _id: 5, name: 'SmartPhone', price: 599 }
```

例：分页查找所有产品，每页 2 个产品，查看第 3 页

```js
db.products.find({}, { _id: 1, name: 1, price: 1 }).sort({ price: 1 }).skip(4).limit(2);
```

输出：

```json
{ _id: ObjectId("65413081059fcf9ea192cda6"), name: 'xiaomi', price: 799 }
{ _id: 2, name: 'huawei', price: 899 }
```

# 4 更新文档

## 4.1 updateOne()

例：将 _id 为 2 的产品价格改为 888

```js
db.products.updateOne({ _id: 2 }, { $set: { price: 888 } });
```

输出：

```json
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 1,
  modifiedCount: 1,
  upsertedCount: 0
}
```

例：将 _id 为 5 的产品 ram 改为 8，storage 改为 [128,256,512]

```js
db.products.updateOne({ _id: 5 }, { $set: { 'spec.ram': 4, storage: [128, 256, 512] } });
```

输出：

```json
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 1,
  modifiedCount: 1,
  upsertedCount: 0
}
```

## 4.2 updateMany()

例：将所有价格为 899 的产品，价格改为 869

```js
db.products.updateMany({ price: 899 }, { $set: { price: 869 } });
```

输出：

```json
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 2,
  modifiedCount: 2,
  upsertedCount: 0
}
```

## 4.3 $inc 操作符

例：将产品 _id 为 2 的产品，价格增加 50，ram 减少 1

```js
db.products.updateMany({ _id: 2 }, { $inc: { price: 50, 'spec.ram': -1 } });
```

输出：

```json
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 1,
  modifiedCount: 1,
  upsertedCount: 0
}
```

## 4.4 $min/$max 操作符

例：将 _id 为 4 的产品价格降为 799

```js
db.products.updateOne({ _id: 4 }, { $min: { price: 799 } });
```

输出：

```json
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 1,
  modifiedCount: 0,
  upsertedCount: 0
}
```

例：将 _id 为 4 的产品价格降为 569

```js
db.products.updateOne({ _id: 4 }, { $min: { price: 569 } });
```

输出：

```json
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 1,
  modifiedCount: 1,
  upsertedCount: 0
}
```

例：将 _id 为 4 的产品价格涨为 599

```js
db.products.updateOne({ _id: 4 }, { $max: { price: 599 } });
```

输出：

```json
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 1,
  modifiedCount: 1,
  upsertedCount: 0
}
```

## 4.5 $mul 操作符

例：将 _id 为 4 的产品价格涨价 50%

```js
db.products.updateOne({ _id: 4 }, { $mul: { price: 1.5 } });
```

输出：

```json
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 1,
  modifiedCount: 1,
  upsertedCount: 0
}
```

例：将 _id 为 4 的 storage 的第 2 项上涨 1 倍

```js
db.products.updateOne({ _id: 4 }, { $mul: { 'storage.1': 2 } });
```

输出：

```json
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 1,
  modifiedCount: 1,
  upsertedCount: 0
}
```

## 4.6 $unset 操作符

例：删除 _id 为 4 的产品中，color 和 spec 中的 screen 字段

```js
db.products.updateOne({ _id: 4 }, { $unset: { color: "", 'spec.screen': "" } });
```

输出：

```json
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 1,
  modifiedCount: 1,
  upsertedCount: 0
}
```

## 4.6 $rename 操作符

例：将 _id 为 6 的产品的 color 字段改名为 colour

```js
db.products.updateMany({ _id: 6 }, { $rename: { color: 'colour' } });
```

输出：

```json
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 1,
  modifiedCount: 1,
  upsertedCount: 0
}
```

## 4.7 UPSERT

例：将 _id 为 10 的产品价格设为 799

```js
db.products.updateOne({ _id: 10 }, { $set: { price: 799 } }, { upsert: true });
```

输出：

```json
{
  acknowledged: true,
  insertedId: 10,
  matchedCount: 0,
  modifiedCount: 0,
  upsertedCount: 1
}
```

# 5 删除文档

## 5.1 deleteOne()

例：删除 _id 为 10 的产品

```js
db.products.deleteOne({ _id: 10 });
```

输出：

```json
{
  acknowledged: true,
  deletedCount: 1
}
```

## 5.2 deleteMany()

例：删除价格高于 800 的所有文档

```js
db.products.deleteMany({ price: { $gt: 800 } });
```

例：删除所有产品

```js
db.products.deleteMany();
```

# 6 聚合操作

## 6.1 准备数据

```js
// 创建 users 集合并插入示例数据
db.users.insertMany([
  {
    user_id: "A123",
    name: "张三",
    email: "zhang.san@example.com",
    age: 28,
    city: "北京",
    vip_level: "gold",
    register_date: ISODate("2023-01-15")
  },
  {
    user_id: "B456",
    name: "李四",
    email: "li.si@example.com",
    age: 35,
    city: "上海",
    vip_level: "platinum",
    register_date: ISODate("2022-08-20")
  },
  {
    user_id: "C789",
    name: "王五",
    email: "wang.wu@example.com",
    age: 22,
    city: "深圳",
    vip_level: "silver",
    register_date: ISODate("2024-01-10")
  },
  {
    user_id: "D001",
    name: "赵六",
    email: "zhao.liu@example.com",
    age: 42,
    city: "广州",
    vip_level: "platinum",
    register_date: ISODate("2022-11-05")
  }
]);

// 创建 orders 集合并插入示例数据
db.orders.insertMany([
  { cust_id: "A123", amount: 100, status: "shipped", items: ["apple", "banana"] },
  { cust_id: "B456", amount: 250, status: "delivered", items: ["pear"] },
  { cust_id: "A123", amount: 80, status: "pending", items: ["apple", "orange"] }
]);
```

## 6.2 筛选与分组

例：计算每个客户已发货订单的总金额。

```js
db.orders.aggregate([
  { $match: { status: "shipped" } },
  { $group: { _id: "$cust_id", total: { $sum: "$amount" } } },
  { $sort: { total: -1 } }
]);
```

输出：

```json
{ _id: "A123", total: 100 }
```

## 6.3 多表关联 ($lookup)

例：订单集合关联用户集合

```js
db.orders.aggregate([
  { $lookup: {
    from: "users",
    localField: "cust_id",
    foreignField: "user_id",
    as: "user_info"
  }},
  { $unwind: "$user_info" },
  { $project: { "user_info.name": 1, amount: 1 } }
]);
```

## 6.4 unwind 与数组处理

例：统计每个商品被购买的次数（拆解 items 数组）

```js
db.orders.aggregate([
  { $unwind: "$items" },
  { $group: { _id: "$items", count: { $sum: 1 } } }
]);
```

输出：

```json
{ _id: "apple", count: 2 }, { _id: "banana", count: 1 }, ...
```
```

这个 Markdown 文档可以直接复制到支持 Markdown 的编辑器（如 Typora、VS Code、Obsidian 等）中查看，格式清晰，内容与原文档完全一致。