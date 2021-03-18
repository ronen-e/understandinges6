<div dir="rtl">

# סימבול ותכונותיו

סימבול הוא סוג ערך פרימיטיבי
(primitive type)
שהופיע בגרסת
ECMAScript 6,
והצטרף לערכים הפרימיטיביים הקיימים: מחרוזות, בוליאנים, מספרים,
`null`,
ו-
`undefined`.
סימבולים החלו בתור דרך ליצור איברים פרטיים בתוך אוביקט, יכולת שמפתחי ג׳אווהסקריפט רצו מזה זמן רב. לפני סימבולים, כל תכונה הייתה נגישה בקלות ויכולת ה״משתנים הפרטיים״ נועדה לאפשר למפתחים לייצר שמות תכונה שאינם מסוג מחרוזת.
בצורה זו, שיטות קיימות לזהות את השמות הפרטיים הללו לא יעבדו.

ההצעה ליצירת שמות פרטיים התפתחה לסימבול של
ECMAScript 6
ובפרק זה נלמד כיצד להשתמש בסימבולים. בעוד שצורת המימוש נותרה כשהייתה
(כלומר, הוסיפו ערך שאינו מחרוזת לתכונות אוביקטים)
השאיפה לאיברים פרטיים לא התקיימה. במקום זאת תכונות בעלות ערך סימבול משוייכות לקבוצה אחרת מזו של תכונות אחרות.

## יצירת סימבולים

לסימבול מעמד ייחודי בקרב ערכים פרימיטיביים של ג׳אווהסקריפט בכך שהם היחידים שאין להם צורת כתיבה ישירה כמו למשל
`true`
עבור ערך בוליאני
או
`42`
עבור מספרים. ניתן ליצור סימבול בעזרת הפונקציה הגלובלית
`Symbol`
כמו בדוגמה הבאה:

<div dir="ltr">

```js
let firstName = Symbol();
let person = {};

person[firstName] = "Nicholas";
console.log(person[firstName]);     // "Nicholas"
```

</div>

בדוגמה לעיל, הסימבול
`firstName`
נוצר ומשתמשים בו כדי לבצע השמה של תכונה חדשה על האוביקט
`person`.
חובה להשתמש בסימבול הנ״ל בכל פעם שרוצים לגשת לאותה תכונה. זה רעיון טוב לתת שם לסימבול, כדי לדעת מה הוא מייצג.


<!-- This is not entirely correct see issue #459 https://github.com/nzakas/understandinges6/issues/459 -->
W> מאחר וסימבולים הינם ערכים פרימיטיביים, קריאה ל
<span dir="ltr">`new Symbol()`</span>
זורקת שגיאה. ניתן ליצור אוביקט מסוג
`Symbol`
בעזרת
<span dir="ltr">`new Object(yourSymbol)`</span>
אבל לא ברור אם יש לכך שימוש.

הפונקציה
`Symbol`
מקבלת ארגומנט אופציונלי שמתאר את הסימבול. התיאור עצמו לא יכול לשמש על מנת לגשת לתכונה אך יכול לשמש למטרות פתרון שגיאות. לדוגמה:

<div dir="ltr">

```js
let firstName = Symbol("first name");
let person = {};

person[firstName] = "Nicholas";

console.log("first name" in person);        // false
console.log(person[firstName]);             // "Nicholas"
console.log(firstName);                     // "Symbol(first name)"
```

</div>

תיאור הסימבול נשמר בתכונה הפנימית
`[[Description]]`.
תכונה זו נקראת בכל פעם שמתודת
<span dir="ltr">`toString()`</span>
של הסימבול נקראת, באופן ישיר או עקיף.
בדוגמה לעיל, מתודת
<span dir="ltr">`toString()`</span>
של הסימבול
`firstName`
נקראת באופן עקיף על ידי
<span dir="ltr">`console.log()`</span>
והתיאור מודפס כפלט. אין דרך אחרת לגשת ל
`[[Description]]`
ישירות מהקוד. מומלץ תמיד לספק תיאור לסימבול בכדי להקל על קריאתו ועל פתרון שגיאות הקשורות בו

A> ### זיהוי סימבול
A>
A>מפני שסימבולים הינם ערכים פרימיטיביים ניתן להשתמש באופרטור
A>`typeof`
A>כדי לבדוק אם למשתנה יש ערך מסוג סימבול.
A>ECMAScript 6
A>מרחיבה את האופרטור
A>`typeof`
A>כך שיחזיר
A>`"symbol"`
A>כאשר הוא פועל על סימבול. לדוגמה:
A><div dir="ltr">
A>
A>```js
A>let symbol = Symbol("test symbol");
A>console.log(typeof symbol);         // "symbol"
A>```
A>
A></div>
A>
A>אמנם קיימות שיטות עקיפות לבדוק אם משתנה הוא מסוג סימבול, אך השימוש באופרטור
A>`typeof`
A>הינו ללא ספק השיטה הטובה ביותר.

## שימוש בסימבולים

ניתן להשתמש בסימבול בכל מקום שבו ניתן להשתמש בשם תכונה מחושב
<span dir="ltr">(computed property name)</span>.
כבר ראינו סימון בסוגריים
<span dir="ltr">(bracket notation)</span>
ביחד עם סימבולים בפרק זה אך ניתן להשתמש בסימבולים גם בתכונות אוביקטים מחושבות וגם בקריאה לפונקציות
<span dir="ltr">`Object.defineProperty()`</span>
ו-
<span dir="ltr">`Object.defineProperties()`</span>
כמו בדוגמה הבאה:

<div dir="ltr">

```js
let firstName = Symbol("first name");

// שימוש בשם תכונה מחושב
let person = {
    [firstName]: "ניקולאס"
};

// הפיכת התכונה לקריאה בלבד
Object.defineProperty(person, firstName, { writable: false });

let lastName = Symbol("last name");

Object.defineProperties(person, {
    [lastName]: {
        value: "זאקאס",
        writable: false
    }
});

console.log(person[firstName]);     // "ניקולאס"
console.log(person[lastName]);      // "זאקאס"
```

</div>

הדוגמה לעיל משתמש בשם תכונה מחושב כדי ליצור תכונה בעלת מזהה מסוג סימבול
`firstName`.
השורה הבאה מגדירה את התכונה לקריאה בלבד.
לאחר מכן מוגדרת תכונה מסוג סימבול
`lastName`
בעזרת המתודה
<span dir="ltr">`Object.defineProperties()`</span>.
תכונה מחושבת של אוביקט נוצרת שוב אך הפעם כחלק מהארגומנט השני בקריאה לפונקציה
<span dir="ltr">`Object.defineProperties()`</span>.

