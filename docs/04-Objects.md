

# הרחבת פונקציונליות של אוביקטים

ECMAScript 6
שמה דגש על ייעול השימוש באוביקטים. יש בכך הגיון רב מאחר וכמעט כל ערך בג׳אווהסקריפט הינו סוג מסוים של אוביקט. בנוסף, מספר האוביקטים אשר מופיע בתוכנת ג׳אווהסקריפט ממוצעת ממשיך לגדול ככל שמורכבותן של אפליקציות ג׳אווהסקריפט גדלה. משמעות הדבר היא שתוכנות קיימות מייצרות אוביקטים נוספים כל הזמן. בד בבד עם הוספת אוביקטים מגיע הצורך להשתמש בהם באופן יעיל יותר.

ECMAScript 6
משפרת את השימוש באוביקטים במספר דרכים, החל משינויי תחביר פשוטים ועד לדרכים חדשות לשנות אותם.

## קטגוריות של אוביקטים

ג׳אווהסקריפט משתמשת בערב רב של טרמינולוגיה על מנת לתאר אוביקטים אשר מופיעים בשפה, בניגוד לאלו שמתווספים לסביבת ההרצה כמו שיש בדפדפן או ב
Node.js,
האפיון עבור
ECMAScript 6
נותן הגדרות מדויקות עבור כל קטגוריה של אוביקטים.
חשוב מאוד להבין את הטרמינולוגיה הזו על מנת להשיג הבנה טובה של השפה עצמה.
ואלו הן קטגוריות האוביקטים:

* *לאוביקטים רגילים*
יש להם את כל ההתנהגויות הפנימיות הרגילות עבור אוביקטים בג׳אווהסקריפט.
* *לאוביקטים אקזוטיים*
יש להם התנהגות פנימית ששונה במידת מה מהתנהגות הרגילה
* *אוביקטים סטנדרטים*
הם אותם אוביקטים אשר מוגדרים על ידי
ECMAScript 6
כמו
<span dir="ltr">`Array`, `Date`<span>,
וכדומה.
אוביקט סטנדרטי ייתכן שיהיה  רגיל או אקזוטי.
* *אוביקטים מובנים*
נוכחים בסביבת הריצה של ג׳אווהסקריפט כאשר סקריפט כלשהו מתחיל לרוץ.
כל האוביקטים הסטנדרטים הינם אוביקטים מובנים.

במונחים אלו ייעשה שימוש נרחב בהמשך הספר על מנת להרחיב על האוביקטים השונים שמוגדרים תחת
ECMAScript 6.

## שינויי תחביר עבור אוביקט

אוביקט ליטראל
(object literal)
היא אחת משיטות הכתיבה הנפוצות בג׳אווהסקריפט.
התחביר עבור
JSON
נבנה על פי אותו תחביר, וניתן למצוא אותו כמעט בכל קובץ ג׳אווהסקריפט באינטרנט.
אוביקט ליטראל פופולרי כיוון ומדובר דרך קלה וקצרה ליצירת אוביקטים שאחרת היו נכתבים עם מספר שורות קוד.
ECMAScript 6
מרחיבה את התחביר הנוכחי במספר צורות.

### אתחול תכונות

בגרסת
ECMAScript 5
ועוד קודם לכן, אוביקט ליטראל היה אוסף של זוגות
שמות-ערכים. המשמעות הייתה שתיתכן כפילות בקוד בעת אתחול תכונות.
לדוגמה:



```js
function createPerson(name, age) {
    return {
        name: name,
        age: age
    };
}
```


הפונקציה
<span dir="ltr">`createPerson()`</span>
מייצרת אוביקט ששמות תכונותיו זהים לשמות הפרמטרים של הפונקציה.
ניתן לראות כפילות עבור  התכונות
`name`
ו
`age`,
למרות שהמופע הראשון הינו שם התכונה והשני הינו ערכה.
המזהה
`name`
באוביקט שנוצר מקבל את הערך של המשתנה בעל השם
`name`,
והמזהה
`age`
מקבל את הערך של המשתנה בעל השם
`age`.

ב
ECMAScript 6,
ניתן לייתר את הכפילות עבור שמות תכונות ושמות משתנים בעזרת שימוש בתחביר מקוצר הקרוי בשם
*מאתחל התכונות*
(*property initializer*).
כאשר שם התכונה זהה לשם המשתנה הלוקאלי, ניתן לכתוב את השם ללא סימון נקודותיים וערך.
לדוגמה, את הפונקציה
<span dir="ltr">`createPerson()`</span>
ניתן לכתוב מחדש כמו בדוגמה הבאה:



```js
function createPerson(name, age) {
    return {
        name,
        age
    };
}
```


כאשר תכונה של אוביקט ליטראל מכילה רק מפתח שם, מנוע ג׳אווהסקריפט מחפש בסביבתו משתנה בעל אותו שם.
במידה ונמצא משתנה כזה, ערכו מושם לתכונה בעלת אותו שם בתוך האוביקט.
בדוגמה לעיל, התכונה
`name`
באוביקט שנוצר מקבלת את ערכו של המשתנה
`name`.

