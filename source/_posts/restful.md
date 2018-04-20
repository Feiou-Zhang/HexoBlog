---
title: Rest
date: 2017-02-23 13:33:23
tags: [Rest]
categories: [学习笔记]
---

Rest 首先应该是一种资源，所以，它应该是以名词的方式出现和表示。所以url里面也应该只出现名词
好像好的命名习惯是用 复数名字

好的例子是 xxx.com/api/v1/path/to/resources

动作的话 是用 http的 PUSH GET PUT DELETE

Rest 有一个很重要的特性就是 stateless，意思就是任何人看这个资源，看到的东西应该是一样的

---
tinyURL rest api design


* insert
    * POST /urls
    * data: {
                longUrl: "xxxxxxx"
            }
所以就是要在所有的url资源里面添加一个

* lookup
    * GET 