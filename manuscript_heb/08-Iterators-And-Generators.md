<div dir="rtl">

# איטרטורים וגנרטורים

שפות תכנות רבות עברו מאיטרציה על נתונים באמצעות לולאות
שדורשות אתחול משתנים על מנת לעקוב אחר מיקום הפריטים באוסף, לשימוש באוביקטים מסוג איטרטור שמתוכנתים להחזיר את הפריט הבא ברשימה. איטרטורים מקלים על עבודה עם אוספים של מידע, ואקמהסקריפט 6
מוסיפה איטרטורים לג׳אווהסקריפט. בשילוב עם מתודות חדשות עבור מערך ואוספים מסוגים חדשים
(כמו סט ומפה),
איטרטורים חיוניים לעיבוד נתונים יעיל ומוצאים אותם בחלקים רבים של השפה. ראו לדוגמה את לולאת
`for-of`
שעובדת עם איטרטורים,
את אופרטור הפיזור
(`...`)
שמשתמש באיטרטורים,
ואיטרטורים גם מקלים על תכנות אסינכרוני.

הפרק הזה מרחיב על השימושים הרבים של איטרטורים, אך קודם, חשוב להבין את הסיבה מדוע הוסיפו איטרטורים לג׳אווהסקריפט.

## בעיית הלולאה

אם אי פעם כתבתם קוד בג׳אווהסקריפט, קרוב לוודאי שכתבתם קוד דומה לדוגמה הבאה:

<div dir="ltr">

```js
var colors = ["red", "green", "blue"];

for (var i = 0, len = colors.length; i < len; i++) {
    console.log(colors[i]);
}
```

</div>

לולאת 
`for`
הסטנדרטית עוקבת אחר האינדקס במערך
`colors`
באמצעות המשתנה
`i`.
ערכו של 
`i`
גדל כל פעם שהלולאה רצה כל עוד אינו גדול יותר מאורך המערך
(
    שנשמר במשתנה
    `len`
).

למרות שמדובר בלולאה פשוטה, לולאות גדלות במורכבותן כאשר משתמשים בלולאות פנימיות ונוצר צורך לעקוב אחר מספר משתנים. מורכבות גדולה יותר יכולה להוביל לשגיאות, והטבע השבלוני של לולאת
`for`
נוטה לייצר שגיאות נוספות כאשר קוד דומה נכתב במספר מקומות. איטרטורים נועדו לפתור בעיה זו.

## מהם איטרטורים?

איטרטורים הם אוביקטים אשר מממשים ממשק ספציפי שתוכנן עבור איטרציה. כל איטרטור מממש מתודת
<span dir="ltr">`next()`</span>
שמחזירה תוצאה שגם היא אוביקט.
לאוביקט התוצאה יש שתי תכונות:
`value`,
שמייצגת את הערך הבא, ותכונת
`done`, 
בעלת ערך בוליאני שערכו
`true`
כאשר אין עוד ערכים להחזיר. האיטרטור שומר על מצביע פנימי למקום בתוך אוסף הערכים ובכל קריאה למתודה
<span dir="ltr">`next()`</span>
יחזיר את הערך הבא.

אם קוראים למתודה
<span dir="ltr">`next()`</span>
לאחר שהערך האחרון כבר הוחזר, המתודה מחזירה תוצאה עם התכונה
`done`
בעלת ערך
`true`
והתכונה
`value`
מכילה את 
*ערך החזרה*
(*return value*)
.עבור האיטרטור
ערך החזרה אינו חלק מאוסף הנתונים, אלא יותר מעין חתיכה אחרונה של מידע קשור, או שיקבל את הערך 
`undefined`
כערך דיפולטיבי.
ערך החזרה של איטרטור דומה לערך החזרה של פונקציה בכך שהוא מהווה דרך להעביר מידע למי שקרא לאיטרטור.

בהתחשב באמור לעיל, יצירת איטרטור באקמהסקריפט 5 היא עניין פשוט:

<div dir="ltr">

```js
function createIterator(items) {

    var i = 0;

    return {
        next: function() {

            var done = (i >= items.length);
            var value = !done ? items[i++] : undefined;

            return {
                done: done,
                value: value
            };

        }
    };
}

var iterator = createIterator([1, 2, 3]);

console.log(iterator.next());           // "{ value: 1, done: false }"
console.log(iterator.next());           // "{ value: 2, done: false }"
console.log(iterator.next());           // "{ value: 3, done: false }"
console.log(iterator.next());           // "{ value: undefined, done: true }"

// for all further calls
console.log(iterator.next());           // "{ value: undefined, done: true }"
```
</div>

הפונקציה
<span dir="ltr">`createIterator()`</span>
מחזירה אוביקט עם מתודת
<span dir="ltr">`next()`</span>
כל פעם שהמתודה נקראת היא מחזירה את הערך הבא במערך
`items`
בתור ערך התכונה
`value`. 
כאשר
`i`
שווה ל-3,
`done`
מקבל את הערך
`true`
וערכו של 
`value`
משתנה לערך
`undefined`.
התוצאות הללו מקיימות את התנאי עבור איטרטורים באקמהסקריפט 6, לגבי הקריאה למתודה 
<span dir="ltr">`next()`</span>
על איטרטור לאחר שהערך האחרון באוסף הוחזר.

כפי שהדוגמה הקודמת ממחישה, כתיבת איטרטורים שמתנהגים בהתאם לכללים שהוגדרו באקמהסקריפט 6
יכולה להיות עניין מורכב.

למזלנו, אקמהסקריפט 6 מספקת לנו גנרטורים, שהופכים יצירת איטרטורים לעניין פשוט.

## מהם גנרטורים?

*גנרטור*
(*generator*) 
הוא פונקציה שמחזירה איטרטור. פונקציות מסוג גנרטור מסומנות בכוכב
(`*`)
לאחר המילה השמורה
`function`
ומשתמשות במילה השמורה
`yield`.
זה לא חשוב אם הכוכב מופיע מיד לאחר המילה
`function`
או אם יש רווח ביניהם, כפי שרואים בדוגמה הבאה:


<div dir="ltr">

```js
// גנרטור
function *createIterator() {
    yield 1;
    yield 2;
    yield 3;
}

// קוראים לגנרטור כמו פונקציה רגילה אבל מוחזר איטרטור
let iterator = createIterator();

console.log(iterator.next().value);     // 1
console.log(iterator.next().value);     // 2
console.log(iterator.next().value);     // 3
```
</div>

הסימון 
`*`
לפני
<span dir="ltr">`createIterator()`</span>
הופך את הפונקציה לגנרטור. המילה השמורה
`yield`
שגם היא תוספת חדשה לאקמהסקריפט 6, מייצגת ערכים שהאיטרטור יחזיר בעת הקריאה למתודה
<span dir="ltr">`next()`</span>,
לפי הסדר בו יש להחזיר אותם. לאיטרטור שנוצר בדוגמה זו יש שלושה ערכים שונים להחזיר בעת קריאה למתודה
<span dir="ltr">`next()`</span>:
תחילה
`1`,
ואז
`2`,
ולבסוף
`3`.
קוראים לגנרטור בדיוק כמו כל פונקציה אחרת, כפי שראינו כאשר נוצר המשתנה
`iterator`.

אחד ההיבטים המעניינים ביותר של גנרטורים הוא שהם משהים את הרצת הקוד לאחר כל פקודת
`yield`.
למשל, לאחר שהפקודה
`yield 1`
רצה, הפונקציה לא תמשיך לרוץ עד אשר המתודה 
<span dir="ltr">`next()`</span>
של האיטרטור תיקרא שוב.
בנקודה זו הפקודה
`yield 2`
תרוץ. יכולת זו להשהות את הרצת הקוד באמצע הפונקציה בעלת פוטנציאל גדול ומובילה למספר שימושים מעניינים לגנרטורים
(
    נדון בהם בסעיף
    ״יכולות איטרטור מתקדמות״
).

המילה השמורה
`yield` 
יכולה לשמש עבור כל ערך או ביטוי
(expression),
ולכן ניתן לכתוב גנרטורים שמוסיפים פריטים לאיטרטורים מבלי לציין את הפריטים אחד אחר השני. להלן דוגמה בה ניתן להשתמש ב
`yield` 
בתוך לולאת
`for`:


<div dir="ltr">

```js
function *createIterator(items) {
    for (let i = 0; i < items.length; i++) {
        yield items[i];
    }
}

let iterator = createIterator([1, 2, 3]);

console.log(iterator.next());           // "{ value: 1, done: false }"
console.log(iterator.next());           // "{ value: 2, done: false }"
console.log(iterator.next());           // "{ value: 3, done: false }"
console.log(iterator.next());           // "{ value: undefined, done: true }"

// עבור כל קריאה נוספת
console.log(iterator.next());           // "{ value: undefined, done: true }"
```
</div>

הדוגמה לעיל מעבירה מערך בשם
`items`
לגנרטור
<span dir="ltr">`createIterator()`</span>.
בתוך הפונקציה לולאת 
`for`
מעבירה פריטים מהמערך לאיטרטור ככל שהלולאה מתקדמת. כל פעם שהלולאה מגיעה לפקודת
`yield`
הלולאה נעצרת, וכל פעם שהמשתנה
`iterator`
קורא למתודה
<span dir="ltr">`next()`</span>.
הלולאה ממשיכה לפקודת 
`yield`
הבאה.

