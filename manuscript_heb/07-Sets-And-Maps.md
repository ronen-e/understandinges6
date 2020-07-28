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

</div>

#### Creating a Weak Set

Weak sets are created using the `WeakSet` constructor and have an `add()` method, a `has()` method, and a `delete()` method. Here's an example that uses all three:

```js
let set = new WeakSet(),
    key = {};

// add the object to the set
set.add(key);

console.log(set.has(key));      // true

set.delete(key);

console.log(set.has(key));      // false
```

Using a weak set is a lot like using a regular set. You can add, remove, and check for references in the weak set. You can also seed a weak set with values by passing an iterable to the constructor:

```js
let key1 = {},
    key2 = {},
    set = new WeakSet([key1, key2]);

console.log(set.has(key1));     // true
console.log(set.has(key2));     // true
```

In this example, an array is passed to the `WeakSet` constructor. Since this array contains two objects, those objects are added into the weak set. Keep in mind that an error will be thrown if the array contains any non-object values, since `WeakSet` can't accept primitive values.

#### Key Differences Between Set Types

The biggest difference between weak sets and regular sets is the weak reference held to the object value. Here's an example that demonstrates that difference:

```js
let set = new WeakSet(),
    key = {};

// add the object to the set
set.add(key);

console.log(set.has(key));      // true

// remove the last strong reference to key, also removes from weak set
key = null;
```

After this code executes, the reference to `key` in the weak set is no longer accessible. It is not possible to verify its removal because you would need one reference to that object to pass to the `has()` method. This can make testing weak sets a little confusing, but you can trust that the reference has been properly removed by the JavaScript engine.

These examples show that weak sets share some characteristics with regular sets, but there are some key differences. Those are:

1. In a `WeakSet` instance, the `add()` method throws an error when passed a non-object (`has()` and `delete()` always return `false` for non-object arguments).
1. Weak sets are not iterables and therefore cannot be used in a `for-of` loop.
1. Weak sets do not expose any iterators (such as the `keys()` and `values()` methods), so there is no way to programmatically determine the contents of a weak set.
1. Weak sets do not have a `forEach()` method.
1. Weak sets do not have a `size` property.

The seemingly limited functionality of weak sets is necessary in order to properly handle memory. In general, if you only need to track object references, then you should use a weak set instead of a regular set.

Sets give you a new way to handle lists of values, but they aren't useful when you need to associate additional information with those values. That's why ECMAScript 6 also adds maps.

## Maps in ECMAScript 6

The ECMAScript 6 `Map` type is an ordered list of key-value pairs, where both the key and the value can have any type. Keys equivalence is determined by using the same approach as `Set` objects, so you can have both a key of `5` and a key of `"5"` because they are different types. This is quite different from using object properties as keys, as object properties always coerce values into strings.

You can add items to maps by calling the `set()` method and passing it a key and the value to associate with the key. You can later retrieve a value by passing the key to the `get()` method. For example:

```js
let map = new Map();
map.set("title", "Understanding ES6");
map.set("year", 2016);

console.log(map.get("title"));      // "Understanding ES6"
console.log(map.get("year"));       // 2016
```

In this example, two key-value pairs are stored. The `"title"` key stores a string while the `"year"` key stores a number. The `get()` method is called later to retrieve the values for both keys. If either key didn't exist in the map, then `get()` would have returned the special value `undefined` instead of a value.

You can also use objects as keys, which isn't possible when using object properties to create a map in the old workaround approach. Here's an example:

```js
let map = new Map(),
    key1 = {},
    key2 = {};

map.set(key1, 5);
map.set(key2, 42);

console.log(map.get(key1));         // 5
console.log(map.get(key2));         // 42
```

This code uses the objects `key1` and `key2` as keys in the map to store two different values. Because these keys are not coerced into another form, each object is considered unique. This allows you to associate additional data with an object without modifying the object itself.

### Map Methods

Maps share several methods with sets. That is intentional, and it allows you to interact with maps and sets in similar ways. These three methods are available on both maps and sets:

* `has(key)` - Determines if the given key exists in the map
* `delete(key)` - Removes the key and its associated value from the map
* `clear()` - Removes all keys and values from the map

Maps also have a `size` property that indicates how many key-value pairs it contains. This code uses all three methods and `size` in different ways:

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

As with sets, the `size` property always contains the number of key-value pairs in the map. The `Map` instance in this example starts with the `"name"` and `"age"` keys, so `has()` returns `true` when passed either key. After the `"name"` key is removed by the `delete()` method, the `has()` method returns `false` when passed `"name"` and the `size` property indicates one less item. The `clear()` method then removes the remaining key, as indicated by `has()` returning `false` for both keys and `size` being 0.

The `clear()` method is a fast way to remove a lot of data from a map, but there's also a way to add a lot of data to a map at one time.

