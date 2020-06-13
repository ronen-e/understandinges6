<div dir="rtl">
# הרחבת פונקציונליות של אוביקטים

ECMAScript 6
שמה דגש כבד על ייעול השימוש באוביקטים. יש בזה הגיון רב מאחר וכמעט כל ערך בג׳אווהסקריפט הינו סוג מסוים של אוביקט. בנוסף, מספר האוביקטים אשר מופיע בתוכנת ג׳אווהסקריפט ממוצעת ממשיך לגדול ככל שמורכבותן של אפליקציות ג׳אווהסקריפט גדלה. משמעות הדבר היא שתוכנות קיימות מייצרות אוביקטים נוספים כל הזמן. בד בבד עם הוספת אוביקטים מגיע הצורך להשתמש בהם באופן יעיל יותר.

ECMAScript 6
משפרת את השימוש באוביקטים במספר דרכים, החל משינויי תחביר פשוטים ועד לדרכים חדשות לשנות אותם.

## קטגוריות של אוביקטים

ג׳אווהסקריפט משתמשת בערב רב של טרמינולוגיה על מנת לתאר אוביקטים אשר מופיעים בשפה, בניגוד לאלו שמתווספים לסביבת ההרצה כמו שיש בדפדפן או ב 
Node.js,
 המפרט עבור
ECMAScript 6
נותן הגדרות מדויקות עבור כל קטגוריה שונה של אוביקטים. 
חשוב מאוד להבין את הטרמינולוגיה הזו על מנת להשיג הבנה טובה של השפה עצמה. 
ואלו הן קטגוריות האוביקטים:

* *לאוביקטים רגילים*
יש את כל ההתנהגויות הפנימיות הרגילות עבור אוביקטים בג׳אווהסקריפט.
* *לאוביקטים אקזוטיים* 
יש התנהגות פנימית ששונה במידת מה מהתנהגות הרגילה
* *אוביקטים סטנדרטים* 
 הם אותם אוביקטים אשר מוגדרים על ידי
ECMAScript 6 
כמו 
<span dir="ltr">`Array`, `Date`<span>, 
וכדומה.
אוביקט סטנדרטי ייתכן שיהיה  רגיל או אקזוטי.
* *אוביקטים מובנים* 
נוכחים בסביבת הריצה של ג׳אווהסקריפט כאשר סקריפט כלשהו מתחיל לרוץ.
כל האוביקטים הסטנדרטים הינם אוביקטים מובנים.

במונחים אלו ייעשה שימוש נרחב בהמשך הספר על מנת להרחיב על האוביקטים השונים שמוגדרים תחת
ECMAScript 6.

## שינויי תחביר עבור אוביקט

אוביקט ליטראל
(object literal)
היא אחת משיטות הכתיבה הנפוצות בג׳אווהסקריפט.
JSON 
נבנה על פי אותו תחביר, וניתן למצוא אותו כמעט בכל קובץ ג׳אווהסקריפט באינטרנט. 
אוביקט ליטראל כה פופולרי כיוון ומדובר דרך קלה וקצרה ליצירת אוביקטים שאחרת היו לוקחים מספר שורות קוד. 
ECMAScript 6 
מרחיב את התחביר הנוכחי במספר צורות.

### אתחול תכונות

ב 
ECMAScript 5
ולפני כן, אוביקט ליטראל היה אוסף של זוגות
שמות-ערכים. המשמעות הייתה שתיתכן כפילות בקוד בעת אתחול תכונות.
לדוגמה:

<div dir="ltr">

```js
function createPerson(name, age) {
    return {
        name: name,
        age: age
    };
}
```
</div>

הפונקציה
<span dir="ltr">`createPerson()`</span>
מייצרת אוביקט ששמות תכונותיו זהים לשמות הפרמטרים של הפונקציה. 
ניתן לראות כפילות עבור  התכונות
`name` 
ו
`age`,
למרות שהמופע הראשון הינו שם התכונה והשני הינו ערכה.
המזהה
`name`
באוביקט שנוצר מקבל את הערך של המשתנה
`name`,
והמזהה
`age` 
מקבל את הערך של המשתנה
`age`.