גנרטורים הם תוספת חשובה באקמהסקריפט 6, ומכיוון והם פונקציות לכל דבר, הם יכולים לעבוד באותם מקומות. יתר הפרק מתמקד בדרכים שימושיות אחרות לכתוב גנרטורים.


W> המילה השמורה
W> `yield`
W> יכולה לעבוד רק בתוך גנרטורים. שימוש ב
W> `yield`
W> בכל מקום אחר נחשב לשגיאה תחבירית, וזה תקף גם עבור פונקציות 
W> שמוגדרות בתוך גנרטור, כמו למשל:
W> 
W> <div dir="ltr">
W>
W> ```js
W> function *createIterator(items) {
W>
W>     items.forEach(function(item) {
W>
W>         // syntax error
W>         yield item + 1;
W>     });
W> }
W> ```
W> למרות שפקודת
W> `yield`
W> נמצאת בתוך גנרטור נזרקת שגיאת תחסיר מכיוון שפקודת 
W> `yield`
W> אינה יכולה לחצות גבולות פונקציה. מבחינה זו
W> `yield`
W> דומה לפקודת
W> `return`,
W> בכך שפונקציה פנימית אינה יכולה להחזיר ערך עבור הפונקציה 
W> החיצונית.

</div>

###  גנרטורים כביטוי קוד

ניתן ליצור גנרטור כביטוי רק על ידי כתיבת כוכב
(`*`)
בין המילה 
`function`
לבין הסוגריים הפותחים. לדוגמה:

<div dir="ltr">

```js
let createIterator = function *(items) {
    for (let i = 0; i < items.length; i++) {
        yield items[i];
    }
};

let iterator = createIterator([1, 2, 3]);

console.log(iterator.next());           // "{ value: 1, done: false }"
console.log(iterator.next());           // "{ value: 2, done: false }"
console.log(iterator.next());           // "{ value: 3, done: false }"
console.log(iterator.next());           // "{ value: undefined, done: true }"

// for all further calls
console.log(iterator.next());           // "{ value: undefined, done: true }"
```

</div>
 
בדוגמה לעיל, הפונקציה
<span dir="ltr">`createIterator()`</span>.
נחשבת לביטוי פונקציה 
(generator function expression)
ולא הגדרת פונקציה
(function declaration).
הכוכב מופיע בין המילה השמורה
`function`
לבין הסוגר הפותח מכיוון שביטוי הפונקציה הוא אנונימי. מלבד הבדל זה הדוגמה זהה לגרסה הקודמת של הפונקציה
<span dir="ltr">`createIterator()`</span>
שגם היא השתמשה בלולאת 
`for`.

I> לא ניתן ליצור פונקציה חץ שהיא גם גנרטור

### מתודות מסוג גנרטור

מכיוון שגנרטורים הם פונקציות, אפשר להוסיף אותם לאוביקטים. לדוגמה, אפשר ליצור גנרטור באוביקט ליטראל באקמהסקריפט 5 באמצעות ביטוי פונקציה
(function expression):

<div dir="ltr">

```js
var o = {

    createIterator: function *(items) {
        for (let i = 0; i < items.length; i++) {
            yield items[i];
        }
    }
};

let iterator = o.createIterator([1, 2, 3]);
```

</div>

אפשר להשתמש בתחביר המקוצר למתודה של אקמהסקריפט 6 באמצעות הוספת כוכב
(`*`)
לפני שם המתודה:

<div dir="ltr">

```js
var o = {

    *createIterator(items) {
        for (let i = 0; i < items.length; i++) {
            yield items[i];
        }
    }
};

let iterator = o.createIterator([1, 2, 3]);
```

</div>

הדוגמאות הנ״ל מתנהגות כמו הדוגמה בסעיף ״גנרטורים כביטוי קוד״. רק התחביר שונה. בגרסה המקוצרה, מפני שהמתודה
<span dir="ltr">`createIterator()`</span>
מוגדרת ללא המילה השמורה
`function`, 
הכוכב מופיע מיד לפני שם המתודה, למרות שניתן לשים רווחים בין הכוכב לבין שם המתודה.

## איטרבילים ו for-of

אוביקט בעל קשר הדוק לאיטרטור הוא אוביקט
*איטרבילי*
(*iterable*), 
אוביקט עם התכונה
`Symbol.iterator`.
הסימבול המובנה
`Symbol.iterator`
מפנה לפונקציה שמחזירה איטרטור עבור אוביקט נתון. כל האוביקטים שנחשבים לאוסף
(מערך, סט ומפה)
ומחרוזות הם איטרבילים באקמהסקריפט 6 ולכן מוגדר עבורם איטרטור מובנה. אוביקטים איטרבילים משמשים בתוספת החדשה של אקמהסקריפט:
לולאת
`for-of`.

I> כל האיטרטורים שנוצרים על ידי גנרטור הם איטרביליים, מפני שגנרטורים מגדירים את התכונה
`Symbol.iterator`
כברירת מחדל.

בתחילת הפרק, הוזכרה הבעיה של מעקב אחר אינדקס בתוך לולאת
`for`
איטרטורים הם הצעד הראשון לפתרון הבעיה. לולאת 
`for-of`
היא הצעד הבא: היא מייתרת את הצורך לעקוב אחר אינדקס בתוך אוסף, וכך מתאפשר לנו להתמקד רק בעבודה עם תוכן האוסף.

לולאת 
`for-of`
קוראת למתודה
<span dir="ltr">`next()`</span>
של איטרטור בכל איטרציה של הלולאה ושומרת את 
`value`
שמגיע מאוביקט התוצאה בתוך משתנה. הלולאה ממשיכה לעשות זאת עד אשר אוביקט התוצאה מחזיר את הערך
`true`
עבור התכונה
`done`.
לדוגמה:

<div dir="ltr">

```js
let values = [1, 2, 3];

for (let num of values) {
    console.log(num);
}
```

</div>

הקוד בדוגמה מייצר את הפלט הבא:

```
1
2
3
```

לולאת
`for-of`
תחילה קוראת למתודה 
`Symbol.iterator`
על המערך
`values`
כדי להשיג איטרטור
(
    הקריאה ל-
    `Symbol.iterator`
    מתרחשת מאחורי הקלעים על ידי מנוע הריצה עצמו
).
לאחר מכן קוראים ל-
<span dir="ltr">`iterator.next()`</span>
והתכונה
`value`
של אוביקט התוצאה מועברת לתוך המשתנה
`num`.
המשתנה 
`num`
מקבל תחילה את הערך 1,
לאחר מכן את הערך 2
ולבסוף את הערך 3. 
הלולאה מפסיקה לרוץ
כאשר התכונה 
`done`
על אוביקט התוצאה מקבלת את הערך
`true`,
ולכן
`num`
לא יקבל את הערך
`undefined`.

אם נרצה רק לעבור על פריטים בתוך מערך או אוסף, אז מומלץ להשתמש בלולאת
`for-of`
במקום לולאת 
`for`
לולאת 
`for-of`
פחות נוטה לבעיות מפני שיש פחות ערכים לבדוק. עדיף לשמור את לולאת
`for`
עבור מצבים יותר מורכבים.

W> לולאת 
`for-of`
תזרוק שגיאה כאשר היא מופעלת על אוביקט שאינו איטרבילי
`null`,
או 
`undefined`.

### גישה לאיטרטור הדיפולטיבי

ניתן להשתמש ב
`Symbol.iterator`
כדי לגשת לאיטרטור הדיפולטיבי של אוביקט, כמו בדוגמה הבאה:

<div dir="ltr">

```js
let values = [1, 2, 3];
let iterator = values[Symbol.iterator]();

console.log(iterator.next());           // "{ value: 1, done: false }"
console.log(iterator.next());           // "{ value: 2, done: false }"
console.log(iterator.next());           // "{ value: 3, done: false }"
console.log(iterator.next());           // "{ value: undefined, done: true }"
```

</div>

הקוד בדוגמה משיג את האיטרטור הדיפולטיבי עבור
`values`
ומשתמש בו כדי לעבור על הפריטים במערך. זהו אותו תהליך שמתרחש מאחורי הקלעים בעת שימוש בלולאת
`for-of`.

מכיוון ש 
`Symbol.iterator`
מגדיר את האיטרטור הדיפולטיבי בעבור אוביקט, ניתן להשתמש כדי לדעת האם אוביקט הוא איטרבילי כמו בדוגמה הבאה:

<div dir="ltr">

```js
function isIterable(object) {
    return typeof object[Symbol.iterator] === "function";
}

console.log(isIterable([1, 2, 3]));     // true
console.log(isIterable("Hello"));       // true
console.log(isIterable(new Map()));     // true
console.log(isIterable(new Set()));     // true
console.log(isIterable(new WeakMap())); // false
console.log(isIterable(new WeakSet())); // false
```

</div>

הפונקציה
<span dir="ltr">`isIterable()`</span>
בודקת האם קיים איטרטור דיפולטיבי על האוביקט והאם מדובר בפונקציה.
לולאת 
`for-of`
מבצעת פעולה דומה לפני ריצה.

