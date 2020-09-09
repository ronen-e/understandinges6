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

### הסתרת מתודות במחלקה

המתודות שנמצאות במחלקות נגזרות תמיד מסתירות מתודות באותו שם על מחלקת הבסיס. למשל, ניתן להוסיף מתודה
<span dir="ltr">`getArea()`</span>
ישירות על מחלקה
`Square`
כדי להגדיר מחדש פונקציונליות זו:

<div dir="ltr">

```js
class Square extends Rectangle {
    constructor(length) {
        super(length, length);
    }

    // מסתיר את
    // Rectangle.prototype.getArea()
    getArea() {
        return this.length * this.length;
    }
}
```
</div>

מאחר והמתודה
<span dir="ltr">`getArea()`</span>
מוגדרת על
`Square`,
המתודה
<span dir="ltr">`Rectangle.prototype.getArea()`</span>
לא תיקרא עוד על ידי מופעים של 
`Square`.
כמובן שעדיין ניתן לקרוא למתודה של מחלקת הבסיס על ידי שימוש במתודה
<span dir="ltr">`super.getArea()`</span>
כמו בדוגמה הבאה:


<div dir="ltr">

```js
class Square extends Rectangle {
    constructor(length) {
        super(length, length);
    }

    // מסתיר את וקורא ישירות אל
    // Rectangle.prototype.getArea()
    getArea() {
        return super.getArea();
    }
}
```
</div>

שימוש ב 
`super`
בצורה זו עובד בדומה לשימוש ב
`super`
כפי שהוצג בפרק 4
(
    ראה
    "גישה קלה לפרוטוטיפ בעזרת 
    super"
).
ערכו של 
`this`
מותאם בצורה אוטומטית לערך הנכון וכך ניתן לבצע קריאה למתודה.

### הורשה של איברים סטטיים

אם על מחלקת בסיס קיימים איברים סטטיים, הם יהיו זמינים גם על המחלקה הנגזרת. כך עובדת ירושה בשפות אחרת אך זהו עניין חדש בג׳אווהסקריפט.
לדוגמה:

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

    static create(length, width) {
        return new Rectangle(length, width);
    }
}

class Square extends Rectangle {
    constructor(length) {

        // זהה ל
        // Rectangle.call(this, length, length)
        super(length, length);
    }
}

var rect = Square.create(3, 4);

