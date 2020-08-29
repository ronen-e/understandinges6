<div dir="rtl">

# מחלקות בג׳אווהסקריפט

בניגוד לרוב שפות תכנות מונחה עצמים, ג׳אווהסקריפט לא תמכה במחלקות 
(classes)
והורשה במחלקות 
(
    הורשה קלאסית
    -
    <span dir="ltr">classical inheritance</span>
).
זה גרם לבלבול בקרב מפתחים רבים ולפני אקמהסקריפט 6, ספריות רבות כתבו קוד שיגרום לג׳אווהסקריפט לממש מחלקות למראית עין. אף על פי שמספר מפתחי ג׳אווהסקריפט מאמינים שאין צורך במחלקות בשפה, מספר הספריות הרב שנוצר במיוחד למטרה זו גרם להוספת מחלקות באקמהסקריפט 6.

כדי להבין כיצד מחלקות באקמהסקריפט 6 עובדות, יעזור לנו להבין את המנגנונים הפנימיים שנמצאים בשימוש מחלקות, לכן פרק זה יתחיל בהצגת הדרכים בהן מפתחים באקמהסקריפט 5 השיגו התנהגות דמוית מחלקה. כפי שתראו בהמשך, מחלקות באקמהסקריפט 6 אינן זהות למחלקות בשפות אחרות. יש להן אופי ייחודי שמגלם את הטבע הדינמי של ג׳אווהסקריפט.

## מבנים דמויי מחלקה באקמהסקריפט 5

באקמהסקריפט 5 וקודם לכן, לא היו מחלקות בג׳אווהסקריפט. הדבר הקרוב ביותר למחלקה היה פונקציה מסוג קונסטרטור ולאחר מכן הוספת מתודות לפרוטוטייפ שלה, גישה שלרוב הייתה מכונה ״יצירת סוג מותאם״
<span dir="ltr">(creating a custom type)</span>.
לדוגמה:


<div dir="ltr">

```js
function PersonType(name) {
    this.name = name;
}

PersonType.prototype.sayName = function() {
    console.log(this.name);
};

let person = new PersonType("Nicholas");
person.sayName();   // "Nicholas"

console.log(person instanceof PersonType);  // true
console.log(person instanceof Object);      // true
```

</div>

בקוד שבדוגמה, המזהה 
`PersonType`
הוא קונסטרטור שיוצר אוביקט בעל תכונה אחת בשם
`name`.
המתודה
<span dir="ltr">`sayName()`</span>
מקושרת לפרוטוטייפ כך שהיא משותפת לכל האוביקטים מסוג
`PersonType`.
לאחר מכן נוצר מופע חדש של
`PersonType`
בעזרת האופרטור
`new`.
האוביקט שנוצר
`person`
נחשב למופע של 
`PersonType`
וגם של
`Object`
על ידי הורשה פרוטוטיפית.

צורת כתיבה זו קיימת בבסיס מרבית הספריות שמחקות יצירת מחלקה בג׳אווהסקריפט ומנקודה זו מתחילות גם מחלקות באקמהסקריפט 6.

## הגדרת מחלקה

הצורה הפשוטה ביותר של מחלקה באקמהסקריפט 6 הינה הגדרת מחלקה
<span dir="ltr">`(class declaration)`</span>
שנראית דומה מאוד להגדרת מחלקות בשפות אחרות.

### הגדרת מחלקה בסיסית

הגדרת מחלקה מתחילה עם המילה השמורה
`class`
ולאחר מכן יבוא שם המחלקה. יתר התחביר נראה דומה לזה של מתודות מקוצרות באוביקט ליטראל, מבלי הצורך בפסיק ביניהן. להלן הגדרת מחלקה פשוטה:

<div dir="ltr">

```js
class PersonClass {

    // זהה לקונסטרטור מסוג
    // PersonType
    constructor(name) {
        this.name = name;
    }

    // זהה למתודה
    // PersonType.prototype.sayName
    sayName() {
        console.log(this.name);
    }
}

let person = new PersonClass("Nicholas");
person.sayName();   // "Nicholas"

console.log(person instanceof PersonClass);     // true
console.log(person instanceof Object);          // true

console.log(typeof PersonClass);                    // "function"
console.log(typeof PersonClass.prototype.sayName);  // "function"
```
</div>

הגדרת המחלקה 
`PersonClass`
מתנהגת בדומה ל-
`PersonType`
מהדוגמה הקודמת. אך במקום להגדיר פונקציה בתור קונסטרקטור, הגדרת מחלקה מאפשרת לנו להגדיר את הקונסטרקטור ישירות בתוך המחלקה באמצעות שם המתודה המיוחד
`constructor`.
מכיוון שמתודות של מחלקה משתמשות בתחביר המקוצה, אין צורך להשתמש במילה השמורה
`function`.
כל שאר המתודות חסרי משמעות מיוחדת, ולכן ניתן להוסיף מתודות נוספות ללא הגבלה.

I> *תכונות עצמיות*
(*Own properties*),
שמופיעות על המופע ולא על הפרוטוטיפ, יכולות להופיע רק בתוך קונסטרקטור מחלקה או מתודה. בדוגמה זו,
`name`
הינה תכונה עצמית.
מומלץ ליצור את כל התכונות העצמיות בתוך הקונסטרקטור כך שיהיה מקום אחד שאחראי לכולן.

חשוב לזכור שהגדרת מחלקה היא רק מעטפה תחבירית
(syntactic sugar)
עבור הגדרות קיימות. ההגדרה עבור
`PersonClass`
למעשה יוצרת פונקציה שמתנהגת כמו המתודה
`constructor`,
ולכן הפקודה
`typeof PersonClass`
מחזירה את התוצאה
`"function"`.
בדוגמה זו, המתודה
<span dir="ltr">`sayName()`</span>
נוצרת כמתודה על האוביקט
`PersonClass.prototype`,
בדומה למערכת היחסים בין
<span dir="ltr">`sayName()`</span>
לבין
`PersonType.prototype`
בדוגמה הקודמת. הדמיון הזה בין השיטות מאפשר לנו לערבב בין סוגים מותאמים ומחלקות מבלי לדאוג לשגיאות.