ב
ECMAScript 6,
ניתן לייתר את הכפילות עבור שמות תכונות ושמות משתנים בעזרת שימוש בתחביר מקוצר בשם 
*מאתחל התכונות*
(*property initializer*).
כאשר שם התכונה זהה לשם המשתנה הלוקאלי, ניתן לכתוב את השם ללא סימון נקודותיים וערך.
לדוגמה, את הפונקציה 
<span dir="ltr">`createPerson()`</span>
ניתן לכתוב מחדש כמו בדוגמה הבאה:

<div dir="ltr">

```js
function createPerson(name, age) {
    return {
        name,
        age
    };
}
```
</div>

כאשר תוכנה של אוביקט ליטראל מכילה רק שם, מנוע ג׳אווהסקריפט מחפש בסביבתו משתנה בעל אותו שם. 
במידה ונמצא משתנה כזה, ערכו מושם לתכונה בעלת אותו שם בתוך האוביקט.
בדוגמה לעיל, התכונה
`name` 
באוביקט שנוצר מקבלת את ערכו של המשתנה  
`name`.

הרחבה זו מאפשרת כתיבת אוביקט ליטראל בצורה יותר מתומצתת ומפחיתה שגיאות במתן שמות. 
השמת ערך משתנה לתכונה בעלת אותו שם היא טכניקה נפוצה, ומכאן שימושיות ההרחבה התחבירית החדשה.

### מתודות מקוצרות

ECMAScript 6 
משפר את צורת הכתיבה עבור יצירת מתודות באוביקט ליטראל.
בגרסת
ECMAScript 5
ולפניה, היה חובה לפרט שם והגדרת פונקציה מלאה כדי להוסיף מתודה לאוביקט.
לדוגמה:

<div dir="ltr">


```js
var person = {
    name: "Nicholas",
    sayName: function() {
        console.log(this.name);
    }
};
```
</div>

בגרסת
ECMAScript 6, 
התחביר קוצר על ידי סילוק סימון נקודותיים והמילה
`function`.
כעת ניתן לכתוב את הדוגמה הקודמת כך:

<div dir="ltr">

```js
var person = {
    name: "Nicholas",
    sayName() {
        console.log(this.name);
    }
};
```

</div>

תחביר מקוצר זה, שנקרא תחביר 
*מתודה מקוצרת*
(*concise method*),
מייצר מתודה על אוביקט
`person` 
כמו בדוגמה הקודמת.
התכונה 
<span dir="ltr">`sayName()`</span>
מקבלת בתור ערך פונקציה, ויש לה את כל המאפיינים כמו הפונקציה
<span dir="ltr">`sayName()`</span>
בגרסת 
ECMAScript 5.
ההבדל היחיד הוא שמתודה מקוצרת יכולה להשתמש במזהה
`super` 
(
אשר יורחב עליו בהמשך בחלק על
״גישה קלה לפרוטוטייפ באמצעות 
super" 
)

I> ערכה של תכונת
`name` 
של מתודה שנוצרה באמצעות תחביר מתודה מקוצרת הוא השם שמופיע לפני הסוגריים. בדוגמה האחרונה, ערך תכונת 
`name` 
עבור 
<span dir="ltr">`person.sayName()`</span>
הוא
`"sayName"`

### שמות מחושבים עבור תכונות

בגרסת 
ECMAScript 5 
וקודם לכן היה ניתן לחשב שם תכונה של אוביקט כאשר אותן תכונות היו מוגדרות על ידי סוגריים מרובעים ולא על ידי נקודה 
(dot notation).
הסוגריים המרובעים מאפשרים לנו לתת שם תכונה באמצעות משתנים או מחרוזות שמכילות ערכים שהיו גורמים לשגיאת תחביר לו היו משמשים כמזהה רגיל לשם התכונה.
להלן דוגמה שממחישה זאת:

<div dir="ltr">

```js
var person = {},
    lastName = "last name";

person["first name"] = "Nicholas";
person[lastName] = "Zakas";

console.log(person["first name"]);      // "Nicholas"
console.log(person[lastName]);          // "Zakas"
```

</div>