אמנם ניתן להגדיר סימבולים בכל פעם שניתן להשתמש בתכונות בעל שם מחושב, אך יש צורך במערכת שיתוף סימבולים באזורי קוד שונים על מנת להשתמש בהם בצורה אפקטיבית.

## שיתוף סימבולים

ייתכן ונרצה שאזורים שונים בקוד ישתמשו באותם סימבולים.
לדוגמה, נניח שקיימים שני אוביקטים שונים בקוד שמשתמשים באותה תכונה מסוג סימבול כדי לייצג מזהה ייחודי. מעקב אחר סימבולים על פני קבצים רבים יכול להיות קשה ורצוף שגיאות. בדיוק מסיבה זו גרסת
ECMAScript 6
מספקת מרשם סימבול גלובלי שניתן לגשת אליו בכל עת.

כדי לייצר סימבול משותף יש להשתמש במתודה
<span dir="ltr">`Symbol.for()`</span>.
המתודה
<span dir="ltr">`Symbol.for()`</span>.
מקבלת פרמטר יחיד שהוא מזהה מסוג מחרוזת עבור הסימבול שנרצה ליצור. אותו פרמטר משמש גם בתור תיאור הסימבול. לדוגמה:

<div dir="ltr">

```js
let uid = Symbol.for("uid");
let object = {};

object[uid] = "12345";

console.log(object[uid]);       // "12345"
console.log(uid);               // "Symbol(uid)"
```

</div>

המתודה
<span dir="ltr">`Symbol.for()`</span>
תחילה מחפשת בתוך מרשם הסימבול הגלובלי אחר המזהה
`"uid"`.
במידה ונמצא המזהה המבוקש המתודה מחזירה את הסימבול עבורו.
במידה ואין סימבול קיים אזי נוצר סימבול חדש ונרשם במרשם הגלובלי עם המפתח הנתון. הסימבול החדש מוחזר לאחר יצירתו. קריאות נוספות למתודה
<span dir="ltr">`Symbol.for()`</span>
עם אותו מפתח יחזירו את אותו סימבול, לדוגמה:

<div dir="ltr">

```js
let uid = Symbol.for("uid");
let object = {
    [uid]: "12345"
};

console.log(object[uid]);       // "12345"
console.log(uid);               // "Symbol(uid)"

let uid2 = Symbol.for("uid");

console.log(uid === uid2);      // true
console.log(object[uid2]);      // "12345"
console.log(uid2);              // "Symbol(uid)"
```

</div>

בדוגמה לעיל,
`uid`
ו-
`uid2`
מצביעים לאותו סימבול ולכן יכולים לשמש באופן זהה. הקריאה הראשונה למתודה
<span dir="ltr">`Symbol.for()`</span>
יוצרת את הסימבול והקריאה השנייה מחזירה את הסימבול מהמרשם הגלובלי.

היבט ייחודי נוסף של סימבולים משותפים הוא שניתן להחזיר את המזהה המקושר עם אותו סימבול במרשם הסימבול הגלובלי על ידי קריאת המתודה
<span dir="ltr">`Symbol.keyFor()`</span>.
לדוגמה:

<div dir="ltr">

```js
let uid = Symbol.for("uid");
console.log(Symbol.keyFor(uid));    // "uid"

let uid2 = Symbol.for("uid");
console.log(Symbol.keyFor(uid2));   // "uid"

let uid3 = Symbol("uid");
console.log(Symbol.keyFor(uid3));   // undefined
```

</div>

גם
`uid`
וגם
`uid2`
מחזירים את המזהה
`"uid"`.
הסימבול
`uid3`
אינו קיים במרשם הסימבולים הגלובלי, ולכן אין מזהה שמקושר איתו והקריאה למתודה
<span dir="ltr">`Symbol.keyFor()`</span>.
מחזירה את הערך
`undefined`.

W> מרשם הסימבולים הגלובלי נחשב לסביבה משותפת, בדיוק כמו הסביבה הגלובלית
(global scope).
לכן, לא ניתן להניח הנחות לגבי מה שקיים או לא באותה הסביבה.
יש להשתמש במרחב שמות עבור מזהה סימבולים
<span dir="ltr">(namespacing of symbol keys)</span>
כדי להפחית את הסיכוי להתנגשויות בשמות המזהים כאשר משתמשים ברכיבים חיצונים צד שלישי. לדוגמה, קוד של
jQuery
יכול להשתמש בקידומת
`"jquery."`
עבור כל המזהים שהוא יוצר, וכך נקבל מזהים כדוגמת
<span dir="ltr">`"jquery.element"`</span>.

## אילוץ ערך סימבול

אילוץ ערכים הינו חלק משמעותי בג׳אווהסקריפט, וקיימת מידה רבה של גמישות ביכולת השפה לשנות ערך מסוג אחד לסוג אחר. ואולם סימבול אינו גמיש במיוחד בכל הנוגע לאילוץ ערכים מכיוון שלערכים מסוג אחר אין מקבילה לסימבול. באופן ספציפי, לא ניתן לאלץ סימבול למחרוזת או מספר כך שלא ניתן להשתמש בהם בלי כוונה בתור תכונות רגילות כאשר מצפים מהם להתנהג כסימבולים.

הדוגמאות בפרק זה קוראות ל
<span dir="ltr">`console.log()`</span>
כדי להדפיס את הסימבול וזה עובד היטב כיוון ו
<span dir="ltr">`console.log()`</span>
קוראת ל
<span dir="ltr">`String()`</span>
על הסימבול כדי לייצר פלט בעל משמעות. ניתן להשתמש ב
<span dir="ltr">`String()`</span>
באופן ישיר כדי לקבל את אותה תוצאה. לדוגמה:

<div dir="ltr">

```js
let uid = Symbol.for("uid"),
    desc = String(uid);

console.log(desc);              // "Symbol(uid)"
```

</div>

הפונקציה
<span dir="ltr">`String()`</span>
קוראת ל
<span dir="ltr">`uid.toString()`</span>
ומחזירה את תיאור הסימבול. אם ננסה לשרשר את הסימבול ישירות עם מחרוזות תיזרק שגיאה.

<div dir="ltr">

```js
let uid = Symbol.for("uid"),
    desc = uid + "";            // שגיאה!
```

</div>

שרשור
`uid`
יחד עם מחרוזת ריקה מחייב לאלץ את
`uid`
לערך מסוג מחרוזת קודם לכן. שגיאה נזרקת כאשר אילוץ של הסימבול מתרחש וכך נמנעת הפעולה.

באופן דומה, לא ניתן לאלץ סימבול לערך מסוג מספר. כל האופרטורים המתמטיים גורמים לשגיאה כאשר הם פועלים על סימבול. לדוגמה:

<div dir="ltr">


```js
let uid = Symbol.for("uid"),
    sum = uid / 1;            // error!
```

</div>

