---
title: jest笔记
date: 2021-01-20 13:05:01
tags: 
- 测试
- jest
---

# 1、jest介绍

主流前端测试框架：Jasmine，MOCHA，jest

#### jest优点：

- 新技术
- 性能好、功能多、简单易用
- 能差异化测试，只测试变化的模块
- 快出覆盖率
- 多项目并行

#### 单元测试和集成测试的区别

- **单元测试unit testing**：指对软件中的最小可测试单元进行检查和验证。**前端所说的单元测试就是对一个模块进行测试**
- **集成测试**：也叫组装测试或者联合测试。在单元测试的基础上，将所有模块按照涉及要求组装成为子系统或系统，进行集成测试。

#### 安装：

1. `npm i jest`
2. 修改package.json 的`script`的`test`为`jest`, 或者`jest --watchAll`(自动监测变化并测试，但有时测试结果是错误的:<)
3. `npm test`



# 2、jest配置

> 只对`<name>.test.js`的文件进行测试

#### 生成配置文件jest.config.js：

```
npx jest --init
```

#### 生成覆盖率报告：

```
npx jest --coverage
```

> 覆盖率报告文件夹的名字可通过 jest.config.js的`coverageDirectroy`指定

#### 使jest支持es6的import语法：利用babel将es6转为commonjs语法

​	配置：

```json
{
  "presets": [
    ["@babel/preset-env",
      {
      "targets": {
        "node": "current"
        }
      }
    ]
  ]
}
```

# 3、匹配器

官网匹配器地址：https://jestjs.io/docs/en/expect

1. toBe(): 相当于 `===`

   ```javascript
   test('toBe', () => {    // 就是 ===
     let a = {}
     expect(a).toBe(a)
   })
   ```

2. toEqual()： 外形是否一样

   ```javascript
   test('toEqual', () => {     // 外形
     let a = {}
     expect(a).toEqual({})
   })
   ```

3. toBeNull()：是null

   ```JavaScript
   test('toBeNull', () => {
     let a = null
     expect(a).toBeNull()
   })
   ```

4. toBeUndefined / toBeDefined：是否定义

   ```JavaScript
   test('toBeUndefined', () => {
     let a 
     expect(a).toBeUndefined()
   })
   ```

5. toBeTruthy / toBeFalsy：转换为布尔值后为true还是false

   ```javascript
   test('toBeTruthy', () => {
     let a = {}
     expect(a).toBeTruthy()
   })
   ```

6. toBeGreaterThan / toBeLessThan / toBeGreaterThanOrEqual / toBeLessThanOrEqual：数值比较

   ```JavaScript
   test('toBeGreaterThan', () => {   // 大于
     let a = 10
     expect(a).toBeGreaterThan(4)
   })
   ```

7. toBeCloseTo ：解决浮点数计算不精确的问题

   ```JavaScript
   test('toBeCloseTo', () => {       // 解决浮点数计算不精确的问题
     expect(0.1 + 0.2).toBeCloseTo(0.3)
   })
   ```

8. toMatch：字符串内是否包含

   ```JavaScript
   test('toMatch', () => {         // 字符串内是否包含
     const arr = 'li, liu, wang'
     expect(arr).toMatch('wang')
   })
   ```

9. toContain：数组、set内是否包含

   ```JavaScript
   test('toContain', () => {       // 数组、set内是否包含
     const arr=['谢大脚','刘英','小红']
     const data = new Set(arr)
     expect(data).toContain('谢大脚')
   })
   ```

10. toThrow：程序内是否抛出异常

    ```JavaScript
    test('toThrow', () => {       // 程序内是否抛出异常
      function throwError() {
        // throw new Error('this is an error')
      }
      expect(throwError).not.toThrow('this is an error')    //使用 .not取反
      // expect(outError).toThrow('this is an error')
    })
    ```

# 4、异步代码测试

> 未明白原理，需要补充！！！！！！

```javascript
test('异步代码测试', (done) => {	//使用`done`表征异步完成
  fetchData((data) => {
    done();
    expect(data).toEqual(100)
  })
})
```

使用async/await实现：

```

```

# 5、钩子函数

> 钩子函数只作用于当前的文件

- beforeAll(callback)：在所有测试用例之前进行执行

- afterAll(callback)：完成所有测试用例之后才执行的函数

- beforeEach(callback)：每个测试用例前都会执行一次的钩子函数

- afterEach(callback)：每次测试用例完成测试之后执行一次的钩子函数

  ```JavaScript
  // 举例
  afterEach(()=>{	
      console.log('afterEach')
  })
  ```

  

# 6、测试作用域

`describe(测试用例)`：创建作用域