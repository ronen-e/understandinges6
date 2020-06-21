<div dir="rtl">

# פירוק לשם גישה קלה יותר לנתונים

כתיבה מקוצרת של אוביקטים ומערכים מאוד נפוצה בג׳אווהסקריפט, והודות לפורמט הנתונים מסוג
JSON,
הפכה לחלק חשוב במיוחד של השפה. כיום אין זה נדיר להגדיר אוביקטים ומערכים ואז למשוך חתיכות מתוך אותם מבני נתונים.
ECMAScript 6 
מפשט פעולה זו ע״י הוספת יכולת 
*פירוק*
(*destructuring*), 
שהיא פעולת פירוק מבנה נתונים לחלקים קטנים יותר. פרק זה מראה כיצד לפרק אוביקטים ומערכים.

## כיצד מועילה לנו פעולת הפירוק?

בגרסת 
ECMAScript 5
וקודם לכן,
הצורך לחלץ נתונים מתוך אוביקטים ומערכים יכול היה להוביל לכתיבה מרובה של קוד כמעט זהה לחלוטין לקודמו, אך ורק בשביל להעביר נתונים מסוימים אל תוך משתנים מקומיים.
לדוגמה:

<div dir="ltr">

```js
let options = {
    repeat: true,
    save: false
};

// חילוץ נתונים מתוך האוביקט
let repeat = options.repeat,
    save = options.save;
```

</div>

הקוד לעיל מחלץ את הערכים
`repeat`
ו
`save` 
מתוך האוביקט בשם
`options` 
ושומר אותם במשתנים מקומיים עם שם זהה. בעוד שהקוד נדמה פשוט, ניתן לשער מה מתרחש כאשר קיים מספר רב של משתנים. 
במקרה כזה יש צורך לבצע השמה עבור כל אחד ואחד מהם בנפרד, אחד אחרי השני.
ובמידה והמידע נמצא עמוק בתוך מבנה נתונים פנימי
(nested data structure)
היה צורך לסרוק את כל מבנה הנתונים על מנת למצוא נתון אחד.

מסיבה זו הוסיפו את היכולת לפרק אוביקטים ומערכים בגרסת
ECMAScript 6.
כאשר מפרקים מבנה נתונים לחלקים קטנים יותר, הדבר מקל על השגת המבוקש. שפות שונות משתמשות בשיטות פירוק עם תחביר מינימליסטי כדי לפשט את התהליך.
המימוש של
ECMAScript 6
משתמש בתחביר מוכר: זה של כתיבת אוביקטים ומערכים.

## פירוק אוביקטים

התחביר עבור פירוק מערכים משתמש באוביקט ליטראל בצד השמאלי של אופרציית ההשמה. לדוגמה:

<div dir="ltr">

```js
let node = {
        type: "Identifier",
        name: "foo"
    };

let { type, name } = node;

console.log(type);      // "Identifier"
console.log(name);      // "foo"
```

</div>

בקוד לעיל, ערכו של 
`node.type` 
שמור במשתנה  
`type` 
וערכו של 
`node.name`
שמור במשתנה  
`name`.
צורת הכתיבה זהה לצורה המקוצרת לאתחול תכונה שהופיעה בפרק 4. 
המזהים
`type`
ו- 
 `name`
שניהם נחשבים להגדרות משתנים מקומיים,
כמו גם שמות התכונות שייקראו מתוך אוביקט
`node`.

A> #### אל תשכחו את אתחול הערך
A>
A> כאשר משתמשים בפעולת פירוק על מנת להגדיר משתנים מסוג
A> `var`, `let`, 
A> או
A> `const`,
A> חובה לספק מה שנקרא מאתחל הערך
A> (initializer),
A> זהו הערך שבא אחרי סימן ההשוואה
A> (`=`).
A> השורות הבאות בקוד יגרמו כולן לזריקת שגיאות תחביר בגלל היעדר מאתחל ערך:
A>
A><div dir="ltr">
A>
A>
A>```js
A>// syntax error!
A>var { type, name };
A>
A>// syntax error!
A>let { type, name };
A>
A>// syntax error!
A>const { type, name };
A>```
A></div>
A>
A> בעוד אשר שמשתנה מסוג
A> `const`
A> תמיד דורש מאתחל לערך, אפילו בעת הגדרת משתנה רגילה, ולא רק בפירוק, משתנים מסוג
A> `var` 
A> ו- 
A> `let` 
A> דורשים אתחול לערכם רק בעת פעולת פירוק.