עד עתה, הדוגמאות בחלק זה הציגו דרכים להשתמש ב 
`Symbol.iterator`
יחד עם אוביקטים איטרבילים מובנים, אך ניתן להשתמש ב 
`Symbol.iterator`
כדי ליצור אוביקטים איטרבילים משלכם.

### יצירת אוביקטים איטרבילים

אוביקטים חדשים שנוצרים על ידי מפתחים אינם איטרבילים כברירת מחדל, אך ניתן להפוך אותם לכאלה באמצעות הגדרת התכונה 
`Symbol.iterator`
שמצביעה על גנרטור. לדוגמה:

<div dir="ltr">

```js
let collection = {
    items: [],
    *[Symbol.iterator]() {
        for (let item of this.items) {
            yield item;
        }
    }

};

collection.items.push(1);
collection.items.push(2);
collection.items.push(3);

for (let x of collection) {
    console.log(x);
}
```

</div>

הקוד מייצר את הפלט הבא:

```
1
2
3
```

ראשית, הקוד בדוגמה מגדיר איטרטור דיפולטיבי עבור אוביקט בשם
`collection`.
האיטרטור הדיפולטיבי נוצר על ידי המתודה
`Symbol.iterator`
שהיא גנרטור
(שימו לב שהכוכב עדיין בא לפני שם הפונקציה).
הגנרטור משתמש בלולאת 
`for-of`
כדי לעבור על הערכים בתוך
`this.items`
ומשתמש ב 
`yield`
כדי להחזיר כל אחד מהם. במקום לבצע איטרציה ידנית עבור כל ערך שיוחזר, אוביקט
`collection`.
מסתמך על האיטרטור הדיפולטיבי של 
`this.items`
כדי לבצע את פעולתו.

I> החלק
״גנרטורים פנימיים״
בהמשך מתאר גישה שונה להשתמש באיטרטור של אוביקט אחר.

עברנו על מספר שימושים לאיטרטור המובנה של מערך, אך יש איטרטורים מובנים נוספים שקיימים באקמהסקריפט 6 ומקלים על עבודה עם אוספי מידע.

## איטרטורים מובנים

איטרטורים הינם חלק חשוב של אקמהסקריפט 6, ולכן איננו צריכים ליצור איטרטורים עבור סוגי נתונים מובנים רבים. השפה כוללת אותם באופן דיפולטיבי. צריך ליצור איטרטורים רק כאשר האיטרטורים המובנים לא נותנים מענה מספק. דבר שיקרה לרוב כאשר מגדירים אוביקטים או מחלקות משלנו. אחרת, ניתן להסתמך על איטרטורים מובנים. האיטרטורים הנפוצים ביותר בשימוש הם אלו שפועלים על אוספי מידע.

### איטרטורים עבור אוספי מידע

באקמהסקריפט 6 קיימים שלושה סוגים של אוביקטים שמייצגים אוספי מידע: מערך, סט ומפה. לכל השלושה יש את האיטרטורים המובנים הבאים שעוזרים לנו לנווט בתוך תוכן האוסף.

* <span dir="ltr">`entries()`</span> - מחזיר איטרטור שערכיו הם זוגות מזהה-ערך
* <span dir="ltr">`values()`</span> - מחזיר איטרטור שערכיו הם ערכי אוסף המידע
* <span dir="ltr">`keys()`</span> - מחזיר איטרטור שערכיו הם המזהים שבאוסף במידע

ניתן להחזיר איטרטור לאוסף מידע על ידי קריאה לאחת המתודות הללו.

#### <span dir="ltr">entries()</span>

האיטרטור עבור
<span dir="ltr">`entries()`</span>
מחזיר מערך בן שני פריטים כל פעם שקוראים ל 
<span dir="ltr">`next()`</span>.
המערך מייצג את המזהה ואת הערך עבור כל פריט באוסף במידע. עבור מערך, הפריט הראשון הינו האינדקס הנומרי. עבור סט, הפריט הראשון הוא גם הערך
(ערכים נחשבים גם למזהים בסט). 
עבור מפה הפריט הראשון הוא המזהה.

להלן מספר דוגמאות:

<div dir="ltr">

```js
let colors = [ "red", "green", "blue" ];
let tracking = new Set([1234, 5678, 9012]);
let data = new Map();

data.set("title", "Understanding ECMAScript 6");
data.set("format", "ebook");

for (let entry of colors.entries()) {
    console.log(entry);
}

for (let entry of tracking.entries()) {
    console.log(entry);
}

for (let entry of data.entries()) {
    console.log(entry);
}
```

</div>

הפלט ייראה כך:

```
[0, "red"]
[1, "green"]
[2, "blue"]
[1234, 1234]
[5678, 5678]
[9012, 9012]
["title", "Understanding ECMAScript 6"]
["format", "ebook"]
```

הדוגמה משתמשת במתודה
<span dir="ltr">`entries()`</span>
עבור כל אוסף מידע כדי להשיג איטרטור, והיא משתמשת בלולאת
`for-of`
כדי לעבור על הפריטים. הפלט מראה כיצד המזהים והערכים מוחזרים בזוגות עבור כל אוביקט.

#### <span dir="ltr">values()</span>

האיטרטור 
<span dir="ltr">`values()`</span>
מחזיר ערכים כפי שהם שמורים באוסף המידע. לדוגמה:

<div dir="ltr">

```js
let colors = [ "red", "green", "blue" ];
let tracking = new Set([1234, 5678, 9012]);
let data = new Map();

data.set("title", "Understanding ECMAScript 6");
data.set("format", "ebook");

for (let value of colors.values()) {
    console.log(value);
}

for (let value of tracking.values()) {
    console.log(value);
}

for (let value of data.values()) {
    console.log(value);
}
```

</div>

הקוד מייצר את הפלט הבא:

```
"red"
"green"
"blue"
1234
5678
9012
"Understanding ECMAScript 6"
"ebook"
```

קריאה לאיטרטור 
<span dir="ltr">`values()`</span>
מחזירה את המידע השמור בכל אוסף מידע מבלי צורך לדעת לגבי מיקום המידע באוסף המידע.

#### <span dir="ltr">keys()</span>

האיטרטור 
<span dir="ltr">`keys()`</span>
מחזיר כל מזהה שקיים באוסף המידע. עבור מערך הוא יחזיר אינדקס נומרי, ואף פעם לא יחזיר תכונות עצמיות 
(own properties)
של המערך. עבור סט המזהים מקבלים אותו הערך כמו הערכים המקושרים עימם ולכן
<span dir="ltr">`keys()`</span>
וגם 
<span dir="ltr">`values()`</span>
יחזירו
את אותו איטרטור. עבור מפה האיטרטור
<span dir="ltr">`keys()`</span>
מחזיר כל מזהה ייחודי. להלן דוגמה שממחישה את השימוש בשלושת הצורות:

<div dir="ltr">

```js
let colors = [ "red", "green", "blue" ];
let tracking = new Set([1234, 5678, 9012]);
let data = new Map();

data.set("title", "Understanding ECMAScript 6");
data.set("format", "ebook");

for (let key of colors.keys()) {
    console.log(key);
}

for (let key of tracking.keys()) {
    console.log(key);
}

for (let key of data.keys()) {
    console.log(key);
}
```

</div>

הדוגמה לעיל תייצר את הפלט הבא:


```
0
1
2
1234
5678
9012
"title"
"format"
```

האיטרטור
<span dir="ltr">`keys()`</span>
מחזיר כל מזהה במשתנים
`colors`, `tracking`, ו- `data`,
ואותם מזהים מודפסים בתוך שלושת לולאות
`for-of`.
עבור מערך מודפס האינדקס הנומרי, גם אם היינו מוסיפים תכונות בעלות שם ייחודי
(named properties).
זה שונה מהדרך שבה לולאת
`for-in`
עובדת עם מערך מכיוון שלולאת
`for-in`
סופרת תכונות נוספות מעבר לאינדקסים נומריים.

#### איטרטורים מובנים עבור אוספי מידע

לכל סוג אוסף מידע יש איטרטור מובנה דיפולטיבי שנקרא על ידי לולאת 
`for-of`
כברירת מחדל אלא אם צוין במפורש איטרטור אחר.
המתודה
<span dir="ltr">`values()`</span>
נותנת את האיטרטור הדיפולטיבי עבור מערך וסט, בעוד שהמתודה
<span dir="ltr">`entries()`</span>
מחזירה לנו את האיטרטור הדיפולטיבי עבור מפה. האיטרטורים הדיפולטיביים הללו מספקים איטרציה קלה יותר בלולאות 
`for-of`
ראו למשל את הדוגמה הבאה:

<div dir="ltr">

```js
let colors = [ "red", "green", "blue" ];
let tracking = new Set([1234, 5678, 9012]);
let data = new Map();

data.set("title", "Understanding ECMAScript 6");
data.set("format", "print");

// colors.values()
for (let value of colors) {
    console.log(value);
}

// tracking.values()
for (let num of tracking) {
    console.log(num);
}

// data.entries()
for (let entry of data) {
    console.log(entry);
}
```
</div>

