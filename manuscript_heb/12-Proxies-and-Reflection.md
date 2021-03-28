<div dir="rtl">

# פרוקסי וריפלקשן
<span dir="ltr">(Proxies and the Reflection API)</span>

ECMAScript 5
ו
ECMAScript 6
פותחו תוך כדי חשיבה על התרת המיסטיקה של השפה.
לדוגמה,
סביבת ג'אווהסקריפט
הכילו תכונות אובייקט
שהיו לא אינומרביליות וניתנות לקריאה בלבד
עוד לפני גרסת
ECMAScript 5
אך מפתחים לא יכלו ליצור תכונות כאלו בעצמם.
ECMAScript 5
כללה את המתודה
<span dir="ltr">`Object.defineProperty()`</span>
על מנת לאפשר למפתחים לעשות מה שמנוע הג'אווהסקריפט יכול היה לעשות ממילא.

ECMAScript 6
נתנה למפתחים גישה רחבה יותר ליכולות בשפה שעד אז היו מוגבלות לאובייקטים מובנים בלבד.
השפה חושפת עבורנו את מגנוני הפעולה הפנימיים של האובייקט דרך
אובייקטים מסוג
*פרוקסי*
(*proxies*),
שפועלים כמעין מעטפת שיכולה ליירט ולשנות אופרציות ברמה הבסיסית של מנוע הג'אווהסקריפט. הפרק הזה יתחיל בתיאור הבעיה שפרוקסי בא לפתור ,ואז יראה כיצד ליצור פרוקסי וכיצד ניתן להשתמש בו בצורה יעילה.

## בעיית המערך

אובייקט מסוג מערך מתנהג באופן שמפתחים לא היו מסוגלים לחקות באובייקטים שלהם לפני גרסת
ECMASCript 6.
התכונה
<span dir="ltr">`length`</span>
של המערך משתנה
כאשר מבוצעת השמה של ערך למערך, וניתן גם לשנות את המערך על ידי שינוי התכונה
<span dir="ltr">`length`</span> .
לדוגמה:

<div dir="ltr">

```js
let colors = ["red", "green", "blue"];

console.log(colors.length);         // 3

colors[3] = "black";

console.log(colors.length);         // 4
console.log(colors[3]);             // "black"

colors.length = 2;

console.log(colors.length);         // 2
console.log(colors[3]);             // undefined
console.log(colors[2]);             // undefined
console.log(colors[1]);             // "green"
```

</div>

מערך
`colors`
מתחיל עם שלושה פריטים. השמת הערך
`"black"`
ל
`colors[3]`
מעלה את ערך התכונה
`length`
ל
`4`.
שינוי ערך התכונה
`length`
ל
`2`
ימחק את שני הפריטים האחרונים במערך, וישאיר רק את השניים הראשונים.
לא הייתה שום דרך בגרסת
ECMAScript 5
שתאפשר למפתח להשיג את היכולות הללו, אבל פרוקסי משנה זאת.

I> התנהגות לא סטנדרטית זו היא הסיבה שמערכים נחשבים לאובייקטים אקזוטיים ב
ECMAScript 6.

## מהם פרוקסי וריפלקשן?

ניתן לייצר פרוקסי
ולהשתמש בו במקום אובייקט אחר
(
אשר נקרא לרוב אובייקט
*מטרה*
/
*target*
/
*טרגט*
)
על ידי קריאה לקונסטרקטור
<span dir="ltr">`new Proxy()`</span>.
הפרוקסי
*ידמה*
(*virtualize*)
את המטרה בצורה כזו שהן הפרוקסי והן אובייקט המטרה נחשבים לאותו אובייקט עבור קוד שפועל עליהם.

אובייקטים מסוג פרוקסי מאפשרים לנו ליירט פעולות בסיסיות על אובייקט המטרה, שבמצב רגיל מנוהלות רק ע"י מנוע הג'אווהסקריפט. פעולות בסיסיות אלו מיורטות באמצעות
*מלכודת*
שהיא השם לפונקציה המגיבה לאותה פעולה ספציפית.

ממשק ההתבוננות / ריפלקשן
<span dir="ltr">(`reflection API`)</span>,
שמיוצג על ידי אובייקט בשם
`Reflect` ,
הינו למעשה אוסף של מתודות שמספקות לנו את ההתנהגויות הדיפולטיביות של אותה רמה נמוכה שפרוקסי יכול לשנות. קיימת מתודה על האובייקט
`Reflect`
עבור כל מלכודת פרוקסי. המתודות האלו בעלות אותו שם,
והן מקבלות את אותם ארגומנטים כמו מלכודת הפרוקסי התואמת להן.
טבלה
11-1
מסכמת אותן.

<div id="table-11" dir="ltr" style="unicode-bidi: plaintext">
<style>
    #table-11 td { unicode-bidi: plaintext; }
</style>

{title="טבלה 11-1: מלכודות פרוקסי בג׳אווהסקריפט"}

| מלכודת פרוקסי    |             התנהגות נעקפת |       התנהגות דיפולטיבית |
|--------------------------|---------------------------|------------------|
|`get`                     |           קריאת ערך תכונה | `Reflect.get()` |
|`set`                     |           כתיבת ערך תכונה | `Reflect.set()` |
|`has`                     |            אופרטור `in`   | `Reflect.has()` |
|`deleteProperty`          |          אופרטור `delete` | `Reflect.deleteProperty()` |
|`getPrototypeOf`          | `Object.getPrototypeOf()` | `Reflect.getPrototypeOf()` |
|`setPrototypeOf`          | `Object.setPrototypeOf()` | `Reflect.setPrototypeOf()` |
|`isExtensible`            | `Object.isExtensible()`   | `Reflect.isExtensible()` |
|`preventExtensions`       | `Object.preventExtensions()` | `Reflect.preventExtensions()` |
|`getOwnPropertyDescriptor`| `Object.getOwnPropertyDescriptor()` | `Reflect.getOwnPropertyDescriptor()` |
|`defineProperty`          | `Object.defineProperty()` | `Reflect.defineProperty` |
|`ownKeys`                 | `Object.keys`, `Object.getOwnPropertyNames()`, `Object.getOwnPropertySymbols()` | `Reflect.ownKeys()` |
|`apply`                   | קריאה לפונקציה            | `Reflect.apply()`     |
|`construct`               | קריאה לפונקציה `new`      | `Reflect.construct()` |

</div>


כל מלכודת עוקפת התנהגות מובנית באובייקט של ג'אווהסקריפט, ומאפשרת ליירט ולשנות את התתנהגות הדיפולטיבית.
אם צריך עדיין את ההתנהגות הדיפולטיבית ,ניתן להשתמש במתודה התואמת בממשק הריפלקשן
היחסים בין פרוקסי לבין ממשק הריפלקשן
נהיים ברורים כאשר מתחילים להשתמש בפרוקסי, ולכן עדיף לצלול פנימה ולהסתכל על כמה דוגמאות.

I> באפיון המקורי של
ECMAScript 6
הייתה מלכודת נוספת שנקראה
`enumerate`
שנועדה לשנות את האופן שבו מתבצעת הספירה של תכונות בעזרת לולאת
<span dir="ltr">`for-in`</span>
ופקודת
<span dir="ltr">`Object.keys()`</span>
על האובייקט. אבל המלכודת
`enumerate`
הוסרה ב
ECMAScript 7
(שנקראית גם ECMAScript 2016)
מכיוון שהתגלו קשיים במהלך היישום. מלכודת ה
`enumerate`
אינה קיימת עוד בשום סביבת
JavaScript
ולכן אינה מופיעה בפרק זה.

## יצירת פרוקסי פשוט

כאשר משתמשים בקונסטרקטור
`Proxy`
לייצר פרוקסי, צריך להעביר אליו 2 ארגומנטים: את אובייקט המטרה
ואת האובייקט המטפל
(
    *handler*
    /
    הנדלר
).
handler
הוא אובייקט אשר מגדיר מלכודת אחת או יותר . הפרוקסי מתנהג בצורה רגילה עבור כל האופרציות מלבד אלו שהוגדרה עבורן מלכודת.
בשביל לייצר פרוקסי פשוט אפשר להשתמש ב
handler
ללא מלכודות:

<div dir="ltr">

```js
let target = {};

let proxy = new Proxy(target, {});

proxy.name = "proxy";
console.log(proxy.name);        // "proxy"
console.log(target.name);       // "proxy"

target.name = "target";
console.log(proxy.name);        // "target"
console.log(target.name);       // "target"
```

</div>

בדוגמה הזו,
`proxy`
מעביר את כל הפעולות ישירות ל
`target`.
כאשר
`"proxy"`
מבצע פעולת השמה לתכונה
`proxy.name`,
`name`
נוצר על
`target`.
הפרוקסי עצמו לא שומר את התכונה על עצמו, הוא פשוט מעביר אותה אל
`target`.
באופן דומה, הערכים של
`proxy.name`
ו
`target.name`
זהים מאחר ושניהם מצביעים ל
`target.name`.
זה אומר שהגדרה חדשה ל
`target.name`
יגרום ל
`proxy.name`
להציג את אותם שינויים.
כמובן, פרוקסי בלי מלכודות הוא לא מעניין, אז מה קורה כאשר מגדירים
מלכודת?

## ולידציה של תכונות באמצעות מלכודת `set`

נניח שברצונכם ליצור אובייקט שערכי התכונות שלו חייבים להיות מספרים. המשמעות היא שיש לאמת כל תכונה חדשה שנוספת לאובייקט, ויש לזרוק שגיאה אם הערך אינו מספר. על מנת להשיג זאת, ניתן להגדיר מלכודת
`set`
אשר עוקפת את התנהגות ברירת המחדל של הגדרת ערך. המלכודת
`set`
מקבלת 4 ארגומנטים:

1. `trapTarget` - האובייקט שיקבל את המאפיינים (המטרה של הפרוקסי)
1. `key` - מפתח המאפיין (מחרוזת או סימבול) לכתוב אליו
1. `value` - הערך שנכתב למאפיין
1. `receiver` - האובייקט עליו התבצעה הפעולה (בדר"כ הפרוקסי)

<span dir="ltr">`Reflect.set()`</span>
הינה המתודה המקבילה ל
`set`
באובייקט הריפלקשן, וזוהי התנהגות ברירת המחדל עבור פעולה זו. המתודה
<span dir="ltr">`Reflect.set()`</span>
מקבלת את אותם ארבעה ארגומנטים כמו
מלכודת
`set`
מה שהופך את המתודה לקלה לשימוש בתוך המלכודת.
המלכודת אמורה להחזיר
`true`
אם המאפין נקבע ו
`false`
אם לא.
(
המתודה
<span dir="ltr">`Reflect.set()`</span>
מחזירה את הערך הנכון על סמך הצלחת הפעולה.
)
כדי לאמת את ערכי התכונות, ניתן להשתמש במלכודת
`set`
על מנת לבדוק את הערך
שמועבר. הנה דוגמה:

<div dir="ltr">

```js
let target = {
    name: "target"
};

let proxy = new Proxy(target, {
    set(trapTarget, key, value, receiver) {

        // נתעלם מתכונות קיימות כדי לא להשפיע עליהן
        if (!trapTarget.hasOwnProperty(key)) {
            if (isNaN(value)) {
                throw new TypeError("ערך התכונה חייב להיות מספר.");
            }
        }

        // נוסיף את המאפיין
        return Reflect.set(trapTarget, key, value, receiver);
    }
});

// הוספה של מאפיין חדש
proxy.count = 1;
console.log(proxy.count);       // 1
console.log(target.count);      // 1

// ניתן לבצע פעולת השמה ל
// name
// מכיוון שהוא קיים כבר בטרגט
proxy.name = "proxy";
console.log(proxy.name);        // "proxy"
console.log(target.name);       // "proxy"

// יזרוק שגיאה
proxy.anotherName = "proxy";
```

</div>

הקוד לעיל מגדיר מלכודת פרוקסי המאמתת את הערך של כל תכונה חדשה שנוספת ל
`target`.
כאשר רץ הקוד
`proxy.count = 1`
המלכודת
`set`
מופעלת.
הערך
`trapTarget`
מצביע על
`target`,
הערך של
`key`
הוא
`count`,
הערך עבור
`"value"`
הינו
`1`
ו
`receiver`
(שלא נעשה בו שימוש בדוגמה כאן)
מצביע על
`proxy`.
לא קיימת תכונה בשם
`count`
על אובייקט
`target`,
לכן הפרוקסי מבצע ולידציה עבור
`value`
ע"י העברת הערך לפונקציה
<span dir="ltr">`isNaN()`</span>.
אם התוצאה היא
`NaN`,
אזי התכונה אינה מספר ותיזרק שגיאה. מאחר והקוד משנה את
`count`
לערך
`1`
הפרוקסי קורא ל
<span dir="ltr">`Reflect.set()`</span>
עם אותם ארבעת הארגומנטים שהועברו למלכודת
על מנת להוסיף תכונה חדשה.

כאשר
`proxy.name`
מקבל ערך מסוג מחרוזת
הפעולה מתבצעת בהצלחה. מאחר ועל
`target`
קיימת התכונה
`name` ,
עוד קודם לכן.
תכונה זו מושמטת מבדיקת האימות שמתבצעת ע"י המתודה
<span dir="ltr">`trapTarget.hasOwnProperty()`</span> .
זה מבטיח שמאפיינים שלא הוגדרו שאינם מספרים עדיין נתמכים.

כאשר התכונה
`proxy.anotherName`
מקבלת ערך מסוג מחרוזת,  תיזרק שגיאה. התכונה
`anotherName`
לא קיימת על המטרה, לכן ערכה צריך לעבור ולידציה. בזמן הולידציה, תיזרק שגיאה מאחר ו-
`"proxy"`
אינו ערך מספרי.

בעוד שמלכודת
`set`
מאפשרת לנו ליירט כתיבת ערכים לתכונות, המלכודת
`get`
מאפשרת לנו ליירט פעולות קריאת ערך מתכונה.

## אימות מבנה אובייקט באמצעות מלכודת `get`

אחד ההיבטים המעניינים, ולעיתים מבלבלים, של
JavaScript
מתרחש
כאשר קוראים לתכונות שלא קיימות - לא נזרקת שגיאה. במקום זאת, מוחזר הערך
`undefined`
כמו בדוגמה:

<div dir="ltr">

```js
let target = {};

console.log(target.name);       // undefined
```

</div>

בשפות אחרות, נסיון לקרוא את
`target.name`
יזרוק שגיאה מאחר והתכונה לא קיימת. אבל ג'אווה סקריפט משתמשת ב
`undefined`
בשביל ערך התכונה
`target.name`.
אם אי פעם עבדתם על בסיס קוד גדול, וודאי ראיתם כיצד התנהגות זו עלולה לגרום לבעיות משמעותיות, במיוחד כאשר יש שגיאת הקלדה בשם התכונה. פרוקסי יכול לעזור לכם להציל את עצמכם מבעיה זו על ידי אימות צורת אובייקט.

*צורת אובייקט*
(*object shape*)
זה אוסף של מאפיינים ומתודות שנמצא על האובייקט. מנוע הג'אווהסקריפט משתמש בצורת האובייקט לעשות אופטמיזציה לקוד ,לעיתים נוצר קלאס לייצג את האובייקט. אם ניתן להניח בבטחה שלאובייקט יהיו תמיד אותם מאפיינים והמתודות איתן הוא התחיל (התנהגות שניתן לכפות ע"י המתודה
<span dir="ltr">`Object.preventExtensions()`</span> ,
המתודה
<span dir="ltr">`Object.seal()`</span> ,
או המתודה
<span dir="ltr">`Object.freeze()`</span>),
ואז זריקת שגיאה בניסיונות לגשת למאפיינים לא קיימות יכולה להועיל. פרוקסי מקל על אימות צורת האובייקט.

