<div dir="rtl">

# נספח א: שינויים קטנים

יחד עם השינויים העיקריים שכוסו בספר זה, אקמהסקריפט 6 עשתה שינויים קטנים אך מועילים. השינויים הללו כוללים הפיכת ערכים מספריים לקלים יותר לשימוש, בעזרת הוספת צורות חישוב חדשות, שיפורים לעבודה עם קידוד טקסט מסוג יוניקוד והפיכת התכונה
`__proto__`
לתכונה פורמאלית. כל השינויים הללו מתוארים בנספח זה.

## עבודה עם ערכים מספריים

ג׳אווהסקריפט משתמש בשיטת הקידוד
<span dir="ltr">IEEE 754</span>
על מנת לייצג גם ערכים מספריים שלמים
(integers)
וגם מספרים צפים
(floats),
דבר שגרם לבלבול רב במהלך השנים. השפה הלכה למרחק גדול על מנת לוודא שמפתחים לא יצטרכו לדאוג לגבי הפרטים הקטנים של קידוד מספרים, אך בעיות עדיין צצות מדי פעם. אקמהסקריפט 6 מטפלת בכך על ידי הפיכת ערכים מספרים שלמים קלים יותר לזיהוי ושימוש.

### זיהוי ערכים מספריים שלמים

ראשית, אקמהסקריפט 6 הוסיפה את המתודה
<span dir="ltr">`Number.isInteger()`</span>
שמאפשרת לקבוע האם ערך כלשהו מייצג מספר שלם. למרות שג׳אווהסקריפט משתמשת בשיטת
<span dir="ltr">IEEE 754</span>
כדי לייצג שני סוגי המספרים, מספרים צפים ושלמים שמורים בצורה שונה.
המתודה
<span dir="ltr">`Number.isInteger()`</span>
מנצלת עובדה זו וכאשר היא נקראת היא בודקת את הייצוג הפנימי של אותו ערך על מנת לקבוע האם מדובר במספר שלם. המשמעות היא שמספרים שנראים כמו מספרים צפים יכולים להיות שמורים כמספר שלם ויגרמו למתודה
<span dir="ltr">`Number.isInteger()`</span>
להחזיר את הערך
`true`.
לדוגמה:

<div dir="ltr">

```js
console.log(Number.isInteger(25));      // true
console.log(Number.isInteger(25.0));    // true
console.log(Number.isInteger(25.1));    // false
```
</div>

בקוד לעיל,
<span dir="ltr">`Number.isInteger()`</span>
מחזירה את הערך

גם עבור
`25`
וגם עבור
`25.0`
למרות שהערך האחרון נראה כמו מספר צף. הוספת נקודה עשרונית לערך מספרי לא הופכת אותו למספר צף באופן אוטומטי בג׳אווהסקריפט.
לעומת זאת, הערך
`25.1`
נשמר כמספר צף כיוון שקיים אחרי הנקודה העשרונית.

### מספרים שלמים בטוחים לשימוש

שיטת הקידוד המספרי
<span dir="ltr">IEEE 754</span>
יכולה לייצג באופן מדוייק ערכים שלמים בטווח הערכים
-2^53^
עד
2^53^.
מעבר לטווח ״בטוח״ זה, ייצוג בינארי שונה משמש למספר מרובה של ערכים נומריים. משמעות הדבר היא שג׳אווהסקריפט יכולה לייצג ערכים שלמים בצורה מדוייקת בתוך טווח זה לפני שצצות בעיות.
לדוגמה:

<div dir="ltr">

```js
console.log(Math.pow(2, 53));      // 9007199254740992
console.log(Math.pow(2, 53) + 1);  // 9007199254740992
```

</div>

כפי שרואים בדוגמה לעיל, שני ערכים שונים מיוצגים על ידי אותו ערך מספרי שלם.
התופעה תופיע באופן תכוף יותר ככל שהערך המדובר רחוק יותר מן הטווה הבטוח.

אקמהסקריפט 6 הוסיפה את המתודה
<span dir="ltr">`Number.isSafeInteger()`</span>
כדי לזהות בצורה טובה יותר ערכים שלמים שהשפה מציגה בצורה מדויקת. היא גם הוסיפה את התכונות
<span dir="ltr">`Number.MAX_SAFE_INTEGER`</span>
ו-
<span dir="ltr">`Number.MIN_SAFE_INTEGER`</span>
כדי לייצג את הגבול העליון והתחתון, בהתאמה, של הטווח הבטוח.
המתודה
<span dir="ltr">`Number.isSafeInteger()`</span>
יכולה להבטיח לנו שערך הינו מספר שלם שנמצא בטווח הערכים השלמים הבטוח לשימוש, כמו בדוגמה הבאה:


<div dir="ltr">

