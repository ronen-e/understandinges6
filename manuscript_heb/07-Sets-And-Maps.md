<div dir="rtl">

# סטים ומפות

לג׳אווהסקריפט היה במקור רק סוג אחד של אוסף, 
(collection)
שמיוצג על ידי האוביקט
`Array`
(
    למרות שחלק יטענו שכל האוביקטים שאינם מערכים הינם אך ורק אוספים של זוגות של מזהה-וערך,
    מטרת השימוש שלהם הייתה במקור שונה מאוד מזו של מערכים
).
מערכים משמשים בג׳אווהסקריפט בדיוק כמו מערכים בשפות אחרות, אבל היעדר יכולות של מערכים כאלו הוביל לכך שמערכים שימשו תמיד בתור מבני נתונים מסוג תור או ערימה
(
    stacks & queues
).
מכיוון ולמערכים יש אינדקס נומרי מפתחים השתמשו באוביקטים מסוג אחר בכל פעם שהיה צורך באינדקס מסוג אחר. טכניקה זו הובילה למימושים עצמאיים של סטים ומפות בעזרת אוביקטים שאינם מערכים.

*סט* 
*(Set)*
הינו רשימת ערכים שאינם יכולים להכיל כפילויות.
בדרך כלל לא ניגשים לפריטים בודדים בתוך סט כמו שעושים לפריטים בתוך מערך. במקום זאת נפוץ יותר לבדוק בסט האם הערך קיים.

*מפה*
(*Map*)
היא אוסף של מזהים שתואמים לערכים מסוימים. כל פריט במפה מכיל 2 חתיכות מידע, וערכים נשלפים לפי המזהה.
מפות משמשות לרוב בתוך זכרון מטמון, עבור מידע שנועד לשליפה מהירה בהמשך. בעוד שאקמהסקריפט 5 לא הגדירה סטים ומפות באופן רשמי, מפתחים מימשו זאת בעצמם באמצעות אוביקטים שאינם מערכים.

אקמהסקריפט 6 הוסיפה סטים ומפות לשפת ג׳אווהסקריפט ופרק זה דן בכל מה שעליכם לדעת על שני סוגי האוספים הללו.

תחילה אדון בשיטות השונות שמפתחים השתמשו על מנת לממש סטים ומפות לפני אקמהסקריפט 6, ומדוע המימושים האלו היו בעייתיים. לאחר מכן ארחיב כיצד סטים ומפות פועלים באקמהסקריפט 6.

## סטים ומפות באקמהסקריפט 5

באקמהסקריפט 5, מפתחים חיקו סטים ומפות על ידי שימוש בתכונות אוביקט, כמו בדוגמה הבאה:

<div dir="ltr">

```js
let set = Object.create(null);

set.foo = true;

// בדיקה שהערך קיים
if (set.foo) {

    // קוד
}
```

</div>

המשתנה
`set`
בדוגמה הזו הוא אוביקט עם הפרוטוטיפ
`null`,
ובכך מוודא שאין תכונות מורשות על האוביקט. שימוש בתכונות אוביקט בתור ערכים ייחודיים לבדיקה נמצא בשימוש נפוץ באקמהסקריפט 5. כאשר תכונה מתווספת לאוביקט
`set`
היא מקבלת את הערך
`true`
כך שפקודות תנאי
(
    כמו פקודת
    `if`
    בדוגמה לעיל
)
יוכלו לבדוק בקלות האם הערך נוכח על האוביקט.

ההבדל האמיתי היחידי בין שימוש באוביקט בתור סט ושימוש באוביקט בתור מפה הינו הערך השמור. כך למשל, הדוגמה הבאה משתמשת באוביקט בתור מפה.

<div dir="ltr">

```js
let map = Object.create(null);

map.foo = "bar";

// שליפת ערך
let value = map.foo;

console.log(value);         // "bar"
```

</div>

הקוד לעיל שומר את ערך המחרוזת
`"bar"`
תחת המזהה
`foo`.
בניגוד לסט, מפות משמשות בעיקר לשליפת אינפורמציה ולא רק לבדיקת קיום המזהה.

## הבעיות בשיטות הקודמות

בעוד שאין בעיה להשתמש בסט ומפה במצבים פשוטים, הדבר מסתבך כאשר נתקלים במגבלות של שימוש בתכונות אוביקט. לדוגמה, מכיוון שכל תכונה חייבות להיות בעלת מזהה מסוג מחרוזת, יש לוודא ששני מזהים נפרדים אינם בעלי אותו ערך. ראה בדוגמה הבאה:

<div dir="ltr">

```js
let map = Object.create(null);

map[5] = "foo";

console.log(map["5"]);      // "foo"
```

</div>

הדוגמה לעיל מבצעת השמה של הערך
`"foo"`
למזהה הנומרי
`5`.
הערך הנומרי מומר למחרוזת, ולכן גם
`map["5"]`
וגם
`map[5]`
למעשה מתייחסים לאותה תכונה. ההמרה הזו יכולה ליצור בעיות כאשר רוצים להשתמש גם במספרים וגם במחרוזות בתור מזהים. בעיה נוספת צצה כאשר משתמשים באוביקטים בתור מזהים, כמו בדוגמה הבאה:

<div dir="ltr">

```js
let map = Object.create(null),
    key1 = {},
    key2 = {};

map[key1] = "foo";

console.log(map[key2]);     // "foo"
```

</div>

בדוגמה לעיל גם 
`map[key2]`
וגם
`map[key1]`
מצביעים על אותו ערך. האוביקטים
`key1`
ו-
`key2`
מומרים למחרוזות כיוון שתכונות אוביקט תמיד מיוצגות בתור מחרוזות. מכיוון שהערך 
`"[object Object]"`
הינו הערך הדיפולטיבי לייצוג אוביקט כמחרוזת, גם 
`key1`
וגם 
`key2`
מומרים לאותה מחרוזת. זה יכול ליצור שגיאות כאשר לא מודעים לתופעה.

