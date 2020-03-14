<div dir="rtl">

# פונקציות

פונקציות הן חלק חשוב בכל שפת תכנות ועד אקמהסקריפט 6, פונקציות נותרו כמעט כמות שהן מאז כתיבת השפה. 
הדבר הותיר מאחוריו שובל של בעיות והתנהגות בעייתית שבגללה היה קל מאוד לטעות ולעיתים תכופות דרשה כתיבת קוד רבה רק לשם השגת מטרות פשוטות.

פונקציות כפי שהן קיימות באקמהסקריפט 6 מקדמות את השפה באופן ניכר, ומבטאות התייחסות לשנים של תלונות ובקשות מצד מפתחי ג׳אווהסקריפט. 
התוצאה הינה מספר שיפורים על גבי השיפורים שכבר התווספו באקמהסקריפט 5 שבזכותם תכנות בג׳אווהסקריפט הופך לקל ויעיל יותר.

## פונקציות עם ערכי ברירת מחדל

לפונקציות בג׳אווהסקריפט הינן בעלות תכונה מעניינת וייחודית - הן יכולות לקבל כל מספר של ארגומנטים, ללא קשר למספר הארגומנטים שניתן בהגדרת הפונקציה. זה מאפשר יצירת פונקציות שיכולות לטפל במספרים שונים של ארגומנטים, לעיתים תכופות רק על ידי כתיבת ערכי ברירת מחדל כאשר ארגומנטים לא מועברים פנימה לפונקציה. 
כעת נרחיב כיצד ערכי ברירת מחדל 
(ערכים דיפולטיביים) 
עובדים לפני ואחרי אקמהסקריפט, בנוסף להסבר על אובייקט 
`arguments`
המיוחד, 
באמצעות ביטויים
<span dir="ltr">(expressions)</span> 
בתור ארגומנטים ועוד. 
כמו כן, יוצג בפניכם אזור מת באופן זמני חדש
<span dir="ltr">(TDZ)</span>.

### ערכים דיפולטיביים באקמהסקריפט 5

באקמהסקריפט 5 וקודם לכן, היה ניתן להשתמש בשיטה הבאה על מנת ליצור פונקציה עם ערכים דיפולטיביים

<div dir="ltr">

```js
function makeRequest(url, timeout, callback) {

    timeout = timeout || 2000;
    callback = callback || function() {};

    // יתר הפונקציה

}
```

</div>

בדוגמה לעיל, שני המשתנים
`timeout` ו `callback`
הינם משתנים אופציונליים מכיוון שהם מקבלים ערך דיפולטיבי במידה ואיננו מסופק לפונקציה. 
האופרטור הלוגי 
OR 
(`||`)
תמיד מחזיר את האופרנד השני כאשר הראשון הינו ״שקרי״.
מאחר ופרמטרים שאינם מסופקים לפונקציה מקבלים את הערך 
`undefined`,
האופרטור הלוגי 
OR 
משמש לעיתים תכופות בכדי לספק ערך דיפולטיבי עבור פרמטרים חסרים. 
קיים פגם בשיטה זו, מאחר וערך תקין עבור המשתנה 
`timeout` 
יכול להיות 
`0`, 
אך הערך יוחלף ב
 `2000` 
מאחר ו
`0` 
הינו ״שקרי״ 

במקרה כזה, מומלץ לבדוק את סוג הארגומנט בעזרת 
`typeof`
כמו בדוגמה הבאה:


<div dir="ltr">

```js
function makeRequest(url, timeout, callback) {

    timeout = (typeof timeout !== "undefined") ? timeout : 2000;
    callback = (typeof callback !== "undefined") ? callback : function() {};

    // יתר הפונקציה

}
```

</div>

בעוד שגישה זו עדיפה, היא עדיין דורשת כתיבת קוד רב יחסית עבור פעולה בסיסית. 
ספריות ג׳אווהסקריפט פופולריות מלאות בדוגמאות דומות. 

### ערכים דיפולטיביים באקמהסקריפט 6

אקמהסקריפט 6 מפשטת את תהליך הוספת ערכים דיפולטיביים לפרמטרים באמצעות הוספת יכולת אתחול פרמטרים כאשר הללו אינם נתונים לנו. לדוגמה:

<div dir="ltr">

```js
function makeRequest(url, timeout = 2000, callback = function() {}) {

    // יתר הפונקציה

}
```

</div>

הפונקציה בדוגמה לעיל מצפה רק את הפרמטר הראשון כארגומנט שחובה להעבירו. שני הפרמטרים האחרים מקבלים ערכים דיפולטיביים, מה שהופך את הפונקציה לקטנה יותר מכיוון ואין צורך לכתוב קוד נוסף שבודק ערכים חסרים ומחליף אותם.

כאשר קוראים לפונקציה
<span dir="ltr">`makeRequest()`</span>
ומספקים את כל שלושת הפרמטרים, הערכים הדיפולטיביים אינם נלקחים. לדוגמה:

<div dir="ltr">

```js
// שימוש בערכים דיפולטיביים
// timeout, callback
makeRequest("/foo");

// שימוש בערכים דיפולטיביים
// callback
makeRequest("/foo", 500);

// אין שימוש בערכים דיפולטיביים
makeRequest("/foo", 500, function(body) {
    doSomething(body);
});
```

</div>

תחת אקמהסקריפט 6 המשתנה
`url`
הוא משתנה שחובה לספקו, וזוהי הסיבה שהערך
`"/foo"` 
נתון בכל שלושת הקריאות לפונקציה
<span dir="ltr">`makeRequest()`</span>. 
שני הפרמטרים שמקבלים ערכים דיפולטיביים הינם משתנים אופציונליים.

ניתן להגדיר ערך דיפולטיבי בעבור כל ארגומנט, כולל אלו שמופיעים לפני ארגומנטים ללא ערכים דיפולטיביים בהגדרת הפונקציה. 
למשל, 
הקוד בדוגמה הבאה הינו קוד תקין:

<div dir="ltr">

```js
function makeRequest(url, timeout = 2000, callback) {

    // יתר הפונקציה

}
```

</div>

בדוגמה זו, הערך הדיפולטיבי עבור המשתנה 
`timeout`
יתקבל רק כאשר הארגומנט השני איננו מסופק לפונקציה או כאשר הארגומנט השני מסופק עם הערך 
`undefined`, 
לדוגמה: 

<div dir="ltr">

```js
// שימוש בערכים דיפולטיביים
// timeout
makeRequest("/foo", undefined, function(body) {
    doSomething(body);
});

// שימוש בערכים דיפולטיביים
// timeout
makeRequest("/foo");

// אין שימוש בערכים דיפולטיביים
makeRequest("/foo", null, function(body) {
    doSomething(body);
});
```

</div>

בכל הנוגע לערכים דיפולטיביים, הערך 
`null`
נחשב לערך תקין, 
ומכאן שבעת הקריאה השלישית לפונקציה 
<span dir="ltr">`makeRequest()`</span> 
הערך הדיפולטיבי של המשתנה 
`timeout`
לא יתקבל.

### כיצד ערכים דיפולטיביים משפיעים על אוביקט ארגומנטס

חשוב לדעת שהתנהגות אוביקט
`arguments` 
שונה ביחס לערכים דיפולטיביים. 
באקמהסקריפט 5 תחת עבודה במצב לא קשיח 
<span dir="ltr">(nonstrict mode)</span> 
אוביקט 
`arguments` 
משקף שינויים בפרמטרים של הפונקציה, כאשר הועברו לתוכה
לדוגמה:

<div dir="ltr">

```js
function mixArgs(first, second) {
    console.log(first === arguments[0]);
    console.log(second === arguments[1]);
    first = "c";
    second = "d";
    console.log(first === arguments[0]);
    console.log(second === arguments[1]);
}

mixArgs("a", "b");
```

</div>

הפלט עבור הקוד לעיל הינו:

<div dir="ltr">

```
true
true
true
true
```

</div>

אוביקט 
`arguments` 
תמיד משתנה במצב לא קשיח ומשקף שינויים בפרמטרים של הפונקציה, כאשר הם מסופקים לפונקציה  
ומכאן, כאשר המשתנים 
`first` 
ו 
`second` 
מקבלים ערכים חדשים 
גם
<span dir="ltr">`arguments[0]` </span> 
ו 
<span dir="ltr">`arguments[1]` </span> 
משתנים בהתאם, ולכן כל ההשוואות מסוג 
`===` 
מקבלות את הערך 
`true`. 

לעומת זאת,
כאשר פועלים תחת הכללים של מצב קשיח באקמהסקריפט 5, 
ההתנהגות של אוביקט 
`arguments`
משתנה. 
במצב קשיח אוביקט
`arguments`
לא משקף שינויים בפרמטרים של הפונקציה.
לדוגמה:

<div dir="ltr">

```js
function mixArgs(first, second) {
    "use strict";

    console.log(first === arguments[0]);
    console.log(second === arguments[1]);
    first = "c";
    second = "d"
    console.log(first === arguments[0]);
    console.log(second === arguments[1]);
}

mixArgs("a", "b");
```

</div>

הפלט הינו:

<div dir="ltr">

```
true
true
false
false
```

</div>

בדוגמה לעיל,
שינוי המשתנים 
`first` 
ו
 `second` 
אינו משפיע על אוביקט 
`arguments`, 
ולכן הפלט נראה כמצופה.

לעומת זאת,
כאשר מופיע אוביקט 
`arguments` 
בפונקציה שמשתמשת בערכים דיפולטיביים של אקמהסקריפט 6,
 יתנהג באותו אופן כמו במצב קשיח של אקמהסקריפט 5,
בלי קשר לשאלה האם הפונקציה ככלל נמצאת במצב קשיח. 
נוכחותם של ערכים דיפולטיביים גורמת לאוביקט 
`arguments`
להתנתק מן הפרמטרים בפונקציה. 
זהו פרט חשוב לדעת כאשר משתמשים באוביקט 
`arguments`
בעת הפיתוח. 
לדוגמה:

<div dir="ltr">

```js
// הפונקציה אינה פועלת במצב קשיח
function mixArgs(first, second = "b") {
    console.log(arguments.length);
    console.log(first === arguments[0]);
    console.log(second === arguments[1]);
    first = "c";
    second = "d"
    console.log(first === arguments[0]);
    console.log(second === arguments[1]);
}

mixArgs("a");
```

</div>

הפלט הינו:

<div dir="ltr">


```
1
true
false
false
false
```

</div>

בדוגמה זו, 
הערך של 
<span dir="ltr">`arguments.length`</span> 
הינו 1 מכיוון שרק ארגומנט אחד הועבר לפונקציה 
<span dir="ltr">`mixArgs()`</span>. 
ומכאן הערך עבור 
<span dir="ltr">`arguments[1]`</span>
הינו
`undefined`, 
כמצופה, כאשר רק ארגומנט אחד מסופק לפונקציה. 
המשמעות היא שערך המשתנה 
`first` 
זהה לערך של 
<span dir="ltr">`arguments[0]`</span>. 
שינוי ערכי המשתנים 
`first` 
ו 
`second`
לא משנה את אוביקט 
`arguments` 
בשום צורה שהיא.
התנהגות זו מתקיימת הן תחת מצב קשיח והן תחת מצב רגיל, ובצורה כזו ניתן לסמוך על אוביקט 
`arguments` 
שישקף תמיד את המצב ההתחלתי של הפונקציה בעת קריאתה.

### חישוב ערכים דיפולטיביים

תכונה מעניינת של ערכים דיפולטיביים היא שהם אינם חייבים להיות ערכים פרימיטיביים. 
ניתן למשל, להריץ פונקציה כדי להחזיר ממנה את הערך הדיפולטיבי, לדוגמה:

<div dir="ltr">

```js
function getValue() {
    return 5;
}

function add(first, second = getValue()) {
    return first + second;
}

console.log(add(1, 1));     // 2
console.log(add(1));        // 6
```

</div>

כאשר הארגומנט האחרון אינו מועבר לפונקציה 
<span dir="ltr">`add()`</span>
תופעל הפונקציה 
<span dir="ltr">`getValue()`</span>
על מנת לקבל את הערך הדיפולטיבי עבור הפרמטר השני. 
 הפונקציה 
<span dir="ltr">`getValue()`</span> 
נקראת רק כאשר 
<span dir="ltr">`add()`</span> 
נקראת ללא הפרמטר השני, לא בעת הגדרת הפונקציה. 
אם הפונקציה 
<span dir="ltr">`getValue()`</span> 
הייתה נכתבת אחרת היא הייתה יכולה להחזיר ערך אחר. 
לדוגמה:

<div dir="ltr">

```js
let value = 5;

function getValue() {
    return value++;
}

function add(first, second = getValue()) {
    return first + second;
}

console.log(add(1, 1));     // 2
console.log(add(1));        // 6
console.log(add(1));        // 7
```

</div>

בדוגמה לעיל, המשתנה
`value` 
מאותחל עם הערך 
`5` 
ומעודכן בכל פעם שהפונקציה 
<span dir="ltr">`getValue()`</span> 
נקראת. 
הקריאה הראשונה לפונקציה 
<span dir="ltr">`add(1)`</span> 
מחזירה את הערך 
`6`,  
בעוד שהקריאה השנייה לפונקציה 
<span dir="ltr">`add(1)`</span> 
מחזירה את הערך 
`7` 
מכיוון שערכו של 
`value` 
השתנה. 
מפני שהערך הדיפולטיבי של המשתנה 
`second`
מתקבל רק כאשר הפונקציה 
`add`
נקראת, שינויים לאותו ערך יכולים שיתבצעו בכל עת.

W> חובה להיזהר בעת שימוש בפונקציות למתן ערכים דיפולטיביים. 
אם שכחתם את השימוש בסוגריים, לדוגמה, אם היה נכתב: 

<span dir="ltr">`second = getValue`</span> 

בדוגמה האחרונה, המשמעות הינה העברת מצביע לפונקציה עצמה במקום לערך המוחזר ממנה.

ניתן להשתמש בפרמטר קודם כערך דיפולטיבי לפרמטר שאחריו. 
לדוגמה: 

<div dir="ltr">

```js
function add(first, second = first) {
    return first + second;
}

console.log(add(1, 1));     // 2
console.log(add(1));        // 2
```

</div>

בקוד לעיל, הפרמטר
`second` 
מקבל את הערך הדיפולטיבי שזהה לערכו של המשתנה 
`first`, 
כך שהעברת ארגומנט אחד לפונקציה למעשה נותן לשני הארגומנטים
את אותו הערך. 
ולכן הקריאה לפונקציה כך:
<span dir="ltr">`add(1, 1)`</span> 
תחזיר את הערך `2` כתוצאה כמו הקריאה 
<span dir="ltr">`add(1)`</span>. 
ניתן אף להעביר את המשתנה
 `first`
לתוך פונקציה על מנת לקבל ערך דיפולטיבי עבור המשתנה 
 `second`:


<div dir="ltr">

```js
function getValue(value) {
    return value + 5;
}

function add(first, second = getValue(first)) {
    return first + second;
}

console.log(add(1, 1));     // 2
console.log(add(1));        // 7
```

</div>

בדוגמה האחרונה נותנים למשתנה 
`second` 
את הערך המוחזר כתוצאה מהפונקציה
<span dir="ltr">`getValue(first)`</span>, 
לכן בעוד שהקריאה לפונקציה
<span dir="ltr">`add(1, 1)`</span> 
מחזירה את הערך 2, 
הקריאה לפונקציה 
<span dir="ltr">`add(1)`</span> 
מחזירה את הערך 
7 
(1 + 6).

שיטה זו של הפניה לפרמטרים קודמים בעת השמת ערכים דיפולטיביים עובדת רק עבור ארגומנטים קיימים, ולכן אין גישה לארגומנטים שבאים מאוחר יותר מאשר בזמן הגדרת הפונקציה. לדוגמה:


<div dir="ltr">

```js
function add(first = second, second) {
    return first + second;
}

console.log(add(1, 1));         // 2
console.log(add(undefined, 1)); // שגיאה
```

</div>

הקריאה לפונקציה 
<span dir="ltr">`add(undefined, 1)`</span> 
זורקת שגיאה מכיוון שהפרמטר
`second`
מוגדר לאחר
`first` 
ולכן איננו זמין בתור ערך דיפולטיבי. 
על מנת להבין מדוע הדבר קורה חשוב להסביר שוב על
הנושא הקרוי
`אזור מת באופן זמני`.

### אזור מת באופן זמני בערכים דיפולטיביים

בפרק 1 דובר על האזור המת באופן זמני 
<span dir="ltr">(TDZ)</span> 
כפי שהוא נוגע למשתנים מסוג 
`let` 
ו-
`const`, 
ובאופן דומה גם לפרמטרים דיפולטיביים קייבם אזור מת באופן זמני שבו פרמטרים אינם נגישים. 
בדומה להגדרת 
`let` 
כל פרמטר מייצר קישור חדש משלו שלא ניתן לגישה לפני אתחולו. ניסיון לעשות זאת יגרום לשגיאה. 
אתחול פרמטרים מתקיים כאשר הפונקציה נקראת, או על ידי העברת ערך עבור הפרמטר או באמצעות קביעת ערך דיפולטיבי.