לא מצוין במפורש איטרטור, ולכן האיטרטור הדיפולטיבי ישמש בקוד. האיטרטורים הדיפולטיביים עבור מערך, סט ומפה תוכננו לשקף את אופי האתחול שלהם, ולכן הפלט ייראה כך:

```
"red"
"green"
"blue"
1234
5678
9012
["title", "Understanding ECMAScript 6"]
["format", "print"]
```

מערך וסט מחזירים את הערכים שלהם כברירת מחדל, בעוד שמפות מחזירות את אותו מערך שמועבר לקונסטרטור
`Map`.
בניגוד לכך, סט חלש, כמו גם מפה חלשה, לא מקבלים איטרטור מובנה. ניהול מצביעים חלשים משמעותו שאין דרך לדעת כמה ערכים נמצאים באוסף המידע, ומכאן אין אפשרות לבצע איטרציה עליהם.

A> ### פעולת פירוק ולולאות for-of
A> 
A> התנהגות האיטרטור המובנה במפה עוזרת לנו כאשר משתמשים בו בלולאת
A> `for-of`
A> יחד עם פעולת פירוק, כמו בדוגמה הבאה:
A> 
A> <div dir="ltr">
A> 
A> ```js
A> let data = new Map();
A>
A> data.set("title", "Understanding ECMAScript 6");
A> data.set("format", "ebook");
A>
A> // data.entries()
A> for (let [key, value] of data) {
A>     console.log(key + "=" + value);
A> }
A> ```
A> 
A> לולאת
A> `for-of`
A> בקוד לעיל משתמש בפעולת פירוק על מערך עבור כל רשומה במפה. בצורה זו, ניתן לעבוד בקלות עם מזהים וערכים בעת ובעונה אחת מבלי A> הצורך לקרוא לתוך המערך בין שני הפריטים ומבלי לשלוף מתוך המפה את המזהה או הערך. שימוש בפירוק מערכים על הערך המוחזר מן מפה הופך את לולאת
A> `for-of`
A> לשימושית באותה המידע עבור מפה כשם שהיא שימושים בסט ומערך.

</div>

### איטרטורים עבור מחרוזת

מחרוזות בג׳אווהסקריפט הפכו עם הזמן ליותר ויותר דומות למערך מאז שחרור אקמהסקריפט 5. לדוגמה, אקמהסקריפט 5 הוסיפה שימוש בסוגריים מרובעים
(bracket notation)
לגישה ישירה לתווים בתוך מחרוזת
(
    כלומר, ניתן להשתמש בקוד
    `text[0]`
    בשביל לקרוא את התו הראשון במחרוזת, וכן הלאה
).
אך סוגריים מרובעים עובדים על יחידות קוד ולא על תווים, ולכן לא ניתן לגשת באמצעותם לתווים בעלי גודל כפול בצורה נכונה, כפי שרואים בדוגמה הבאה:


<div dir="ltr">

```js
var message = "A 𠮷 B";

for (let i=0; i < message.length; i++) {
    console.log(message[i]);
}
```
</div>

הקוד בדוגמה משתמש בסוגריים מרובעים ובתכונה
`length`
כדי לעבור על התווים במחרוזת ומדפיס את התו יוניקוד באותו מיקום. התוצאה אינה זו שציפינו לה:

```
A
(blank)
(blank)
(blank)
(blank)
B
```

מכיוון שהתו בגודל כפול נחשב בתור שתי יחידות קוד נפרדות ישנן ארבע שורות ריקות בין
`A`
ל-
`B`
בפלט.

למרבה המזל, אקמהסקריפט 6 מכוונת לתמיכה מלאה ביוניקוד
(ראה פרק 2)
והאיטרטור המובנה של מחרוזת מהווה ניסיון לפתור את בעיית האיטרציה הספציפית הזו. ולכן האיטרטור המובנה במחרוזת עובד על תווים ולא על יחידות קוד. שינוי הדוגמה לכזו שמשתמשת בלולאת
`for-of`
מייצר פלט תקין. להלן הקוד המתוקן:

<div dir="ltr">

```js
var message = "A 𠮷 B";

for (let c of message) {
    console.log(c);
}
```

</div>

הקוד לעיל מייצר את הפלט הבא:

```
A
(blank)
𠮷
(blank)
B
```

התוצאה היא כזו שהיינו מצפים לה כאשר עובדים עם תווים: הלולאה מדפיסה בהצלחה את התו הכפול בגודלו, כמו את יתר התווים.

### איטרטור NodeList

למודל אוביקט מסמך 
(DOM)
יש סוג נתונים בשם
`NodeList`
שמייצג אוסף של אלמנטים במסמך. עבור מפתחים שכותבים ג׳אווהסקריפט שאמור לרוץ בדפדפנים, הבנת ההבדל בין אוביקט מסוג
`NodeList`
.לבין מערך מאז ומתמיד הייתה עניין קשה יחסית
גם אוביקט
`NodeList`
וגם מערך משתמשים בתכונה בשם
`length`
כדי לייצג את מספר הפריטים באוסף, ושניהם משתמשים בכתיבת סוגרים מרובעים
(bracket notation)
כדי לגשת לפריטים בודדים. אך ברמת התנהגות פנימית יש הבדל משמעותי בין 
`NodeList`
לבין מערך, דבר שהוביל לבלבול בקרב מפתחים.

בנוסף להגדרת איטרטורים מובנים באקמהסקריפט 6 ההגדרה ב 

עבור אוביקט מסוג

(
    שמופיעה באפיון עבור
    HTML
    ולא באקמהסקריפט 6
)
כללה איטרטור מובנה שפועל באותה צורה כמו האיטרטור המובנה במערך. משמעות הדבר היא שניתן להשתמש באוביקט
`NodeList`
בתוך לולאת
`for-of`
או בכל מקום אחר שצורך את האיטרטור המובנה של אוביקט. לדוגמה:

<div dir="ltr">

```js
var divs = document.getElementsByTagName("div");

for (let div of divs) {
    console.log(div.id);
}
```

</div>

הקוד לעיל קורא למתודה
<span dir="ltr">`getElementsByTagName()`</span>
כדי לקבל אוביקט
`NodeList`
שמייצג את כל האלמנטים מסוג 
`<div>`
בתוך האוביקט
`document`
לולאת
`for-of`
עוברת על כל אלמנט ומדפיסה את תכונת
`for-of`
שלו, ובכך למעשה הופכת את הקוד זהה לקוד עבור מערך רגיל.

## אופרטור הפיזור ואוביקטים איטרביליים שאינם מסוג מערך

בפרק 7 ראינו כיצד אופרטור הפיזור
(`...`)
יכול לשמש כדי להפוך סט למערך. לדוגמה:

<div dir="ltr">

```js
let set = new Set([1, 2, 3, 3, 3, 4, 5]),
    array = [...set];

console.log(array);             // [1,2,3,4,5]
```

</div>

הקוד בדוגמה משתמש באופרטור הפיזור בתוך מערך ליטראלי כדי למלא את אותו מערך עם הערכים מתוך
`set`.
אופרטור הפיזור פועל על כל אוביקט איטרבילי ומשתמש באיטרטור המובנה כדי להחליט אילו ערכים לכלול. כל הערכים נקראים מתוך האיטרטור ומוכנסים למערך לפי הסדר שלפיו הוחזרו מהאיטרטור. הדוגמה לעיל עבדת כיוון שסט הינו איטרבילי, אבל הקוד יעבוד היטב עבור כל אוביקט איטרבילי אחר. ראו בדוגמה הבאה:

<div dir="ltr">

```js
let map = new Map([ ["name", "Nicholas"], ["age", 25]]),
    array = [...map];

console.log(array);         // [ ["name", "Nicholas"], ["age", 25]]
```

</div>

בדוגמה לעיל, אופרטור הפיזור ממיר את המשתנה בשם
`map`
למערך של מערכים. מכיוון שהאיטרטור המובנה של מפה מחזיר זוגות מזהה-ערך, התוצאה נראית כמו המערך שהועבר לקונסטרטור בעת הקריאה לקוד
<span dir="ltr">`new Map()`</span>.

ניתן להשתמש באופרטור הפיזור בתוך מערך ליטראלי ללא הגבלה, וניתן להשתמש בו גם בכל מקום שבו נרצה להכניס מספר פריטים למערך מתוך אוביקט איטרבילי. אותם פריטים יופיעו במערך החדש לפי הסדר בו הוחזרו, בנקודה בה הופעל אופרטור הפיזור. לדוגמה:


<div dir="ltr">

```js
let smallNumbers = [1, 2, 3],
    bigNumbers = [100, 101, 102],
    allNumbers = [0, ...smallNumbers, ...bigNumbers];

console.log(allNumbers.length);     // 7
console.log(allNumbers);    // [0, 1, 2, 3, 100, 101, 102]
```

</div>

אופרטור הפיזור משמש ליצירת המערך
`allNumbers` 
מתוך הערכים שבתוך
`smallNumbers`
ו-
`bigNumbers`.
הערכים מועברים לתוך
`allNumbers` 
לפי הסדר בו הוסיפו את המערכים הפנימיים בעת יצירת
`allNumbers`:
`0`
יהיה הפריט הראשון, לאחר מכן יבואו הערכים של
`smallNumbers`, 
ולאחר מכן הערכים מתוך
`bigNumbers`.
המערכים המקוריים לא עוברים שינוי, ערכיהם הועתקו לתוך
`allNumbers`.