### מתי להשתמש במחלקות

למרות הדמיון בין מחלקות וסוגים מותאמים
(custom types),
קיימים מספר הבדלים חשובים ביניהם שחשוב לזכור:

1. עבור הגדרת מחלקה, בניגוד להגדרת פונקציה, לא מתבצעת פעולת ״הרמה״
(hoisting).
הגדרת מחלקה מתנהגת בדומה למשתנים מסוג
`let`
ולכן קיימת בתוך אזור ״מת״ עד שמנוע הריצה מגיע לשורה בה מופיעה ההגדרה.
1. הקוד שרץ בתוך הגדרת המחלקה רץ במצב קשיח באופן אוטומטי. אין דרך להפסיק את פעולת המצב הקשיח בתוך מחלקה
1. כל המתודות אינן אינומרביליות. 
(non-enumerable)
זהו שינוי משמעותי יחסית לסוגים מותאמים אישית,  שבהם צריך להשתמש במתודה
<span dir="ltr">`Object.defineProperty()`</span>
בשביל להפוך מתודה ללא אינומרבילית.

1. למתודות המחלקה אין מתודה פנימית מסוג
`[[Construct]]`
ולכן הן זורקות שגיאה כאשר קוראים להן עם האופרטור
`new`.
1. קריאה לקונסטרקטור המחלקה ללא האופרטור
`new`
זורקת שגיאה
1. ניסיון לדרוס את שם המחלקה מתוך מתודה של המחלקה יזרוק שגיאה

בהתייחס לכתוב לעיל, הגדרת המחלקה
`PersonClass`
מהדוגמה הקודמה זהה למעשה לקוד הבא, שלא מתשמש בתחביר המחלקה:

<div dir="ltr">

```js
// זהה להגדרת המחלקה
// PersonClass
let PersonType2 = (function() {

    "use strict";

    const PersonType2 = function(name) {

        // חשוב לוודא שנעשה שימוש באופרטור
        // new
        if (typeof new.target === "undefined") {
            throw new Error("Constructor must be called with new.");
        }

        this.name = name;
    }

    Object.defineProperty(PersonType2.prototype, "sayName", {
        value: function() {

            // חשוב לוודא שלא נעשה שימוש באופרטור
            // new
            if (typeof new.target !== "undefined") {
                throw new Error("Method cannot be called with new.");
            }

            console.log(this.name);
        },
        enumerable: false,
        writable: true,
        configurable: true
    });

    return PersonType2;
}());
```
</div>

שימו לב שמופיעים 2 משתנים בשם
`PersonType2`:
הגדרת
`let`
במרחב החיצוני
והגדרת
`const`
בתוך ה-
IIFE.
בשיטה זו מונעים ממתודות של מחלקה לדרוס את שם המחלקה בעוד שקוד חיצוני למחלקה רשאי לעשות זאת. פונקצית הקונסטרקטור בוחנת את
`new.target`
כדי לוודא שהיא נקראת בעזרת שימוש באופרטור
`new`;
אחרת, תיזרק שגיאה.
בהמשך, המתודה
<span dir="ltr">`sayName()`</span>
מוגדרת ככזו שאינה אנומרבילית, והמתודה בוחנת את
`new.target`
על מנת לוודא שאינה נקראת עם האופרטור
`new`.
השלב האחרון הינו החזרת פונקצית הקונסטרקטור בתור ערך החזרה.

הדוגמה לעיל מדגימה שגם אם ניתן לעשות את כל מה שמחלקה יכולה לעשות אף מבלי להשתמש בתחביר החדש, תחביר המחלקה מפשט את הקוד באופן משמעותי.

A> ### שמות מחלקה קבועים
A>
A> שם המחלקה נחשב בתור 
`const`
רק בתוך המחלקה עצמה. המשמעות היא שניתן לדרוס את שם המחלקה מחוץ למחלקה אך לא מתוך מתודה שלה.
לדוגמה:
A>
<div dir="ltr">
A> ```js
A> class Foo {
A>    constructor() {
A>        Foo = "bar";    // תיזרק שגיאה
A>    }
A> }
A>
A>// עובד ללא שגיאה
A> Foo = "baz";
A> ```
</div>
A>
A> בקוד שבדוגמה, המשתנה
`Foo`
שבתוך הקונסטרקטור מצביע לערך אחר מאשר המשתנה
`Foo`
שבתוך המחלקה.
`Foo`
הפנימי מוגדר כמו
`const`
ולכן לא ניתן לשנות ערכו.
שגיאה נזרקת כאשר הקונסטרקטור מנסה לדרוס את
`Foo`
ללא קשר לערך החדש.
אך מכיוון ש
`Foo`
החיצוני מוגדר כאילו היה משתנה מסוג
`let`
ניתן לדרוס את ערכו עם כל סוג ערך בכל זמן נתון.

## מחלקה כביטוי קוד

מחלקות ופונקציות דומות בכך שהן מגיעות בשתי צורות:
הגדרות 
(declarations)
וביטויים
(expressions).
הגדרות פונקציה ומחלקה מתחילות במילה השמורה המתאימה
(
`function`
או
`class`, 
בהתאמה
)
ולאחר מכן בא המזהה. לפונקציות שבאות בצורה של ביטוי יש צורה שלא דורשת מזהה לאחר המילה
`function`
ובאופן דומה, למחלקות יש צורת כתב כביטוי שלא דורשת מזהה לאחר המילה
`class`.
*ביטויי מחלקה*
(*class expressions*)
אלו נועדו לשמש בהגדרת משתנים או להיות מועברים לפונקציה כארגומנטים.

### ביטוי מחלקה בסיסי

להלן ביטוי מחלקה שמשתווה לדוגמאות הקודמות של
`PersonClass`
ולאחריו קוד שמשתמש באותו הביטוי.

<div dir="ltr">

