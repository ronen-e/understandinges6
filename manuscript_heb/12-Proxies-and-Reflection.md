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
לייצר פרוקסי, צריך להעביר אליו
2
ארגומנטים: את אובייקט המטרה
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

1. `trapTarget` - האובייקט שיקבל את התכונות
(המטרה של הפרוקסי)
1. `key` - מזהה התכונה
(מחרוזת או סימבול)
אליה כותבים
1. `value` - הערך שנכתב לתכונה
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
(מטרת הפרוקסי)
1. `key` - מזהה התכונה
(מחרוזת או סימבול)
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

## הסתרת תכונה באמצעות מלכודת `has`

האופרטור
`in`
בודק אם תכונה קיימת על אובייקט ומחזיר
`true`
אם התכונה קיימת על האובייקט או על הפרוטוטייפ המתאימים לשם או ל
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


גם התכונה בשם
`value`
וגם התכונה בשם
`toString`
קיימים על
`object`,
לכן בשני המקרים האופרטור
`in`
יחזיר את הערך
`true`.
התכונה
`value`
נמצאת על האובייקט
`object`,
בזמן ש
`toString`
מוגדרת על הפרוטוטייפ
(של
`Object`).
פרוקסי מאפשרים ליירט את הפעולה הזו ולהחזיר ערכים שונים עבור
`in`
בעזרת מלכודת
`has`.

מלכודת
`has`
מופעלת בזמן שאופרטור
`in`
נמצא בשימוש. כאשר הוא מופעל, שני ארגומנטים מועברים למלכודת
`has`:

1. `trapTarget` - האובייקט ממנו קוראים את התכונה
(המטרה לפרוקסי)
1. `key` - שם התכונה
(סטרינג או סימבול)
אותה יש לבדוק

המתודה
<span dir="ltr">`Reflect.has()`</span>
מקבלת את אותם ארגומנטים ומחזירה את ערך ברירת המחדל עבור אופרטור
`in`.
שימוש במלכודת
`has`
וב
<span dir="ltr">`Reflect.has()`</span>
מאפשר לשנות את ההתנהגות הרגילה של אופרטור
`in`
עבור תכונות מסויימות ולהשתמש בהתנהגות הרגילה כשנרצה. אם למשל  היינו מעוניינים להסתיר את התכונה
`value`,
ניתן לעשות זאת כך:

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
של
`proxy`
בודקת האם ערך
`key`
הינו
`"value"`
ואם כן תחזיר את הערך
`false`.
אחרת, התנהגות ברירת המחדל משמשת בקריאה אל המתודה
<span dir="ltr">`Reflect.has()`</span>.
כתוצאה מכך, האופרטור
`in`
מחזיר
`false`
עבור התכונה
`value`
למרות שהתכונה
`value`
אכן קיימת על האובייקט. התכונות האחרות,
`name`
ו
`toString`,
מחזירות את הערך
`true`
כאשר משתמשים באופרטור
`in`.

## מניעת מחיקת תכונה ע"י מלכודת `deleteProperty`

האופרטור
`delete`
מוחק תכונה מתוך אובייקט ומחזיר
`true`
כאשר מצליח ו
`false`
כאשר נכשל. במצב
strict,
פקודת
`delete`
זורקת שגיאה כאשר מנסים למחוק תכונה שאינה ניתנת לשינוי קונפיגורציה
(
    nonconfigurable
    \
    לא קונפיגורבילית
)
; במצב לא
strict,
פקודת
`delete`
מחזירה את הערך
`false`.
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
// strict
let result2 = delete target.name;
console.log(result2);               // false

console.log("name" in target);      // true
```

</div>

בדוגמה לעיל התכונה
`value`
נמחקת ע"י שימוש באופרטור
`delete`
וכתוצאה האופרטור
`in`
מחזיר את הערך
`false`
בקריאה השלישית של
<span dir="ltr">`console.log()`</span>.
התכונה הלא קוניפגורבילית
`name`
לא ניתנת למחיקה ולכן האופרטור
`delete`
פשוט מחזיר
`false`
(
אם אותו הקוד ירוץ במצב
strict
תיזרק שגיאה
).
ניתן לשנות התנהגות הזו ע"י שימוש במלכודת
`deleteProperty`
של פרוקסי.

המלכודת
`deleteProperty`
מופעלת בכל פעם שהאופרטור
`delete`
מופעל על תכונה של אובייקט. המלכודת מקבלת שני ארגומנטים:

1. `trapTarget` - האובייקט ממנו מוחקים את המאפיין
(המטרה של הפרוקסי)
1. `key` - מזהה התכונה
(מחרוזת או סימבול)
אותה מוחקים

המתודה
<span dir="ltr">`Reflect.deleteProperty()`</span>
מספקת את היישום הדיפולטיבי של  המלכודת
`deleteProperty`
ומקבלת את אותם שני הארגומנטים.  ניתן לשלב את
<span dir="ltr">`Reflect.deleteProperty()`</span>
ואת מלכודת
`deleteProperty`
כדי לשנות את התנהגות האופרטור
`delete`.
לדוגמה, כך ניתן לוודא שהתכונה
`value`
לא תימחק:

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

// נסיון למחוק את
// proxy.value

console.log("value" in proxy);      // true

let result1 = delete proxy.value;
console.log(result1);               // false

console.log("value" in proxy);      // true

// נסיון למחוק את
// proxy.name

console.log("name" in proxy);       // true

let result2 = delete proxy.name;
console.log(result2);               // true

console.log("name" in proxy);       // false
```

</div>

הקוד לעיל דומה מאוד למלכודת
`has`
בכך שהמלכודת
`deleteProperty`
בודקת האם הערך עבור
`key`
הוא
`"value"`
ומחזירה
`false`
אם כן. אחרת, ההתנהגות הדיפולטיבית מופעלת ע"י המתודה
<span dir="ltr">`Reflect.deleteProperty()`<span>.
התכונה
`value`
לא ניתנת למחיקה דרך
`proxy`
בגלל המלכודת הקיימת, אך התכונה
`name`
נמחקת כרגיל. גישה זו שימושית במיוחד כשרוצים להגן על מאפיינים מלהימחק ב
strict mode
בלי לזרוק שגיאה.

## מלכודת פרוקסי לפרוטוטייפ (Prototype)

פרק ארבע הציג את המתודה
<span dir="ltr">`Object.setPrototypeOf()`<span>
שהוסיפה מהדורה
ECMAScript 6
בהמשך להוספת המתודה
<span dir="ltr">`Object.getPrototypeOf()`<span>
במהדורה
ECMAScript 5.
פרוקסי מאפשר לנו ליירט את הפעולה של שתי המתודות הללו בעזרת המלכודות `setPrototypeOf`
ו
`getPrototypeOf`.

בשני המקרים, המתודות על האובייקט
`Object`
קוראות למלכודת המתאימה בפרוקסי, ומאפשרות לשנות את התנהגות המתודות.

מאחר ויש 2 מלכודות פרוקסי הקשורות לפרוטוטייפ, קיים סט של המתודות הקשורות לכל אחת מהמלכודות.

המלכודת
`setPrototypeOf`
מקבלת את הארגומנטים הבאים:

1. `trapTarget` - האובייקט שעבורו יש להגדיר את הפרוטוטייפ
(מטרת הפרוקסי)
1. `proto` - האובייקט שיוגדר בתור פרוטוטייפ לאובייקט המטרה

אלו אותם ארגומנטים המועברים למתודות
<span dir="ltr">`Object.setPrototypeOf()`</span>
ו
<span dir="ltr">`Reflect.setPrototypeOf()`</span>.

המלכודת
`getPrototypeOf` ,
מקבלת רק את הארגומנט
`trapTarget`,
וזהו הארגומנט שמקבלות המתודות
<span dir="ltr">`Object.getPrototypeOf()`</span>
ו
<span dir="ltr">`Reflect.getPrototypeOf()`</span>.

### איך מלכודות פרוקסי לפרוטוטייפ פועלות

יש כמה מגבלות למלכודות אלה. ראשית, מלכודת
`getPrototypeOf`
חייבת להחזיר אובייקט או
`null`,
וכל ערך חזרה אחר מביא לשגיאה בזמן ריצה. בדיקת הערך החוזר מבטיחה ש
<span dir="ltr">`Object.getPrototypeOf()`</span>
תמיד תחזיר את הערך הרצוי. באופן דומה, ערך החזרה של המלכודת
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
יחזיר כל ערך אחר מאשר
`false`,
אז מניחים שפעולת
<span dir="ltr">`Object.setPrototypeOf()`</span>
הצליחה.

הדוגמה הבאה מסתירה את הפרוטוטייפ עבור ה-
proxy
על ידי החזרה של
`null`
ואינה מאפשרת לשנות את הפרוטוטייפ:

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
בעוד ש
<span dir="ltr">`Object.getPrototypeOf()`</span>
מחזיר ערך עבור האובייקט
`target`,
מוחזר
`null`
עבור
`proxy`
מפני שהמלכודת
`getPrototypeOf`
מופעלת. באופן דומה, המתודה
<span dir="ltr">`Object.setPrototypeOf()`</span>
פועלת בהצלחה כאשר משתמשים בה על
`target`
אבל זורקת שגיאה כאשר משתמשים בה על
`proxy`
בגלל המלכודת
`setPrototypeOf`.

