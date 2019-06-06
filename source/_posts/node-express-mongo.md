---
title: 'node-express-mongo'
date: 2019-06-06 11:36:43
categories:
    - ['Demo']
tags:
---
## 初始化项目
* 创建项目目录，并通过npm初始化package.json文件。
```bash
mkdir express-mongo-demo
cd express-mongo-demo
npm init --yes
```

## 创建app.js
* 安装express、mongoose
```
npm i express
npm i mongoose
```
* 在项目根目录下创建app.js
<font size=2>*app.js*</font>
```bash
const express = require('express');
const mongoose = require('mongoose');

const app = express();
app.use(express.json());
app.use(express.urlencoded({extended: true}));
mongoose.set('useFindAndModify', false);//让部分更新和删除方法不提示弃用警告

mongoose.connect('mongodb://localhost/myapp', {useNewUrlParser: true})
    .then(() => console.log('Connected to MongoDB...'))
    .catch(err => console.error('Could not connect to MongoDB...', err));

const port = process.env.POST || 3000;
app.listen(port, () => console.log(`Listening on port ${port}`));
```

## 创建model
* 创建models文件夹，该文件夹用于管理model文件。
* 在该目录下创建customer.js文件。
<font size=2>*models/customer.js*</font>
```bash
const mongoose = require('mongoose');
const Joi = require('@hapi/joi');

const Customer = mongoose.model('Customer', new mongoose.Schema({
    name: {
        type: String,
        required: true,
        minlength: 5,
        maxlength: 50
    },
    phone: {
        type: String,
        trim: true,
    },
    tags: {
        type: Array,
        validate: function(v) {
            return v && v.length >= 1;
        },
        message: 'tags is illegal.'
    },
    des: {
        type: String,
        required: function() { return this.isPublish; }
    },
    isPublish: {
        type: Boolean,
        default: true
    }
}));

function validateCustomer(customer) {
    const schema = {
        name: Joi.string().min(5).max(50).required(),
        phone: Joi.string(),
        tags: Joi.array().min(1),
        des: Joi.string(),
        isPublish: Joi.boolean()
    }
    return Joi.validate(customer, schema);
}

module.exports = {
    Customer: Customer,
    validate: validateCustomer
}
```
* 用`npm i @hapi/joi`命令安装Joi，用于数据验证，将错误信息发送给客户端。点击[API](https://github.com/hapijs/joi/blob/v15.0.3/API.md#booleantruthyvalue)查看。
* Schema 定义了 collection 中 document 的结构。Model 则是通过 Schema 创建。

## 创建routes
* 创建routes文件夹，该文件夹用于管理router文件。
* 在该目录下创建customer.js文件。
<font size=2>*routes/customer.js*</font>
```bash
const express = require('express');
const {Customer, validate} = require('../models/customer');

const router = express.Router();

router.get('/', async (req, res) => {
    const customers = await Customer.find().sort('name');
    res.send(customers);
});

router.get('/:id', async (req, res) => {
    try {
        const customer = await Customer.findById(req.params.id);
        if (!customer) return res.status(404).send(`${req.params.id} 不存在。`);
        res.send(customer);
    } catch (error) {
        res.send(error.message)
    }
});

router.post('/', async (req, res) => {
    const {error} = validate(req.body);
    if (error) return res.status(400).send(error.details[0].message);
    let customer = new Customer({
        name: req.body.name,
        phone: req.body.phone,
        tags: req.body.tags,
        des: req.body.des,
        isPublish: req.body.isPublish
    });
    try {
        customer = await customer.save();
        res.send(customer);
    } catch (error) {
        res.send(error.message);
    }
});

router.put('/:id', async (req, res) => {
    const {error} = validate(req.body);
    if (error) return res.status(400).send(error.details[0].message);
    try {
        const customer = await Customer.findByIdAndUpdate(req.params.id, {
            $set: {
                name: req.body.name,
                phone: req.body.phone,
                tags: req.body.tags,
                des: req.body.des,
                isPublish: req.body.isPublish
            }
        }, { new: true });
        if (!customer) return res.status(400).send(`${req.params.id} 不存在。`);
        res.send(customer);
    } catch (error) {
        res.send(error.message);
    }
});

router.delete('/:id', async (req, res) => {
    const customer = await Customer.findByIdAndRemove(req.params.id);
    if (!customer) return res.status(404).send(`${req.params.id} 不存在。`);
    res.send(customer);
});

module.exports = router;
```
* 操作数据库的回调方法要用async、await。
* 定义 RESTful Apis，get查询，post创建，put更新，delete删除。

## 注册路由
* 返回修改app.js。
<font size=2>*app.js*</font>
```bash
const express = require('express');
const mongoose = require('mongoose');
const customer = require('./routes/customer');

const app = express();
app.use(express.json());
app.use(express.urlencoded({extended: true}));
mongoose.set('useFindAndModify', false);//让部分更新和删除方法不提示弃用警告
app.use('/api/customer', customer);

mongoose.connect('mongodb://localhost/myapp', {useNewUrlParser: true})
    .then(() => console.log('Connected to MongoDB...'))
    .catch(err => console.error('Could not connect to MongoDB...', err));

const port = process.env.POST || 3000;
app.listen(port, () => console.log(`Listening on port ${port}`));
```