```js
let PersonClass = class {

    // דומה לקונסטרטור
    // PersonType
    constructor(name) {
        this.name = name;
    }

    // PersonType.prototype.sayName
    sayName() {
        console.log(this.name);
    }
};

let person = new PersonClass("Nicholas");
person.sayName();   // "Nicholas"

console.log(person instanceof PersonClass);     // true
console.log(person instanceof Object);          // true

console.log(typeof PersonClass);                    // "function"
console.log(typeof PersonClass.prototype.sayName);  // "function"
```

</div>

כפי שרואים בדוגמה לעיל, ביטויי מחלקה אינם דורשים מזהים לאחר המילה השמורה
`class`.
מלבד עניין התחביר, ביטויי המחלקה זהים מבחינה פונקציונאלית להגדרת מחלקה.

ההחלטה האם להשתמש בהגדרת מחלקה או בביטוי מחלקה היא בעיקרה עניין של סגנון. בניגוד להגדרת פונקציה וביטוי פונקציה, לא מתבצעת פעולת הרמה
(hoisting)
להגדרת מחלקה או ביטוי מחלקה, ולכן לבחירה אין משמעות מבחינת הרצת הקוד.

### ביטוי מחלקה עם שם

החלק האחרון השתמש בביטוי מחלקה אנונימי בדוגמה, אבל כמו בביטוי פונקציה רגיל, ניתן לתת שם עבור ביטוי המחלקה. כדי לעשות זאת יש להשתמש במזהה לאחר המילה השמורה
`class`
כמו בדוגמה הבאה:

<div dir="ltr">

```js
let PersonClass = class PersonClass2 {

    // דומה לקונסטרטור
    // PersonType
    constructor(name) {
        this.name = name;
    }

    // PersonType.prototype.sayName
    sayName() {
        console.log(this.name);
    }
};

console.log(typeof PersonClass);        // "function"
console.log(typeof PersonClass2);       // "undefined"
```
</div>

בדוגמה זו, ביטוי המחלקה מקבל את השם
`PersonClass2`.
המזהה
`PersonClass2`
קיים אך ורק בתוך הקוד שמגדיר את המחלקה ויכול לשמש במתודות מחלקה
(
כמו למשל המתודה
<span dir="ltr">`sayName()`</span>
שבדוגמה לעיל
).
מחוץ למחלקה, התוצאה עבור
`typeof PersonClass2`
היא
`"undefined"`
מאחר והמזהה 
אינו קיים.

כדי להבין מדוע זאת, ראו קוד זהה בהתנהגותו שאינו משתמש במחלקות:

<div dir="ltr">

```js
// זהה מבחינה פונקציונלית לביטוי המחלקה בעל השם
// PersonClass
let PersonClass = (function() {

    "use strict";

    const PersonClass2 = function(name) {

        // חשוב לוודא שנעשה שימוש באופרטור
        // new
        if (typeof new.target === "undefined") {
            throw new Error("Constructor must be called with new.");
        }

        this.name = name;
    }

    Object.defineProperty(PersonClass2.prototype, "sayName", {
        value: function() {

            // חשוב לוודא שלא נעשה שימוש באופרטור
            // new
            if (typeof new.target !== "undefined") {
                throw new Error("Method cannot be called with new.");
            }

            console.log(this.name);
        },
        enumerable: false,
        writable: true,
        configurable: true
    });

    return PersonClass2;
}());
```

</div>


יצירת ביטוי מחלקה בעל שם משנה במקצת את מה שמתרחש בתוך מנוע הריצה. עבור הגדרות מחלקה
(class declarations)
המזהה החיצוני
(
    שהוגדר עם 
    `let`
)
יש אותו שם כמו הקשירה הפנימית
(
    שהוגדרה עם
    `const`
).
ביטוי מחלקה בעל שם משתמש באותו שם באמצעות 
`const`
ולכן
`PersonClass2`
ניתן לשימוש רק בתוך המחלקה.

למרות שיש הבדק בהתנהגות בין ביטוי מחלקה בעל שם לבין ביטוי פונקציה בעל שם, קיים עדיין דמיון רב בין השניים. שניהם יכולים לשמש כערכים, דבר שפותח בפנינו אפשרויות רבות, שעליהן יורחב בהמשך.

## מחלקות נחשבות אזרחים מדרגה ראשונה

בעולם התכנות, נאמר על דבר שהוא *אזרח מדרגה ראשונה*
(*first-class citizen*)
כאשר הוא יכול לשמש בתור ערך, כלומר ניתן להעביר אותו לפונקציה, להחזיר אותו מפונקציה, ולבצע השמה שלו למשתנה.
פונקציות בג׳אווהסקריפט נחשבות לאזרחים מדרגה ראשונה
(
    לפעמים קוראים להם בשם פונקציות מדרגה ראשונה - 
<span dir="ltr">first class functions</span>
),
והדבר נחשב לאחד מהדברים שמייחדים את ג׳אווהסקריפט יחסית למשפות תכנות אחרות.

אקמהסקריפט 6 ממשיכה עם המסורת הקיימת על ידי הפיכת מחלקות לאזרחים מדרגה ראשונה. זה מאפשר לנו להשתמש במחלקות במגוון דרכים. למשל, ניתן להעביר אותן לתוך פונקציות כארגומנטים:

<div dir="ltr">

```js
function createObject(classDef) {
    return new classDef();
}

let obj = createObject(class {

    sayHi() {
        console.log("Hi!");
    }
});

obj.sayHi();        // "Hi!"
```

</div>

בדוגמה לעיל, הפונקציה
<span dir="ltr">`createObject()`</span>
נקראת עם מחלקה אנונימית בתור ארגומנט, יוצרת מופע של אותה מחלקה, ומחזירה את המופע ומבצעת השמה שלו למשתנה
`obj`.

שיומש מעניין נוסף של ביטויי מחלקה הינו יצירת סינגלטון
(singleton)
על ידי קריאה מיידית לקונסטרקטור המחלקה. כדי לעשות זאת יש לקרוא לאופרטור
`new`
על ביטוח המחלקה ולהשתמש בסוגריים בסוף.
לדוגמה:

<div dir="ltr">

```js
let person = new class {

    constructor(name) {
        this.name = name;
    }

    sayName() {
        console.log(this.name);
    }

}("Nicholas");

person.sayName();       // "Nicholas"
```