console.log(rect instanceof Rectangle);     // true
console.log(rect.getArea());                // 12
console.log(rect instanceof Square);        // false
```

</div>

בקוד לעיל מוסיפים מתודה סטטית חדשה בשם
<span dir="ltr">`create()`</span>
למחלקה
`Rectangle`.
באמצעות ירושה, המתודה הופכת לנגישה באמצעות הקוד
<span dir="ltr">`Square.create()`</span>
ומתנהגת באותו אופן כמו המתודה
<span dir="ltr">`Rectangle.create()`</span>

### מחלקות נגזרות מביטויים

אחד מההיבטים המעניינים של מחלקות נגזרות באקמהסקריפט 6 הינו היכולת לייצר מחלקה מביטוי. ניתן להשתמש במילה
`extends`
עם כל ביטוי כל עוד תוצאתו היא פונקציה עם
תכונה פנימית
`[[Construct]]`
ועם פרוטוטיפ.
לדוגמה:

<div dir="ltr">

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
</div>

`Rectangle`
מוגדר כקונסטרקטור בסגנון אקמהסקריפט 5 בעוד ש
`Square`
היא מחלקה.
מכיוון של- 
`Rectangle`
יש
`[[Construct]]`
ופרוטוטיפ, המחלקה
`Square`
יכולה לרשת תכונות ישירות ממנו.

היכולת לקבל כל סוג ביטוי לאחר שימוש בסעיף
`extends`
מאפשר לנו שיטות עבודה כמו קביעה דינמית של הסוג ממנו יורשים. למשל:

<div dir="ltr">

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
</div>

הפונקציה
<span dir="ltr">`getBase()`</span>
נקראת ישירות כחלק מהגדרת המחלקה. היא מחזירה
`Rectangle`,
מה שהופך דוגמה זו למקבילה לדוגמה הקודמת. מכיוון שניתן לקבוע את זהות מחלקת הבסיס בצורה דינמית, ניתן להשתמש בטכניקות ירושה שונות. כך למשל ניתן ליצור מיקסינים
(mixins):

<div dir="ltr">

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
</div>

בדוגמה זו משתמשים במיקסינים במקום בירושה ממחלקות. הפונקציה
<span dir="ltr">`mixin()`</span>
לוקחת כל מספר של ארגומנטים שכל אחד מהם מייצג אוביקט מיקסין. 
הפונקציה מייצרת פונקציה בשם
`base`
ומבצעת השמה של תכונות כל אוביקט מיקסין לפרוטוטיפ.
הפונקציה מוחזרת על מנת שהמחלקה
`Square`
תוכל להשתמש ב-
`extends`.
שימו לב שמכיוון שמשתמשים ב-
`extends`
חובה לקרוא ל-
<span dir="ltr">`super()`</span>
בתוך הקונסטרקטור.

המופע של 
`Square`
יכול להשתמש במתודה
<span dir="ltr">`getBase()`</span>
שמוגדרת על
`AreaMixin`
ובמתודה
`serialize`
שמוגדרת על
`SerializableMixin`.
זה ממומש על ידי ירושה פרוטוטיפית.
הפונקציה
<span dir="ltr">`mixin()`</span>
מאכלסת באופן דינמי את הפרוטוטיפ של הפונקציה החדשה עם כל התכונות העצמיות בעבור כל מיקסין בנפרד
(
    במידה ולמספר מיקסינים יש את אותו שם תכונה רק התכונה האחרונה תיבחר
)

W> ניתן להשתמש בכל ביטוי לאחר
W> `extends`,
W> אך לא כל ביטוי יוצר מחלקה תקינה. בפרט, הביטויים הבאים גורמים לשגיאות:
W>
W> * `null`
W> * פונקציות מסוג גנרטור
W> (ראו פרק 8)
W> 
W> במקרים כאלו ניסיון ליצור מופע של המחלקה יזרוק שגיאה מכיוון שאין מתודה פנימית מסוג
W> `[[Construct]]`
W> שניתן לקרוא לה.

### ירושה מסוגים מובנים

החל מהרגע קיומם של מערכים בג׳אווהסקריפט, מפתחים רצו ליצור סוגי מערכים מיוחדים משלהם באמצעות הורשה. באקמהסקריפט 5 וקודם לכן, זה לא היה אפשרי. ניסיון להשתמש בהורשה קלאסית לא הוביל לקוד שעובד. לדוגמה:

<div dir="ltr">

```js
// התנהגות מובנית של מערך
var colors = [];
colors[0] = "red";
console.log(colors.length);         // 1

colors.length = 0;
console.log(colors[0]);             // undefined

// ניסיון לבצע הורשה ממערך בגרסת
// ES5

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

</div>

הפלט של הפונקציה
<span dir="ltr">`console.log()`</span>
בדוגמה לעיל מראה כיצד שימוש בסגנון הורשה קלאסית בג׳אווהסקריפט על מערך מוביל להתנהגות בלתי צפויה. התכונה
`length`
כמו גם התכונות הנומריות
על מופע
`MyArray`
לא מתנהגות באותו אופן כמו שהן מתנהגות עם מערכים מובנים של השפה מכיוון ומדובר בפונקציונליות שאינה מטופלת על ידי
<span dir="ltr">`Array.apply()`</span>
או על ידי השמה על הפרוטוטיפ.

תכלית אחת של מחלקות באקמהסקריפט 6 הינה לאפשר הורשה מכול האוביקטים המובנים. לשם כך, מודל ההורשה של מחלקות שונה במקצת ממודל ההורשה שנמצא באקמהסקריפט 5 וקודם לכן:

בהורשה קלאסית של מחלקות באקמהסקריפט 5 הערך עבור
`this`
מתקבל תחילה על ידי המחלקה הנגזרת
(
    למשל,
    `MyArray`
),
ובהמשך נקרא קונסטרקטור הבסיס
(
    למשל המתודה
    <span dir="ltr">`Array.apply()`</span>
).
המשמעות היא שהערך
`this`
מתחיל כמופע של
`MyArray`
ולאחר מכן מוסיפים עליו תכונות נוספות מתוך
`Array`.