מאחר ואימות התכונה קורה רק בזמן כשמנסים לקרוא את התכונה, נצטרך להשתמש במלכודת
`get` .
מלכודת
`get`
נקראת כאשר תכונה נקראת, אפילו אם התכונה אינה קיימת על האובייקט, והוא מקבל 3 ארגומנטים:

1. `trapTarget` - האובייקט ממנו קוראים את התכונה
(פרוקסי target)
1. `key` - מפתח התכונה
(סטרינג או סימבול)
שממנו קוראים
1. `receiver` - האובייקט עליו התרחשה הפעולה (בדר"כ הפרוקסי)

ארגומנטים אלה משקפים את הארגומנטים של מלכודת ה
`set`,
עם הבדל בולט אחד.
אין ארגומנט
`value`
מפני שמלכודת
`get`
לא כותבת ערכים. המתודה
<span dir="ltr">`Reflect.get()`</span>
מקבלת שלושה ארגומנטים כמו מלכודת ה
`get`
ומחזירה את ערך ברירת המחדל של התכונה.

ניתן להשתמש במלכודת
`get`
וב
<span dir="ltr">`Reflect.get()`</span>
לזרוק שגיאה כאשר תכונה אינה קיימת על המטרה, כדלקמן:

<div dir="ltr">

```js
let proxy = new Proxy({}, {
        get(trapTarget, key, receiver) {
            if (!(key in receiver)) {
                throw new TypeError("Property " + key + " does not exist.");
            }

            return Reflect.get(trapTarget, key, receiver);
        }
    });

// הוספת תכונה עדיין עובד
proxy.name = "proxy";
console.log(proxy.name);            // "proxy"

// תכונה לא קיימת תזרוק שגיאה
console.log(proxy.nme);             // throws error
```

</div>

בדוגמה זו, מלכודת
`get`
מיירטת פעולת קריאת תכונה. האופרטור
`in`
משמש כדי לקבוע אם התכונה כבר קיימת ב-
`receiver`.
ה
`receiver`
משמש אותנו לצורך הבדיקה עם אופרטור
`in`
במקום
`trapTarget`
במקרה ש
`receiver`
הוא פרוקסי שהוגדרה עליו מלכודת
`has`
(נושא שנכסה בחלק הבא).
שימוש ב
`trapTarget`
במקרה כזה היה עוקף את מלכודת
`has`
ועלול לתת תוצאה שגויה. תיזרק שגיאה במידה והתכונה אינה קיימת, אחרת משתמשים בהתנהגות ברירת המחדל.

קוד זה מאפשר להוסיף תכונות חדשות כמו
`proxy.name`,
להיות מורשות לכתיבה, וניתן לקרוא אותן ללא בעיה.
השורה האחרונה מכילה שגיאת דפוס:
`proxy.nme`
צריך  להיות
`proxy.name`
הקוד יזרוק שגיאה כי התכונה
`nme`
אינה מוגדרת.

## הסתרת קיום מאפיין באמצעות מלכודת `has`

האופרטור
`in`
בודק אם מאפיין קיים על אובייקט ומחזיר
`true`
אם קיים או קיים במאפייני הפרוטוטייפ המתאימים לשם או ל
symbol.
לדוגמה:

<div dir="ltr">

```js
let target = {
    value: 42;
}

console.log("value" in target);     // true
console.log("toString" in target);  // true
```

</div>


שניהם - גם
`value`
וגם
`toString`
קיימים על
`object`,
לכן בשני המקרים
`in`
האופרטור יחזיר
`true`.
המאפיין
`value`
זה מאפיין של האובייקט בזמן ש
`toString`
הוא מאפיין מהפרוטוטייפ
(יורש
`Object`).
פרוקסי מאפשרים לך ליירט את הפעולה הזו ולהחזיר ערכים שונים עבור
`in`
עם מלכודת
`has`.

מלכודת
`has`
נקראת בזמן שאופרטור
`in`
נמצא בשימוש. כאשר הוא נקרא, שני הארגונטים מועברים למלכודת
`has`:

1. `trapTarget` - האובייקט ממנו קוראים את המאפיין (פרוקסי target)
1. `key` - מפתח המאפיין (סטרניג או סימבול) שממנו קוראים

המתודה
<span dir="ltr">`Reflect.has()`</span>
מקבלת את אותם ארגומנטים ומחזירה את תגובת ברירת המחדל עבור
`in`
אופרטור. בשימוש במלכודת
`has`
וב
<span dir="ltr">`Reflect.has()`</span>
ניתן לשנות את ההתנהגות של
`in`
עבור מאפיינים מסוימים תוך בזמן שיש לך גיבוי להתנהגות ברירת המחדך עבור אחרים. לדוגמה,נניח שמעוניינים רק להסתיר את המאפיין
`value` .
ניתן לעשות את זה כך:

<div dir="ltr">


```js
let target = {
    name: "target",
    value: 42
};

let proxy = new Proxy(target, {
    has(trapTarget, key) {

        if (key === "value") {
            return false;
        } else {
            return Reflect.has(trapTarget, key);
        }
    }
});


console.log("value" in proxy);      // false
console.log("name" in proxy);       // true
console.log("toString" in proxy);   // true
```

</div>

מלכודת
`has`
עבור
`proxy`
בודק אם
`key`
הוא
`"value"`
מחזיר
`false`
אם כן. אחרת, התנהגות ברירת המחדל משמשת בקריאה אל המתודה
<span dir="ltr">`Reflect.has()`</span>.
כתוצאה, האופרטור
`in`
מחזיר
`false`
עבור המאפיין
`value`
למרות שהמאפיין
`value`
קיים על האובייקט. המאפיינים האחרים,
`name`
ו
`toString`,
מחזירים
`true`
כאשר משתמשים באופרטור
`in`.

## מניעת מחיקת מאפיין ע"י מלכודת `deleteProperty`

האופרטור
`delete`
מוחק מאפיין מתוך האובייקט ומחזיר
`true`
כאשר מצליח ו
`false`
כאשר נכשל. במצב
strict ,
`delete`
יזרוק שגיאה כאשר ינסה למחוק מאפיין שלא הוגדר; במצב לא
strict,
`false`
יחזיר פשוט
`delete`.
להלן דוגמה:

<div dir="ltr">

```js
let target = {
    name: "target",
    value: 42
};

Object.defineProperty(target, "name", { configurable: false });

console.log("value" in target);     // true

let result1 = delete target.value;
console.log(result1);               // true

console.log("value" in target);     // false

// הערה: השורה הבאה תזרוק שגיאה במצב
// strict mode
let result2 = delete target.name;
console.log(result2);               // false

console.log("name" in target);      // true
```

</div>

המאפיין
`value`
נמחק ע"י שימוש באופרטור
`delete`
וכתוצאה ה
`in`
אופרטור מחזיר
`false`
בקריאה השלישית של
<span dir="ltr">`console.log()`</span>.
המאפיין שלא מוגדר
`name`
לא יכול להמחק לכן האופרטור
`delete`
פשוט מחזיר
`false`
(אם הקוד רץ ב
strict
הוא יזרוק שגיאה במקום).
ניתן להתערב בהתנהגות הזו ש"י שימוש במלכודת
`deleteProperty`
בתוך הפרוקסי.

המלכודת
`deleteProperty`
נקראית כל פעם שהאופרטור
`delete`
הוא בשימוש על מאפיין באובייקט. המלכודת מעבירה שני ארגומנטים:

1. `trapTarget` - האובייקט ממנו מוחקים את המאפיין (פרוקסי target)
1. `key` - מפתח המאפיין (מחרוזת או symbol) שאותו מוחקים

המתודה
<span dir="ltr">`Reflect.deleteProperty()`</span>
מספקת את היישום הדיפולטי של  המלכודת
`deleteProperty`
ומקבלת את אותם שני ארגומנטים.  ניתן לשלב את
<span dir="ltr">`Reflect.deleteProperty()`</span>
ואת במלכודת
`deleteProperty`
לשנות את התנהגות האופרטור
`delete` .
לדוגמה, ניתן לוודא שהמאפיין
`value`
לא יוכל להימחק:

<div dir="ltr">

```js
let target = {
    name: "target",
    value: 42
};

let proxy = new Proxy(target, {
    deleteProperty(trapTarget, key) {

        if (key === "value") {
            return false;
        } else {
            return Reflect.deleteProperty(trapTarget, key);
        }
    }
});

// נסיון למחוק proxy.value

console.log("value" in proxy);      // true

let result1 = delete proxy.value;
console.log(result1);               // false

console.log("value" in proxy);      // true

// נסיון למחוק proxy.value

console.log("name" in proxy);       // true

let result2 = delete proxy.name;
console.log(result2);               // true

console.log("name" in proxy);       // false
```

</div>

הקוד נורא דומה לדומת המלכודת
`has`
המלכודת
`deleteProperty`
בודקת אם ה
`key`
הוא
`"value"`
ומחזירה
`false`
אם כן. אחרת, ההתנהגות הדיפולטיבית נקראית ע"י המתודה
<span dir="ltr">`Reflect.deleteProperty()`<span> .
המאפיין
`value`
לא יכול להמחק דרך
`proxy`
בגלל שהמאפיין ממולכד, אבל המאפיין
`name`
נמחק כמצופה. גישה זו שימושית במיוחד כשרוצים להגן על מאפיינים מלהימחק ב
strict mode
בלי לזרוק שגיאה.

## מלכודת פרוקסי לאב-טיפוס (Prototype)

פרק ארבע הציג את המתודה
<span dir="ltr">`Object.setPrototypeOf()`<span>
של
ECMAScript 6
כדי להשלים את המתודה של
ECMAScript 5
<span dir="ltr">`Object.getPrototypeOf()`<span>.
פרוקסי מאפשר לך ליירט את הביצוע של שתי המתודות דרך המלכודות `setPrototypeOf`
ו
`getPrototypeOf`
. בשני המקרים, המתודות על האובייקט
`Object`
קוראות למלכודת של השם המקביל בפרוקסי, ומאפשרות לשנות את התנהגות המתודות.

מאחר ויש שתי מלכודות הקשורות לפרוטוטייפ פרוקסי, קיים סט של מלכודות הקשורות לכל אחת מהמתודות. המלכודת
`setPrototypeOf`
מקבלת את הארגומנטים הבאים:

1. `trapTarget` - האובייקט שעבורו יש להגדיר את אב הטיפוס
(פרוקסי
target)
1. `proto` - האובייקט לשימוש בו כאב-טיפוס

אלו אותם ארגומנטים המועברים למתודות
<span dir="ltr">`Object.setPrototypeOf()`</span>
ו
<span dir="ltr">`Reflect.setPrototypeOf()`</span>.
המלכודת
`getPrototypeOf` ,
מצד שני, מקבלת רק את הארגומנט
`trapTarget`,
שזה הארגומנט שמועבר למטודות
<span dir="ltr">`Object.getPrototypeOf()`</span>
ו
<span dir="ltr">`Reflect.getPrototypeOf()`</span>.

### איך פרוקסי לפרוטוטיפ עובד

יש כמה מגבלות למלכודות אלה. ראשית, מלכודת
`getPrototypeOf`
חייבת להחזיר אובייקט או
`null`,
וכל ערך החזרה אחר מביא לשגיאת זמן ריצה. בדיקת הערך החוזר מבטיחה ש
<span dir="ltr">`Object.getPrototypeOf()`</span>
תמיד תחזיר את הערך הרצוי. באופן דומה, ערך ההחזר של המלכודת
`setPrototypeOf`
חייב להיות
`false`
אם הפעולה לא מצליחה. כאשר
`setPrototypeOf`
מחזיר
`false`,
<span dir="ltr">`Object.setPrototypeOf()`</span>
יזרוק שגיאה. אם
`setPrototypeOf`
יחזיר ערך אחר מאשר
`false`,
אז
<span dir="ltr">`Object.setPrototypeOf()`</span>
ישער שהפעולה הצליחה.

הדוגמה הבאה מסתירה את אב הטיפוס של ה-
proxy
על ידי החזרה תמיד של
`null`
וגם לא מאפשרת לשנות את האב-טיפוס:

<div dir="ltr">

```js
let target = {};
let proxy = new Proxy(target, {
    getPrototypeOf(trapTarget) {
        return null;
    },
    setPrototypeOf(trapTarget, proto) {
        return false;
    }
});

let targetProto = Object.getPrototypeOf(target);
let proxyProto = Object.getPrototypeOf(proxy);

console.log(targetProto === Object.prototype);      // true
console.log(proxyProto === Object.prototype);       // false
console.log(proxyProto);                            // null

// יצליח
Object.setPrototypeOf(target, {});

// יזרוק שגיאה
Object.setPrototypeOf(proxy, {});
```

</div>

קוד זה מדגיש את ההבדל בין התנהגות של
`target`
ו
`proxy`.
בזמן ש
<span dir="ltr">`Object.getPrototypeOf()`</span>
מחזיר את הערך של
`target`,
מוחזר
`null`
עבור
`proxy`
בגלל שהמלכודת
`getPrototypeOf`
נקראת. באופן דומה,
<span dir="ltr">`Object.setPrototypeOf()`</span>
מצליח כאשר משתמשים ב
`target`
אבל זורק שגיאה כאשר משתמשים ב
`proxy`
בגלל המלכודת
`setPrototypeOf` .

אם ברצונך להשתמש בהתנהגות ברירת המחדל עבור שני מלכודות אלה,אתה יכול להשתמש במתודות המתאימות ב-
`Reflect`.
לדוגמה, קוד זה מיישם את התנהגות ברירת המחדל עבור המלכודות-
`getPrototypeOf`
ו
`setPrototypeOf`:

<div dir="ltr">

```js
let target = {};
let proxy = new Proxy(target, {
    getPrototypeOf(trapTarget) {
        return Reflect.getPrototypeOf(trapTarget);
    },
    setPrototypeOf(trapTarget, proto) {
        return Reflect.setPrototypeOf(trapTarget, proto);
    }
});

let targetProto = Object.getPrototypeOf(target);
let proxyProto = Object.getPrototypeOf(proxy);

console.log(targetProto === Object.prototype);      // true
console.log(proxyProto === Object.prototype);       // true

// יצליח
Object.setPrototypeOf(target, {});

// גם יצליח
Object.setPrototypeOf(proxy, {});
```

</div>

בדוגמה זו תוכלו להשתמש גם ב
`target`
וגם ב
`proxy`
לחלופין ולקבל את אותם התוצאות בגלל שהמלכודות
`getPrototypeOf`
ו
`setPrototypeOf`
מעבירות את ההתנהגות הדיפולטיבית. אנחנו משידים זאת בדוגמה הנוכחית תודות למתודות
<span dir="ltr">`Reflect.getPrototypeOf()`</span>
ו
<span dir="ltr">`Reflect.setPrototypeOf()`</span>
ולא למתודות עם אותו שם שנמצאות על ה
`Object`
עם כמה הבדלים חשובים.

### למה שתי סוגי מתודות?

ההיבט המבלבל של
<span dir="ltr">`Reflect.getPrototypeOf()`</span>
ו
<span dir="ltr">`Reflect.setPrototypeOf()`</span>
זה שהם נראים מאוד דומים בצורה חשודה למתודות
<span dir="ltr">`Object.getPrototypeOf()`</span>
ו
<span dir="ltr">`Object.setPrototypeOf()`</span>
.בזמן ששני סוגי המתודות נראים שעושים פעולות דומות , ישנם כמה הבדלים ברורים בין השניים.

נתחיל,
<span dir="ltr">`Object.getPrototypeOf()`</span>
ו
<span dir="ltr">`Object.setPrototypeOf()`</span>
הן פעולות ברמה גבוהה יותר שנוצרו לשימוש מפתחים מההתחלה. המתודות
<span dir="ltr">`Reflect.getPrototypeOf()`</span>
ו
<span dir="ltr">`Reflect.setPrototypeOf()`</span>
הן פעולות ברמה נמוכה המעניקות למפתחים גישה למערכת הפנימית  שהיתה בעבר פנימית בלבד - האופרטורים
`[[GetPrototypeOf]]`
ו
`[[SetPrototypeOf]]`
. המתודה
<span dir="ltr">`Reflect.getPrototypeOf()`</span>
היא מעטפת לפעולה
`[[GetPrototypeOf]]`
(עם אותו אימות קלט). המתודה
<span dir="ltr">`Reflect.setPrototypeOf()`</span>
ו
`[[SetPrototypeOf]]`
יש אותה מערכת יחסים. המתודות המתאימות על ה
`Object`
גם נקראות
`[[GetPrototypeOf]]`
ו
`[[SetPrototypeOf]]`
אך מבוצעת מספר שלבים לפני הקריאה ובודקת את הערך החוזר כדי לקבוע את ההתנהגות שלו.

המתודה
<span dir="ltr">`Reflect.getPrototypeOf()`</span>
זורקת שגיאה אם הארגומנט הוא לא אובייקט, כאשר
<span dir="ltr">`Object.getPrototypeOf()`</span>
קודם יכניס את הערך לאובייקט לפני ביצוע הפעולה. אם הייתם מעבירים מספר לכל אחת מהשיטות, הייתם מקבלים תוצאה שונה:

<div dir="ltr">

```js
let result1 = Object.getPrototypeOf(1);
console.log(result1 === Number.prototype);  // true

// throws an error
Reflect.getPrototypeOf(1);
```

</div>

המתודה
<span dir="ltr">`Object.getPrototypeOf()`</span>
מאפשרת לך לקבל את הפרוטוטייפ של המספר
`1`
בגלל שקודם הוא מכפיף את הערך לאובייקט
`Number`
ואז מחזיר
`Number.prototype`.
המתודה
<span dir="ltr">`Reflect.getPrototypeOf()`</span>
לא מכפיפה את הערך , לכן
`1`
הוא לא אובייקט, וזורק שגיאה.

המתודה
<span dir="ltr">`Reflect.setPrototypeOf()`</span>
יש לה גם מספר שינויים מהמתודה
<span dir="ltr">`Object.setPrototypeOf()`</span>
. ראשית,
<span dir="ltr">`Reflect.setPrototypeOf()`</span>
מחזירה ערך בוליאני המציין אם הפעולה הצליחה. הערך
`true`
יחזור עבור הצלחה, ו
`false`
יחזור עבור כישלון. אם
<span dir="ltr">`Object.setPrototypeOf()`</span>
נכשל, יזרוק שגיאה.

בדוגמה הראשונה תחת "מלכודת פרוקסי לאב-טיפוס" הראינו, כאשר מלכודת הפרוקסי
`setPrototypeOf`
מחזירה
`false`,
זה גורם ל
<span dir="ltr">`Object.setPrototypeOf()`</span>
לזרוק שגיאה. המתודה
<span dir="ltr">`Object.setPrototypeOf()`</span>
מחזירה את הארגומנט הראשון כערך וזה לא מתאים להתנהגות ברירת המחדל דל מלכודת הפרוקסי
`setPrototypeOf`.
הקוד הבא מדגים את ההבדלים הללו:


<div dir="ltr">

```js
let target1 = {};
let result1 = Object.setPrototypeOf(target1, {});
console.log(result1 === target1);                   // true

let target2 = {};
let result2 = Reflect.setPrototypeOf(target2, {});
console.log(result2 === target2);                   // false
console.log(result2);                               // true
```

</div>

בדוגמה הזו,
<span dir="ltr">`Object.setPrototypeOf()`</span>
מחזיר את
`target1`
כערך שלו, אבל
<span dir="ltr">`Reflect.setPrototypeOf()`</span>
מחזיר
`true`.
ההבדל העדין הזה חשוב מאוד. תאם תוכלו לקראות לכאורה עוד מתודות כפולות ל
`Object`
ו
`Reflect`, אך הקפד להשתמש במתודה
`Reflect`
בתוך מלכודת פרוקסי.

I> שתי השיטות של המתודות יקראו למלכודת הפרוקסי
`getPrototypeOf`
ו
`setPrototypeOf`
כאשר משתמשים בפרוקסי.

## מלכודות להרחבת אובייקט

ECMAScript 5
הוסיף אפשרות להרחבת האובייקט דרך המתודות
<span dir="ltr">`Object.preventExtensions()`</span>
ו
<span dir="ltr">`Object.isExtensible()`</span>
, ו
ECMAScript 6
מאפשרת לפרוקסי ליירט את הקריאה למתודות לאובייקטים הבסיסיים באמצעות המלכודות
`preventExtensions`
ו
`isExtensible`.
שני המלכודות מקבלים ארגומנט אחד שנקרא
`trapTarget`
זה האובייקט עליו נקראה המתודה. המלכודת
`isExtensible`
חייבת להחזיר ערך בוליאני המציין אם האובייקט ניתן להרחבה ואילו המלכודת
`preventExtensions`
חייבת להחזיר ערך בוליאני המציין אם הפעולה הצליחה.

יש גם את המתודות
<span dir="ltr">`Reflect.preventExtensions()`</span>
ו
<span dir="ltr">`Reflect.isExtensible()`</span>
ליישם את ההתנהגות הדיפולטיבית. שניהם מחזירים ערך בוליאני, כך שניתן להשתמש בהם ישירות במלכודות המקבילות שלהם.

### שתי דוגמאות בסיסיות

כדי לראות מלכודות הרחבה בפעולה, בקוד הבא , אשר מיישם את התנהגות ברירת המחדל עבור המלכודות
`isExtensible`
ו
`preventExtensions`:

<div dir="ltr">

```js
let target = {};
let proxy = new Proxy(target, {
    isExtensible(trapTarget) {
        return Reflect.isExtensible(trapTarget);
    },
    preventExtensions(trapTarget) {
        return Reflect.preventExtensions(trapTarget);
    }
});


console.log(Object.isExtensible(target));       // true
console.log(Object.isExtensible(proxy));        // true

Object.preventExtensions(proxy);

console.log(Object.isExtensible(target));       // false
console.log(Object.isExtensible(proxy));        // false
```

</div>


דוגמה זו מראה ש
<span dir="ltr">`Object.preventExtensions()`</span>
ו
<span dir="ltr">`Object.isExtensible()`</span>
עוברים כראוי מ
`proxy`
ל
`target`.
אתה יכול כמובן , לשנות את ההתנהגות. לדוגמה, אם אתה לא רוצה לאפשר ל
<span dir="ltr">`Object.preventExtensions()`</span>
להצליח בפרוקסי שלך, אתה יכול להחזיר
`false`
מהמלכודת
`preventExtensions`:

<div dir="ltr">

```js
let target = {};
let proxy = new Proxy(target, {
    isExtensible(trapTarget) {
        return Reflect.isExtensible(trapTarget);
    },
    preventExtensions(trapTarget) {
        return false
    }
});


console.log(Object.isExtensible(target));       // true
console.log(Object.isExtensible(proxy));        // true

Object.preventExtensions(proxy);

console.log(Object.isExtensible(target));       // true
console.log(Object.isExtensible(proxy));        // true
```

</div>

כאן בקריאה ל
<span dir="ltr">`Object.preventExtensions(proxy)`</span>
מתעלם ביעילות מכיוון שהמלכודת
`preventExtensions`
מחזירה
`false`.
הפעולה אינה מועברת לבסיס
`target`,
כך ש
<span dir="ltr">`Object.isExtensible()`</span>
מחזיר
`true`.

### שיטות הרחבה כפולות

יתכן ששמת לב, שיש דמיון וכפילויות במתודות ב
`Object`
ו
`Reflect`.
במקרה הזה יש יותר דמיון מאשר לא. המתודות
<span dir="ltr">`Object.isExtensible()`</span>
ו
<span dir="ltr">`Reflect.isExtensible()`</span>
הם דומים למעט כאשר מועבר ערך שהוא אינו אובייקט. במקרה הזה,
<span dir="ltr">`Object.isExtensible()`</span>
תמיד יחזיר
`false`
כאשר
<span dir="ltr">`Reflect.isExtensible()`</span>
יזרוק שגיאה. הנה דוגמה להתנהגות הזו:

<div dir="ltr">

```js
let result1 = Object.isExtensible(2);
console.log(result1);                       // false

// יזרוק שגיאה
let result2 = Reflect.isExtensible(2);
```

</div>

מגבלה זו דומה להבדל בין המתודות
<span dir="ltr">`Object.getPrototypeOf()`</span>
ו
<span dir="ltr">`Reflect.getPrototypeOf()`</span>,
מכיוון  שלמתודות עם פונקונאליות ברמה הנמוכה יש יותר בדיקות שגיאה מחמירות מאשר  למקבילה ברמה הגבוהה יותר.

המתודות
<span dir="ltr">`Object.preventExtensions()`</span>
ו
<span dir="ltr">`Reflect.preventExtensions()`</span>
גם כן נורא דומות. המתודה
<span dir="ltr">`Object.preventExtensions()`</span>
תמיד תחזיר את הערך שהועובר אליה כארגומנט אפילו אם הערך הוא אינו אובייקט. המתודה
<span dir="ltr">`Reflect.preventExtensions()`</span>,
מצד שני , תזרוק שגיאה אם הארגומנט הוא אינו אובייקט; אם הארגומנט הוא אובייקט, אזי
<span dir="ltr">`Reflect.preventExtensions()`</span>
יחזיר
`true`
כאשר הפעולה מצליחה  ויחזיר
`false`
אם לא. לדוגמה:

<div dir="ltr">

```js
let result1 = Object.preventExtensions(2);
console.log(result1);                               // 2

let target = {};
let result2 = Reflect.preventExtensions(target);
console.log(result2);                               // true

// יזרוק שגיאה
let result3 = Reflect.preventExtensions(2);
```

</div>

כאן מועבר ל
<span dir="ltr">`Object.preventExtensions()`</span>
הערך
`2`
והוא מחזיר למרות ש
`2`
הוא לא אובייקט. המתודה
<span dir="ltr">`Reflect.preventExtensions()`</span>
מחזירה
`true`
כאשר אובייקט מועובר אליה וזורקת שגיאה כאשר
`2`
מועבר אליה.

## מלכודת לתיאור מאפיינים

אחת התכונות החשובות ביותר של
ECMAScript 5
היא היכולת להגדיר מאפיינים
-property attributes
בשימוש עם המתודה
<span dir="ltr">`Object.defineProperty()`</span>.
בגרסאות קודמות של ג'אווה סקריפט, לא הייתה שום דרך להגדיר מאפיין , להגדיר מאפיין רק לקריאה, או בלתי נספר. כל זה אפשרי תודות למתודה
<span dir="ltr">`Object.defineProperty()`</span>,
ואתה יכול לקבל מאפיין תודות למתודה
<span dir="ltr">`Object.getOwnPropertyDescriptor()`</span>.

פרוקסי נותן לך אפשרות ליירט קריאות ל
<span dir="ltr">`Object.defineProperty()`</span>
ו
<span dir="ltr">`Object.getOwnPropertyDescriptor()`</span>
בשימוש המלכודות
`defineProperty`
ו
`getOwnPropertyDescriptor`,
בהתאמה. המלכודת
`defineProperty`
מקבלת את הארגומנטים הבאים:

1. `trapTarget` - האובייקט שיקבל את המאפיינים
(המטרה של הפרוקסי)
1. `key` - מפתח המאפיין
(סטרינג או
symbol)
לכתוב אליו
1. `descriptor` - אובייקט המתאר את המאפיין

מלכודת
`defineProperty`
דורשת שתחזיר
`true`
אם הפעולה הצליחה ו
`false`
אם לא. מלכודת
`getOwnPropertyDescriptor`
מקבלת רק
`trapTarget`
ו
`key`,
ואתה מצפה שתחזיר את המתאר. המתודות התואמות
<span dir="ltr">`Reflect.defineProperty()`</span>
ו
<span dir="ltr">`Reflect.getOwnPropertyDescriptor()`</span>
מקבלות את אותם ארגומנטים כמו מלכודת הפרוקסי המקבילה. להלן דוגמה שמיישמת את התנהגות ברירת המחדל עבור כל מלכודת:

<div dir="ltr">

```js
let proxy = new Proxy({}, {
    defineProperty(trapTarget, key, descriptor) {
        return Reflect.defineProperty(trapTarget, key, descriptor);
    },
    getOwnPropertyDescriptor(trapTarget, key) {
        return Reflect.getOwnPropertyDescriptor(trapTarget, key);
    }
});


Object.defineProperty(proxy, "name", {
    value: "proxy"
});

console.log(proxy.name);            // "proxy"

let descriptor = Object.getOwnPropertyDescriptor(proxy, "name");

console.log(descriptor.value);      // "proxy"
```

</div>


הקוד פה מגדיר מאפיין
`"name"`
על הפרוקסי עם המתודה
<span dir="ltr">`Object.defineProperty()`</span>.
לאחר מכן מאוחזר מתאר המאפיינים של אותו נכס במתודה
<span dir="ltr">`Object.getOwnPropertyDescriptor()`</span>.

### מלכודת Object.defineProperty

מלכודת
`defineProperty`
דורשת שתחזיר ערך בוליאני כדי לציין אם הפעולה הצליחה. כאשר מוחזר
`true` ,
<span dir="ltr">`Object.defineProperty()`</span>
הצליח כרגיל; כאשר מוחזר
`false` ,
<span dir="ltr">`Object.defineProperty()`</span>
יזרוק שגיאה. אתה יכול להשתמש בפונקציונליות זו כדי להגביל את סוגי המאפיינים שהמתודה-
<span dir="ltr">`Object.defineProperty()`</span>
יכולה להגדיר. לדוגמה, אם אתה רוצה למנוע את הגדרת מאפייני ה
symbol,
אתה יכול לבדוק שהמפתח הוא מחרוזת ולהחזיר
`false`,
כמו כאן:

<div dir="ltr">

```js
let proxy = new Proxy({}, {
    defineProperty(trapTarget, key, descriptor) {

        if (typeof key === "symbol") {
            return false;
        }

        return Reflect.defineProperty(trapTarget, key, descriptor);
    }
});


Object.defineProperty(proxy, "name", {
    value: "proxy"
});

console.log(proxy.name);                    // "proxy"

let nameSymbol = Symbol("name");

// יזרוק שגיאה
Object.defineProperty(proxy, nameSymbol, {
    value: "proxy"
});
```

</div>

מלכודת הפרוקסי
`defineProperty`
מחזירה
`false`
אם
`key`
הוא
symbol
אחרת ממשיכה עם ההתנהגות הדיפולטיבית. כאשר
<span dir="ltr">`Object.defineProperty()`</span>
נקרא עם
`"name"`
כמפתח, המתודה תצליח בגלל שהמפתח הוא סטרינג. כאשר
<span dir="ltr">`Object.defineProperty()`</span>
נקראית עם
`nameSymbol`,
הוא יזרוק שגיאה כי המלכודת
`defineProperty`
מחזירה
`false`.

I> אתה יכול גם לקבל את
<span dir="ltr">`Object.defineProperty()`</span>
שיכשל בשקט על ידי החזרת
`true`
ולא לקרוא לשיטה
<span dir="ltr">`Reflect.defineProperty()`</span>.
זה ידכא את השגיאה אם לא הגדרת את המאפיין בפועל.

### הגבלת מתאר אובייקט

על מנת להבטיח התנהגות עקבית בזמן השימוש במתודות
<span dir="ltr">`Object.defineProperty()`</span>
ו
<span dir="ltr">`Object.getOwnPropertyDescriptor()`</span>,
מתאר אובייקט שמועבר למלכודת
`defineProperty`
מנורמלים. אובייקטים שמוחזרים מהמלכודת
`getOwnPropertyDescriptor`
תמיד יעברו אימות מאותה הסיבה.

לא משנה איזה אובייקט מועבר בארגומנט השלישי למתודה
<span dir="ltr">`Object.defineProperty()`</span>,
רק המאפיינים
`enumerable`,
`configurable`, `value`, `writable`, `get`,
ו
`set`
יהיו על מתאר האובייקט המועבר למלכודת
`defineProperty`.
לדוגמה:

<div dir="ltr">

```js
let proxy = new Proxy({}, {
    defineProperty(trapTarget, key, descriptor) {
        console.log(descriptor.value);              // "proxy"
        console.log(descriptor.name);               // undefined

        return Reflect.defineProperty(trapTarget, key, descriptor);
    }
});


Object.defineProperty(proxy, "name", {
    value: "proxy",
    name: "custom"
});
```

</div>


כאן,
<span dir="ltr">`Object.defineProperty()`</span>
קורא למאפיין לא סטנדרטי
`name`
בארגומנט השלישי. כאשר מלכודת
`defineProperty`
נקראית, ל
`descriptor`
של האובייקט אין את המאפיין
`name`
אבל יש לו את המאפיין
`value`.
זה בגלל ש
`descriptor`
לא מתייחס בפועל לארגומנט השלישי שהועובר במתודה
<span dir="ltr">`Object.defineProperty()`</span>,
אלא לאובייקט החדש המכיל רק את המאפיינים המותרים. המתודה
<span dir="ltr">`Reflect.defineProperty()`</span>
גם מתעלמת מהמאפיינים שלא מותרים במתאר.

המלכודת
`getOwnPropertyDescriptor`
יש מגבלה מעט שונה שדורשת שיחזיר ערך של
`null`, `undefined`,
או אובייקט. אם מוחזר אובייקט, רק המאפיינים
`enumerable`, `configurable`, `value`, `writable`, `get`,
ו
`set`
מותרים כמאפיינים על האובייקט. שגיאה נזרקת אם אתה מחזיר אובייקט עם מאפיין משלו שאינו מותר, כמו שהקוד מראה:

<div dir="ltr">

```js
let proxy = new Proxy({}, {
    getOwnPropertyDescriptor(trapTarget, key) {
        return {
            name: "proxy"
        };
    }
});

// יזרוק שגיאה
let descriptor = Object.getOwnPropertyDescriptor(proxy, "name");
```

</div>

המאפיין
`name`
לא מאושר כמאפיין על המתאר, כך ש
<span dir="ltr">`Object.getOwnPropertyDescriptor()`</span>
נקרא, ה
`getOwnPropertyDescriptor`
ערך החזרה מפעיל שגיאה. הגבלה זו מבטיחה שהערך שיוחזר על ידי
<span dir="ltr">`Object.getOwnPropertyDescriptor()`</span>
תמיד יש מבנה אמין ללא קשר לשימוש בפרוקסי.

### מתודות תיאור כפול

שוב,
ECMAScript 6
יש כמה מתודות דומות באופן מבלבל, כמו המתודת
<span dir="ltr">`Object.defineProperty()`</span>
ו
<span dir="ltr">`Object.getOwnPropertyDescriptor()`</span>
נראה שעושות את הודות הדבר כמו המתודות
<span dir="ltr">`Reflect.defineProperty()`</span>
ו
<span dir="ltr">`Reflect.getOwnPropertyDescriptor()`</span>,
בהתאמה. כמו זוגות מתודות אחרות שנדונו קודם לכן בפרק זה, יש להם כמה הבדלים עדינים אך חשובים.

#### המתודה defineProperty

המתודות
<span dir="ltr">`Object.defineProperty()`</span>
ו
<span dir="ltr">`Reflect.defineProperty()`</span>
הם בדיוק אותו הדבר למעט הערך שחוזר מהם. המתודה
<span dir="ltr">`Object.defineProperty()`</span>
מחזירה את הארומנט הראשון, בזמן ש
<span dir="ltr">`Reflect.defineProperty()`</span>
תחזיר
`true`
אם הפעולה הצליחה ו
`false`
אם לא . לדוגמה:

<div dir="ltr">

```js
let target = {};

let result1 = Object.defineProperty(target, "name", { value: "target "});

console.log(target === result1);        // true

let result2 = Reflect.defineProperty(target, "name", { value: "reflect" });

console.log(result2);                   // true
```

</div>

כאשר
<span dir="ltr">`Object.defineProperty()`</span>
נקראת על
`target`,
הערך החוזר הוא
`target`.
כאשר
<span dir="ltr">`Reflect.defineProperty()`</span>
נקרא על
`target`,
הערך החוזר הוא
`true`,
שמעיד שהפעולה הצליחה. מאחר ומלכודת הפרוקסי
`defineProperty`
דורשת שערך בוליאני יחזור, זה יותר טוב להשתמש ב
<span dir="ltr">`Reflect.defineProperty()`</span>
ליישם את התנהגות ברירת המחדל במידת הצורך.

#### המתודה getOwnPropertyDescriptor

המתודה
<span dir="ltr">`Object.getOwnPropertyDescriptor()`</span>
כופה את
הארגומנט
הראשון שלו לאובייקט כאשר מועבר ערך פרימיטיבי ואז ממשיך בפעולה. מצד שני, המתודה
<span dir="ltr">`Reflect.getOwnPropertyDescriptor()`</span>
תזרוק שגיאה אם הארגומנט הראשון יהיה מסוג פרמטיבי. להלן דוגמה המציגה את שניהם:

<div dir="ltr">

```js
let descriptor1 = Object.getOwnPropertyDescriptor(2, "name");
console.log(descriptor1);       // undefined

// יזרוק שגיאה
let descriptor2 = Reflect.getOwnPropertyDescriptor(2, "name");
```

</div>

המתודה
<span dir="ltr">`Object.getOwnPropertyDescriptor()`</span>
תחזיר
`undefined`
בגלל ההשמה של
`2`
לתוך אובייקט, ולו אין את המאפיין
`name`
. זוהי ההתנהגות הסטנדרטית של המתודה כאשר מאפיין עם השם הנתון אינו נמצא באובייקט. כאשר
<span dir="ltr">`Object.getOwnPropertyDescriptor()`</span>
נקרא, מצד שני, שגיאה נזרקת מייד מכיוון שמתודה זו אינה מקבלת ערכים פרימיטיביים לטיעון הראשון.

## מלכודת `ownKeys`

מלכודת הפרוקסי
`ownKeys`
מיירטת את המתודה הפנימית
`[[OwnPropertyKeys]]`
ומאפשרת לך לשנות את התתנהגות שלה ע"י החזרה של מערך של ערכים. במערך הזה נעשה שימוש בארבע מתודות שונות: המתודה
<span dir="ltr">`Object.keys()`</span>,
המתודה
<span dir="ltr">`Object.getOwnPropertyNames()`</span>,
המתודה
<span dir="ltr">`Object.getOwnPropertySymbols()`</span>,
והמתודה
<span dir="ltr">`Object.assign()`</span>.
(
המתודה
<span dir="ltr">`Object.assign()`</span>
משתמשת במערך כדי להחליט איזה מאפיינים להעתיק.)

ההתנהגות הדיפולטיבית עבור המלכודת
`ownKeys`
ממומשת ע"י המתודה
<span dir="ltr">`Reflect.ownKeys()`</span>
ומחזירה מערך של כל מפתחות של המאפיינים,כולל סטרינג וסימבול. המתודה
<span dir="ltr">`Object.getOwnPropertyNames()`</span>
והמתודה
מסננות סימבול מהמערך ומחזירה את התוצאות בזמן שהמתודה
<span dir="ltr">`Object.getOwnPropertySymbols()`</span>
מסננת את הסטרינג ומחזירה את התוצאות. המתודה
<span dir="ltr">`Object.assign()`</span>
משתמשת גם בסטרינג וגם בסימבול.

המלכודת
`ownKeys`
מקבלת ארגומנט אחד בלבד, המטרה, וחייבת להחזיר תמיד מערך או מערך-דמה של אובייקט (array-like);
אחרת, שגיאה תיזרק. אתה יכול להשתמש במלכודת
`ownKeys`
לדוגמה, לסנן החוצה את כל המפתחות שאתה לא רוצה שישתמשו בזמן הקריאה  למתודה
<span dir="ltr">`Object.keys()`</span>,
למתודה
<span dir="ltr">`Object.getOwnPropertyNames()`</span>,
למתודה
<span dir="ltr">`Object.getOwnPropertySymbols()`</span>,
או למתודה
<span dir="ltr">`Object.assign()`</span>.
נניח שאינך רוצה לכלול את המאפיינים שמתחילים עם קו-תחתון, סימון נפוץ ב-
JavaScript
המצביע על כך ששדה הוא פרטי. אתה יכול להשתמש במלכודת
`ownKeys`
לסנן החוצה את המפתחות האלו כך:

<div dir="ltr">

```js
let proxy = new Proxy({}, {
    ownKeys(trapTarget) {
        return Reflect.ownKeys(trapTarget).filter(key => {
            return typeof key !== "string" || key[0] !== "_";
        });
    }
});

let nameSymbol = Symbol("name");

proxy.name = "proxy";
proxy._name = "private";
proxy[nameSymbol] = "symbol";

let names = Object.getOwnPropertyNames(proxy),
    keys = Object.keys(proxy);
    symbols = Object.getOwnPropertySymbols(proxy);

console.log(names.length);      // 1
console.log(names[0]);          // "name"

console.log(keys.length);      // 1
console.log(keys[0]);          // "name"

console.log(symbols.length);    // 1
console.log(symbols[0]);        // "Symbol(name)"
```

</div>

הדוגמה הזו השתמשנו במלכודת
`ownKeys`
שקוראת בהתחלה למתודה
<span dir="ltr">`Reflect.ownKeys()`</span>
כדי לקבל את כל המפתחות של אובייקט המטרה. ואז במתודה
<span dir="ltr">`filter()`</span>
נעשה שימוש לסנן החוצה את המפתחות שמתחילות עם סימון של קו-תחתון. לאחר מכן מוספים שלושה מאפיינים לאובייקט פרוקסי
`proxy` : `name`,
`_name`,
ו
`nameSymbol`.
כאשר
<span dir="ltr">`Object.getOwnPropertyNames()`</span>
ו
<span dir="ltr">`Object.keys()`</span>
נקראות על פרוקסי
`proxy`,
רק המאפיין
`name`
מוחזר. באופן דומה
`nameSymbol`
מוחזר כאשר
<span dir="ltr">`Object.getOwnPropertySymbols()`</span>
נקרא על
`proxy`.
המאפיין
`_name`
לא מופיע בשום תומאה מאחר והוא סונן החוצה.

I> המלכודת
`ownKeys`
משפיעה גם על הלולאה
`for-in`,
הקוראת למלכודת כדי לקבוע באילו מפתחות להשתמש בתוך הלולאה.

## פונקציות פרוקסי עם מלכודות `apply` ו `construct`

מבין כל מלכודות הפרוקסי, רק
`apply`
ו
`construct`
דורשים שהמטרה של הפרוקסי תהיה פונקציה. נזכיר מפרק 3 שלפונקציות שתי מתודות פנימיות הנקראות
`[[Call]]`
ו
`[[Construct]]`
המבוצעות כאשר פונקציה נקראת ללא ועם האופרטור
`new` ,
בהתאמה. המלכודות
`apply`
ו
`construct`
תואמים ומאפשרים לך לעקוף את אותן המתודות הפנימיות. כאשר פונקציה נקראית בלי באופרטוק `new`,
המלכודת
`apply`
ו
<span dir="ltr">`Reflect.apply()`</span>
מקבלים ומצפים , לארגומנטים הבאים :

1. `trapTarget` - הפונקציה שמבוצעת
(the proxy's target)
1. `thisArg` - הערך של
`this`
בתוך הפונקציה במהלך הקריאה
1. `argumentsList` - מערך של ארגומנטים שהועברו לפונקציה

מלכודת
`construct`,
אשר נקרא כאשר הפונקציה מבוצעת באמצעות
`new`,
מקבלת את הארגומנטים הבאים:

1. `trapTarget` - הפונקציה שמבוצעת
(the proxy's target)
1. `argumentsList` - מערך של ארגומנטים שהועברו לפונקציה

המתודה
<span dir="ltr">`Reflect.construct()`</span>
מקבלת גם את שני הארגומנטים הללו ויש להם ארגומנט שלישי אופציונלי שנקרא
`newTarget`.
כאשר ניתן, הארגומנט
`newTarget`
מציין את הערך של
`new.target`
בתוך הפונקציה.

יחד, מלכודות
`apply`
ו
`construct`
שולטות באופן מוחלט בהתנהגות של כל פונקציית יעד של פרוקסי. בשביל לחקות את התנהגות ברירת המחדל של פונקציה, אתה יכול לעשות זאת:

<div dir="ltr">

```js
let target = function() { return 42 },
    proxy = new Proxy(target, {
        apply: function(trapTarget, thisArg, argumentList) {
            return Reflect.apply(trapTarget, thisArg, argumentList);
        },
        construct: function(trapTarget, argumentList) {
            return Reflect.construct(trapTarget, argumentList);
        }
    });

// פרוקסי עם פונקציה כמטרה שלו נראה כמו פונקציה
console.log(typeof proxy);                  // "function"

console.log(proxy());                       // 42

var instance = new proxy();
console.log(instance instanceof proxy);     // true
console.log(instance instanceof target);    // true
```

</div>


בדוגמה זו יש פונקציה שמחזירה את המספר 42.
ה-
proxy
עבור פונקציה זו משתמש במלכודות
`apply`
ו
`construct`
להאציל התנהגויות אלה למתודות
<span dir="ltr">`Reflect.apply()`</span>
ו
<span dir="ltr">`Reflect.construct()`</span>,
בהתאמה. התוצאה הסופית היא שפונקציית ה-
Proxy
פועלת בדיוק כמו פונקציית היעד, כולל זיהוי עצמו כפונקציה כאשר משתמשים ב-
`typeof`.
כאשר הפרוקסי נקרא בלי
`new`
הוא יחזיר
42
וכאשר נקרא עם
`new`
הוא יצור אובייקט שנקרא
`instance`.
האובייקט
`instance`
נחשב למופע של שניהם- של
`proxy`
ו
`target`
בגלל
`instanceof`
משתמש בשרשרת אב-הטיפוס
<span dir="ltr">(prototype chain)</span>
כדי לקבוע מידע זה. בדיקת שרשרת אב-טיפוס אינה מושפעת מהפרוקסי, וזו הסיבה ש-
`proxy`
ו
`target`
נראים בעלי אב-טיפוס זהה למנוע ה-
JavaScript.

### אימות פרמטרים של פונקציה

המלכודות
`apply`
ו
`construct`
לפתוח הרבה אפשרויות לשינוי אופן ביצוע הפונקציה. לדוגמה, נניח שברצונך לאמת שכל הארגומנטים הם מסוג מסוים. אתה יכול לבדוק את הטיעונים במלכודת
`apply`:

<div dir="ltr">

```js

// מוסיף יחד את כל הארגומנטים
function sum(...values) {
    return values.reduce((previous, current) => previous + current, 0);
}

let sumProxy = new Proxy(sum, {
        apply: function(trapTarget, thisArg, argumentList) {

            argumentList.forEach((arg) => {
                if (typeof arg !== "number") {
                    throw new TypeError("All arguments must be numbers.");
                }
            });

            return Reflect.apply(trapTarget, thisArg, argumentList);
        },
        construct: function(trapTarget, argumentList) {
            throw new TypeError("This function can't be called with new.");
        }
    });

console.log(sumProxy(1, 2, 3, 4));          // 10

// יזרוק שגיאה
console.log(sumProxy(1, "2", 3, 4));

// גם יזרוק שגיאה
let result = new sumProxy();
```

</div>

בדוגמה הזו שנו משתמשים במלכודת
`apply`
להבטיח שכל הארגומנטים הם מספרים. הפונקציה
<span dir="ltr">`sum()`</span>
מחברת את כל הפרמטרים המועברים. אם מועבר ערך שאינו מספר, הפונקציה עדיין תנסה לבצע את הפעולה, מה שעלול לגרום לתוצאות בלתי צפויות. ע"י זה שאנחנו עוטפים את
<span dir="ltr">`sum()`</span>
בתוך הפרוקסי
<span dir="ltr">`sumProxy()`</span>,
קוד זה מיירט קריאות לפונקציה ומבטיח שכל ארגומנט הוא מספר לפני שהוא מאפשר לקריאה להמשיך. כדי להיות בטוחים, הקוד משתמש גם במלכודת
`construct`
כדי להבטיח שלא ניתן לקרוא לפונקציה עם
`new`.

אתה יכול גם לעשות את ההפך, להבטיח שחייבים לקרוא לפונקציה עם
`new`
ולאמת את הארגומנטים שלה שיהיו מספרים :

<div dir="ltr">

```js
function Numbers(...values) {
    this.values = values;
}

let NumbersProxy = new Proxy(Numbers, {

        apply: function(trapTarget, thisArg, argumentList) {
            throw new TypeError("This function must be called with new.");
        },

        construct: function(trapTarget, argumentList) {
            argumentList.forEach((arg) => {
                if (typeof arg !== "number") {
                    throw new TypeError("All arguments must be numbers.");
                }
            });

            return Reflect.construct(trapTarget, argumentList);
        }
    });

let instance = new NumbersProxy(1, 2, 3, 4);
console.log(instance.values);               // [1,2,3,4]

// יזרוק שגיאה
NumbersProxy(1, 2, 3, 4);
```

</div>

כאן, מלכודת
`apply`
יזרוק שגיאה בזמן שמלכודת
`construct`
משתמשת במתודה
<span dir="ltr">`Reflect.construct()`</span>
כדי לאמת קלט ולהחזיר מופע חדש. כמובן, שתוכלו להשיג את אותו הדבר מבלי להשתמש בפרוקסי ע"י שימוש ב
`new.target`
במקום.

### לקרוא לקונסטרקטור בלי new

בפרק 3 הצגנו את המטא-מאפיין
(metaproperty)
`new.target`.
נזכיר,
`new.target`
היא הפניה לפונקציה עליה
`new`
נקרא, כלומר תוכלו לדעת אם נקראה פונקציה באמצעות
`new`
או לא על ידי בדיקת הערך של
`new.target`
כך:

<div dir="ltr">

```js
function Numbers(...values) {

    if (typeof new.target === "undefined") {
        throw new TypeError("This function must be called with new.");
    }

    this.values = values;
}

let instance = new Numbers(1, 2, 3, 4);
console.log(instance.values);               // [1,2,3,4]

// יזרוק שגיאה
Numbers(1, 2, 3, 4);
```

</div>

דוגמה זו זורקת שגיאה מתי ש
`Numbers`
נקראית בלי
`new`,
הדומה לדוגמא בסעיף
"אימות פרמטרים של פונקציה"
אבל בלי שימוש עם פרוקסי.
כתיבת קוד כזה היא הרבה יותר פשוטה מאשר שימוש בפרוקסי ועדיפה אם המטרה היחידה שלך היא למנוע קריאה לפונקציה בלי
`new`.
אך לפעמים אינך שולט בפונקציה אשר יש לשנות את התנהגותה. במקרה זה, השימוש בפרוקסי הגיוני.

נניח שהפונקציה
`Numbers`
מוגדר בקוד שלא ניתן לשנות. אתה יודע שהקוד מסתמך על
`new.target`
וברצונך להימנע מאותה בדיקה בזמן שאתה קורא לפונקציה. ההתנהגות בעת השימוש
`new`
נקבעה כבר, כך שתוכלו פשוט להשתמש במלכודת
`apply` :

<div dir="ltr">

```js
function Numbers(...values) {

    if (typeof new.target === "undefined") {
        throw new TypeError("This function must be called with new.");
    }

    this.values = values;
}


let NumbersProxy = new Proxy(Numbers, {
        apply: function(trapTarget, thisArg, argumentsList) {
            return Reflect.construct(trapTarget, argumentsList);
        }
    });


let instance = NumbersProxy(1, 2, 3, 4);
console.log(instance.values);               // [1,2,3,4]
```

</div>

הפונקציה
`NumbersProxy`
מאפשרת לך לקרוא ל
`Numbers`
מבלי להשתמש ב
`new`
ושהיא תתנהג כאילו השתמשה ב
`new`.
לשם כך, המלכודת
`apply`
קוראת ל
<span dir="ltr">`Reflect.construct()`</span>
עם הארגומנטים המועוברים ל
`apply`.
ה
`new.target`
בתוך
`Numbers`
הוא שווה ל
`Numbers`
עצמו, ןהשגיאה תיזרק. אמנם זו דוגמה פשוטה לשינוי
`new.target`,
אתה יכול גם לעשות זאת באופן ישיר יותר.

### עקיפת מחלקה אבסטרקטית

אתה יכול ללכת צעד אחד קדימה ולפרט את הארגומנט השלישי ב
<span dir="ltr">`Reflect.construct()`</span>
כערך ספציפי שיש להקצות עבור
`new.target`.
זה יכול להיות יעיל כאשר הפונקציה בודקת את
`new.target`
מול ערך ידוע, כמו בעת יצירת קוסנרקטור לקלאס בסיס אבסטרקטי
(נדון בפרק 9).
בקונסטרקטור בקלאס אבסטקטי (מופשט)
In ,
`new.target`
מצפה להיות משהו אחר מאשר הקוסנטרקטור עצמו , כמו בדוגמה:

<div dir="ltr">

```js
class AbstractNumbers {

    constructor(...values) {
        if (new.target === AbstractNumbers) {
            throw new TypeError("This function must be inherited from.");
        }

        this.values = values;
    }
}

class Numbers extends AbstractNumbers {}

let instance = new Numbers(1, 2, 3, 4);
console.log(instance.values);           // [1,2,3,4]

// יזרוק שגיאה
new AbstractNumbers(1, 2, 3, 4);
```

</div>

כאשר
<span dir="ltr">`new AbstractNumbers()`</span>
נקרא,
`new.target`
שווה ל
`AbstractNumbers`
ושגיאה נזרקת. שקוראים ל
<span dir="ltr">`new Numbers()`</span>
יעבוד כי
`new.target`
שווה ל
`Numbers`.
אתה יכול לעקוף מגבלה זו על ידי הקצאה ידנית
`new.target`
עם פרוקסי:

<div dir="ltr">

```js
class AbstractNumbers {

    constructor(...values) {
        if (new.target === AbstractNumbers) {
            throw new TypeError("This function must be inherited from.");
        }

        this.values = values;
    }
}

let AbstractNumbersProxy = new Proxy(AbstractNumbers, {
        construct: function(trapTarget, argumentList) {
            return Reflect.construct(trapTarget, argumentList, function() {});
        }
    });


let instance = new AbstractNumbersProxy(1, 2, 3, 4);
console.log(instance.values);               // [1,2,3,4]
```

</div>

ה
`AbstractNumbersProxy`
משתמש במלכודת
`construct`
ליירט את הקריאה למתודה
<span dir="ltr">`new AbstractNumbersProxy()`</span>.
כאשר המתודה
<span dir="ltr">`Reflect.construct()`</span>
נראית עם ארגומנט מהמלכות ומוסיפים כארגומנט שלישי פונקציה ריקה. הפונקציה הריקה משמשת כערך ל
`new.target`
במקום הבנאי- הקונסרקטור. בגלל ש
`new.target`
לא שווה ל
`AbstractNumbers`,
לא נזרקית שגיאה והבנאי מופעל בשלמות.

### החלפת קונסטרקטור-בנאי של קלאס

פרק 9 הסביר שבנאי שקלאס קונסטקטור חייב להיקרא עם
`new`.
זה קורה בגלל המתודה הפנימית
`[[Call]]`
שהיא עבור קלאס קונסטרקטור שמוגדרת לזרוק שגיאה. אבל פרוקסי יכול ליירט קריאות למתודה
`[[Call]]`,
זאת אומרת שאת היכול ליצור ביעילות בנאי של קונסטרקטור על ידי שימוש בפרוקסי
.לדוגמה, אם אתה רוצה שקלאס קונסטרקטור יעבוד בלי
`new`,
אתה יכול להשתמש במלכודת
`apply`
על מנת ליצור ישות חדשה . הנה קוד לדוגמה:

<div dir="ltr">

```js
class Person {
    constructor(name) {
        this.name = name;
    }
}

let PersonProxy = new Proxy(Person, {
        apply: function(trapTarget, thisArg, argumentList) {
            return new trapTarget(...argumentList);
        }
    });


let me = PersonProxy("Nicholas");
console.log(me.name);                   // "Nicholas"
console.log(me instanceof Person);      // true
console.log(me instanceof PersonProxy); // true
```

</div>

האובייקט
`PersonProxy`
הוא פרוקסי של קלאס-קונסטרקטור
`Person`.
קלאס קונסטרקטור הוא סכ"ה פונקציה,אז ההתנהגות שלו תהיה כמו פונקציה שנשתמש בפרוקסי.מלכודת
`apply`
היא תשכתב את ההתנהגות הדיפולטיבית ובבמקום זאת תחזיר אינסטנס חדש של
`trapTarget`
שזה שווה ערך ל
`Person`.
(
בדוגמה זאת אני משתמש ב
`trapTarget`
להראות שאתה לא צריך ידנית להגדיר קלאס.
)
ה
`argumentList`
מועבר ל
`trapTarget`
בשימוש ה
spread operator
להעברת הארגונמטים. קריאה ל
<span dir="ltr">`PersonProxy()`</span>
בלי להשתמש
`new`
יחזיר מופע של
`Person`;
אם אתה תנסה לקרוא ל
<span dir="ltr">`Person()`</span>
בלי
`new`,
הקונסטרקטור עדיין יזרוק שגיאה. ליצור אפשרות לקרוא לקלאס קונסטקרטור זה משהו שאפשרי רק ע"י שימוש בפרוקסי.

## לבטל פרוקסי

בדרך כלל פרוקסי לא יכול להתנתק מהמטרה שלו ארי הוא נוצר. כל הדוגמאות בפרק זה השתמשנו בפרוקסים בלתי ניתנים לביטול. אך יתכנו מצבים שבהם ברצונך לבטל פרוקסי כך שלא ניתן יהיה להשתמש בו עוד. תמצא שזה מועיל ביותר לבטל פרוקסי עבודה כאשר ברצונך לספק אובייקט באמצעות API למטרות אבטחה ולשמור על היכולת לנתק את הגישה לפונקציונליות כלשהי בכל נקודת זמן.

אתה יכול ליצור פרוקסי הניתן לביטול ע"י המתודה
<span dir="ltr">`Proxy.revocable()`</span>
, שלוקחת את אותם ארגומנטים כמו הבנאי של  ה
`Proxy` --
אובייקט מטרה ואת המטפל של הפרוקסי
(handler).
ערך ההחזרה הוא אובייקט עם המאפיינים הבאים:

1. `proxy` - אובייקט ה-
proxy
שניתן לבטל
1. `revoke` - הפונקציה להתקשר לביטול ה-
Proxy

כאשר הפונקציה
<span dir="ltr">`revoke()`</span>
נקראית, לא ניתן לבצע פעולות נוספות דרך
`proxy`.
כל ניסיון לקיים אינטראקציה עם אובייקט ה-
proxy
יגרום למלכודת
proxy
לזרוק שגיאה. לדוגמה:

<div dir="ltr">

```js
let target = {
    name: "target"
};

let { proxy, revoke } = Proxy.revocable(target, {});

console.log(proxy.name);        // "target"

revoke();

// יזרוק שגיאה
console.log(proxy.name);
```

</div>

בדוגמה הזו יצרנו פרוקסי הניתן לביטול. השתמשנו ב
destructuring
להשמה של המשתנים
`proxy`
ו
`revoke`
לתכונות עם אותו שם שהחוזרו ע"י המתודה
<span dir="ltr">`Proxy.revocable()`</span>.
לאחר מכן,ניתן להשתמש  באובייקט
`proxy`
כמו כל פרוקסי שלא ניתן לביטול, כך ש
`proxy.name`
יחזיר
`"target"`
כי זה עובר ל
`target.name`.
כאשר הפונקציה
<span dir="ltr">`revoke()`</span>
נקראת, מאידך,
`proxy`
כבר לא מתפקד. נסיון לגשת ל
`proxy.name`
יזרוק שגיאה, כמו כל פעולה אחרת שתפעיל מלכודת
`proxy`.

## לפתור את בעיית המערך

בתחילת פרק זה הסברתי כיצד מפתחים לא יכולים לחקות את ההתנהגות של מערך במדויק ב-
JavaScript
לפני
ECMAScript 6.
פרוקסי וה
API
של ההשתקפות
(reflection API)
מאפשרים לך ליצור אובייקט שמתנהג כמו הסוג המובנה של
`Array`
כאשר מוסיפים או מחסירים ערכים. לריענון הזיכרון, הנה מספר דוגמאות
שפרוקסי יכול לחקות:

<div dir="ltr">

```js
let colors = ["red", "green", "blue"];

console.log(colors.length);         // 3

colors[3] = "black";

console.log(colors.length);         // 4
console.log(colors[3]);             // "black"

colors.length = 2;

console.log(colors.length);         // 2
console.log(colors[3]);             // undefined
console.log(colors[2]);             // undefined
console.log(colors[1]);             // "green"
```

</div>

בדוגמה זו ישנן שתי התנהגויות חשובות במיוחד:

1. המאפיין
`length`
יוגדל לארבע כאשר תהיה השמה של ערך ב
`colors[3]`.
1. שני הערכים האחרונים של המערך ימחקו כאשר המאפיין
`length`
מוגדר ל 2.

שתי ההתנהגויות הללו הן היחידות שצריך לחקות כדי לשחזר במדויק את האופן שבו מערכים מובנים עובדים. החלקים הבאים הבאים מתארים כיצד ליצור אובייקט שמחקה אותם נכון.

### איתור מדדי מערך

קחו בחשבון שהקצאה למפתח מספר שלם הוא מקרה מיוחד עבור מערכים, מכיוון שאלו מטופלים באופן שונה ממפתחות שאינם מספרים. מפרט
ECMAScript 6
נותן הוראות אלה כיצד לקבוע אם ערך מפתח הוא אינדקס מערך:

<span dir="ltr">

> A String property name `P` is an array index if and only if `ToString(ToUint32(P))` is equal to `P` and `ToUint32(P)` is not equal to 2^32^-1.

</span>

ניתן ליישם פעולה זו ב-
JavaScript
באופן הבא:

<div dir="ltr">

```js
function toUint32(value) {
    return Math.floor(Math.abs(Number(value))) % Math.pow(2, 32);
}

function isArrayIndex(key) {
    let numericKey = toUint32(key);
    return String(numericKey) == key && numericKey < (Math.pow(2, 32) - 1);
}
```

</div>

הפונקציה
<span dir="ltr">`toUint32()`</span>
ממירה ערך שמתקבל ממיר ערך נתון למספר שלם של  32 סיביות לא חתום באמצעות אלגוריתם המתואר במפרט. הפונקציה
<span dir="ltr">`isArrayIndex()`</span>
ראשית ממיר את המפתח ל-
uint32
ואז מבצע את ההשוואות כדי לקבוע אם המפתח הוא אינדקס מערך או לא. עם פונקציות שירות אלה זמינות, אתה יכול להתחיל ליישם אובייקט שיחקה מערך מובנה.

### הגדלת האורך בעת הוספת אלמנטים חדשים

אולי שמתם לב ששתי התנהגויות המערך שתיארתי מסתמכות על הקצאת ערך. זה אומר שאתה באמת צריך להשתמש רק במלכודת
`set`
של פרוקסי להשיג את ההתנהגות הזו. כדי להתחיל, הנה דוגמה המיישמת את ההתנהגות הראשונה משתיים על ידי הגדלת המאפיין
`length`
כאשר משתמשים באינדקס מערך גדול מ
`length - 1` :

<div dir="ltr">

```js
function toUint32(value) {
    return Math.floor(Math.abs(Number(value))) % Math.pow(2, 32);
}

function isArrayIndex(key) {
    let numericKey = toUint32(key);
    return String(numericKey) == key && numericKey < (Math.pow(2, 32) - 1);
}

function createMyArray(length=0) {
    return new Proxy({ length }, {
        set(trapTarget, key, value) {

            let currentLength = Reflect.get(trapTarget, "length");

            // המקרה המיוחד
            if (isArrayIndex(key)) {
                let numericKey = Number(key);

                if (numericKey >= currentLength) {
                    Reflect.set(trapTarget, "length", numericKey + 1);
                }
            }

            // עשה זאת תמיד ללא קשר לסוג המפתח
            return Reflect.set(trapTarget, key, value);
        }
    });
}

let colors = createMyArray(3);
console.log(colors.length);         // 3

colors[0] = "red";
colors[1] = "green";
colors[2] = "blue";

console.log(colors.length);         // 3

colors[3] = "black";

console.log(colors.length);         // 4
console.log(colors[3]);             // "black"
```

</div>

הדוגמה הזו משתמשת במלכודת פרוקסי
`set`
ליירט הגדרת אינדקס מערך. אם המפתח הוא אינדקס מערך, הוא יומר למספר מכיוון שמפתחות מועברים תמיד כמחרוזות. בשלב הבא, אם הערך המספרי הזה גדול או שווה למאפיין
`length`
הנוכחי, אז המאפיין
`length`
מתעדכן להיות אחד יותר מהמפתח המספרי
(
הגדרה של פריט במיקום 3 פירושה ש
`length`
חייב להיות 4
)
. לאחר מכן משתמשים בהתנהגות ברירת המחדל להגדרת מאפיין באמצעות
<span dir="ltr">`Reflect.set()`</span>,
מכיוון שאתה רוצה שהמאפיין יקבל את הערך כמפורט.

המערך המותאם אישית מאותחל על ידי קריאה ל
<span dir="ltr">`createMyArray()`</span>
עם
`length`
של 3 והערכים לשלושת הפריטים מתווספים מיד לאחר מכן. המאפיין
`length`
נכון לעכשיו נשאר 3 עד שהערך
`"black"`
מוקצה למיקום  3. בנקודה הזו,
`length`
מוגדר ל 4.

כאשר ההתנהגות הראשונה עובדת, הגיע הזמן לעבור לשנייה.

### מחיקת אלמנטים כאשר מצמצמים את האורך - length

ההתנהגות הראשונה לחיקוי המערך תהיה רק כאשר  האינדקס של המערך יהיה שווה או גדול מהערך של המאפיין
`length`.
ההתנהגות השנייה עושה את ההפך ומסירה פריטי מערך כאשר מאפיין
`length`
מוגדר לערך קטן יותר מכפי שהיה קודם. זה כרוך לא רק בשינוי מאפיין
`length` ,
אלא גם במחיקת כל הפריטים שעלולים להתקיים. לדוגמה אם מערך עם
`length`
של 4 וה
`length`
מוגדר ל 2, האייטמים במקומות  2 ו 3 ימחקו. אתה יכול להשיג זאת בתוך מלכודת הפרוקסי
`set`
לצד ההתנהגות הראשונה. הנה הדוגמה הקודמת שוב, עם עידכון למתודה
`createMyArray`:

<div dir="ltr">

```js
function toUint32(value) {
    return Math.floor(Math.abs(Number(value))) % Math.pow(2, 32);
}

function isArrayIndex(key) {
    let numericKey = toUint32(key);
    return String(numericKey) == key && numericKey < (Math.pow(2, 32) - 1);
}

function createMyArray(length=0) {
    return new Proxy({ length }, {
        set(trapTarget, key, value) {

            let currentLength = Reflect.get(trapTarget, "length");

            // המקרה המיוחד
            if (isArrayIndex(key)) {
                let numericKey = Number(key);

                if (numericKey >= currentLength) {
                    Reflect.set(trapTarget, "length", numericKey + 1);
                }
            } else if (key === "length") {

                if (value < currentLength) {
                    for (let index = currentLength - 1; index >= value; index--) {
                        Reflect.deleteProperty(trapTarget, index);
                    }
                }

            }

            // עשה זאת תמיד ללא קשר לסוג המפתח
            return Reflect.set(trapTarget, key, value);
        }
    });
}

let colors = createMyArray(3);
console.log(colors.length);         // 3

colors[0] = "red";
colors[1] = "green";
colors[2] = "blue";
colors[3] = "black";

console.log(colors.length);         // 4

colors.length = 2;

console.log(colors.length);         // 2
console.log(colors[3]);             // undefined
console.log(colors[2]);             // undefined
console.log(colors[1]);             // "green"
console.log(colors[0]);             // "red"
```

</div>

המלכודת
`set`
בקוד הזה בודקת אם
`key`
הוא
`"length"`
על מנת להתאים נכון את שאר האובייקט.כאשר זה קורה, האורך הנוכחי נשלף תחילה
באמצעות
<span dir="ltr">`Reflect.get()`</span>
ומושווה לערך החדש. אם הערך החדש הוא קטן מהאורך הנוכחי, אזי הלופ
`for`
מוחק את כל המאפיינים ביעד שאינם אמורים להיות זמינים עוד. הלופ
`for`
הולך לאחור מאורך המערך הנוכחי
(`currentLength`)
ומוחק כל נכס עד שהוא מגיע לאורך המערך החדש
(`value`).

דוגמה זו מוסיפה ארבעה צבעים ל
`colors`
ואז קובעת את המאפיין
`length`
ל 2. זה מסיר ביעילות את הפריטים בעמדות 2 ו -3, כך שהם חוזרים כעת
`undefined`
שמנסים לגשת אליהם. המאפיין
`length`
המאפיין מוגדר כ- 2 והפריטים בעמדות 0 ו- 1 עדיין נגישים.

כאשר שתי ההתנהגויות מיושמות, תוכלו ליצור בקלות אובייקט המחקה את ההתנהגות של מערכים מובנים. אולם פעולה כזו עם פונקציה אינה רצויה כמו יצירת מחלקה שתכסה את ההתנהגות הזו, אז השלב הבא הוא ליישם פונקציונליות זו כמחלקה-קלאס.

### הטמעת מחלקת MyArray

הדרך הפשוטה ביותר ליצור מחלקה המשתמשת בפרוקסי היא להגדיר את המחלקה כרגיל ואז להחזיר פרוקסי מהבנאי. באופן זה, האובייקט המוחזר כאשר מופעלת המחלקה יהיה ה- proxy במקום המופע.
(
המופע הוא הערך של
`this`
בתוך הבנאי.
)
המופע הופך להיות היעד של ה-
proxy
וה-
proxy
מוחזר כאילו היה המופע. המופע יהיה פרטי לחלוטין ולא תוכלו לגשת אליו ישירות, אם כי תוכלו לגשת אליו בעקיפין דרך ה-
proxy.

להלן דוגמה פשוטה להחזרת שרת פרוקסי מבונה כיתתי:

<div dir="ltr">

```js
class Thing {
    constructor() {
        return new Proxy(this, {});
    }
}

let myThing = new Thing();
console.log(myThing instanceof Thing);      // true
```

</div>

בדוגמה הזו המחלקה
`Thing`
מחזירה פרוקסי מתוך הבנאי. המטרה של הפרוקסי היא
`this`
והפרוקסי מוחזר מהבנאי. הכוונה ש
`myThing`
הוא למעשה פרוקסי למרות שזה נוצר על ידי הקריאה לבנאי של
`Thing`.
בגלל שפרוקסים מעבירים דרך ההתנהגות שלהם אל היעד שלהם,
`myThing`
נחשב עדיין למופע של
`Thing`,
מה שהופך את ה-
Proxy
לשקוף לחלוטין לכל מי שמשתמש במחלקה
`Thing`.

עם זאת בחשבון, יצירת כיתת מערך בהתאמה אישית באמצעות פרוקסי הוא יחסית פשוט. הקוד זהה בעיקר לקוד בסעיף "מחיקת אלמנטים על צמצום האורך"
.משתמשים באותו קוד פרוקסי, אך הפעם הוא נמצא בתוך בנאי כיתתי. להלן הדוגמה המלאה:

<div dir="ltr">

```js
function toUint32(value) {
    return Math.floor(Math.abs(Number(value))) % Math.pow(2, 32);
}

function isArrayIndex(key) {
    let numericKey = toUint32(key);
    return String(numericKey) == key && numericKey < (Math.pow(2, 32) - 1);
}

class MyArray {
    constructor(length=0) {
        this.length = length;

        return new Proxy(this, {
            set(trapTarget, key, value) {

                let currentLength = Reflect.get(trapTarget, "length");

                // המקרה המיוחד
                if (isArrayIndex(key)) {
                    let numericKey = Number(key);

                    if (numericKey >= currentLength) {
                        Reflect.set(trapTarget, "length", numericKey + 1);
                    }
                } else if (key === "length") {

                    if (value < currentLength) {
                        for (let index = currentLength - 1; index >= value; index--) {
                            Reflect.deleteProperty(trapTarget, index);
                        }
                    }

                }

                // עשה זאת תמיד ללא קשר לסוג המפתח
                return Reflect.set(trapTarget, key, value);
            }
        });

    }
}


let colors = new MyArray(3);
console.log(colors instanceof MyArray);     // true

console.log(colors.length);         // 3

colors[0] = "red";
colors[1] = "green";
colors[2] = "blue";
colors[3] = "black";

console.log(colors.length);         // 4

colors.length = 2;

console.log(colors.length);         // 2
console.log(colors[3]);             // undefined
console.log(colors[2]);             // undefined
console.log(colors[1]);             // "green"
console.log(colors[0]);             // "red"
```

</div>

הקוד הזה יצר מחלקה
`MyArray`
שמחזירה פרוקסי מתוך הבנאי שלה.המאפיין
`length`
מתווסף בתוך הבנאי
(מאתחל לערך שמועבר או לערך ברירת מחדל של 0)
והפרוקסי נוצר ומוחזר. זה נותן למשתנה
`colors`
את הנראות להיות רמופע של
`MyArray`
ומיישם את שתי התנהגויות של המערך והמפתח.

אמנם קל להחזיר פרוקסי מבנאימחלקה, אך זה אומר שנוצר פרוקסי חדש עבור כל מופע. עם זאת יש דרך לגרום לכל המקרים לשתף פרוקסי אחד: אתה יכול להשתמש בפרוקסי כאב-טיפוס -
prototype.

## שימוש בפרוקסי כאב-טיפוס - Prototype

פרוקסיות יכולות לשמש כאבות-טיפוס, אך פעולה זו מסובכת מעט יותר מהדוגמאות הקודמות בפרק זה. כאשר פרוקסי הוא אב-טיפוס, מלכודות ה-
Proxy
נקראות רק כאשר פעולת ברירת המחדל תמשיך בדרך כלל לאב-טיפוס, מה שמגביל את יכולות ה-
Proxy
כאב-טיפוס. שקול דוגמה זו:

<div dir="ltr">

```js
let target = {};
let newTarget = Object.create(new Proxy(target, {

    // לעולם לא יקרא
    defineProperty(trapTarget, name, descriptor) {

        // יחזיר שגיאה אם יקרא
        return false;
    }
}));

Object.defineProperty(newTarget, "name", {
    value: "newTarget"
});

console.log(newTarget.name);                    // "newTarget"
console.log(newTarget.hasOwnProperty("name"));  // true
```

</div>

האובייקט
`newTarget`
נוצר באמצעות פרוקסי כאב-טיפוס. הפיכת
`target`
למטרת הפרוקסי באופן יעיל עושה  את
`target`
לאב-טיפוס של
`newTarget`
בגלל שהפרוקסי הוא שקוף. כעת, מלכודות פרוקסי יקראו רק אם פעולה ב-
`newTarget`
תעביר את הפעולה לקרות ב
`target`.

המתודה
<span dir="ltr">`Object.defineProperty()`</span>
נקראית ב
`newTarget`
ליצירת מאפיין משלו בשם
`name`.
הגדרת מאפיין על אובייקט אינה פעולה שממשיכה בדרך כלל לאב-טיפוס של האובייקט, כך שמלכודת
`defineProperty`
על הפרוקסי לעולם לא נקראת והמאפיין
`name`
מתווסף ל
`newTarget`
כמאפיין משל עצמו.

בעוד שיכולות פרוקסי  מוגבלים מאוד כאשר משתמשים בהם כאבות-טיפוס, ישנם כמה מלכודות שעדיין מועילות.

### שימוש במלכודת `get` על מאפיין

כאשר הפונקציה הפנימית
`[[Get]]`
נקראית כדי לקבל מאפיין, הפעולה מחפשת תחילה במאפיינים שלו.אם לא נמצא מאפיין משלו עם השם הנתון, הפעולה ממשיכה לאב-טיפוס ומחפשת שם מאפיין. התהליך נמשך עד שלא יהיו אבטיפוסים נוספים לבדיקה.

תודה לתהליך זה, אם תגדיר את מלכודת הפרוקסי
`get` ,
המלכודת תיקרא באב-טיפוס בכל פעם שאין מאפיין משלו בשם הנתון. רתה יכול להשתמש במלכודת
`get`
כדי למנוע התנהגות בלתי צפויה בעת כניסה למאפיינים שאינך יכול להבטיח שיהיו קיימים. פשוט צור אובייקט שזורק שגיאה בכל פעם שאתה מנסה לגשת למאפיין שאינו קיים:

<div dir="ltr">

```js
let target = {};
let thing = Object.create(new Proxy(target, {
    get(trapTarget, key, receiver) {
        throw new ReferenceError(`${key} doesn't exist`);
    }
}));