</div>

בדוגמה לעיל, ביטוי מחלקה אנונימי נוצר ומופעל מיידית. הטכניקה מאפשר לנו להשתמש בתחביר כתיבת מחלקה לשם יצירת סינגלטון מבלי להשאיר משתנים שמצביעים על המחלקה.
(
    חשוב לזכור כי
    `PersonClass`
    יוצר קישור רק בתוך המחלקה, לא מחוצה לה
).
הסוגריים בסוף הביטוי מהווים אינדיקציה לכך שאנו קוראים לפונקציה וגם מעבירים פנימה ארגומנט.

הדוגמאות בפרק זה עד עתה התמקדו במחלקות עם מתודות. אך באפשרותנו ליצור גם תכונות גישה
(accessor properties)
בעזרת תחביר דומה לזה של אוביקט ליטראל.

## תכונות גישה

בעוד שתכונות עצמיות
(own properties)
אמורות להיווצר בתוך קונסטרקטור המחלקה, מחלקות מאפשרו לנו להגדיר תוכנות גישה
(accessor properties)
על הפרוטוטייפ. כדי ליצור
`getter`
יש להשתמש במילה השמורה
`get`
ולאחר מכן ברווח, שלאחריו יבוא המזהה.
כדי ליצור
`setter`
יש לפעול באותה צורה ולהשתמש במילה השמורה
`set`.
לדוגמה:


<div dir="ltr">

```js
class CustomHTMLElement {

    constructor(element) {
        this.element = element;
    }

    get html() {
        return this.element.innerHTML;
    }

    set html(value) {
        this.element.innerHTML = value;
    }
}

var descriptor = Object.getOwnPropertyDescriptor(CustomHTMLElement.prototype, "html");
console.log("get" in descriptor);   // true
console.log("set" in descriptor);   // true
console.log(descriptor.enumerable); // false
```

</div>

בקוד לעיל, המחלקה
`CustomHTMLElement`
פועלת כמעטפת לאלמנט
DOM
קיים. יש לה גם
getter
וגם
setter
בשם
`html`
שמשתמשת בתכונה
`innerHTML`
על האלמנט עצמו.
תכונת הגישה נוצרת על
`CustomHTMLElement.prototype`,
שכמו כל מתודה אחרת נחשבת ללא אינומרבילית. 
צורת הכתיבה המקבילה ללא שימוש במחלקה הינה:

<div dir="ltr">

```js
// מקביל לדוגמה הקודמת
let CustomHTMLElement = (function() {

    "use strict";

    const CustomHTMLElement = function(element) {

        // נוודא שהקונסטרקטור נקרא באמצעות האופרטור
        // new
        if (typeof new.target === "undefined") {
            throw new Error("Constructor must be called with new.");
        }

        this.element = element;
    }

    Object.defineProperty(CustomHTMLElement.prototype, "html", {
        enumerable: false,
        configurable: true,
        get: function() {
            return this.element.innerHTML;
        },
        set: function(value) {
            this.element.innerHTML = value;
        }
    });

    return CustomHTMLElement;
}());
```
</div>

כמו בדוגמאות קודמות, דוגמה זו מראה כמה קוד ניתן לחסוך על ידי שימוש במחלקה.
הגדרת תכונת הגישה
`html`
בלבד כמעט שווה בגודלה להגדרת המחלקה כולה.

## שם תכונה מחושב לאיבר במחלקה

הדמיון בין אוביקט ליטראל לבין מחלקה ממשיך. מתודות ותכונות גישה יכולות שיהיו בעלי שם מחושב
(computed name).
במקום להשתמש במזהה, נוכל להשתמש בסוגריים מרובעים מסביב לביטוי, כמו התחביר שמשמש לכתיבת שם תכונה מחושב באוביקט ליטראל. לדוגמה:

<div dir="ltr">

```js
let methodName = "sayName";

class PersonClass {

    constructor(name) {
        this.name = name;
    }

    [methodName]() {
        console.log(this.name);
    }
}

let me = new PersonClass("Nicholas");
me.sayName();           // "Nicholas"
```
</div>

בדוגמה לעיל המחלקה 
`PersonClass`
משתמשת במשתנה ומבצעת השמה של שם המתודה אליו. ערך המחרוזת
`"sayName"`
מועבר למשתנה
`methodName`,
שלאחר מכן משמש לצורך הגדרת המתודה. המתודה
<span dir="ltr">`sayName()`</span>
נקראת באופן ישיר לאחר מכן.

תכונות גישה יכולות להשתמש בשמות תכונה מחושבים גם כן:

<div dir="ltr">

```js
let propertyName = "html";

class CustomHTMLElement {

    constructor(element) {
        this.element = element;
    }

    get [propertyName]() {
        return this.element.innerHTML;
    }

    set [propertyName](value) {
        this.element.innerHTML = value;
    }
}
```
</div>

בדוגמה זו ה-
getter
וה-
setter
עבור התכונה
`html`
נקבעים על ידי המשתנה
`propertyName`.
לגישה לתכונה באמצעות
`.html`
יש אפקט רק עבור ההגדרה עצמה.

עד כה ראינו דמיון רב בין מחלקות לבין אוביקט ליטראל, במתודות, תכונות גישה ושמות מחושבים.
יש רק עוד נקודת דמיון אחת לכסות: גנרטורים.

## מתודות מסוג גנרטור

גנרטורים הוצגו בפרק 8, שם למדנו כיצד להגדיר גנרטור על אוביקט ליטראל על ידי הצמדת כוכבית
(`*`)
לשם המתודה.
אותו תחביר עובד עבור מחלקות, ומאפשר להפוך כל מתודה לגנרטור.
לדוגמה:

<div dir="ltr">

```js
class MyClass {

    *createIterator() {
        yield 1;
        yield 2;
        yield 3;
    }

}

let instance = new MyClass();
let iterator = instance.createIterator();
```
</div>