אם נרצה להשתמש בהתנהגות הדיפולטיבית עבור 2 מלכודות אלו, ניתן להשתמש במתודות המתאימות באובייקט -
`Reflect`.
כך לדוגמה, קוד זה מיישם את התנהגות הרגילה עבור המלכודות-
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
ולקבל את אותה תוצאה מפני שהמלכודות
`getPrototypeOf`
ו
`setPrototypeOf`
רק מפעילות את ההתנהגות הדיפולטיבית.
חשוב לשים לב לכך שבדוגמה זו משתמשים במתודות
<span dir="ltr">`Reflect.getPrototypeOf()`</span>
ו
<span dir="ltr">`Reflect.setPrototypeOf()`</span>
ולא למתודות עם אותו שם שנמצאות על הקונסטרקטור
`Object`
עם כמה הבדלים חשובים.

### למה שתי סוגי מתודות?

ההיבט המבלבל של
<span dir="ltr">`Reflect.getPrototypeOf()`</span>
ו
<span dir="ltr">`Reflect.setPrototypeOf()`</span>
זה שהם נראים מאוד דומים למתודות
<span dir="ltr">`Object.getPrototypeOf()`</span>
ו
<span dir="ltr">`Object.setPrototypeOf()`</span>.
למרות שהמתודות עושות פעולות דומות , ישנם מספר הבדלים ברורים ביניהן.

ראשית,
<span dir="ltr">`Object.getPrototypeOf()`</span>
ו
<span dir="ltr">`Object.setPrototypeOf()`</span>
הן פעולות ברמה גבוהה יותר
<span dir="ltr">(higher-level operations)</span>

שבמקור נועדו לשימוש על ידי מפתחים עוד למן ההתחלה. המתודות
<span dir="ltr">`Reflect.getPrototypeOf()`</span>
ו
<span dir="ltr">`Reflect.setPrototypeOf()`</span>
הן פעולות ברמה נמוכה
<span dir="ltr">(lower-level operations)</span>
המעניקות למפתחים גישה לפעולות שהיו פעולות פנימיות בלבד של מנוע הריצה - הפעולות
`[[GetPrototypeOf]]`
ו
`[[SetPrototypeOf]]`.
המתודה
<span dir="ltr">`Reflect.getPrototypeOf()`</span>
היא מעטפת לפעולה
`[[GetPrototypeOf]]`
(עם אותה שיטת ולידציה של קלט).
למתודה
<span dir="ltr">`Reflect.setPrototypeOf()`</span>
ו
`[[SetPrototypeOf]]`
יש אותה מערכת יחסים. המתודות שקיימות על הקונסטרקטור
`Object`
גם מפעילות בסופו של דבר את הפעולות
`[[GetPrototypeOf]]`
ו
`[[SetPrototypeOf]]`
אך מבצעות מספר שלבים לפני כן ובודקות את הערך המוחזר כדי לקבוע את ההתנהגות הסופית.

המתודה
<span dir="ltr">`Reflect.getPrototypeOf()`</span>
זורקת שגיאה במידה והארגומנט איננו אובייקט, ואילו
<span dir="ltr">`Object.getPrototypeOf()`</span>
תבצע המרת הערך לאובייקט לפני ביצוע הפעולה. אם הייתם מעבירים ערך מספרי לכל אחת מהמתודות, הייתם מקבלים תוצאה שונה:

<div dir="ltr">

```js
let result1 = Object.getPrototypeOf(1);
console.log(result1 === Number.prototype);  // true

// שגיאה
Reflect.getPrototypeOf(1);
```

</div>

המתודה
<span dir="ltr">`Object.getPrototypeOf()`</span>
מאפשרת לקבל את הפרוטוטייפ של המספר
`1`
בגלל שקודם הוא ממיר את הערך לאובייקט מסוג
`Number`
ואז מחזיר
`Number.prototype`.
המתודה
<span dir="ltr">`Reflect.getPrototypeOf()`</span>
לא משנה את סוג הערך , ולכן כיוון שהערך
`1`
איננו מסוג אובייקט, נזרקת שגיאה.

עבור המתודה
<span dir="ltr">`Reflect.setPrototypeOf()`</span>
קיימים מספר הבדלים יחסית למתודה
<span dir="ltr">`Object.setPrototypeOf()`</span>.
ראשית,
<span dir="ltr">`Reflect.setPrototypeOf()`</span>
מחזירה ערך בוליאני המציין אם הפעולה הצליחה. הערך
`true`
יחזור עבור הצלחה, והערך
`false`
יחזור עבור כישלון. אם
<span dir="ltr">`Object.setPrototypeOf()`</span>
נכשל, תיזרק שגיאה.

בדוגמה הראשונה תחת סעיף "מלכודת פרוקסי לפרוטוטייפ" ראינו, שכאשר מלכודת הפרוקסי
`setPrototypeOf`
מחזירה
`false`,
זה גורם למתודה
<span dir="ltr">`Object.setPrototypeOf()`</span>
לזרוק שגיאה. המתודה
<span dir="ltr">`Object.setPrototypeOf()`</span>
מחזירה את הארגומנט הראשון כערך, מה שאינו תואם להתנהגות ברירת המחדל של מלכודת הפרוקסי
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
ההבדל הזה חשוב מאוד. בהמשך נראה מתודות כפולות לכאורה נוספות בין האובייקטים
`Object`
ו
`Reflect`,
אך יש להקפיד להשתמש במתודה של האובייקט
`Reflect`
בתוך מלכודות פרוקסי.

I> שתי הצורות של המתודות יפעילו אתמלכודת הפרוקסי
`getPrototypeOf`
ו
`setPrototypeOf`
כאשר משתמשים בהן על פרוקסי.

## מלכודות להרחבת אובייקט

ECMAScript 5
הרחיבה את היכולת לשינוי אובייקטים דרך המתודות
<span dir="ltr">`Object.preventExtensions()`</span>
ו
<span dir="ltr">`Object.isExtensible()`</span>,
-ו
ECMAScript 6
מאפשרת לפרוקסי ליירט את הקריאה לאותן מתודות באמצעות המלכודות
`preventExtensions`
ו
`isExtensible`.
שתי המלכודות מקבלות ארגומנט אחד שנקרא
`trapTarget`
זהו האובייקט עליו נקראה המתודה. המלכודת
`isExtensible`
חייבת להחזיר ערך בוליאני המציין האם האובייקט ניתן להרחבה ואילו המלכודת
`preventExtensions`
חייבת להחזיר ערך בוליאני המציין האם הפעולה הצליחה.

בנוסף, קיימות גם המתודות
<span dir="ltr">`Reflect.preventExtensions()`</span>
ו
<span dir="ltr">`Reflect.isExtensible()`</span>
בהן ניתן להשתמש כדי ליישם את ההתנהגות הדיפולטיבית. שתיהן מחזירות ערך בוליאני, כך שניתן להשתמש בהן ישירות במלכודות התואמות.

### שתי דוגמאות בסיסיות

ניתן לראות מלכודות הרחבת אובייקט בפעולה, בדוגמת הקוד הבאה, אשר מיישמת את התנהגות ברירת המחדל עבור המלכודות
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
אפשר לשנות את התנהגות זו. לדוגמה, במידה ולא נרצה לאפשר ל
<span dir="ltr">`Object.preventExtensions()`</span>
לעבוד בפרוקסי שלנו, ניתן להחזיר ערך
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

בדוגמה לעיל המתודה
<span dir="ltr">`Object.preventExtensions(proxy)`</span>
אינה פועלת מפני שהמלכודת
`preventExtensions`
מחזירה
`false`.
הפעולה אינה מועברת אל
`target`,
כך שהמתודה
<span dir="ltr">`Object.isExtensible()`</span>
מחזירה את הערך
`true`.

### שיטות הרחבה כפולות

יתכן ששמתם לב, לכך ששוב אנו נתקלים במה שנראה על פניו בתור כפילויות במתודות על אובייקט
`Object`
ועל אובייקט
`Reflect`.
במקרה הנוכחי קיים יותר דמיון מאשר קודם.
המתודות
<span dir="ltr">`Object.isExtensible()`</span>
ו
<span dir="ltr">`Reflect.isExtensible()`</span>
דומות מאוד למעט כאשר מועבר ערך שאינו אובייקט. במקרה הזה,
<span dir="ltr">`Object.isExtensible()`</span>
תמיד יחזיר
`false`
כאשר
<span dir="ltr">`Reflect.isExtensible()`</span>
יזרוק שגיאה. להלן דוגמה

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
מכיוון שלמתודה שפועלת ברמה העמוקה יותר
<span dir="ltr">(low level)</span>
יש בדיקות שגיאה מחמירות יותר מאשר  למקבילה ברמה הגבוהה יותר.

המתודות
<span dir="ltr">`Object.preventExtensions()`</span>
ו
<span dir="ltr">`Reflect.preventExtensions()`</span>
גם הן דומות מאוד. המתודה
<span dir="ltr">`Object.preventExtensions()`</span>
תמיד תחזיר את הערך שהועבר אליה כארגומנט גם אם אין הוא אובייקט. אך המתודה
<span dir="ltr">`Reflect.preventExtensions()`</span>
תזרוק שגיאה אם הארגומנט אינו אובייקט; אם הארגומנט הוא אובייקט, אזי
<span dir="ltr">`Reflect.preventExtensions()`</span>
תחזיר את הערך
`true`
כאשר הפעולה מצליחה ואת הערך
`false`
אם לא.
לדוגמה:

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

