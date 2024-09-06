---
layout: ts数据转换
title: class-transformer
date: 2024-04-08 11:27:34
tags:
---

1. # **什么是class-transformer**

> 文档：https://github.com/typestack/class-transformer

class-transformer是一个为Typescript设计的轻量级库，用于实现JS普通对象和类对象之间的**转换**。它基于**装饰器**模式，使得开发者能够定义如何将对象属性从一个形式映射到另一个形式，以及在转换过程中如何处理复杂的类型和嵌套的对象结构。有助于维护类型安全并提高开发效率。

1. # **为什么需要class-transformer**

举个🌰：

假设我们定义了一个User类

```JavaScript
export class User {
  firstName: string
  lastName: string
  constructor(firstName: string, lastName: string) {
    this.firstName = firstName
    this.lastName = lastName
  }
  getName() {
    return this.firstName + ' ' + this.lastName
  }
}
```

我们通过接口获取到一个user对象

```JavaScript
{
  firstName: 'John',
  lastName: 'Cage',
}
fetch("user.json")
  .then(response => response.json())
  .then((user: User) => {
    console.log(user.getName()) // 报错，不能使用❌
  })
```

注意：可以使用User定义类型获取到的user对象，且类型提示也可以使用；但是user只是普通对象，并不是User类的实例，所以不能使用User类中的方法

解决方案：

1. 为了使用User类中的方法，我们可以自己这样处理：

```JavaScript
fetch("user.json")
  .then(response => response.json())
  .then((user: User) => {
    // 使用得到的数据直接构建User类实例
    const userInstance = new User(user.firstName, user.lastName);
    // 以使用 User 类中定义的方法
    console.log(userInstance.getName());
  })
```

1. 使用已有工具 class-transformer 进行处理

```JavaScript
import { plainToInstance } from 'class-transformer'
  
fetch("user.json")
  .then(response => response.json())
  .then((user: User) => {
    // 使自动将普通对象转换为User类实例
    const userInstance = plainToInstance(User, user)
    console.log(userInstance.getName());
  })
```

userInstance打印结果：