הקוד לעיל יוצר מחלקה בשם
`MyClass`
עם מתודת גנרטור בשם
<span dir="ltr">`createIterator()`</span>.
המתודה מחזירה איטרטור שערכיו מוגדרים בגנרטור. מתודות מסוג גנרטור שימושיות כאשר משתמשים באוביקט שמייצג אוסף של ערכים ונרצה לעבור על אותם ערכים בקלות. 
למערך, מפה וסט יש מספר מתודות מסוג גנרטור שעוזרות למפתחים לגשת לפריטים השמורים שם בצורות שונות.

בעוד שמתודות גנרטור הן שימושיות, הגדרת איטרטור דיפולטיבי למחלקה שימושית מאוד כאשר המחלקה מייצגת אוסף של ערכים. ניתן להגדיר את האיטרטור הדיפולטיבי למחלקה על ידי שימוש בסימבול
`Symbol.iterator`
כדי להגדיר מתודה מסוג גנרטור, כמו למשל:

<div dir="ltr">

```js
class Collection {

    constructor() {
        this.items = [];
    }

    *[Symbol.iterator]() {
        yield *this.items.values();
    }
}

var collection = new Collection();
collection.items.push(1);
collection.items.push(2);
collection.items.push(3);

for (let x of collection) {
    console.log(x);
}

// פלט
// 1
// 2
// 3
```
</div>

הדוגמה לעיל משתמשת בשם מחושב למתודה מסוג גנרטור שמעבירה שליטה אל איטרטור
<span dir="ltr">`values()`</span>.
של המערך
`this.items`.
כל מחלקה שמנהלת אוסף של ערכים כדאי שתכלול איטרטור דיפולטיבי מכיוון שמספר פעולות שמתבצעות על אוספי ערכים דורשות איטרטור. כעת כל מופע של
`Collection`
יכול להופיע בתוך לולאת
`for-of`
או שנוכל להריץ עליו את אופרטור הפיזור.

הוספת מתודות תכונות גישה לפרוטוטייפ המחלקה שימושי כאשר נרצה שלמופע המחלקה תהיה גישה ישירה אליהם. אם נרצה מתודות או תכונות גישה על המחלקה עצמה נצטרך להשתמש באיברים סטטיים.

## איברים סטטיים

הוספת מתודות על קונסטרקטורים כדי לדמות איברים סטטיים הינה טכניקה נפוצה באקמהסקריפט 5 וקודם לכן. לדוגמה:

<div dir="ltr">

```js
function PersonType(name) {
    this.name = name;
}

// מתודה סטטית
PersonType.create = function(name) {
    return new PersonType(name);
};

// מתודה של מופע
PersonType.prototype.sayName = function() {
    console.log(this.name);
};

var person = PersonType.create("Nicholas");
```
</div>

בשפות תכנות אחרות, מתודת פקטורי
(
    <span dir="ltr">`factory method`</span>. 
    הכוונה לפונקציה על המחלקה עצמה שמחזירה מופע של המחלקה
)
בשם
<span dir="ltr">`PersonType.create()`</span>
תיחשב למתודה סטטית, כיוון שאינה תלויה במופע של 
`PersonType`
לצורך פעולתה. 
מחלקות באקמהסקריפט 6 מפשטות יצירת איברים סטטיים על ידי השימוש במילה השמורה
`static`
לפני שם המתודה או תכונת הגישה.
להלן המקבילה בשימוש במחלקה עבור הדוגמה הקודמת:

<div dir="ltr">

```js
class PersonClass {

    // מקביל לקונסטרקטור
    // PersonType
    constructor(name) {
        this.name = name;
    }

    // מקביל למתודה
    // PersonType.prototype.sayName
    sayName() {
        console.log(this.name);
    }

    // מקביל למתודה
    // PersonType.create
    static create(name) {
        return new PersonClass(name);
    }
}

let person = PersonClass.create("Nicholas");
```

</div>

המחלקה
`PersonClass`
מכילה מתודה סטטית בודדת בשם
<span dir="ltr">`create()`</span>.
תחביר המתודה זהה לזה של
<span dir="ltr">`sayName()`</span>
מלבד המילה השמורה
`static`.
ניתן להשתמש במילה השמורה
`static`
עבור כל מתודה או תכונת גישה בתוך מחלקה. המגבלה היחידה היא שלא ניתן להשתמש במילה
`static`
בהגדרת מתודת הקונסטרקטור.

W> איברים סטטיים לא ניתנים לגישה מתוך מופע המחלקה. חייבים לגשת לאיברים סטטיים ישירות מתוך המחלקה עצמה

## ירושה במחלקות נגזרות

לפני אקמהסקריפט 6, מימוש ירושה עם סוגים מותאמים היה הליך ארוך. ירושה נכונה דרשה מספר שלבים. לדוגמה:

<div dir="ltr">

```js
function Rectangle(length, width) {
    this.length = length;
    this.width = width;
}

Rectangle.prototype.getArea = function() {
    return this.length * this.width;
};

function Square(length) {
    Rectangle.call(this, length, length);
}

Square.prototype = Object.create(Rectangle.prototype, {
    constructor: {
        value:Square,
        enumerable: false,
        writable: true,
        configurable: true
    }
});

var square = new Square(3);

console.log(square.getArea());              // 9
console.log(square instanceof Square);      // true
console.log(square instanceof Rectangle);   // true
```
</div>

`Square`
יורשת
מ-
`Rectangle`,
ועל מנת לעשות זאת עליה לדרוס את 
`Square.prototype`
עם אוביקט חדש שנוצר מ-
`Rectangle.prototype`
יחד עם קריאה ל-
<span dir="ltr">`Rectangle.call()`</span>.
השלבים האלו לרוב בלבלו מפתחים חדשים לשפה והיוו מקור לשגיאות גם עבור מפתחים מנוסים.

מחלקות מקלות על מימוש ירושה על ידי שימוש במילה השמורה
`extends`
כדי להגדיר את הפונקציה שממנה יורשת המחלקה. הפרוטוטיפים מקבלים הכוונה אוטומטית וניתן לקרוא לקונסטרקטור מחלקת הבסיס על ידי קריאה ל-
<span dir="ltr">`super()`</span>.
להלן דוגמה מקבילה לדוגמה הקודמת שנכתבה באקמהסקריפט 6:

<div dir="ltr">