thing.name = "thing";

console.log(thing.name);        // "thing"

// יזרוק שגיאה
let unknown = thing.unknown;
```

</div>

בקוד הזה האובייקט
`thing`
האובייקט נוצר באמצעות פרוקסי כאב-טיפוס שלו. המלכודת
`get`
זורקת שגיאה כאשר היא נקראת כדי לציין שהמפתח הנתון אינו קיים באובייקט -
`thing`.
כאשר
`thing.name`
נקרא, הפעולה אינה קוראת לעולם למלכודת
`get`
באב-טיפוס מכיוון שהמאפיין קיים על
`thing`.
מלכודת
`get`
נקראית רק כאשר קוראים למאפיין
`thing.unknown` ,
שלא קיים ואין אליו גישה.

כאשר השורה האחרונה מתבצעת,
`unknown`
אינו מאפיין של
`thing`,
כך שהפעולה ממשיכה לאב-טיפוס. המלכודת
`get`
אזי זוקרת שגיאה. התנהגות מסוג זה יכולה להיות שימושית מאוד ב-
JavaScript,
כאשר תכונות לא ידועות חוזרות בשקט
`undefined`
במקום לזרוק שגיאה
(כמו שקורה בשפות אחרות).

חשוב להבין כי בדוגמה זו,
`trapTarget`
ו
`receiver`
הם אובייקטים שונים. כאשר פרוקסי משמש כאב-טיפוס, ה
`trapTarget`
הוא האובייקט האבטיפוס עצמו בעוד
`receiver`
הוא אובייקט המופע. במקרה זה, משמעות הדבר היא כי
`trapTarget`
שווה ל
`target`
ו
`receiver`
שווה ל
`thing`.
זה מאפשר לך לגשת הן למטרה המקורית של ה-
Proxy
והן לאובייקט עליו אמורה הפעולה להתקיים.

### שימוש במלכודת `set` על אבטיפוסים - Prototype

המתודה הפנימית
`[[Set]]`
בודק גם מאפיינים משלו ואז ממשיך לאב-טיפוס במידת הצורך. כשאתה מקצה ערך למאפיין באובייקט, הערך מוקצה למאפיין עצמו עם אותו שם אם הוא קיים. אם אין מאפיין משלו עם השם הנתון, הפעולה ממשיכה לאב-טיפוס. החלק המסובך הוא שלמרות שפעולת ההקצאה ממשיכה לאב-טיפוס, הקצאת ערך למאפיין זה תיצור מאפיין במופע
(ולא אב-הטיפוס)
כברירת מחדל, ללא קשר אם קיים מאפיין בשם זה באב-הטיפוס.

כדי לקבל מושג טוב יותר מתי נקרא מלכודת
`set`
באב-טיפוס ומתי  לא , הבט בדוגמה הבאה שמציגה את התנהגות ברירת המחדל:

<div dir="ltr">

```js
let target = {};
let thing = Object.create(new Proxy(target, {
    set(trapTarget, key, value, receiver) {
        return Reflect.set(trapTarget, key, value, receiver);
    }
}));