מכיוון שאופרטור הפיזור יכול לעבוד עבור כל אוביקט איטרבילי, זוהי הדרך הקלה ביותר להפוך אוביקט שכזה למערך. ניתן להמיר מחרוזות לתוך מערך של תווים
(לא יחידות קוד)ת
כמו גם אוביקטים מסוג
`NodeList`
בדפדפן לתוך מערך של אוביקטים מסוג
`Node`.

כעת כשידוע לנו הבסיס לפעולת איטרטור, כולל לולאת 
`for-of`
ואופרטור הפיזור, ניתן לבחון שימושים מורכבים עבור איטרטורים.

## יכולות איטרטור מתקדמות

ניתן להשיג הרבה באמצעות ההתנהגות הבסיסית של איטרטורים והנוחות של יצירה שלהם בעזרת גנרטורים. אך איטרטורים עוצמתיים אף יותר כאשר משתמשים בהם לביצוע משימות שונות מאיטרציה פשוטה על אוסף של ערכים. במהלך פיתוח אקמהסריפט 6, רעיונות ותבניות כתיבת קוד בעלי אופי ייחודי יצאו לאור שעודדו את היוצרים להרחיב את הפונקציונליות הקיימת. חלק מאותן תוספות היו בקושי מורגשות, אך בשילוב של אחת בשניה יצרו תוצאות מעניינות ביותר. 

### העברת ארגומנטים לתוך איטרטורים

בפרק זה ראינו דוגמאות כיצד איטרטורים מעבירים ערכים החוצה בעזרת מתודת 
<span dir="ltr">`next()`</span>
או בעזרת הפקודה
`yield`
בתוך גנרטור.
אך ניתן גם להעביר ארגומנטים לתוך איטרטור על ידי שימוש במתודה
<span dir="ltr">`next()`</span>.
כאשר ארגומנט מועבר למתודה
<span dir="ltr">`next()`</span>
אותו ארגומנט הופך לערך שמוחזר מפקודת 
`yield`
בתוך הגנרטור. יכולת זו חשובה במיוחד עבור תכנות אסינכרוני.
להלן דוגמה בסיסית:

<div dir="ltr">

```js
function *createIterator() {
    let first = yield 1;
    let second = yield first + 2;       // 4 + 2
    yield second + 3;                   // 5 + 3
}

let iterator = createIterator();

console.log(iterator.next());           // "{ value: 1, done: false }"
console.log(iterator.next(4));          // "{ value: 6, done: false }"
console.log(iterator.next(5));          // "{ value: 8, done: false }"
console.log(iterator.next());           // "{ value: undefined, done: true }"
```

</div>

הקריאה הראשונה ל-
<span dir="ltr">`next()`</span>
נחשבת למקרה מיוחד שלא מתייחס לערך המועבר פנימה.  מכיוון שארגומנטים שמועברים לתוך
<span dir="ltr">`next()`</span>
הופכים לערכים שמוחזרים על ידי 
`yield`,
ארגומנט מהקריאה הראשונה ל
<span dir="ltr">`next()`</span>
יכול להחליף את פקודת 
`yield`
הראשונה בגנרטור רק אם היה ניתן לגשת אליה לפני פקודת
`yield`. 
זה לא אפשרי ולכן אין טעם להעביר ארגומנט בקריאה הראשונה של
<span dir="ltr">`next()`</span>.

בקריאה השניה של
<span dir="ltr">`next()`</span>,
הערך
`4`
מועבר כארגומנט.
אותו ערך מועבר למשתנה
`first`
בתוך הגנרטור. בפקודת 
`yield` 
שכוללת גם פעולת השמה, הצד הימני של הביטוי
רץ בקריאה הראשונה ל 
<span dir="ltr">`next()`</span>
והצד השמאלי רץ בקריאה השניה ל
<span dir="ltr">`next()`</span>.
מכיוון שהקריאה השניה ל
מעבירה פנימה את הערך
`4`,
זה הערך שמועבר למשתנה
`first`
ולאחר מכן הפונקציה ממשיכה לרוץ.

פקודת 
`yield` 
השניה משתמשת בתוצאת פקודת
`yield` 
הראשונה ומוסיפה `2` לתוצאה, משמע יוחזר הערך `6`. כאשר
<span dir="ltr">`next()`</span>
נקראת פעם שלישית, הערך 
`5`
מועבר כארגומנט. הערך הזה מועבר למשתנה
`second`
ונעשה בו שימוש בפקודת
`yield` 
השלישית על מנת להחזיר את הערך
`8`.

קל יותר לחשוב על מה שקורה כאשר מתייחסים לקוד שעובד כל פעם שהקוד רץ בתוך הגנרטור.
תרשים 8-1 משתמש בצבעים כדי להראות את הקוד שרץ לפני שמוחזר ערך.

![תרשים 8-1: הרצת קוד בתוך גנרטור](images/fg0601.png)

הצבע הצהוב מייצג את הקריאה הראשונה ל
<span dir="ltr">`next()`</span>
וכל הקוד שרץ בתוך הגנרטור כתוצאה מכך. הצבע הכחול מייצג את הקריאה 
<span dir="ltr">`next(4)`</span>
והקוד שרץ יחד עימה. הצבע הסגול מייצג את הקריאה 
<span dir="ltr">`next(5)`</span>
והקוד שרץ כתוצאה מכך. החלק הקשה הוא כיצד הקוד שבצד ימין של כל ביטוי רץ ונעצר לפני שהקוד בצידו השמאלי רץ. זה הופך פעולת פתרון שגיאות
(debugging)
על גנרטורים מורכבים מורכבת יותר מאשר בפונקציות רגילות.

עד כה ראינו כיצד פקודת 
`yield`
יכולה לפעול כמו פקודת
`return`
כאשר ערך מועבר למתודה
<span dir="ltr">`next()`</span>.
אך באפשרותך גם לגרום לאיטרטורים לזרוק שגיאה.

### זריקת שגיאות בתוך איטרטורים

מלבד נתונים ניתן להעביר לתוך איטרטורים מצבי שגיאה. איטרטורים יכולים לבחור ליישם מתודת
<span dir="ltr">`throw()`</span>.
שמורה לאיטרטור לזרוק שגיאה כאשר הוא ממשיך לרוץ. מדובר ביכולת חשובה מאוד לתכנות אסינכרוני שמאפשרת גמישות בתוך גנרטורים, כאשר נרצה לחקות התנהגות גם של החזרת ערך וגם של זריקת שגיאה
(שתי הדרכים לצאת מפונקציה). 
ניתן להעביר למתודה
<span dir="ltr">`throw()`</span>
אוביקט שגיאה שתיזרק כאשר האיטרטור יחזור להריץ קוד מחדש. לדוגמה:

<div dir="ltr">

```js
function *createIterator() {
    let first = yield 1;
    let second = yield first + 2;       // החזרת 4+2 ואז שגיאה
    yield second + 3;                   // הקוד לא ירוץ
}

let iterator = createIterator();

console.log(iterator.next());                   // "{ value: 1, done: false }"
console.log(iterator.next(4));                  // "{ value: 6, done: false }"
console.log(iterator.throw(new Error("Boom"))); // זורק שגיאה מהגנרטור
```
</div>

בדוגמה זו, שתי פקודות 
`yield`
הראשונות עובדות כצפוי, אך כאשר קוראים למתודה
<span dir="ltr">`throw()`</span>
נזרקת שגיאה  לפני שהקוד
`let second`
עובד.
זה למעשה עוצר את הקוד בדומה לזריקת שגיאה באופן ישיר. ההבדל היחיד הוא המיקום בו נזרקת השגיאה.
איור 8-2 מראה איזה קוד רץ בכל שלב.

![איור 8-2: זריקת שגיאה בתוך גנרטור](images/fg0602.png)

באיור זה, הצבע האדום מייצג את הקוד שרץ כאשר קוראים למתודה
<span dir="ltr">`throw()`</span>,
והכוכבית האדומה מראה בקירוב היכן נזרקת השגיאה מתוך הגנרטור. שתי פקודות 
`yield`
הראשונות פועלות כצפוי, וכאשר קוראים למתודה
<span dir="ltr">`throw()`</span>,
נזרקת שגיאה לפני שרץ קוד נוסף.

ניתן לתפוס שגיאות כאלו בתוך גנרטור בעזרת בלוק 
`try-catch`:

<div dir="ltr">

```js
function *createIterator() {
    let first = yield 1;
    let second;

    try {
        second = yield first + 2;       // החזרת 4+2 ואז שגיאה
    } catch (ex) {
        second = 6;                     // תפיסת שגיאה והשמה של ערך
    }
    yield second + 3;
}

let iterator = createIterator();

console.log(iterator.next());                   // "{ value: 1, done: false }"
console.log(iterator.next(4));                  // "{ value: 6, done: false }"
console.log(iterator.throw(new Error("Boom"))); // "{ value: 9, done: false }"
console.log(iterator.next());                   // "{ value: undefined, done: true }"
```

</div>

