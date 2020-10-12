<div dir="rtl">
# קידוד עם מודולים

גישת ה ״שיתוף הכל״ של js לטעינת קוד היא אחד ההיבטים המועדים לשגיאה והמבלבלים ביותר בשפה. שפות אחרות משתמשות במושגים כמו חבילות כדי להגדיר את היקף הקוד, אך לפני ECMAScript 6, כל מה שהוגדר בכל קובץ JavaScript של יישום שיתף סקופ גלובלי אחד. כאשר אפליקציות ווב הפכו מורכבים יותר והחלו להשתמש בקוד JavaScript עוד יותר, גישה זו גרמה לבעיות שונות כמו: התנגשויות שמות ובעיות אבטחה. מטרה אחת של ECMAScript 6 הייתה לפתור את בעיית הסקופ ולהביא קצת סדר ליישומי JavaScript. לשם נכנסים מודולים.

## מה זה Modules?

_מודולים_ הם קבצי JavaScript הנטענים במצב שונה (בניגוד ל _סקריפטים_, הנטענים באופן המקורי ש- JavaScript עבד). מצב שונה זה הכרחי מכיוון שלמודולים יש סמנטיקה שונה מאוד מאשר סקריפטים:

1. קוד המודול פועל באופן אוטומטי במצב קפדני (strict mode), ואין דרך לבטלו
1. משתנים שנוצרו ברמה העליונה של מודול אינם מתווספים אוטומטית לסקופ הגלובלי המשותף. הם קיימים רק בסקופ העליון של המודול.
1. הערך של `this` ברמה העליונה של המודול הוא `undefined`.
1. מודולים אינם מאפשרים הערות בסגנון HTML בקוד (מאפיין שאריות מימי הדפדפן המוקדמים של JavaScript).
1. על המודולים לייצא כל מה שאמור להיות זמין לקוד מחוץ למודול.
1. מודולים רשאים לייבא ממודולים אחרים.

הבדלים אלה עשויים להיראות קטנים במבט ראשון, אך הם מייצגים שינוי משמעותי באופן טעינת קוד ה- JavaScript והערכתו, עליו אדון במהלך פרק זה. הכוח האמיתי של המודולים הוא היכולת לייצא ולייבא רק חלקים שאתה זקוק להן, ולא כל מה שיש בקובץ. הבנה טובה של ייצוא וייבוא הינה יסודית להבנת האופן שבו מודולים שונים מתסריטים.

## ייצוא בסיסי

אתה יכול להשתמש במילת המפתח `export` כדי לחשוף חלקים מהקוד שפורסם למודולים אחרים. במקרה הפשוט ביותר, אתה יכול להציב `export` מול כל הצהרת משתנה, פונקציה או מחלקה כדי לייצא אותו מהמודול, כך:

</div>

```js
// export data
export var color = "red";
export let name = "Nicholas";
export const magicNumber = 7;

// export function
export function sum(num1, num2) {
  return num1 + num1;
}

// export class
export class Rectangle {
  constructor(length, width) {
    this.length = length;
    this.width = width;
  }
}

// this function is private to the module
function subtract(num1, num2) {
  return num1 - num2;
}

// define a function...
function multiply(num1, num2) {
  return num1 * num2;
}

// ...and then export it later
export { multiply };
```

<div dir="rtl">

יש לשים לב לדוגמא זו לכמה דברים. ראשית, מלבד מילת המפתח `export` , כל הצהרה זהה לחלוטין לדרך הרגילה. לכל פונקציה או מחלקה מיוצאת יש גם שם; הסיבה לכך היא שהייצוא של פונקציות והצהרות כיתות דורשות שם. אינך יכול לייצא פונקציות או מחלקות אנונימיות באמצעות תחביר זה אלא אם כן אתה משתמש במילת המפתח 'ברירת מחדל' (נדון בפירוט בסעיף "ערכי ברירת מחדל במודולים").