#### השמה בפירוק

הדוגמאות שהוצגו עד עתה הגדירו משתנים. 
אך ניתן  לבצע השמה באמצעות פעולת פירוק. כך למשל, ניתן לשנות ערכי משתנים לאחר הגדרתם, כמו בדוגמה הבאה:

<div dir="ltr">

```js
let node = {
        type: "Identifier",
        name: "foo"
    },
    type = "Literal",
    name = 5;

// השמת ערכים באמצעות פירוק אוביקט
({ type, name } = node);

console.log(type);      // "Identifier"
console.log(name);      // "foo"
```

</div>

בדוגמה לעיל, המשתנים
`type` 
ו- 
`name` 
מאותחלים עם ערכים בעת הגדרתם ולאחר מכן שני משתנים בעלי אותו שם מאותחלים עם ערכים שונים.
השורה הבאה משתמשת בהשמה דרך פעולת פירוק
(destructuring assignment) 
כדי לשנות את אותם ערכים על ידי קריאת האוביקט 
`node`. 
שים לב לסוגריים שחובה לשים מסביב לפעולת הפירוק. 
הסיבה לכך היא שמבחינת התחביר, סוגרים מסולסלים פותחים
(`{`)
נחשבים לתחילת בלוק של קוד
(block statement). 
אך בלוק של קוד אינו יכול להופיע בצידה השמאלי של פעולת השמה.
הסוגרים מסמנים לנו שהסוגרים המסולסלים אינם תחילת בלוק של קוד ויש להתייחס אליהם בתור ביטוי
(expression), 
וכך ניתן לבצע את פעולת ההשמה.

פעולת הפירוק מחשבת ומקבלת את ערכו של צדו הימני של הביטוי
(החלק שלאחר סימן 
`=`). 
המשמעות היא שניתן להשתמש בפעולת השמה בעזרת פירוק בכל מקום שבו מצפים לקבל ערך. כך למשל ניתן להעביר ערך אל תוך פונקציה:

<div dir="ltr">

```js
let node = {
        type: "Identifier",
        name: "foo"
    },
    type = "Literal",
    name = 5;

function outputInfo(value) {
    console.log(value === node);
}

outputInfo({ type, name } = node);        // true

console.log(type);      // "Identifier"
console.log(name);      // "foo"
```

</div>

הפונקציה
<span dir="ltr">`outputInfo()`</span>
נקראת עם ביטוי השמה על ידי פירוק
(destructuring assignment expression). 
הביטוי מקבל את הערך עבור המשתנה
`node` 
מכיוון וזה ערך הצד הימני של הביטוי.
ההשמה עבור המשתנים
`type` 
ו- 
`name` 
מתבצעת כרגיל והמשתנה
`node` 
מועבר כארגומנט לפונקציה
<span dir="ltr">`outputInfo()`</span>.

W> שגיאה תיזרק במידה וצידו הימני של ביטוי השמה על ידי פירוק
(החלק שלאחר סימן 
`=`) 
מקבלת את הערכים
`null` 
או 
`undefined`.
זה קורה מכיוון שכל ניסיון לקרוא תכונה בערך מסוג
`null` 
או 
`undefined`
זורק שגיאת ריצה
(runtime error).


#### ערכים דיפולטיביים

כאשר משתמשים בפקודת השמה על ידי פירוק 
(destructuring assignment statement), 
אם מציינים משתנה עבור תכונה שאינה קיימת על האוביקט, אזי אותו משתנה מקבל את הערך
`undefined`.
לדוגמה:

<div dir="ltr">

```js
let node = {
        type: "Identifier",
        name: "foo"
    };

let { type, name, value } = node;

console.log(type);      // "Identifier"
console.log(name);      // "foo"
console.log(value);     // undefined
```
</div>