```js
var inside = Number.MAX_SAFE_INTEGER,
    outside = inside + 1;

console.log(Number.isInteger(inside));          // true
console.log(Number.isSafeInteger(inside));      // true

console.log(Number.isInteger(outside));         // true
console.log(Number.isSafeInteger(outside));     // false
```
</div>

הערך
`inside`
הינו הערך המספרי השלם הבטוח הגדול ביותר, ולכן הוא מחזיר את הערך

עבור המתודות
<span dir="ltr">`Number.isInteger()`</span>
ו-
<span dir="ltr">`Number.isSafeInteger()`</span>.
המספר
`outside`
אינו נחשב לערך בטוח למרות שעדיין מדובר בערך מספרי שלם.

רוב הזמן, נרצה להשתמש בערכים בטוחים לשימוש בעת חישובים או השוואות בג׳אווהסקריפט, כך ששימוש במתודה
<span dir="ltr">`Number.isSafeInteger()`</span>.
כחלק מוולידציה של קלט מהמשתמש נחשב לרעיון טוב.

## מתודות מתמטיות חדשות

הדגש החדש על גיימינג וגרפיקה שהוביל את אקמהסקריפט 6 לכלול בתוכה מערכים בינאריים הוביל להבנה שמנוע הריצה של ג׳אווהסקריפט יכול לבצע חישובים מתמטיים רבים בצורה יעילה יותר. אך שיטות אופטימיזציה כמו
asm.js,
שפועלות על תת-קבוצה של ג׳אווהסקריפט על מנת לשפר ביצועים, זקוקות ליותר נתונים כדי לבצע חישובים באופן המהיר ביותר. למשל, הידיעה האם יש להתייחס לערכים נומריים בתור ערכים שלמים בני 32 ביט או בתור מספרים צפים בגודל 64 ביט חשובה עבור פעולות תלויות חומרה, שהן מהירות באופן משמעותי יותר מאשר פעולות על בסיס תוכנה.

כתוצאה מכך, אקמהסקריפט 6 הוסיפה מספר מתודות לאובייקט
`Math`
כדי לשפר את מהירות החישובים המתמטיים הנפוצים.
שיפור מהירות החישובים הנפוצים גם שיפרה את מהירות האפליקציות שמבצעות חישובים רבים, כמו תוכנות גרפיקה רבות. המתודות החדשות מופיעות בהמשך:

* `Math.acosh(x)` מחזיר קוסינוס היפרבולי הפוך עבור `x`.
* `Math.asinh(x)` מחזיר סינוס היפרבולי הפוך עבור `x`.
* `Math.atanh(x)` מחזיר טנגנס היפרבולי הפוך עבור `x`
* `Math.cbrt(x)` מחזיר שורש מעוקב עבור `x`.
* `Math.clz32(x)` מחזיר את מספר הביטים המובילים בעלי ערך אפס בייצוג 32 ביט עבור `x`.
* `Math.expm1(x)` מחזיר את התוצאה של חיסור 2 מהפונקציה האקספוננציאלית של `x`
* `Math.fround(x)` מחזיר את המספר הצף הקרוב ביותר עבור `x`.
* `Math.hypot(...values)` מחזיר את שורש סכום החזקות של כל הארגומנטים
* `Math.imul(x, y)` מחזיר את תוצאת מכפלת 32 ביט של שני הארגומנטים
* `Math.log1p(x)` מחזיר את הלוגריתם הטבעי של (`1 + x`).
* `Math.log10(x)` מחזיר את הלוגריתם על העשרוני של `x`.
* `Math.log2(x)` מחזיר את הלוגריתם על בסיס 2 של `x`.
* `Math.sign(x)` מחזיר -1 אם הארגומנט שלילי, 0 אם ערכו +0 או -0 , ואת הערך 1 אם הארגומנט חיובי
* `Math.cosh(x)` מחזיר קוסינוס היפרבולי עבור `x`.
* `Math.sinh(x)` מחזיר את הסינוס ההיפרבולי עבור `x`.
* `Math.tanh(x)` מחזיר טנגנס היפרבולי עבור `x`.
* `Math.trunc(x)` מסלק ספרות לאחר הנקודה הדצימלית ממספר צף ומחזיר מספר שלם.

הספר הזה לא ירחיב על כל מתודה חדשה שהוזכרה. אך במידה והאפליקציה שלכם צריכה לבצע חישובים מסוג נפוץ יחסית, חשוב לבדוק האם קיימת מתודה חדשה על האובייקט
`Math`
שעושה זאת בעבורכם לפני שמנסים לממש אותה בעצמכם.

## מזהי יוניקוד

אקמהסקריפט 6 מציעה לנו תמיכה טובה יותר ביוניקוד מאשר גרסאות קודמות והיא גם משנה את סוג התווים שיכולים לשמש בתור מזהים.
בגרסת אקמהסקריפט 5, היה ניתן להשתמש ברצף תווי בריחה מסוג יוניקוד בתור מזהים.
לדוגמה:


<div dir="ltr">

```js
// תקין בגרסת אקמהסקריפט 5 ו-6
var \u0061 = "abc";

console.log(\u0061);     // "abc"

// כמו לכתוב
console.log(a);          // "abc"
```
</div>

לאחר פקודת
`var`
בדוגמה זו,
ניתן להשתמש ברצף התווים
`\u0061`
או פשוט
`a`
על מנת לגשת למשתנה.
בגרסת אקמהסקריפט 6 ניתן להשתמש ברצף בריחה של נקודות קוד של יוניקוד בתור מזהים.
למשל:

<div dir="ltr">

```js
// תקין באקמהסקריפט 6
var \u{61} = "abc";

console.log(\u{61});      // "abc"

// כמו לכתוב
 console.log(a);          // "abc"
```
</div>

הדוגמה לעיל רק מחליפה את המזהה
`\u0061`
בנקודת הקוד המקבילה. חוץ מהבדל זה היא זהה לחלוטין לדוגמה הקודמת.

בנוסף לכך, אקמהסקריפט 6 מגדירה באופן רשמי מזהים תקינים לפי המונחים המצויים בדוקומנטציה של
<span dir="ltr">
[Unicode Standard Annex #31: Unicode Identifier and Pattern Syntax](http://unicode.org/reports/tr31/)
</span>,
שנותנת לנו את הכללים הבאים:

1. התו הראשון צריך להיות התו
`$`, `_`,
או כל תו יוניקוד בעל תכונת
`ID_Start`.
2. תו עוקב חייב להיות מהתווים
`$`, `_`, `\u200c`
(
    תו לא מחבר באורך אפסי
    <span dir="ltr">a zero-width non-joiner</span>
),
`\u200d`
(
    תו מחבר באורך אפסי
    <span dir="ltr">a zero-width joiner</span>
),
או כל תו יוניקוד אחר בעל תכונת
`ID_Continue`.

התכונות
`ID_Start`
ו-
`ID_Continue`
מוגדרות בדוקומנטציה האמורה לעיל בתור דרך לזהות תווים שמקובלים לשימוש כמזהים בתור משתנים ושמות דומיין.
ההגדרה המופיעה שם אינה בלעדית לג׳אווהסקריפט בלבד.

## Formalizing the `__proto__` Property

Even before ECMAScript 5 was finished, several JavaScript engines already implemented a custom property called `__proto__` that could be used to both get and set the `[[Prototype]]` property. Effectively, `__proto__` was an early precursor to both the `Object.getPrototypeOf()` and `Object.setPrototypeOf()` methods. Expecting all JavaScript engines to remove this property is unrealistic (there were popular JavaScript libraries making use of `__proto__`), so ECMAScript 6 also formalized the `__proto__` behavior. But the formalization appears in Appendix B of ECMA-262 along with this warning:

> These features are not considered part of the core ECMAScript language. Programmers should not use or assume the existence of these features and behaviours when writing new ECMAScript code. ECMAScript implementations are discouraged from implementing these features unless the
implementation is part of a web browser or is required to run the same legacy ECMAScript code that web browsers encounter.

The ECMAScript specification recommends using `Object.getPrototypeOf()` and `Object.setPrototypeOf()` instead because `__proto__` has the following characteristics:

1. You can only specify `__proto__` once in an object literal. If you specify two `__proto__` properties, then an error is thrown. This is the only object literal property with that restriction.
1. The computed form `["__proto__"]` acts like a regular property and doesn't set or return the current object's prototype. All rules related to object literal properties apply in this form, as opposed to the non-computed form, which has exceptions.

While you should avoid using the `__proto__` property, the way the specification defined it is interesting. In ECMAScript 6 engines, `Object.prototype.__proto__` is defined as an accessor property whose `get` method calls `Object.getPrototypeOf()` and whose `set` method calls the `Object.setPrototypeOf()` method. This leaves no real difference between using `__proto__` and `Object.getPrototypeOf()`/`Object.setPrototypeOf()`, except that `__proto__` allows you to set the prototype of an object literal directly. Here's how that works:

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
let friend = {
    __proto__: person
};
console.log(friend.getGreeting());                      // "Hello"
console.log(Object.getPrototypeOf(friend) === person);  // true
console.log(friend.__proto__ === person);               // true

// set prototype to dog
friend.__proto__ = dog;
console.log(friend.getGreeting());                      // "Woof"
console.log(friend.__proto__ === dog);                  // true
console.log(Object.getPrototypeOf(friend) === dog);     // true
```

Instead of calling `Object.create()` to make the `friend` object, this example creates a standard object literal that assigns a value to the `__proto__` property. When creating an object with the `Object.create()` method, on the other hand, you'd have to specify full property descriptors for any additional object properties.