console.log(thing.hasOwnProperty("name"));      // false

// מפעיל את מלכודת ה-  `set`
thing.name = "thing";

console.log(thing.name);                        // "thing"
console.log(thing.hasOwnProperty("name"));      // true

// לא מפעיל את המלכודת `set`
thing.name = "boo";

console.log(thing.name);                        // "boo"
```

</div>

בדוגמה זו,
`target`
מתחיל ללא מאפיינים משלו. האובייקט
`thing`
יש לו פרוקסי כאב-טיפוס שלו שמגדיר המלכודת
`set`
לתפוס את היצירה של כל מאפיינים חדשים. כאשר
`thing.name`
מוקצה
`"thing"`
כערך שלו,מהלכודת פרוקסי
`set`
נקראית בגלל של
`thing`
אין לו מאפיין משלו שנקרא
`name`.
בתוך מלכודת
`set` ,
`trapTarget`
שווה ערך ל
`target`
ו
`receiver`
שווה ל
`thing`.
הביצוע אמור בסופו של דבר ליצור מאפיין חדש ב
`thing`,
ולמרבה המזל
<span dir="ltr">`Reflect.set()`</span>
מיישם עבורך התנהגות ברירת מחדל אם תעביר
`receiver`
כארגומנט רביעי.

כאשר המאפיין
`name`
נוצר על
`thing`,
הגדרת
`thing.name`
לערך שונה לא תקרא למלכודת
`set`
.בנקודה זו,
`name`
הוא מאפיין משלו אז
`[[Set]]`
הפעולה אף פעם לא ממשיכה לאב-טיפוס.

### שימוש במלכודת `has` על אבטיפוסים - Prototype

נזכיר שמלכודת ה
`has`
מיירטת את השימוש באופרטור
`in`
על אובייקטים. האופרטור
`in`
מחפש תחילה מאפיין של אובייקט עם השם הנתון. אם מאפיין משלו בשם זה לא קיים, הפעולה ממשיכה לאב-טיפוס. אם אין מאפיין דומה באב-הטיפוס, החיפוש יימשך דרך שרשרת האבות-טיפוס עד שנמצא המאפיין עצמו או שאין יותר טיפוס לחיפוש.

לפיכך נקראת מלכודת ה
`has`
רק כאשר החיפוש מגיע לאובייקט ה-
proxy
בשרשרת האב-טיפוס. כשמשתמשים בפרוקסי כאב-טיפוס, זה קורה רק כאשר אין מאפיין משלו בשם זה. לדוגמה:

<div dir="ltr">

```js
let target = {};
let thing = Object.create(new Proxy(target, {
    has(trapTarget, key) {
        return Reflect.has(trapTarget, key);
    }
}));