![img](https://iq.feishu.cn/space/api/box/stream/download/asynccode/?code=ODkwNmFjYzUyN2ZkZjQxNzNmZmQ5ZmJhNGMwMTRlM2VfanVaOFVmdUwzbFZLb1pabWZXVEtyMnJsa2RGbjBCRXRfVG9rZW46R09Vd2JCM2x4b0I5NFN4MU1uNWM4YWpNblFiXzE3MTI1NDY3OTI6MTcxMjU1MDM5Ml9WNA)

1. # **常用方法**

1. ## **plainToInstance（****plainToClass****）**

将一个普通js对象转换为指定类的实例；第一个参数为要转换成的类定义；第二个参数是一个普通对象或者是对象数组；第三个参数为可选的转换选项

默认情况下，如果对象的属性和类的属性不匹配：

- 对象中有额外的属性（即类定义中没有的属性），转换的结果会包含这些属性
- 对象中缺少类定义中的属性，这些属性将被默认设置为undefined
- 如果对象中属性和类定义的类型不匹配，会保留对象属性的值

1. ## **instanceToPlain（****classToPlain****）**

将一个类的实例转换为普通的js对象；第一个参数为类的实例；第二个参数为可选的转换选项

举例：

```JavaScript
import { instanceToPlain } from 'class-transformer'

class User {
  firstName: string
  lastName: string
  constructor(firstName: string, lastName: string) {
    this.firstName = firstName
    this.lastName = lastName
  }
  getName() {
    return this.firstName + ' ' + this.lastName
  }
}

const userInstance = new User('John', 'Cage')
const userObj = instanceToPlain(userInstance)
console.log(userObj)
```

userObj打印结果：只包含属性，不包含方法

![img](https://iq.feishu.cn/space/api/box/stream/download/asynccode/?code=ODQ4ZDRhYzVmYTc2ZDNlN2JhZjdkNjZlNTYzNWJlMDBfTmVEb2RLSmlrQmJaSUs3RmQyOHlvOVJzVmpXZWVlZmhfVG9rZW46Q1R0RWJrSzE0b3MxZTF4NkQwMGM2TVZPbjZiXzE3MTI1NDY3OTI6MTcxMjU1MDM5Ml9WNA)

1. ## **instanceToInstance（****classToClass****）**

将一个类实例转换为一个新的类实例；第一个参数为类的实例；第二个参数为可选的转换选项

举例：

```JavaScript
import { instanceToInstance } from 'class-transformer'

class User {
  firstName: string
  lastName: string
  constructor(firstName: string, lastName: string) {
    this.firstName = firstName
    this.lastName = lastName
  }
  getName() {
    return this.firstName + ' ' + this.lastName
  }
}

const userInstance = new User('John', 'Cage')
const userInstanceNew = instanceToInstance(userInstance)
console.log(userInstanceNew)
```

userInstanceNew打印结果：拥有和userInstance相同的属性和方法

![img](https://iq.feishu.cn/space/api/box/stream/download/asynccode/?code=MjViNTRhZTg0MzFkZjgxMTY0OGMyODEzZDE0NTFkNmVfaU9Gb3Zhd3k5Tlh6OU1WVjNLU056T3lVcFFCR1lVaHVfVG9rZW46UE45cGIyQVJKb0hoWEJ4V3lnbGNUb3V3bnliXzE3MTI1NDY3OTI6MTcxMjU1MDM5Ml9WNA)

1. ## **serialize** 

将类实例转换为JSON字符串；第一个参数为类的实例；第二个参数为可选的转换选项

即将废弃，不推荐使用，同JSON.stringify

1. ## **deserialize**

将JSON字符串转换为类实例；第一个参数为要转换成的类定义；第二个参数为一个JSON字符串；第三个参数为可选的转换选项

即将废弃，不推荐使用，同 plainToInstance(cls, JSON.parse(jsonStr))

1. ## **转换方法整理**

暂时无法在飞书文档外展示此内容

1. # **装饰器及选项配置**

- **装饰器：**
  - 用于自定义控制转换过程中的行为，比如哪些属性需要被包含、应该如何转换等等
- **选项配置：**
  - 转换方法的最后一个参数，允许你定制操作的具体行为。通常与装饰器配合使用

1. ## **@Expose()**

控制类的属性是否应该被包含在转换过程中

```JavaScript
import { Expose, plainToInstance } from 'class-transformer';

class User {
  // 没有被Expose标记
  id: number;
  // 无参数，标记该属性在转换过程中被包含
  @Expose()
  firstName: string;
  // 只有当指定groups中包含“admin”时才会包含该属性
  @Expose({ groups: ['admin'] })
  lastName: string;
  constructor(id: number, firstName: string, lastName: string) {
    this.id = id
    this.firstName = firstName
    this.lastName = lastName
  }
}

const userObj = {
  id: 123,
  firstName: 'John',
  lastName: 'Cage',
  extraProperty: 123,
}

// excludeExtraneousValues表示只包含Expose标识的属性，会过滤掉对象中extraProperty属性
// groups与lastName中定义的相同，返回的类中会包含password属性
const userInstance = plainToInstance(User, userObj, {
  excludeExtraneousValues: true,
  groups: ['admin'],
})
```

userInstance返回结果：

![img](https://iq.feishu.cn/space/api/box/stream/download/asynccode/?code=NmEyNjQyNTA4YzZjZTlhNzBiYzEzNjBiMTQ5NzNlNzNfUWt3N3V2TXYxbE11Nk1BVWE2Tm5xSDVsZm5YZ2JhWmNfVG9rZW46R2NiRWJHSkx6b1hCUlh4NlFMemNabldtbkNlXzE3MTI1NDY3OTI6MTcxMjU1MDM5Ml9WNA)

1. ##  **@Exclude()**

用于标记属性来在转换后排除它们

```JavaScript
import { Exclude, plainToInstance } from 'class-transformer';

class User {
  id: number
  @Exclude()
  firstName: string
  // 只有转换为对象时会排除
  @Exclude({ toPlainOnly: true })
  lastName: string
  constructor(id: number, firstName: string, lastName: string) {
    this.id = id
    this.firstName = firstName
    this.lastName = lastName
  }
}

const userObj = {
  id: 123,
  firstName: 'John',
  lastName: 'Cage',
}

const userInstance = plainToInstance(User, userObj)
```

userInstance输出结果：

![img](https://iq.feishu.cn/space/api/box/stream/download/asynccode/?code=NWM5NTYzNmEwMzU3ZGY3YjhjNjA0MDgzMjMzZjEwNzlfVlpwTmFmNDgybG42OXE1djdsN1Z5ZGFxRXJ5WXdlcTdfVG9rZW46SVJVVGIyRGV3b1BvVXp4VzdrSWMzb1JjbkVjXzE3MTI1NDY3OTI6MTcxMjU1MDM5Ml9WNA)

1. ## **@Transform()**

自定义一个函数来转换值，用来指定如何处理这个属性的值

```JavaScript
import { Transfrom, plainToInstance } from 'class-transformer';

class User {
  firstName: string
  @Transform((obj) => {
    return obj.value + '123'
  })
  lastName: string
  
  constructor(firstName: string, lastName: string) {
    this.firstName = firstName
    this.lastName = lastName
  }
}
const userObj = {
  firstName: 'John',
  lastName: 'Cage',
}
const userInstance = plainToInstance(User, userObj)
```

userInstance输出结果：

![img](https://iq.feishu.cn/space/api/box/stream/download/asynccode/?code=NDZjYjE3M2U0MzgwNjdjNTliYjA2ZDJkOWQwZjVjMWJfcG9TUnBHbUV2QmRBMnRUMGd2N0lCUDRUdGY4YktrS1VfVG9rZW46TGcxRWJkNzVKb2J1cUN4UlFGaWM0SkZRbkpmXzE3MTI1NDY3OTI6MTcxMjU1MDM5Ml9WNA)

@Transform中转换函数接收的对象参数，包括以下属性：

- key：当前属性的名称
- obj：包含当前属性的整个对象
- options：当前操作的选项配置
- type： 转换操作类型
- value：当前属性的值

![img](https://iq.feishu.cn/space/api/box/stream/download/asynccode/?code=N2E5ODc2YTIzYTBmODZiZGE4OTI0ZjJlODdlOTM0NjZfQkJ4MEgyREN0QXNCRjdJandhQ0FqUUN6SGpZM0FFT3lfVG9rZW46U0F4aWJGenNvb2tqd3V4SWpxYWN1WTBUbnRkXzE3MTI1NDY3OTI6MTcxMjU1MDM5Ml9WNA)

1. ##  **@Type()**

指定类的属性在转换时应该的目标类型，主要用于复杂类型的转换场景

```JavaScript
import { Transfrom, Type, plainToInstance } from 'class-transformer';

class Address {
  street: string
  @Transform(({ value }) => {
    return value.toUpperCase()
  })
  city: string
  constructor(street: string, city: string) {
    this.street = street
    this.city = city
  }
}

// 用户类，包含几个类型化的属性
class User {
  id: number
  @Type(() => Address) // 用于自定义 Address 类的属性
  address: Address
  constructor(id: number, address: any) {
    this.id = id
    this.address = address
  }
}

const userObj = {
  id: 1,
  address: {
    street: '123 Main St',
    city: 'shanghai',
  },
}

const userInstance = plainToInstance(User, userObj)
```

userInstane输出结果：

![img](https://iq.feishu.cn/space/api/box/stream/download/asynccode/?code=YWY3MTdiNzQzYjQyZDIyZWE5ZDMyNmFhNDdjYWIzNWVfRGs4bG5ORlpweGc2dTNtcWZHdnNnb0tLMHo5Qk5keG1fVG9rZW46Vlc1WmJhODVlb0VWS3d4RkdTdWM0RjFCbmFoXzE3MTI1NDY3OTI6MTcxMjU1MDM5Ml9WNA)

如果不增加@Type装饰器则会输出：

![img](https://iq.feishu.cn/space/api/box/stream/download/asynccode/?code=ZmQ3N2IxYmIzMDVhMjRkZjRmODBmNTdiZTc5ZDE5ZmNfZFBSSDdFejROSEhiRHZPYkduejlMZzRxVkliQWhHQWxfVG9rZW46WENWN2J1T2Jnb0x3emZ4ZUlzRmNLRDdYbk5iXzE3MTI1NDY3OTI6MTcxMjU1MDM5Ml9WNA)

1. # **tianji-service项目中使用class-transformer**

- 场景1：
  - service中使用，处理接口返回的数据，比如枚举映射、时间格式处理等
  - class的定义放到entity中
- 场景2：
  - service中使用，处理接口需要的参数，比如补充参数默认值等
  - class的定义放到dto中

1. # **配合class-validator**

1. ## **什么是class-validator**

> 文档：https://github.com/typestack/class-validator

class-validator可以为类的属性添加校验，确保类实例的属性值满足特定约束。一般配合class-transformer使用

1. ## **使用场景**

Controller层，定义Dto，代表这个post请求传入的数据结构。虽然User类定义了每个属性的类型，但其实是可以传入任意类型的，比如id传入String类型也是不会有任何报错

```JavaScript
import { Body, Controller, Post } from '@nestjs/common';

class UserDto {
  id: number
  email: string
  constructor(id: number, email: string) {
    this.id = id
    this.email = email
  }
}

@Controller('/query')
export class UserController {
  @Post('/userInfo')
  queryUserInfo(@Body() userDto: UserDto): string {
    const response = `User Info - ID: ${userDto.id}, Email: ${userDto.email}`
    return response
  }
}
```

通过class-validator对数据类型进行校验

```JavaScript
import { Body, Controller, Post } from '@nestjs/common';
import { plainToInstance } from 'class-transformer'
import { IsNumber, IsEmail, validate } from 'class-validator'

class UserDto {
  @IsNumber()
  id: number
  @IsEmail()
  email: string
  constructor(id: number, email: string) {
    this.id = id
    this.email = email
  }
}

@Controller('/query')
export class UserController {
  @Post('/userInfo')
  async queryUserInfo(@Body() userDto: UserDto): string {
    const userInstance = plainToInstance(UserDto, userDto)
    const errors = await validate(userInstance)
    if (errors.length > 0) {
      // 如果有验证错误，抛出 HttpException 异常
      throw new HttpException({
        status: HttpStatus.BAD_REQUEST,
        error: 'Validation failed',
        message: errors.map((error) => error.constraints),
      }, HttpStatus.BAD_REQUEST);
    }

    // 如果验证成功，继续处理请求
    const response = `User Info - ID: ${userDto.id}, Email: ${userDto.email}`
    return response
  }
}
```

errros返回内容：

![img](https://iq.feishu.cn/space/api/box/stream/download/asynccode/?code=OGE2M2UwZDczMjQ3NDE4Mjk1YTg4ZWM4NTJjN2Y1NWJfNjNMbG5FWUY1NGFyS3lGbkFENFlPOXVJU1p6YnYzZ1NfVG9rZW46S0dzQmJlQWl0b0Ryb2J4dWZQTmNEVlVpbm9kXzE3MTI1NDY3OTI6MTcxMjU1MDM5Ml9WNA)

1. ## **结合ValidationPipe管道**

ValidationPipie是@nestjs/common提供的，能够利用class-validator提供的装饰器进行自动验证，并且使用class-transformer来转换请求体中的普通对象为DTO的类实例。使得在Nest中处理和验证传入数据变得简单且一致

使用ValidationPipe改写queryUserInfo方法：

```JavaScript
import { Body, ValidationPipe, Controller, Post, UsePipes } from '@nestjs/common';
import { IsNumber, IsEmail } from 'class-validator'

class UserDto {
  @IsNumber()
  id: number
  @IsEmail()
  email: string
  constructor(id: number, email: string) {
    this.id = id
    this.email = email
  }
}

@Controller('/query')
export class UserController {
  @Post('/userInfo')
  @UsePipes(new ValidationPipe())
  async queryUserInfo(@Body() userDto: UserDto): string {
    // 如果验证成功，继续处理请求
    const response = `User Info - ID: ${userDto.id}, Email: ${userDto.email}`
    return response
  }
}
```

1. ## **常用装饰器**

> 更多：https://github.com/typestack/class-validator

| 类型                     | 装饰器                     | 含义                                                |
| :----------------------- | :------------------------- | :-------------------------------------------------- |
| 通用验证                 | @IsEmpty()                 | 检查给定值是否为空(=== ‘’, === null, === undefined) |
| @Equals(comparison: any) | 检查值是否相等（“===”）    |                                                     |
| @IsIn(values: any[])     | 检查值是否在允许值的数组中 |                                                     |
| 类型验证                 | @IsInt()                   | 是否为整数                                          |
| @IsDate()                | 是否为日期                 |                                                     |
| @IsString()              | 是否为字符串               |                                                     |
| 数字验证                 | @IsPositive()              | 是否是大于0的整数                                   |
| @Min(min: number)        | 是否大于等于给定的数字     |                                                     |
| 字符串验证               | @Contains(seed: string)    | 是否包含指定的子字符串                              |
| @IsBase64()              | 是否是base64编码           |                                                     |
