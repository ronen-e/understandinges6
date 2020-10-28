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
`true`
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
<span dir="ltr">-2^53^</span>
עד
<span dir="ltr">2^53^</span>.
מעבר לטווח ״בטוח״ זה, ייצוג בינארי שונה משמש למספר מרובה של ערכים נומריים. משמעות הדבר היא שג׳אווהסקריפט יכולה לייצג ערכים שלמים בצורה מדוייקת בתוך טווח זה לפני שצצות בעיות.
לדוגמה:

<div dir="ltr">

```js
console.log(Math.pow(2, 53));      // 9007199254740992
console.log(Math.pow(2, 53) + 1);  // 9007199254740992
```

</div>

כפי שרואים בדוגמה לעיל, שני ערכים שונים מיוצגים על ידי אותו ערך מספרי שלם.
התופעה תופיע באופן תכוף יותר ככל שהערך המדובר רחוק יותר מן הטווח הבטוח.

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
`true`
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
<span dir="ltr">`\u0061`</span>
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
<span dir="ltr">`\u0061`</span>
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
`$`, `_`,
<span dir="ltr">`\u200c`</span>
(
    תו לא מחבר באורך אפסי
    <span dir="ltr">a zero-width non-joiner</span>
),
<span dir="ltr">`\u200d`</span>
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

## התכונה  `__proto__`

אפילו לפני שהוציאו לאור את גרסת אקמהסקריפט 5, מספר מנועי ריצה של ג׳אווהסקריפט כבר מימשו תכונה מיוחדת בשם
`__proto__`
שהייתה מסוגלת לקרוא ולקבוע את התכונה הפנימית
`[[Prototype]]`.
באופן מעשי,
התכונה
`__proto__`
הייתה הקדמה למתודות
<span dir="ltr">`Object.getPrototypeOf()`</span>
ו-
<span dir="ltr">`Object.setPrototypeOf()`</span>.
הציפייה שכל מנועי הריצה של ג׳אווהסקריפט יסירו את התכונה אינה מציאותית
(היו ספריות ג׳אווהסקריפט פופולריות שעשו שימוש בתכונה
`__proto__`),
ולכן אקמהסקריפט 6 הפכה את התנהגות התכונה
`__proto__`
לרשמית.
אך השינוי מופיע בנספח
B
של תקן
ECMA-262
ביחד עם אזהרה זו:

<div dir="ltr">

> These features are not considered part of the core ECMAScript language. Programmers should not use or assume the existence of these features and behaviours when writing new ECMAScript code. ECMAScript implementations are discouraged from implementing these features unless the
implementation is part of a web browser or is required to run the same legacy ECMAScript code that web browsers encounter.

</div>

> תכונות אלו אינן נחשבות לחלק מעיקרי השפה.
מתכנתים לא צריכים להשתמש או להסתמך על קיום תכונות והתנהגויות אלו בעת כתיבת קוד אקמהסקריפט חדש. מימושים של אקמהסקריפט צריכים להימנע ממימוש תכונות אלו אלא אם כן המימוש הינו חלק מדפדפן או נדרש להריץ את אותו קוד מגרסה קודמת של אקמהסקריפט שדפדפנים מריצים.

האפיון עבור אקמהסקריפט ממליץ להשתמש
במתודות
<span dir="ltr">`Object.getPrototypeOf()`</span>
ו-
<span dir="ltr">`Object.setPrototypeOf()`</span>
מכיוון שלתכונה
`__proto__`
יש את המאפיינים הבאים:

1. ניתן להגדיר את התכונה
`__proto__`
פעם אחת בלבד בעת הגדרת אובייקט ליטראל. אם נגדיר שתי תכונות מסוג
`__proto__`
תיזרק שגיאה. זוהי התכונה היחידה בתוך אובייקט ליטראל בעלת מגבלה זו.
1. הצורה המחושבת של תכונה זו
`["__proto__"]`
פועלת כמו תכונה רגילה ולא קוראת או משנה את הפרוטוטייפ של האובייקט הנוכחי. כל הכללים
הקשורים לתכונות אובייקט ליטראל תקפים בצורה זו בניגוד לצורה הלא מחושבת, שיש לה כללים יוצאי דופן.

בעוד שרצוי להימנע משימוש בתכונה
`__proto__`
הצורה שבה היא נמצאת באפיון השפה מעניינת מאוד. במנועי ריצה של אקמהסקריפט 6, התכונה
<span dir="ltr">`Object.prototype.__proto__`</span>
מוגדרת בתור תכונת גישה שהמתודה

שלה קוראת למתודה
<span dir="ltr">`Object.getPrototypeOf()`</span>
והמתודה
`set`
שלה קוראת למתודה
<span dir="ltr">`Object.setPrototypeOf()`</span>.
זה אומר שאין הבדל אמיתי בין שימוש בתכונה
`__proto__`
לבין שימוש ב
<span dir="ltr">`Object.getPrototypeOf()`/`Object.setPrototypeOf()`</span>,
מלבד זאת שהתכונה
`__proto__`
מאפשרת לנו להגדיר פרוטוטייפ של אובייקט ליטראל בצורה ישירה.
לדוגמה:

<div dir="ltr">

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

// person
// הוא הפרוטוטייפ
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
</div>

במקום לקרוא למתודה
<span dir="ltr">`Object.create()`</span>
כדי לייצר את האובייקט
`friend`
דוגמה זו מייצרת אובייקט ליטראל סטנדרטי שמבצעת השמה לערך התכונה
`__proto__`.
בעת יצירת אובייקט בעזרת המתודה
<span dir="ltr">`Object.create()`</span>
ניאלץ להגדיר את כל מאפייני התכונה המלאים בעבור כל תכונה נוספת.

</div>