---
title: Mongoose 的增删改查（CRUD）
date: 2019-05-30 00:24:50
categories: 
    - [Web, Node.js, Mongoose]
tags:
---

### 连接 MongoDB
```bash
const mongoose = require('mongoose');

mongoose.connect('mongodb://localhost/myapp', {useNewUrlParser:true})
    .then(() => console.log('Connected to MongoDB...'))
    .catch(err => console.error('Connected failed', err));
```

### Schema
```bash
const courseSchema = new mongoose.Schema({
    name: String,
    author: String,
    tags: [String],
    date: {type: Date, default: Date.now},
    isPublished: Boolean
});
```
1. Schema 定义了 collection 中 document 的结构。
2. mongoDB 中 collection 相当于关系数据库的 table，而 document 则是其中的一条数据，相当于 table 中的 row。

### Model 和 Document
```bash
const Course = new mongoose.model('Course', courseSchema);
```
1. Model 是从 Schema 编译来的构造函数。 它们的实例就代表着可以从数据库保存和读取的 documents。 从数据库创建和读取 document 的所有操作都是通过 model 进行的。

### 保存 Document
```bash
async function createCourse() {
    const course = new Course({
        name: 'Node.js Course',
        author: 'Mosh',
        tags: ['node', 'backend'],
        isPublished: true
    })
    const result = await course.save();
    console.log(result);
}
createCourse();
```
1. 操作数据库属于是异步操作，使用异步编程。

### 查询 Document
```bash
async function getCourse() {
    const courses = await Course
        .find({author: 'Mosh', isPublished: true})
        .limit(10)
        .sort({name: 1})
        .select({name: 1, tags: 1});
    console.log(courses);
}
getCourse();
```
1. fing() 设置需要匹配的属性。
2. limit() 设置返回的数量。
3. sort() 按照指定属性排序，1表示升序，-1表示降序。
4. select() 设置需要返回属性。

#### 使用查询运算符
```bash
async function getCourse() {
    // eq (equal)
    // ne (not equal)
    // gt (greater than)
    // gte (greater than or equal to)
    // lt (less than)
    // lte (less than or equal to)
    // in
    // nin (not in)

    const courses = await Course
        // .find({author: 'Mosh', isPublished: true})
        // .find({ price: { $gte: 10, $lte: 20 } })
        .find({ price: { $in: [10, 15, 20] } })
        .limit(10)
        .sort({ name: 1 })
        .select({ name: 1, tags: 1 });
    console.log(courses);
}

getCourse();
```

#### 使用or和and方法查询
```bash
async function getCourse() {
    const courses = await Course
        .find()
        .or([ { author: 'Mosh' }, { isPublished: true } ])
        .and([ {author: 'Mosh', isPublished: true} ])
        .limit(10)
        .sort({name: 1})
        .select({name: 1, tags: 1});
    console.log(courses);
}
```

#### 使用正则表达式查询
```bash
async function getCourse() {
    const courses = await Course
        .find({ author: /^Mosh/ })
        .find({ author: /Hanmedani$/i })
        .find({ author: /.*Mosh.*/ })
        .limit(10)
        .sort({name: 1})
        .select({name: 1, tags: 1});
    console.log(courses);
}
```

#### 获取查询的条数，使用 count() 方法
```bash
async function getCourse() {
    const courses = await Course
        .find({author: 'Mosh', isPublished: true})
        .limit(10)
        .sort({name: 1})
        .count();
    console.log(courses);
}
```

#### 查询分页操作，使用 skin() 方法
```bash
async function getCourse() {
    const pageNumber = 2;
    const pageSize = 10;

    const courses = await Course
        .find({author: 'Mosh', isPublished: true})
        .skip((pageNumber - 1) * pageSize)
        .limit(pageSize)
        .sort({name: 1})
        .count();
    console.log(courses);
}
```
1. 注意 limit() 方法也要传入pageSize

### 更新 Document
```bash
//第一种方式
async function updateCourse(id) {
    const course = await Course.findById(id);
    if (!course) return;

    course.isPublished = true;
    course.author = 'Another Author';

    const result = await course.save();
    console.log(result);
}
//第二种方式
async function updateCourse(id) {
    // const result = await Course.update({ _id: id }, {
    //     $set: {
    //         author: 'Mosh',
    //         isPublished: false
    //     }
    // });
    const result = await Course.findByIdAndUpdate(id, {
        $set: {
            author: 'Jack',
            isPublished: true
        }
    }, { new : true });
    console.log(result);
}

```
1. Course.update() 返回的是操作结果，例如`{ n: 1, nModified: 0, ok: 1 }`。
2. 如果需要返回修改的对象，使用 Course.findByIdAndUpdate() 方法，第三个参数`{ new : true}`表示的是返回修改后的对象，如果设置为false则会返回修改前的对象。
3. More info: [Update Operator(更新操作符)](https://docs.mongodb.com/manual/reference/operator/update/)。

### 删除 Document
```bash
async function removeCourse(id) {
    const result = await Course.deleteOne({ _id: id });
    // const result = await Course.deleteMany({ _id: id });
    // const result = await Course.findByIdAndRemove({ _id: id });
    console.log(result);
}
```
1. 需要返回被删除的对象使用 Course.findByIdAndRemove() 方法，查找不到需要删除的对象会返回null。