הרחבה זו של השפה מאפשרת כתיבת אוביקט ליטראל בצורה יותר מתומצתת ומפחיתה שגיאות במתן שמות. השמת ערך משתנה לתכונה בעלת אותו שם היא טכניקה נפוצה, ומכאן שימושיות ההרחבה התחבירית החדשה.

### מתודות מקוצרות

ECMAScript 6
משפר את צורת הכתיבה עבור יצירת מתודות באוביקט ליטראל.
בגרסת
ECMAScript 5
ולפניה, היה חובה לפרט שם והגדרת פונקציה מלאה כדי להוסיף מתודה לאוביקט.
לדוגמה:



```js
var person = {
    name: "ניקולאס",
    sayName: function() {
        console.log(this.name);
    }
};
```



בגרסת
ECMAScript 6,
התחביר קוצר על ידי סילוק סימון נקודותיים והמילה
`function`.
כעת ניתן לכתוב את הדוגמה הקודמת כך:



```js
var person = {
    name: "ניקולאס",
    sayName() {
        console.log(this.name);
    }
};
```



תחביר מקוצר זה, שנקרא תחביר
*מתודה מקוצרת*
(*concise method*),
מייצר מתודה על אוביקט
`person`
כמו בדוגמה הקודמת.
התכונה
<span dir="ltr">`sayName()`</span>
מקבלת פונקציה בתור ערך, ויש לה את כל המאפיינים כמו הפונקציה
<span dir="ltr">`sayName()`</span>
בגרסת
ECMAScript 5.
ההבדל היחיד הוא שמתודה מקוצרת יכולה להשתמש במזהה
`super`
(
אשר יורחב עליו בהמשך בחלק ״גישה קלה לפרוטוטייפ באמצעות
super"
)

!> ערכה של תכונת
`name`
של מתודה שנוצרה באמצעות תחביר מתודה מקוצרת הוא השם שמופיע לפני הסוגריים. בדוגמה האחרונה, ערך תכונת
`name`
עבור
<span dir="ltr">`person.sayName()`</span>
הוא
`"sayName"`

### שמות תכונה מחושבים

בגרסת
ECMAScript 5
וקודם לכן היה ניתן לחשב שם תכונה של אוביקט כאשר אותן תכונות היו מוגדרות על ידי סוגריים מרובעים ולא על ידי נקודה
(dot notation).
הסוגריים המרובעים מאפשרים לנו לתת שם תכונה באמצעות משתנים או מחרוזות שמכילות ערכים שהיו גורמים לשגיאת תחביר לו היו משמשים כמזהה רגיל לשם התכונה.
להלן דוגמה שממחישה זאת:



```js
var person = {},
    lastName = "last name";

person["first name"] = "ניקולאס";
person[lastName] = "זאקאס";

console.log(person["first name"]);      // "ניקולאס"
console.log(person[lastName]);          // "זאקאס"
```



מכיוון והמשתנה
`lastName`
מקבל את הערך
`"last name"`,
שני שמות התכונות בדוגמה לעיל מכילים רווח,
ולכן לא ניתן לקרוא להם על ידי שימוש בנקודה.
ואולם, שימוש בסוגריים מאפשר לכל ערך מחרוזת לשמש בתור שם תכונה, ובדוגמה לעיל  הדבר מאפשר את השמת הערך
`"ניקולאס"`
לתכונה בשם
`"first name"`
והשמת הערך
`"זאקאס"`
לתכונה בשם
`"last name"`

כמו כן, ניתן להשתמש במחרוזת עבור שם תכונה בתוך אוביקט.
לדוגמה:



```js
var person = {
    "first name": "ניקולאס"
};

console.log(person["first name"]);      // "ניקולאס"
```



טכניקה זו עובדת היטב עבור שמות תכונות שידועים מראש וניתן לייצגם ע״י מחרוזת. אך, אילו התכונה
`"first name"`
הייתה שמורה בתוך משתנה
(כמו בדוגמה הקודמת)
או אם היה צורך לחשבה, אזי כלל לא הייתה קיימת דרך להגדירה בגרסת
ECMAScript 5.

בגרסת
ECMAScript 6,
שמות תכונות מחושבים הינם חלק מתחביר השפה, והם משתמשים בסוגריים מרובעים.
לדוגמה:



```js
var lastName = "last name";

var person = {
    "first name": "ניקולאס",
    [lastName]: "זאקאס"
};

console.log(person["first name"]);      // "ניקולאס"
console.log(person[lastName]);          // "זאקאס"
```



הסוגריים המרובעים בתוך אוביקט ליטראל מצביעים על היותה של התכונה בעלת ערך מחושב, כך שערכה מחושב כמחרוזת.
מכאן ניתן לכלול גם ביטויים
(expressions)
כמו בדוגמה הבאה:



```js
var suffix = " name";

var person = {
    ["first" + suffix]: "ניקולאס",
    ["last" + suffix]: "זאקאס"
};

console.log(person["first name"]);      // "ניקולאס"
console.log(person["last name"]);       // "זאקאס"
```