הקוד בדוגמה האחרונה מגדיר משתנה בשם
`value` 
ומנסה לבצע השמת ערך אליו. ואולם, אין בנמצא תכונה על האוביקט
`node` 
באותו שם, ולכן המשתנה מקבל את הערך
 `undefined` 
כמצופה.
`undefined`.

ניתן להגדיר ערך התחלתי דיפולטיבי עבור תכונה ספציפית שאינה מוגדרת. כדי לעשות זאת יש להשתמש בסימן ההשוואה
(`=`) 
לאחר שם התכונה ואז להגדיר את הערך ההתחלתי. כמו בדוגמה הבאה:

<div dir="ltr">

```js
let node = {
        type: "Identifier",
        name: "foo"
    };

let { type, name, value = true } = node;

console.log(type);      // "Identifier"
console.log(name);      // "foo"
console.log(value);     // true
```

</div>

בדוגמה זו, המשתנה
`value` 
מקבל את הערך
`true`
בתור ערך דיפולטיבי. הערך הדיפולטיבי יתקבל רק במידה והתכונה אינה קיימת על האוביקט
`node` 
או בעלת ערך
`undefined`.
מאחר ואין תכונה מסוג
`node.value` 
המשתנה
`value` 
משתמש בערך הדיפולטיבי. זה עובד בצורה דומה לערכי פרמטר דיפולטיביים עבור פונקציות כפי שהוסבר בפרק 3.

#### השמה למשתנים בעלי שמות שונים
עד עתה, כל דוגמה השתמשה בשמות התכונות על האוביקט בתור שמות המשתנים. כך למשך, הערך עבור
`node.type`
נשמר במשתנה בשם
`type`.
זה טוב כל עוד ברצונכם להשתמש באותו שם, אבל מה אם לא?
לגרסת

יש צורת תחביר שמאפשר השמת ערכים למשתנים בעלי שם שונה, והתחביר דומה במראהו לזה של אתחול תכונה על אוביקט. לדוגמה:

<div dir="ltr">

```js
let node = {
        type: "Identifier",
        name: "foo"
    };

let { type: localType, name: localName } = node;

console.log(localType);     // "Identifier"
console.log(localName);     // "foo"
```
</div>

הקוד בדוגמה משתמש בהשמה ע״י פירוק על מנת להגדיר את המשתנים
`localType`
ו-
 `localName`,
 שמכילים את ערכי התכונות
`node.type`
ו- 
`node.name`,
בהתאמה.
התחביר
`type: localType`
מורה על קריאת התכונה בשם 
`node.type`
ולשמור את ערכה במשתנה
`localType`.
התחביר הנ״ל הינו למעשה ההיפך מהתחביר הרגיל לכתיבת אוביקט ליטראל, שבו שם התכונה נמצא משמאל לסימן הנקודותיים והערך מצידו הימני. במקרה שלנו, שם המשתנה מופיע מימין לסימן הנקודותיים ומיקום הערך על האוביקט נמצא מצידו השמאלי.

באפשרותכם להוסיף ערכים דיפולטיביים כאשר משתמשים בשמות שונים.
סימן ההשוואה והערך הדיפולטיבי עדיין ממוקמים לאחר שם המשתנה. לדוגמה:

<div dir="ltr">

```js
let node = {
        type: "Identifier"
    };

let { type: localType, name: localName = "bar" } = node;

console.log(localType);     // "Identifier"
console.log(localName);     // "bar"
```
</div>

בדוגמה זו, למשתנה
`localName`
יש את הערך הדיפולטיבי 
`"bar"`.
המשתנה יקבל את הערך הדיפולטיבי מכיוון והתכונה 
`node.name`
אינה קיימת.

בינתיים, ראינו כיצד לבצע פירוק לאוביקט שתכונותיו מקבלות ערכים פשוטים. פירוק אוביקטים יכול לשמש כדי לקבל ערכים בתוך אוביקט פנימי 
(nested object structures).

#### פירוק עבור אוביקטים פנימיים
בעזרת כתיבה הדומה לזו של אוביקט ליטראל, ניתן לנווט בתוך אוביקט פנימי על מנת לקבל את הנתונים הרצויים. לדוגמה:

<div dir="ltr">