// יפעיל את מלכודת `has`
console.log("name" in thing);                   // false

thing.name = "thing";

// לא יפעיל את מלכודת `has`
console.log("name" in thing);                   // true
```

</div>

הקוד כאן יוצר מלכודת
`has`
באב-טיפוס של
`thing`.
המלכודת
לא מעבירה
`has`
את האובייקט
`receiver`
כמו המלכודות
`get`
ו
`set`
מכיוון שחיפוש באב-טיפוס מתרחש באופן אוטומטי כאשר משתמשים באופרטור
`in`.
במקום זאת, מלכודת
`has`
חייבת לפעול רק על
`trapTarget`,
שזה שווה ל
`target`.
בפעם הראשונה האופרטור
`in`
משמש בדוגמה זו, המלכודת
`has`
נקראית מכיוון ששהמאפיין
`name`
לא קיים כמאפיין משל עתמו על
`thing`.
כאשר
`thing.name`
מקבל ערך ושוב משתמשים באופרטור
`in`
, המלכודת
`has`
לא נקראת מכיוון שהפעולה נפסקת לאחר מציאת המאפיין האישי
`name`
בתוך
`thing`.

דוגמאות האבטיפוס לנקודה זו התרכזו סביב אובייקטים שנוצרו באמצעות המתודה
<span dir="ltr">`Object.create()`</span>.
אבל אם אתה רוצה ליצור מחלקה שיש בה פרוקסי כאב-טיפוס, התהליך מורכב קצת יותר.

### פרוקסים כאבות-טיפוס למחלקות

לא ניתן לשנות מחלקות באופן ישיר להשתמש כפרוקסי כאב טיפוס מכיוון שמאפיין ה
`prototype`
שלהם לא ניתן לכתיבה -
non-writable.
עם זאת, באפשרותך להשתמש במעט הכוונה שגויה כדי ליצור מחלקה שיש בה פרוקסי כאב-טיפוס שלה באמצעות ירושה. כדי להתחיל, עליך ליצור בסגנון
ECMAScript 5-
הגדרת באמצעות פונקציית קונסטרוקטור. לאחר מכן באפשרותך להחליף את אב-הטיפוס כפרוקסי. הנה דוגמה:

<div dir="ltr">

```js
function NoSuchProperty() {
    // ריק
}