התכונות בדוגמה מקבלות את הערכים
`"first name"`
ו-
`"last name"`,
ואותן מחרוזות יכולות לשמש על מנת להצביע על התכונות בהמשך.
כל ערך שניתן להכניס בתוך סוגריים מרובעים מתאים גם לשמות תכונה מחושבים בתוך אוביקט ליטראל.

## מתודות חדשות

אחד מיעדי האפיון של
ECMAScript
החל מגרסת
ECMAScript 5
היה הימנעות מהוספת פונקציות גלובליות או הוספת מתודות ל -
`Object.prototype`,
ובמקום זאת למצוא אוביקטים עליהם ניתן יהיה להוסיף מתודות חדשות. כתוצאה מכך, אוביקט
`Object`
הגלובלי הוסיף אליו עוד מתודות כאשר אוביקטים אחרים מתאימים לא נמצאו.
ECMAScript 6
הוסיפה מספר מתודות חדשות על אוביקט
`Object`
הגלובלי שנועדו לפשט ביצוע משימות מסוימות.

### מתודת Object.is
כאשר משווים שני ערכים בג׳אווהסקריפט משתמשים בד״כ באופרטור
(`==`)
או לצורך השוואה עמוקה יותר - בסימן
(`===`).
מפתחים רבים מעדיפים את האחרון, כדי להימנע מהשוואה כפויה בין סוגים שונים
(type coercion).
גם השוואה עמוקה אינה מדויקת בכל המקרים.
כך למשל הערכים
+0
ו
-0
נחשבים לזהים בעת שימוש באופרטור
`===`,
אף על פי שהם מיוצגים בצורה שונה בתוך מנוע ריצה של ג׳אווהסקריפט.
כמו כן, ההשוואה
`NaN === NaN`
מחזירה את הערך
`false`,
ומכאן הצורך להשתמש בפונקציה הגלובלית
<span dir="ltr">`isNaN()`</span>
על מנת לזהות את הערך
`NaN`.

ECMAScript 6
הוסיפה את המתודה
<span dir="ltr">`Object.is()`</span>
כדי לתת מענה למוזרויות השונות של סימן ההשוואה העמוקה.
המתודה מקבלת שני ארגומנטים ומחזירה את הערך
`true`
במידה והערכים להשוואה זהים.
שני ערכים נחשבים זהים כאשר הם מאותו סוג ומאותו ערך.
להלן מספר דוגמאות:



```js
console.log(+0 == -0);              // true
console.log(+0 === -0);             // true
console.log(Object.is(+0, -0));     // false

console.log(NaN == NaN);            // false
console.log(NaN === NaN);           // false
console.log(Object.is(NaN, NaN));   // true

console.log(5 == 5);                // true
console.log(5 == "5");              // true
console.log(5 === 5);               // true
console.log(5 === "5");             // false
console.log(Object.is(5, 5));       // true
console.log(Object.is(5, "5"));     // false
```


לרוב, המתודה
<span dir="ltr">`Object.is()`</span>
תעבוד באופן זהה לאופרטור
`===`.
ההבדלים היחידים הם שהערכים
+0
ו
-0
נחשבים שונים והערך
`NaN`
זהה ל
`NaN`.
אך אין זה אומר שיש להימנע משימוש באופרטורי השוואה.
הבחירה בין השימוש בשיטת
<span dir="ltr">`Object.is()`</span>
במקום אופרטור
`==`
או אופרטור
`===`
תלויה באותם מקרים המושפעים בשוני בין השיטות ובדרך בה הוא משפיע על הקוד שלך.

### מתודת Object.assign
טכניקת
*מיקסין*
(*Mixins*)
הינה שיטה נפוצה לבניית אוביקטים בג׳אווהסקריפט.
בעזרת מיקסין, אוביקט אחד מקבל תכונות ומתודות
(properties and methods)
מאוביקט אחר.
ספריות רבות משתמשות בטכניקה דומה לזו שבדוגמה הבאה:



```js
function mixin(receiver, supplier) {
    Object.keys(supplier).forEach(function(key) {
        receiver[key] = supplier[key];
    });

    return receiver;
}
```


הפונקציה
<span dir="ltr">`mixin()`</span>
עוברת על התכונות של
`supplier`
ומעתיקה אותם אל
`receiver`
(מה שנקרא ״עותק שטחי״, שבו תכונות שערכיהם הינם אוביקטים משותפים).
בצורה זו מתאפשר ל
`receiver`
לקבל תכונות חדשות ללא שימוש בהורשה, כמו בדוגמה הבאה:



```js
function EventTarget() { /*...*/ }
EventTarget.prototype = {
    constructor: EventTarget,
    emit: function() { /*...*/ },
    on: function() { /*...*/ }
};

var myObject = {};
mixin(myObject, EventTarget.prototype);

myObject.emit("somethingChanged");
```



בדוגמה לעיל, האוביקט
`myObject`
מעתיק התנהגות מתוך האוביקט
`EventTarget.prototype`.
האוביקט
`myObject`
יכול כעת לשדר אירועים ולהירשם אליהם באמצעות שימוש במתודות
<span dir="ltr">`emit()`</span>
ו
<span dir="ltr">`on()`</span>
בהתאמה.