לאחר מכן שקול את הפונקציה `multiply()` , שאינה מיוצאת כאשר היא מוגדרת. זה עובד כי לא תמיד צריך לייצא הצהרה: אתה יכול גם לייצא הפניות. לבסוף, שים לב כי דוגמה זו אינה מייצאת את הפונקציה `subtract()`. פונקציה זו לא תהיה נגישה מחוץ למודול זה מכיוון שמשתנים, פונקציות או מחלקות שלא מיוצאים במפורש נשארים פרטיים למודול.

## ייבוא בסיסי

ברגע שיש לך מודול עם ייצוא, אתה יכול לגשת לפונקציונליות במודול אחר באמצעות מילת המפתח `import`. שני החלקים של משפט `import` הם המזהים שאתה מייבא והמודול ממנו יש לייבא מזהים אלה. זו הצורה הבסיסית של ההצהרה:

</div>

```js
import { identifier1, identifier2 } from "./example.js";
```

<div dir="rtl">

הסוגריים המסולסלות לאחר `import` מציינת את החלקים לייבוא ממודול נתון. מילת המפתח `from` מציינת את המודול שממנו ניתן לייבא את החלק הנתון. המודול מוגדר על ידי מחרוזת המייצגת את הנתיב למודול (הנקרא _ specifule_module_). הדפדפנים משתמשים באותו פורמט נתיב שעשוי להעביר לאלמנט `<script>`, כלומר עליך לכלול סיומת קובץ. לעומת זאת, Node.js עוקב אחר המוסכמה המסורתית שלה להבדיל בין קבצים וחבילות מקומיים בהתבסס על קידומת מערכת קבצים. לדוגמה,`example` תהיה חבילה ו- `./example.js` תהיה קובץ מקומי.

I> רשימת החלקים לייבוא נראית דומה לאובייקט , אך הוא אינו כזה.

בעת ייבוא חלק ממודול, הקוד פועלת כאילו הוגדרה באמצעות `const`. כלומר, אינך יכול להגדיר משתנה אחר בעל אותו שם (כולל ייבוא חלק נוספת באותו שם), להשתמש במזהה לפני משפט `import` או לשנות את ערכו.

### ייבוא חלק קוד כיחידה

נניח שהדוגמה הראשונה בסעיף "ייצוא בסיסי" היא במודול עם שם הקובץ `example.js`. ניתן לייבא ולהשתמש בחלקים מאותו מודול במספר דרכים. למשל, אתה יכול פשוט לייבא מזהה אחד:

</div>

```js
// import just one
import { sum } from "./example.js";

console.log(sum(1, 2)); // 3

sum = 1; // error
```

<div dir="rtl">

למרות ש- `example.js` מייצאת יותר מפונקציה אחת בלבד, דוגמה זו מייבאת רק את הפונקציה `sum()`. אם תנסה להקצות ערך חדש ל `sum`, התוצאה היא שגיאה, מכיוון שלא תוכל להקצות מחדש חלקים מיובאות.

W> הקפד לכלול את `/`, `./`, או `../` בתחילת הקובץ שאתה מייבא לצורך התאמה טובה ביותר בין דפדפנים ו- Node.js.

### ייבוא חלקי קוד מרובים

אם ברצונך לייבא חלקים מרובים ממודול הדוגמה, תוכל לפרט אותן באופן מפורש כדלקמן:

</div>

```js
// import multiple
import { sum, multiply, magicNumber } from "./example.js";
console.log(sum(1, magicNumber)); // 8
console.log(multiply(1, 2)); // 2
```

<div dir="rtl">

כאן, שלוש חלקי קוד מיובאים ממודול הדוגמה: `sum`, `multiply` ו`magicNumber`. לאחר מכן הם משמשים כאילו הם הוגדרו באופן מקומי.

### ייבוא כל המודול

יש גם מקרה מיוחד המאפשר לך לייבא את כל המודול כאובייקט יחיד. כל הייצוא זמין אז לאובייקט כמאפיינים. לדוגמה:

</div>

```js
// import everything
import * as example from "./example.js";
console.log(example.sum(1, example.magicNumber)); // 8
console.log(example.multiply(1, 2)); // 2
```

<div dir="rtl">