ההמרה הדיפולטיבית למחרוזת מקשה על השימוש באוביקט בתור מזהה.
(אותה בעיה קיימת גם כאשר מנסים להשתמש באוביקט בתור סט)

מפות עם מזהים שערכם ״שקרי״ גם הן עם בעיה משלהן.
ערך ״שקרי״ מומר לערך
`false`
במצבים בהם דרוש ערך בוליאני, כמו בתנאים בתוך בתוך פקודת
`if`. 
ההמרה לבדה אינה הבעיה כל עוד נזהרים בשימוש בערכים.
לדוגמה:

<div dir="ltr">

```js
let map = Object.create(null);

map.count = 1;

// בדיקת קיום של המזהה
// "count"
// או בדיקת קיום של ערך שאינו אפס?
if (map.count) {
    // ...
}
```
</div>

בדוגמה לעיל קיימת חוסר בהירות לגבי השימוש של
`map.count`.
האם תנאי
 `if`
נועד לבדוק את נוכחות המזהה
`map.count`
או שמא נועד לבדוק שהערך איננו 0?
הקוד בתוך תנאי 
`if`
ירוץ מכיוון שהערך
`1`
נחשב ערך ״אמת״ תחת תנאי
 `if`.
אם הערך עבור 
`map.count`
הוא 0, או אם
`map.count`
לא קיים,
הקוד בתוך תנאי
`if`
לא ירוץ.

אלו הן בעיות קשות לזיהוי ופתרון כאשר הן קורות במערכות גדולת, וזוהי סיבה מספיק טובה לכך שאקמהסקריפט 6 מוסיפה לשפה סטים ומפות.

I> בג׳אווהסקריפט קיים האופרטור
`in`
שמחזיר את הערך
`true`
במידה ותכונה קיימת על אוביקט מבלי אף לקרוא את הערך. אך האופרטור
`in`
גם מחפש בתוך הפרוטוטיפ של האוביקט מה שהופך אותו לבטוח לשימוש רק כאשר לאוביקט יש את הפרוטוטיפ 
`null`
למרות זאת, מפתחים רבים עדיין משתמשים בקוד בצורה שגויה כבדוגמה האחרונה במקום להשתמש באופרטור
`in`.

## סטים באקמהסקריפט 6

אקמהסקריפט 6 מוסיפה סוג משתנה בשם
`Set`
שנחשב לרשימה מוסדרת של ערכים ללא כפילויות.
סטים מאפשרים גישה מהירה למידע שבתוכם.

### יצירת סטים והוספת פריטים

סטים נוצרים באמצעות פקודת
<span dir="ltr">`new Set()`</span>
ופריטים נוספים לסט על ידי קריאת המתודה
<span dir="ltr">`add()`</span>.
ניתן לראות כמה פריטים קיימים בסט על ידי בדיקת התכונה
`size`:

<div dir="ltr">

```js
let set = new Set();
set.add(5);
set.add("5");

console.log(set.size);    // 2
```

</div>

סטים לא מאלצים ערכים לסוג אחר של ערך כדי לבדוק אם הם זהים. כלומר סט יכול להכיל הן את המספר
`5`
והן את המחרוזת
`"5"`
בתור פריטים נפרדים.
(
    היוצא דופן היחיד הוא שהערכים
    -0
    והערך
    +0
    נחשבים זהים
)
ניתן להוסיף אוביקטים לסט ואותם אוביקטים יישארו נבדלים מהשאר:

<div dir="ltr">

```js
let set = new Set(),
    key1 = {},
    key2 = {};

set.add(key1);
set.add(key2);

console.log(set.size);    // 2
```

</div>

מכיוון והאוביקטים
`key1`
ו-
`key2`
לא מומרים למחרוזות, הם נחשבים לשני פריטים נבדלים בסט.
(
    אם הם היו מומרים למחרוזות, שניהם היו שווים לערך
    `"[object Object]"`
)

אם המתודה
<span dir="ltr">`add()`</span>
נקראת יותר מפעם אחת עם אותו ערך, כל יתר הקריאות לאחר הראשונה אינן משפיעות:

<div dir="ltr">

```js
let set = new Set();
set.add(5);
set.add("5");
set.add(5);     // כפילות - מתעלמים

console.log(set.size);    // 2
```

</div>

ניתן לאתחל סט באמצעות מערך, והקונסטרקטור יבטיח שרק ערכים ייחודיים יופיעו בתוצאה. לדוגמה:

<div dir="ltr">

```js
let set = new Set([1, 2, 3, 4, 5, 5, 5, 5]);
console.log(set.size);    // 5
```

</div>

בדוגמה לעיל, מערך עם כפילויות משמש לאתחול הסט. המספר

מופיע בסט רק פעם אחת למרות שהוא מופיע 4 פעמים במערך. ההתנהגות הזו מקלה על המרת קוד קיים או נתונים מסוג
JSON
לסטים.

I> הקונסטרקטור של
`Set`
מקבל כל אוביקט איטרבילי כארגומנט. מערכים מתקבלים כיוון שהם איטרבילים, כמו גם סטים ומפות. הקונסטרקטור 
`Set`
משתמש באיטרטור כדי לשלוף ערכים מתוך הארגומנט.
(
    איטרטורים ואיטרבילים מוסברים בהרחבה בפרק 8
)

ניתן לבדוק אלו ערכים נמצאים בתוך סט בעזרת המתודה
<span dir="ltr">`has()`</span>,
כמו בדוגמה הבאה:

<div dir="ltr">

```js
let set = new Set();
set.add(5);
set.add("5");

console.log(set.has(5));    // true
console.log(set.has(6));    // false
```

</div>

בדוגמה לעיל קריאה לפונקציה
<span dir="ltr">`set.has(6)`</span>
תחזיר את הערך
false
מפני שלסט אין את הערך 6

### הסרת ערכים

ניתן להסיר ערכים מסט. ניתן להסיר ערך בודד על ידי המתודה 
<span dir="ltr">`delete()`</span>

או להסיר את כל הערכים מהסט בעזרת המתודה
<span dir="ltr">`clear`</span>

הדוגמה הבאה מראה שימוש של שתי השיטות:

<div dir="ltr">

```js
let set = new Set();
set.add(5);
set.add("5");

console.log(set.has(5));    // true

set.delete(5);

console.log(set.has(5));    // false
console.log(set.size);      // 1

set.clear();

console.log(set.has("5"));  // false
console.log(set.size);      // 0
```

</div>

לאחר הקריאה למתודה
<span dir="ltr">`delete()`</span>
רק הערך

נמחק.
לאחר הקריאה למתודה
<span dir="ltr">`clear()`</span>
אנו רואים שהמשתנה 
`set`
התרוקן מערכים.

כל המתואר לעיל מעניק לנו מנגנון קל לשימוש עבור סידור ערכים ייחודיים.
אך מה אם נרצה להוסיף פריטים לסט ואז לבצע פעולה על כל פריט? כאן נכנסת לפעולה המתודה
<span dir="ltr">`forEach()`</span>.

### forEach

אם התרגלתם לעבוד עם מערכים, אז ייתכן שאתם כבר מכירים את המתודה
<span dir="ltr">`forEach()`</span>.
של מערך.
אקמהסקריפט 5 הוסיפה את
<span dir="ltr">`forEach()`</span>
כדי לאפשר מעבר על כל פריט במערך מבלי להגדיר לולאות
`for`.
המתודה הפכה לפופולרית בקרב מפתחים ולכן המתודה קיימת גם בסט ופועלת באותו אופן.

המתודה 
<span dir="ltr">`forEach()`</span>
מקבלת פונקציית קולבק שמקבלת 3 ארגומנטים

1. הערך הבא בסט
1. אותו ערך כמו הארגומנט הראשון
1. הסט ממנו מגיע הערך

ההבדל בין גרסת
<span dir="ltr">`forEach()`</span>
של סט לבין זו של מערך היא שהארגומנט הראשון והשני בפונקציית הקולבק זהים. זו אינה טעות וקיימת סיבה טובה לכך.

אוביקטים אחרים בעלי מתודת
<span dir="ltr">`forEach()`</span>
(מערכים ומפות)
מעבירים 3 ארגומנטים לפונקציית הקולבק. 2 הארגומנטים הראשונים עבור מערכים ומפות הם הערך והמזהה
(האינדקס הנומרי בעבור מערכים).

לסט אין מזהים עבור הערכים. האנשים מאחורי אקמהסקריפט 6 היו יכולים להגדיר את פונקציית הקולבק בגרסת

של סט כך שתקבל 2 ארגומנטים, אבל רצו לשמור את פונקציית הקולבק שתשמור על חתימה זהה לגרסאות האחרות. לכן הוחלט שכל ערך בתוך סט ייחשב בתור מזהה וגם בתור ערך. ולכן הארגומנט הראשון זהה לארגומנט השני בתוך 
<span dir="ltr">`forEach()`</span>
של סט, וזאת כדי לשמור על התנהגות שתואמת למתודות
<span dir="ltr">`forEach()`</span>
האחרות על מערך ומפה

מלבד ההבדל שתואר לעיל לגבי הארגומנטים, שימוש ב 
<span dir="ltr">`forEach()`</span>
של סט הינו זהה לזה שיש במערך.
לדוגמה:

<div dir="ltr">

```js
let set = new Set([1, 2]);

set.forEach(function(value, key, ownerSet) {
    console.log(key + " " + value);
    console.log(ownerSet === set);
});
```

</div>

הקוד עובר על כל פריט בסט ומדפיס את הערכים שמועברים לפונקציית הקולבק. כל פעם שהפונקציה רצה, 
`key` 
ו-
`value`
זהים,
ו-
`ownerSet`
שווה לערך של
`set`.
התוצאה של הקוד היא:

<div dir="ltr">

```
1 1
true
2 2
true
```

</div>

בדומה למערכים, ניתן להעביר ערך עבור
`this`
בתור הארגומנט השני של
<span dir="ltr">`forEach()`</span>
שישמש בתור הערך של 
`this`
בתוך פונקציית הקולבק:

<div dir="ltr">

```js
let set = new Set([1, 2]);

let processor = {
    output(value) {
        console.log(value);
    },
    process(dataSet) {
        dataSet.forEach(function(value) {
            this.output(value);
        }, this);
    }
};

processor.process(set);
```

</div>

בדוגמה זו, המתודה
<span dir="ltr">`processor.process()`</span>
קוראת למתודה
<span dir="ltr">`forEach()`</span>
של הסט ומעבירה את 
`this`
הנוכחי בתור הערך שישמש בתור 
`this`
בתוך פונקציית הקולבק.
זה הכרחי על מנת שהקוד
<span dir="ltr">`this.output()`</span>
יתייחס למתודה
<span dir="ltr">`processor.output()`</span>.
פונקציית הקולבק של
<span dir="ltr">`forEach()`</span>
משתמשת רק בארגומנט הראשון,
`value`,
ולכן הארגומנטים האחרים הושמטו. ניתן גם להשתמש בפונקציית חץ כדי להשיג את אותו אפקט מבלי להעביר ארגומנט שני, כמו בדוגמה הבאה:

<div dir="ltr">

```js
let set = new Set([1, 2]);

let processor = {
    output(value) {
        console.log(value);
    },
    process(dataSet) {
        dataSet.forEach((value) => this.output(value));
    }
};

processor.process(set);
```

</div>

פונקציית החץ בדוגמה לעיל קוראת את הערך
`this`
מתוך פונקציית 
<span dir="ltr">`process()`</span>
העוטפת ולכן היא תתייחס בתוכה לקוד
<span dir="ltr">`this.output()`</span>
כמו אל קריאה לפונקציה
<span dir="ltr">`processor.output()`</span>.

יש לזכור שאף על פי שסטים נותנים פתרון מצוין למעקב אחר ערכים והמתודה 
<span dir="ltr">`forEach()`</span>
מאפשרת לנו לעבוד על ערכים באופן סדרתי, לא ניתן לגשת לערך באמצעות אינדקס נומרי כמו שניתן לעשות במערך. אם צריך לעשות זאת, הפתרון הטוב ביותר הוא להפוך את הסט למערך.

### המרת סט למערך

קל להמיר מערך לסט כיוון שניתן להעביר מערך כמות שהוא לתוך קונסטרטור
`Set`.
קל גם להמיר סט למערך על ידי שימוש באופרטור הפיזור
(spread operator).
פרק 3 הציג את אופרטור הפיזור
(`...`)
בתור דרך לפצל פריטים במערך לפרמטרים נפרדים בקריאה לפונקציה. ניתן גם להשתמש באופרטור הפיזור על אוביקט איטרבילי, כמו סט, כדי להפוך אותם למערך. לדוגמה:

<div dir="ltr">

```js
let set = new Set([1, 2, 3, 3, 3, 4, 5]),
    array = [...set];

console.log(array);             // [1,2,3,4,5]
```

</div>

בדוגמה זו סט נוצר על ידי העברת מערך שמכיל כפילויות. הסט מסלק את הכפילויות ואז הפריטים מושמים לתוך מערך חדש בעזרת אופרטור הפיזור. הסט עצמו עדיין מכיל את אותם הפריטים
(`1`, `2`, `3`, `4`, `5`)
שקיבל כאשר נוצר. 
הם פשוט הועתקו לתוך מערך חדש.

הטכניקה הזו שימושית כאשר יש לנו כבר מערך ורוצים להסיר ממנו כפילויות. לדוגמה:

<div dir="ltr">

```js
function eliminateDuplicates(items) {
    return [...new Set(items)];
}

let numbers = [1, 2, 3, 3, 3, 4, 5],
    noDuplicates = eliminateDuplicates(numbers);

console.log(noDuplicates);      // [1,2,3,4,5]
```

</div>

בתוך הפונקציה
<span dir="ltr">`eliminateDuplicates()`</span>,
הסט משמש בתור תווך זמני לסינון הכפילויות לפני יצירת מערך חדש ללא כפילויות.

### סט חלש

`Set`
יכול להיקרא בתור סט חזק, בגלל האופן בה הוא שומר מצביעים לאוביקט. אוביקט ששמור שתוך סט מייצר את אותה תופעה כמו שמירת האוביקט בתוך משתנה. כל עוד קיים מצביע לאותו מופע של 
`Set` 
לא ניתן לשחרר את הזכרון ולאסוף את האוביקט לפח האשפה. לדוגמה:

<div dir="ltr">

```js
let set = new Set(),
    key = {};

set.add(key);
console.log(set.size);      // 1

// ביטול המצביע המקורי
key = null;

console.log(set.size);      // 1

// שחזור המצביע המקורי
key = [...set][0];
```

</div>

בדוגמה זו, הגדרת 
`key`
לערך
`null`
מסלקת מצביע אחד של האוביקט
`key`
אבל מצביע אחר נשאר בתוך 
`set`.
עדיין אפשר לחלץ את 
`key`
על ידי המרת הסט למערך בעזרת אופרטור הפיזור וקריאת הפריט הראשון. התוצאה עובדת היטב בעבור רוב הפעמים, אבל לפעמים נעדיף שמצביעים בתוך סט ייעלמו כאשר מצביעים מחוץ לסט ייעלמו. למשל, אם הקוד שלנו רץ בתוך דפדפן ונרצה לעקוב אחר אלמנטים ב 
DOM
שאולי יימחקו על ידי סקריפט אחר, לא נרצה שהקוד שלנו יחזיק את המצביע האחרון לאלמנט
DOM
(
    המצב הזה נקרא
    *דליפת זכרון*,
    או באנגלית
    *memory leak*
)

כדי להתגבר על בעיות אלו אקמהסקריפט 6 הוסיפה לשפה מה שנקרא
*סט חלש*
(*weak set*)
ששומר בתוכו מצביעים חלשים ואינו יכול לשמור ערכים פרימיטיביים.
*מצביע חלש*
(*weak reference*)
לאוביקט לא מונע איסוף זבל במידה והוא המצביע היחיד שנותר.

#### יצירת סט חלש

סט חלש נוצר בעזרת הקונסטרקטור
`WeakSet`
ויש לו את המתודות
<span dir="ltr">`add()`, `has()`, `delete()`</span>.
הדוגמה הבאה מדגימה שימוש בשלושת המתודות:

<div dir="ltr">

```js
let set = new WeakSet(),
    key = {};

// הוספת אוביקט לסט
set.add(key);

console.log(set.has(key));      // true

set.delete(key);

console.log(set.has(key));      // false
```

</div>

שימוש בסט חלש דומה מאוד לשימוש בסט רגיל. ניתן להוסיף, להסיר ולבדוק קיום מצביעים בסט חלש. ניתן גם לאתחל סט חלש עם ערכים על ידי העברת אוביקט איטרבילי לקונסטרקטור:

<div dir="ltr">

```js
let key1 = {},
    key2 = {},
    set = new WeakSet([key1, key2]);

console.log(set.has(key1));     // true
console.log(set.has(key2));     // true
```
</div>

בדוגמה לעיל, מערך מועבר לקונסטרקטור 
`WeakSet`.
מכיוון והמערך מכיל שני אוביקטים, שניהם מתווספים לסט החלש. זכרו שתיזרק שגיאה אם המערך יכיל ערך שאינו אוביקט, מאחר והקונסטרקטור
`WeakSet`
לא מקבל ערכים פרימיטיביים.

#### הבדלים מהותיים בין סוגי סטים

ההבדל המהותי ביותר בין סט חלש לסט רגיל הוא המצביע החלש עבור אוביקט. להלן דוגמה שממחישה את ההבדל:

<div dir="ltr">

```js
let set = new WeakSet(),
    key = {};

// הוספת האוביקט לסט
set.add(key);

console.log(set.has(key));      // true

// הסרת המצביע החזק למזהה, מסיר גם מתוך הסט החלש
key = null;
```

</div>

לאחר שהקוד רץ המצביע של
`key`
בסט החלש אינו נגיש עוד. בלתי אפשרי לוודא שהוא אכן הוסר כיוון שהדבר יצריך מצביע אחד לפחות שיועבר למתודה
<span dir="ltr">`has()`</span>.
לכן הכנת טסטים לסט חלש יכולה להיות מבלבלת, אך ניתן לסמוך על מנוע הריצה של ג׳אווהסקריפט שיעשה את עבודתו נאמנה.

הדוגמאות הללו מראות שלסט חלש מאפיינים דומים לסט רגיל, אך יש מספר הבדלים מהותיים. ואלו הם:

1. במופע של  
`WeakSet`
המתודה
<span dir="ltr">`add()`</span>.
זורקת שגיאה כאשר היא מקבלת ערך שאינו אוביקט
(
    <span dir="ltr">`has()`</span>
    ו-
    <span dir="ltr">`delete()`</span>
    מחזירים את הערך
    `false`
    עבור ארגומנט שאינו אוביקט
)

1. סט חלש אינו איטרבילי ולכן לא יכול לשמש כערך עבור לולאת
`for-of`
1. סט חלש לא חושף איטרטורים למפתחים
(
    כמו מתודות בסגנון
    <span dir="ltr">`keys()`, `values()`</span>
)
כך שאין דרך עבור מפתחים לבחון את תוכן הסט
1. לסט חלש אין את מתודת
<span dir="ltr">`forEach()`</span>

1. לסט חלש אין את התכונה
`size`

היכולות המוגבלות למראה של סט חלש נחוצות כדי לטפל בבעיות זיכרון. באופן עקרוני, אם ברצונכם רק לעקוב אחר מצביעים לאוביקטים, עדיף להשתמש בסט חלש יותר מאשר סט רגיל.

סט נותן לנו דרך חדשה לטפל ברשימות ערכים, אך אינו שימושי במיוחד כאשר רוצים לקשר מידע נוסף עם אותם ערכים. מסיבה זו בדיוק אקמהסקריפט 6 מוסיפה גם מפות בנוסף לסטים.

## מפות באקמהסקריפט 6

האוביקט המובנה החדש
`Map`
באקמהסקריפט 6
מייצג רשימה מוסדרת של זוגות מזהה-ערך, כאשר גם המזהה וגם הערך יכולים להיות מכל סוג. בדיקת שוויון עבור מזהה נקבעת באותה צורה כמו עבור אוביקטים מסוג
`Set`,
כך שאפשר להשתמש במזהה הנומרי
`5`
כמו גם במזהה מחרוזת
`"5"`
מכיוון והם נחשבים לסוגים שונים. זה שונה מאוד משימוש בתכונות אוביקט בתור מזהים, מאחר ותכונות אוביקט תמיד מומרות למחרוזות.

ניתן להוסיף פריטים למפה בעזרת המתודה
<span dir="ltr">`set()`</span>
שאליה מעבירים מזהה ואת הערך המקושר אליו. ניתן בהמשך לשלוף ערך מהמפה על ידי העברת המזהה למתודה
<span dir="ltr">`get()`</span>
לדוגמה:


<div dir="ltr">

```js
let map = new Map();
map.set("title", "Understanding ES6");
map.set("year", 2016);

console.log(map.get("title"));      // "Understanding ES6"
console.log(map.get("year"));       // 2016
```

</div>

בדוגמה לעיל נשמרים שני זוגות מזהה-ערך.
המזהה
`"title"`
מקושר לערך מחרוזת
בעוד שהמזהה
`"year"`
מקושר לערך נומרי.
המתודה
<span dir="ltr">`get()`</span>
נקראת כדי לשלוף את הערכים המקושרים לשני המזהים. במידה ומזהה אינו קיים 
<span dir="ltr">`get()`</span>
תחזיר את הערך המיוחד
`undefined`.

ניתן להשתמש באוביקטים בתור מזהים, מה שלא אפשר לעשות כאשר משתמשים בתכונות אוביקט בגישה הישנה.
לדוגמה:

<div dir="ltr">

```js
let map = new Map(),
    key1 = {},
    key2 = {};

map.set(key1, 5);
map.set(key2, 42);

console.log(map.get(key1));         // 5
console.log(map.get(key2));         // 42
```

</div>

הקוד בדוגמה משתמש באוביקטים
`key1` 
ו-
`key2`
בתור מזהים במפה שמקושרים אל ערכים שונים. מכיוון והמזהים לא מומרים לסוג ערך אחר, כל אוביקט נחשב ייחודי.
זה מאפשר לנו לקשר נתונים נוספים לאוביקט מבלי לשנות את האוביקט עצמו.