NoSuchProperty.prototype = new Proxy({}, {
    get(trapTarget, key, receiver) {
        throw new ReferenceError(`${key} doesn't exist`);
    }
});

let thing = new NoSuchProperty();

// זורק שגיאה עקב
// `get` proxy trap
let result = thing.name;
```

</div>

הפונקציה
`NoSuchProperty`
מייצגת את הבסיס ממנו תירש המחלקה. אין הגבלות על המאפיין
`prototype`
של פונקציות,כך שתוכל להחליף אותו באמצעות פרוקסי. המלכודת
`get`
בשימוש לזרוק שגיאה כאשר המאפיין אינו קיים. האובייקט
`thing`
נוצר כמופע של
`NoSuchProperty`
וזורק שגיאה כאשר ניגשים למאפיין
`name`
שאינו קיים.

השלב הבא הוא ליצור מחלקה שיורשת מ
`NoSuchProperty`.
אתה יכול פשוט להשתמש בסינטקס
`extends`
כמו שהוסבר בפרק 9 כדי להכניס את ה-
proxy
לשרשרת האב-טיפוס של מחלקה, ככה:

<div dir="ltr">

```js
function NoSuchProperty() {
    // ריק
}

NoSuchProperty.prototype = new Proxy({}, {
    get(trapTarget, key, receiver) {
        throw new ReferenceError(`${key} doesn't exist`);
    }
});