טכניקה זו הייתה כה נפוצה שבגרסת
ECMAScript 6
הוסיפו את מתודת
<span dir="ltr">`Object.assign()`</span>,
שמתנהגת באותה הצורה, ומקבלת כארגומנטים אוביקט ״מקבל״ ואז אוסף של ״ספקים״ ומחזיר את המקבל.
שינוי השם
<span dir="ltr">`assign()`</span>,
במקום
<span dir="ltr">`mixin()`</span>,
נועד לשקף את הפעולה שמתבצעת.
מאחר והפונקציה
<span dir="ltr">`mixin()`</span>,
משתמשת באופרטור ההשמה
(`=`),
היא לא יכולה להעתיק תכונות מסוג תכונות גישה
(accessor properties, e.g `getters`)
למקבל בתור תכונות גישה. אלא רק בתור הערך שלהן.
השם
<span dir="ltr">`Object.assign()`</span>,
נועד לשקף את ההבחנה בין המצבים.

!> מתודות דומות בספריות שונות משתמשות בשמות שונים עבור אותה התנהגות בסיסית. אלטרנטיבות נפוצות כוללות את
המתודות
<span dir="ltr">`extend()`</span>
ו-
<span dir="ltr">`mix()`</span>.
לזמן קצר התקיימה גם מתודה בשם
<span dir="ltr">`Object.mixin()`</span>,
עבור גרסת
ECMAScript 6
בנוסף למתודה
<span dir="ltr">`Object.assign()`</span>.
ההבדל העיקרי היה ש
<span dir="ltr">`Object.mixin()`</span>
העתיקה תכונות מסוג תכונות גישה,
אך המתודה סולקה עקב דאגות הקשורות לשימוש במזהה
`super`
(
אשר יורחב עליו בהמשך בחלק על
״גישה קלה לפרוטוטייפ באמצעות
super"
בפרק זה
)

ניתן להשתמש ב
<span dir="ltr">`Object.assign()`</span>.
בכל מקום שבו ניתן היה להשתמש בפונקציה
<span dir="ltr">`mixin()`</span>.
לדוגמה:



```js
function EventTarget() { /*...*/ }
EventTarget.prototype = {
    constructor: EventTarget,
    emit: function() { /*...*/ },
    on: function() { /*...*/ }
}

var myObject = {}
Object.assign(myObject, EventTarget.prototype);

myObject.emit("somethingChanged");
```


המתודה
<span dir="ltr">`Object.assign()`</span>
מקבלת כל מספר של ״ספקים״ ו״המקבל״ מעתיק את התכונות החדש לפי הסדר בו הופיעו הספקים.
ומכאן, ספק שמופיע בהמשך יכול לדרוס ערך שהגיע מספק מוקדם יותר ברשימה,
כפי שקורה בדוגמה הבאה:



```js
var receiver = {};

Object.assign(receiver,
    {
        type: "js",
        name: "file.js"
    },
    {
        type: "css"
    }
);

console.log(receiver.type);     // "css"
console.log(receiver.name);     // "file.js"
```


הערך של
`receiver.type`
הוא
`"css"`
מאחר והספק השני דרס את ערכו של הספק הראשון

התוספת של
<span dir="ltr">`Object.assign()`</span>
אינה תוספת גדולה עבור
ECMAScript 6,
אך היא הופכת פונקציה נפוצה בספריות לפונקציה רשמית של השפה.

### עבודה עם תכונות גישה

חשוב לזכור ש
<span dir="ltr">`Object.assign()`</span>
לא יוצרת תכונות גישה על המקבל כאשר הן מופיעות בספק.
מכיוון ש
<span dir="ltr">`Object.assign()`</span>
משתמשת באופרטור ההשמה, תכונת גישה בספק תהפוך לתכונת ערך במקבל.
לדוגמה:



```js
var receiver = {},
    supplier = {
        get name() {
            return "file.js"
        }
    };

Object.assign(receiver, supplier);

var descriptor = Object.getOwnPropertyDescriptor(receiver, "name");

console.log(descriptor.value);      // "file.js"
console.log(descriptor.get);        // undefined
```


לאוביקט
`supplier`
יש תכונת גישה בשם
`name`.
לאחר הפעלת המתודה
<span dir="ltr">`Object.assign()`</span>,
`receiver.name`
קיים בתור תכונה עם הערך
`"file.js"`
מכיוון ש
`supplier.name`
החזיר
`"file.js"`
לאחר הקריאה למתודה
<span dir="ltr">`Object.assign()`</span>

## כפילות בשמות תכונות

מצב קשיח
בגרסת
ECMAScript 5
הכיל בתוכו בדיקה עבור תכונות בעל שם זהה שהייתה זורקת שגיאה בהימצא כפילות.
הקוד בדוגמה הבאה נחשב לקוד בעייתי:



```js
"use strict";

var person = {
    name: "ניקולאס",
    // שגיאה במצב קשיח תחת גרסה 5
    name: "גרג"
};
```


כאשר הקוד למעלה רץ במצב קשיח בגרסת
ECMAScript 5,
תכונת
`name`
השנייה גורמת לשגיאת תחביר
(syntax error).
אבל תחת גרסה
ECMAScript 6,
הבדיקה לגילוי כפילויות סולקה.
גם הגרסה הרגילה וגם הגרסה הקשיחה לא בודקות כפילות בשם התכונה.
במקום זאת, התכונה האחרונה עבור אותו שם הופכת לערך הסופי של התכונה.
כלומר - ״דורסת״ את התכונות לפניה בעלות אותו שם.
כפי שמודגם בקוד הבא



```js
"use strict";

var person = {
    name: "ניקולאס",
    // אין שגיאה במצב קשיח בגרסה 6
    name: "גרג"
};

console.log(person.name);       // "גרג"
```


בדוגמה לעיל, ערכו של
`person.name`
הוא
`"גרג"`
מאחר וזהו הערך האחרון עבור אותה תכונה.

## סדר ספירה של תכונות עצמיות

ECMAScript 5
לא הגדירה את סדר הספירה
(enumeration order)
עבור תכונות של אוביקט, והשאירה זאת לשיקול דעתו של יצרן מנוע הריצה של ג׳אווהסקריפט.
לעומת זאת
ECMAScript 6
מגדירה באופן ברור את הסדר לפיו תכונות מוחזרות בעת ספירתן. הדבר משפיע על האופן בו תכונות מוחזרות בעת שימוש במתודה
<span dir="ltr">`Object.getOwnPropertyNames()`</span>
ובמתודה
<span dir="ltr">`Reflect.ownKeys()`</span>
(עליו יורחב בפרק 12).
הדבר משפיע על הסדר לפיו תכונות מטופלות בעת קריאה למתודה
<span dir="ltr">`Object.assign()`</span>.

סדר הספירה עבור תכונות עצמיות
(own properties)
הוא:

1. כל המפתחות הנומריים בסדר עולה
2. כל המפתחות מסוג מחרוזת (סטרינג) בסדר לפיו הוסיפו אותם לאוביקט
3. כל המפתחות מסוג סמל (סימבול) בסדר לפיו הוסיפו אותם לאוביקט

להלן דוגמה:



```js
var obj = {
    a: 1,
    0: 1,
    c: 1,
    2: 1,
    b: 1,
    1: 1
};

obj.d = 1;

console.log(Object.getOwnPropertyNames(obj).join(""));     // "012acbd"
```


המתודה
<span dir="ltr">`Object.getOwnPropertyNames()`</span>
מחזירה את התכונות של
`obj`
לפי הסדר הבא:
`0`, `1`, `2`, `a`, `c`, `b`, `d`.
שימו לב שהמפתחות הנומריים מקובצים יחד וממוינים, אף על פי שבעת כתיבת האוביקט ליטראל הם אינם באותו סדר.
המפתחות מסוג מחרוזת מופיעים לאחר המפתחות הנומריים ובסדר שבו נכתבו.
המפתחות שהוגדרו בעת הגדרת האוביקט באים קודם, ואחריהם יופיעו מפתחות דינמיים שנכתבו מאוחר יותר
(
במקרה זה המפתח
`d`
).

W> עבור לולאת
`for-in`
עדיין קיים סדר ספירה לא מוגדר
מכיוון שלא כל מנועי הריצה של ג׳אווהסקריפט מפעילים אותה באופן זהה.
המתודות
<span dir="ltr">`Object.keys()`</span>
ו
<span dir="ltr">`JSON.stringify()`</span>
עובדות גם הן כמו לולאת
`for-in`
בסדר ספירה לא מוגדר.

בעוד שסדר ספירה מוגדר מהווה שינוי יחסית קל בשפה, אין זה נדיר למצוא תוכנות אשר מסתמכות על סדר ספירה מסוים בעת פעולתן.
ECMAScript 6,
על ידי הגדרת סדר הספירה באופן ברור, מבטיח שקוד אשר מסתמך על סדר ספירה מסוים יעבוד בצורה תקינה.

## פרוטוטיפ משופר

פרוטוטיפים
(Prototypes)
מהווים את הבסיס להורשה בג׳אווהסקריפט, וגרסת
ECMAScript 6
הוסיפה לו רבות.
גרסאות מוקדמות של ג׳אווהסקריפט הגבילו בצורה מהותית את היכולת לעבוד איתו.
אך ככל שהשפה התבגרה ויותר מפתחים למדו להכיר את התנהגותו, התברר שמפתחים רצו שליטה מוגברת על פרוטוטיפים ודרכים קלות יותר לעבוד איתם. כתוצאה מכך
ECMAScript 6
הוסיפה מספר שיפורים לפרוטוטיפ

### שינוי פרוטוטיפ עבור אוביקט