בדוגמה לעיל, המתודה
<span dir="ltr">`Object.preventExtensions()`</span>
מחזירה חזרה את הערך
`2`
למרות שמדובר בערך שאינו מסוג אובייקט.
המתודה
<span dir="ltr">`Reflect.preventExtensions()`</span>
מחזירה את הערך
`true`
כאשר היא מקבלת אובייקט כארגומנט וזורקת שגיאה כאשר הערך
`2`
מועבר אליה.

## מלכודת לתיאור מאפיינים

אחד השיפורים החשובים ביותר של
ECMAScript 5
היה היכולת להגדיר מאפייני תכונה
<span dir="ltr">(property attributes)</span>
באמצעות המתודה
<span dir="ltr">`Object.defineProperty()`</span>.
בגרסאות קודמות של ג'אווהסקריפט, לא היה  ניתן להגדיר תכונת גישה
/
גטר
<span dir="ltr">(accessor property - getter)</span>,
להגדיר תכונה לקריאה בלבד, או להגדיר אותה כתכונה לא אנומרבילית. כל הדברים הללו ניתנים לביצוע בעזרת המתודה
<span dir="ltr">`Object.defineProperty()`</span>,
וניתן לקרוא את אותם מאפיינים על ידי המתודה
<span dir="ltr">`Object.getOwnPropertyDescriptor()`</span>.

פרוקסי מאפשר לנו ליירט קריאות למתודה
<span dir="ltr">`Object.defineProperty()`</span>
ולמתודה
<span dir="ltr">`Object.getOwnPropertyDescriptor()`</span>
באמצעות המלכודות
`defineProperty`
ו
`getOwnPropertyDescriptor`,
בהתאמה. המלכודת
`defineProperty`
מקבלת את הארגומנטים הבאים:

1. `trapTarget` - האובייקט שעליו תוגדר התכונה
(המטרה של הפרוקסי)
1. `key` - מזהה התכונה
(מחרוזת או
סימבול)
1. `descriptor` - אובייקט מאפייני התכונה
(דיסקריפטור)

מלכודת על
`defineProperty`
להחזיר את הערך
`true`
אם הפעולה הצליחה ואת הערך
`false`
אם לא. מלכודת
`getOwnPropertyDescriptor`
מקבלת רק את הערכים
`trapTarget`
ו
`key`,
ותחזיר את מאפייני התכונה. המתודות התואמות
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


הקוד לעיל מגדיר תכונה בשם
`"name"`
על הפרוקסי באמצעות המתודה
<span dir="ltr">`Object.defineProperty()`</span>.
לאחר מכן מוחזר אובייקט המאפיינים על ידי המתודה
<span dir="ltr">`Object.getOwnPropertyDescriptor()`</span>.

### חסימת פעולת Object.defineProperty

מלכודת
`defineProperty`
דורשת להחזיר ערך בוליאני שמציין האם הפעולה הצליחה. כאשר מוחזר הערך
`true` ,
המשמעות היא שהמתודה
<span dir="ltr">`Object.defineProperty()`</span>
פעלה בהצלחה; כאשר מוחזר הערך
`false`,
אזי המתודה
<span dir="ltr">`Object.defineProperty()`</span>
תזרוק שגיאה. ניתן להשתמש בפונקציונליות זו כדי להגביל את סוגי התכונות שהמתודה-
<span dir="ltr">`Object.defineProperty()`</span>
יכולה להגדיר. לדוגמה, כדי למנוע הגדרת תכונות עם מזהה מסוג סימבול,
ניתן לבדוק שהמזהה הוא מסוג מחרוזת ולהחזיר
`false`
אם לא.
כמו בדוגמה הבאה:

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
הוא מסוג סימבול
אחרת היא ממשיכה עם ההתנהגות הדיפולטיבית. כאשר המתודה
<span dir="ltr">`Object.defineProperty()`</span>
נקראת עם הערך
`"name"`
בתור מזהה, הפעולה תצליח מפני שהמפתח הוא מסוג מחרוזת. כאשר המתודה
<span dir="ltr">`Object.defineProperty()`</span>
מופעלת עם הסימבול
`nameSymbol`
בתור מזהה התכונה,
תיזרק שגיאה מפני שהמלכודת
`defineProperty`
מחזירה
`false`.

I> ניתן לגרום למתודה
<span dir="ltr">`Object.defineProperty()`</span>
שתיכשל בשקט על ידי החזרת הערך
`true`
ולא לקרוא למתודה
<span dir="ltr">`Reflect.defineProperty()`</span>.
הדבר ימנע את זריקת השגיאה ללא הגדרת התכונה בפועל.

### הגבלת מתאר אובייקט

על מנת להבטיח התנהגות עקבית בעת שימוש במתודות
<span dir="ltr">`Object.defineProperty()`</span>
ו
<span dir="ltr">`Object.getOwnPropertyDescriptor()`</span>,
אובייקט מסוג דיסקריפטור שמועבר למלכודת
`defineProperty`
עובר תהליך נורמליזציה. אובייקטים שמוחזרים מהמלכודת
`getOwnPropertyDescriptor`
תמיד יעברו ולידציה מאותה הסיבה.

לא משנה איזה אובייקט מועבר בתור הארגומנט השלישי למתודה
<span dir="ltr">`Object.defineProperty()`</span>,
רק התכונות
`enumerable`,
`configurable`, `value`, `writable`, `get`,
ו
`set`
יהיו על אובייקט דיסקריפטור המועבר למלכודת
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


בדוגמה לעיל המתודה,
<span dir="ltr">`Object.defineProperty()`</span>
נקראת עם תכונה לא תקנית בשם
`name`
על הארגומנט השלישי למתודה. כאשר מלכודת
`defineProperty`
נקראת, למשתנה
`descriptor`
אין תכונה בשם
`name`
אבל יש לו תכונה בשם
`value`.
הסיבה לכך היא שהמשתנה
`descriptor`
לא מצביע בפועל על הארגומנט השלישי שהועבר למתודה
<span dir="ltr">`Object.defineProperty()`</span>,
אלא מצביע על אובייקט חדש המכיל רק את התכונות המותרות. המתודה
<span dir="ltr">`Reflect.defineProperty()`</span>
גם כן מתעלמת מתכונות לא תקניות על הדיסקריפטור.

המלכודת
`getOwnPropertyDescriptor`
פועלת תחת אילוצים קצת שונים שדורשים החזרת ערך של
`null`, `undefined`,
או אובייקט. אם מוחזר אובייקט, רק התכונות
`enumerable`, `configurable`, `value`, `writable`, `get`,
ו
`set`
מותרות לשימוש כתכונות של האובייקט. תיזרק שגיאה נזרקת אם יוחזר אובייקט עם תכונה עצמית
<span dir="ltr">(own property)</span>
לא תקנית, כמו שרואים בדוגמת הקוד הבאה:

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

התכונה בשם
`name`
אינה תכונה תקנית של אובייקט מסוג דיסקריפטור, לכן כאשר המתודה
<span dir="ltr">`Object.getOwnPropertyDescriptor()`</span>
מופעלת, הערך שמוחזרת מן המתודה
`getOwnPropertyDescriptor`
זורק שגיאה. התנהגות זו מבטיחה שהערך שיוחזר על ידי
<span dir="ltr">`Object.getOwnPropertyDescriptor()`</span>
יהיה בעל צורה מוסכמת ללא קשר לשימוש בפרוקסי.

### מתודות דיסקריפטור משוכפלות

שוב, אנו רואים שלגרסת
ECMAScript 6
יש כמה מתודות דומות באופן מבלבל, כמו המתודה
<span dir="ltr">`Object.defineProperty()`</span>
והמתודה
<span dir="ltr">`Object.getOwnPropertyDescriptor()`</span>
נראות כאילו הן עושות את אותה פעולה כמו המתודות
<span dir="ltr">`Reflect.defineProperty()`</span>
ו
<span dir="ltr">`Reflect.getOwnPropertyDescriptor()`</span>,
בהתאמה. כמו צמדי מתודות אחרים שנדונו קודם לכן בפרק זה, יש להם כמה הבדלים חשובים.

#### המתודה defineProperty

המתודות
<span dir="ltr">`Object.defineProperty()`</span>
ו
<span dir="ltr">`Reflect.defineProperty()`</span>
פועלות באופן למעט הערך שחוזר מהם. המתודה
<span dir="ltr">`Object.defineProperty()`</span>
מחזירה את הארגומנט הראשון, בזמן ש
<span dir="ltr">`Reflect.defineProperty()`</span>
תחזיר
`true`
אם הפעולה הצליחה ואת הערך
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

כאשר המתודה
<span dir="ltr">`Object.defineProperty()`</span>
נקראת על
`target`,
הערך המוחזר מהפונקציה הוא
`target`.
כאשר המתודה
<span dir="ltr">`Reflect.defineProperty()`</span>
נקרא על
`target`,
הערך המוחזר הוא
`true`,
שמעיד שהפעולה הצליחה. מאחר ומלכודת הפרוקסי
`defineProperty`
דורשת שערך בוליאני יחזור, עדיף להשתמש במתודה
<span dir="ltr">`Reflect.defineProperty()`</span>
על מנת ליישם את התנהגות ברירת המחדל במידת הצורך.

#### המתודה getOwnPropertyDescriptor