בקוד זה, כל החלקים המיוצאים ב- `example.js` נטענות לאובייקט שנקרא`example`. לאחר מכן ניתן יהיה לייצא את הייצוא הנקוב (פונקציית `sum()`, פונקצית `multiple()` ו- `magicNumber` ) כתכונות ב `example`. פורמט ייבוא זה נקרא ייבוא _שמם_ (_namespace import_ ) מכיוון שהאובייקט `example` אינו קיים בתוך הקובץ `example.js`.במקום זה נוצר כדי לשמש כאובייקט מרחב שמות עבור כל החברים המיוצאים של `example.js` .

זכור, עם זאת, שלא משנה כמה פעמים אתה משתמש במודול בהצהרות `import` , המודול יבוצע רק פעם אחת. לאחר ביצוע הקוד לייבוא המודול, המודול המייצר נשמר בזיכרון ומשמש אותו מחדש בכל פעם שמשפט אחר של `import` מפנה אליו. שקול את הדברים הבאים:

</div>

```js
import { sum } from "./example.js";
import { multiply } from "./example.js";
import { magicNumber } from "./example.js";
```

<div dir="rtl">

למרות שישנן שלוש הצהרות `import` במודול זה,`example.js` יבוצע רק פעם אחת. אם מודולים אחרים באותה יישום היו מייבאים חלקים מ- `example.js` , מודולים אלה ישתמשו באותו מופע מודול בו קוד משתמש.

### מגבלות תחביר המודול

מגבלה חשובה הן של `export` והן של `import` היא שיש להשתמש בהם מחוץ להצהרות ופונקציות אחרות. למשל, קוד זה ייתן שגיאת תחביר:

</div>

```js
if (flag) { export flag; } // syntax error
```

<div dir="rtl">

הצהרת `export` נמצאת בתוך הצהרת 'אם', שאינה מותרת. היצוא לא יכול להיות מותנה או להתבצע באופן דינמי בשום צורה שהיא. אחת הסיבות לכך שקיימת תחביר של מודולים היא לאפשר למנוע JavaScript לקבוע באופן סטטי מה ייצא. ככזה, אתה יכול להשתמש רק ב- `export` ברמה העליונה של המודול.

באופן דומה, אינך יכול להשתמש ב `import` בתוך הצהרה; אתה יכול להשתמש בו רק ברמה העליונה. כלומר, קוד זה נותן גם שגיאת תחביר:

</div>

```js
function tryImport() {
  import flag from "./example.js";
} // syntax error
```

<div dir="rtl">

אינך יכול לייבא חלקים בצורה דינמית מאותה סיבה שלא ניתן לייצא חלקחים בצורה דינמית. מילות המפתח`export` ו `import` נועדו להיות סטטיות כך שכלים כמו עורכי טקסט יוכלו לדעת בקלות איזה מידע זמין ממודול.

### A Subtle Quirk of Imported Bindings

הצהרות `יבוא` של ECMAScript 6 יוצרות אפרות קריאה בלבד למשתנים, פונקציות וקלאסים במקום להתייחס רק לקוד המקור כמו למשתנים רגילים. למרות שהמודול שמייבא את הקוד לא יכול לשנות את ערכו, המודול שמייצא את המזהה יכול. לדוגמה, נניח שתרצה להשתמש במודול זה:

</div>

```js
export var name = "Nicholas";
export function setName(newName) {
  name = newName;
}
```

<div dir="rtl">

כאשר אתה מייבא את שתי הפונקציות האלה, הפונקציה `setName ()` יכולה לשנות את הערך של `שם`:

</div>

```js
import { name, setName } from "./example.js";

console.log(name); // "Nicholas"
setName("Greg");
console.log(name); // "Greg"

name = "Nicholas"; // error
```

<div dir="rtl">

הקריאה ל `setName("Greg")` חוזרת למודול שממנו יוצא `setName()` ומבוצעת שם, ומגדירה שם את `name` ל `"Greg"` . שים לב ששינוי זה משתקף אוטומטית בחלק `name` המיובא. הסיבה לכך היא ש `name` הוא השם המקומי למזהה `name` המיוצא. `name` המשמש בקוד לעיל וה `name` המשמש במודול המיובא ממנו אינם זהים.