### Map Initialization

Also similar to sets, you can initialize a map with data by passing an array to the `Map` constructor. Each item in the array must itself be an array where the first item is the key and the second is that key's corresponding value. The entire map, therefore, is an array of these two-item arrays, for example:

```js
let map = new Map([["name", "Nicholas"], ["age", 25]]);

console.log(map.has("name"));   // true
console.log(map.get("name"));   // "Nicholas"
console.log(map.has("age"));    // true
console.log(map.get("age"));    // 25
console.log(map.size);          // 2
```

The keys `"name"` and `"age"` are added into `map` through initialization in the constructor. While the array of arrays may look a bit strange, it's necessary to accurately represent keys, as keys can be any data type. Storing the keys in an array is the only way to ensure they aren't coerced into another data type before being stored in the map.

### The forEach Method on Maps

The `forEach()` method for maps is similar to `forEach()` for sets and arrays, in that it accepts a callback function that receives three arguments:

1. The value from the next position in the map
1. The key for that value
1. The map from which the value is read

These callback arguments more closely match the `forEach()` behavior in arrays, where the first argument is the value and the second is the key (corresponding to a numeric index in arrays). Here's an example:

```js
let map = new Map([ ["name", "Nicholas"], ["age", 25]]);

map.forEach(function(value, key, ownerMap) {
    console.log(key + " " + value);
    console.log(ownerMap === map);
});
```

The `forEach()` callback function outputs the information that is passed to it. The `value` and `key` are output directly, and `ownerMap` is compared to `map` to show that the values are equivalent. This outputs:

```
name Nicholas
true
age 25
true
```

The callback passed to `forEach()` receives each key-value pair in the order in which the pairs were inserted into the map. This behavior differs slightly from calling `forEach()` on arrays, where the callback receives each item in order of numeric index.

I> You can also provide a second argument to `forEach()` to specify the `this` value inside the callback function. A call like that behaves the same as the set version of the `forEach()` method.

### Weak Maps

Weak maps are to maps what weak sets are to sets: they're a way to store weak object references. In *weak maps*, every key must be an object (an error is thrown if you try to use a non-object key), and those object references are held weakly so they don't interfere with garbage collection. When there are no references to a weak map key outside a weak map, the key-value pair is removed from the weak map.

The most useful place to employ weak maps is when creating an object related to a particular DOM element in a web page. For example, some JavaScript libraries for web pages maintain one custom object for every DOM element referenced in the library, and that mapping is stored in a cache of objects internally.

The difficult part of this approach is determining when a DOM element no longer exists in the web page, so that the library can remove its associated object. Otherwise, the library would hold onto the DOM element reference past the reference's usefulness and cause a memory leak. Tracking the DOM elements with a weak map would still allow the library to associate a custom object with every DOM element, and it could automatically destroy any object in the map when that object's DOM element no longer exists.

I> It's important to note that only weak map keys, and not weak map values, are weak references. An object stored as a weak map value will prevent garbage collection if all other references are removed.

#### Using Weak Maps

The ECMAScript 6 `WeakMap` type is an unordered list of key-value pairs, where a key must be a non-null object and a value can be of any type. The interface for `WeakMap` is very similar to that of `Map` in that `set()` and `get()` are used to add and retrieve data, respectively:

```js
let map = new WeakMap(),
    element = document.querySelector(".element");

map.set(element, "Original");

let value = map.get(element);
console.log(value);             // "Original"

// remove the element
element.parentNode.removeChild(element);
element = null;

// the weak map is empty at this point
```

In this example, one key-value pair is stored. The `element` key is a DOM element used to store a corresponding string value. That value is then retrieved by passing in the DOM element to the `get()` method. When the DOM element is later removed from the document and the variable referencing it is set to `null`, the data is also removed from the weak map.

Similar to weak sets, there is no way to verify that a weak map is empty, because it doesn't have a `size` property. Because there are no remaining references to the key, you can't retrieve the value by calling the `get()` method, either. The weak map has cut off access to the value for that key, and when the garbage collector runs, the memory occupied by the value will be freed.

#### Weak Map Initialization

To initialize a weak map, pass an array of arrays to the `WeakMap` constructor. Just like initializing a regular map, each array inside the containing array should have two items, where the first item is the non-null object key and the second item is the value (any data type). For example:

```js
let key1 = {},
    key2 = {},
    map = new WeakMap([[key1, "Hello"], [key2, 42]]);

console.log(map.has(key1));     // true
console.log(map.get(key1));     // "Hello"
console.log(map.has(key2));     // true
console.log(map.get(key2));     // 42
```

The objects `key1` and `key2` are used as keys in the weak map, and the `get()` and `has()` methods can access them. An error is thrown if the `WeakMap` constructor receives a non-object key in any of the key-value pairs.

#### Weak Map Methods

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