על מנת לחקור את האזור המת של פרמטרים דיפולטיביים ניתן להשתמש בדוגמת קוד קודמת:

<div dir="ltr">

```js
function getValue(value) {
    return value + 5;
}

function add(first, second = getValue(first)) {
    return first + second;
}

console.log(add(1, 1));     // 2
console.log(add(1));        // 7
```

</div>

הקריאות לפונקציות
<span dir="ltr">`add(1, 1)`</span> 
ו- 
<span dir="ltr">`add(1)`</span> 
למעשה מריצות את הקוד הבא על מנת ליצור את הערכים עבור הפרמטרים
`first` 
ו 
 `second`:

<div dir="ltr">

```js
// add(1, 1)
let first = 1;
let second = 1;

// add(1)
let first = 1;
let second = getValue(first);
```

</div>

כאשר הפונקציה
<span dir="ltr">`add()`</span> 
נקראת לראשונה,
הקישורים למשתנים 
`first` 
ו 
 `second`
נוצרים בתוך אזור מת מיוחד לפרמטרים
(בדומה להתנהגות משתנים מסוג 
`let`). 
בעוד שהמשתנה 
`second`
יכול שיאותחל בעזרת הערך של המשתנה
`first`
וזאת מכיוון שהמשתנה
`first`
תמיד מאותחל
קודם לכן, 
אך ההיפך אינו תמיד נכון.
לדוגמה:

<div dir="ltr">

```js
function add(first = second, second) {
    return first + second;
}

console.log(add(1, 1));         // 2
console.log(add(undefined, 1)); // שגיאה
```

</div>

בדוגמה לעיל הקריאות לפונקציות
<span dir="ltr">`add(1, 1)`</span> 
ו- 
<span dir="ltr">`add(undefined, 1)`</span> 
למעשה מתורגמות מאחורי הקלעים לקוד הבא:

<div dir="ltr">

```js
// add(1, 1)
let first = 1;
let second = 1;

// add(undefined, 1)
let first = second;
let second = 1;
```

</div>

בדוגמה לעיל הקריאה לפונקציה
<span dir="ltr">`add(undefined, 1)`</span> 
זורקת שגיאה מכיוון והמשתנה
`second`
עוד לא מאותחל בעת אתחול המשתנה
`first`.
באותו הרגע המשתנה 
`second`
שוהה בתוך האזור המת ולכן כל ניסיון לקרוא את ערכו של 
`second`
יזרוק שגיאה. 
זוהי אותה התנהגות שנצפית במשתנים מסוג 
`let`
כפי שתוארה בפרק 1. 

I> פרמטרים של פונקציה מקבלים סביבה ואזור מת בנפרד מזה של גוף הפונקציה. המשמעות של הדבר היא שערך דיפולטיבי של פרמטר אינו יכול לגשת למשתנים אשר מוגדרים בתוך גוף הפונקציה.

## פרמטרים שלא הוגדרו

הדוגמאות בפרק זה עסקו אך ורק בפרמטרים שהוגדרו בחתימת הפונקציה. 
אך פונקציות בג׳אווהסקריפט אינן מוגבלות מבחינת מספר הפרמטרים שניתן להעביר לתוך הפונקציה ללא קשר למספר הפרמטרים שהוגדרו בפועל. 
תמיד ניתן להעביר פחות או יותר פרמטרים מאלו שהוגדרו באופן רשמי. 
ערכים דיפולטיביים מציגים בצורה ברורה מתי פונקציה יכולה לקבל פחות פרמטרים, ואקמהסקריפט 6 רצתה גם לשפר את המצב שבו מעבירים יותר פרמטרים מאשר הוגדרו.

### פרמטרים לא מוגדרים באקמהסקריפט 5

עוד מתחילתה, ג׳אווהסקריפט סיפקה את אוביקט 
`arguments`
בתור דרך להתייחס לכל הפרמטרים שמועברים לפונקציה מבלי שיהיה צורך להגדיר כל אחד מהם בנפרד. 
התעסקות עם האוביקט ייתכן שתהיה מכבידה יחסית. לדוגמה, הקוד הבא:

<div dir="ltr">

```js
function pick(object) {
    let result = Object.create(null);

    // start at the second parameter
    for (let i = 1, len = arguments.length; i < len; i++) {
        result[arguments[i]] = object[arguments[i]];
    }

    return result;
}

let book = {
    title: "Understanding ECMAScript 6",
    author: "Nicholas C. Zakas",
    year: 2015
};

let bookData = pick(book, "author", "year");

console.log(bookData.author);   // "Nicholas C. Zakas"
console.log(bookData.year);     // 2015
```
</div>

הפונקציה בדוגמת הקוד פועלת בדומה לפונקציה 
<span dir="ltr">`pick()`</span> 
מתוך ספריית 
<span dir="ltr">*Underscore.js*</span> ,
שמחזירה עותק של אוביקט שמקבל אליו חלק מתכונות האוביקט המקורי. דוגמה זו מגדירה רק ארגומנט אחד ומצפה ממנו להיות האוביקט שממנו יועתקו תכונות. כל ארגומנט נוסף שמועבר לפונקציה הינו שם תכונה שאמורה להיות מועתקת מהאוביקט המקורי לתוצר הפונקציה. 

ישנם מספר דברים שיש להתייחס אליהם בפונקציה 
<span dir="ltr">`pick()`</span> 
שבדוגמה. 
ראשית, 
כלל אין זה ברור לעין שהפונקציה מסוגלת לטפל ביותר מפרמטר אחד. 
ניתן להגדיר פרמטרים נוספים אך עדיין הדבר לא ייתן אינדיקציה לכך שהפונקציה מסוגלת לקבל כל מספר של פרמטרים. 
שנית, מאחר והפרמטר הראשון מוגדר ונעשה בו שימוש בפונקציה, חובה להתחיל את חיפוש התכונות בסמן אינדקס מספר 
`1` 
במקום בסמן אינדקס מספר 
`0`. 

אקמהסקריפט 6 מציעה לנו פרמטרים מסוג רסט בכדי להתגבר על בעיות אילו.

### פרמטרים מסוג רסט

*פרמטר מסוג רסט* 
<span dir="ltr">`(Rest Parameter)`</span> 
מיוצג על ידי שלוש נקודות 
(`...`)
שבאות לפני שם הפרמטר. 
הפרמטר הופך לערך מסוג מערך 
`Array`
שמכיל את שאר הפרמטרים המועברים לפונקציה. 
זהו המקור לשם 
״rest״
(״שאר״).
כך ניתן לשכתב את הפונקציה 
<span dir="ltr">`pick()`</span> 
להשתמש בפרמטרים מסוג רסט:

<div dir="ltr">

```js
function pick(object, ...keys) {
    let result = Object.create(null);

    for (let i = 0, len = keys.length; i < len; i++) {
        result[keys[i]] = object[keys[i]];
    }

    return result;
}
```

</div>

בדוגמה לעיל, 
הפרמטר בשם
`keys`
הינו פרמטר מסוג רסט שמכיל את כל הפרמטרים המועברים לפונקציה לאחר הפרמטר בשם
`object` 
(בניגוד אל 
`arguments`, 
שמכיל את כל הפרמטרים המועברים לפונקציה, כולל הראשון
). 
המשמעות הינה שניתן לרוץ על המשתנה
`keys`
מההתחלה ועד הסוף ללא כל חשש. 
בנוסף, ניתן להתרשם בקלות שמדובר בפונקציה שמסוגלת לקבל כל מספר של פרמטרים.

I> פרמטרים מסוג רסט אינם משפיעים על תכונת 
`length`
בפונקציה, שמספקת מידע על מספר הפרמטרים המוגדרים בחתימת הפונקציה. הערך של התכונה 
<span dir="ltr">`pick()`</span> 
בעבור הפונקציה 
בדוגמה למעלה הינו 1 מכיוון שרק הפרמטר 
`object` 
נחשב עבור חישוב ערך התכונה.

#### מגבלות על פרמטרים מסוג רסט

מתקיימות שתי הגבלות לעניין פרמטרים מסוג רסט. המגבלה הראשונה היא שיהיה אך ורק פרמטר אחד מסוג רסט, ואותו פרמטר חייב להיות מוגדר אחרון. 
הקוד בדוגמה הבאה לא יעבוד:

<div dir="ltr">

```js
// Syntax error: Can't have a named parameter after rest parameters
function pick(object, ...keys, last) {
    let result = Object.create(null);

    for (let i = 0, len = keys.length; i < len; i++) {
        result[keys[i]] = object[keys[i]];
    }

    return result;
}
```