בהורשה על בסיס מחלקות של אקמהסקריפט 6, הערך עבור
`this`
נקבע תחילה על ידי מחלקת הבסיס
(`Array`)
ואז משתנה על ידי הקונסטרקטור של המחלקה הנגזרת
(`MyArray`). 
התוצאה היא שהערך
`this`
מתחיל עם כל ההתנהגות המובנית של מחלקת הבסיס.

הדוגמה הבאה מראה מחלקה נגזרת של מערך בפעולה:

<div dir="ltr">

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

</div>

`MyArray`
יורש ישירות מן
`Array`. 
ולכן פועל כמו
`Array`. 
שינוי תכונות נומריות מעדכן את התכונה
`length`
ושינוי התכונה
`length`
מעדכן את התכונות הנומריות. המשמעות היא שניתן לבצע הורשה מן
`Array`
כדי ליצור מחלקת מערך נגזרת משלנו ולקיים הורשה מאוביקטים מובנים אחרים. בזכות היכולות החדשות שהתווספו, אקמהסקריפט 6 ומחלקות נגזרות נתנו מענה על הצורך המיוחד של הורשה מאוביקטים מובנים, אך מדובר בצורך שראוי לחקור.

### Symbol.species

היבט מעניין של הורשה מאוביקטים מובנים הוא שכל מתודה שמחזירה מופע של האוביקט המובנה תחזיר אוטומטית מופע של המחלקה הנגזרת. ולכן אם קיימת מחלקה נגזרת 
`MyArray`
שיורשת מן
`Array`
מתודות כמו
<span dir="ltr">`slice()`</span>
יחזירו מופע של
`MyArray`.
לדוגמה:

<div dir="ltr">

```js
class MyArray extends Array {
    // empty
}

let items = new MyArray(1, 2, 3, 4),
    subitems = items.slice(1, 3);

console.log(items instanceof MyArray);      // true
console.log(subitems instanceof MyArray);   // true
```

</div>

בקוד לעיל, המתודה
<span dir="ltr">`slice()`</span>
מחזירה מופע של 
`MyArray`.
המתודה
<span dir="ltr">`slice()`</span>
מתקבלת בהורשה מן
`Array`
ומחזירה מופע של 
`Array`
כמצופה.
הקונסטרקטור שבו ישתמשו ליצירת המופע נקרא מתוך התכונה
`Symbol.species`
ומאפשר לנו לבצע שינוי במידה ונרצה.

הסימבול המוכר
`Symbol.species`
משמש לצורך הגדרת פונקצית גישה סטאטית שמחזירה פונקציה. הפונקציה שחוזרת היא הקונסטרקטור שבו יש להשתמש בכל פעם שמופע של המחלקה נוצר בתוך מתודה של המופע
(כאשר לא משתמשים בקונסטרקטור).
לסוגים המובנים הבאים מוגדרת התכונה
`Symbol.species` :

* `Array`
* `ArrayBuffer` (ראו פרק 10)
* `Map`
* `Promise`
* `RegExp`
* `Set`
* Typed Arrays (ראו פרק 10)

לכל אחד מהסוגים הללו מוגדרת תכונה
`Symbol.species`
שמחזירה את הערך 
`this`,
משמע התכונה תמיד תחזיר את הקונסטרקטור עצמו. במידה ונרצה לממש התנהגות זו על מחלקה שאנו יצרנו הקוד ייראה כך:

<div dir="ltr">