מכיוון והמשתנה
`lastName` 
מקבל את הערך
"last name"`,
שני שמות התכונות בדוגמה לעיל מכילים רווח, 
ולכן לא ניתן לקרוא להם על ידי שימוש בנקודה. 
ואולם, שימוש בסוגריים מאפשר לכל ערך מחרוזת לשמש בתור שם תכונה, ובדוגמה לעיל מאפשר השמת הערך 
`"Nicholas"` 
לתכונה בשם
`"first name"`
והשמת הערך
`"Zakas"`
לתכונה בשם
`"last name"`

כמו כן, ניתן להשתמש במחרוזת עבור שם תכונה בתוך אוביקט. 
לדוגמה:

<div dir="ltr">

```js
var person = {
    "first name": "Nicholas"
};

console.log(person["first name"]);      // "Nicholas"
```

</div>

טכניקה זו עובדת היטב עבור שמות תכונות שידועים מראש וניתן לייצגם ע״י מחרוזת. ואולם, אילו התכונה 
`"first name"`
הייתה שמורה בתוך משתנה 
(כמו בדוגמה הקודמת)
או אם היה צורך לחשבה, אזי כלל לא הייתה קיימת דרך להגדירה בגרסת
ECMAScript 5.

בגרסת 
ECMAScript 6, 
שמות תכונות מחושבים הינם חלק מתחביר השפה, והם משתמשים בסוגריים מרובעים.
לדוגמה:

<div dir="ltr">

```js
var lastName = "last name";

var person = {
    "first name": "Nicholas",
    [lastName]: "Zakas"
};

console.log(person["first name"]);      // "Nicholas"
console.log(person[lastName]);          // "Zakas"
```

</div>

הסוגריים המרובעים בתוך אוביקט ליטראל מצביעים על היותה של התכונה בעל ערך מחושב, כך שערכה מחושב כמחרוזת.
מכאן ניתן לכלול גם ביטויים 
(expressions) 
כמו בדוגמה הבאה:

<div dir="ltr">

```js
var suffix = " name";

var person = {
    ["first" + suffix]: "Nicholas",
    ["last" + suffix]: "Zakas"
};

console.log(person["first name"]);      // "Nicholas"
console.log(person["last name"]);       // "Zakas"
```
</div>

התכונות בדוגמה מקבלות את הערכים
`"first name"`
ו- 
`"last name"`,
ואותן מחרוזות יכולות לשמש על מנת להצביע על התכונות בהמשך. 
כל ערך שניתן להכניס בתוך סוגריים מרובעים מתאים גם לשמות תכונה מחושבים בתוך אוביקט ליטראל.

## מתודות חדשות

אחד מיעדי האפיון של 
ECMAScript
החל מגרסת 
ECMAScript 5
היה הימנעות מהוספת פונקציות גלובליות או הוספת מתודות ל - 
`Object.prototype`, 
ובמקום זאת למצוא אוביקטים עליהם ניתן יהיה להוסיף מתודות חדשות. כתוצאה, אוביקט
`Object`
הגלובלי הוסיף אליו עוד מתודות כאשר אוביקטים אחרים לא נמצאו. 
ECMAScript 6 
הוסיפה מספר מתודות חדשות על אוביקט 
`Object`
הגלובלי שנועדו לפשט ביצוע משימות מסוימות.

### מתודת Object.is
כאשר משווים שני ערכים בג׳אווהסקריפט משתמשים בד״כ באופרטור
(`==`)
או לצורך השוואה עמוקה יותר - בסימן 
(`===`).
מפתחים רבים מעדיפים את האחרון, כדי להימנע מהשוואה בין סוגים שונים 
(type coercion).
גם השוואה עמוקה אינה מדויקת בכל המקרים.
כך למשל הערכים
+0
ו 
-0
נחשבים לזהים בעת שימוש באופרטור
`===`, 
אף על פי שהם מיוצגים בצורה שונה בתוך מנוע ריצה של ג׳אווהסקריפט.
כמו כן, ההשוואה 
`NaN === NaN`
מחזירה את הערך 
`false`, 
ומכאן הצורך להשתמש בפונקציה הגלובלית
<span dir="ltr">`isNaN()`</span>
על מנת לזהות את הערך 
`NaN`.

ECMAScript 6 
הוסיפה את המתודה
<span dir="ltr">`Object.is()`</span>
כדי לתת מענה למוזרויות השונות של סימן ההשוואה העמוקה.
המתודה מקבלת שני ארגומנטים ומחזירה את הערך 
`true`
במידה והערכים להשוואה זהים.
שני ערכים נחשבים זהים כאשר הם מאותו סוג ומאותו ערך.
להלן מספר דוגמאות:

<div dir="ltr">

```js
console.log(+0 == -0);              // true
console.log(+0 === -0);             // true
console.log(Object.is(+0, -0));     // false