</div>

בדוגמה לעיל, הפרמטר 
`last` 
בא אחרי הפרמטר מסוג רסט בשם 
`keys`,
ולכן תיזרק שגיאת תחביר.

המגבלה השניה היא שפרמטרים מסוג רסט לא יכולים להיות מוגדרים בתוך מגדיר ערך תכונה,
<span dir="ltr">`(setter)`</span>. 
גם הקוד הבא יזרוק שגיאת תחביר:

<div dir="ltr">

```js
let object = {

    // Syntax error: Can't use rest param in setter
    set name(...value) {
        // שאר הקוד
    }
};
```

</div>

מגבלה זו קיימת מכיוון ומגדירי ערך תכונה מוגבלים לארגומנט בודד יחיד. 
פרמטרים מסוג רסט מייצגים מספר בלתי מוגבל של ארגומנטים ולכן הם אסורים לשימוש בהקשר זה.

#### השפעת פרמטרים מסוג רסט על אוביקט ארגומנטס

פרמטרים מסוג רסט נועדו להחליף את אוביקט 
`arguments`. 
במקור, אקמהסקריפט 4 סיימה את השימוש באוביקט 
`arguments` 
ובמקומו הוסיפה פרמטרים מסוג רסט על מנת לאפשר העברת מספר בלתי מוגבל של ארגומנטים לתוך פונקציות. 
בסופו של דבר, אקמהסקריפט 4 מעולם לא יצאה אל הפועל, אך הרעיון עצמו נשמר והופיע שוב באקמהסקריפט 6, למרות שאוביקט 
`arguments`
עדיין ממשיך להתקיים.

אוביקט 
`arguments`
עובד יחד עם פרמטרים מסוג רסט כאשר הוא משקף את הארגומנטים שהועברו לפונקציה בעת קריאתה, כפי שמודגם בדוגמה הקוד הבאה:

<div dir="ltr">

```js
function checkArgs(...args) {
    console.log(args.length);
    console.log(arguments.length);
    console.log(args[0], arguments[0]);
    console.log(args[1], arguments[1]);
}

checkArgs("a", "b");
```

</div>

הקריאה לפונקציה 
<span dir="ltr">`checkArgs()`</span>. 
מייצרת את הפלט הבא:

<div dir="ltr">

```
2
2
a a
b b
```

</div>

אוביקט 
`arguments`
משקף בצורה מדויקת את הפרמטרים שהועברו בפועל לפונקציה בעת קריאתה, ללא קשר לשימוש בפרמטרים מסוג רסט.

## יכולות משופרות של בנאי פונקציה

בנאי הפונקציה
<span dir="ltr">(`Function` constructor)</span>
מהווה חלק מהשפה שמאפשר ליצור פונקציות חדשות באופן דינמי. 
הארגומנטים עבור הבנאי הם הפרמטרים עבור הפונקציה והארגומנט האחרון הינו גוף הפונקציה, 
כל הארגומנטים הינם מחרוזות. 
להלן דוגמה:

<div dir="ltr">

```js
var add = new Function("first", "second", "return first + second");

console.log(add(1, 1));     // 2
```

</div>

אקמהסקריפט 6 מוסיפה לבנאי הפונקציה את האפשרות להוסיף ערכים דיפולטיביים ופרמטרים מסוג רסט. 
צריך רק להוסיף סימן
`=` 
וערך עבור שמות הפרמטרים, לדוגמה:

<div dir="ltr">

```js
var add = new Function("first", "second = first",
        "return first + second");

console.log(add(1, 1));     // 2
console.log(add(1));        // 2
```

</div>

בדוגמה זו, הפרמטר
`second` 
מקבל את ערכו של הפרמטר
`first` 
כאשר מועבר לפונקציה פרמטר בודד בלבד. 
צורת הכתיבה זהה כמו פונקציה רגילה

לגבי פרמטרים מסוג רסט, יש להוסיף 
`...` 
לפני הפרמטר האחרון, כמו בדוגמה הבא:

<div dir="ltr">

```js
var pickFirst = new Function("...args", "return args[0]");

console.log(pickFirst(1, 2));   // 1
```

</div>

הקוד בדוגמה יוצר פונקציה שמקבלת בפרמטר בודד מסוג רסט ומחזירה את הארגומנט הראשון שהועבר לפונקציה.

הוספת ערכים דיפולטיביים ופרמטרים מסוג רסט לבנאי הפונקציה מספקת לו את אותן יכולות הקיימות בצורה הרגילה להגדרת הפונקציה.

## אופרטור הפיזור

לאופרטור הפיזור
<span dir="ltr">(`spread`)</span>
קשר מסוים עם פרמטרים מסוג רסט. 
בעוד שפרמטרים מסוג רסט מאפשרים לחבר מספר ארגומנטים לתוך מערך, 
אופרטור הפיזור מאפשר לקבוע שמערך מסויים יפורק כך שכל עצמיו יועברו לפונקציה בתור ארגומנטים נפרדים. 
ראה דוגמה שמשתמש בפונקציה 
<span dir="ltr">`Math.max()`</span> 
שיכולה לקבל כל מספר של ארגומנטים ומחזירה את האחד בעל הערך המספרי הגבוה ביותר.

<div dir="ltr">

```js
let value1 = 25,
    value2 = 50;

console.log(Math.max(value1, value2));      // 50
```

</div>

כאשר מעבירים לפונקציה שני ערכים בלבד, כמו בדוגמה לעיל,
קל להשתמש בפונקציה 
<span dir="ltr">`Math.max()`</span>. 
 אך מה היה קורה אם היינו רוצים למצוא את הערך הגבוה ביותר בתוך מערך של עצמים?  
הפונקציה 
<span dir="ltr">`Math.max()`</span>. 
לא מקבלת מערכים, ולכן לפני אקמהסקריפט 6 היה צורך לבדוק את המערך בעצמנו או להשתמש ב
<span dir="ltr">`apply()`</span> 
כמו בדוגמה הבאה: 

<div dir="ltr">

```js
let values = [25, 50, 75, 100]

console.log(Math.max.apply(Math, values));  // 100
```

</div>

הקוד בדוגמה עובד אך השימוש ב
<span dir="ltr">`apply()`</span> 
בצורה זו יכול לבלבל. הוא גם מערפל את משמעות הקוד.

אופרטור הפיזור הופך את הקוד לפשוט יותר. 

במקום לקרוא לפונקציה 
<span dir="ltr">`apply()`</span> 
ניתן להעביר מערך לתוך 
<span dir="ltr">`Math.max()`</span>. 
ולהשתמש באותו תחביר 
`...` 
כמו זה שמשמש בפרמטרים מסוג רסט. 
המערך מפורק לארגומנטים בודדים שמועברים אחד אחד לפונקציה. 
כמו בדוגמה הבאה:

<div dir="ltr">

```js
let values = [25, 50, 75, 100]

// פועל באותו אופן כמו
// console.log(Math.max(25, 50, 75, 100));
console.log(Math.max(...values));           // 100
```

</div>

כעת הקריאה ל
<span dir="ltr">`Math.max()`</span> 
נראית יותר סדורה ונמנעת מן הצורך להגדיר במפורש את החיבור ל
`this` 
(שהוא הארגומנט הראשון בקריאה ל
<span dir="ltr">`Math.max.apply()`</span> 
בדוגמה הקודמת
)

ניתן לשלב את אופרטור הפיזור עם ארגומנטים אחרים. 
נניח שהיינו רוצים שלא יוחזר מספר קטן מ-0. 
ניתן להעביר את הערך הנ״ל בנפרד ועדיין להשתמש באופרטור הפיזור, לדוגמה:

<div dir="ltr">

```js
let values = [-25, -50, -75, -100]

console.log(Math.max(...values, 0));        // 0
```

</div>

בדוגמה האחרונה, הארגומנט האחרון שמועבר לפונקציה 
<span dir="ltr">`Math.max()`</span> 
הוא 
`0`, 
והוא בא לאחר שכל שאר הארגומנטים מועברים באמצעות אופרטור הפיזור.

אופרטור הפיזור מקל על שימוש במערכים בתור ארגומנטים לפונקציה. ברוב המקרים ניתן להחליף בו את השימוש בפונקציה 
<span dir="ltr">`apply()`</span> 

ניתן להשתמש באופרטור הפיזור גם עבור פונקציות שנוצרו באמצעות בנאי הפונקציה.

## התכונה name