```js
// קוד דומה קיים במספר אוביקטים מובנים
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
</div>

בדוגמה לעיל נעשה שימוש בסימבול המוכר בשם
`Symbol.species`
כדי לייצר תכונת גישה סטטית עבור
`MyClass`.
שימו לב לכך שישנו רק
getter
ואין 
setter
מכיוון ששינוי סוג עבור מחלקה אינו אפשרי. כל קריאה אל
<span dir="ltr">`this.constructor[Symbol.species]`</span>
מחזירה את
`MyClass`.
המתודה
<span dir="ltr">`clone()`</span>
משתמשת באותו ערך כדי להחזיר מופע חדש במקום להשתמש ישירות במחלקה
`MyClass`,
וזה מה שמאפשר למחלקות נגזרות לעקוף את אותו ערך. לדוגמה:


<div dir="ltr">

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
    // מחלקה ריקה
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
</div>

בדוגמת הקוד לעיל המחלקה
`MyDerivedClass1`
יורשת מן המחלקה
`MyClass`
ואינה משנה את ערך התכונה
`Symbol.species`.
כאשר המתודה
<span dir="ltr">`clone()`</span>
נקראת, מתקבל מופע של 
`MyDerivedClass1`
מאחר ו-
<span dir="ltr">`this.constructor[Symbol.species]`</span>
מחזיר את
`MyDerivedClass1`.
המחלקה
`MyDerivedClass2`
יורשת מן המחלקה
`MyClass`
ודורסת את התכונה
`Symbol.species`
כך שתחזיר את 
`MyClass`.
כאשר המתודה 
<span dir="ltr">`clone()`</span>
נקראת עבור מופע של 
`MyDerivedClass2`
הערך המוחזר הינו מופע של 
`MyClass`.
על ידי שימוש בסימבול
`Symbol.species`,
כל מחלקה נגזרת יכולה לקבוע את סוג הערך שמוחזר כאשר מתודה מחזירה מופע.

כך למשל הסוג המובנה
`Array`
משתמש בתכונה
`Symbol.species`
על מנת להגדיר את המחלקה בה יש להשתמש כאשר מפעילים מתודות שמחזירות מערך. במחלקה נגזרת מן
`Array`,
ניתן לקבוע את סוג האוביקט המוחזר מהשיטות שהתקבלו בירושה. כמו למשל:


<div dir="ltr">

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

</div>

הקוד בדוגמה האחרונה דורס את 
`Symbol.species`
בעבור
`MyArray`,
שיורש תכונות מן
`Array`.
כל התכונות המורשות שמחזירות מערך יחזירו כעת מופע של 
`Array`
במקום מופע של 
`MyArray`.

כעקרון, נרצה להשתמש בתכונה
`Symbol.species`
כל פעם שנרצה להשתמש בערך
`this.constructor`
בתוך מתודה של מחלקה. זה יאפשר למחלקות נגזרות לעקוף את הערך המוחזר בקלות. בנוסף, אם ניצור מחלקות נגזרות ממחלקה שמוגדרת עבורה התכונה
`Symbol.species`
עלינו לוודא שאנו אכן משתמשים באותו ערך ולא משתמשים בקונסטרקטור באופן ישיר.

## שימוש ב new.target בתוך קונסטרקטור

בפרק 3 למדנו על
`new.target`
וכיצד הערך משתנה בהתאם לצורת הקריאה של הפונקציה. ניתן להשתמש בערך
`new.target`
בקונסטרקטור של מחלקה על מנת לקבוע כיצד קוראים למחלקה. במקרה הפשוט הערך
`new.target`
מצביע על קונסטרקטור המחלקה. כמו בדוגמה זו:

<div dir="ltr">

```js
class Rectangle {
    constructor(length, width) {
        console.log(new.target === Rectangle);
        this.length = length;
        this.width = width;
    }
}

// new.target -> Rectangle
var obj = new Rectangle(3, 4);      // true
```

</div>

בדוגמת הקוד האחרונה הערך
`new.target` 
מצביע על 
`Rectangle`
כאשר קוראים למחלקה על ידי הקוד
<span dir="ltr">`new Rectangle(3, 4)`</span>.
מכיוון שלא ניתן להריץ קונסטרקטור של מחלקה ללא האופרטור
`new`,
התכונה
`new.target` 
תמיד מוגדרת בתוך קונסטרקטור של מחלקה. אך הערך לא תמיד יהיה אותו הערך. לדוגמה:

<div dir="ltr">

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

// new.target -> Square
var obj = new Square(3);      // false
```

</div>