```js
let node = {
        type: "Identifier",
        name: "foo",
        loc: {
            start: {
                line: 1,
                column: 1
            },
            end: {
                line: 1,
                column: 4
            }
        }
    };

let { loc: { start }} = node;

console.log(start.line);        // 1
console.log(start.column);      // 1
```
</div>

טכניקת הפירוק בקוד בדוגמה לעיל משתמש בסוגריים מסולסלים כדי להורות לפעולת הפירוק לנווט לתכונה בשם
`loc`
שבאוביקט
`node`
ולתור אחר התכונה
`start`.
היכן שקיים סימן נקודותיים באופרציית פירוק, המשמעות שהמזהה לפני הסימן משמש לקביעת המיקום לבדיקה והצד הימני משמש לקביעת הערך. כאשר רואים סוגריים מסולסלים לאחר סימן נקודותיים המשמעות היא שהיעד הסופי הוא פנימי בתוך האוביקט.

ניתן אף לתת שם אחר למשתנה:

<div dir="ltr">

```js
let node = {
        type: "Identifier",
        name: "foo",
        loc: {
            start: {
                line: 1,
                column: 1
            },
            end: {
                line: 1,
                column: 4
            }
        }
    };

// extract node.loc.start
let { loc: { start: localStart }} = node;

console.log(localStart.line);   // 1
console.log(localStart.column); // 1
```
</div>

בדוגמה לעיל, הערך בתוך
`node.loc.start` 
שמור במשתנה בשם 
`localStart`.
פירוק יכול להתבצע בכל רמת עומק, ובכל רמה גישה מלאה ליכולות המלאות של פעולת הפירוק.

פירוק אוביקטים היא פעולה עוצמתית בעלת אפשרויות רבות, אך פירוק של מערכים מוסיף מספר יכולות מיוחדות שמאפשרות לחלץ נתונים מתוך מערכים.

A> #### בעיות תחביר
A>
A> יש להיזהר ממצב שבו יוצרים פקודה חסרת משמעות בעת פירוק עמוק. סוגריים מסולסלים ריקים נחשבים לתחביר תקין בעת פירוק אוביקט, אך הינם חסרי משמעות. לדוגמה:
A>
A> <div dir="ltr">
A>
A>```js
A>// אין הגדרת משתנה!
A>let { loc: {} } = node;
A>```
A> </div>
A> בקוד למעלה לא מתבצע קישור בין תכונה למשתנה. בגלל הסוגריים המסולסלים בצד ימין, השם
A> `loc` 
A> משמש לניווט ולא בתור משתנה. במקרה כזה סביר שהכוונה הייתה להשתמש בסימן
A> `=`
A> כדי להגדיר ערך דיפולטיבי ולא להשתמש בסימן
A> `:` 
A> שמשמש לקביעת מיקום. ייתכן שתחביר כזה יהיה בלתי תקין בעתיד, אך כרגע יש להיזהר ממנו.

## פירוק מערכים

תחביר עבור פירוק מערכים דומה לזה של פירוק אוביקטים. הוא פשוט משתמש בתחביר של כתיבת מערך במקום זה של כתיבת  אוביקט ליטראל. פעולת הפירוק עובדת על מיקומים בתוך המערך במקום לעבוד על תכונות בתוך האוביקט. לדוגמה:

<div dir="ltr">

```js
let colors = [ "red", "green", "blue" ];

let [ firstColor, secondColor ] = colors;

console.log(firstColor);        // "red"
console.log(secondColor);       // "green"
```
</div>

בדוגמה לעיל, פירוק על המערך מושך את הערכים
`"red"` 
ו-
`"green"`
מתוך המערך בשם
`colors`
ושומר אותם במשתנים
`firstColor` 
ו-
`secondColor`.
הערכים הללו נבחרו בשל מיקומם בתוך המערך. שמות המשתנים היו יכולים להיות כל שם. כל פריט במערך שאין אליו התייחסות - מתעלמים ממנו. המערך עצמו אינו מושפע מפעולת הפירוק.

ניתן להתעלם מפריטים קיימים בעת הפירוק ולתת שמות רק עבור פריטים שאנו מעוניינים בהם. אם למשל היינו רוצים רק את הערך במיקום השלישי במערך, אין צורך לספק שמות עבור הפריט הראשון והשני. לדוגמה:

<div dir="ltr">

```js
let colors = [ "red", "green", "blue" ];

let [ , , thirdColor ] = colors;

console.log(thirdColor);        // "blue"
```

</div>

הקוד בדוגמה לעיל משתמש בפעולת פירוק כדי לקבל את הפריט השלישי במערך
`colors`.
סימוני הפסיק לפני השם
`thirdColor` 
משמשים בתור סמנים לפריטים במערך שבאים לפניו.

W> בדומה לפירוק אוביקטים, חובה לספק מאתחל ערך כאשר מפרקים מערך, על ידי שימוש במזהה מסוג
`var`, `let`, 
או
`const`.

#### השמה על ידי פירוק

ניתן להשתמש בפירוק מערכים בכדי לבצע השמה, אך בניגוד לפירוק אוביקט אין צורך לעטוף את הביטוי בסוגריים. לדוגמה:

<div dir="ltr">

```js
let colors = [ "red", "green", "blue" ],
    firstColor = "black",
    secondColor = "purple";

[ firstColor, secondColor ] = colors;

console.log(firstColor);        // "red"
console.log(secondColor);       // "green"
```

</div>

ההשמה על ידי פירוק בקוד לעיל מתבצעת באופן דומה לדוגמה הקודמת שעסקה בפירוק מערך. ההבדל היחידי הינו שהמשתנים
`firstColor` 
ו- 
`secondColor`
כבר הוגדרו מבעוד מועד. במרבית המקרים אין צורך לדעת יותר מעבר לכך בעת פירוק מערכים. אך במקצת המקרים יש דבר נוסף שיועיל לנו.

פירוק מערכים מאפשר במצב מסוים להחליף את ערכם של שני משתנים. החלפת ערכים היא פעולה נפוצה בפעולות מיון, ושיטת החלפת הערכים בגרסת 
ECMAScript 5
מערבת משתנה שלישי זמני כמו בדוגמה הבאה:

<div dir="ltr">

```js
// החלפת מערכים בגרסת אקמהסקריפט 5
let a = 1,
    b = 2,
    tmp;

tmp = a;
a = b;
b = tmp;

console.log(a);     // 2
console.log(b);     // 1
```

</div>

המשתנה הזמני
`tmp`
נחוץ על מנת להחליף את ערכי
`a`
ו-
`b`.
באמצעות פירוק מערכים , אין צורך במשתנה הנוסף. בדוגמה הבאה נראה כיצד ניתן להחליף ערכים בגרסת
ECMAScript 6:

<div dir="ltr">

```js
// החלפת ערכים בגרסת אקמהסקריפט 6
let a = 1,
    b = 2;

[ a, b ] = [ b, a ];

console.log(a);     // 2
console.log(b);     // 1
```

</div>

פעולת ההשמה על ידי פירוק בדוגמה לעיל נראית כמו השתקפות בראי. הצד השמאלי של ההשמה
(לפני סימן ההשוואה)
מראה שימוש בפירוק בדומה לדוגמאות הקודמות עבור פירוק מערכים.
הצד הימני הינו מערך שנוצר באופן זמני לצורך פעולת ההחלפה. 
הפירוק מתרחש על המערך הזמני, שמכיל בתוכו את הערכים
`a`
ו-
`b`
אשר מועתקים למיקום הראשון והשני במערך, בהתאמה.
התוצאה היא שערכי המשתנים הוחלפו זה בזה.

W> בדומה לפעולת השמה על ידי פירוק באוביקט, גם כאן תיזרק שגיאה במידה וצידה הימני של פעולת ההשמה מכיל את הערכים 
`null` 
או
`undefined`.


W> Like object destructuring assignment, an error is thrown when the right side of an array destructured assignment expression evaluates to `null` or `undefined`.

#### Default Values

Array destructuring assignment allows you to specify a default value for any position in the array, too. The default value is used when the property at the given position either doesn't exist or has the value `undefined`. For example:

```js
let colors = [ "red" ];

let [ firstColor, secondColor = "green" ] = colors;

console.log(firstColor);        // "red"
console.log(secondColor);       // "green"
```