### מתודות של מפה

למפה יש מספר מתודות משותפות עם סט. זה נעשה בכוונה, ומאפשר לנו לעבוד עם מפה ועם סט בצורה דומה. שלוש המתודות הבאות זמינות במפה וגם בסט:

* <span dir="ltr">`has(key)`</span> -
בודקת האם מזהה נתון מופיע במפה
* <span dir="ltr">`delete(key)`</span> -
מסירה את המזהה והערך המקושר אליו מתוך המפה
* <span dir="ltr">`clear()`</span> -
מסירה את כל המזהים והערכים מתוך המפה

למפה יש תכונה בשם
`size`
שערכה שווה למספר זוגות מזהה-ערך שהיא מכילה.
הדוגמה הבאה משתמשת ב-3 המתודות ובתכונה
`size`

<div dir="ltr">

```js
let map = new Map();
map.set("name", "Nicholas");
map.set("age", 25);

console.log(map.size);          // 2

console.log(map.has("name"));   // true
console.log(map.get("name"));   // "Nicholas"

console.log(map.has("age"));    // true
console.log(map.get("age"));    // 25

map.delete("name");
console.log(map.has("name"));   // false
console.log(map.get("name"));   // undefined
console.log(map.size);          // 1

map.clear();
console.log(map.has("name"));   // false
console.log(map.get("name"));   // undefined
console.log(map.has("age"));    // false
console.log(map.get("age"));    // undefined
console.log(map.size);          // 0

```

</div>

כמו בסט, התכונה
`size`
תמיד מכילה את מספר זוגות מזהה-ערך שבמפה. מופע 
`Map`
בדוגמה זו מתחיל עם המזהים 
`"name"`
ו-
`"age"`,
לכן המתודה
<span dir="ltr">`has()`</span>
מחזירה
`true`
כאשר היא מקבלת כארגומנט כל אחד מהמזהים. לאחר שהמזהה
`"name"`
מסולק על ידי מתודת
<span dir="ltr">`delete()`</span>
המתודה
<span dir="ltr">`has()`</span>
מחזירה את הערך
`false`
כאשר היא מקבלת כארגומנט את הערך
`"name"`
והתכונה
`size`
מראה שפריט אחד הוסר. המתודה
<span dir="ltr">`clear()`</span>
מסירה את המזהה הנותר, כפי שניתן לראות מהמתודה
<span dir="ltr">`has()`</span>
שמחזירה
`false`
עבור שני המזהים
והתכונה
`size`
ששווה ל-0

המתודה
<span dir="ltr">`clear()`</span>
היא הדרך המהירה ביותר להסיר את כל הנתונים מהמפה, אבל קיימת גם דרך לאתחל מפה עם כמות נתונים גדולה.

### אתחול מפה

בדומה לסט, ניתן לאתחל מפה עם נתונים על ידי העברת מערך לקונסטרקטור
`Map`.
כל פריט במערך חייב להיות בעצמו מערך כאשר הפריט הראשון במערך הפנימי הוא המזהה והפריט השני הוא הערך המקושר לאותו מזהה. המפה עצמה היא מערך של אותם מערכים קטנים בני 2 פריטים.
לדוגמה:

<div dir="ltr">

```js
let map = new Map([["name", "Nicholas"], ["age", 25]]);

console.log(map.has("name"));   // true
console.log(map.get("name"));   // "Nicholas"
console.log(map.has("age"));    // true
console.log(map.get("age"));    // 25
console.log(map.size);          // 2
```

</div>

המזהים
`"name"`
ו-
`"age"`
התווספו ל
`map`
על ידי אתחול הקונסטרקטור.
בעוד שמערך של מערכים נראה קצת מוזר, הוא נחוץ למען ייצוג מדויק של מזהים, כיוון שמזהה יכול להיות מכל סוג נתון.
שמירת המזהים בתוך מערך היא הדרך היחידה לוודא שהם לא מומרים לסוג נתונים אחר לפני שמירה בתוך המפה.

### מתודת forEach של מפה

מתודת
<span dir="ltr">`forEach()`</span>
במפה דומה ל 
<span dir="ltr">`forEach()`</span>
של סט ושל מערך, בכך שהיא מקבלת כארגומנט פונקציית קולבק שמקבלת 3 ארגומנטים:

1. הערך הבא של המפה לפי סדר ההשמה
1. המזהה עבור אותו ערך
1. המפה שממנה נקרא הערך

הארגומנטים הללו עבור פונקציית הקולבק תואמים להתנהגות המתודה

במערכים, היכן שהארגומנט הראשון הוא הערך והשני הוא המזהה
(
    שנחשב לאינדקס נומרי במערכים
).
ראו דוגמה:


<div dir="ltr">

```js
let map = new Map([ ["name", "Nicholas"], ["age", 25]]);

map.forEach(function(value, key, ownerMap) {
    console.log(key + " " + value);
    console.log(ownerMap === map);
});
```

</div>

פונקציית הקולבק עבור
<span dir="ltr">`forEach()`</span>
מדפיסה את מה שמועבר לתוכה. 
`value`
ו-
`key`
מודפסים באופן ישיר,
ואילו
`ownerMap`
עובר השוואה אל
`map`
כדי להראות שהערכים זהים.
הפלט יהיה:

```
name Nicholas
true
age 25
true
```

פונקציית הקולבק עבור
<span dir="ltr">`forEach()`</span>
מקבלת כל זוג מזהה-ערך לפי הסדר שבו הוכנסו לתוך המפה. התנהגות זו שונה במקצת מהצורה שבה
<span dir="ltr">`forEach()`</span>
עובדת במערך, שם פונקציית הקולבק מקבלת כל פריט לפי סדר האינדקס הנומרי.