`Square`
קורא לקונסטרקטור של
`Rectangle`
ולכן 
`new.target` 
מצביע על
`Square`
כאשר קוראים לקונסטרקטור של המחלקה
`Rectangle`.
מדובר בהיבט חשוב מכיוון שכך מתאפשר לכל קונסטרקטור לשנות את התנהגותו לפי הדרך בה הוא נקרא. כך למשל, ניתן ליצור מחלקת בסיס אבסטרקטית
(כזו שלא מפעילים באופן ישיר)
על ידי שימוש בערך
`new.target` 
כמו בדוגמה הבאה:

<div dir="ltr">

```js
// מחלקת בסיס אבסטרקטית
class Shape {
    constructor() {
        if (new.target === Shape) {
            throw new Error("לא ניתן ליצור מופע ישיר של מחלקה זו")
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

var x = new Shape();                // שגיאה

var y = new Rectangle(3, 4);        // אין שגיאה
console.log(y instanceof Shape);    // true
```

</div>

בדוגמה האחרונה, קונסטרקטור המחלקה
`Shape`
זורק שגיאה כאשר
`new.target`
מצביע על 
`Shape`,
כלומר, הפקודה
`new Shape()`
<span dir="ltr">`new Shape()`</span>.
תמיד תזרוק שגיאה. אך ניתן להשתמש במחלקה
`Shape`
בתור מחלקת בסיס, וזה אכן מה שהמחלקה
`Rectangle` 
עושה. הקריאה 
<span dir="ltr">`super()`</span>
מפעילה את הקונסטרקטור של 
`Shape`
והערך 
`new.target`
מצביע על 
`Rectangle` 
וכל הקונסטרקטור ממשיך לרוץ ללא שגיאה.

I> מאחר וחייבים לקרוא למחלקה באמצעות האופרטור
`new`,
התכונה
`new.target`
לעולם לא תקבל את הערך 
`undefined`
בתוך קונסטרקטור של מחלקה

## סיכום

מחלקות באקמהסקריפט 6 מקלות על שימוש בירושה בג׳אווהסקריפט, כך שאין צורך לשכוח הבנה קודמת של הורשה שאולי הגיעה משפות תכנות אחרות. מחלקות באקמהסקריפט 6 מתחילות בתור עטיפה נוחה
(syntactic sugar)
למודל הירושה במחלקות של אקמהסקריפט 5, וכמו כן מוסיף יכולות נוספות על מנת לצמצם טעויות..

מחלקות באקמהסקריפט 6 מממשות הורשה פרוטוטיפית על ידי הגדרת מתודה לא סטטיות על פרוטוטיפ המחלקה, בעוד שמתודות סטטיות מוגדרות על הקונסטרקטור עצמו. כל המתודות אינן אינומרביליות, התנהגות שתואמת בצורת טובה יותר את ההתנהגות הקיימת של אוביקטים מובנים שעבורם מתודות בדרך כלל אינן אינומרביליות כברירת מחדל. בנוסף, קונסטרקטור של מחלקה לא יכולים להיקרא ללא האופרטור
`new`,
מה שמבטיח שלא קוראים למחלקה כמו לפונקציה רגילה.

הורשה ממחלקות מאפשרת לנו ליצור מחלקה שיורשת ממחלקה אחרת, מפונקציה, ואף מביטוי. יכולת זו משמעותה שניתן לקרוא לפונקציה כדי לקבוע את מחלקת הבסיס שממנה נירש, וכך ביכולתנו להשתמש במיקסינים וטכניקות קומפוזיציה שונות כדי ליצור מחלקה חדשה. הורשה עובדת בצורה כזו שהורשה מאוביקטים מובנים כמו
`Array`
אפשרית ופועלת כמצופה.

ניתן להשתמש בערך
`new.target`
בתוך קונסטרקטור של מחלקה כדי להגדיר התנהגויות שונות בהתאם לאופן בה קראנו למחלקה. השימוש הנפוץ ביותר הינו יצירת מחלקת בסיס אבסטרקטית שזורקת שגיאה כאשר מנסים לקרוא לה ישירות אך עדיין מאפשרת הורשה דרך מחלקות אחרות.

ככלל, מחלקות הינן תוספת חשובה בג׳אווהסקריפט. הן מאפשרות כתיבה ברורה ומקוצרת ויכולת משופרת להגדרת סוגי אוביקטים מותאמים.

</div>