console.log(NaN == NaN);            // false
console.log(NaN === NaN);           // false
console.log(Object.is(NaN, NaN));   // true

console.log(5 == 5);                // true
console.log(5 == "5");              // true
console.log(5 === 5);               // true
console.log(5 === "5");             // false
console.log(Object.is(5, 5));       // true
console.log(Object.is(5, "5"));     // false
```
</div>

לרוב, המתודה
<span dir="ltr">`Object.is()`</span>
תעבוד באופן זהה לאופרטור 
`===`.
ההבדלים היחידים הם שהערכים
+0 
ו 
-0
נחשבים שונים והערך 
`NaN`
זהה ל
`NaN`.
אך אין זה אומר שיש להימנע משימוש באופרטורי השוואה.
הבחירה בין השימוש בשיטת
<span dir="ltr">`Object.is()`</span>
במקום אופרטור
`==`
או אופרטור
`===`
תלויה באותם מקרים המושפעים בשוני בין השיטות ובדרך בה הוא משפיע על הקוד שלך.

</div>

### The Object.assign() Method

*Mixins* are among the most popular patterns for object composition in JavaScript. In a mixin, one object receives properties and methods from another object. Many JavaScript libraries have a mixin method similar to this:

```js
function mixin(receiver, supplier) {
    Object.keys(supplier).forEach(function(key) {
        receiver[key] = supplier[key];
    });

    return receiver;
}
```

The `mixin()` function iterates over the own properties of `supplier` and copies them onto `receiver` (a shallow copy, where object references are shared when property values are objects). This allows the `receiver` to gain new properties without inheritance, as in this code:

```js
function EventTarget() { /*...*/ }
EventTarget.prototype = {
    constructor: EventTarget,
    emit: function() { /*...*/ },
    on: function() { /*...*/ }
};

var myObject = {};
mixin(myObject, EventTarget.prototype);

myObject.emit("somethingChanged");
```

Here, `myObject` receives behavior from the `EventTarget.prototype` object. This gives `myObject` the ability to publish events and subscribe to them using the `emit()` and `on()` methods, respectively.

This pattern became popular enough that ECMAScript 6 added the `Object.assign()` method, which behaves the same way, accepting a receiver and any number of suppliers, and then returning the receiver. The name change from `mixin()` to `assign()` reflects the actual operation that occurs. Since the `mixin()` function uses the assignment operator (`=`), it cannot copy accessor properties to the receiver as accessor properties. The name `Object.assign()` was chosen to reflect this distinction.

I> Similar methods in various libraries may have other names for the same basic functionality; popular alternates include the `extend()` and `mix()` methods. There was also, briefly, an `Object.mixin()` method in ECMAScript 6 in addition to the `Object.assign()` method. The primary difference was that `Object.mixin()` also copied over accessor properties, but the method was removed due to concerns over the use of `super` (discussed in the "Easy Prototype Access with Super References" section of this chapter).

You can use `Object.assign()` anywhere the `mixin()` function would have been used. Here's an example:

```js
function EventTarget() { /*...*/ }
EventTarget.prototype = {
    constructor: EventTarget,
    emit: function() { /*...*/ },
    on: function() { /*...*/ }
}

var myObject = {}
Object.assign(myObject, EventTarget.prototype);

myObject.emit("somethingChanged");
```

The `Object.assign()` method accepts any number of suppliers, and the receiver receives the properties in the order in which the suppliers are specified. That means the second supplier might overwrite a value from the first supplier on the receiver, which is what happens in this snippet:

```js
var receiver = {};

Object.assign(receiver,
    {
        type: "js",
        name: "file.js"
    },
    {
        type: "css"
    }
);