class Square extends NoSuchProperty {
    constructor(length, width) {
        super();
        this.length = length;
        this.width = width;
    }
}

let shape = new Square(2, 6);

let area1 = shape.length * shape.width;
console.log(area1);                         // 12

// יזרוק שגיאה בגלל ש "wdth" לא קיים
let area2 = shape.length * shape.wdth;
```

</div>

המחלקה
`Square`
יורשת מ
`NoSuchProperty`
אז ה-
proxy
נמצא בשרשרת אב-טיפוס של
`Square`.
האובייקט
`shape`
וצר אז כמופע חדש של
`Square`
ויש לו שני מאפיינים משלו:
`length`
ו
`width`.
קריאת הערכים של אותם נכסים מצליחה מכיוון שמלכודת
`get`
אף פעם לא נקראת. רק כאשר ניגשים למאפיין שאינו קיים על
`shape`
(
`shape.wdth`,
שגיאת הקלדה ברורה
)
גורם למלכודת
`get`
לפעול ולזרוק שגיאה.

זה מוכיח שהפרוקסי נמצא בשרשרת האב-טיפוס של
`shape`,
אך יתכן שלא יהיה ברור שה-
proxy
אינו האבטיפוס הישיר של
`shape`.
למעשה, ה-
proxy
הוא כמה צעדים במעלה שרשרת האבטיפוס מ
`shape`.
ניתן לראות זאת בצורה ברורה יותר על ידי שינוי מעט של הדוגמה הקודמת:

<div dir="ltr">

```js
function NoSuchProperty() {
    // ריק
}

