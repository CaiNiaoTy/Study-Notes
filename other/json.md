# JSON

## 一 简介

- **JSON**:JavaScript Object Notation（JavaScript 对象表示法），用来存储和交换文本信息的语法。它独立于语言和平台！

  格式如下：
  ```
    {
      "stitles":[
        {"name":"zhangsan","position":"manager"}.
        {"name":"lisi","position":"work"}
      ]
    }
  ```
- **语法格式**：
  
  * 数据在名称/值 中
  * 数据由逗号分隔
  * 大括号保存对象
  * 中括号保存数组
  
  - 说明：JSON的值可以是数字、字符串、true/false、数组、对象、null。
  
- **JSON对象：**
  
  * JSON对象使用{}中定义其键值对，其中键必须是字符串，值可以是JSON数据类型就能够，键和值之间用 : 进行分隔，键值对之间用 , 进行分隔。其中键值对中的值可以是对象类型
  * 调用对象的值使用 . 来进行调用，和 Java 中对象调用无差别。

- **JSON数组：**
  
  * 数组中的值必须是JSON数据类型。
  
- **JSON.parse()**：
  
  * 格式：JSON(text[,reviver])
    
    text:必须，一个有效的JSON字符串
    reviver：可选，一个转换结果的函数，将为对象的每个成员调用次函数。
    
  * 用于和服务器端交换数据，用此种方式可以将接受到的JSON格式数据转换为JavaScript对象。