בדוגמה זו, בלוק של
`try-catch`
עוטף את הפקודה
`yield`
השניה.
אף על פי שפקודת 
`yield`
עצמה רצה ללא שגיאה, השגיאה נזרקת לפני שניתן לשים ערך למשתנה
`second`,
ולכן בלוק
`catch`
מבצע בו השמה של הערך 
`6`.
הקוד ממשיך לרוץ לפקודת
`yield`
הבאה ומחזיר את הערך
`9`.

שימו לב לדבר מעניין שקרה: מתודת
<span dir="ltr">`throw()`</span>
החזירה לנו אוביקט תוצאה ממש כמו מתודת
<span dir="ltr">`next()`</span>.
מכיוון שהשגיאה נתפסה בתוך הגנרטור, הקוד ממשיך לרוץ עד לפקודת
`yield`
הבאה ומחזיר את הערך הבא, 
`9`.

מומלץ לחשוב על מתודת 
<span dir="ltr">`next()`</span>
ו-
<span dir="ltr">`throw()`</span>
כעל הוראות עבור האיטרטור. המתודה
<span dir="ltr">`next()`</span>
מורה לאיטרטור להמשיך לרוץ
(ייתכן שאם ערך נתון)
ומתודת 
<span dir="ltr">`throw()`</span>
מורה לאיטרטור להמשיך לרוץ על ידי זריקת שגיאה. מה שקורה לאחר מכן תלוי בקוד שבתוך הגנרטור עצמו.

המתודות
<span dir="ltr">`next()`</span>
ו-
<span dir="ltr">`throw()`</span>
שולטות על הרצת הקוד בתוך איטרטור כאשר משתמשים בפקודת
`yield`,
אך ניתן להשתמש גם בפקודת
`return`.
פקודת 
`return`
עובדת בצורה שונה במקצת בתוך גנרטור, כפי שנראה בחלק הבא.

### פקודת החזרת ערך בגנרטור

מאחר וגנרטורים הם פונקציות ניתן להשתמש בפקודת
`return`
גם לסיים את הפונקציה מוקדם וגם כדי להחזיר ערך מהקריאה האחרונה למתודת
<span dir="ltr">`next()`</span>. 
ברוב הדוגמאות בפרק זה, הקריאה האחרונה של
<span dir="ltr">`next()`</span>
על איטרטור מחזירה את הערך
`undefined`,
אך ניתן לספק ערך חלופי על ידי שימוש בפקודה
`return`
כפי שניתן לעשות בפונקציה רגילה. בתוך גנרטור
`return`
משמעו שאין עוד קוד נוסף להריץ ולכן התכונה
`done`
מוגדרת עם הערך
`true`
והערך, במידה וסופק, הופך לערך התכונה
`value`
להלן דוגמה שפשוט מסיימת מוקדם את הגנרטור באמצעות הפקודה
`return`:

<div dir="ltr">

```js
function *createIterator() {
    yield 1;
    return;
    yield 2;
    yield 3;
}

let iterator = createIterator();

console.log(iterator.next());           // "{ value: 1, done: false }"
console.log(iterator.next());           // "{ value: undefined, done: true }"
```

</div>

בדוגמה לעיל, יש בגנרטור פקודת
`yield`
שלאחריה מופיעה פקודת
`return`.
פקודת
`return`
משמעותה שאין ערכים נוספים, ולכן יתר פקודות
`yield`
לא ירוצו
(לא ניתן להגיע אליהן).

ניתן לספק ערך שיופיע בתור התכונה
`value`
של אוביקט התוצאה. 
לדוגמה:

<div dir="ltr">

```js
function *createIterator() {
    yield 1;
    return 42;
}

let iterator = createIterator();

console.log(iterator.next());           // "{ value: 1, done: false }"
console.log(iterator.next());           // "{ value: 42, done: true }"
console.log(iterator.next());           // "{ value: undefined, done: true }"
```
</div>

בדוגמה זו, הערך
`42`
מוחזר בתור התכונה
`value`
בקריאה השניה למתודה
<span dir="ltr">`next()`</span>
(
זו גם הפעם הראשונה שהערך עבור התכונה
`done`
הוא
`true`
).
הקריאה השלישית עבור
<span dir="ltr">`next()`</span>
מחזירה אוביקט תוצאה שערך התכונה
`value`
הינו שוב
`undefined`.
כל ערך שנספק בפקודת
`return`
יופיע באוביקט התוצאה פעם אחת בלבד לפני שמאתחלים את תכונת
`value`
לערך
`undefined`.

I> אופרטור הפיזור ולולאת
`for-of`
מתעלמים מערכים שמגיעים מפקודת
return`
ברגע שהתכונה 
`done`
מקבלת את הערך
`true`,
הם מפסיקים את פעולתם מבלי לקרוא את הערך של תכונת
`value`.
ערכים שמוחזרים מאיטרטורים באמצעות פקודת
`return`
עוזרים בעיקר בפעולת דלגציה של גנרטורים.

### דלגציה של גנרטורים

במצבים מסוימים, נרצה לשלב את הערכים המגיעים משני איטרטורים נפרדים לתוך איטרטור אחד. גנרטורים יכולים להעביר את השליטה לאיטרטורים אחרים באמצעות כתיבת הפקודה
`yield`
עם כוכבית
(`*`).
בדומה לכללים עבור הגדרת גנרטור, אין זה משנה היכן מופיעה הכוכבית, כל עוד הכוכבית תימצא בין המילה
`yield`
לבין שם הגנרטור.
את פעולה זו של העברת השליטה לאיטרטורים מכנים דלגציה של גנרטורים.
להלן דוגמה:

<div dir="ltr">

```js
function *createNumberIterator() {
    yield 1;
    yield 2;
}

function *createColorIterator() {
    yield "red";
    yield "green";
}

function *createCombinedIterator() {
    yield *createNumberIterator();
    yield *createColorIterator();
    yield true;
}

var iterator = createCombinedIterator();

console.log(iterator.next());           // "{ value: 1, done: false }"
console.log(iterator.next());           // "{ value: 2, done: false }"
console.log(iterator.next());           // "{ value: "red", done: false }"
console.log(iterator.next());           // "{ value: "green", done: false }"
console.log(iterator.next());           // "{ value: true, done: false }"
console.log(iterator.next());           // "{ value: undefined, done: true }"
```

</div>

בדוגמה זו, הגנרטור
<span dir="ltr">`createCombinedIterator()`</span>
מעביר את השליטה תחילה לאיטרטור שמוחזר מהגנרטור
<span dir="ltr">`createNumberIterator()`</span>
ולאחר מכן לאיטרטור שמוחזר מהגנרטור
<span dir="ltr">`createColorIterator()`</span>.
האיטרטור שמוחזר מהגנרטור
<span dir="ltr">`createCombinedIterator()`</span>
נראה, כלפי חוץ, כאיטרטור שיצר את כל הערכים. כל קריאה למתודה
<span dir="ltr">`next()`</span>
מעבירה את השליטה לאיטרטור המתאים עד אשר האיטרטורים שנוצרו על ידי
<span dir="ltr">`createNumberIterator()`</span>
ו
<span dir="ltr">`createColorIterator()`</span>
סיימו עבודתם. במקרה זה פקודת
`yield`
האחרונה תחזיר את הערך
`true`.

דלגציה של גנרטורים מאפשרת לנו שימוש נוסף בערך חזרה של גנרטור. למעשה, זו הדרך הקלה ביותר לגשת לערכים אלו, והדבר יכול לשמש אותנו בביצוע פעולות מורכבות. לדוגמה:

<div dir="ltr">

```js
function *createNumberIterator() {
    yield 1;
    yield 2;
    return 3;
}

function *createRepeatingIterator(count) {
    for (let i=0; i < count; i++) {
        yield "repeat";
    }
}

function *createCombinedIterator() {
    let result = yield *createNumberIterator();
    yield *createRepeatingIterator(result);
}

var iterator = createCombinedIterator();

console.log(iterator.next());           // "{ value: 1, done: false }"
console.log(iterator.next());           // "{ value: 2, done: false }"
console.log(iterator.next());           // "{ value: "repeat", done: false }"
console.log(iterator.next());           // "{ value: "repeat", done: false }"
console.log(iterator.next());           // "{ value: "repeat", done: false }"
console.log(iterator.next());           // "{ value: undefined, done: true }"
```

</div>

בדוגמה זו, הגנרטור
<span dir="ltr">`createCombinedIterator()`</span>
מעביר את השליטה לגנרטור
<span dir="ltr">`createNumberIterator()`</span>
ומבצע השמה של ערך חזרה למשתנה
`result`.
מכיוון ש 
<span dir="ltr">`createNumberIterator()`</span>
משתמש בקוד
`return 3`,
הערך המוחזר הינו
`3`. 
המשתנה
`result`
מועבר אל תוך 
<span dir="ltr">`createRepeatingIterator()`</span>
בתור ארגומנט שקובע את מספר הפעמים שיוחזר אותו ערך מחרוזת
(בדוגמה למעלה, 3 פעמים).

שימו לב שהערך 
`3`
לא מוחזר מאף קריאה למתודה
<span dir="ltr">`next()`</span>.
במקרה שלנה, הוא קיים אך ורק בתוך הגנרטור 
<span dir="ltr">`createCombinedIterator()`</span>.
אך ניתן להוסיף ערכים על ידי פקודות

נוספות, כמו בדוגמה הבאה:
`yield`

<div dir="ltr">

```js
function *createNumberIterator() {
    yield 1;
    yield 2;
    return 3;
}