המתודה
<span dir="ltr">`Object.getOwnPropertyDescriptor()`</span>
הופכת את
הארגומנט
הראשון שלה לאובייקט כאשר מועבר ערך פרימיטיבי ואז ממשיכה בפעולתה. ואילו המתודה
<span dir="ltr">`Reflect.getOwnPropertyDescriptor()`</span>
תזרוק שגיאה אם הארגומנט הראשון יהיה מסוג ערך פרימיטיבי. להלן דוגמה המציגה את שניהם:

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
מאחר והיא הופכת את הערך המספרי
`2`
לאובייקט. אך אחד ללא תכונה בשם
`name`
זוהי ההתנהגות הסטנדרטית של המתודה כאשר תכונה בעלת השם הנתון אינו אינה קיימת באובייקט. אך כאשר
<span dir="ltr">`Object.getOwnPropertyDescriptor()`</span>
נקראת, נזרקת שגיאה מכיוון שמתודה זו אינה מקבלת ערכים פרימיטיביים עבור הארגומנט הראשון.

## מלכודת `ownKeys`

מלכודת הפרוקסי
`ownKeys`
מיירטת את המתודה הפנימית
`[[OwnPropertyKeys]]`
ומאפשרת לך לשנות את התתנהגות שלה ע"י החזרת מערך של ערכים. במערך הזה נעשה שימוש בארבע מתודות שונות: המתודה
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
ומחזירה מערך של כל מזהי התכונות העצמות של האובייקט, מסוג סטרינג וסימבול. המתודה
<span dir="ltr">`Object.getOwnPropertyNames()`</span>
והמתודה
<span dir="ltr">`Object.keys()`</span>
מסננות החוצה מזהים מסוג סימבול מן המערך ומחזירה את התוצאה בזמן שהמתודה
<span dir="ltr">`Object.getOwnPropertySymbols()`</span>
מסננת החוצה תכונות מסוג סטרינג ומחזירה את התוצאה. המתודה
<span dir="ltr">`Object.assign()`</span>
משתמשת בערכים מסוג סטרינג וגם מסוג סימבול.

המלכודת
`ownKeys`
מקבלת ארגומנט אחד, אובייקט היעד (הטרגט), וחייבת להחזיר תמיד מערך או אובייקט דמוי-מערך
<span dir="ltr">(array-like object)</span>
אחרת, תיזרק שגיאה.
ניתן להשתמש במלכודת
`ownKeys`
על מנת לסנן החוצה את כל המזהים שלא נרצה שייעשה בהם שימוש בזמן הקריאה  למתודה
<span dir="ltr">`Object.keys()`</span>,
למתודה
<span dir="ltr">`Object.getOwnPropertyNames()`</span>,
למתודה
<span dir="ltr">`Object.getOwnPropertySymbols()`</span>,
או למתודה
<span dir="ltr">`Object.assign()`</span>.
אילו לא היינו רוצים לכלול את התכונות ששמן מתחיל עם קו-תחתון, סימון נפוץ ב-
JavaScript
שנועד לסמן ששדה מסויים הוא פרטי, אזי היה אפשר להשתמש במלכודת
`ownKeys`
לסנן החוצה את המפתחות האלו כמו בדוגמה הבאה:

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

הדוגמה לעיל משתמשת במלכודת
`ownKeys`
שקוראת למתודה
<span dir="ltr">`Reflect.ownKeys()`</span>
כדי לקבל את כל המזהים של אובייקט המטרה. ולאחר מכן קוראת למתודה
<span dir="ltr">`filter()`</span>
כדי לסנן החוצה את המזהים שמתחילים עם סימון של קו-תחתון. לאחר מכן מוסיפים שלוש תכונות למשתנה מסוג אובייקט -
`proxy`:
`name`,
`_name`,
ו
`nameSymbol`.
כאשר המתודות
<span dir="ltr">`Object.getOwnPropertyNames()`</span>
ו
<span dir="ltr">`Object.keys()`</span>
מופעלות על
`proxy`,
רק התכונה
`name`
מוחזרת. באופן דומה הערך
`nameSymbol`
מוחזר כאשר המתודה
<span dir="ltr">`Object.getOwnPropertySymbols()`</span>
נקרא על
`proxy`.
התכונה
`_name`
אינה מופיעה באף תוצאה מאחר והיא סוננה החוצה.

I> המלכודת
`ownKeys`
משפיעה גם על הלולאה
`for-in`,
שקוראת למלכודת כדי לקבוע באילו מזהים להשתמש בתוך הלולאה.

## פונקציות פרוקסי עם מלכודות `apply` ו `construct`

מבין כל מלכודות הפרוקסי, רק
`apply`
ו
`construct`
דורשות שהטרגט של הפרוקסי יהיה פונקציה. נאמר בפרק 3 שלפונקציות שתי מתודות פנימיות בשם
`[[Call]]`
ו
`[[Construct]]`
שמופעלות כאשר פונקציה נקראת ללא האופרטור
`new`,
או עם האופרטור,
בהתאמה. המלכודות
`apply`
ו
`construct`
מאפשרות לנו לעקוף את אותן המתודות הפנימיות.

כאשר פונקציה מופעלת ללא האופרטור
`new`,
המלכודת
`apply`
והמתודה
<span dir="ltr">`Reflect.apply()`</span>
שתיהן מקבלות את הארגומנטים הבאים:

1. `trapTarget` - הפונקציה שמופעלת
(היעד - הטרגט של הפרוקסי)
1. `thisArg` - הערך של
`this`
בתוך הפונקציה בזמן ריצה.
1. `argumentsList` - מערך של ארגומנטים שהועברו לפונקציה

מלכודת
`construct`,
נקראת כאשר הפונקציה מופעלת באמצעות
`new`,
מקבלת את הארגומנטים הבאים:

1. `trapTarget` - הפונקציה שמופעלת
(היעד - הטרגט של הפרוקסי)
1. `argumentsList` - מערך של ארגומנטים שהועברו לפונקציה

המתודה
<span dir="ltr">`Reflect.construct()`</span>
מקבלת גם היא את שני הארגומנטים הללו ובנוסף מקבלת ארגומנט שלישי אופציונלי שנקרא
`newTarget`.
כאשר הוא מועבר, הארגומנט
`newTarget`
מציין את הערך של
<span dir="ltr">`new.target`</span>
בתוך הפונקציה.

יחד, מלכודות
`apply`
ו
`construct`
שולטות באופן מוחלט בהתנהגות של כל אובייקט יעד של פרוקסי סמוג פונקציה. בשביל לחקות את התנהגות ברירת המחדל של פונקציה, ניתן  לעשות זאת כך:

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


בדוגמה זו מפעילים פונקציה שמחזירה כתוצאה את המספר 42.
הפרוקסי
עבור אותה פונקציה משתמש במלכודות
`apply`
ו
`construct`
כדי להעביר פעולות אלה למתודות
<span dir="ltr">`Reflect.apply()`</span>
ו
<span dir="ltr">`Reflect.construct()`</span>,
בהתאמה. התוצאה הסופית היא שהפרוקסי לפונקציה פועל
בדיוק כמו פונקציית היעד, כולל קבלת סוג ערך כפונקציה כאשר משתמשים באופרטור
`typeof`.
כאשר הפרוקסי נקרא בלי האופרטור
`new`
הוא יחזיר כתוצאה את הערך
42
וכאשר הפרוקיס נקרא עם
`new`
הוא יוצר אובייקט בשם
`instance`.
האובייקט
`instance`
נחשב למופע של שניהם- של
`proxy`
וגם של
`target`
מכיוון שהאופרטור
`instanceof`
מחפש בשרשרת הפרוטוטייפ
<span dir="ltr">(prototype chain)</span>
כדי לקבוע את התוצאה. בדיקת שרשרת הפרוטוטייפ אינה מושפעת משימוש בפרוקסי, וזו הסיבה ש-
`proxy`
ו
`target`
נראים בעלי פרוטוטייפ זהה עבור מנוע הריצה של-
JavaScript.

### אימות פרמטרים של פונקציה

המלכודות
`apply`
ו
`construct`
פותחות בפנינו אפשרויות רבות לשינוי מנגנון ריצת הפונקציה. לדוגמה, נניח שנרצה לאמת שכל הארגומנטים שייכים לסוג מסוים. ניתן  לבדוק את הארגומנטים במלכודת
`apply`:

<div dir="ltr">

```js

// מחזירה סכום כל הארגומנטים
function sum(...values) {
    return values.reduce((previous, current) => previous + current, 0);
}

let sumProxy = new Proxy(sum, {
        apply: function(trapTarget, thisArg, argumentList) {

            argumentList.forEach((arg) => {
                if (typeof arg !== "number") {
                    throw new TypeError("כל הארגומנטים חייבים להיות מסוג מספר");
                }
            });

            return Reflect.apply(trapTarget, thisArg, argumentList);
        },
        construct: function(trapTarget, argumentList) {
            throw new TypeError("אסור לקרוא לפונקציה עם אופרטור " + "new");
        }
    });

console.log(sumProxy(1, 2, 3, 4));          // 10

// יזרוק שגיאה
console.log(sumProxy(1, "2", 3, 4));

// גם יזרוק שגיאה
let result = new sumProxy();
```

</div>

בדוגמה זו משתמשים במלכודת
`apply`
על מנת להבטיח שכל הארגומנטים הם מספרים. הפונקציה
<span dir="ltr">`sum()`</span>
מחברת את סכום כל הפרמטרים המועברים. אם מועבר ערך שאיננו מספר, הפונקציה עדיין תנסה לבצע את הפעולה, מה שעלול לגרום לתוצאות בלתי צפויות. ע"י זה שאנחנו עוטפים את
<span dir="ltr">`sum()`</span>
בתוך הפרוקסי
<span dir="ltr">`sumProxy()`</span>,
אנו מיירטים קריאות לפונקציה ומבטיחים שכל ארגומנט הוא מספר לפני שמאפשרים לפונקציה להמשיך בפעולתה. על מנת לוודא קריאה תקינה לפונקציה, משתמשים גם במלכודת
`construct`
כדי להבטיח שלא ניתן לקרוא לפונקציה עם
`new`.