הדוגמה לעיל מנסה לבצע פעולת חילוק של הסימול במכנה 1 , מה שגורם לשגיאה. השגיאה נזרקת ללא קשר לזהות האופרטור המתמטי
(
אופרטורים לוגים לא זורקים שגיאה כיוון שסימבול נחשב שווה לערך
`true`,
בדיוק כמו כל ערך רגיל שאינו ריק בג׳אווהסקריפט
).

## תכונות אוביקט מסוג סימבול

המתודות
<span dir="ltr">`Object.keys()`</span>
ו-
<span dir="ltr">`Object.getOwnPropertyNames()`</span>
מסוגלות למצוא את כל שמות התכונות באוביקט. המתודה הראשונה מחזירה את כל שמות התכונות הניתנות לספירה
(enumerable),
והאחרונה מחזירה את כל התכונות ללא קשר ליכולת הספירה שלהן. אף אחת מהן אינה מחזירה תכונות מסוג סימבול, וזאת כדי לשמור על צורת פעולתן מגרסת
ECMAScript 5
באופן זהה. במקום זאת המתודה
<span dir="ltr">`Object.getOwnPropertySymbols()`</span>
נוספה לגרסת
ECMAScript 6
כדי לאפשר לנו לקרוא תכונות מסוג סימבול מאוביקט

הערך המוחזר מן המתודה
<span dir="ltr">`Object.getOwnPropertySymbols()`</span>
הוא מערך של תכונות מסוג סימבול המשויכות לאוביקט
<span dir="ltr">(own property symbols)</span>.
לדוגמה:

<div dir="ltr">

```js
let uid = Symbol.for("uid");
let object = {
    [uid]: "12345"
};

let symbols = Object.getOwnPropertySymbols(object);

console.log(symbols.length);        // 1
console.log(symbols[0]);            // "Symbol(uid)"
console.log(object[symbols[0]]);    // "12345"
```

</div>

בקוד לעיל, לאוביקט
`object`
יש תכונה עם מפתח סימבול השם
`uid`.
המערך שמוחזר מן המתודה
<span dir="ltr">`Object.getOwnPropertySymbols()`</span>
הוא מערך שמכיל אך ורק את הסימבול הזה.

אוביקטים נוצרים ללא תכונות מסוג סימבול, אך יכולים לרשת
אותן מהפרוטוטיפ שלהם.
ECMAScript 6
מגדירה מספר תכונות כאלו שמיושמות באמצעות מה שידוע בשם סימבולים ידועים
(well-known symbols).

## חשיפת מנגנונים פנימיים באמצעות סימבולים ידועים

תכלית מרכזית בגרסת
ECMAScript 5
היתה חשיפת והגדרת חלק מאותם חלקים ״קסומים״ בג׳אווהסקריפט, אותם חלקים שמפתחים לא יכולים היו לחקות באותו זמן.
ECMAScript 6
ממשיכה באותו קו מחשבה על ידי חשיפה נרחבת יותר של מנגנונים פנימיים
של השפה, בעיקר על ידי שימוש בתכונות מסוג סימבול על מנת להגדיר התנהגות של אוביקטים מסוימים.

ECMAScript 6
הגדירה מספר סימבולים שנקראים
*סימבולים ידועים*
(*well-known symbols*)
אשר מייצגים התנהגות בג׳אווהסקריפט שקודם לכן הייתה פנימית לשפה ולא חשופה למפתחים. כל סימבול ידוע מיוצג על ידי תכונה על אוביקט
`Symbol`,
למשל
`Symbol.create`

הסימבולים הידועים הם:

* `Symbol.hasInstance` - מתודה שמשמשת את הפקודה
`instanceof`
כדי לבדוק ירושה של האוביקט.
* `Symbol.isConcatSpreadable` -  ערך בוליאני שלפיו יוחלט האם המתודה
<span dir="ltr">`Array.prototype.concat()`</span>
תשטח את האלמנטים בתוך הקבוצה כאשר הקבוצה מועברת בתור פרמטר אל
<span dir="ltr">`Array.prototype.concat()`</span>.

* `Symbol.iterator` - מתודה שמחזירה איטרטור.
(ראה פרק 8)

* `Symbol.match` - מתודה שמשמשת את
<span dir="ltr">`String.prototype.match()`</span>
על מנת לבצע השוואה בין מחרוזות

* `Symbol.replace` - מתודה שמשמשת את
<span dir="ltr">`String.prototype.replace()`</span>
להחליף בין מחרוזות פנימיות

* `Symbol.search` - מתודה שמשמשת את
<span dir="ltr">`String.prototype.search()`</span>
לאתר מחרוזות פנימיות

* `Symbol.species` - הקונסטרקור ליצירת אוביקטים שעוברים בירושה
(
<span dir="ltr">Derived objects</span>,
ראה פרק 9.
)

* `Symbol.split` - מתודה שמשמשת את
<span dir="ltr">`String.prototype.split()`</span>
לפיצול מחרוזות

* `Symbol.toPrimitive` - מתודה שמחזירה ייצוג של אוביקט כערך פשוט
* `Symbol.toStringTag` - מחרוזת שמשתמשת את
<span dir="ltr">`Object.prototype.toString()`</span>
כדי ליצור תיאור של אוביקט

* `Symbol.unscopables` - אוביקט שתכונותיו הן שמות של תכונות שלא ייכללו בפקודת
`with`

מספר סימבולים ידועים יוסברו בפרק זה בעוד שאחרות יוסברו בפרקים אחרים כדי לשמור אותם בתוך ההקשר המתאים.

I> דריסת מתודה שהוגדרה באמצעות סמל ידוע הופכת אוביקט רגיל לאוביקט אקזוטי, מכיוון שהדבר גורם לשינוי בהתנהגות פנימית. אין לכך השפעה מעשית על הקוד, זה פשוט משנה את הצורה שבה השפה מתייחסת לאוביקט.

### Symbol.hasInstance

לכל פונקציה יש מתודה עם המזהה
`Symbol.hasInstance`
שקובעת האם אוביקט מסוים הוא מופע של אותה פונקציה. המתודה מוגדרת על
`Function.prototype`
ולכן כל הפונקציות יורשות את ההתנהגות הדיפולטיבית עבור התכונה
`instanceof`
והמתודה אינה ניתנת לכתיבה, שינוי ומניה,
(nonwritable, nonconfigurable, nonenumerable)
כדי לוודא שלא תידרס בטעות על ידי קוד אחר.

המתודה
`Symbol.hasInstance`
מקבלת ארגומנט אחד: הערך שאותו יש לבדוק. היא מחזירה
`true`
אם הערך נחשב למופע של הפונקציה.
כדי להבין איך
`Symbol.hasInstance`
עובדת, מוצג הקוד בדוגמה הבאה:

<div dir="ltr">

```js
obj instanceof Array;
```

</div>

הקוד זהה למעשה ל:

<div dir="ltr">

```js
Array[Symbol.hasInstance](obj);
```

</div>

ECMAScript 6
למעשה מגדירה מחדש את האופרטור
`instanceof`
בתור תחביר מקוצר לקריאת המתודה הזו. וכעת כאשר מדובר בקריאה לפונקציה ניתן לשנות את הדרך שבה האופרטור
`instanceof`
עובד.

נניח שהיינו רוצים להגדיר פונקציה שאף אוביקט אינו נחשב למופע שלה. ניתן לעשות זאת על ידי שינוי הערך המוחזר מן
`Symbol.hasInstance`
לערך
`false`.
לדוגמה:

<div dir="ltr">

```js
function MyObject() {
    // ...
}

Object.defineProperty(MyObject, Symbol.hasInstance, {
    value: function(v) {
        return false;
    }
});

let obj = new MyObject();

console.log(obj instanceof MyObject);       // false
```

</div>

חייבים להשתמש במתודה
<span dir="ltr">`Object.defineProperty()`</span>
כדי לכתוב לתכונה שהוגדרה לקריאה בלבד, ולכן הדוגמה משתמשת במתודה זו כדי לשכתב את המתודה
`Symbol.hasInstance`
ולהחליפה בפונקציה חדשה. הפונקציה החדשה תמיד מחזירה את הערך
`false`,
כך שגם אם
`obj`
הינו למעשה מופע של
`MyObject`
האופרטור
`instanceof`
יחזיר
`false`.

כמובן, ניתן גם לבדוק את הערך והחליט האם מדובר במופע של קונסטרקטור מסוים על סמך תנאי מסוים. לדוגמה ייתכן כי מספרים בעלי ערך בין 1 עד 100 נחשבים למופע של סוג מסוים. כדי לממש התנהגות זו תוכל לכתוב קוד דומה לזה שבדוגמה הבאה:


<div dir="ltr">

```js
function SpecialNumber() {
    // empty
}

Object.defineProperty(SpecialNumber, Symbol.hasInstance, {
    value: function(v) {
        return (v instanceof Number) && (v >=1 && v <= 100);
    }
});

let two = new Number(2),
    zero = new Number(0);

console.log(two instanceof SpecialNumber);    // true
console.log(zero instanceof SpecialNumber);   // false
```

</div>

הקוד בדוגמא מגדיר מתודה
`Symbol.hasInstance`
שמחזירה
`true`
אם הערך הוא מופע של
`Number`
וגם נמצא בין 1 ל 100.
לכן
`SpecialNumber`
יתייחס למשתנה
`two`
כאל מופע שלו למרות שאין קשר ישיר בין הפונקציה
`SpecialNumber`
והמשתנה
`two`.
שימו לב שהאופרנד השמאלי לאופרטור
`instanceof`
חייב להיות אוביקט על מנת להפעיל את הקריאה למתודה
`Symbol.hasInstance`,
מכיוון שמשתנה שאינו אוביקט יגרום לאופרטור
`instanceof`
להחזיר
`false`
באופן קבוע.

W> ניתן לשנות את ההתנהגות הדיפולטיבית של
`Symbol.hasInstance`
עבור כל הפונקציות המוגדרות מראש כמו למשל
`Date`
ו-
`Error`.
הדבר אינו מומלץ מכיוון וההשפעה על הקוד יכול להיות בלתי צפויה ומבלבלת. מומלץ לדרוס את המתודה
`Symbol.hasInstance`
על פונקציות שהמפתח כתב בעצמו וגם אז רק כאשר יש בכך צורך ממשי.

### Symbol.isConcatSpreadable

למערכים בג׳אווהסקריפט יש מתודת
<span dir="ltr">`concat()`</span>
שנועדה לחבר שני מערכים יחד. להלן דוגמה לשימוש בה:

<div dir="ltr">

```js
let colors1 = [ "red", "green" ],
    colors2 = colors1.concat([ "blue", "black" ]);

console.log(colors2.length);    // 4
console.log(colors2);           // ["red","green","blue","black"]
```

</div>

הקוד בדוגמה מחבר מערך חדש לסוף המערך בשם
`colors1`
ויוצר את המערך
`colors2`,
מערך עם כל האלמנטים משני המערכים.
המתודה
<span dir="ltr">`concat()`</span>
יכולה לקבל ארגומנטים שאינם מערכים ובמקרה שכזה, הם יתווספו כמות שהם לסוף המערך. לדוגמה:

<div dir="ltr">

```js
let colors1 = [ "red", "green" ],
    colors2 = colors1.concat([ "blue", "black" ], "brown");

console.log(colors2.length);    // 5
console.log(colors2);           // ["red","green","blue","black","brown"]
```

</div>

בדוגמה זו הארגומנט
`"brown"`
מועבר אל
<span dir="ltr">`concat()`</span>
והופך לאלמנט החמישי במערך
`colors2`.
מדוע ארגומנט שהוא מערך מקבל יחס שונה מזה של ארגמונט מסוג
מחרוזת?
האפיון של השפה קובע שמערכים מפוצלים לאלמנטים בודדים בעוד שסוגים אחרים לא. עד לגרסת
ECMAScript 6,
לא הייתה דרך לשנות התנהגות זו.

התכונה
`Symbol.isConcatSpreadable`
הינה ערך בוליאני שמייצג כי לאוביקט יש תכונה בשם
`length`
ותכונות שהמזהה שלהן הינו מספר ושערכי אותן תכונות צריכים להתווסף בנפרד כל אחת לתוצאת הקריאה למתודה
<span dir="ltr">`concat()`</span>.
בניגוד לסימבולים ידועים אחרים התכונה הזו אינה מופיעה על אוביקט רגיל בעת יצירתו. במקום זאת הסימבול קיים כדרך לשנות את הצורה בה
<span dir="ltr">`concat()`</span>
עובדת על אוביקטים מסוימים, ולמעשה דורסת את ההתנהגות הדיפולטיבית.
ניתן להגדיר עבור כל סוג משתנה כך שינהג כמו מערך בעת קריאה למתודה
<span dir="ltr">`concat()`</span>.
כמו בדוגמה הבאה:

<div dir="ltr">

```js
let collection = {
    0: "Hello",
    1: "world",
    length: 2,
    [Symbol.isConcatSpreadable]: true
};

let messages = [ "Hi" ].concat(collection);

console.log(messages.length);    // 3
console.log(messages);           // ["Hi","Hello","world"]
```

</div>

