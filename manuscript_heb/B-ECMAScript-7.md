<div dir="rtl">

# נספח ב: להבין אקמהסקריפט 7 (2016)

לקח כ-4 שנים לפתח את אקמהסקריפט 6. ולאחר מכן
TC-39
הגיעה לכלל החלטה שתהליך פיתוח ארוך כל כך אינו בר קיימא. תחת זאת, הוחלט לעבור למחזור שחרור גרסאות שנתי כדי לאפשר לתכונות חדשות של השפה להיכנס לאפיון מוקדם יותר.

שחרורים בתדירות גבוהה יותר משמעותם שכל מהדורה חדשה של אקמהסקריפט תכיל פחות שינויים יחסית לאקמהסקריפט 6. כדי לתת משמעות לשינוי זה, גרסאות חדשות של השפה לא יציגו את מספר המהדורה ובמקום זאת יתייחסו לשנת הפרסום. כתוצאה מכך, אקמהסקריפט 6 ידועה גם בתור אקמהסקריפט 2015 ואקמהסקריפט 7 ידועה באופן רשמי בתור אקמהסקריפט 2016.
TC-39
מצפה להשתמש בשיטת השמות על בסיס השנה עבור כל גרסאות אקמהסקריפט העתידיות.

אקמהסקריפט 2016 פורסמה במרץ 2016 והכילה רק 3 תוספות לשפה:
אופרטור מתמטי חדש, מתודת מערך חדשה, ושגיאת תחביר חדשה.
נספח זה ירחיב על כך.

## האופרטור המעריכי

השינוי היחיד לתחביר בג׳אווהסקריפט שהוצג בגרסת אקמהסקריפט 2016 הינו
*האופרטור המעריכי*
(*exponentiation operator*),
שמבטא פעולה מתמטית שמגדילה בסיס במעריך.
בג׳אווהסקריפט כבר הייתה קיימת המתודה
<span dir="ltr">`Math.pow()`</span>
שביצעה הגדלה במעריך, אך ג׳אווהסקריפט הייתה אחת מהשפות הבודדות שדרשה מתודה לשם ביצוע הפעולה ולא היה לה אופרטור רשמי לכך
(וישנם מפתחים שטוענים שאופרטור הינו קל יותר לקריאה והבנה).

האופרטור המעריכי מורכב משת כוכביות
(`**`)
כאשר האופרנד השמאלי הינו הבסיס והאופרנד הימני הינו המעריך.
לדוגמה:

<div dir="ltr">

```js
let result = 5 ** 2;

console.log(result);                        // 25
console.log(result === Math.pow(5, 2));     // true
```

</div>

הדוגמה לעיל מחשבת
<span dir="ltr">5^2^</span>,
שערכו 25.
ניתן עדיין להשתמש במתודה
<span dir="ltr">`Math.pow()`</span>
על מנת להשיג את אותה התוצאה.

### סדר הפעולות

האופרטור המעריכי בעל העדיפות הגבוהה ביותר מכל האופרטורים הבינאריים בג׳אווהסקריפט
(לאופרטורים אונאריים יש עדיפות גבוהה יותר מאשר
`**`).
המשמעות היא שהוא מופעל קודם כל עבור כל פעולה מורכבת,
כמו בדוגמה הבאה:

<div dir="ltr">

```js
let result = 2 * 5 ** 2;
console.log(result);        // 50
```

</div>

ראשית כל נעשה החישוב עבור
<span dir="ltr">5^2^</span>.
התוצאה מוכפלת ב-2 ונקבל תוצאה סופית של 50.

### הגבלת אופרנדים

עבור האופרטור המעריכי קיימת מגבלה שאינה קיימת עבור אופרטורים אחרים. הצד השמאלי של האופרטור לא יכול להיות ביטוי אונארי מלבד
`++`
או
`--`.
הדוגמה הבאה מציגה תחביר לא תקני:


<div dir="ltr">

```js
// שגיאת תחביר
let result = -5 ** 2;
```

המספר
`-5`
בדוגמה זו נחשב לשגיאה תחבירית מכיוון שסדר הפעולות אינו ברור.
האם הסימן
`-`
מופעל רק עבור הערך
`5`
או עבור תוצאת הביטוי
<span dir="ltr">`5 ** 2`</span> ?
הטלת איסור על ביטויים אונאריים בצידו השמאלי של האופרטור מבטלת את חוסר הבהירות.
על מנת להבהיר את הכוונה, יש להוסיף סוגריים מסביב למספר
`-5`
או מסביב לביטוי
`5 ** 2`
כמו בדוגמה הבאה:

<div dir="ltr">

```js
// תקין
let result1 = -(5 ** 2);    // -25

// גם תקין
let result2 = (-5) ** 2;    // 25
```

</div>

אם נשים את הסוגריים מסביב לביטוי, הסימן
`-`
פועל על כל הביטוי.
כאשר הסוגריים מופיעים מסביב לערך
`-5`,
ברור שהכוונה היא להגדיל את
-5
בריבוע.

אין צורך להשתמש בסוגריים עבור האופרטורים
`++`
ו-
`--`
בצד השמאלי של האופרטור המעריכי מכיוון שלשני האופרטורים יש התנהגות מוגדרת היטב על האופרנדים שלהם. מקדם של
`++`
או
`--`
משנה את האופרנד לפני ביצוע כל פעולה אחרת והגרסה של האופרטורים שמופיעה מצד ימין של האופרנד לא מבצעת שינוי עד לאחר קריאת כל הביטוי כולו. שני הסוגים בטוחים לשימוש בצד השמאלי של האופרטור המעריכי, כפי שרואים בדוגמה הבאה:

<div dir="ltr">

```js
let num1 = 2,
    num2 = 2;

console.log(++num1 ** 2);       // 9
console.log(num1);              // 3

console.log(num2-- ** 2);       // 4
console.log(num2);              // 1
```

</div>

בדוגמה זו,
`num1`
מועלה בערכו לפני שמופעל האופרטור המעריכי,
לכן
`num1`
הופך למספר 3
ותוצאת האופרציה היא
9.
עבור
`num2`,
הערך נשאר 2 עבור פעולת האופרטור המעריכי ואז משתנה ערכה ל 1.


## The Array.prototype.includes() Method

You might recall that ECMAScript 6 added `String.prototype.includes()` in order to check whether certain substrings exist within a given string. Originally, ECMAScript 6 was also going to introduce an `Array.prototype.includes()` method to continue the trend of treating strings and arrays similarly. But the specification for `Array.prototype.includes()` was incomplete by the ECMAScript 6 deadline, and so `Array.prototype.includes()` ended up in ECMAScript 2016 instead.

### How to Use Array.prototype.includes()

The `Array.prototype.includes()` method accepts two arguments: the value to search for and an optional index from which to start the search. When the second argument is provided, `includes()` starts the match from that index. (The default starting index is `0`.) The return value is `true` if the value is found inside the array and `false` if not. For example:

```js
let values = [1, 2, 3];

console.log(values.includes(1));        // true
console.log(values.includes(0));        // false

// start the search from index 2
console.log(values.includes(1, 2));     // false
```

Here, calling `values.includes()` returns `true` for the value of `1` and `false` for the value of `0` because `0` isn't in the array. When the second argument is used to start the search at index 2 (which contains the value `3`), the `values.includes()` method returns `false` because the number `1` is not found between index 2 and the end of the array.

### Value Comparison

The value comparison performed by the `includes()` method uses the `===` operator with one exception: `NaN` is considered equal to `NaN` even though `NaN === NaN` evaluates to `false`. This is different than the behavior of the `indexOf()` method, which strictly uses `===` for comparison. To see the difference, consider this code:

```js
let values = [1, NaN, 2];

console.log(values.indexOf(NaN));       // -1
console.log(values.includes(NaN));      // true
```

The `values.indexOf()` method returns `-1` for `NaN` even though `NaN` is contained in the `values` array. On the other hand, `values.includes()` returns `true` for `NaN` because it uses a different value comparison operator.

W> When you want to check just for the existence of a value in an array and don't need to know the index , I recommend using `includes()` because of the difference in how `NaN` is treated by the `includes()` and `indexOf()` methods. If you do need to know where in the array a value exists, then you have to use the `indexOf()` method.

Another quirk of this implementation is that `+0` and `-0` are considered to be equal. In this case, the behavior of `indexOf()` and `includes()` is the same:

```js
let values = [1, +0, 2];

console.log(values.indexOf(-0));        // 1
console.log(values.includes(-0));       // true
```

Here, both `indexOf()` and `includes()` find `+0` when `-0` is passed because the two values are considered equal. Note that this is different than the behavior of the `Object.is()` method, which considers `+0` and `-0` to be different values.

## Change to Function-Scoped Strict Mode

When strict mode was introduced in ECMAScript 5, the language was quite a bit simpler than it became in ECMAScript 6. Despite that, ECMAScript 6 still allowed you to specify strict mode using the `"use strict"` directive either in the global scope (which would make all code run in strict mode) or in a function scope (so only the function would run in strict mode). The latter ended up being a problem in ECMAScript 6 due to the more complex ways that parameters could be defined, specifically, with destructuring and default parameter values. To understand the problem, consider the following code:

```js
function doSomething(first = this) {
    "use strict";

    return first;
}
```

Here, the named parameter `first` is assigned a default value of `this`. What would you expect the value of `first` to be? The ECMAScript 6 specification instructed JavaScript engines to treat the parameters as being run in strict mode in this case, so `this` should be equal to `undefined`. However, implementing parameters running in strict mode when `"use strict"` is present inside the function turned out to be quite difficult because parameter default values can be functions as well. This difficulty led to most JavaScript engines not implementing this feature (so `this` would be equal to the global object).

As a result of the implementation difficulty, ECMAScript 2016 makes it illegal to have a `"use strict"` directive inside of a function whose parameters are either destructured or have default values. Only *simple parameter lists*, those that don't contain destructuring or default values, are allowed when `"use strict"` is present in the body of a function. Here are some examples:

```js
// okay - using simple parameter list
function okay(first, second) {
    "use strict";

    return first;
}

// syntax error
function notOkay1(first, second=first) {
    "use strict";

    return first;
}

// syntax error
function notOkay2({ first, second }) {
    "use strict";

    return first;
}
```

You can still use `"use strict"` with simple parameter lists, which is why `okay()` works as you would expect (the same as it would in ECMAScript 5). The `notOkay1()` function is a syntax error because you can no longer use `"use strict"` in functions with default parameter values. Similarly, the `notOkay2()` function is a syntax error because you can't use `"use strict"` in a function with destructured parameters.

Overall, this change removes both a point of confusion for JavaScript developers and an implementation problem for JavaScript engines.