function *createRepeatingIterator(count) {
    for (let i=0; i < count; i++) {
        yield "repeat";
    }
}

function *createCombinedIterator() {
    let result = yield *createNumberIterator();
    yield result;
    yield *createRepeatingIterator(result);
}

var iterator = createCombinedIterator();

console.log(iterator.next());           // "{ value: 1, done: false }"
console.log(iterator.next());           // "{ value: 2, done: false }"
console.log(iterator.next());           // "{ value: 3, done: false }"
console.log(iterator.next());           // "{ value: "repeat", done: false }"
console.log(iterator.next());           // "{ value: "repeat", done: false }"
console.log(iterator.next());           // "{ value: "repeat", done: false }"
console.log(iterator.next());           // "{ value: undefined, done: true }"
```

</div>

בקוד למעלה, פקודת
`yield`
נוספת מוציאה את הערך המוחזר מתוך הגנרטור
<span dir="ltr">`createNumberIterator()`</span>.

דלגציה של גנרטורים בה משתמשים בערך חזרה היא פרדיגמה עוצמתית שפותחת בפנינו מספר אפשרויות מעניינות, בעיקר כאשר משתמשים בה בביצוע פעולות אסינכרוניות.

I> ניתן להשתמש בפקודת
`yield *`
על מחרוזות
(
    למשל,
    <span dir="ltr">`yield * "hello"`</span>
)
והאיטרטור המובנה של מחרוזת ישמש במקרה שכזה.

## ביצוע פעולות אסינכרוניות

חלק גדול מההתלהבות בעניין גנרטורים קשורה קשר ישיר לתכנות אסינכרוני. תכנות אסינכרוני בג׳אווהסקריפט נחשב לחרב פיפיות: משימות פשוטות קלות לביוצע באופן אסינכרוני, בעוד שמשימות מורכבות הופכות למסובכות בכל הקשור לארגון הקוד. מכיוון שגנרטורים מאפשרים לנו למעשה לעצור את הקוד באמצע ריצה, הם פותחים בפנינו אפשרויות רבות בכל הקשור לתכנות אסינכרוני.

הדרך המסורתית לבצע פעולות אסינכרוניות הייתה לקרוא לפונקציה אחת שמקבלת פונקציה נוספת בתור פונקציית קולבק. לדוגמה קריאת קובץ מתוך דיסק קשיח בסביבת
Node.js:

<div dir="ltr">

```js
let fs = require("fs");

fs.readFile("config.json", function(err, contents) {
    if (err) {
        throw err;
    }

    doSomethingWith(contents);
    console.log("Done");
});
```
</div>

המתודה
<span dir="ltr">`fs.readFile()`</span>
נקראת עם 2 ארגומנטים, שם הקובץ אותו רוצים לקרוא ופונקציית קולבק.
כאשר הפעולה מסתיימת קוראים לפונקציית הקולבק שבודקת אם יש שגיאה, ובמידה ואין כזו, מעבדת את התוצאה
`contents`. 
תהליך מסוג זה עובד כאשר יש מספר קטן ומוגבל של פעולות אסינכרוניות לביצוע, אך מסתבך כאשר צריך להכיל פונקציות קולבק אחת בתוך השניה או כאשר יש סדרה של פעולות אסינכרוניות. במצב כזה גנרטורים ופקודות
`yield`
שימושיים במיוחד.

### מריץ פעולות פשוט

מכיוון שפקודת
`yield`
מפסיקה את הרצת הקוד ומחכה לקריאה נוספת של מתודת 
<span dir="ltr">`next()`</span>
טרם הרצה נוספת, ניתן לממש פעולות אסינכרוניות מבלי הצורך לנהל פונקציות קולבק. בתור התחלה נזדקק לפונקציה שיכולה לקרוא לגנרטור ולהפעיל את האיטרטור שמוחזר ממנו. כמו בדוגמה הבאה:


<div dir="ltr">

```js
function run(taskDef) {

    // יצירת האיטרטור ושמירתו
    let task = taskDef();

    // התחלת ביצוע המשימות
    let result = task.next();

    // קריאות ריקורסיביות לערך הבא באיטרטור
    function step() {

        // בדיקה אם נותרו עוד פעולות
        if (!result.done) {
            result = task.next();
            step();
        }
    }

    // התחלת התהליך
    step();

}
```

</div>

הפונקציה
<span dir="ltr">`run()`</span>
מקבלת הגדרת פעולה
(פונקציית גנרטור)
כארגומנט. היא קוראת לגנרטור כדי ליצור איטרטור ושומרת אותו במשתנה
`task`.
המשתנה
`task`
נמצא מחוץ לפונקציה ולכן ניתן לגשת אליו מפונקציות אחרות. הסיבה לכך תוסבר בהמשך. הקריאה הראשונה של
<span dir="ltr">`next()`</span>
מפעילה את האיטרטור והתוצאה נשמרת עבור שימוש עתידי. הפונקציה
<span dir="ltr">`step()`</span>
בודקת אם הערך 
`result.done`
הוא
`false`
ואם אכן כך, קוראת למתודה
<span dir="ltr">`next()`</span>
לפני שהיא קוראת לעצמה באופן רקורסיבי. כל קריאה של
<span dir="ltr">`next()`</span>
שומרת את התוצאה במשתנה
`result`
שערכו תמיד מוגדר כך שיכיל את התוצאה האחרונה.
הקריאה הראשונה לפונקציה
<span dir="ltr">`step()`</span>
מתחילה את התהליך של בחינת הערך
`result.done`
על מנת לבדוק האם יש צורך בפעולות נוספות.

באמצעות מימוש זה של הפונקציה
<span dir="ltr">`run()`</span>
ניתן להריץ גנרטור שמכיל מספר פקודות
`yield`
כמו בדוגמה הבאה:

<div dir="ltr">

```js
run(function*() {
    console.log(1);
    yield;
    console.log(2);
    yield;
    console.log(3);
});
```

</div>

הדוגמה הזו מדפיסה שלושה מספרים לקונסולה, ומראה שכל הקריאות של
<span dir="ltr">`next()`</span>
אכן מתבצעות. אך אין זו פעולה בעלת שימוש רב. השלב הבא הוא העברת ערכים אל תוך האיטרטור ומחוצה ממנו.

### הרצת משימות עם נתונים

הדרך הקלה להעביר נתונים לתוך מריץ משימות היא להעביר את הערך שמגיע מפקודת
`yield`
לקריאה הבאה למתודה
<span dir="ltr">`next()`</span>
כדי לעשות זאת, עלינו להעביר רק את הערך
`result.value`,
כמו בדוגמה הבאה:

<div dir="ltr">

```js
function run(taskDef) {

    // יצירת האיטרטור ושמירתו
    let task = taskDef();

    // התחלת ביצוע המשימות
    let result = task.next();

    // קריאות ריקורסיביות לערך הבא באיטרטור
    function step() {

        // בדיקה אם נותרו עוד פעולות
        if (!result.done) {
            result = task.next(result.value);
            step();
        }
    }

    // התחלת התהליך
    step();

}
```
</div>

כעת שהערך 
`result.value`
מועבר למתודה
<span dir="ltr">`next()`</span>
בתור ארגומנט, ניתן להעביר נתונים בין קריאות
`yield`
שונות, כמו בדוגמה הבאה:

<div dir="ltr">

```js
run(function*() {
    let value = yield 1;
    console.log(value);         // 1

    value = yield value + 3;
    console.log(value);         // 4
});
```

</div>

הדוגמה הזו מדפיסה שני ערכים לקונסול: הערכים
`1`
ו-
`4`.
הערך
`1`
מגיע מפקודת
`yield 1`,
או אז הוא מועבר לתוך המשתנה
`value`.
הערך 
`4`
מחושב על ידי הוספת 3 למשתנה
`value` 
והעברת התוצאה בחזרה לתוך 
`value`. 
כעת שהנתונים עוברים בין קריאות, יש צורך בשינוי אחד קטן על מנת לאפשר קריאות אסינכרוניות.

### מריץ משימות אסינכרוני

הדוגמה הקודמת העבירה מידע סטטי בין קריאות
`yield`
אבל המתנה לסיום פעולה אסינכרונית שונה במקצת. מריץ המשימות צריך לדעת כאשר התקבלה פונקציית קולבק וכיצד הוא אמור להשתמש בה. מכיוון שפקודות
`yield`
מעבירות את ערכיהם לתוך מריץ המשימות, המשמעות היא שכל קריאה לפונקציה חייבת להחזיר ערך שיורה למריץ המשימות שמדובר בפעולה אסינכרונית שצריך לחכות לה.

להלן דוגמה לדרך אחת שבה ניתן להפנות לפעולה אסינכרונית:

<div dir="ltr">

```js
function fetchData() {
    return function(callback) {
        callback(null, "Hi!");
    };
}
```

</div>

למטרת דוגמה זו, כל פונקציה שלה יקרא מריץ המשימות תחזיר פונקציה שמריצה פונקצית קולבק. הפונקציה
<span dir="ltr">`fetchData()`</span>
מחזירה פונקציה שמקבלת פונקצית קולבק כארגומנט. כאשר הפונקציה שהוחזרה תיקרא המיא תריץ את פונקצית הקולבק עם נתון אחד
(
    המחרוזת
    `"Hi!"`
).
הארגומנט 
`callback`
צריך להגיע מתוך מריץ המשימות על מנת לוודא שהרצת פונקצית הקולבק תתאים לאופן פעולת האיטרטור הפנימי. אמנם הפונקציה
<span dir="ltr">`fetchData()`</span>
סינכרונית, ניתן להפוך אותה בקלות לאסינכרונית על ידי קריאה לקולבק עם שיהוי קל. לדוגמה:

<div dir="ltr">

```js
function fetchData() {
    return function(callback) {
        setTimeout(function() {
            callback(null, "Hi!");
        }, 50);
    };
}
```

</div>

גרסה זו של 
<span dir="ltr">`fetchData()`</span>
מפעילה שיהוי של 50 מילישניות לפני הקריאה לקולבק, ומראה שהטכניקה עובדת היטב עבור קוד סינכרוני או אסינכרוני. עלינו רק לוודא שכל פונקציה שתיקרא באמצעות
`yield`
פועלת באותו אופן

באמצעות הבנה טובה כיצד פונקציה יכולה לאותת שהיא פועלת באופן אסינכרוני, ניתן לשנות את מריץ המשימות כך שיתייחס לכך. כל פעם שהערך
`result.value`
הוא פונקציה, מריץ המשימות יריץ אותה. להלן הקוד המעודכן:

<div dir="ltr">

```js
function run(taskDef) {

    // יצירת האיטרטור ושמירתו
    let task = taskDef();

    // התחלת ביצוע המשימות
    let result = task.next();

    // קריאות ריקורסיביות לערך הבא באיטרטור
    function step() {

        // בדיקה אם נותרו עוד פעולות
        if (!result.done) {
            if (typeof result.value === "function") {
                result.value(function(err, data) {
                    if (err) {
                        result = task.throw(err);
                        return;
                    }

                    result = task.next(data);
                    step();
                });
            } else {
                result = task.next(result.value);
                step();
            }

        }
    }

    // התחלת התהליך
    step();

}
```
</div>

כאשר הערך
`result.value`
הוא פונקציה
(
    הדבר נבדק באמצעות האופרטור
    `===`
),
הפונקציה תיקרא עם פונקציית קולבק כארגומנט. פונקציית הקולבק פועלת לפי המוסכמה של 
Node.js
לפיה מעבירים שגיאה בתור הארגומנט הראשון
(`err`)
והתוצאה בתור הארגומנט השני.
אם קיים ערך עבור
`err`
משמע הייתה שגיאה ולכן תתבצע קריאה של
<span dir="ltr">`task.throw()`</span>
עם השגיאה כארגומנט במקום לקרוא ל 
<span dir="ltr">`task.next()`</span>
כמצופה במצב תקין, כך ששגיאה תיזרק במיקום המתאים.
במידה ואין שגיאה אזי 
`data`
מועבר לתוך 
<span dir="ltr">`task.next()`</span>
והתוצאה נשמרת. לאחר מכן מתבצעת קריאה לפונקציה
<span dir="ltr">`step()`</span>
כדי להמשיך את התהליך. כאשר
`result.value`
אינה פונקציה
הערך מועבר ישירות אל 
<span dir="ltr">`task.next()`</span>.

גרסה משופרת זו של מריץ המשימות מוכנה עבור כל משימה אסינכרונית. על מנת לקרוא נתונים מתוך קובץ בסביבת
Node.js,
יש לייצר מעטפת מסביב לפונקציה
<span dir="ltr">`fs.readFile()`</span>
שמחזירה פונקציה דומה לפונקציה
<span dir="ltr">`fetchData()`</span>
שראינו קודם.
לדוגמה:

<div dir="ltr">

```js
let fs = require("fs");