האוביקט
`collection`
בדוגמה לעיל נראה כמו מערך: יש לו תכונה בשם
`length`
ושני מזהים נומריים. התכונה
`Symbol.isConcatSpreadable`
מוגדרת עם הערך
`true`
כדי להבטיח שערכים יתווספו בתור אלמנטים נפרדים למערך. כאשר
`collection`
מועבר כארגומנט למתודה
<span dir="ltr">`concat()`</span>,
המערך החדש מכיל את הערכים
`"Hello"`
ו-
`"world"`
כערכים נפרדים לאחר הערך
`"Hi"`

I> אפשר גם להגדיר את
`Symbol.isConcatSpreadable`
עם הערך
`false`
עבור תת מחלקות של מערך כדי למנוע מאלמנטים להיות מופרדים על ידי קריאה למתודה
<span dir="ltr">`concat()`</span>.
תת מחלקות מופיעות בפרק 8.

### Symbol.match, Symbol.replace, Symbol.search, Symbol.split

מחרוזות וביטויים רגולריים תמיד היו קרובים זה לזה בג׳אווהסקריפט. עבור מחרוזת יש מספר מתודות שמקבלות ביטוי רגולרי בתור ארגומנטים

* `match(regex)` - קובעת מתי מחרוזות תואמת לביטוי רגולרי
* `replace(regex, replacement)` - מחליפה התאמות לביטוי רגולרי עם
`replacement`
* `search(regex)` - מאתרת התאמה לביטוי רגולרי בתוך המחרוזת
* `split(regex)` - מפצלת מחרוזת למערך לפי התאמה לביטוי רגולרי

לפני
ECMAScript 6,
הצורה שבה מתודות אלו עבדו עם ביטויים רגולריים לא הייתה חשופה עבור מפתחים, ולא הייתה דרך לחקות ביטויים רגולריים באמצעות אוביקטים שהוגדרו על ידי המפתחים עצמם.
ECMAScript 6
מגדירה ארבעה סימבולים שתואמים לארבעת המתודות הללו, למעשה היא מייצאת את ההתנהגות הקיימת לאוביקט הקיים מסוג
`RegExp`.

הסימבולים
`Symbol.match`, `Symbol.replace`, `Symbol.search`, `Symbol.split`
מייצגים מתודות במקום הארגומנט שמקבל ביטוי רגולרי בקריאה למתודות
<span dir="ltr">`match()`</span>,
<span dir="ltr">`replace()`</span>,
<span dir="ltr">`search()`</span>,
<span dir="ltr">`split()`</span>,
בהתאמה. ארבע התכונות מסוג סימבול מוגדרות בעבור האוביקט
`RegExp.prototype`
בצורה הדיפולטיבית שבה המתודות הללו אמורות לעבוד.

ניתן ליצור אוביקט לשימוש עם מתודות האלו של מחרוזות בצורה שדומה לשימוש בביטויים רגולריים. לשם כך יש להשתמש בפונקציות הסימבול הבאות:

* `Symbol.match` - פונקציה שמקבלת ארגומנט מסוג מחרוזת ומחזירה מערך של תוצאות או את הערך
`null`
במידה ולא נמצאה כל תוצאה
* `Symbol.replace` - פונקציה שמקבלת ארגומנט מסוג מחרוזת ומחרוזת נוספת לצורך החלפה, ומחזירה מחרוזת.
* `Symbol.search` - פונקציה שמקבלת ארגומנט מסוג מחרוזת ומחזירה את האינדקס הנומרי של ההתאמה או את הערך
(-1)
במידה ולא נמצאה התאמה.
* `Symbol.split` - פונקציה שמקבלת ארגומנט מסוג מחרוזת ומחזירה מערך שמכיל חתיכות של המחרוזת לאחר שפוצלו לפי ההתאמה

היכולת להגדיר תכונות אלו על אוביקט מאפשרת לנו ליצור אוביקטים שמשתמשים בהתאמה לפי תבנית רגולרית ללא שימוש בביטויים רגולריים ולהשתמש באותם אוביקטים במתודות שמצפות לקבל ביטויים רגולריים כארגומנטים. להלן דוגמה שמראה את הסימבולים הנ״ל בפעולה:


<div dir="ltr">

```js
// /^.{10}$/
let hasLengthOf10 = {
    [Symbol.match]: function(value) {
        return value.length === 10 ? [value] : null;
    },
    [Symbol.replace]: function(value, replacement) {
        return value.length === 10 ? replacement : value;
    },
    [Symbol.search]: function(value) {
        return value.length === 10 ? 0 : -1;
    },
    [Symbol.split]: function(value) {
        return value.length === 10 ? ["", ""] : [value];
    }
};

let message1 = "Hello world",   // 11 characters
    message2 = "Hello John";    // 10 characters


let match1 = message1.match(hasLengthOf10),
    match2 = message2.match(hasLengthOf10);

console.log(match1);            // null
console.log(match2);            // ["Hello John"]

let replace1 = message1.replace(hasLengthOf10, "Howdy!"),
    replace2 = message2.replace(hasLengthOf10, "Howdy!");

console.log(replace1);          // "Hello world"
console.log(replace2);          // "Howdy!"

let search1 = message1.search(hasLengthOf10),
    search2 = message2.search(hasLengthOf10);

console.log(search1);           // -1
console.log(search2);           // 0

let split1 = message1.split(hasLengthOf10),
    split2 = message2.split(hasLengthOf10);

console.log(split1);            // ["Hello world"]
console.log(split2);            // ["", ""]
```

</div>

האוביקט
`hasLengthOf10`
נועד לעבוד כמו ביטוי רגולרי שתואם למחרוזת בעלת אורך של 10 תווים בדיוק. כל אחת מארבעת המתודות על האוביקט
`hasLengthOf10`
ממומשת בעזרת הסימבול המתאים, ואז המתודות המקבילות על שתי מחרוזות נקראות. המחרוזת הראשונה,
`message1`
בעלת 11 תווים ולכן אינה תואמת. המחרוזת השנייה,
`message2`
בעלת 10 תווים ולכן תהיה התאמה.
למרות שהוא אינו ביטוי רגולרי, האוביקט
`hasLengthOf10`
מועבר כארגומנט לכל מתודה של מחרוזת ונעשה בו שימוש נכון בזכות המתודות החדשות מסוג סימבול שהוספנו לו.

הדוגמה שהוצגה הינה דוגמה פשוטה. אך ניתן לבצע פעולות מורכבות יותר.

### Symbol.toPrimitive

ג׳אווהסקריפט מנסה באופן תדיר ובצורה עקיפה להמיר אוביקטים לערכים פרימיטיביים בזמן ביצוע פעולות מסוימות. לדוגמה, כאשר משווים מחרוזת לאוביקט באמצעות אופרטור השוואה כפולה
(`==`),
האוביקט מומר לערך פרימיטיבי לפני ההשוואה. הערך הפרימיטיבי הנ״ל היה עד עתה חלק מפעולה פנימית, אך
ECMAScript 6
חושפת את הערך ומאפשרת שינויו בעזרת המתודה
`Symbol.toPrimitive`