console.log(receiver.type);     // "css"
console.log(receiver.name);     // "file.js"
```

The value of `receiver.type` is `"css"` because the second supplier overwrote the value of the first.

The `Object.assign()` method isn't a big addition to ECMAScript 6, but it does formalize a common function found in many JavaScript libraries.

A> ### Working with Accessor Properties
A>
A> Keep in mind that `Object.assign()` doesn't create accessor properties on the receiver when a supplier has accessor properties. Since `Object.assign()` uses the assignment operator, an accessor property on a supplier will become a data property on the receiver. For example:
A>
A> ```js
A> var receiver = {},
A>     supplier = {
A>         get name() {
A>             return "file.js"
A>         }
A>     };
A>
A> Object.assign(receiver, supplier);
A>
A> var descriptor = Object.getOwnPropertyDescriptor(receiver, "name");
A>
A> console.log(descriptor.value);      // "file.js"
A> console.log(descriptor.get);        // undefined
A> ```
A>
A> In this code, the `supplier` has an accessor property called `name`. After using the `Object.assign()` method, `receiver.name` exists as a data property with a value of `"file.js"` because `supplier.name` returned `"file.js"` when `Object.assign()` was called.

## Duplicate Object Literal Properties

ECMAScript 5 strict mode introduced a check for duplicate object literal properties that would throw an error if a duplicate was found. For example, this code was problematic:

```js
"use strict";

var person = {
    name: "Nicholas",
    name: "Greg"        // syntax error in ES5 strict mode
};
```

When running in ECMAScript 5 strict mode, the second `name` property causes a syntax error. But in ECMAScript 6, the duplicate property check was removed. Both strict and nonstrict mode code no longer check for duplicate properties. Instead, the last property of the given name becomes the property's actual value, as shown here:

```js
"use strict";

var person = {
    name: "Nicholas",
    name: "Greg"        // no error in ES6 strict mode
};

console.log(person.name);       // "Greg"
```

In this example, the value of `person.name` is `"Greg"` because that's the last value assigned to the property.

## Own Property Enumeration Order

ECMAScript 5 didn't define the enumeration order of object properties, as it left this up to the JavaScript engine vendors. However, ECMAScript 6 strictly defines the order in which own properties must be returned when they are enumerated. This affects how properties are returned using `Object.getOwnPropertyNames()` and `Reflect.ownKeys` (covered in Chapter 12). It also affects the order in which properties are processed by `Object.assign()`.

The basic order for own property enumeration is:

1. All numeric keys in ascending order
2. All string keys in the order in which they were added to the object
3. All symbol keys (covered in Chapter 6) in the order in which they were added to the object

Here's an example:

```js
var obj = {
    a: 1,
    0: 1,
    c: 1,
    2: 1,
    b: 1,
    1: 1
};

obj.d = 1;

console.log(Object.getOwnPropertyNames(obj).join(""));     // "012acbd"
```

The `Object.getOwnPropertyNames()` method returns the properties in `obj` in the order `0`, `1`, `2`, `a`, `c`, `b`, `d`. Note that the numeric keys are grouped together and sorted, even though they appear out of order in the object literal. The string keys come after the numeric keys and appear in the order that they were added to `obj`. The keys in the object literal itself come first, followed by any dynamic keys that were added later (in this case, `d`).

W> The `for-in` loop still has an unspecified enumeration order because not all JavaScript engines implement it the same way. The `Object.keys()` method and `JSON.stringify()` are both specified to use the same (unspecified) enumeration order as `for-in`.

While enumeration order is a subtle change to how JavaScript works, it's not uncommon to find programs that rely on a specific enumeration order to work correctly. ECMAScript 6, by defining the enumeration order, ensures that JavaScript code relying on enumeration will work correctly regardless of where it is executed.

## More Powerful Prototypes

Prototypes are the foundation of inheritance in JavaScript, and ECMAScript 6 continues to make prototypes more powerful. Early versions of JavaScript severely limited what could be done with prototypes. However, as the language matured and developers became more familiar with how prototypes work, it became clear that developers wanted more control over prototypes and easier ways to work with them. As a result, ECMAScript 6 introduced some improvements to prototypes.

### Changing an Object's Prototype

Normally, the prototype of an object is specified when the object is created, via either a constructor or the `Object.create()` method. The idea that an object's prototype remains unchanged after instantiation was one of the biggest assumptions in JavaScript programming through ECMAScript 5. ECMAScript 5 did add the `Object.getPrototypeOf()` method for retrieving the prototype of any given object, but it still lacked a standard way to change an object's prototype after instantiation.

ECMAScript 6 changes that assumption by adding the `Object.setPrototypeOf()` method, which allows you to change the prototype of any given object. The `Object.setPrototypeOf()` method accepts two arguments: the object whose prototype should change and the object that should become the first argument's prototype. For example:

```js
let person = {
    getGreeting() {
        return "Hello";
    }
};