// מאחסן הפניה ל- פרוקסי- שיהיה אב-הטיפוס
let proxy = new Proxy({}, {
    get(trapTarget, key, receiver) {
        throw new ReferenceError(`${key} doesn't exist`);
    }
});

NoSuchProperty.prototype = proxy;

class Square extends NoSuchProperty {
    constructor(length, width) {
        super();
        this.length = length;
        this.width = width;
    }
}

let shape = new Square(2, 6);

let shapeProto = Object.getPrototypeOf(shape);

console.log(shapeProto === proxy);                  // false

let secondLevelProto = Object.getPrototypeOf(shapeProto);

console.log(secondLevelProto === proxy);            // true
```

</div>

גרסה זו של הקוד מאחסנת את ה-
proxy
במשתנה שנקרא
`proxy`
כך שקל לזהות אחר כך. אב הטיפוס של
`shape`
הוא
`Square.prototype`,
שאינו
proxy.
אבל אב הטיפוס של
`Square.prototype`
הוא ה-
proxy
שיורש ממנו
`NoSuchProperty`.

הירושה מוסיפה שלב נוסף בשרשרת האב-טיפוס, וזה משנה מכיוון שפעולות שעלולות לגרום לקריאה למלכודת
`get`
על
`proxy`
צריכות לעבור שלב נוסף נוסף לפני שתגיע לשם. אם יש מאפיין ב
`Square.prototype`,
אז זה ימנע את קריאת מלכודת
`get`
להקרא,כמו בדוגמה הזו:

<div dir="ltr">

```js
function NoSuchProperty() {
    // ריק
}

NoSuchProperty.prototype = new Proxy({}, {
    get(trapTarget, key, receiver) {
        throw new ReferenceError(`${key} doesn't exist`);
    }
});

class Square extends NoSuchProperty {
    constructor(length, width) {
        super();
        this.length = length;
        this.width = width;
    }

    getArea() {
        return this.length * this.width;
    }
}

let shape = new Square(2, 6);

let area1 = shape.length * shape.width;
console.log(area1);                         // 12

let area2 = shape.getArea();
console.log(area2);                         // 12

// זורק שגיאה מכיוון ש "wdth" אינו קיים
let area3 = shape.length * shape.wdth;
```

</div>

כאן למחלקה
`Square`
יש מתודה
<span dir="ltr">`getArea()`</span>
. המתודה
<span dir="ltr">`getArea()`</span>
מתווספת אוטומטית ל-
`Square.prototype`
כך כאשר
<span dir="ltr">`shape.getArea()`</span>
נקראת, החיפוש אחרי המתודה
<span dir="ltr">`getArea()`</span>
מתחיל במופע
`shape`
ואז ממשיך לאב-טיפוס שלו. בגלל ש
<span dir="ltr">`getArea()`</span>
נמצא באב-טיפוס, החיפוש נעצר והפרוקסי מעולם לא נקרא. זוהי למעשה ההתנהגות שאתה רוצה במצב זה, מכיוון שלא תרצה לזרוק בטעות טעות כש
<span dir="ltr">`getArea()`</span>
נקראת.

למרות שנדרש קצת קוד נוסף כדי ליצור כיתה עם פרוקסי בשרשרת האב-טיפוס שלה, זה יכול להיות שווה את המאמץ אם אתה זקוק לפונקציונליות כזו.

## סיכום

לפני
ECMAScript 6,
אובייקטים מסוימים (כגון מערכים) הציגו התנהגות לא סטנדרטית שמפתחים לא הצליחו לשכפל. פרוקסי משנה את זה. הם מאפשרים לך להגדיר התנהגות לא-סטנדרטית משלך עבור מספר פעולות
JavaScript
ברמה נמוכה, כך שתוכל לשכפל את כל ההתנהגויות של אובייקטים מובנים של
JavaScript
באמצעות מלכודות
proxy.
מלכודות אלה נקראות מאחורי הקלעים כאשר מבצעים פעולות שונות, כמו שימוש באופרטור
`in`.

ה
API
לשיקוף
-Reflect
הוצג גם ב-
ECMAScript 6
כדי לאפשר למפתחים ליישם את התנהגות ברירת המחדל עבור כל מלכודת פרוקסי. לכל מלכודת פרוקסי יש שיטה המתאימה לאותו שם באובייקט
`Reflect` ,
תוספות נוספת של
ECMAScript 6.
באמצעות שילוב של מלכודות פרוקסי ושיטות
API
של השתקפות, זה אפשרי  לסנן פעולות מסוימות כדי להתנהג אחרת רק בתנאים מסוימים תוך ברירת המחדל להתנהגות המובנית.

פרוקסי הניתנים לביטול הם פרוקסים מיוחדים הניתנים לביטול יעיל באמצעות השימוש בפונקציה
<span dir="ltr">`revoke()`</span>.
הפונקציה
<span dir="ltr">`revoke()`</span>
מסיים את כל הפונקציונליות שב-
proxy,
כך שכל ניסיון לקיים אינטראקציה עם תכונות ה-
proxy זורק שגיאה לאחר ש
<span dir="ltr">`revoke()`</span>
נקרא. פרוקסי הניתנים לביטול חשובים לאבטחת יישומים שבהם מפתחים של צד שלישי עשויים להזדקק לגישה לאובייקטים מסוימים למשך פרק זמן מוגדר.

בעוד ששימוש בפרוקסי ישירות הוא מקרה השימוש החזק ביותר, אתה יכול גם להשתמש בפרוקסי כאב-טיפוס של אובייקט אחר. במקרה כזה, אתה מוגבל מאוד במספר מלכודות ה-
proxy
בהן אתה יכול להשתמש ביעילות. רק המלכודות
`get`, `set`,
ו
`has`
אי פעם ייקרא לפרוקסי כאשר הוא משמש כאב-טיפוס, מה שהופך את מערך השימוש במקרים להרבה יותר קטן.