בדרך כלל, הפרוטוטיפ של אוביקט נקבע בעת יצירתו, או באמצעות בנאי (קונסטרקטור) או בעזרת  המתודה
<span dir="ltr">`Object.create()`</span>.
ההנחה שפרוטוטיפ של אוביקט אינו משתנה לאחר יצירתו הייתה מן ההנחות הגדולות ביותר של ג׳אווהסקריפט לפני
ECMAScript 6.
ECMAScript 5
אכן הוסיפה את המתודה
<span dir="ltr">`Object.getPrototypeOf()`</span>
שהחזירה את הפרוטוטיפ של אוביקט נתון, אך לא הייתה קיימת דרך לשנות פרוטוטיפ של אוביקט לאחר יצירתו.

ECMAScript 6
משנה את השפה על ידי הוספת המתודה
<span dir="ltr">`Object.setPrototypeOf()`</span>,
שמאפשרת לנו לשנות את הפרוטוטיפ של כל אוביקט.
המתודה
<span dir="ltr">`Object.setPrototypeOf()`</span>,
מקבלת שני ארגומנטים:
האוביקט שעבורו יוגדר הפרוטוטיפ והאוביקט שיוגדר כפרוטוטיפ עבור הנ״ל.
לדוגמה:



```js
let person = {
    getGreeting() {
        return "שלום";
    }
};

let dog = {
    getGreeting() {
        return "הב הב";
    }
};

// הפרוטוטיפ הינו האוביקט בשם
// person
let friend = Object.create(person);
console.log(friend.getGreeting());                      // "שלום"
console.log(Object.getPrototypeOf(friend) === person);  // true

// שינוי הפרוטוטיפ לאוביקט בשם
// dog
Object.setPrototypeOf(friend, dog);
console.log(friend.getGreeting());                      // "הב הב"
console.log(Object.getPrototypeOf(friend) === dog);     // true
```


הקוד בדוגמה מגדיר שני אוביקטים בסיסיים:
`person`
ו
`dog`.
לשני האוביקטים מתודה בשם
<span dir="ltr">`getGreeting()`</span>
שמחזירה מחרוזת. האוביקט
`friend`
יורש תחילה מהאוביקט
`person`,
כלומר - המתודה
<span dir="ltr">`getGreeting()`</span>
תדפיס
`"שלום"`.
כאשר הפרוטוטיפ משתנה לאוביקט
`dog`
אזי המתודה
<span dir="ltr">`friend.getGreeting()`</span>
תדפיס את המילה
`"הב הב"`
מכיוון שהקישור לאוביקט
`person`
אינו קיים עוד.

הקישור האמיתי עבור פרוטוטיפ כלשהו מצוי בתוך תכונה פנימית פרטית שנקראת
<span dir="ltr">`[[Prototype]]`</span>.
המתודה
<span dir="ltr">`Object.getPrototypeOf()`</span>
מחזירה את ערכה של
<span dir="ltr">`[[Prototype]]`</span>
והמתודה
<span dir="ltr">`Object.setPrototypeOf()`</span>
משנה את ערכה של
<span dir="ltr">`[[Prototype]]`</span>.
ואולם, אלו אינן הדרכים היחידות לעבוד עם הערך השמור בתוך
<span dir="ltr">`[[Prototype]]`</span>.

### גישה קלה לפרוטוטיפ בעזרת super

כפי שהוזכר קודם, לפרוטוטיפים תפקיד חשוב בג׳אווהסקריפט ועבודה רבה נעשתה כדי להקל על השימוש בהם בגרסת
ECMAScript 6.
שיפור נוסף היה יצירת מצביעים בשם
`super`,
שמאפשרים גישה נוחה יותר לפרוטוטיפ.
לפני כן, אם היינו רוצים לדרוס מתודה על אוביקט כך שהיא תקרא למתודה באותו שם של הפרוטוטיפ,
היינו עושים את הדבר הבא:



```js
let person = {
    getGreeting() {
        return "שלום";
    }
};

let dog = {
    getGreeting() {
        return "הב הב";
    }
};


let friend = {
    getGreeting() {
        return Object.getPrototypeOf(this).getGreeting.call(this) + ", הי";
    }
};

// שינוי הפרוטוטיפ לאוביקט בשם
// person
Object.setPrototypeOf(friend, person);
console.log(friend.getGreeting());                      // "שלום, הי"
console.log(Object.getPrototypeOf(friend) === person);  // true

// שינוי הפרוטוטיפ לאוביקט בשם
// dog
Object.setPrototypeOf(friend, dog);
console.log(friend.getGreeting());                      // "הב הב, הי"
console.log(Object.getPrototypeOf(friend) === dog);     // true
```



בדוגמה לעיל,
המתודה
<span dir="ltr">`friend.getGreeting()`</span>
קוראת למתודה באותו שם הקיימת על הפרוטוטיפ.
המתודה
<span dir="ltr">`Object.getPrototypeOf()`</span>
דואגת לקרוא לפרוטוטיפ הנכון ולהוסיף מחרוזת לפלט.
התוספת של
<span dir="ltr">`.call(this)`</span>
דואגת שהערך של
`this`
בתוך המתודה של הפרוטוטיפ יהיה הנכון.