In this code, the `colors` array has only one item, so there is nothing for `secondColor` to match. Since there is a default value, `secondColor` is set to `"green"` instead of `undefined`.

#### Nested Destructuring

You can destructure nested arrays in a manner similar to destructuring nested objects. By inserting another array pattern into the overall pattern, the destructuring will descend into a nested array, like this:

```js
let colors = [ "red", [ "green", "lightgreen" ], "blue" ];

// later

let [ firstColor, [ secondColor ] ] = colors;

console.log(firstColor);        // "red"
console.log(secondColor);       // "green"
```

Here, the `secondColor` variable refers to the `"green"` value inside the `colors` array. That item is contained within a second array, so the extra square brackets around `secondColor` in the destructuring pattern are necessary. As with objects, you can nest arrays arbitrarily deep.

#### Rest Items

Chapter 3 introduced rest parameters for functions, and array destructuring has a similar concept called *rest items*. Rest items use the `...` syntax to assign the remaining items in an array to a particular variable. Here's an example:

```js
let colors = [ "red", "green", "blue" ];

let [ firstColor, ...restColors ] = colors;

console.log(firstColor);        // "red"
console.log(restColors.length); // 2
console.log(restColors[0]);     // "green"
console.log(restColors[1]);     // "blue"
```

The first item in `colors` is assigned to `firstColor`, and the rest are assigned into a new `restColors` array. The `restColors` array, therefore, has two items: `"green"` and `"blue"`. Rest items are useful for extracting certain items from an array and keeping the rest available, but there's another helpful use.

A glaring omission from JavaScript arrays is the ability to easily create a clone. In ECMAScript 5, developers frequently used the `concat()` method as an easy way to clone an array. For example:

```js
// cloning an array in ECMAScript 5
var colors = [ "red", "green", "blue" ];
var clonedColors = colors.concat();

console.log(clonedColors);      //"[red,green,blue]"
```

While the `concat()` method is intended to concatenate two arrays together, calling it without an argument returns a clone of the array. In ECMAScript 6, you can use rest items to achieve the same thing through syntax intended to function that way. It works like this:

```js
// cloning an array in ECMAScript 6
let colors = [ "red", "green", "blue" ];
let [ ...clonedColors ] = colors;

console.log(clonedColors);      //"[red,green,blue]"
```

In this example, rest items are used to copy values from the `colors` array into the `clonedColors` array. While it's a matter of perception as to whether this technique makes the developer's intent clearer than using the `concat()` method, this is a useful ability to be aware of.

W> Rest items must be the last entry in the destructured array and cannot be followed by a comma. Including a comma after rest items is a syntax error.

## Mixed Destructuring

Object and array destructuring can be used together to create more complex expressions. In doing so, you are able to extract just the pieces of information you want from any mixture of objects and arrays. For example:

```js
let node = {
        type: "Identifier",
        name: "foo",
        loc: {
            start: {
                line: 1,
                column: 1
            },
            end: {
                line: 1,
                column: 4
            }
        },
        range: [0, 3]
    };

let {
    loc: { start },
    range: [ startIndex ]
} = node;

console.log(start.line);        // 1
console.log(start.column);      // 1
console.log(startIndex);        // 0
```

This code extracts `node.loc.start` and `node.range[0]` into `start` and `startIndex`, respectively. Keep in mind that `loc:` and `range:` in the destructured pattern are just locations that correspond to properties in the `node` object. There is no part of `node` that cannot be extracted using destructuring when you use a mix of object and array destructuring. This approach is particularly useful for pulling values out of JSON configuration structures without navigating the entire structure.

## Destructured Parameters

Destructuring has one more particularly helpful use case, and that is when passing function arguments. When a JavaScript function takes a large number of optional parameters, one common pattern is to create an `options` object whose properties specify the additional parameters, like this:

```js
// properties on options represent additional parameters
function setCookie(name, value, options) {

    options = options || {};

    let secure = options.secure,
        path = options.path,
        domain = options.domain,
        expires = options.expires;

    // code to set the cookie
}

// third argument maps to options
setCookie("type", "js", {
    secure: true,
    expires: 60000
});
```
Many JavaScript libraries contain `setCookie()` functions that look similar to this one. In this function, the `name` and `value` arguments are required, but `secure`, `path`, `domain`, and `expires` are not. And since there is no priority order for the other data, it's efficient to just have an `options` object with named properties, rather than list extra named parameters. This approach works, but now you can't tell what input the function expects just by looking at the function definition; you need to read the function body.

Destructured parameters offer an alternative that makes it clearer what arguments a function expects. A destructured parameter uses an object or array destructuring pattern in place of a named parameter. To see this in action, look at this rewritten version of the `setCookie()` function from the last example:

```js
function setCookie(name, value, { secure, path, domain, expires }) {

    // code to set the cookie
}

setCookie("type", "js", {
    secure: true,
    expires: 60000
});
```

This function behaves similarly to the previous example, but now, the third argument uses destructuring to pull out the necessary data. The parameters outside the destructured parameter are clearly expected, and at the same time, it's clear to someone using `setCookie()` what options are available in terms of extra arguments. And of course, if the third argument is required, the values it should contain are crystal clear. The destructured parameters also act like regular parameters in that they are set to `undefined` if they are not passed.

A>Destructured parameters have all of the capabilities of destructuring that you've learned so far in this chapter. You can use default values, mix object and array patterns, and use variable names that differ from the properties you're reading from.

### Destructured Parameters are Required

One quirk of using destructured parameters is that, by default, an error is thrown when they are not provided in a function call. For instance, this call to the `setCookie()` function in the last example throws an error:

```js
// Error!
setCookie("type", "js");
```

The third argument is missing, and so it evaluates to `undefined` as expected. This causes an error because destructured parameters are really just a shorthand for destructured declaration. When the `setCookie()` function is called, the JavaScript engine actually does this:

```js
function setCookie(name, value, options) {

    let { secure, path, domain, expires } = options;

    // code to set the cookie
}
```

Since destructuring throws an error when the right side expression evaluates to `null` or `undefined`, the same is true when the third argument isn't passed to the `setCookie()` function.

If you want the destructured parameter to be required, then this behavior isn't all that troubling. But if you want the destructured parameter to be optional, you can work around this behavior by providing a default value for the destructured parameter, like this:

```js
function setCookie(name, value, { secure, path, domain, expires } = {}) {

    // ...
}
```

This example provides a new object as the default value for the third parameter. Providing a default value for the destructured parameter means that the `secure`, `path`, `domain`, and `expires` will all be `undefined` if the third argument to `setCookie()` isn't provided, and no error will be thrown.

### Default Values for Destructured Parameters

You can specify destructured default values for destructured parameters just as you would in destructured assignment. Just add the equals sign after the parameter and specify the default value. For example:

```js
function setCookie(name, value,
    {
        secure = false,
        path = "/",
        domain = "example.com",
        expires = new Date(Date.now() + 360000000)
    } = {}
) {

    // ...
}
```

Each property in the destructured parameter has a default value in this code, so you can avoid checking to see if a given property has been included in order to use the correct value. Also, the entire destructured parameter has a default value of an empty object, making the parameter optional. This does make the function declaration look a bit more complicated than usual, but that's a small price to pay for ensuring each argument has a usable value.

## Summary

Destructuring makes working with objects and arrays in JavaScript easier. Using the familiar object literal and array literal syntax, you can pick data structures apart to get at just the information you're interested in. Object patterns allow you to extract data from objects while array patterns let you extract data from arrays.

Both object and array destructuring can specify default values for any property or item that is `undefined` and both throw errors when the right side of an assignment evaluates to `null` or `undefined`. You can also navigate deeply nested data structures with object and array destructuring, descending to any arbitrary depth.

Destructuring declarations use `var`, `let`, or `const` to create variables and must always have an initializer. Destructuring assignments are used in place of other assignments and allow you to destructure into object properties and already-existing variables.

Destructured parameters use the destructuring syntax to make "options" objects more transparent when used as function parameters. The actual data you're interested in can be listed out along with other named parameters. Destructured parameters can be array patterns, object patterns, or a mixture, and you can use all of the features of destructuring.