let dog = {
    getGreeting() {
        return "Woof";
    }
};

// prototype is person
let friend = Object.create(person);
console.log(friend.getGreeting());                      // "Hello"
console.log(Object.getPrototypeOf(friend) === person);  // true

// set prototype to dog
Object.setPrototypeOf(friend, dog);
console.log(friend.getGreeting());                      // "Woof"
console.log(Object.getPrototypeOf(friend) === dog);     // true
```

This code defines two base objects: `person` and `dog`. Both objects have a `getGreeting()` method that returns a string. The object `friend` first inherits from the `person` object, meaning that `getGreeting()` outputs `"Hello"`. When the prototype becomes the `dog` object, `friend.getGreeting()` outputs `"Woof"` because the original relationship to `person` is broken.

The actual value of an object's prototype is stored in an internal-only property called `[[Prototype]]`. The `Object.getPrototypeOf()` method returns the value stored in `[[Prototype]]` and `Object.setPrototypeOf()` changes the value stored in `[[Prototype]]`. However, these aren't the only ways to work with the value of `[[Prototype]]`.

### Easy Prototype Access with Super References

As previously mentioned, prototypes are very important for JavaScript and a lot of work went into making them easier to use in ECMAScript 6. Another improvement is the introduction of `super` references, which make accessing functionality on an object's prototype easier. For example, to override a method on an object instance such that it also calls the prototype method of the same name, you'd do the following:

```js
let person = {
    getGreeting() {
        return "Hello";
    }
};

let dog = {
    getGreeting() {
        return "Woof";
    }
};


let friend = {
    getGreeting() {
        return Object.getPrototypeOf(this).getGreeting.call(this) + ", hi!";
    }
};

// set prototype to person
Object.setPrototypeOf(friend, person);
console.log(friend.getGreeting());                      // "Hello, hi!"
console.log(Object.getPrototypeOf(friend) === person);  // true

// set prototype to dog
Object.setPrototypeOf(friend, dog);
console.log(friend.getGreeting());                      // "Woof, hi!"
console.log(Object.getPrototypeOf(friend) === dog);     // true
```

In this example, `getGreeting()` on `friend` calls the prototype method of the same name. The `Object.getPrototypeOf()` method ensures the correct prototype is called, and then an additional string is appended to the output. The additional `.call(this)` ensures that the `this` value inside the prototype method is set correctly.

Remembering to use `Object.getPrototypeOf()` and `.call(this)` to call a method on the prototype is a bit involved, so ECMAScript 6 introduced `super`. At its simplest, `super` is a pointer to the current object's prototype, effectively the `Object.getPrototypeOf(this)` value. Knowing that, you can simplify the `getGreeting()` method as follows:

```js
let friend = {
    getGreeting() {
        // in the previous example, this is the same as:
        // Object.getPrototypeOf(this).getGreeting.call(this)
        return super.getGreeting() + ", hi!";
    }
};
```

The call to `super.getGreeting()` is the same as `Object.getPrototypeOf(this).getGreeting.call(this)` in this context. Similarly, you can call any method on an object's prototype by using a `super` reference, so long as it's inside a concise method. Attempting to use `super` outside of concise methods results in a syntax error, as in this example:

```js
let friend = {
    getGreeting: function() {
        // syntax error
        return super.getGreeting() + ", hi!";
    }
};
```

This example uses a named property with a function, and the call to `super.getGreeting()` results in a syntax error because `super` is invalid in this context.

The `super` reference is really powerful when you have multiple levels of inheritance, because in that case, `Object.getPrototypeOf()` no longer works in all circumstances. For example:

```js
let person = {
    getGreeting() {
        return "Hello";
    }
};

// prototype is person
let friend = {
    getGreeting() {
        return Object.getPrototypeOf(this).getGreeting.call(this) + ", hi!";
    }
};
Object.setPrototypeOf(friend, person);