I> ניתן להעביר ארגומנט שני אל
<span dir="ltr">`forEach()`</span>
עבור ערך
`this`
בתוך פונקציית הקולבק. קריאה כזו לפונקציה תעבוד באותה צורה כמו הגרסה של
<span dir="ltr">`forEach()`</span>
שנמצאת על סט

### מפה חלשה

מפה חלשה עבור מפות היא כמו סט חלש עבור סטים: דרך לשמור על מצביע חלש לאוביקט.
בתוך
*מפה חלשה*
(*weak map*),
כל מזהה חייב להיות אוביקט
(נזרקת שגיאה אם מנסים להשתמש במזהה שאיננו אוביקט),
ואותם מצביעים נשמרים באופן חלש כך שאינם מפריעים לשחרור זיכרון
(garbage collection). 
כאשר אין עוד מצביעים למזהה בתוך מפה חלשה גם מחוצה למפה, הזוג מזהה-ערך מסולק מהמפה החלשה.

הדרך היעילה ביותר להשתמש במפה חלשה היא עבור יצירת אוביקט שמשויך לאלמנט 
DOM
מסוים בדף אינטרנט. לדוגמה, מספר ספריות ג׳אווהסקריפט עבור הדפדפן שומרות בזכרון אוביקט משלהן עבור כל אלמנט
DOM
שמצביעים עליו בספריה, והמיפוי בין האוביקטים נשמר בזכרון פנימי.

החלק הבעייתי בשיטה זו הוא לקבוע מתי אלמנט 
DOM
לא מופיע בדף, כדי שהספריה תוכל למחוק את האוביקט המקושר אליו. אחרת הספריה תמשיך לשמור על המצביע לאותו אלמנט גם לאחר שאין בו צורך מה שיגרום לבעיית דליפת זכרון. שמירת המצביע לאותו אלמנט
DOM
בעזרת מפה חלשה עדיין יאפשר לספריה לקשר אוביקט עצמאי עם כל אלמנט 
DOM
והמפה החלשה תסלק באופן אוטומטי כל אוביקט במפה ברגע שאלמנט
DOM
המקושר אינו קיים.

I> חשוב לשים לב שרק מזהים במפה חלשה נחשבים למצביעים חלשים.
היחס לערכים הוא כרגיל. אוביקט ששמור בתור ערך במפה חלשה ימנע שחרור זכרון גם אם כל שאר המצביעים יימחקו.

#### שימוש במפה חלשה

אוביקט מסוג
`WeakMap`
באקמהסקריפט הינו רשימה לא מסודרת של זוגות מזהה-ערך, כאשר המזהה חייב להיות אוביקט שאינו
`null`
והערך יכול להיות מכל סוג. הממשק עבור
`WeakMap`
דומה מאוד לזה של 
`Map`
בכך שהמתודות
<span dir="ltr">`set()`</span>
ו-
<span dir="ltr">`get()`</span>
משמשות אותנו להוסיף ולשלוף מידע, בהתאמה.

<div dir="ltr">

```js
let map = new WeakMap(),
    element = document.querySelector(".element");

map.set(element, "Original");

let value = map.get(element);
console.log(value);             // "Original"

// הסרת האלמנט
element.parentNode.removeChild(element);
element = null;

// המפה החלשה ריקה בנקודה זו
```

</div>

בדוגמה זו, אנו שומרים זוג מזהה-ערך יחיד.
המזהה
`element`
הינו אלמנט
DOM
שמשמש לשמירת ערך מחרוזת. הערך הזה נשלף על ידי העברת אלמנט
DOM
למתודה
<span dir="ltr">`get()`</span>. 
כאשר אלמנט ה
DOM
מוסר מהמסמך והמשתנה שמצביע עליו משתנה לערך
`null`,
הערך המשויך גם הוא מסולק מהמפה החלשה.

בדומה לסט חלש, אין דרך לוודא שמפה חלשה ריקה, כיוון שאין לה את התכונה
`size`. 
מכיון שאין מצביעים נותרים עבור המזהה, גם לא ניתן לשלוף את הערך על ידי שימוש במתודה
<span dir="ltr">`get()`</span>. 
המפה החלשה חוסמת את הגישה לערך שהיה מקושר לאותו מזהה, וכאשר משחרר הזכרון רץ, הזכרון שנתפס על ידי אותו ערך ישוחרר.

#### אתחול מפה חלשה

כדי לאתחל מפה חלשה, יש להעביר מערך של מערכים לתוך הקונסטרקטור
`WeakMap`.
ממש כמו באתחול מפה רגילה, כל מערך פנימי צריך להכיל שני פריטים, כאשר הפריט הראשון הינו אוביקט שאינו
`null`
והפריט השני הוא הערך
(מכל סוג נתונים).
לדוגמה:

<div dir="ltr">

```js
let key1 = {},
    key2 = {},
    map = new WeakMap([[key1, "Hello"], [key2, 42]]);

console.log(map.has(key1));     // true
console.log(map.get(key1));     // "Hello"
console.log(map.has(key2));     // true
console.log(map.get(key2));     // 42
```

</div>

האוביקטים 
`key1`
ו-
`key2`
משמשים בתור מזהים במפה החלשה והמתודות
<span dir="ltr">`get()`</span>
ו-
<span dir="ltr">`has()`</span>.
תיזרק שגיאה אם הקונסטרקטור
`WeakMap`
מקבל מזהה שאינו אוביקט באחד מזוגות מזהה-ערך.