ניתן גם לעשות את ההפך, להבטיח שחייבים לקרוא לפונקציה עם
`new`
ולאמת את הארגומנטים שלה שיהיו מספרים :

<div dir="ltr">

```js
function Numbers(...values) {
    this.values = values;
}

let NumbersProxy = new Proxy(Numbers, {

        apply: function(trapTarget, thisArg, argumentList) {
            throw new TypeError("חובה לקרוא לפונקציה עם אופרטור " + "new");
        },

        construct: function(trapTarget, argumentList) {
            argumentList.forEach((arg) => {
                if (typeof arg !== "number") {
                    throw new TypeError("כל הארגומנטים חייבים להיות מסוג מספר");
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

בדוגמה זו, מלכודת
`apply`
תזרוק שגיאה בזמן שמלכודת
`construct`
משתמשת במתודה
<span dir="ltr">`Reflect.construct()`</span>
כדי לאמת קלט ולהחזיר מופע חדש. ניתן להגיע לאותה תוצאה מבלי להשתמש בפרוקסי ע"י שימוש ב
<span dir="ltr">`new.target`</span>.

### לקרוא לקונסטרקטור בלי new

בפרק 3 הצגנו את המטא-תכונה
(metaproperty)
<span dir="ltr">`new.target`</span>.
כזכור לכם,
<span dir="ltr">`new.target`</span>
מצביעה על פונקציה שהופעלה בעזרת האופרטור
`new`,
כך ניתן לדעת אם הפונקציה נקראה באמצעות האופרטור
`new`
או לא על ידי בדיקת הערך של
<span dir="ltr">`new.target`</span>,
לדוגמה:

<div dir="ltr">

```js
function Numbers(...values) {

    if (typeof new.target === "undefined") {
        throw new TypeError("חובה לקרוא לפונקציה עם אופרטור " + "new");
    }

    this.values = values;
}

let instance = new Numbers(1, 2, 3, 4);
console.log(instance.values);               // [1,2,3,4]

