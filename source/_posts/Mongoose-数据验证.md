---
title: Mongoose 数据验证
date: 2019-06-05 00:09:18
categories:
    - [Web, Node.js, Mongoose]
tags:
---

## Built-in Validators（内置验证器）
```bash
const mongoose = require('mongoose');
//连接数据库
mongoose.connect('mongodb://localhost/playground', {useNewUrlParser:true})
    .then(() => console.log('Connected to MongoDB...'))
    .catch(err => console.error('Could not connect to MongoDB...', err));
    
const courseSchema = new mongoose.Schema({
    name: {
        type: String, 
        required: true, // true设置为必填，也可以设置函数
        minlength: 5, // 最小长度
        maxlength: 255, // 最大长度
        // match: /patttern/ // 正则表达式
    }, 
    category: {
        type: String,
        required: true,
        enum: ['web', 'mobile', 'network'] // 枚举，该属性的值必须是其中之一
    },
    author: String,
    tags: [String],
    date: {type: Date, default: Date.now},
    isPublished: Boolean,
    price: {
        type: Number,
        required: function() { return this.isPublished; }, // 这里不可以用箭头函数，箭头函数无法获取this
        min: 20, // 最小为20
        max: 100 // 最大100
    }
});

const Course = new mongoose.model('Course', courseSchema);

async function createCourse() {
    const course = new Course({
        name: 'Angular Course',
        category: 'web',
        author: 'Mosh',
        tags: ['angular', 'frontend'],
        isPublished: true,
        price: 25,
    });
    // 用trycatch捕获错误
    try {
        const result = await course.save();
        console.log(result);
    } catch (error) {
        console.log(error.message);
    }
}

createCourse();
```

## Custom Validators（自定义验证器）
```bash
const courseSchema = new mongoose.Schema({
    name: String,
    tags: {
        type: Array,
        validate: {   // 自定义验证器
            validator: function(v) {
                return v && v.length >= 1;
            },
            message: 'A course should have at least one tag.'
        }
    },
    date: {type: Date, default: Date.now},
    isPublished: Boolean,
    price: Number
});
```

## Async Validators（异步验证器）
```bash
const courseSchema = new mongoose.Schema({
    name: String,
    tags: {
        type: Array,
        validate: {   // 异步验证器
            isAsync: true,
            validator: function(v, callback) {
                setTimeout(() => {
                    const result = v && v.length >= 1;
                    callback(result);
                }, 4000);
            },
            message: 'A course should have at least one tag.'
        }
    },
    date: {type: Date, default: Date.now},
    isPublished: Boolean,
    price: Number
});
```

## SchemaType Options
```bash
const courseSchema = new mongoose.Schema({
    category: {
        type: String,
        lowercase: true,  // 小写字母
        // uppercase: true, // 大写字母
        trim: true, 
        required: true,
        enum: ['web', 'mobile', 'network'] // 枚举，该属性的值必须是其中之一
    },
    price: {
        type: Number,
        required: function() { return this.isPublished; }, // 这里不可以用箭头函数，箭头函数无法获取this
        min: 20, // 最小为20
        max: 100, // 最大100
        set: v => Math.round(v), // 自定义set
        get: v => Math.round(v) // 自定义get
    }
});
```