המתודה
`Symbol.toPrimitive`
מוגדרת על הפרוטוטיפ של כל סוג סטנדרטי ומתארת מה קורה כאשר אוביקט מומר לערך פרימיטיבי. כאשר יש צורך בהמרה לערך פרימיטיבי, מתבצעת קריאה למתודה עם ארגומנט אחד, שנקרה
`hint`
באפיון השפה.
לארגומנט
`hint`
שלושה ערכים אפשריים.
במידה וערכו של
`hint`
הינו
`"number"`
המתודה
`Symbol.toPrimitive`
צריכה להחזיר מספר. אם
`hint`
הינו
`"string"`
אז המתודה תחזיר מחרוזת, ואם ערכו
`"default"`
אז אין העדפה מיוחדת בעניין סוג הערך המוחזר.

עבור רוב האוביקטים הסטנדרטים, מצב ״מספר״ מבטא את ההתנהגות הבאה לפי הסדר:

1. קריאה למתודה
<span dir="ltr">`valueOf()`</span>,
ואם התוצאה הינה ערך פרימיטיבי הוא מוחזר.
1. אחרת, תתבצע קריאה למתודה
<span dir="ltr">`toString()`</span>,
ואם התוצאה הינה ערך פרימיטיבי הוא יוחזר.
1. אחרת, תיזרק שגיאה.

באופן דומה, עבור רוב האוביקטים הסטנדרטים מצב ״מחרוזת״ מבטא את ההתנהגות הבאה:

1. קריאה למתודה
<span dir="ltr">`toString()`</span>,
ואם התוצאה הינה ערך פרימיטיבי הוא מוחזר.
1. אחרת, תתבצע קריאה למתודה
<span dir="ltr">`valueOf()`</span>,
ואם התוצאה הינה ערך פרימיטיבי הוא יוחזר.
1. אחרת, תיזרק שגיאה.

ברוב המקרים אוביקטים סטנדרטים מתייחסים למצב דיפולטיבי כמו למצב ״מספרי״
(
מלבד
`Date`,
שמתייחס למצב דיפולטיבי כמו למצב ״מחרוזת״
).
על ידי הגדרת המתודה
`Symbol.toPrimitive`
ניתן לעקוף את ההתנהגויות הדיפולטיביות של המרה לערך פרימיטיבי.

I> מצב דיפולטיבי משמש אותנו רק עבור האופרטורים
`==`, `+`,
וכאשר מעבירים ארגומנט בודד לקונסטרקטור
`Date`.
רוב הפעולות משתמשות במצב ״מחרוזת״ או מצב ״מספר״

כדי לעקוף את התנהגות ההמרה הדיפולטיבית ניתן להגדיר מתודה עבור המזהה
`Symbol.toPrimitive`.
לדוגמה:


<div dir="ltr">

```js
function Temperature(degrees) {
    this.degrees = degrees;
}

Temperature.prototype[Symbol.toPrimitive] = function(hint) {

    switch (hint) {
        case "string":
            return this.degrees + "\u00b0"; // סמל מעלות לטמפרטורה

        case "number":
            return this.degrees;

        case "default":
            return this.degrees + " degrees";
    }
};

let freezing = new Temperature(32);

console.log(freezing + "!");            // "32 degrees!"
console.log(freezing / 2);              // 16
console.log(String(freezing));          // "32°"
```

</div>

הקוד מגדיר קונסטרקטור בשם
`Temperature`
ועוקף את המתודה
`Symbol.toPrimitive`
שנמצאת על הפרוטוטיפ. ערך שונה מוחזר בהתאם לערך הארגומנט
`hint`
שמצביע על מצב מסוג ״מספר״, ״מחרוזת״ או מצב דיפולטיבי
(
הארגומנט
`hint`
מתקבל באמצעות מנוע הריצה של ג׳אווהסקריפט
).
במצב מחרוזת, המתודה
`Symbol.toPrimitive`
מחזירה את הטמפרטורה עם סמל המעלות בסולם
Unicode.
במצב מספרי היא מחזירה רק את הערך הנומרי,
ובמצב דיפולטיבי, היא  מוסיפה את המילה
"degrees"
אחרי הערך המספרי.

כל אחת מפקודות ההדפסה מייצרת ערך שונה לארגומנט
`hint`.
האופרטור
`+`
מפעיל מצב דיפולטיבי ומגדיר את ערך הארגומנט
`hint`
לערך
`"default"`,
האופרטור
`/`
מפעיל מצב מספרי ומגדיר את ערך הארגומנט
`hint`
לערך
`"number"`,
והפונקציה
<span dir="ltr">`String()`</span>
מפעילה מצב מחרוזת ומגדירה את ערך הארגומנט
`hint`
לערך
`"string"`.
החזרת ערכים שונים עבור כל שלושת המצבים אפשרית, אך נפוץ יותר להגדיר את המצב הדיפולטיבי כך שיהיה זהה למצב מספרי או מצב מחרוזת.

### Symbol.toStringTag

אחת הבעיות המעניינות יותר בג׳אווהסקריפט הייתה קיומן של מספר סביבות הרצה גלובליות. זה קורה בדפדפנים כאשר דף מכיל דף אחר, למשל על ידי שימוש בתגית
`iframe`,
כאשר כל דף מכיל סביבת הרצה משלו. במרבית המקרים הדבר אינו יוצר בעיה, מכיוון ונתונים יכולים לעבור בין הסביבות ללא סיבה לדאגה. הבעיה מופיעה כאשר מנסים לזהות את סוג האוביקט לאחר שהועבר בין סביבות שונות.

הדוגמה הנפוצה לבעיה היא העברת מערך מתוך
iframe
אל הדף המכיל או להיפך.
מבחינת הטרמינולוגיה של אקמהסקריפט 6 ה-
iframe
והדף המכיל אותה נחשבים כל אחד בנפרד בתור
*מרחב ריצה*
(*realm*)
עצמאי שנחשב לסביבת הרצה עבור ג׳אווהסקריפט.
לכל מרחב ריצה יש את המרחב הגלובלי שלו ועותק נפרד של אוביקטים גלובלים. בכל מרחב ריצה שבו מוגדר המערך, הוא אכן מערך. אך כאשר הוא מועבר למרחב ריצה שונה קריאה לפעולה
`instanceof Array`
מחזירה את הערך
`false`
מפני שהמערך נוצר על ידי קונסטרקטור ממרחב ריצה שונה והמזהה
`Array`
מייצג את הקונסטרקטור של מרחב הריצה הנוכחי.