לקרוא למתודה של פרוטוטיפ  על ידי שימוש ב
<span dir="ltr">`Object.getPrototypeOf()`</span>
ו
<span dir="ltr">`.call(this)`</span>
יכול להיות מורכב ומעיק לכתיבה, ולכן
ECMAScript 6
הוסיפה את
`super`.
עקרונית,
`super`
הינו מצביע
(pointer)
לפרוטוטיפ הנוכחי עבור אוביקט,
אך למעשה הוא משמש כצורה מקוצרת של
<span dir="ltr">`Object.getPrototypeOf(this)`</span>.
אפשר לכתוב את
<span dir="ltr">`getGreeting()`</span>
כך:



```js
let friend = {
    getGreeting() {
        // עושה את אותה פעולה כמו בדוגמה הקודמת עבור
        // Object.getPrototypeOf(this).getGreeting.call(this)
        return super.getGreeting() + ", הי";
    }
};
```


הקריאה ל
<span dir="ltr">`super.getGreeting()`</span>
עושה כאן את אותו הדבר כמו
<span dir="ltr">`Object.getPrototypeOf(this).getGreeting.call(this)`</span>.
באופן דומה ניתן לקרוא לכל מתודה של פרוטוטיפ באמצעות
`super`
כל עוד הוא מופיע בתוך מתודה מקוצרת
(concise method).
ניסיון לקרוא ל
`super`
מחוץ למתודה מקוצרת זורק שגיאת תחביר
(syntax error)
כמו בדוגמה הבאה:



```js
let friend = {
    getGreeting: function() {
        // שגיאת תחביר
        return super.getGreeting() + ", הי";
    }
};
```


הדוגמה לעיל משתמשת בתכונה שערכה הוא פונקציה, והקריאה ל
<span dir="ltr">`super.getGreeting()`</span>
זורקת שגיאה תחבירית מפני ש
`super`
אינו תקין בהקשר הזה.
`super`
מראה את כוחו כאשר יש רמות מרובות של ירושה, שכן,
<span dir="ltr">`Object.getPrototypeOf()`</span>.
אינו פועל כמצופה בכל המקרים.
לדוגמה:



```js
let person = {
    getGreeting() {
        return "שלום";
    }
};

// הפרוטוטיפ הינו האוביקט בשם
// person
let friend = {
    getGreeting() {
        return Object.getPrototypeOf(this).getGreeting.call(this) + ", הי";
    }
};
Object.setPrototypeOf(friend, person);


// הפרוטוטיפ הינו האוביקט בשם
// friend
let relative = Object.create(friend);

console.log(person.getGreeting());                  // "שלום"
console.log(friend.getGreeting());                  // "שלום, הי"
console.log(relative.getGreeting());                // שגיאה
```


הקריאה ל
<span dir="ltr">`Object.getPrototypeOf()`</span>.
גורמת לשגיאה כאשר קוראים למתודה
<span dir="ltr">`relative.getGreeting()`</span>.
זה קורה מפני ש
`this`
מצביע על
`relative`,
והפרוטוטיפ של
`relative`
הינו
`friend`.
כאשר
<span dir="ltr">`friend.getGreeting().call()`</span>
נקראת בזמן ש
`this`
מצביע על
`relative`,
התהליך מתחיל מחדש וממשיך לקרוא לפונקציה באופן רקורסיבי עד שמתקבלת שגיאת מחסנית
(stack overflow).

אין פתרון פשוט לבעיה בגרסת
ECMAScript 5
אך בגרסת
ECMAScript 6
בעזרת
`super`,
זה מאוד פשוט:



```js
let person = {
    getGreeting() {
        return "שלום";
    }
};

// הפרוטוטיפ הינו האוביקט בשם
// person
let friend = {
    getGreeting() {
        return super.getGreeting() + ", הי";
    }
};
Object.setPrototypeOf(friend, person);


// הפרוטוטיפ הינו האוביקט בשם
// person
let relative = Object.create(friend);

console.log(person.getGreeting());                  // "שלום"
console.log(friend.getGreeting());                  // "שלום, הי"
console.log(relative.getGreeting());                // "שלום, הי"
```



`super`
אינו נקבע באופן דינמי ולכן תמיד יצביע לאוביקט הנכון.
במקרה שלנו,
<span dir="ltr">`super.getGreeting()`</span>
תמיד מפנה ל
<span dir="ltr">`person.getGreeting()`</span>,
ללא קשר למספר האוביקטים שיורשים את המתודה.

## הגדרה רשמית עבור מתודה

עד גרסת
ECMAScript 6,
המושג ״מתודה״ לא הוגדר בצורה רשמית.
מתודות היו תכונות של אוביקט שערכן היה פונקציה.
ECMAScript 6
מגדיר מתודה בצורה רשמית כפונקציה בעלת תכונה פנימית בשם
<span dir="ltr">`[[HomeObject]]`</span>
שמצביעה על האוביקט אליו שייכת המתודה.
לדוגמה:

```js
let person = {

    // מתודה
    getGreeting() {
        return "שלום";
    }
};

// לא מתודה
function shareGreeting() {
    return "הי";
}
```