// prototype is friend
let relative = Object.create(friend);

console.log(person.getGreeting());                  // "Hello"
console.log(friend.getGreeting());                  // "Hello, hi!"
console.log(relative.getGreeting());                // error!
```

The call to `Object.getPrototypeOf()` results in an error when `relative.getGreeting()` is called. That's because `this` is `relative`, and the prototype of `relative` is the `friend` object. When `friend.getGreeting().call()` is called with `relative` as `this`, the process starts over again and continues to call recursively until a stack overflow error occurs.

That problem is difficult to solve in ECMAScript 5, but with ECMAScript 6 and `super`, it's easy:

```js
let person = {
    getGreeting() {
        return "Hello";
    }
};

// prototype is person
let friend = {
    getGreeting() {
        return super.getGreeting() + ", hi!";
    }
};
Object.setPrototypeOf(friend, person);


// prototype is friend
let relative = Object.create(friend);

console.log(person.getGreeting());                  // "Hello"
console.log(friend.getGreeting());                  // "Hello, hi!"
console.log(relative.getGreeting());                // "Hello, hi!"
```

Because `super` references are not dynamic, they always refer to the correct object. In this case, `super.getGreeting()` always refers to `person.getGreeting()`, regardless of how many other objects inherit the method.

## A Formal Method Definition

Prior to ECMAScript 6, the concept of a "method" wasn't formally defined. Methods were just object properties that contained functions instead of data. ECMAScript 6 formally defines a method as a function that has an internal `[[HomeObject]]` property containing the object to which the method belongs. Consider the following:

```js
let person = {

    // method
    getGreeting() {
        return "Hello";
    }
};

// not a method
function shareGreeting() {
    return "Hi!";
}
```

This example defines `person` with a single method called `getGreeting()`. The `[[HomeObject]]` for `getGreeting()` is `person` by virtue of assigning the function directly to an object. The `shareGreeting()` function, on the other hand, has no `[[HomeObject]]` specified because it wasn't assigned to an object when it was created. In most cases, this difference isn't important, but it becomes very important when using `super` references.

Any reference to `super` uses the `[[HomeObject]]` to determine what to do. The first step is to call `Object.getPrototypeOf()` on the `[[HomeObject]]` to retrieve a reference to the prototype. Then, the prototype is searched for a function with the same name. Last, the `this` binding is set and the method is called. Here's an example:

```js
let person = {
    getGreeting() {
        return "Hello";
    }
};

// prototype is person
let friend = {
    getGreeting() {
        return super.getGreeting() + ", hi!";
    }
};
Object.setPrototypeOf(friend, person);

console.log(friend.getGreeting());  // "Hello, hi!"
```

Calling `friend.getGreeting()` returns a string, which combines the value from `person.getGreeting()` with `", hi!"`. The `[[HomeObject]]` of `friend.getGreeting()` is `friend`, and the prototype of `friend` is `person`, so `super.getGreeting()` is equivalent to `person.getGreeting.call(this)`.

## Summary

Objects are the center of programming in JavaScript, and ECMAScript 6 made some helpful changes to objects that both make them easier to deal with and more powerful.

ECMAScript 6 makes several changes to object literals. Shorthand property definitions make assigning properties with the same names as in-scope variables easier. Computed property names allow you to specify non-literal values as property names, which you've already been able to do in other areas of the language. Shorthand methods let you type a lot fewer characters in order to define methods on object literals, by completely omitting the colon and `function` keyword. ECMAScript 6 loosens the strict mode check for duplicate object literal property names as well, meaning you can have two properties with the same name in a single object literal without throwing an error.

The `Object.assign()` method makes it easier to change multiple properties on a single object at once. This can be very useful if you use the mixin pattern. The `Object.is()` method performs strict equality on any value, effectively becoming a safer version of `===` when dealing with special JavaScript values.

Enumeration order for own properties is now clearly defined in ECMAScript 6. When enumerating properties, numeric keys always come first in ascending order followed by string keys in insertion order and symbol keys in insertion order.

It's now possible to modify an object's prototype after it's already created, thanks to ECMAScript 6's `Object.setPrototypeOf()` method.

Finally, you can use the `super` keyword to call methods on an object's prototype. The `this` binding inside a method invoked using `super` is set up to automatically work with the current value of `this`.