#### פתרון בעיית הזיהוי בצורה עקיפה

מפתחים מצאו שיטה טובה לזהות מערכים. הם גילו שקריאה למתודה
`toString()`
על אוביקט מחזירה תמיד את אותו ערך. לכן ספריות ג׳אווהסקריפט רבות מכילות פונקציה כמו הפונקציה בדוגמה הבאה:

<div dir="ltr">

```js
function isArray(value) {
    return Object.prototype.toString.call(value) === "[object Array]";
}

console.log(isArray([]));   // true
```

</div>

זה אולי נראה כמו פתרון עקום, אבל הוא אכן פתר את בעיית זיהוי המערכים בכל הדפדפנים. המתודה
<span dir="ltr">`toString()`</span>
של מערך אינה יעילה לזיהוי אוביקט מכיוון שהיא מחזירה ייצוג כמחרוזת תווים של הפריטים שאותו אוביקט מכיל. אך המתודה
<span dir="ltr">`toString()`</span>
ששייכת לאוביקט
`Object.prototype`
כוללת בתוכה פרמטר פנימי בשם
`[[Class]]`
בתוצאה.
מפתחים היו מסוגלים להשתמש במתודה זו על אוביקט על מנת להציג את מה שסביבת הריצה החשיבה לסוג האוביקט.

לא הייתה דרך לשנות התנהגות זו. היה ניתן להשתמש בשיטה כדי להבדיל בין אוביקטים של השפה ואלו שנוצרו על ידי מפתחים.
המקרה החשוב ביותר של נושא זה היה אוביקט
`JSON`
של אקמהסקריפט 5.

לפני אקמהסקריפט 5, מפתחים רבים השתמשו בקובץ
*json2.js*
של דאגלס קרוקפורד שיצר אוביקט גלובלי. כאשר דפדפנים החלו לממש בעצמם את האוביקט
הגלובלי
`JSON`
היה נחוץ לדעת האם אותו אוביקט נוצר על ידי הסביבה עצמה או על ידי ספריה חיצונית.
בעזרת שימוש באותה טכניקה שהראיתי קודם עם הפונקציה
<span dir="ltr">`isArray()`</span>,
מפתחים יצרו פונקציות כמו בדוגמה הבאה:

<div dir="ltr">

```js
function supportsNativeJSON() {
    return typeof JSON !== "undefined" &&
        Object.prototype.toString.call(JSON) === "[object JSON]";
}
```

</div>

אותן תכונות של
`Object.prototype
שאפשרו למפתחים לזהות מערכים בין תגיות
iframe
אפשרו לדעת האם
`JSON`
היה אוביקט מובנה בסביבה או לא.
אוביקט שאינו מובנה יחזיר
`[object Object]`
בעוד שגרסה מובנית של האוביקט תחזיר
`[object JSON]`.
טכניקה זו הפכה לשיטה סטנדרטית לזיהוי אוביקטים מובנים.

#### ECMAScript 6

אקמהסקריפט 6 נותנת גישה להתנהגות זו בעזרת הסימבול הידוע
`Symbol.toStringTag`.
הסימבול מייצג תכונה של אוביקט שמגדירה את הערך שיוחזר בקריאה לפונקציה
<span dir="ltr">`Object.prototype.toString.call()`</span>
על האוביקט.
בעבור מערך הערך שמוחזר מהפונקציה שמור בערך
`"Array"`
עבור התכונה
`Symbol.toStringTag`

באופן דומה, ביכולתך להגדיר את הערך של הסימבול
`Symbol.toStringTag`
עבור אוביקטים משלך:

<div dir="ltr">

```js
function Person(name) {
    this.name = name;
}

Person.prototype[Symbol.toStringTag] = "Person";

let me = new Person("Nicholas");

console.log(me.toString());                         // "[object Person]"
console.log(Object.prototype.toString.call(me));    // "[object Person]"
```
</div>

בדוגמה לעיל, התכונה בשם
`Symbol.toStringTag`
מוגדרת בעבור
`Person.prototype`
כדי לייצר את ההתנהגות הרצויה עבור ייצוג בתור מחרוזת. מכיוון שהאוביקט
`Person.prototype`
יורש את המתודה
<span dir="ltr">`Object.prototype.toString()`</span>,
הערך המוחזר מקריאה ל
`Symbol.toStringTag`
משמש גם בקריאה לפונקציה
<span dir="ltr">`me.toString()`</span>.
ואולם, ניתן להגדיר מתודה בשם
<span dir="ltr">`toString()`</span>
משלנו שיכולה לממש התנהגות שונה מבלי להשפיע על המתודה
<span dir="ltr">`Object.prototype.toString.call()`</span>.
כך זה יכול להיראות כדוגמה:

<div dir="ltr">

```js
function Person(name) {
    this.name = name;
}

Person.prototype[Symbol.toStringTag] = "Person";

Person.prototype.toString = function() {
    return this.name;
};

let me = new Person("ניקולאס");

console.log(me.toString());                         // "ניקולאס"
console.log(Object.prototype.toString.call(me));    // "[object Person]"
```

</div>

הקוד לעיל מגדיר את הפונקציה
<span dir="ltr">`Person.prototype.toString`</span>
כך שתחזיר את הערך השמור בתכונה
`name`.
מכיוון ש
`Person`
כבר לא משתמש באופן דיפולטיבי במתודה
<span dir="ltr">`Object.prototype.toString()`</span>,
קריאה ל
<span dir="ltr">`me.toString()`</span>.
מחזירה תוצאה שונה.

I> כל האוביקטים יורשים את
`Symbol.toStringTag`
מהאוביקט
`Object.prototype`
אלא אם מצוין אחרת.
הערך הדיפולטיבי עבור התכונה הינו המחרוזת
`"Object"`.

אין הגבלה על הערכים שיכולים לשמש עבור התכונה
`Symbol.toStringTag`
על אוביקטים שנוצרו על ידינו. כך לדוגמה שום דבר לא מונע מאיתנו להשתמש בערך
`"Array"
כמו בדוגמה הבאה:

<div dir="ltr">

```js
function Person(name) {
    this.name = name;
}

Person.prototype[Symbol.toStringTag] = "Array";

Person.prototype.toString = function() {
    return this.name;
};

let me = new Person("ניקולאס");

console.log(me.toString());                         // "ניקולאס"
console.log(Object.prototype.toString.call(me));    // "[object Array]"
```
</div>

התוצאה של קריאה למתודה
<span dir="ltr">`Object.prototype.toString()`</span>,
היא
`"[object Array]"`,
שהיא אותה תוצאה שתתקבל ממערך אמיתי. זה ממחיש את העובדה שהמתודה
<span dir="ltr">`Object.prototype.toString()`</span>,
כבר אינה דרך אמינה לזהות את סוג האוביקט.