```js
class Rectangle {
    constructor(length, width) {
        this.length = length;
        this.width = width;
    }

    getArea() {
        return this.length * this.width;
    }
}

class Square extends Rectangle {
    constructor(length) {

        // זהה ל
        // Rectangle.call(this, length, length)
        super(length, length);
    }
}

var square = new Square(3);

console.log(square.getArea());              // 9
console.log(square instanceof Square);      // true
console.log(square instanceof Rectangle);   // true
```
</div>

בדוגמה זו, המחלקה
`Square`
יורשת מהמחלקה
`Rectangle`
בעזרת המילה השמורה
`extends`.
הקונסטרקטור של
`Square`
משתמש ב-
<span dir="ltr">`super()`</span>
כדי לקרוא לקונסטרטור של
`Rectangle`
עם הארגומנטים הנחוצים. שימו לב שלא כמו בגרסת אקמהסקריפט 5, המזהה
`Rectangle`
מופיע אך ורק בתוך הגדרת המחלקה
(
    לאחר
    `extends`
).

מחלקות שיורשות ממחלקה אחרת נקראות
*מחלקות נגזרות*
(*derived classes*).
מחלקות נגזרות דורשות מאיתנו להשתמש ב-
<span dir="ltr">`super()`</span>
במידה והגדרנו קונסטרקטור משלנו. אם לא נעשה זאת תיזרק שגיאה
אם לא נממש קונסטרקטור בעצמנו אזי 
<span dir="ltr">`super()`</span>
ייקרא באופן אוטומטי עם כל הארגומנטים המסופקים בעת יצירת מופע חדש של המחלקה.
לכן בדוגמה הבאה, שתי המחלקות הבאות זהות זו לזו:

<div dir="ltr">

```js
class Square extends Rectangle {
    // אין קונסטרקטור
}

// זהה מבחינה מעשית
class Square extends Rectangle {
    constructor(...args) {
        super(...args);
    }
}
```
</div>

המחלקה השנייה בדוגמה מראה כיצד עובד הקונסטרקטור הדיפולטיבי עבור כל מחלקה נגזרת. כל הארגומנטים מועברים, לפי הסדר, לקונסטרקטור מחלקת הבסיס. במקרה זה הפונקציונליות לא תואמת באופן מלא מכיוון שהקונסטרקטור עבור
`Square`
דורש רק ארגומנט אחד, לכן מומלץ להגדיר את הקונסטרקטור בעצמנו.

W> ישנם מספר דברים שחשוב לזכור כאשר משתמשים במילה
W> <span dir="ltr">`super()`</span>
W> 1. ניתן להשתמש ב
W> <span dir="ltr">`super()`</span>
W> רק במחלקה נגזרת. אם ננסה להשתמש בו במחלקה שאינה נגזרת
W> (
W>     מחלקה שאינה משתמשת ב-
W>     `extends`
W> )
W> או בתוך פונקציה, הדבר יגרום לשגיאה.
W> 1. חייבים לקרוא ל-
W> <span dir="ltr">`super()`</span>
W> לפני שניגשים אל 
W> `this`
W> בקונסטרקטור.
W> מכיוון ש
W> <span dir="ltr">`super()`</span>
W> אחראי לאתחול ערכו של 
W> `this`,
W> כל ניסיון לגשת אל
W> `this`
W> לפני קריאה ל-
W> <span dir="ltr">`super()`</span>
W> גורם לשגיאה.
W> 1. הדריך היחידה להימנע מקריאה ל-
W> היא להחזיר אוביקט מתוך הקונסטרקטור.

</div>


### Shadowing Class Methods

The methods on derived classes always shadow methods of the same name on the base class. For instance, you can add `getArea()` to `Square` to redefine that functionality:

```js
class Square extends Rectangle {
    constructor(length) {
        super(length, length);
    }

    // override and shadow Rectangle.prototype.getArea()
    getArea() {
        return this.length * this.length;
    }
}
```

Since `getArea()` is defined as part of `Square`, the `Rectangle.prototype.getArea()` method will no longer be called by any instances of `Square`. Of course, you can always decide to call the base class version of the method by using the `super.getArea()` method, like this:

```js
class Square extends Rectangle {
    constructor(length) {
        super(length, length);
    }

    // override, shadow, and call Rectangle.prototype.getArea()
    getArea() {
        return super.getArea();
    }
}
```

Using `super` in this way works the same as the the super references discussed in Chapter 4 (see "Easy Prototype Access With Super References"). The `this` value is automatically set correctly so you can make a simple method call.

### Inherited Static Members

If a base class has static members, then those static members are also available on the derived class. Inheritance works like that in other languages, but this is a new concept for JavaScript. Here's an example:

```js
class Rectangle {
    constructor(length, width) {
        this.length = length;
        this.width = width;
    }

    getArea() {
        return this.length * this.width;
    }

    static create(length, width) {
        return new Rectangle(length, width);
    }
}

class Square extends Rectangle {
    constructor(length) {

        // same as Rectangle.call(this, length, length)
        super(length, length);
    }
}

var rect = Square.create(3, 4);

console.log(rect instanceof Rectangle);     // true
console.log(rect.getArea());                // 12
console.log(rect instanceof Square);        // false
```

In this code, a new static `create()` method is added to the `Rectangle` class. Through inheritance, that method is available as `Square.create()` and behaves in the same manner as the `Rectangle.create()` method.

### Derived Classes from Expressions

Perhaps the most powerful aspect of derived classes in ECMAScript 6 is the ability to derive a class from an expression. You can use `extends` with any expression as long as the expression resolves to a function with `[[Construct]]` and a prototype. For instance:

```js
function Rectangle(length, width) {
    this.length = length;
    this.width = width;
}

Rectangle.prototype.getArea = function() {
    return this.length * this.width;
};

class Square extends Rectangle {
    constructor(length) {
        super(length, length);
    }
}

var x = new Square(3);
console.log(x.getArea());               // 9
console.log(x instanceof Rectangle);    // true
```

`Rectangle` is defined as an ECMAScript 5-style constructor while `Square` is a class. Since `Rectangle` has `[[Construct]]` and a prototype, the `Square` class can still inherit directly from it.