Weak maps have only two additional methods available to interact with key-value pairs. There is a `has()` method to determine if a given key exists in the map and a `delete()` method to remove a specific key-value pair. There is no `clear()` method because that would require enumerating keys, and like weak sets, that isn't possible with weak maps. This example uses both the `has()` and `delete()` methods:

```js
let map = new WeakMap(),
    element = document.querySelector(".element");

map.set(element, "Original");

console.log(map.has(element));   // true
console.log(map.get(element));   // "Original"

map.delete(element);
console.log(map.has(element));   // false
console.log(map.get(element));   // undefined
```

Here, a DOM element is once again used as the key in a weak map. The `has()` method is useful for checking to see if a reference is currently being used as a key in the weak map. Keep in mind that this only works when you have a non-null reference to a key. The key is forcibly removed from the weak map by the `delete()` method, at which point `has()` returns `false` and `get()` returns `undefined`.

#### Private Object Data

While most developers consider the main use case of weak maps to be associated data with DOM elements, there are many other possible uses (and no doubt, some that have yet to be discovered). One practical use of weak maps is to store data that is private to object instances. All object properties are public in ECMAScript 6, and so you need to use some creativity to make data accessible to objects, but not accessible to everything. Consider the following example:

```js
function Person(name) {
    this._name = name;
}

Person.prototype.getName = function() {
    return this._name;
};
```

This code uses the common convention of a leading underscore to indicate that a property is considered private and should not be modified outside the object instance. The intent is to use `getName()` to read `this._name` and not allow the `_name` value to change. However, there is nothing standing in the way of someone writing to the `_name` property, so it can be overwritten either intentionally or accidentally.

In ECMAScript 5, it's possible to get close to having truly private data, by creating an object using a pattern such as this:

```js
var Person = (function() {

    var privateData = {},
        privateId = 0;

    function Person(name) {
        Object.defineProperty(this, "_id", { value: privateId++ });

        privateData[this._id] = {
            name: name
        };
    }

    Person.prototype.getName = function() {
        return privateData[this._id].name;
    };

    return Person;
}());
```

This example wraps the definition of `Person` in an IIFE that contains two private variables, `privateData` and `privateId`. The `privateData` object stores private information for each instance while `privateId` is used to generate a unique ID for each instance. When the `Person` constructor is called, a nonenumerable, nonconfigurable, and nonwritable `_id` property is added.

Then, an entry is made into the `privateData` object that corresponds to the ID for the object instance; that's where the `name` is stored. Later, in the `getName()` function, the name can be retrieved by using `this._id` as the key into `privateData`. Because `privateData` is not accessible outside of the IIFE, the actual data is safe, even though `this._id` is exposed publicly.

The big problem with this approach is that the data in `privateData` never disappears because there is no way to know when an object instance is destroyed; the `privateData` object will always contain extra data. This problem can be solved by using a weak map instead, as follows:

```js
let Person = (function() {

    let privateData = new WeakMap();

    function Person(name) {
        privateData.set(this, { name: name });
    }

    Person.prototype.getName = function() {
        return privateData.get(this).name;
    };

    return Person;
}());
```

This version of the `Person` example uses a weak map for the private data instead of an object. Because the `Person` object instance itself can be used as a key, there's no need to keep track of a separate ID. When the `Person` constructor is called, a new entry is made into the weak map with a key of `this` and a value of an object containing private information. In this case, that value is an object containing only `name`. The `getName()` function retrieves that private information by passing `this` to the `privateData.get()` method, which fetches the value object and accesses the `name` property. This technique keeps the private information private, and destroys that information whenever an object instance associated with it is destroyed.

#### Weak Map Uses and Limitations

When deciding whether to use a weak map or a regular map, the primary decision to consider is whether you want to use only object keys. Anytime you're going to use only object keys, then the best choice is a weak map. That will allow you to optimize memory usage and avoid memory leaks by ensuring that extra data isn't kept around after it's no longer accessible.

Keep in mind that weak maps give you very little visibility into their contents, so you can't use the `forEach()` method, the `size` property, or the `clear()` method to manage the items. If you need some inspection capabilities, then regular maps are a better choice. Just be sure to keep an eye on memory usage.

Of course, if you only want to use non-object keys, then regular maps are your only choice.

## Summary

ECMAScript 6 formally introduces sets and maps into JavaScript. Prior to this, developers frequently used objects to mimic both sets and maps, often running into problems due to the limitations associated with object properties.

Sets are ordered lists of unique values. Values are not coerced to determine equivalence. Sets automatically remove duplicate values, so you can use a set to filter an array for duplicates and return the result. Sets aren't subclasses of arrays, so you cannot randomly access a set's values. Instead, you can use the `has()` method to determine if a value is contained in the set and the `size` property to inspect the number of values in the set. The `Set` type also has a `forEach()` method to process each set value.

Weak sets are special sets that can contain only objects. The objects are stored with weak references, meaning that an item in a weak set will not block garbage collection if that item is the only remaining reference to an object. Weak set contents can't be inspected due to the complexities of memory management, so it's best to use weak sets only for tracking objects that need to be grouped together.

Maps are ordered key-value pairs where the key can be any data type. Similar to sets, keys are not coerced to determine equivalence, which means you can have a numeric key `5` and a string `"5"` as two separate keys. A value of any data type can be associated with a key using the `set()` method, and that value can later be retrieved by using the `get()` method. Maps also have a `size` property and a `forEach()` method to allow for easier item access.

Weak maps are a special type of map that can only have object keys. As with weak sets, an object key reference is weak and doesn't prevent garbage collection when it's the only remaining reference to an object. When a key is garbage collected, the value associated with the key is also removed from the weak map. This memory management aspect makes weak maps uniquely suited for correlating additional information with objects whose lifecycles are managed outside of the code accessing them.
