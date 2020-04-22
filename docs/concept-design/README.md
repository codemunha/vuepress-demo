---
sidebar: auto
prev: /
---

# Concept Design

## Into

### Developer ควรรู้ว่าเมื่อใดต้องทำการ refactor code

เมื่อระบบงานมีจำนวน feature มากขึ้น
Developer ก็ยิ่งเขียน code มากขึ้นเท่านั้น
ส่งผลให้ code ในระบบมีความซับซ้อนขึ้นอย่างต่อเนื่อง
ดังนั้น เราควรหยุดคิด และ พิจารณา code กันหน่อยไหม

เนื่องจากยิ่งนับวันค่าใช้จ่ายสำหรับการดูแลจัดการ code ยิ่งสูงขึ้น
ทั้งจาก code ที่มันแย่ ๆ
ทั้งจาก code ที่มันดูสวยหรู หรือ เกินจริง
กรณีที่เราพบเห็นได้ทั่วไปก็คือ
การนำ design pattern มาใช้กันตั้งแต่เริ่มต้น
บ่อยครั้งมันคือ
การที่ออกแบบเกินกว่าความต้องการจริงหรือว่าการเผื่อนั่นเอง

ดังนั้น ย้อนกลับมาดูก่อนไหมว่า
ความเจ็บปวด และ ปัญหาที่แท้จริงมันคืออะไรกันแน่
จากนั้นจึงหาวิธีการแก้ไข
เพื่อให้ความเจ็บปวด และ ปัญหามันลดน้อยลงไป

### คำถาม ###
แล้วเราจะกลับมาดู code และ แก้ไขเมื่อไรดีล่ะ ?
เป็นคำถามที่ตอบได้ยากมาก
แต่ว่ามีคำแนะนำดังนี้

### อย่าละเลยการ refactor code ด้วยการผลัดวันประกันพรุ่ง

  * Developer มักจะเขียน code ให้มันทำงานได้ไปก่อน
  * Developer มักจะบอกว่า code มันดีอยู่แล้ว อย่าไปยุ่งกับมันเลย
  * Developer มักจะบอกว่าเดี๋ยวจะกลับมาแก้ไข
  * Developer มักจะบอกว่า …
  * สุดท้าย code เหล่านั้นจะเป็นอย่างไร ?
  ... น่าคิดนะ

```java
public class BonusCalculator {

    public double getBonus(Employee employee) {
        if( employee.department == Department.IT ) {
            if(employee.year >= 5) {
                return 0.5 * employee.salary;
            }
            return 0;
        } else {
            return 1000 + 500 * employee.salary;
        }
    }
}
```


## Cleaner Code

Writing cleaner code with the rule of least power `#ROLP`

The rule of least power suggests that:
> the less powerful the [computer] language, the more you can do with the data stored in that language.

An example of this would be JSON vs Javascript object literal.

Javascript object literal is clearly more powerful:
* It can have references to variables and native javascript objects, e.g. `Set`, `Map`, `RegExp` and even functions.
* It has a more complex syntax, e.g. keys without `"`, keys with `[]` to refer to other variables etc.

In contrast, JSON is much less powerful:

* It only supports strings, numbers, JSON object, arrays, boolean and null.
* You can only define an entry with "property": ....

Although JSON is less powerful, it is much more straight-forward to parse and understand, both by humans and computers. This is one of the reasons why JSON has become the standard in data transfer nowadays.

I learnt about this rule a few years back; but have only recently realised it can also improve the quality of our code.

I would extend the rule of least power, so that it is not only applicable to choices amongst computer languages / systems, but also to choices amongst every line of code we write.

This article uses Javascript in the examples but the principle is applicable to other languages.

### Abstract
When writing computer programs, one is often faced with a choice between multiple ways to express a condition, or to perform an operation, or to solve some problem. The "Rule of Least Power" (extended) suggests choosing the least powerful way suitable for a given purpose.

### Expression Power and Readability
Readability of a piece of code has huge impact on maintainability, extensibility, optimisability etc. Readable code is much easier to be analysed, refactored and built on top of. This section explores the connection between the choice of expressions and the readability of a piece of code.

> Principle: Powerful expression inhibits readability.

The power of an expression can also be thought of as "how much more it can do beyond achieving a specific purpose".

Consider the following example:

```js
// More powerful: RegExp.prototype.test
/hi/.test(str)
// Less powerful: String.prototype.includes
str.includes('hi')
```

The first expression /hi/.test(str) is more powerful because you could do so much more with regex. str.includes('hi') is pretty much all String.prototype.includes can do.

The reason why str.includes('hi') is more readable is that it requires no extra thinking to understand it. You can be 100% sure that str.includes(...) will only check if ... is a substring of str. In the contrary, /.../.test(str) would require reading into ... in order to figure out what it actually does.

Consider another example:

```js
// More powerful: Array.prototype.reduce
['a', 'b', 'c'].reduce((acc, key) => ({
  ...acc,
  [key]: null
}), {})
// Less powerful: Object.fromEntries + Array.prototype.map
Object.fromEntries(['a', 'b', 'c'].map(key => [key, null]))
```

The same arguments about power and readability apply similarly here.  `['a', 'b', 'c'].reduce(...)`   can reduce to literally anything, whereas Object.fromEntries(...) will definitely return an object. Hence, Array.prototype.reduce is more powerful; and Object.fromEntries(...) is more readable.

More examples

```js
// More powerful: RegExp.prototype.test
/^hi$/.test(str)
// Less powerful: ===
str === 'hi'

// More powerful: RegExp.prototype.test
/^hi/.test(str)
// Less powerful: String.prototype.startsWith
str.startsWith('hi')

// More powerful: RegExp.prototype.test
/hi$/.test(str)
// Less powerful: String.prototype.endsWith
str.endsWith('hi')


/// More powerful: Array.protype.reduce
xs.reduce((x, y) => x > y ? x : y, -Infinity)
// Less powerful: Math.max
Math.max(...xs)

// More powerful: Array.prototype.reduce
parts.reduce((acc, part) => ({ ...acc, ...part }), {})
// Less powerful: Object.assign
Object.assign({}, ...parts)


// More powerful: Object.assign - can mutate first object
Object.assign({}, a, b)
// Less powerful: Object spread
{ ...a, ...b }


// More powerful: function - have its own `this`
function f() { ... }
// Less powerful: arrow function
const f = () => {...}

// More powerful: without destructure - who knows what the function will
//                                      do with the universe
const f = (universe) => { ... }
// Less powerful - f only needs earth
const f = ({ earth }) => { ... }
```