זיהוי פונקציות יכול להפוך לדבר מאתגר בג׳אווהסקריפט בהתחשב במספר הדרכים שניתן להגדיר פונקציה.
בנוסף, השכיחות הגבוהה של פונקציות אנונימיות הופכת פתרון באגים לקשה יותר, 
מסיבות אלו, אקמהסקריפט 6 מוסיפה את התכונה
`name` 
לכל הפונקציות.

### בחירת שמות מתאימים

כל הפונקציות שרצות תחת אקמהסקריפט 6 יקבלו ערך עבור תכונת
`name`. 

ניתן לראות זאת בדוגמה הבאה שמדפיסה את תכונת 
`name`, 
עבור הגדרת פונקציה 
ועבור משתנה מסוג פונקציה.

<div dir="ltr">

```js
function doSomething() {
    // ...
}

var doAnotherThing = function() {
    // ...
};

console.log(doSomething.name);          // "doSomething"
console.log(doAnotherThing.name);       // "doAnotherThing"
```

</div>

בדוגמת הקוד האחרון לפונקציה 
<span dir="ltr">`doSomething()`</span>  
יש תכונת 
`name` 
בעל הערך
`"doSomething"` 
מכיוון שמדובר בהגדרת פונקציה רגילה. 

לפונקציה האנונימית 
<span dir="ltr">`doAnotherThing()`</span>  
יש תכונת 
`name` 
עם הערך 
`"doAnotherThing"` 
מכיוון שזהו שמו של המשתנה אליו מקושרת הפונקציה.

### מקרים מיוחדים של תכונת name

בעוד שמציאת שמות עבור פונקציות נהפכה לדבר קל, אקמהסקריפט 6 מרחיקה לכת ודואגת לכך 
*שכל* 
הפונקציות יהיו בעלות שם מתאים.
לדוגמה:

<div dir="ltr">

```js
var doSomething = function doSomethingElse() {
    // ...
};

var person = {
    get firstName() {
        return "Nicholas"
    },
    sayName: function() {
        console.log(this.name);
    }
}

console.log(doSomething.name);      // "doSomethingElse"
console.log(person.sayName.name);   // "sayName"

var descriptor = Object.getOwnPropertyDescriptor(person, "firstName");
console.log(descriptor.get.name); // "get firstName"
```

</div>

בדוגמה זו,
הערך של
<span dir="ltr">`doSomething.name`</span> 
הינו 
`"doSomethingElse"` 
מכיוון ולמשתנה עצמו ניתן אותו שם.
ערך 
`name` 
עבור 
<span dir="ltr">`person.sayName()`</span> 
הינו
`"sayName"`, 
מאחר וזהו שם המשתנה על האוביקט
`person` . 

המשתנה 
<span dir="ltr">`person.firstName`</span> 
הינו למעשה פונקציית גטר
<span dir="ltr">`(getter function)`</span> 
ולכן שמו יהיה
<span dir="ltr">`"get firstName"`</span>. 
לפונקציות סטר
<span dir="ltr">`(setter function)`</span> 
תופיע המילה 
`"set"` 
בהתאמה.
(במידה ונרצה לקבל גישה לפונקציה עצמה נצטרך להשתמש ב 
<span dir="ltr">`Object.getOwnPropertyDescriptor()`</span> 
)

ישנם כמה מקרים מיוחדים עבור שמות פונקציה. 
עבור פונקציות שנוצרו באמצעות
<span dir="ltr">`bind()`</span> 
תופיע הקידומת  
`"bound"` 
ועבור פונקציות שנוצרו באמצעות הבנאי
`Function` 
תופיע הקידומת 
`"anonymous"`, 
כמו בדוגמה הבאה:

<div dir="ltr">

```js
var doSomething = function() {
    // ...
};

console.log(doSomething.bind().name);   // "bound doSomething"

console.log((new Function()).name);     // "anonymous"
```

</div>

השם של פונקציה שעברה דרך
<span dir="ltr">`bind()`</span> 
תמיד יהיה ערך התכונה 
`name` 
של הפונקציה המקורית בתוספת הקידומת 
<span dir="ltr">`"bound "`</span>, 
ולכן שמה של הגרסה החדשה של 
<span dir="ltr">`doSomething()`</span> 
לאחר שעברה דרך 
<span dir="ltr">`bind()`</span> 
יהיה 
<span dir="ltr">`"bound doSomething"`</span> 

חשוב לזכור שהערך של תכונת 
`name`
של פונקציה לא בהכרח משקף משתנה בעל אותו שם.
תכונת
`name`
אמורה לספק מידע, לעזור בפתרון באגים, ולכן 
אינה יכולה לשמש כדי להשיג גישה לפונקציה עצמה.

## הפרדה ברורה בין שתי תכליות הפונקציה

עד וכולל אקמהסקריפט 5, לפונקציות הייתה תכלית כפולה לפיה היה ניתן לקרוא לפונקציה עם או בלי האופרטור 
`new`. 
כאשר נקראה הפונקציה בעזרת 
`new`, 
הדבר הפך את ערך המשתנה 
`this` 
בתוך הפונקציה לאוביקט חדש שמוחזר מהפונקציה. 
כפי שמודגם בדוגמת הקוד הבאה:

<div dir="ltr">

```js
function Person(name) {
    this.name = name;
}

var person = new Person("Nicholas");
var notAPerson = Person("Nicholas");

console.log(person);        // "[Object object]"
console.log(notAPerson);    // "undefined"
```

</div>

כאשר יוצרים את המשתנה 
`notAPerson`, 
קריאה לפונקציה 
<span dir="ltr">`Person()`</span> 

ללא שימוש ב 
`new` 
מחזירה את הערך 
`undefined`
(
    וגם מגדירה את תכונת
`name`
    על האוביקט הגלובלי במצב לא קשיח
). 
השימוש באות ראשונה גדולה 
(`capitalization`) 
של הפונקציה 
`Person` 
נותן לנו אינדיקציה שהפונקציה מיועדת לקריאה באמצעות 
`new`, 
כפי שנפוץ אצל מפתחי ג׳אווהסקריפט רבים. 
הבלבול הזה, לגבי התכלית הכפולה של פונקציות הוביל למספר שינויים באקמהסקריפט 6.

בג׳אווהסקריפט קיימות שתי פונקציות פנימיות, שאינן חשופות למפתחים, עבור פונקציות:
<span dir="ltr">`[[Call]]`</span> 
ו 
<span dir="ltr">`[[Construct]]`</span>. 
כאשר פונקציה נקראת ללא האופרטור 
`new`, 
מופעלת הפונקציה
<span dir="ltr">`[[Call]]`</span> 
והיא מריצה את הפונקציה כפי שהיא כתובה. 
כאשר הפונקציה נקראת עם 
`new`, 
מופעלת הפונקציה
<span dir="ltr">`[[Construct]]`</span>. 
והיא אחראית ליצירת אוביקט חדש, 
שנקרא 
*אוביקט המטרה* 
<span dir="ltr">`(new target)`</span> 
וממשיכה להריץ את הפונקציה כמות שהיא כתובה ורק המשתנה המיוחד 
 `this`
מצביע על
אוביקט המטרה. 
פונקציות שמכילות פונקציה פנימית 
<span dir="ltr">`[[Construct]]`</span> 
*נקראות *קונסטרקטורים.

I> חשוב לשים לב לכך שלא לכל הפונקציות יש פונקציה פנימית 
<span dir="ltr">`[[Construct]]`</span>, 
ולכן לא כל הפונקציות יכולות להיקרא בעזרת 
`new`. 
פונקציות מקוצרות, שידובר עליהן בהרבה תחת פרק 
״פונקציות מקוצרות״, 
אינן בעלות פונקציית 
<span dir="ltr">`[[Construct]]`</span> 
פנימית.

### בדיקת דרך הקריאה לפונקציה באקמהסקריפט 5

השיטה הנפוצה ביותר לבדוק האם פונקציה נקראה באמצעות האופרטור 
`new` 
(ומכאן על ידי קונסטרקטור)
הייתה בעזרת 
`instanceof`, 
לדוגמה:

<div dir="ltr">

```js
function Person(name) {
    if (this instanceof Person) {
        this.name = name;   // new
    } else {
        throw new Error("You must use new with Person.")
    }
}

var person = new Person("Nicholas");
var notAPerson = Person("Nicholas");  // error
```

</div>

בדוגמה זו, ערכו של המשתנה 
`this` 
נבדק כדי לבדוק האם מדובר במופע של הקונסטרקטור
`Person`, 
ואם כן,
הקוד ממשיך לרוץ כרגיל. 
במידה ו 
`this` 
איננו מופע של 
`Person` 
נזרקת שגיאה. 
הקוד עובד מפני שפונקציית
<span dir="ltr">`[[Construct]]`</span> 
יוצרת מופע חדש של 
`Person` 
ומבצעת השמה שלו למשתנה 
`this`. 
לרוע המזל, גישה זו אינה אמינה מכיוון והמשתנה 
`this` 
יכול להיות מופע של 
`Person` 
גם ללא שימוש ב
`new`, 
לדוגמה:

<div dir="ltr">

```js
function Person(name) {
    if (this instanceof Person) {
        this.name = name;   // using new
    } else {
        throw new Error("You must use new with Person.")
    }
}

var person = new Person("Nicholas");
var notAPerson = Person.call(person, "Michael");    // works!
```

</div>

הקריאה לפונקציה
<span dir="ltr">`Person.call()`</span> 
מעבירה את המשתנה 
`person` 
בתור הארגומנט הראשון, 
ולכן 
`this`
מצביע על
`person`
בתוך פונקציית הקונסטרקטור 
`Person`.
להפונקציה 
`Person`
אין דרך לדעת אם הפונקציה נקראה בדרך זו או בעזרת 
`new`.

### new.target

כדי להתגבר על בעיה זו, הוסיפו באקמהסקריפט 6 את 
*מטה-תכונה* בשם
<span dir="ltr"> `new.target`</span> 
מטה-תכונה היא שאינה שייכות לאוביקט ומספק מידע מוסף הקשור לבעליו 
(במקרה זה -
`new`). 
כאשר נקראת הפונקציה 
<span dir="ltr">`[[Construct]]`</span> 
הערך של 
<span dir="ltr">`new.target`</span> 
מצביע על המטרה שעליה הופעלל אופרטור 
`new`. 
המטרה היא בדרך כלל פונקציית הקונסטרקטור של האוביקט שיפעל בתור 
`this`
בתוך גוף הפונקציה כאשר היא רצה. 
במידה ומופעלת הפונקציה 
<span dir="ltr">`[[Call]]`</span> 
אזי 
`new.target`
תקבל את הערך 
`undefined`.

מטה-תכונה חדשה זו מאפשרת לנו לבדוק האם פונקציה נקראה בעזרת 
בדיקת הערך עבור
<span dir="ltr">`new.target`</span>  
לדוגמה:

<div dir="ltr">

```js
function Person(name) {
    if (typeof new.target !== "undefined") {
        this.name = name;   // new
    } else {
        throw new Error("You must use new with Person.")
    }
}

var person = new Person("Nicholas");
var notAPerson = Person.call(person, "Michael");    // error!
```

</div>

בעזרת שימוש ב
<span dir="ltr">`new.target`</span>  
במקום ב
<span dir="ltr">`this instanceof Person`</span>   
הקונסטרקטור
`Person`
זורק כעת שגיאה כאשר מפעילים אותו ללא 
`new`. 

כמו כן, ניתן לבדוק באמצעות שימוש ב
<span dir="ltr">`new.target`</span>  
קריאה לקונסטרקטור ספציפי.
לדוגמה:

<div dir="ltr">

```js
function Person(name) {
    if (new.target === Person) {
        this.name = name;   // using new
    } else {
        throw new Error("You must use new with Person.")
    }
}

function AnotherPerson(name) {
    Person.call(this, name);
}

var person = new Person("Nicholas");
var anotherPerson = new AnotherPerson("Nicholas");  // error!
```

</div>

בקוד שבדוגמה הערך עבור 
<span dir="ltr">`new.target`</span>  
חייב להיות 
`Person` 
על מנת לעבוד.
לאחר הקריאה ל
<span dir="ltr">`new AnotherPerson("Nicholas")`</span>  
הקריאה הבאה 
<span dir="ltr">`Person.call(this, name)` </span>  
זורקת שגיאה מפני ש 
<span dir="ltr">`new.target`</span>  
מקבלת את הערך 
`undefined`
בתוך הקונסטרקטור
`Person` 
(שנקרא בלי שימוש ב 
`new`).



W> Warning: Using `new.target` outside of a function is a syntax error.
W> אזהרה: 
כל שימוש ב 
<span dir="ltr">`new.target`</span>  
מחוץ לפונקציה יזרוק שגיאת תחביר 
<span dir="ltr">(syntax error)</span>  

באמצעות שימוש ב
<span dir="ltr">`new.target`</span>  
אקמהסקריפט 6 עזרה להבדיל בין דרכים שונות לקריאת פונקציה.
אקמהסקריפט 6 המשיכה בנוסף לחלק אחר שאינו ברור של השפה: הגדרת הפונקציה בתוך בלוק של קוד.

## פונקציות ברמת בלוק של קוד

בגרסת אקמהסקריפט 3 וקודם לכן, הגדרת פונקציה בתוך בלוק
*פונקציה ברמת בלוק של קוד*
הייתה נחשבת לשגיאה תחבירית מבחינה טכנית, 
ובכל זאת דפדפנים תמכו בכך. 
לרוע המזל, כל דפדפן שאיפשר את התחביר התנהג בצורה שונה במקצת, ולכן עדיף להימנע מהגדרת פונקציה בתוך בלוק של קוד 
(האלטרנטיבה הטובה ביותר היא לכתוב פונקציה בתור ביטוי קוד - 
`expression`
).

בניסיון להשתלט על הבעיה, מהדורה 5 של אקמהסקריפט זרקה שגיאה בכל פעם שפונקציה הייתה מוגדרת בתוך בלוק אך במצב קשיח בלבד:

<div dir="ltr">

```js
"use strict";

if (true) {

    // נזרקת שגיאה באקמהסקריפט 5 אך לא באקמהסקריפט 6
    function doSomething() {
        // ...
    }
}
```

</div>

באקמהסקריפט 5, הקוד זורק שגיאה תחבירית. 
באקמהסקריפט 6, הפונקציה 
<span dir="ltr">`doSomething()`</span>  
נחשבת למוגדרת ברמת בלוק ויכולה להיקרא בתוך אותו בלוק בה הוגדרה. 
לדוגמה: 

<div dir="ltr">

```js
"use strict";

if (true) {

    console.log(typeof doSomething);        // "function"

    function doSomething() {
        // ...
    }

    doSomething();
}

console.log(typeof doSomething);            // "undefined"
```

</div>

פונקציות ברמת בלוק מורמות לתחילת הבלוק שבו הוגדרו, ולכן הקוד 
<span dir="ltr">`typeof doSomething`</span>  
מחזיר לנו
`"function"` 
למרות שהוא מופיע לפני הגדרת הפונקציה בקוד. 
ברגע שמגיעים לסוף הבלוק  
`if` 
שבתוכו הוגדרה הפונקציה 
<span dir="ltr">`doSomething()`</span>  
היא אינה קיימת יותר

### מתי להשתמש בפונקציות ברמת בלוק

פונקציות ברמת בלוק דומות למשתנים מסוג 
`let` 
בכך שהפונקציה מפסיקה להתקיים ברגם שהקוד הרץ יוצא מתחומי הבלוק בו היא הוגדרה. 
ההבדל העיקרי הוא שהגדרות פונקציה תמיד מורמות לתחילת הבלוק העוטף. 
ביטויי פונקציה
<span dir="ltr">`(function expressions)`</span>  
שמוגדרים בעזרת 
אינם מורמים, כפי שמדגימה הדוגמה הבאה:

<div dir="ltr">

```js
"use strict";

if (true) {

    console.log(typeof doSomething);        // שגיאה

    let doSomething = function () {
        // ...
    }

    doSomething();
}

console.log(typeof doSomething);
```

</div>

בדוגמה זו, הקוד נעצר בעת הרצת הפקודה 
<span dir="ltr">`typeof doSomething`</span>  
מאחר והפונקציה  
<span dir="ltr">`doSomething()`</span>  
משויכת למשתנה מסוג 
`let` 
שעוד לא אותחל. 
ומכאן הפונקציה 
<span dir="ltr">`doSomething`</span> 
נמצאת באותו רגע באזור המת באופן זמני
<span dir="ltr">`(TDZ)`</span>  
ולכן נזרקת שגיאה. 

באפשרותנו לבחור כיצד להגדיר פונקציות 
בתוך בלוק על פי השאלה האם ברצוננו הרמה של הפונקציה או לא

### פונקציות ברמת בלוק במצב לא קשיח

אקמהסקריפט 6 מתירה פונקציות ברמת בלוק במצב לא קשיח, אך ההתנהגות שונה במקצת.
במקום להרים את הגדרת הפונקציה לתחילת הבלוק הן מורמות לתחילת הפונקציה העוטפת, ובמקרה שאין פונקציה עוטפת - לתחילת הסביבה הגלובלית.
לדוגמה:

<div dir="ltr">


```js
// ECMAScript 6 
if (true) {

    console.log(typeof doSomething);        // "function"

    function doSomething() {
        // ...
    }

    doSomething();
}

console.log(typeof doSomething);            // "function"
```

</div>

בדוגמה זו, 
<span dir="ltr">`doSomething()`</span> 
מורמת לסביבה הגלובלית כך שהפונקציה עדיין קיימת מעבר לגבולות בלוק ה 
`if`.
אקמהסקריפט 6 אוכפת התנהגות זו על מנת להסדיר ולאחד את התנהגויות הדפדפנים השונות שהיו קיימות מקודם.


Allowing block-level functions improves your ability to declare functions in JavaScript, but ECMAScript 6 also introduced a completely new way to declare functions.

## Arrow Functions

One of the most interesting new parts of ECMAScript 6 is the *arrow function*. Arrow functions are, as the name suggests, functions defined with a new syntax that uses an "arrow" (`=>`). But arrow functions behave differently than traditional JavaScript functions in a number of important ways:

* **No `this`, `super`, `arguments`, and `new.target` bindings** - The value of `this`, `super`, `arguments`, and `new.target` inside of the function is by the closest containing nonarrow function. (`super` is covered in Chapter 4.)
* **Cannot be called with `new`** - Arrow functions do not have a `[[Construct]]` method and therefore cannot be used as constructors. Arrow functions throw an error when used with `new`.
* **No prototype** - since you can't use `new` on an arrow function, there's no need for a prototype. The `prototype` property of an arrow function doesn't exist.
* **Can't change `this`** - The value of `this` inside of the function can't be changed. It remains the same throughout the entire lifecycle of the function.
* **No `arguments` object** - Since arrow functions have no `arguments` binding, you must rely on named and rest parameters to access function arguments.
* **No duplicate named parameters** - arrow functions cannot have duplicate named parameters in strict or nonstrict mode, as opposed to nonarrow functions that cannot have duplicate named parameters only in strict mode.

There are a few reasons for these differences. First and foremost, `this` binding is a common source of error in JavaScript. It's very easy to lose track of the `this` value inside a function, which can result in unintended program behavior, and arrow functions eliminate this confusion. Second, by limiting arrow functions to simply executing code with a single `this` value, JavaScript engines can more easily optimize these operations, unlike regular functions, which might be used as a constructor or otherwise modified.

The rest of the differences are also focused on reducing errors and ambiguities inside of arrow functions. By doing so, JavaScript engines are better able to optimize arrow function execution.

I> Note: Arrow functions also have a `name` property that follows the same rule as other functions.

### Arrow Function Syntax

The syntax for arrow functions comes in many flavors depending upon what you're trying to accomplish. All variations begin with function arguments, followed by the arrow, followed by the body of the function. Both the arguments and the body can take different forms depending on usage. For example, the following arrow function takes a single argument and simply returns it:

```js
var reflect = value => value;

// effectively equivalent to:

var reflect = function(value) {
    return value;
};
```

When there is only one argument for an arrow function, that one argument can be used directly without any further syntax. The arrow comes next and the expression to the right of the arrow is evaluated and returned. Even though there is no explicit `return` statement, this arrow function will return the first argument that is passed in.

If you are passing in more than one argument, then you must include parentheses around those arguments, like this:

```js
var sum = (num1, num2) => num1 + num2;

// effectively equivalent to:

var sum = function(num1, num2) {
    return num1 + num2;
};
```

The `sum()` function simply adds two arguments together and returns the result. The only difference between this arrow function and the `reflect()` function is that the arguments are enclosed in parentheses with a comma separating them (like traditional functions).

If there are no arguments to the function, then you must include an empty set of parentheses in the declaration, as follows:

```js
var getName = () => "Nicholas";

// effectively equivalent to:

var getName = function() {
    return "Nicholas";
};
```

When you want to provide a more traditional function body, perhaps consisting of more than one expression, then you need to wrap the function body in braces and explicitly define a return value, as in this version of `sum()`:

```js
var sum = (num1, num2) => {
    return num1 + num2;
};

// effectively equivalent to:

var sum = function(num1, num2) {
    return num1 + num2;
};
```

You can more or less treat the inside of the curly braces the same as you would in a traditional function, with the exception that `arguments` is not available.

If you want to create a function that does nothing, then you need to include curly braces, like this:

```js
var doNothing = () => {};

// effectively equivalent to:

var doNothing = function() {};
```

Curly braces are used to denote the function's body, which works just fine in the cases you've seen so far. But an arrow function that wants to return an object literal outside of a function body must wrap the literal in parentheses. For example:

```js
var getTempItem = id => ({ id: id, name: "Temp" });

// effectively equivalent to:

var getTempItem = function(id) {

    return {
        id: id,
        name: "Temp"
    };
};
```

Wrapping the object literal in parentheses signals that the braces are an object literal instead of the function body.

### Creating Immediately-Invoked Function Expressions

One popular use of functions in JavaScript is creating immediately-invoked function expressions (IIFEs). IIFEs allow you to define an anonymous function and call it immediately without saving a reference. This pattern comes in handy when you want to create a scope that is shielded from the rest of a program. For example:

```js
let person = function(name) {

    return {
        getName: function() {
            return name;
        }
    };

}("Nicholas");

console.log(person.getName());      // "Nicholas"
```

In this code, the IIFE is used to create an object with a `getName()` method. The method uses the `name` argument as the return value, effectively making `name` a private member of the returned object.

You can accomplish the same thing using arrow functions, so long as you wrap the arrow function in parentheses:

```js
let person = ((name) => {

    return {
        getName: function() {
            return name;
        }
    };

})("Nicholas");

console.log(person.getName());      // "Nicholas"
```

Note that the parentheses are only around the arrow function definition, and not around `("Nicholas")`. This is different from a formal function, where the parentheses can be placed outside of the passed-in parameters as well as just around the function definition.

### No this Binding

One of the most common areas of error in JavaScript is the binding of `this` inside of functions. Since the value of `this` can change inside a single function depending on the context in which the function is called, it's possible to mistakenly affect one object when you meant to affect another. Consider the following example:

```js
var PageHandler = {

    id: "123456",

    init: function() {
        document.addEventListener("click", function(event) {
            this.doSomething(event.type);     // error
        }, false);
    },

    doSomething: function(type) {
        console.log("Handling " + type  + " for " + this.id);
    }
};
```

In this code, the object `PageHandler` is designed to handle interactions on the page. The `init()` method is called to set up the interactions, and that method in turn assigns an event handler to call `this.doSomething()`. However, this code doesn't work exactly as intended.

The call to `this.doSomething()` is broken because `this` is a reference to the object that was the target of the event (in this case `document`), instead of being bound to `PageHandler`. If you tried to run this code, you'd get an error when the event handler fires because `this.doSomething()` doesn't exist on the target `document` object.

You could fix this by binding the value of `this` to `PageHandler` explicitly using the `bind()` method on the function instead, like this:

```js
var PageHandler = {

    id: "123456",

    init: function() {
        document.addEventListener("click", (function(event) {
            this.doSomething(event.type);     // no error
        }).bind(this), false);
    },

    doSomething: function(type) {
        console.log("Handling " + type  + " for " + this.id);
    }
};
```

Now the code works as expected, but it may look a little bit strange. By calling `bind(this)`, you're actually creating a new function whose `this` is bound to the current `this`, which is `PageHandler`. To avoid creating an extra function, a better way to fix this code is to use an arrow function.

Arrow functions have no `this` binding, which means the value of `this` inside an arrow function can only be determined by looking up the scope chain. If the arrow function is contained within a nonarrow function, `this` will be the same as the containing function; otherwise, `this` is equivalent to the value of `this` in the global scope. Here's one way you could write this code using an arrow function:

```js
var PageHandler = {

    id: "123456",

    init: function() {
        document.addEventListener("click",
                event => this.doSomething(event.type), false);
    },

    doSomething: function(type) {
        console.log("Handling " + type  + " for " + this.id);
    }
};
```

The event handler in this example is an arrow function that calls `this.doSomething()`. The value of `this` is the same as it is within `init()`, so this version of the code works similarly to the one using `bind(this)`. Even though the `doSomething()` method doesn't return a value, it's still the only statement executed in the function body, and so there is no need to include braces.

Arrow functions are designed to be "throwaway" functions, and so cannot be used to define new types; this is evident from the missing `prototype` property, which regular functions have. If you try to use the `new` operator with an arrow function, you'll get an error, as in this example:

```js
var MyType = () => {},
    object = new MyType();  // error - you can't use arrow functions with 'new'
```