Accepting any type of expression after `extends` offers powerful possibilities, such as dynamically determining what to inherit from. For example:

```js
function Rectangle(length, width) {
    this.length = length;
    this.width = width;
}

Rectangle.prototype.getArea = function() {
    return this.length * this.width;
};

function getBase() {
    return Rectangle;
}

class Square extends getBase() {
    constructor(length) {
        super(length, length);
    }
}

var x = new Square(3);
console.log(x.getArea());               // 9
console.log(x instanceof Rectangle);    // true
```

The `getBase()` function is called directly as part of the class declaration. It returns `Rectangle`, making this example is functionally equivalent to the previous one. And since you can determine the base class dynamically, it's possible to create different inheritance approaches. For instance, you can effectively create mixins:

```js
let SerializableMixin = {
    serialize() {
        return JSON.stringify(this);
    }
};

let AreaMixin = {
    getArea() {
        return this.length * this.width;
    }
};

function mixin(...mixins) {
    var base = function() {};
    Object.assign(base.prototype, ...mixins);
    return base;
}

class Square extends mixin(AreaMixin, SerializableMixin) {
    constructor(length) {
        super();
        this.length = length;
        this.width = length;
    }
}

var x = new Square(3);
console.log(x.getArea());               // 9
console.log(x.serialize());             // "{"length":3,"width":3}"
```

In this example, mixins are used instead of classical inheritance. The `mixin()` function takes any number of arguments that represent mixin objects. It creates a function called `base` and assigns the properties of each mixin object to the prototype. The function is then returned so `Square` can use `extends`. Keep in mind that since `extends` is still used, you are required to call `super()` in the constructor.

The instance of `Square` has both `getArea()` from `AreaMixin` and `serialize` from `SerializableMixin`. This is accomplished through prototypal inheritance. The `mixin()` function dynamically populates the prototype of a new function with all of the own properties of each mixin. (Keep in mind that if multiple mixins have the same property, only the last property added will remain.)

W> Any expression can be used after `extends`, but not all expressions result in a valid class. Specifically, the following expression types cause errors:
W>
W> * `null`
W> * generator functions (covered in Chapter 8)
W>
W> In these cases, attempting to create a new instance of the class will throw an error because there is no `[[Construct]]` to call.

### Inheriting from Built-ins

For almost as long as JavaScript arrays have existed, developers have wanted to create their own special array types through inheritance. In ECMAScript 5 and earlier, this wasn't possible. Attempting to use classical inheritance didn't result in functioning code. For example:

```js
// built-in array behavior
var colors = [];
colors[0] = "red";
console.log(colors.length);         // 1

colors.length = 0;
console.log(colors[0]);             // undefined

// trying to inherit from array in ES5

function MyArray() {
    Array.apply(this, arguments);
}

MyArray.prototype = Object.create(Array.prototype, {
    constructor: {
        value: MyArray,
        writable: true,
        configurable: true,
        enumerable: true
    }
});

var colors = new MyArray();
colors[0] = "red";
console.log(colors.length);         // 0

colors.length = 0;
console.log(colors[0]);             // "red"
```

The `console.log()` output at the end of this code shows how using the classical form of JavaScript inheritance on an array results in unexpected behavior. The `length` and numeric properties on an instance of `MyArray` don't behave the same as they do for the built-in array because this functionality isn't covered either by `Array.apply()` or by assigning the prototype.

One goal of ECMAScript 6 classes is to allow inheritance from all built-ins. In order to accomplish this, the inheritance model of classes is slightly different than the classical inheritance model found in ECMAScript 5 and earlier:

In ECMAScript 5 classical inheritance, the value of `this` is first created by the derived type (for example, `MyArray`), and then the base type constructor (like the `Array.apply()` method) is called. That means `this` starts out as an instance of `MyArray` and then is decorated with additional properties from `Array`.

In ECMAScript 6 class-based inheritance, the value of `this` is first created by the base (`Array`) and then modified by the derived class constructor (`MyArray`). The result is that `this` starts with all the built-in functionality of the base and correctly receives all functionality related to it.

The following example shows a class-based special array in action:

```js
class MyArray extends Array {
    // empty
}

var colors = new MyArray();
colors[0] = "red";
console.log(colors.length);         // 1

colors.length = 0;
console.log(colors[0]);             // undefined
```

`MyArray` inherits directly from `Array` and therefore works like `Array`. Interacting with numeric properties updates the `length` property, and manipulating the `length` property updates the numeric properties. That means you can both properly inherit from `Array` to create your own derived array classes and inherit from other built-ins as well. With all this added functionality, ECMAScript 6 and derived classes have effectively removed the last special case of inheriting from built-ins, but that case is still worth exploring.

### The Symbol.species Property

An interesting aspect of inheriting from built-ins is that any method that returns an instance of the built-in will automatically return a derived class instance instead. So, if you have a derived class called `MyArray` that inherits from `Array`, methods such as `slice()` return an instance of `MyArray`. For example:

```js
class MyArray extends Array {
    // empty
}

let items = new MyArray(1, 2, 3, 4),
    subitems = items.slice(1, 3);

console.log(items instanceof MyArray);      // true
console.log(subitems instanceof MyArray);   // true
```

In this code, the `slice()` method returns a `MyArray` instance. The `slice()` method is inherited from `Array` and returns an instance of `Array` normally. Behind the scenes, it's the `Symbol.species` property that is making this change.

The `Symbol.species` well-known symbol is used to define a static accessor property that returns a function. That function is a constructor to use whenever an instance of the class must be created inside of an instance method (instead of using the constructor). The following builtin types have `Symbol.species` defined:

* `Array`
* `ArrayBuffer` (discussed in Chapter 10)
* `Map`
* `Promise`
* `RegExp`
* `Set`
* Typed Arrays (discussed in Chapter 10)

Each of these types has a default `Symbol.species` property that returns `this`, meaning the property will always return the constructor function. If you were to implement that functionality on a custom class, the code would look like this:

```js
// several builtin types use species similar to this
class MyClass {
    static get [Symbol.species]() {
        return this;
    }

    constructor(value) {
        this.value = value;
    }

    clone() {
        return new this.constructor[Symbol.species](this.value);
    }
}
```

In this example, the `Symbol.species` well-known symbol is used to assign a static accessor property to `MyClass`. Note that there's only a getter without a setter, because changing the species of a class isn't possible. Any call to `this.constructor[Symbol.species]` returns `MyClass`. The `clone()` method uses that definition to return a new instance rather than directly using `MyClass`, which allows derived classes to override that value. For example:

```js
class MyClass {
    static get [Symbol.species]() {
        return this;
    }

    constructor(value) {
        this.value = value;
    }

    clone() {
        return new this.constructor[Symbol.species](this.value);
    }
}

class MyDerivedClass1 extends MyClass {
    // empty
}

class MyDerivedClass2 extends MyClass {
    static get [Symbol.species]() {
        return MyClass;
    }
}

let instance1 = new MyDerivedClass1("foo"),
    clone1 = instance1.clone(),
    instance2 = new MyDerivedClass2("bar"),
    clone2 = instance2.clone();

console.log(clone1 instanceof MyClass);             // true
console.log(clone1 instanceof MyDerivedClass1);     // true
console.log(clone2 instanceof MyClass);             // true
console.log(clone2 instanceof MyDerivedClass2);     // false
```

Here, `MyDerivedClass1` inherits from `MyClass` and doesn't change the `Symbol.species` property. When `clone()` is called, it returns an instance of `MyDerivedClass1` because `this.constructor[Symbol.species]` returns `MyDerivedClass1`. The `MyDerivedClass2` class inherits from `MyClass` and overrides `Symbol.species` to return `MyClass`. When `clone()` is called on an instance of `MyDerivedClass2`, the return value is an instance of `MyClass`. Using `Symbol.species`, any derived class can determine what type of value should be returned when a method returns an instance.

For instance, `Array` uses `Symbol.species` to specify the class to use for methods that return an array. In a class derived from `Array`, you can determine the type of object returned from the inherited methods, such as:

```js
class MyArray extends Array {
    static get [Symbol.species]() {
        return Array;
    }
}

let items = new MyArray(1, 2, 3, 4),
    subitems = items.slice(1, 3);

console.log(items instanceof MyArray);      // true
console.log(subitems instanceof Array);     // true
console.log(subitems instanceof MyArray);   // false
```

This code overrides `Symbol.species` on `MyArray`, which inherits from `Array`. All of the inherited methods that return arrays will now use an instance of `Array` instead of `MyArray`.

In general, you should use the `Symbol.species` property whenever you might want to use `this.constructor` in a class method. Doing so allows derived classes to override the return type easily. Additionally, if you are creating derived classes from a class that has `Symbol.species` defined, be sure to use that value instead of the constructor.

## Using new.target in Class Constructors

In Chapter 3, you learned about `new.target` and how its value changes depending on how a function is called. You can also use `new.target` in class constructors to determine how the class is being invoked. In the simple case, `new.target` is equal to the constructor function for the class, as in this example:

```js
class Rectangle {
    constructor(length, width) {
        console.log(new.target === Rectangle);
        this.length = length;
        this.width = width;
    }
}

// new.target is Rectangle
var obj = new Rectangle(3, 4);      // outputs true
```

This code shows that `new.target` is equivalent to `Rectangle` when `new Rectangle(3, 4)` is called. Class constructors can't be called without `new`, so the `new.target` property is always defined inside of class constructors. But the value may not always be the same. Consider this code:

```js
class Rectangle {
    constructor(length, width) {
        console.log(new.target === Rectangle);
        this.length = length;
        this.width = width;
    }
}

class Square extends Rectangle {
    constructor(length) {
        super(length, length)
    }
}

// new.target is Square
var obj = new Square(3);      // outputs false
```

`Square` is calling the `Rectangle` constructor, so `new.target` is equal to `Square` when the `Rectangle` constructor is called. This is important because it gives each constructor the ability to alter its behavior based on how it's being called. For instance, you can create an abstract base class (one that can't be instantiated directly) by using `new.target` as follows:

```js
// abstract base class
class Shape {
    constructor() {
        if (new.target === Shape) {
            throw new Error("This class cannot be instantiated directly.")
        }
    }
}

class Rectangle extends Shape {
    constructor(length, width) {
        super();
        this.length = length;
        this.width = width;
    }
}

var x = new Shape();                // throws error

var y = new Rectangle(3, 4);        // no error
console.log(y instanceof Shape);    // true
```

In this example, the `Shape` class constructor throws an error whenever `new.target` is `Shape`, meaning that `new Shape()` always throws an error. However, you can still use `Shape` as a base class, which is what `Rectangle` does. The `super()` call executes the `Shape` constructor and `new.target` is equal to `Rectangle` so the constructor continues without error.

I> Since classes can't be called without `new`, the `new.target` property is never `undefined` inside of a class constructor.

## Summary

ECMAScript 6 classes make inheritance in JavaScript easier to use, so you don't need to throw away any existing understanding of inheritance you might have from other languages. ECMAScript 6 classes start out as syntactic sugar for the classical inheritance model of ECMAScript 5, but add a lot of features to reduce mistakes.

ECMAScript 6 classes work with prototypal inheritance by defining non-static methods on the class prototype, while static methods end up on the constructor itself. All methods are non-enumerable, a feature that better matches the behavior of built-in objects for which methods are typically non-enumerable by default. Additionally, class constructors can't be called without `new`, ensuring that you can't accidentally call a class as a function.

Class-based inheritance allows you to derive a class from another class, function, or expression. This ability means you can call a function to determine the correct base to inherit from, allowing you to use mixins and other different composition patterns to create a new class. Inheritance works in such a way that inheriting from built-in objects like `Array` is now possible and works as expected.

You can use `new.target` in class constructors to behave differently depending on how the class is called. The most common use is to create an abstract base class that throws an error when instantiated directly but still allows inheritance via other classes.

Overall, classes are an important addition to JavaScript. They provide a more concise syntax and better functionality for defining custom object types in a safe, consistent manner.