## Renaming Exports and Imports

לפעמים, אולי לא תרצה להשתמש בשם המקורי של משתנה, פונקציה או מחלקה שייבאת ממודול. למרבה המזל, אתה יכול לשנות את שם היצוא גם במהלך הייצוא וגם במהלך הייבוא.

במקרה הראשון, נניח שיש לך פונקציה שברצונך לייצא בשם אחר. אתה יכול להשתמש במילת המפתח `as` כדי לציין את השם שהפונקציה אמורה להיות מחוץ למודול:

</div>

```js
function sum(num1, num2) {
  return num1 + num2;
}

export { sum as add };
```

<div dir="rtl">

כאן, הפונקציה`sum()` (`sum` היא השם המקומי) מיוצאת כ-`add () `(`add` הוא השם המיוצא). פירוש הדבר שכאשר מודול אחר מעוניין לייבא פונקציה זו, יהיה עליו להשתמש בשם `add` במקום זאת:

</div>

```js
import { add } from "./example.js";
```

<div dir="rtl">

אם המודול שמייבא את הפונקציה רוצה להשתמש בשם אחר, הוא יכול גם להשתמש ב `as`:

</div>

```js
import { add as sum } from "./example.js";
console.log(typeof add); // "undefined"
console.log(sum(1, 2)); // 3
```

<div dir="rtl">

קוד זה מייבא את הפונקציה `add ()` באמצעות import ומשנה את שמו ל- `sum ()` (השם המקומי). פירוש הדבר שאין במודול זה מזהה בשם `add`.

## ברירת מחדל במודולים

תחביר המודול מותאם באמת לייצוא וייבוא של ערכי ברירת מחדל ממודולים, מכיוון שדפוס זה היה נפוץ למדי במערכות מודולים אחרות, כמו CommonJS (פורמט מודול JavaScript אחר הפופולרי על ידי Node.js). ערך ברירת המחדל עבור מודול הוא משתנה, פונקציה או מחלקה כפי שצוינו על ידי מילת המפתח `default`, וניתן להגדיר רק יצוא ברירת מחדל אחד לכל מודול. שימוש במילת המפתח `default` עם יצוא מרובה יגרום לשגיאת תחביר.

### Exporting Default Values

Here's a simple example that uses the `default` keyword:

</div>

```js
export default function (num1, num2) {
  return num1 + num2;
}
```

This module exports a function as its default value. The `default` keyword indicates that this is a default export. The function doesn't require a name because the module itself represents the function.

You can also specify an identifier as the default export by placing it after `export default`, such as:

```js
function sum(num1, num2) {
  return num1 + num2;
}

export default sum;
```

Here, the `sum()` function is defined first and later exported as the default value of the module. You may want to choose this approach if the default value needs to be calculated.

A third way to specify an identifier as the default export is by using the renaming syntax as follows:

```js
function sum(num1, num2) {
  return num1 + num2;
}

export { sum as default };
```

The identifier `default` has special meaning in a renaming export and indicates a value should be the default for the module. Because `default` is a keyword in JavaScript, it can't be used for a variable, function, or class name (it can be used as a property name). So the use of `default` to rename an export is a special case to create a consistency with how non-default exports are defined. This syntax is useful if you want to use a single `export` statement to specify multiple exports, including the default, at once.

### Importing Default Values

You can import a default value from a module using the following syntax:

```js
// import the default
import sum from "./example.js";

console.log(sum(1, 2)); // 3
```

This import statement imports the default from the module `example.js`. Note that no curly braces are used, unlike you'd see in a non-default import. The local name `sum` is used to represent whatever default function the module exports. This syntax is the cleanest, and the creators of ECMAScript 6 expect it to be the dominant form of import on the Web, allowing you to use an already-existing object.

For modules that export both a default and one or more non-default bindings, you can import all exported bindings with one statement. For instance, suppose you have this module:

```js
export let color = "red";

export default function (num1, num2) {
  return num1 + num2;
}
```

You can import both `color` and the default function using the following `import` statement:

```js
import sum, { color } from "./example.js";

console.log(sum(1, 2)); // 3
console.log(color); // "red"
```

