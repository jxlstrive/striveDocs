## **Five new javascript features**

1. 使用 “Object.hasOwn” 替代 “in” 操作符（想知道对象上是否存在某个属性，一般会使用 in 操作符 或 obj.hasOwnProperty，但它们都有各自的缺陷）
---------
   * in 如果指定的属性位于对象或其原型链中，in 运算符将返回 true

    ```js
      const Person = function (age) {
        this.age = age
      }
      Person.prototype.name = 'fatfish'

      const p1 = new Person(24)
      console.log('age' in p1) // true 
      console.log('name' in p1) // true  注意这里
      console.log(p1) // Person {age: 24}
    ```
   * obj.hasOwnProperty
     **hasOwnProperty** 方法会返回一个布尔值，表示对象自身属性中是否具有对应的值（原型链上的属性不会读取）

    ```js
      const Person = function (age) {
        this.age = age
      }
      Person.prototype.name = 'fatfish'

      const p1 = new Person(24)
      console.log(p1.hasOwnProperty('age')) // true 
      console.log(p1.hasOwnProperty('name')) // fasle  注意这里
      console.log(p1)
      /* Person {
        age: 24
        [[Property]]: Object
        name: 'fatfish'
      } */
    ```
    **obj.hasOwnProperty** 已经可以过滤掉原型链上的属性，但在某些情况下，它还是不安全。

    ```js
      Object.create(null).hasOwnProperty('name')
      // Uncaught TypeError: Object.create(...).hasOwnProperty is not a function
    ```

   * Object.hasOwn 可以避免以上两个问题，这比 obj.hasOwnProperty 方法更加方便、安全

     ```js
      let object = { age: 24 }
      Object.hasOwn(object, 'age')  // true
      let object2 = Object.create({ age: 24 })
      Object.hasOwn(object2, 'age')  // false
      let object3 = Object.create(null)
      Object.hasOwn(object3, 'age') // false
     ```
---------
2. 使用 “#” 声明私有属性（以前，我们一般用 _ 表示私有属性，但它并不靠谱，还是会被外部修改。）

    ```js
      class Person {
        constructor (name) {
          this._money = 1
          this.name = name
        }
        get money () {
          return this._money
        }
        set money (money) {
          this._money = money
        }
        showMoney () {
          console.log(this._money)
        }
      }
      const p1 = new Person('fatfish')
      console.log(p1.money) // 1
      console.log(p1._money) // 1
      p1._money = 2 // 依旧可以从外部修改_money属性，所以这种做法并不安全
      console.log(p1.money) // 2
      console.log(p1._money) // 2
    ```

    **使用 # 实现真正私有属性**
    ```js
      class Person {
        #money=1
        constructor (name) {
          this.name = name
        }
        get money () {
          return this.#money
        }
        set money (money) {
          this.#money = money
        }
        showMoney () {
          console.log(this.#money)
        }
      }
      const p1 = new Person('fatfish')
      console.log(p1.money) // 1
      // p1.#money = 2 // 没法从外部直接修改
      p1.money = 2
      console.log(p1.money) // 2
      console.log(p1.#money) // Uncaught SyntaxError: Private field '#money' must be declared in an enclosing class
    ```
---------
3. 数字分隔符

    ```js
      const sixBillion = 6000000000
      // ❌ 难以阅读
      const sixBillion2 = 6000_000_000
      // ✅ 更加易于阅读
      console.log(sixBillion2) // 6000000000
    ```
    **当然也可以使用 _ 用于计算**
    ```js
    const sum = 1000 + 6000_000_000  // 600001000
    ```
---------
4. 使用"?."简化"&&"和三元运算符

   ```js
    const obj = null
    console.log(obj && obj.name)
    const $title = document.querySelector('.title')
    const title = $title ? title.innerText : undefined

    `?.`
    const obj = null
    console.log(obj?.name)
    const $title = document.querySelector('.title')
    const title = $title?.innerText
   ```
   **Tips（?. 的一般用法）**
   1. obj?.prop 对象属性
   2. obj?.[expr] 对象属性
   3. func?.(...args) 执行函数
---------
5. 使用 BigInt 支持大数计算（JS 中超过 Number.MAX_SAFE_INTEGER 的数字计算将是不安全的）

  ```js
    `注：Math.pow() 函数返回基数（base）的指数（exponent）次幂，即 base^exponent`

    Math.pow(2,53) === Math.pow(2, 53) + 1  // true
    // Math.pow(2,53)  =>  9007199254740992
    // Math.pow(2,53) + 1  =>  9007199254740992

    `使用 BigInt 完全可以避免这个问题`

    BigInt(Math.pow(2, 53)) === BigInt(Math.pow(2, 53)) + BigInt(1) // false
  ```