הדוגמה לעיל מגדירה אוביקט
`person`
בעל מתודה אחת בשם
<span dir="ltr">`ֿgetGreeting()`</span>.
הערך המושם בתוך
<span dir="ltr">`[[HomeObject]]`</span>
עבור המתודה
<span dir="ltr">`ֿgetGreeting()`</span>.
הינו
`person`
מתוקף שיוך הפונקציה לאוביקט.
מצד שני,
הפונקציה
<span dir="ltr">`shareGreeting()`</span>
אינה בעלת ערך עבור התכונה הפנימית
<span dir="ltr">`[[HomeObject]]`</span>
מאחר והיא לא שויכה לאוביקט בזמן הגדרתה.
ברוב המקרים ההבדל אינו חשוב, אך הופך לחשוב ביותר כאשר משתמשים ב
`super`.

`super`
משתמש בתכונה הפנימית
<span dir="ltr">`[[HomeObject]]`</span>.
השלב הראשון הינו לקרוא למתודה
<span dir="ltr">`Object.getPrototypeOf()`</span>
על הערך של
<span dir="ltr">`[[HomeObject]]`</span>
כדי לקבל את הפרוטוטיפ.
לאחר מכן מחפשים עליו פונקציה בעלת אותו השם.
בסוף, נקבע הערך של-
`this`
והמתודה נקראת.
לדוגמה:

```js
let person = {
    getGreeting() {
        return "שלום";
    }
};

// prototype is person
let friend = {
    getGreeting() {
        return super.getGreeting() + ", הי";
    }
};
Object.setPrototypeOf(friend, person);

console.log(friend.getGreeting());  // "שלום, הי"
```


הקריאה למתודה
<span dir="ltr">`friend.getGreeting()`</span>
מחזירה מחרוזת שמשלבת את הערך המוחזר מקריאת
<span dir="ltr">`person.getGreeting()`</span>
יחד עם המחרוזת
`", הי"`.
ערך התכונה הפנימית
<span dir="ltr">`[[HomeObject]]`</span>
של
<span dir="ltr">`friend.getGreeting()`</span>
הוא
`friend`
והפרוטוטיפ של
`friend`
הוא
person
ומכאן
<span dir="ltr">`super.getGreeting()`</span>
פועלת כמו
<span dir="ltr">`person.getGreeting.call(this)`</span>.

## סיכום

אוביקטים מהווים נושא מרכזי עבור מתכנתים בג׳אווהסקריפט,
וגרסת
ECMAScript 6
הוסיפה מספר תוספות מבורכות לאוביקטים שהופכות את העבודה איתם לקלה יותר ומספקת יכולות חדשות.

ECMAScript 6
ביצעה מספר שינויים למה שנקרא אוביקט ליטראל.
הגדרות תכונה מקוצרות מקלות על הגדרת תכונות בעלות שם זהה למשתנים באותה סביבה.
שמות מחושבים עבור תכונות מאפשרים לנו להגדיר ערכים לא רגילים בתור שמות של תכונות, אגב, יכולת שהייתה קיימת באופן חלקי גם קודם לכן.
כתיבה קצרה עבור מתודות מאפשרת לנו לכתוב פחות קוד על מנת להגדיר מתודה על אוביקט בעזרת השמטת סימן נקודותיים
(:)
ואת המילה
`function`.
ECMAScript 6
מגמיש את הבדיקה במצב קשיח עבור כפילות בשם התכונות של אוביקט, כך שניתן לשייך שתי תכונות בעלות אותו שם עבור אוביקט אחד ללא שגיאה.

המתודה
<span dir="ltr">`Object.assign()`</span>
מקלה על שינוי מספר תכונות באוביקט בו זמנית. זהו שיפור יעיל כשמשתמשים בטכניקת מיקסין
(mixin pattern).
המתודה
<span dir="ltr">`Object.is()`</span>
מבצעת בדיקת שוויון עמוק עבור כל ערך, למעשה היא הופכת לגרסה בטוחה של האופרטור
`===`
כאשר מטפלים בערכים מיוחדים של ג׳אווהסקריפט.

סדר הספירה של תכונות עצמיות מוגדר באופן ברור תחת
ECMAScript 6.
כאשר עוברים על תכונות,
מפתחות נומריים תמיד באים ראשונים בסדר עולה כשבעקבותיהם באים מפתחות מסוג מחרוזת לפי סדר ההוספה ולאחר מכן באים מפתחות מסוג סמלים
(symbol)
לפי סדר ההוספה.

כעת ניתן לשנות את הפרוטוטיפ של אוביקט לאחר יצירתו,
הודות למתודה
<span dir="ltr">`Object.setPrototypeOf()`</span>.

לבסוף, ניתן להשתמש במילה
`super`
כדי לקרוא למתודות על הפרוטוטיפ של אוביקט נתון.
הקישור למשתנה
`this`
בתוך מתודה אשר נקראת באמצעות
`super`
נקבע באופן אוטומטי לערך הנוכחי של
`this`.