The comma separates the default local name from the non-defaults, which are also surrounded by curly braces. Keep in mind that the default must come before the non-defaults in the `import` statement.

As with exporting defaults, you can import defaults with the renaming syntax, too:

```js
// equivalent to previous example
import { default as sum, color } from "example";

console.log(sum(1, 2)); // 3
console.log(color); // "red"
```

In this code, the default export (`default`) is renamed to `sum` and the additional `color` export is also imported. This example is equivalent to the preceding example.

## Re-exporting a Binding

There may be a time when you'd like to re-export something that your module has imported (for instance, if you're creating a library out of several small modules). You can re-export an imported value with the patterns already discussed in this chapter as follows:

```js
import { sum } from "./example.js";
export { sum };
```

That works, but a single statement can also do the same thing:

```js
export { sum } from "./example.js";
```

This form of `export` looks into the specified module for the declaration of `sum` and then exports it. Of course, you can also choose to export a different name for the same value:

```js
export { sum as add } from "./example.js";
```

Here, `sum` is imported from `"./example.js"` and then exported as `add`.

If you'd like to export everything from another module, you can use the `*` pattern:

```js
export * from "./example.js";
```

When you export everything, you are including all named exports and excluding any default export. For instance, if `example.js` has a default export, you would need to import it explicitly and then export it explicitly.

## Importing Without Bindings

Some modules may not export anything, and instead, only make modifications to objects in the global scope. Even though top-level variables, functions, and classes inside modules don't automatically end up in the global scope, that doesn't mean modules cannot access the global scope. The shared definitions of built-in objects such as `Array` and `Object` are accessible inside a module and changes to those objects will be reflected in other modules.

For instance, if you want to add a `pushAll()` method to all arrays, you might define a module like this:

```js
// module code without exports or imports
Array.prototype.pushAll = function (items) {
  // items must be an array
  if (!Array.isArray(items)) {
    throw new TypeError("Argument must be an array.");
  }

  // use built-in push() and spread operator
  return this.push(...items);
};
```

This is a valid module even though there are no exports or imports. This code can be used both as a module and a script. Since it doesn't export anything, you can use a simplified import to execute the module code without importing any bindings:

```js
import "./example.js";

let colors = ["red", "green", "blue"];
let items = [];

items.pushAll(colors);
```

This code imports and executes the module containing the `pushAll()` method, so `pushAll()` is added to the array prototype. That means `pushAll()` is now available for use on all arrays inside of this module.

I> Imports without bindings are most likely to be used to create polyfills and shims.

## Loading Modules

While ECMAScript 6 defines the syntax for modules, it doesn't define how to load them. This is part of the complexity of a specification that's supposed to be agnostic to implementation environments. Rather than trying to create a single specification that would work for all JavaScript environments, ECMAScript 6 specifies only the syntax and abstracts out the loading mechanism to an undefined internal operation called `HostResolveImportedModule`. Web browsers and Node.js are left to decide how to implement `HostResolveImportedModule` in a way that makes sense for their respective environments.

### Using Modules in Web Browsers

Even before ECMAScript 6, web browsers had multiple ways of including JavaScript in an web application. Those script loading options are:

1. Loading JavaScript code files using the `<script>` element with the `src` attribute specifying a location from which to load the code.
1. Embedding JavaScript code inline using the `<script>` element without the `src` attribute.
1. Loading JavaScript code files to execute as workers (such as a web worker or service worker).

In order to fully support modules, web browsers had to update each of these mechanisms. These details are defined in the HTML specification, and I'll summarize them in this section.

#### Using Modules With `<script>`

The default behavior of the `<script>` element is to load JavaScript files as scripts (not modules). This happens when the `type` attribute is missing or when the `type` attribute contains a JavaScript content type (such as `"text/javascript"`). The `<script>` element can then execute inline code or load the file specified in `src`. To support modules, the `"module"` value was added as a `type` option. Setting `type` to `"module"` tells the browser to load any inline code or code contained in the file specified by `src` as a module instead of a script. Here's a simple example:

```html
<!-- load a module JavaScript file -->
<script type="module" src="module.js"></script>

<!-- include a module inline -->
<script type="module">
  import { sum } from "./example.js";

  let result = sum(1, 2);
</script>
```

The first `<script>` element in this example loads an external module file using the `src` attribute. The only difference from loading a script is that `"module"` is given as the `type`. The second `<script>` element contains a module that is embedded directly in the web page. The variable `result` is not exposed globally because it exists only within the module (as defined by the `<script>` element) and is therefore not added to `window` as a property.

As you can see, including modules in web pages is fairly simple and similar to including scripts. However, there are some differences in how modules are loaded.

I> You may have noticed that `"module"` is not a content type like the `"text/javascript"` type. Module JavaScript files are served with the same content type as script JavaScript files, so it's not possible to differentiate solely based on content type. Also, browsers ignore `<script>` elements when the `type` is unrecognized, so browsers that don't support modules will automatically ignore the `<script type="module">` line, providing good backwards-compatibility.

#### Module Loading Sequence in Web Browsers

Modules are unique in that, unlike scripts, they may use `import` to specify that other files must be loaded to execute correctly. To support that functionality, `<script type="module">` always acts as if the `defer` attribute is applied.

The `defer` attribute is optional for loading script files but is always applied for loading module files. The module file begins downloading as soon as the HTML parser encounters `<script type="module">` with a `src` attribute but doesn't execute until after the document has been completely parsed. Modules are also executed in the order in which they appear in the HTML file. That means the first `<script type="module">` is always guaranteed to execute before the second, even if one module contains inline code instead of specifying `src`. For example:

```html
<!-- this will execute first -->
<script type="module" src="module1.js"></script>

<!-- this will execute second -->
<script type="module">
  import { sum } from "./example.js";

  let result = sum(1, 2);
</script>

<!-- this will execute third -->
<script type="module" src="module2.js"></script>
```

These three `<script>` elements execute in the order they are specified, so `module1.js` is guaranteed to execute before the inline module, and the inline module is guaranteed to execute before `module2.js`.

Each module may `import` from one or more other modules, which complicates matters. That's why modules are parsed completely first to identify all `import` statements. Each `import` statement then triggers a fetch (either from the network or from the cache), and no module is executed until all `import` resources have first been loaded and executed.

All modules, both those explicitly included using `<script type="module">` and those implicitly included using `import`, are loaded and executed in order. In the preceding example, the complete loading sequence is:

1. Download and parse `module1.js`.
1. Recursively download and parse `import` resources in `module1.js`.
1. Parse the inline module.
1. Recursively download and parse `import` resources in the inline module.
1. Download and parse `module2.js`.
1. Recursively download and parse `import` resources in `module2.js`

Once loading is complete, nothing is executed until after the document has been completely parsed. After document parsing completes, the following actions happen:

1. Recursively execute `import` resources for `module1.js`.
1. Execute `module1.js`.
1. Recursively execute `import` resources for the inline module.
1. Execute the inline module.
1. Recursively execute `import` resources for `module2.js`.
1. Execute `module2.js`.

Notice that the inline module acts like the other two modules except that the code doesn't have to be downloaded first. Otherwise, the sequence of loading `import` resources and executing modules is exactly the same.

I> The `defer` attribute is ignored on `<script type="module">` because it already behaves as if `defer` is applied.

#### Asynchronous Module Loading in Web Browsers

You may already be familiar with the `async` attribute on the `<script>` element. When used with scripts, `async` causes the script file to be executed as soon as the file is completely downloaded and parsed. The order of `async` scripts in the document doesn't affect the order in which the scripts are executed, though. The scripts are always executed as soon as they finish downloading without waiting for the containing document to finish parsing.

The `async` attribute can be applied to modules as well. Using `async` on `<script type="module">` causes the module to execute in a manner similar to a script. The only difference is that all `import` resources for the module are downloaded before the module itself is executed. That guarantees all resources the module needs to function will be downloaded before the module executes; you just can't guarantee _when_ the module will execute. Consider the following code:

```html
<!-- no guarantee which one of these will execute first -->
<script type="module" async src="module1.js"></script>
<script type="module" async src="module2.js"></script>
```