שינוי ייצוג המחרוזת עבור אוביקטים מובנים גם הוא אפשרי. רק צריך להגדיר את
`Symbol.toStringTag`
על הפרוטוטיפ של אותו אוביקט, כמו בדוגמה הבאה:

<div dir="ltr">

```js
Array.prototype[Symbol.toStringTag] = "Magic";

let values = [];

console.log(Object.prototype.toString.call(values));    // "[object Magic]"
```
</div>

לאחר שהתכונה
`Symbol.toStringTag`
משתנה עבור מערכים כמו בדוגמה לעיל, הקריאה לפונקציה
<span dir="ltr">`Object.prototype.toString()`</span>,
מחזירה את הערך
`"[object Magic]"`
במקום את הערך
`"[object Array]"`.
למרות שאני ממליץ לא לשנות אוביקטים מובנים בדרך זו אין משהו בשפה שמונע זאת מאיתנו.

### Symbol.unscopables

פקודת
`with`
הינה אחד מהחלקים היותר שנויים במחלוקת.
במקור היא נועדה להימנע מהקלדה עודפת. היא קיבלה ביקורת בתור פקודה שמקשה על הבנת הקוד ובגלל השלכות על ביצועים שליליים כמו גם היותה מועדת לשגיאות.

כתוצאה מכך הפקודה
`with`
אינה מורשית לשימוש במצב קשיח.
המגבלה משפיעה גם על מחלקות ומודולים, שפועלות במצב קשיח באופן דיפולטיבי.

בעוד שקוד עתידי לא ישתמש בפקודת
`with`
הפקודה עדיין נתמכת באקמהסקריפט 6 במצב רגיל למען תאימות לאחור, ולכן היה צורך למצוא דרכים שמאפשרות שימוש תקין בקוד שמשתמש בפקודה
`with`.

כדי להבין את מורכבות המשימה חשוב על הדוגמה הבאה:

<div dir="ltr">

```js
let values = [1, 2, 3],
    colors = ["red", "green", "blue"],
    color = "black";

with(colors) {
    push(color);
    push(...values);
}

console.log(colors);    // ["red", "green", "blue", "black", 1, 2, 3]
```
</div>

בדוגמה לעיל, 2 הקריאות לפונקציה
<span dir="ltr">`push()`</span>
בתוך פקודת
`with`
זהות לשימוש ב
<span dir="ltr">`colors.push()`</span>
מכיוון שפקודת
`with`
הוסיפה את המזהה
`push`
בתור מזהה מקומי. הקישור למזהה
`color`
הינו עבור המשתנה שנוצר מחוץ לפקודת
`with`
כך גם לגבי הקישור למזהה
`values`.

אך אקמהסקריפט 6 הוסיפה את המתודה
`values`
למערכים.
(
המתודה
`values`
מוסברת בהרחבה בפרק 7,
״איטרטורים וגנרטורים״.
)
המשמעות היא שבסביבת אקמהסקריפט 6 המזהה
`values`
בתוך פקודת
`with`
מצביע על המתודה
`values`
ולא על המשתנה המקומי בשם
`values`,
מה שישבור את הקוד הקיים.
זו הסיבה לקיומו של הסימבול
`Symbol.unscopables`.

הסימבול
`Symbol.unscopables`
נמצא בשימוש על האוביקט
`Array.prototype`
כדי להחליט אילו תכונות לא צריכות לייצר מזהים בתוך פקודת
`with`
כאשר הוא מופיע,
`Symbol.unscopables`
הינו אוביקט ששמות תכונותיו הינן המזהים שיושמטו מפקודת
`with`
ושהערך עבורן הוא
`true`.
כך נראית התכונה הדיפולטיבית
`Symbol.unscopables`
עבור מערכים:

<div dir="ltr">

```js
// קיים באקמהסקריפט 6 באופן מובנה
Array.prototype[Symbol.unscopables] = Object.assign(Object.create(null), {
    copyWithin: true,
    entries: true,
    fill: true,
    find: true,
    findIndex: true,
    keys: true,
    values: true
});
```

</div>

האוביקט
`Symbol.unscopables`
בעל פרוטוטיפ מסוג
`null`,
שנוצר על ידי קריאה ל
<span dir="ltr">`Object.create(null)`</span>
ומכיל את כל המתודות החדשות של מערכים באקמהסקריפט 6.
(
    המתודות מוסברות בפרק 7,
    ״איטרטורים וגנרטורים״,
    ובפרק 9,
    ״מערכים״.
)
מזהים עבור מתודות אלו לא נוצרים בתוך פקודת
`with`
וכך מתאפשר לקוד ישן לעבוד מבלי ליצור בעיות חדשות.

בעיקרון, אין צורך להגדיר את
`Symbol.unscopables`
עבור אוביקטים משלנו אלא אם משתמשים בפקודת
`with`
ומבצעים שינויים לאוביקט קיים

## סיכום

סימבול הוא סוג חדש של ערך פרימיטיבי בג׳אווהסקריפט אשר משמש ליצירת תכונות שלא ניתן לגשת אליהן מבלי להשתמש בסימבול עצמו.

בעוד שאותן תכונות אינן באמת תכונות פרטיות, קשה יותר לשנותן באופן לא מכוון ומכאן סימבולים מתאימים מאוד להרצת קוד שזקוק לרמה מסוימת של הגנה מפני מפתחים אחרים.

בעבור סימבולים ניתן לספק תיאור שמקל על זיהוי ערכי סימבולים. קיים מרשם סימבולים גלובלי שמאפשר לנו להשתמש בסימבולים משותפים בחלקים שונים בקוד על ידי שימוש באותו תיאור. בדרך זו, אותו סימבול יכול לשמש עבור אותה פעולה במספר מקומות.

מתודות כמו
<span dir="ltr">`Object.keys()`</span>
או
<span dir="ltr">`Object.getOwnPropertyNames()`</span>
לא מחזירות סימבולים.
לכן נוספה מתודה חדשה באקמהסקריפט 6 כדי לאפשר מציאת תכונות מסוג סימבול. עדיין ניתן לשנות תכונות מסוג סימבול על ידי שימוש במתודות
<span dir="ltr">`Object.defineProperty()`</span>
ו-
<span dir="ltr">`Object.defineProperties()`</span>.

סימבולים ידועים מגדירים התנהגות שבעבר נחשבה להתנהגות פנימית
עבור אוביקטים ומשתמשים בסימבולים במרחב הגלובלי, כמו למשל
`Symbol.hasInstance`.
הסימבולים הללו משתמשים בקידומת
<span dir="ltr">`Symbol.`</span>
באפיון השפה ומאפשרים למפתחים לשנות התנהגות רגילה בעבור אוביקטים במגוון דרכים.

</div>