In this code, the call to `new MyType()` fails because `MyType` is an arrow function and therefore has no `[[Construct]]` behavior. Knowing that arrow functions cannot be used with `new` allows JavaScript engines to further optimize their behavior.

Also, since the `this` value is determined by the containing function in which the arrow function is defined, you cannot change the value of `this` using `call()`, `apply()`, or `bind()`.

### Arrow Functions and Arrays

The concise syntax for arrow functions makes them ideal for use with array processing, too. For example, if you want to sort an array using a custom comparator, you'd typically write something like this:

```js
var result = values.sort(function(a, b) {
    return a - b;
});
```

That's a lot of syntax for a very simple procedure. Compare that to the more terse arrow function version:

```js
var result = values.sort((a, b) => a - b);
```

The array methods that accept callback functions such as `sort()`, `map()`, and `reduce()` can all benefit from simpler arrow function syntax, which changes seemingly complex processes into simpler code.

### No arguments Binding

Even though arrow functions don't have their own `arguments` object, it's possible for them to access the `arguments` object from a containing function. That `arguments` object is then available no matter where the arrow function is executed later on. For example:

```js
function createArrowFunctionReturningFirstArg() {
    return () => arguments[0];
}

var arrowFunction = createArrowFunctionReturningFirstArg(5);

console.log(arrowFunction());       // 5
```

Inside `createArrowFunctionReturningFirstArg()`, the `arguments[0]` element is referenced by the created arrow function. That reference contains the first argument passed to the `createArrowFunctionReturningFirstArg()` function. When the arrow function is later executed, it returns `5`, which was the first argument passed to `createArrowFunctionReturningFirstArg()`. Even though the arrow function is no longer in the scope of the function that created it, `arguments` remains accessible due to scope chain resolution of the `arguments` identifier.

### Identifying Arrow Functions

Despite the different syntax, arrow functions are still functions, and are identified as such. Consider the following code:

```js
var comparator = (a, b) => a - b;

console.log(typeof comparator);                 // "function"
console.log(comparator instanceof Function);    // true
```

The `console.log()` output reveals that both `typeof` and `instanceof` behave the same with arrow functions as they do with other functions.

Also like other functions, you can still use `call()`, `apply()`, and `bind()` on arrow functions, although the `this`-binding of the function will not be affected. Here are some examples:

```js
var sum = (num1, num2) => num1 + num2;

console.log(sum.call(null, 1, 2));      // 3
console.log(sum.apply(null, [1, 2]));   // 3

var boundSum = sum.bind(null, 1, 2);

console.log(boundSum());                // 3
```

The `sum()` function is called using `call()` and `apply()` to pass arguments, as you'd do with any function. The `bind()` method is used to create `boundSum()`, which has its two arguments bound to `1` and `2` so that they don't need to be passed directly.

Arrow functions are appropriate to use anywhere you're currently using an anonymous function expression, such as with callbacks. The next section covers another major ECMAScript 6 development, but this one is all internal, and has no new syntax.

## Tail Call Optimization

Perhaps the most interesting change to functions in ECMAScript 6 is an engine optimization, which changes the tail call system. A *tail call* is when a function is called as the last statement in another function, like this:

```js
function doSomething() {
    return doSomethingElse();   // tail call
}
```

Tail calls as implemented in ECMAScript 5 engines are handled just like any other function call: a new stack frame is created and pushed onto the call stack to represent the function call. That means every previous stack frame is kept in memory, which is problematic when the call stack gets too large.

### What's Different?

ECMAScript 6 seeks to reduce the size of the call stack for certain tail calls in strict mode (nonstrict mode tail calls are left untouched). With this optimization, instead of creating a new stack frame for a tail call, the current stack frame is cleared and reused so long as the following conditions are met:

1. The tail call does not require access to variables in the current stack frame (meaning the function is not a closure)
1. The function making the tail call has no further work to do after the tail call returns
1. The result of the tail call is returned as the function value

As an example, this code can easily be optimized because it fits all three criteria:

```js
"use strict";

function doSomething() {
    // optimized
    return doSomethingElse();
}
```

This function makes a tail call to `doSomethingElse()`, returns the result immediately, and doesn't access any variables in the local scope. One small change, not returning the result, results in an unoptimized function:

```js
"use strict";

function doSomething() {
    // not optimized - no return
    doSomethingElse();
}
```

Similarly, if you have a function that performs an operation after returning from the tail call, then the function can't be optimized:


```js
"use strict";

function doSomething() {
    // not optimized - must add after returning
    return 1 + doSomethingElse();
}
```

This example adds the result of `doSomethingElse()` with 1 before returning the value, and that's enough to turn off optimization.

Another common way to inadvertently turn off optimization is to store the result of a function call in a variable and then return the result, such as:

```js
"use strict";

function doSomething() {
    // not optimized - call isn't in tail position
    var result = doSomethingElse();
    return result;
}
```

This example cannot be optimized because the value of `doSomethingElse()` isn't immediately returned.

Perhaps the hardest situation to avoid is in using closures. Because a closure has access to variables in the containing scope, tail call optimization may be turned off. For example:

```js
"use strict";

function doSomething() {
    var num = 1,
        func = () => num;

    // not optimized - function is a closure
    return func();
}
```

The closure `func()` has access to the local variable `num` in this example. Even though the call to `func()` immediately returns the result, optimization can't occur due to referencing the variable `num`.

### How to Harness Tail Call Optimization

In practice, tail call optimization happens behind-the-scenes, so you don't need to think about it unless you're trying to optimize a function. The primary use case for tail call optimization is in recursive functions, as that is where the optimization has the greatest effect. Consider this function, which computes factorials:

```js
function factorial(n) {

    if (n <= 1) {
        return 1;
    } else {

        // not optimized - must multiply after returning
        return n * factorial(n - 1);
    }
}
```

This version of the function cannot be optimized, because multiplication must happen after the recursive call to `factorial()`. If `n` is a very large number, the call stack size will grow and could potentially cause a stack overflow.

In order to optimize the function, you need to ensure that the multiplication doesn't happen after the last function call. To do this, you can use a default parameter to move the multiplication operation outside of the `return` statement. The resulting function carries along the temporary result into the next iteration, creating a function that behaves the same but *can* be optimized by an ECMAScript 6 engine. Here's the new code:

```js
function factorial(n, p = 1) {

    if (n <= 1) {
        return 1 * p;
    } else {
        let result = n * p;

        // optimized
        return factorial(n - 1, result);
    }
}
```

In this rewritten version of `factorial()`, a second argument `p` is added as a parameter with a default value of 1. The `p` parameter holds the previous multiplication result so that the next result can be computed without another function call. When `n` is greater than 1, the multiplication is done first and then passed in as the second argument to `factorial()`. This allows the ECMAScript 6 engine to optimize the recursive call.

Tail call optimization is something you should think about whenever you're writing a recursive function, as it can provide a significant performance improvement, especially when applied in a computationally-expensive function.

## Summary

Functions haven't undergone a huge change in ECMAScript 6, but rather, a series of incremental changes that make them easier to work with.

Default function parameters allow you to easily specify what value to use when a particular argument isn't passed. Prior to ECMAScript 6, this would require some extra code inside the function, to both check for the presence of arguments and assign a different value.

Rest parameters allow you to specify an array into which all remaining parameters should be placed. Using a real array and letting you indicate which parameters to include makes rest parameters a much more flexible solution than `arguments`.

The spread operator is a companion to rest parameters, allowing you to deconstruct an array into separate parameters when calling a function. Prior to ECMAScript 6, there were only two ways to pass individual parameters contained in an array: by manually specifying each parameter or using `apply()`. With the spread operator, you can easily pass an array to any function without worrying about the `this` binding of the function.

The addition of the `name` property should help you more easily identify functions for debugging and evaluation purposes. Additionally, ECMAScript 6 formally defines the behavior of block-level functions so they are no longer a syntax error in strict mode.

In ECMAScript 6, the behavior of a function is defined by `[[Call]]`, normal function execution, and `[[Construct]]`, when a function is called with `new`. The `new.target` metaproperty also allows you to determine if a function was called using `new` or not.

The biggest change to functions in ECMAScript 6 was the addition of arrow functions. Arrow functions are designed to be used in place of anonymous function expressions. Arrow functions have a more concise syntax, lexical `this` binding, and no `arguments` object. Additionally, arrow functions can't change their `this` binding, and so can't be used as constructors.

Tail call optimization allows some function calls to be optimized in order to keep a smaller call stack, use less memory, and prevent stack overflow errors. This optimization is applied by the engine automatically when it is safe to do so, however, you may decide to rewrite recursive functions in order to take advantage of this optimization.

</div>