In this example, there are two module files loaded asynchronously. It's not possible to tell which module will execute first simply by looking at this code. If `module1.js` finishes downloading first (including all of its `import` resources), then it will execute first. If `module2.js` finishes downloading first, then that module will execute first instead.

#### Loading Modules as Workers

Workers, such as web workers and service workers, execute JavaScript code outside of the web page context. Creating a new worker involves creating a new instance `Worker` (or another class) and passing in the location of JavaScript file. The default loading mechanism is to load files as scripts, like this:

```js
// load script.js as a script
let worker = new Worker("script.js");
```

To support loading modules, the developers of the HTML standard added a second argument to these constructors. The second argument is an object with a `type` property with a default value of `"script"`. You can set `type` to `"module"` in order to load module files:

```js
// load module.js as a module
let worker = new Worker("module.js", { type: "module" });
```

This example loads `module.js` as a module instead of a script by passing a second argument with `"module"` as the `type` property's value. (The `type` property is meant to mimic how the `type` attribute of `<script>` differentiates modules and scripts.) The second argument is supported for all worker types in the browser.

Worker modules are generally the same as worker scripts, but there are a couple of exceptions. First, worker scripts are limited to being loaded from the same origin as the web page in which they are referenced, but worker modules aren't quite as limited. Although worker modules have the same default restriction, they can also load files that have appropriate Cross-Origin Resource Sharing (CORS) headers to allow access. Second, while a worker script can use the `self.importScripts()` method to load additional scripts into the worker, `self.importScripts()` always fails on worker modules because you should use `import` instead.

### Browser Module Specifier Resolution

All of the examples to this point in the chapter have used a relative module specifier path such as `"./example.js"`. Browsers require module specifiers to be in one of the following formats:

- Begin with `/` to resolve from the root directory
- Begin with `./` to resolve from the current directory
- Begin with `../` to resolve from the parent directory
- URL format

For example, suppose you have a module file located at `https://www.example.com/modules/module.js` that contains the following code:

```js
// imports from https://www.example.com/modules/example1.js
import { first } from "./example1.js";

// imports from https://www.example.com/example2.js
import { second } from "../example2.js";

// imports from https://www.example.com/example3.js
import { third } from "/example3.js";

// imports from https://www2.example.com/example4.js
import { fourth } from "https://www2.example.com/example4.js";
```

Each of the module specifiers in this example is valid for use in a browser, including the complete URL in the final line (you'd need to be sure `ww2.example.com` has properly configured its Cross-Origin Resource Sharing (CORS) headers to allow cross-domain loading). These are the only module specifier formats that browsers can resolve by default (though the not-yet-complete module loader specification will provide ways to resolve other formats). That means some normal looking module specifiers are actually invalid in browsers and will result in an error, such as:

```js
// invalid - doesn't begin with /, ./, or ../
import { first } from "example.js";

// invalid - doesn't begin with /, ./, or ../
import { second } from "example/index.js";
```

Each of these module specifiers cannot be loaded by the browser. The two module specifiers are in an invalid format (missing the correct beginning characters) even though both will work when used as the value of `src` in a `<script>` tag. This is an intentional difference in behavior between `<script>` and `import`.

## Summary

ECMAScript 6 adds modules to the language as a way to package up and encapsulate functionality. Modules behave differently than scripts, as they don't modify the global scope with their top-level variables, functions, and classes, and `this` is `undefined`. To achieve that behavior, modules are loaded using a different mode.

You must export any functionality you'd like to make available to consumers of a module. Variables, functions, and classes can all be exported, and there is also one default export allowed per module. After exporting, another module can import all or some of the exported names. These names act as if defined by `let` and operate as block bindings that can't be redeclared in the same module.

Modules need not export anything if they are manipulating something in the global scope. You can actually import from such a module without introducing any bindings into the module scope.

Because modules must run in a different mode, browsers introduced `<script type="module">` to signal that the source file or inline code should be executed as a module. Module files loaded with `<script type="module">` are loaded as if the `defer` attribute is applied to them. Modules are also executed in the order in which they appear in the containing document once the document is fully parsed.