function readFile(filename) {
    return function(callback) {
        fs.readFile(filename, callback);
    };
}
```
</div>

הפונקציה
<span dir="ltr">`readFile()`</span>
מקבלת ארגומנט אחד, שם הקובץ, ומחזירה פונקציה שקוראת לפונקציית קולבק. פונקציית הקולבק מועברת כמות שהיא אל תוך 
<span dir="ltr">`fs.readFile()`</span>,
שבעצמה תריץ את פונקציית הקולבק כאשר תסתיים פעולתה. כעת ניתן להריץ משימה זו באמצעות
`yield`
כמו בדוגמה:

<div dir="ltr">

```js
run(function*() {
    let contents = yield readFile("config.json");
    doSomethingWith(contents);
    console.log("Done");
});
```
</div>

הדוגמה לעיל מבצעת פעולת
<span dir="ltr">`readFile()`</span>
אסינכרונית ללא שתופיע בקוד העיקרי פונקציית קולבק באופן נראה לעין.
מלבד הפקודה
`yield`,
הקוד נראה כמו קוד סינכרוני רגיל. כל עוד הפונקציות שפועלות בצורה אסינכרונית עובדות כולן באותו אופן, ניתן לכתוב קוד שנראה כמו קוד סינכרוני לכל דבר.

כמובן, ישנם חסרונות לטכניקה שהופיעה בדוגמאות הקודמות, בעיקר , לא ניתן להיות בטוחים שפונקציה שמחזירה פונקציה הינה אסינכרונית. לעת עתה, חשוב רק שתבינו את התאוריה מאחורי הרצת המשימות. שימוש בפרומיס מאפשר לנו שימוש בדרכים חדשות לניהול משימות אסינכרוניות, ופרק 11 מרחיב על נושא זה.

## סיכום

איטרטורים מהווים חלק חשוב של אקמהסקריפט 6 ועומדים מאחורי מספר היבטים חשובים של השפה עצמה. על פני השטח, איטרטורים מספקים דרך פשוטה להחזיר רצף של ערכים באמצעות 
ממשק תכנות
(API)
פשוט. אך בנוסף לכך קיימות אפשרויות מתקדמות לשימוש באיטרטורים באקמהסקריפט 6.

הסימבול
`Symbol.iterator`
משמש עבור הגדרת איטרטורים דיפולטיביים עבור אוביקטים. גם אוביקטים מובנים וגם כאלו שהוגדרו על ידי מפתחים יכולים להשתמש בסימבול הזה על מנת לייצר מתודה שמחזירה איטרטור.
כאשר קיימת תכונה
`Symbol.iterator`
על אוביקט, אזי אותו אוביקט נחשב איטרבילי.

לולאת
`for-of`
משתמשת באוביקטים איטרבילים כדי להחזיר סדרה של ערכים בלולאה. שימוש בלולאת
`for-of`
קל יותר מאשר איטרציה בלולאת
`for`
רגילה מפני שאין צורך לעקוב אחר אינגקס ולקבוע במפורש מתי הלולאה תפסיק. לולאת
`for-of`
קוראת באופן אוטומטי את כל הערכים מן האיטרטור עד אשר אין עוד ערכים, ואז היא מפסיקה לרוץ.

כדי להפוך את לולאת 
`for-of`
לקלה יותר לשימוש, ניתנו איטרטורים מובנים לערכים רבים באקמהסקריפט 6. כל סוגי אוספי המידע - מערך, מפה וסט - הינם בעלי איטרטורים מובנים שנועדו להפוך את תוכנם לנגיש יותר. גם למחרוזות יש איטרטור מובנה, שהופך איטרציה על כל התווים במחרוזת
(לאו דווקא יחידות קוד)
לקלה.

אופרטור הפיזור עובד עם כל אוביקט איטרבילי ומקל מאוד על המרת אוביקטים איטרבילים למערכים. ההמרה מתבצעת על ידי קריאת הערכים מתוך איטרטור והכנסתם למערך באופן פרטני.

גנרטור הוא סוג מיוחד של פונקציה שמייצרת איטרטור כאשר קוראים לה. הגדרת הגנרטור מתקיימת באמצעות כוכבית
(`*`)
ושימוש במילה שמורה
`yield`
כדי לציין אילו ערכים יחזרו בכל קריאה למתודה
<span dir="ltr">`next()`</span>

דלגציה של גנרטורים מעודדת כימוס טוב של התנהגות איטרטורים בזכות מתן האפשרות למחזר גנרטורים קיימים בתוך גנרטורים חדשים. ניתן להשתמש בגנרטור קיים בתוך אחד אחר על ידי קריאת
`yield *`
במקום
`yield`.
התהליך מאפשר לנו ליצור איטרטור שמחזיר ערכים ממספר איטרטורים.

מעל לכל, ההיבט המעניין והמסקרן ביותר של גנרטורים ואיטרטורים הינו היכולת והאפשרות של יצירת קוד אסינכרוני נקי למראה. במקום שימוש מוגזם בפונקציות קולבק, ניתן לכתוב קוד שנראה סינכרוני אך למעשה משתשמש בפקודת
`yield`
כדי להמתין לסיום פעולות אסינכרוניות עד להשלמתן.

</div>