// יזרוק שגיאה
Numbers(1, 2, 3, 4);
```

</div>

דוגמה זו נזרקת שגיאה כאשר הפונקציה
`Numbers`
נקראת ללא אופרטור
`new`,
דבר שדוגמה לדוגמא שניתנה עבור סעיף
"אימות פרמטרים של פונקציה"
בלי שימוש בפרוקסי.
כתיבת קוד כזה היא הרבה יותר פשוטה מאשר שימוש בפרוקסי ועדיפה אם המטרה היחידה שלך היא למנוע קריאה לפונקציה בלי
`new`.
אך לפעמים אין לנו שליטה בפונקציה אשר יש לשנות את התנהגותה. במקרה כזה, השימוש בפרוקסי יהיה הגיוני.

נניח שהפונקציה
`Numbers`
מוגדרת בקוד שלא ניתן לשנות. ידוע שהקוד מסתמך על
<span dir="ltr">`new.target`</span>
וברצוננו להימנע מאותה בדיקה בזמן הקריאה לפונקציה. ההתנהגות בעת השימוש
`new`
נקבעה כבר, כך שתוכלו פשוט להשתמש במלכודת
`apply` :

<div dir="ltr">

```js
function Numbers(...values) {

    if (typeof new.target === "undefined") {
        throw new TypeError("חובה לקרוא לפונקציה עם אופרטור " + "new");
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
מאפשרת לנו לקרוא לפונקציה
`Numbers`
מבלי להשתמש ב
`new`
ולדאוג שתתנהג כאילו השתמשנו ב
`new`.
לשם כך, המלכודת
`apply`
קוראת ל
<span dir="ltr">`Reflect.construct()`</span>
עם הארגומנטים המועברים ל
`apply`.
המטא תכונה
<span dir="ltr">`new.target`</span>
בתוך
`Numbers`
מקבלת את הערך
`Numbers`
,ולכן לא תיזרק שגיאה. זוהי דוגמה פשוטה לשינוי
<span dir="ltr">`new.target`</span>,
וניתן גם לעשות זאת צורה ישירה יותר.

### עקיפת קונסטרקטור מחלקה אבסטרקטית

ניתן ללכת צעד קדימה ולציין את הארגומנט השלישי עבור
<span dir="ltr">`Reflect.construct()`</span>
כערך ספציפי שיש להקצות עבור
<span dir="ltr">`new.target`</span>.
זה יכול לעזור לנו כאשר הפונקציה בודקת את
<span dir="ltr">`new.target`</span>
מול ערך ידוע, כמו בעת יצירת קוסנרקטור לקלאס בסיס אבסטרקטי
(נדון בפרק 9).
בקונסטרקטור בקלאס אבסטרקטי (מופשט) נצפה שהערך של
<span dir="ltr">`new.target`</span>
יהיה שונה מן הקוסנטרקטור עצמו , כמו בדוגמה הבאה:

<div dir="ltr">

```js
class AbstractNumbers {

    constructor(...values) {
        if (new.target === AbstractNumbers) {
            throw new TypeError("חובה לבצע ירושה של המחלקה");
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

כאשר קוראים לפונקציה
<span dir="ltr">`new AbstractNumbers()`</span>,
<span dir="ltr">`new.target`</span>
שווה ל
`AbstractNumbers`
ושגיאה נזרקת. קריאה ל
<span dir="ltr">`new Numbers()`</span>
תעבוד ללא שגיאה כי
<span dir="ltr">`new.target`</span>
שווה ל
`Numbers`.
ניתן לעקוף מגבלה זו על ידי הגדרה של
<span dir="ltr">`new.target`</span>
עם פרוקסי:

<div dir="ltr">

```js
class AbstractNumbers {

    constructor(...values) {
        if (new.target === AbstractNumbers) {
            throw new TypeError("חובה לבצע ירושה של המחלקה");
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

הפרוקסי
`AbstractNumbersProxy`
משתמש במלכודת
`construct`
כדי ליירט את הקריאה
<span dir="ltr">`new AbstractNumbersProxy()`</span>.
לאחר מכן מופעלת המתודה
<span dir="ltr">`Reflect.construct()`</span>
עם ארגומנטים מן המלכודת ומוסיפה כארגומנט שלישי פונקציה ריקה. הפונקציה הריקה משמשת בתור ערך ל
<span dir="ltr">`new.target`</span>
במקום הקונסטרקטור. מפני ש
<span dir="ltr">`new.target`</span>
אינו שווה ל
`AbstractNumbers`,
לא נזרקת שגיאה והקונסטרקטור רץ בצורה תקינה.

### קונסטרקטור של קלאס שניתן להריץ כפונקציה רגילה

בפרק 9 הוסבר שקונסטרקטור של קלאס חייב להיקרא עם
`new`.
זה קורה מפני שהמתודה הפנימית
`[[Call]]`
של קונסטרקטור לקלאס מוגדרת לזרוק שגיאה. אבל פרוקסי יכול ליירט קריאות למתודה
`[[Call]]`,
כך שניתן ליצור קונסטרקטור של קלאס שניתן לקריאה כפונקציה רגילה על ידי שימוש בפרוקסי.
לדוגמה, אם נרצה קונסטרקטור של קלאס אשר יעבוד בלי שימוש ב
`new`,
אפשר להשתמש במלכודת
`apply`
על מנת ליצור מופע חדש. להלן דוגמה:

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


let me = PersonProxy("ניקולאס");
console.log(me.name);                   // "ניקולאס"
console.log(me instanceof Person);      // true
console.log(me instanceof PersonProxy); // true
```

</div>

האובייקט
`PersonProxy`
הוא פרוקסי של קונסטרקטור של קלאס
`Person`.
קונסטרקטור הינו סוג של פונקציה, ולכן הם מתנהגים כמו פונקציה כאשר משתמשים עליהם בפרוקסי. מלכודת
`apply`
דורסת את ההתנהגות הדיפולטיבית ובבמקום זאת מחזירה מופע חדש של
`trapTarget`
שבמקרה זה מצביע על קלאס
`Person`.
(
בדוגמה זאת נעשה שימוש ישירות ב
`trapTarget`
כדי להראות שאין צורך להתייחס באופן ישיר לקלאס בשם.
)
הארגומנט
`argumentList`
מועבר ל
`trapTarget`
על ידי
אופרטור הפיזור
<span dir="ltr">(spread operator)</span>
כדי להעביר בנפרד כל ארגומנט. בצורה זו קריאה ל
<span dir="ltr">`PersonProxy()`</span>
בלי להשתמש
`new`
תחזיר מופע של
`Person`;
אם ננסה לקרוא ל
<span dir="ltr">`Person()`</span>
בלי
`new`,
הקונסטרקטור עדיין יזרוק שגיאה. יצירת קונסטקרטור לקלאס שעובד ללא שימוש באופרטור
`new`
אפשרי רק ע"י שימוש בפרוקסי.

## פרוקסי שניתן לביטול

בדרך כלל לא ניתן לנתק פרוקסי מאובייקט המטרה שלו לאחר שכבר נוצר. בכל הדוגמאות בפרק זה השתמשנו בפרוקסי בלתי ניתנים לביטול. אך ייתכנו מצבים שבהם נרצה לבטל פרוקסי כך שלא ניתן יהיה להשתמש בו עוד. זהו דבר שיכול להועיל
כאשר נרצה לספק אובייקט למען גישה לקוד באמצעות
API
ולשמור על היכולת לנתק את הגישה לפונקציונליות כלשהי בכל נקודת זמן.

ניתן ליצור פרוקסי הניתן לביטול ע"י המתודה
<span dir="ltr">`Proxy.revocable()`</span>
שמקבלת את אותם ארגומנטים כמו הקונסטרקטור של
`Proxy` -
אובייקט מטרה וההנדלר
(handler)
של הפרוקסי.
ערך ההחזרה הוא אובייקט עם המאפיינים הבאים:

1. `proxy` - אובייקט פרוקסי שניתן לביטול
1. `revoke` - הפונקציה שיש להפעיל כדי לבטל את הפרוקסי

כאשר הפונקציה
<span dir="ltr">`revoke()`</span>
מופעלת, לא ניתן לבצע פעולות נוספות דרך
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

בדוגמה זו יצרנו פרוקסי הניתן לביטול. השתמשנו בפעולת פירוק משתנים כדי לבצע
השמה של המשתנים
`proxy`
ו
`revoke`
לתכונות עם אותו שם שהמתודה
<span dir="ltr">`Proxy.revocable()`</span>.
החזירה. לאחר מכן, ניתן להשתמש  באובייקט
`proxy`
כמו פרוקסי רגיל, כך ש
`proxy.name`
יחזיר את הערך
`"target"`.
אך כאשר הפונקציה
<span dir="ltr">`revoke()`</span>
נקראת,
`proxy`
כבר לא מתפקד. ניסיון לגשת לתכונה
`proxy.name`
יזרוק שגיאה, כמו כל פעולה אחרת שתפעיל מלכודת
`proxy`.

## פתרון לבעיית המערך

בתחילת פרק זה הסברתי כיצד מפתחים לא היו יכולים לחקות במדויק את ההתנהגות של אובייקט מסוג מערך בג׳אווהסקריפט
לפני
ECMAScript 6.
פרוקסי וריפלקשן
מאפשרים לנו ליצור אובייקט שמתנהג כמו הסוג המובנה
`Array`
כאשר מוסיפים או מחסירים ערכים. הנה מספר דוגמאות
שפרוקסי עוזרים לנו לחקות:

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

בדוגמה זו ישנן שתי התנהגויות שחשוב לשים לב אליהן:

1. התכונה
`length`
תוגדל ל-
4
כאשר תתבצעה השמת ערך עבור
`colors[3]`.
1. שני הערכים האחרונים של המערך יימחקו כאשר התכונה
`length`
מוגדרת לערך
2.

שתי התנהגויות הללו הינן כל מה שצריך לחקות כדי לשחזר במדויק את האופן שבו מערכים מובנים עובדים. בהמשך נסביר כיצד ליצור אובייקט שמחקה אותן.

### איתור אינדקס של מערך

חשוב לזכור שהשמת ערך מספרי שלם
(integer)
בתור מזהה תכונה הינו מקרה מיוחד כאשר מדובר במערכים, ויש לכך התייחסות שונה מאשר למזהים שאינם מספרים.
ECMAScript 6
מגדירה הוראות אלה כיצד לקבוע אם מזהה תכונה הינו אינדקס במערך:

<span dir="ltr">

> A String property name `P` is an array index if and only if `ToString(ToUint32(P))` is equal to `P` and `ToUint32(P)` is not equal to 2^32^-1.

</span>

ניתן ליישם פעולה זו בג׳אווהסקריפט באופן הבא:

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
ממירה ערך ערך נתון למספר שלם ללא סימון של 32 סיביות
<span dir="ltr">(unsigned 32-bit integer)</span>
באמצעות אלגוריתם המתואר באפיון השפה. הפונקציה
<span dir="ltr">`isArrayIndex()`</span>
ראשית ממירה את מזהה התכונה לערך מספרי מסוג
uint32
ואז מבצעת בדיקה כדי לקבוע אם המזהה הוא אינדקס מערך. בעזרת פונקציות  אלה ניתן ליישם אובייקט שיחקה מערך מובנה.

### הגדלת האורך בעת הוספת אלמנטים חדשים

שתי התנהגויות המערך שתיארתי מקודם מסתמכות על השמת ערך. ניתן להשתמש רק במלכודת פרוקסי מסוג
`set`
של כדי לייצר את התנהגות הזו. הנה דוגמה המיישמת את ההתנהגות הראשונה מבין השתיים על ידי הגדלת המאפיין
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

            // מתבצע תמיד ללא קשר לסוג המזהה
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

דוגמה זו משתמשת במלכודת פרוקסי
`set`
כדי ליירט את פעולת הגדרת אינדקס של מערך. אם שם התכונה הוא אינדקס מערך, הוא יומר למספר מכיוון שמזהים מועברים תמיד כמחרוזות. בשלב הבא, אם הערך המספרי גדול או שווה לתכונה
`length`
הנוכחית, אז התכונה
`length`
תתעדכן עם ערך גבוה יותר ב-1 מהמזהה המספרי
(
הגדרה של אלמנט במיקום 3 פירושה ש
`length`
חייב להיות 4
).
לאחר מכן משתמשים בהתנהגות ברירת המחדל להגדרת תכונה באמצעות
<span dir="ltr">`Reflect.set()`</span>.

המערך המותאם מאותחל על ידי קריאה ל
<span dir="ltr">`createMyArray()`</span>
עם
`length`
של
3
והערכים לשלושת הפריטים מתווספים מיד לאחר מכן. התכונה
`length`
נשארת עם הערך
3
עד שהערך
`"black"`
מוקצה למיקום
3.
ואז התכונה,
`length`
מוגדרת לערך
4.

כעת כשהתנהגות הראשונה כבר ממומשת, ניתן לעבור למימוש להתנהגות שנייה.

### מחיקת אלמנטים כאשר מצמצמים את התכונה - length

התנהגות המערך הראשונה לחיקוי מתקיימת רק כאשר  האינדקס יהיה שווה או גדול מהערך של התכונה
`length`.
ההתנהגות השנייה עושה את ההפך ומסירה אלמנטים ממערך כאשר תכונה
`length`
מוגדרת לערך קטן יותר מכפי שהיתה קודם. זה כרוך לא רק בשינוי התכונה
`length` ,
אלא גם במחיקת כל האלמנטים שעלולים להתקיים. לדוגמה אם מערך עם תכונה
`length`
של 4
והתכונה
`length`
מוגדרת ל 2, האלמנטים במיקומים  2 ו 3 יימחקו. ניתן לבצע אותה פעולה בעזרת מלכודת הפרוקסי
`set`
לצד מימוש ההתנהגות הראשונה. הנה הדוגמה הקודמת שוב, עם עידכון למתודה
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

            // מתבצע תמיד ללא קשר לסוג המזהה
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
בדוגמת הקוד בודקת האם הערך עבור
`key`
הוא
`"length"`
על מנת לבצע את הפעולה המתאימה על האובייקט. כאשר התנאי מתקיים, האורך הנוכחי נקרא תחילה
באמצעות
<span dir="ltr">`Reflect.get()`</span>
ומושווה לערך החדש. אם הערך החדש קטן מהאורך הנוכחי, אזי לולאת
`for`
עוברת על כל התכונות באובייקט המטרה שאמורות. לולאת
`for`
הולכת אחורה החל מאורך המערך הנוכחי
(`currentLength`)
ומוחקת כל תכונה עד שהיא מגיעה לאורך המערך החדש
(`value`).

דוגמה זו מוסיפה ארבעה צבעים ל
`colors`
ואז מגדירה את התכונה
`length`
ל 2. הפעולה מוחקת את האלמנטים במיקומים 2 ו -3, כך שהם חוזרים כעת את הערך
`undefined`
כאשר מנסים לגשת אליהם. התכונה
`length`
מוגדרת עם הערך 2 והאלמנטים במיקומים 0 ו- 1 עדיין נגישים.

כאשר שתי ההתנהגויות מיושמות, ניתן ליצור בקלות אובייקט המחקה את ההתנהגות של מערכים מובנים. אולם פעולה כזו עם פונקציה אינה רצויה כמו יצירת מחלקה שמממשת התנהגות זו, אז השלב הבא הוא ליישם פונקציונליות זו כמחלקה/קלאס.

### מימוש מחלקת MyArray

הדרך הפשוטה ביותר ליצור מחלקה המשתמשת בפרוקסי היא להגדיר את המחלקה כרגיל ואז להחזיר פרוקסי מהקונסטרקטור.
באופן זה, האובייקט שמקבלים כאשר מופעלת המחלקה יהיה ההפרוקסי
במקום המופע.
(
המופע
/
<span dir="ltr">(instance)</span>
/
אינסטנס,
הוא הערך של
`this`
בתוך הקונסטרקטור.
)
האינסטנס הופך לאובייקט המטרה של הפרוקסי והפרוקסי הוא זה שמוחזר כאילו היה האינסטנס. האינסטנס יהיה פרטי לחלוטין ולא ניתן לגשת אליו ישירות, אם כי ניתן לגשת אליו בעקיפין באמצעות ההפרוקסי.

להלן דוגמה פשוטה להחזרת פרוקסי מקונסטרקטור של מחלקה:

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
מחזירה פרוקסי מתוך הקונסטרקטור. אובייקט המטרה של הפרוקסי הוא
`this`
והפרוקסי מוחזר מהקונסטרקטור. המשמעות הינה ש
`myThing`
הוא למעשה פרוקסי למרות שנוצר על ידי הקריאה לקונסטרקטור של
`Thing`.
מפני שפרוקסי מעבירים את הפעולות שנעשות עליהם אל הטרגט שלהם,
`myThing`
נחשב עדיין למופע של
`Thing`,
מה שהופך את הפרוקסי
לשקוף לחלוטין לכל מי שמשתמש במחלקה
`Thing`.

יצירת מערך בהתאמה אישית באמצעות פרוקסי הוא יחסית פשוט. הקוד דומה  לקוד בסעיף
מחיקת אלמנטים כאשר מצמצמים את התכונה - length
.משתמשים באותו קוד, אך הפעם הפרוקסי קיים בתוך קונסטרקטור של מחלקה. להלן הדוגמה המלאה:

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

                // מתבצע תמיד ללא קשר לסוג המזהה
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

הקוד לעיל יוצר מחלקה
`MyArray`
שמחזירה פרוקסי מתוך הקונסטרקטור שלה. את התכונה
`length`
מוסיפים בתוך הקונסטרקטור
(התכונה מאותחלת לערך שמועבר לקונסטרקטור או לערך ברירת מחדל של 0)
והפרוקסי נוצר ומוחזר. זה גורם למשתנה
`colors`
להיראות כמופע של
`MyArray`
ומיישם את שתי התנהגויות של תכונות המערך.

אמנם קל להחזיר פרוקסי מקונסטרקטור מחלקה, אך במצב שכזהנוצר פרוקסי חדש עבור כל מופע. עם זאת יש דרך לגרום לכל המופעים לחלוק פרוקסי אחד: ניתן להשתמש בפרוקסי כפרוטוטייפ.

## שימוש בפרוקסי כפרוטוטייפ

פרוקסי יכול לשמש כפרוטוטייפ, אך פעולה זו מסובכת יותר מהדוגמאות הקודמות שהופיעו בפרק זה. כאשר פרוקסי משמש פרוטוטייפ, מלכודות הפרוקסי
נקראות רק כאשר פעולת ברירת המחדל תמשיך לפרוטוטייפ, מה שמגביל את יכולות הפרוקסי
כפרוטוטייפ.
כמו בדוגמה זו:

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
נוצר עם פרוקסי כפרוטוטייפ. הפיכת
`target`
למטרת הפרוקסי הופכת את
`target`
לפרוטוטייפ של
`newTarget`
מפני שהפרוקסי שקוף לנו. כעת, מלכודות פרוקסי יקראו רק אם פעולה ב-
`newTarget`
תעביר את הפעולה הלאה ל
`target`.

המתודה
<span dir="ltr">`Object.defineProperty()`</span>
מופעלת על
`newTarget`
ליצירת תכונה עצמית בשם
`name`.
הגדרת מאפיין על אובייקט אינה פעולה שממשיכה בדרך כלל לפרוטוטייפ של האובייקט, כך שמלכודת
`defineProperty`
על הפרוקסי לעולם לא נקראת והמאפיין
`name`
מתווסף ל
`newTarget`
כתכונה עצמית.

בעוד שיכולות הפרוקסי מוגבלות מאוד כאשר משתמשים בהם בתור פרוטוטייפ, ישנו מספר מועט של מלכודות שעדיין מועילות.

### שימוש במלכודת `get` על פרוטוטייפ

כאשר הפונקציה הפנימית
`[[Get]]`
נקראית כדי לקרוא תכונה של אובייקט, מחפשים תחילה בתכונות העצמיות שלו
<span dir="ltr">(own property)</span>.
אם לא נמצאה תכונה עצמית עם השם הנתון, הפעולה ממשיכה לפרוטוטייפ ומחפשת שם את התכונה. התהליך נמשך עד שלא יהיה פרוטוטייפ נוסף לבדיקה.

תודות לתהליך זה, אם נגדיר את מלכודת הפרוקסי
`get`,
המלכודת תיקרא על הפרוטוטייפ בכל פעם שאין בנמצא תכונה עצמית בשם הנתון. ניתן להשתמש במלכודת
`get`
כדי למנוע התנהגות בלתי צפויה בעת גישה לתכונות שאיננו בטוחים אם הן אכן קיימות. פשוט ניצור אובייקט שזורק שגיאה בכל פעם שמנסים לגשת לתכונה שאינה קיימת:

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
נוצר עם פרוקסי שמשמש בתור פרוטוטייפ שלו. המלכודת
`get`
זורקת שגיאה כאשר היא נקראת כדי ליידע שהמזהה הנתון אינו קיים באובייקט -
`thing`.
כאשר
`thing.name`
נקרא, הפעולה אינה מפעילה את מלכודת
`get`
שעל הפרוטוטייפ מכיוון שהתכונה קיימת על
`thing`.
מלכודת
`get`
תפעל רק כאשר קוראים את התכונה
`thing.unknown` ,
שאינה קיימת.

כאשר שורה הקוד האחרונה מתבצעת,
`unknown`
אינו תכונה של
`thing`,
כך שהפעולה ממשיכה לפרוטוטייפ. המלכודת
`get`
זורקת שגיאה.
התנהגות מסוג זה יכולה להיות שימושית מאוד ב
JavaScript,
כאשר תכונות לא ידועות מחזירות
`undefined`
במקום לזרוק שגיאה
(כמו שקורה בשפות אחרות).

חשוב לציין כי בדוגמה זו,
`trapTarget`
ו
`receiver`
הינם אובייקטים שונים. כאשר פרוקסי משמש בתור פרוטוטייפ, ה
`trapTarget`
הוא האובייקט פרוטוטייפ עצמו בעוד
`receiver`
הוא אובייקט המופע. במקרה זה, משמעות הדבר היא כי
`trapTarget`
שווה ל
`target`
ו
`receiver`
שווה ל
`thing`.
זה מאפשר לנו לגשת הן לאובייקט המטרה של הפרוקסי
והן לאובייקט עליו אמורה הפעולה להתקיים.

### שימוש במלכודת `set` על פרוטוטייפ

המתודה הפנימית
`[[Set]]`
בודקת גם תכונות עצמיות
(own properties)
ואז ממשיכה לפרוטוטייפ במידת הצורך. כאשר מקצים ערך לתכונה באובייקט, הערך מוקצה לתכונה עצמית בעלת שם זהה במידה וקיימת. אם אין תכונה עצמית בעלת אותו השם, הפעולה ממשיכה לפרוטוטייפ. החלק המסובך הוא שלמרות שפעולת ההקצאה ממשיכה לפרוטוטייפ, הקצאת ערך עבור לתכונה תיצור תכונה עצמית על המופע עצמו
(ולא על הפרוטוטייפ)
כברירת מחדל, ללא קשר לשאלה האם קיימת תכונה בשם זה על הפרוטוטייפ.

כדי לקבל מושג טוב יותר מתי נקראת מלכודת
`set`
בפרוטוטייפ, עיינו בדוגמה הבאה שמציגה את התנהגות ברירת המחדל:

<div dir="ltr">

```js
let target = {};
let thing = Object.create(new Proxy(target, {
    set(trapTarget, key, value, receiver) {
        return Reflect.set(trapTarget, key, value, receiver);
    }
}));

console.log(thing.hasOwnProperty("name"));      // false

// מפעיל את מלכודת
// `set`
thing.name = "thing";

console.log(thing.name);                        // "thing"
console.log(thing.hasOwnProperty("name"));      // true

// לא מפעיל את המלכודת
// `set`
thing.name = "boo";

console.log(thing.name);                        // "boo"
```

</div>

בדוגמה זו,
`target`
מתחיל ללא תכונות עצמיות. לאובייקט
`thing`
מוגדר פרוקסי כפרוטוטייפ שמוגדרת עבורו המלכודת
`set`
על מנת ליירט יצירה של תכונות חדשות. כאשר עבור
`thing.name`
מוקצה הערך
`"thing"`
,מופעלת מלכודת
`set`
מפני של-
`thing`
אין תכונה עצמית בשם
`name`.
בתוך מלכודת
`set`,
המשתנה בשם
`trapTarget`
זהה ל
`target`
והמשתנה
`receiver`
זהה ל
`thing`.
ביצוע הפעולה אמור בסופו של דבר ליצור תכונה חדשה על האובייקט
`thing`,
ולמרבה המזל
<span dir="ltr">`Reflect.set()`</span>
מיישם עבורנו את התנהגות דיפולטיבית זו אם נעביר את
`receiver`
בתור הארגומנט רביעי.

לאחר שהתכונה
`name`
נוצרה על
`thing`,
הגדרת
`thing.name`
לערך שונה לא תפעיל את מלכודת
`set`.
בנקודה זו,
`name`
נחשבת לתכונה עצמית אל האובייקט ולכן הפעולה
`[[Set]]`
אף פעם לא ממשיכה לאב-טיפוס.

### שימוש במלכודת `has` על פרוטוטייפ

מלכודת ה
`has`
מיירטת את השימוש באופרטור
`in`
על אובייקטים. האופרטור
`in`
מחפש תחילה תכונה עצמית של אובייקט עם השם הנתון. אם תכונה עצמית בשם זה אינה קיימת, הפעולה ממשיכה לפרוטוטייפ. אם אין תכונה עצמית על הפרוטוטייפ, החיפוש יימשך דרך שרשרת הפרוטוטייפ עד שנמצא תכונה עצמית או שאין יותר אובייקטים בשרשרת.

לפיכך מלכודת
`has`
מופעלת
רק כאשר פעולת החיפוש מגיעה לאובייקט פרוקסי
בשרשרת הפרוטוטייפ. כאשר משתמשים בפרוקסי בתור פרוטוטייפ, זה ייתכן רק כאשר אין תכונה עצמית בשם זה. לדוגמה:

<div dir="ltr">

```js
let target = {};
let thing = Object.create(new Proxy(target, {
    has(trapTarget, key) {
        return Reflect.has(trapTarget, key);
    }
}));

// יפעיל את מלכודת
// `has`
console.log("name" in thing);                   // false

thing.name = "thing";

// לא יפעיל את מלכודת
// `has`
console.log("name" in thing);                   // true
```

</div>

הקוד לעיל יוצר מלכודת פרוקסי מסוג
`has`
בפרוטוטייפ של
`thing`.
המלכודת
`has`
אינה מעבירה
את האובייקט
`receiver`,
בניגוד למלכודות
`get`
ו
`set`
מכיוון שחיפוש בפרוטוטייפ הינה פעולה מתרחשת באופן אוטומטי כאשר משתמשים באופרטור
`in`.
במקום זאת, מלכודת
`has`
צריכה לפעול רק על
`trapTarget`,
שזהה ל
`target`.
בפעם הראשונה שהאופרטור
`in`
פעל בדוגמה לעיל, המלכודת
`has`
מופעלת מפני שהתכונה
`name`
אינה קיימת כתכונה עצמית על
`thing`.
כאשר
`thing.name`
מקבל ערך ושוב משתמשים באופרטור
`in`,
המלכודת
`has`
אינה מופעלת מכיוון שהפעולה נפסקת לאחר מציאת התכונה העצמית
`name`
בתוך
`thing`.

הדוגמאות עבור פרוטוטייפ עד כה התרכזו סביב אובייקטים שנוצרו באמצעות המתודה
<span dir="ltr">`Object.create()`</span>.
אבל אם נרצה ליצור מחלקה שיש בה פרוקסי כפרוטוטייפ, התהליך מורכב יותר.

### פרוקסי כפרוטוטייפ של מחלקה

לא ניתן לשנות מחלקה באופן ישיר כך שתשתמש כפרוקסי בתור פרוטוטייפ מכיוון שתכונת
`prototype`
שלהם אינה ניתן לכתיבה -
(non-writable).
עם זאת, ניתן ליצור מחלקה שיש בה פרוקסי כפרוטוטייפ באמצעות ירושה. כדי להתחיל, יש ליצור פונקציית קונסטרוקטור. לאחר מכן ניתן להחליף את הפרוטוטייפ בפרוקסי. הנה דוגמה:

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

// זורק שגיאה בעקבות מלכודת
// `get`
let result = thing.name;
```

</div>

הפונקציה
`NoSuchProperty`
מייצגת את הבסיס ממנו תירש המחלקה. אין הגבלות על התכונה
`prototype`
של פונקציות, כך שנוכל להחליף אותה באמצעות פרוקסי. המלכודת
`get`
משמשת כדי לזרוק שגיאה כאשר התכונה אינה קיימת. האובייקט
`thing`
נוצר כמופע של
`NoSuchProperty`
וזורק שגיאה כאשר ניגשים לתכונה
`name`
אם אינה קיימת.

השלב הבא הוא ליצור מחלקה שיורשת מ
`NoSuchProperty`.
ניתן פשוט להשתמש בסינטקס
`extends`
כמו שהוסבר בפרק 9 על מנת להכניס את הפרוקסי
לשרשרת הפרוטוטייפ של מחלקה, ככה:

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

// יזרוק שגיאה כי
// "wdth"
// לא קיים
let area2 = shape.length * shape.wdth;
```

</div>

המחלקה
`Square`
יורשת מ
`NoSuchProperty`
ולכן הפרוקסי נמצא בשרשרת הפרוטוטייפ של
`Square`.
האובייקט
`shape`
נוצר כמופע חדש של
`Square`
ויש לו שתי תכונות עצמיות:
`length`
ו
`width`.
קריאת הערכים של אותן תכונות פועלת בהצלחה מכיוון שמלכודת
`get`
אף פעם לא נקראת. רק כאשר ניגשים לתכונה שאינה קיימת על
`shape`
(
`shape.wdth`,
שגיאת הקלדה ברורה
)
הדבר גורם למלכודת
`get`
לפעול ולזרוק שגיאה.

הדבר מראה לנו שהפרוקסי אכן מופיע בשרשרת הפרוטוטייפ של
`shape`,
אך לא ברור אם הפרוקסי
הוא הפרוטוטייפ הישיר של
`shape`.
למעשה, הפרוקסי
מרוחק מספר צעדים במעלה שרשרת הפרוטוטייפ מ
`shape`.
ניתן לראות זאת בצורה ברורה יותר על ידי שינוי הדוגמה הקודמת:

<div dir="ltr">

```js
function NoSuchProperty() {
    // ריק
}

// מצביע לפרוקסי שישמש בתור פרוטוטייפ
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

גרסה זו של הקוד שומרת מצביע לפרוקסי
במשתנה שנקרא
`proxy`
כך שקל לזהותו אחר כך. הפרוטוטייפ של
`shape`
הוא
`Square.prototype`,
שאינו
פרוקסי.
אך הפרוטוטייפ של
`Square.prototype`
הוא הפרוקסי שיורש מ-
`NoSuchProperty`.

הירושה מוסיפה שלב נוסף בשרשרת הפרוטוטייפ, זהו דבר שחשוב לדעת מכיוון שפעולות שעלולות לגרום לקריאה למלכודת
`get`
על
`proxy`
צריכות לעבור שלב נוסף. אם קיימת תכונה עצמית על
`Square.prototype`,
אז הדבר ימנע את הפעלת מלכודת
`get`,
כמו בדוגמה הבאה:

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

// יזרוק שגיאה כי
// "wdth"
// לא קיים
let area3 = shape.length * shape.wdth;
```

</div>

כאן למחלקה
`Square`
יש מתודה בשם
<span dir="ltr">`getArea()`</span>.
המתודה
<span dir="ltr">`getArea()`</span>
מתווספת אוטומטית ל-
`Square.prototype`
כך כאשר
<span dir="ltr">`shape.getArea()`</span>
נקראת, החיפוש אחרי המתודה
<span dir="ltr">`getArea()`</span>
מתחיל ב
`shape`
ואז ממשיך לפרוטוטייפ שלו. מפני ש
<span dir="ltr">`getArea()`</span>
קיימת בפרוטוטייפ, החיפוש נעצר והפרוקסי לא מופעל. זוהי למעשה ההתנהגות שנרצה במצב זה, מכיוון שלא נרצה לזרוק שגיאה כאשר המתודה
<span dir="ltr">`getArea()`</span>
נקראת.

למרות שנדרש מעט קוד נוסף כדי ליצור מחלקה עם פרוקסי בשרשרת הפרוטוטייפ, זה יכול להיות שווה את המאמץ אם נרצה פונקציונליות כזו.

## סיכום

לפני
ECMAScript 6,
אובייקטים מסוימים (כגון מערכים) היו בעלי התנהגות לא סטנדרטית שמפתחים לא הצליחו לשכפל. פרוקסי משנה את התמונה. הוא מאפשר לנו להגדיר התנהגות לא-סטנדרטית משלנו עבור מספר פעולות
JavaScript
ברמה הבסיסית, כך שנוכל לשכפל את כל ההתנהגויות של אובייקטים מובנים של
JavaScript
באמצעות מלכודות פרוקסי.
מלכודות אלה נקראות מאחורי הקלעים כאשר מבצעים פעולות שונות, כמו שימוש באופרטור
`in`.

ממשק הריפלקשן
(reflection API)
קיים גם ב-
ECMAScript 6
כדי לאפשר למפתחים ליישם את התנהגות ברירת המחדל עבור כל מלכודת פרוקסי. לכל מלכודת פרוקסי יש מתודה מקבילה בעלת אותו שם באובייקט
`Reflect`,
גם הוא תוספת נוספת של
ECMAScript 6.
באמצעות שילוב של מלכודות פרוקסי וממשק הריפלקשן ניתן לגרום לפעולות מסוימות להתנהג אחרת בתנאים מסוימים תוך שימוש בהתנהגות המובנית כשנרצה בכך.

פרוקסי הניתן לביטול הוא פרוקסי מיוחד הניתן לביטול באמצעות  שימוש בפונקציה
<span dir="ltr">`revoke()`</span>.
הפונקציה
<span dir="ltr">`revoke()`</span>
מבטלת את כל הפונקציונליות שבפרוקסי,
כך שכל ניסיון לקיים אינטראקציה עם תכונות הפרוקסי זורק שגיאה לאחר שהפונקציה
<span dir="ltr">`revoke()`</span>
מופעלת. פרוקסי הניתן לביטול חשוב לאבטחת יישומים שבהם מפתחים חיצונים עשויים להזדקק לגישה לאובייקטים מסוימים למשך פרק זמן מוגדר.

בעוד ששימוש ישיר בפרוקסי הוא מקרה השימוש המובהק ביותר, ניתן גם להשתמש בפרוקסי בתור פרוטוטייפ של אובייקט אחר. במקרה כזה, אנו מוגבלים במספר מלכודות הפרוקסי
בהן אפשר להשתמש ביעילות. רק המלכודות
`get`, `set`,
ו
`has`
ניתנות לשימוש על פרוקסי כאשר הוא משמש בתור פרוטוטייפ, דבר שמצמצם את יכולת השימוש בו.

</div>