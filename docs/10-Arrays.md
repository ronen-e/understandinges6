<div dir="rtl">

# יכולות מערך משופרות

אובייקט מערך הינו אובייקט ג'אווהסקריפט במהותו. עם זאת, בעוד היבטים אחרים של ג'אווהסקריפט התפתחו במהלך הזמן, מערכים נותרו זהים עד ש-
ECMAScript 5
הציגה מספר שיטות להפוך אותם לקלים יותר לשימוש.
ECMAScript 6
המשיכה את מגמת השיפור של המערכים על ידי הוספת פונקציונליות רבה יותר, כמו שיטות יצירה חדשות של מערכים והפיכת כמה שיטות שימושיות לנוחות יותר לכתיבת מערכים.

## יצירת מערך

לפני
ECMAScript 6
היו שתי דרכים עיקריות ליצירת מערכים: קונסטרקטור (בנאי) של מערך
(`array`)
ומה שנקרא מערך ליטראלי
(`array literal`).
שתי הגישות מצריכות כתיבת הפריטים המערך בנפרד ובדרך כלל הן מוגבלות למדי.
אפשרויות להמרת אובייקט דמוי מערך
(
כלומר, אובייקט עם אינדקס מספרי ותכונה בשם
`length`
)
למערך היו גם הן מוגבלות ולעיתים קרובות דרשו קוד נוסף.
כדי להקל על יצירת מערכים בג'אווהסקריפט
ECMAScript 6
הוסיפה את המתודות
<span dir="ltr">`Array.of()`</span>
ו-
<span dir="ltr">`Array.from()`</span>

### <span dir="ltr">Array.of()</span>

אחת הסיבות ש-
ECMAScript 6
הוסיפה מתודות חדשות ליצירת מערכים בג'אווהסקריפט הייתה רצון לסייע למפתחים להמנע מהצורך להשתמש בקונסטרקטור של מערך.
הקונסטרקטור
<span dir="ltr">`new Array()`</span>
מתנהג באופן שונה בהתבסס על סוג וכמות הארגומנטים שהועברו אליו.

לדוגמה:

<span dir="ltr">

```js
let items = new Array(2);
console.log(items.length); // 2
console.log(items[0]); // undefined
console.log(items[1]); // undefined

items = new Array("2");
console.log(items.length); // 1
console.log(items[0]); // "2"

items = new Array(1, 2);
console.log(items.length); // 2
console.log(items[0]); // 1
console.log(items[1]); // 2

items = new Array(3, "2");
console.log(items.length); // 2
console.log(items[0]); // 3
console.log(items[1]); //"2"
```
</span>

כאשר מועבר לקונסטרקטור של המערך ערך מספרי כארגומנט יחיד ערך זה יהווה את ערך התכונה
`length`
של המערך. לעומת זאת, אם מועבר כארגומנט ערך יחיד שאינו מספר, ערך זה יהפוך לפריט האחד והיחיד במערך. אם מספר ערכים (מסוג מספר או לא) מועברים לקונסטרקטור כארגומנטים ערכים אלו יהפכו אף הם לפריטים במערך. התנהגות זו הינה מסוכנת ומבלבלת כיוון שלא תמיד נוכל לדעת אלו סוגים של מידע הועברו לקונסטרקטור בתור ארגומנטים.

ECMAScript 6
שכללה את המתודה
<span dir="ltr">`Array.of()`</span>
כדי לפתור בעיה הזו.
<span dir="ltr">`Array.of()`</span>
פועלת באופו דומה לקונסטרקטור של
`Array`
אך אין לה מקרה מיוחד לגבי ערך מספרי יחיד. המתודה הזו יוצרת תמיד מערך המכיל את הארגומנטים שלו ללא קשר למספר הארגומנטים או לסוגם.
להלן מספר דוגמאות המשתמשות בשיטת
<span dir="ltr">`Array.of()`</span>

<div dir="ltr">

```js
let items = Array.of(1, 2);
console.log(items.length); // 2
console.log(items[0]); // 1
console.log(items[1]); // 2

items = Array.of(2);
console.log(items.length); // 1
console.log(items[0]); // 2

items = Array.of("2");
console.log(items.length); // 1
console.log(items[0]); // "2"
```
</div>

בכדי ליצור מערך באמצעות מתודת
<span dir="ltr">`Array.of()`</span>
פשוט נעביר לה את הערכים הרצויים במערך. כך למשל בדוגמה הראשונה לעיל נוצר מערך המכיל שני מספרים. המערך שבדוגמה השניה מכיל מספר אחד והמערך האחרון מכיל מחרוזת אחת. הדבר דומה לשימוש במערך ליטראלי ואכן ניתן להשתמש במערך ליטראלי במקום
<span dir="ltr">`Array.of()`</span>
ליצירת מערכים מובנים במרבית המקרים.
אך אם נרצה להעביר את הקונסטרקטור של המערך לפונקציה אז יתכן שנרצה להשתמש במתודה
<span dir="ltr">`Array.of()`</span>
על מנת להבטיח התנהגות עקבית.

לדוגמה:

<div dir="ltr">

```js
function createArray(arrayCreator, value) {
  return arrayCreator(value);
}

let items = createArray(Array.of, value);
```
</div>

בקוד זה הפונקציה
<span dir="ltr">`createArray()`</span>
מקבלת פונקציה היוצרת מערך וערך להכניס לתוך המערך. נוכל להעביר את המתודה
<span dir="ltr">`Array.of()`</span>
בתור הארגומנט הראשון לפונקציה
<span dir="ltr">`createArray()`</span>
על מנת ליצור מערך חדש. במקרה זה יהיה זה מסוכן להעביר רק את
`Array`
ישירות מבלי שנוכל להבטיח שהערך
`(value)`
לא יהיה מספר.

I>
- שימו לב: המתודה
<span dir="ltr">`Array.of()`</span>
אינה משתמשת בתכונה
<span dir="ltr">`Symbol.species`</span>
(הנדון בפרק 9)
על מנת לקבוע את סוג הערך המוחזר. במקום זאת היא משתמשת בקונסטרקטור הנוכחי
(
במקרה שלנו מדובר במצביע
`this`
בתוך מתודת ה-
<span dir="ltr">`of()`</span>)
על מנת לקבוע את סוג הנתונים הנכון להחזרה.

### <span dir="ltr">Array.from()</span>

המרת אובייקטים שאינם מערכים אל מערכים הייתה מאז ומתמיד דבר מסורבל בג'אווהסקריפט. לדוגמה, אם יש לנו אובייקט מסוג
`arguments`
(שהוא אובייקט דמוי מערך)
ואנו רוצים להשתמש בו כאילו היה מערך נצטרך להמיר אותו תחילה למערך. כדי להמיר אובייקט דמוי מערך למערך ב-
ECMAScript 5
היה עלינו לכתוב פונקציה כמו זו שבדוגמה הבאה:

<span dir="ltr">

```js
function makeArray(arrayLike) {
  var result = [];

  for (var i = 0; len < arrayLike.length; i++){
    result.push(arrayLike[i]);
  }
  return result;
}

function doSomething() {
  var args = makeArray(arguments);

  // נשתמש במשתנה
  // args
}
```
</span>

דרך זו יוצרת באופן ידני מערך בשם
`result`
ומעתיקה כל פריט מתוך
`arguments`
למערך החדש. זה אומנם עובד אבל מצריך שימוש בכמות קוד לא קטנה לצורך ביצוע פעולה פשוט. בסופו של דבר, מפתחים גילו כי הם יכולים להפחית את כמות הקוד הנדרשת על ידי שימוש במתודת
<span dir="ltr">slice()</span>
לבניית מערכים מאובייקטים דמויי מערך, כמו בדוגמה הבאה:

<div dir="ltr">

```js
function makeArray(arrayLike) {
  return Array.prototype.slice.call(arrayLike);
}

function doSomething() {
  var args = makeArray(arguments);

  // נשתמש במשתנה
  // args
}
```
</div>

קוד זה מקביל מבחינה פונקציונלית לדוגמה הקודמת והוא עובד כיוון שהוא מגדיר את הערך
`this`
עבור
<span dir="ltr">`slice()`</span>
עבור אובייקט דמוי מערך.
מכיוון ש-
<span dir="ltr">`slice()`</span>
זקוק רק לאינדקס נומרי ולתכונה
`length`
כדי לתפקד כראוי ולכן כל אובייקט דמוי מערך יעבוד.

על אף שטכניקה זו מצריכה פחות קוד, הקריאה
<span dir="ltr">`Array.prototype.slice.call(arrayLike)`</span>
לא בהכרח מתורגמת ל-"המר את
arrayLike
למערך".
למרבה המזל,
ECMAScript 6
הוסיפה את המתודה
<span dir="ltr">`Array.from()`</span>
כדרך פשוטה וברורה להמיר אובייקטים למערכים.

בהינתן אובייקט איטרבילי או אובייקט דמוי מערך כארגומנט הראשון, המתודה
<span dir="ltr">`Array.from()`</span>
מחזירה מערך. הנה דוגמה:

<span dir="ltr">

```js
function doSomething() {
  var args = array.from(arguments);

  // נשתמש במשתנה
  // args
}
```
</span>

הקריאה למתודה
<span dir="ltr">`Array.from()`</span>
יוצרת מערך חדש המבוסס על הערכים בתוך
`arguments`
כך שהמשתנה
`args`
הוא מופע של
`Array`
המכיל את אותם הערכים ובאותו הסדר כמו שהם מופיעים באובייקט
`arguments`.

I>
שימו לב: המתודה
<span dir="ltr">`Array.from()`</span>
משתמשת גם במתודה
`this`
על מנת לקבוע את סוג המערך שיש להחזיר.

### המרת מיפוי

אם ברצונך לקחת את המרת המערך צעד נוסף קדימה תוכל לספק למתודה
<span dir="ltr">`Array.from()`</span>
פונקציית מיפוי בתור הארגומנט השני. פונקציה זו פועלת על כל איבר באובייקט דמוי מערך וממירה אותו לצורה סופית בלשהי לפני שהיא שומרת את התוצאה באינדקס המתאים במערך הסופי. לדוגמה:

<div dir="ltr">

```js
function translate() {
    return Array.from(arguments, (value) => value + 1);
}

let numbers = translate(1, 2, 3);

console.log(numbers);   // 2,3,4
```
</div>

בדוגמה זו, למתודת
<span dir="ltr">`Array.from()`</span>
מועברת הפונקציה
<span dir="ltr">(value) => value + 1</span>
כפונקציית מיפוי, אשר מוסיפה 1 לכל פריט במערך לפני אחסון הפריט. אם פונקציית המיפוי נמצאת באובייקט ניתן גם להעביר ארגומנט שלישי למתודה
<span dir="ltr">`Array.from()`</span>
המייצג את הערך
`this`
עבור פונקציית המיפוי.

<div dir="ltr">

```js
let helper = {
    diff: 1,

    add(value) {
        return value + this.diff;
    }
};

function translate() {
    return Array.from(arguments, helper.add, helper);
}

let numbers = translate(1, 2, 3);

console.log(numbers);   // 2,3,4
```
</div>

דוגמה זו מעבירה את הפונקציה
<span dir="ltr">`helper.add()`</span>
כפונקציית מיפוי עבור ההמרה. היות והמתודה
<span dir="ltr">`helper.add()`</span>
משתמשת בתכונה
<span dir="ltr">`this.diff`</span>
עלינו לספק את הארגומנט השלישי ל-
<span dir="ltr">`Array.from()`</span>
המציין את הערך של
`this`.
הודות לארגומנט השלישי,
<span dir="ltr">`Array.from()`</span>
יכול בקלות להמיר נתונים מבלי לקרוא למתודת
<span dir="ltr">`bind()`</span>
או לציין את הערך של
`this`
בדרך אחרת.

### שימוש באובייקטים איטרביליים

המתודה
<span dir="ltr">`Array.from()`</span>
פועלת הן על אובייקטים דמויי מערך והן על אובייקטים איטרבילים. זה אומר שהמתודה מסוגלת להמיר כל אובייקט עם התכונה
<span dir="ltr">`Symbol.iterator`</span>
למערך.
לדוגמא:

<div dir="ltr">

```js
let numbers = {
    *[Symbol.iterator]() {
        yield 1;
        yield 2;
        yield 3;
    }
};

let numbers2 = Array.from(numbers, (value) => value + 1);

console.log(numbers2);              // 2,3,4
```
</div>

מכיוון שהאובייקט
`numbers`
איטרבילי, ניתן להעביר אותו ישירות למתודה
<span dir="ltr">`Array.from()`</span>
על מנת להמיר את ערכיו אל מערך. פונקציית המיפוי מוסיפה 1 לכל מספר, כך שהמערך המתקבל מכיל 2, 3 ו-4 במקום
1, 2
ו-3.

I>
שימו לב: אם אובייקט הוא אובייקט דמוי מערך וגם אובייקט איטרבילי, המתודה
<span dir="ltr">`Array.from()`</span>
משתמשת באיטרטור על מנת לקבוע את הערכים להמרה.

## מתודות חדשות בכל המערכים

בהמשך למגמה שהחלה באקמהסקריפט 5, אקמהסקריפט 6 הוסיפה מספר מתודות חדשות למערכים.
המתודות
<span dir="ltr">`find()`</span>
וכן
<span dir="ltr">`findIndex()`</span>
נועדו לסייע למפתחים להשתמש במערכים עם כל סוג ערכים,
בעוד שהמתודות
<span dir="ltr">`fill()`</span>
ו-
<span dir="ltr">`copyWithin()`</span>
הושפעו משימושים עבור *מערכים בינאריים*, סוג של מערך שהופיע באקמהסקריפט 6 המשתמש רק בערכים נומריים.

### המתודות <span dir="ltr">`find()`</span> ו- <span dir="ltr">`findIndex()`</span>

לפני אקמהסקריפט 5 חיפוש במערכים היה מסורבל כיוון שלא היו מתודות מובנות לשם כך. אקמהסקריפט 5
הוסיפה את המתודות
<span dir="ltr">`indexOf()`</span>
ו-
<span dir="ltr">`lastIndexOf()`</span>,
אשר לבסוף נתנו למפתחים את היכולת לחפש ערכים ספציפיים בתוך מערך.
שתי המתודות הללו היוו שיפור משמעותי, ועדיין היו מוגבלות למדי מכיוון שאפשר היה לחפש רק ערך אחד בכל פעם.
לדוגמה, אם נרצה למצוא את המספר הזוגי הראשון בסדרת מספרים יהיה עלינו לכתוב קוד מיוחד העושה זאת. אקמהסקריפט 6 פתרה את הבעיה בכך שהוסיפה את המתודות
<span dir="ltr">`find()`</span>
ו-
<span dir="ltr">`findIndex()`</span>.

שתי המתודות
<span dir="ltr">`find()`</span>
ו-
<span dir="ltr">`findIndex()`</span>.
מקבלות שני ארגומנטים: הארגומנט הראשון הוא פונקציית קולבק והארגומנט השני הינו ערך אופציונלי, שקובע את הערך
`this`
בתוך פונקציית הקולבק. פונקציית הקולבק מקבלת פריט במערך, יחד עם האינדקס של אותו פריט במערך וכן את המערך עצמו - בדיוק אותם ארגומנטים אשר מועברים למתודות כמו
<span dir="ltr">`map()`</span>
ו-
<span dir="ltr">`forEach()`</span>.
פונקציית הקולבק צריכה להחזיר
`true`
אם הערך תואם לקריטריון שאנו מגדירים.
גם
<span dir="ltr">`find()`</span>
וגם
<span dir="ltr">`findIndex()`</span>
מפסיקות את החיפוש במערך ברגע שפונקציית הקולבק מחזירה את הערך
`true`.

ההבדל היחיד בין שתי המתודות הוא שהמתודה
<span dir="ltr">`find()`</span>
מחזירה את הערך עצמו, ואילו המתודה
<span dir="ltr">`findIndex()`</span>
מחזירה את האינדקס במערך שבו נמצא הערך.

להלן דוגמא להמחשה:

<div dir="ltr">

```js
let numbers = [25, 30, 35, 40, 45];

console.log(numbers.find(n => n > 33));         // 35
console.log(numbers.findIndex(n => n > 33));    // 2
```
</div>

הקוד בדוגמא לעיל קורא למתודות
<span dir="ltr">`find()`</span>
ו-
<span dir="ltr">`findIndex()`</span>
על מנת לאתר את הערך הראשון במערך בשם
`numbers`
שהוא גדול מ-33.
הקריאה ל-
<span dir="ltr">`find()`</span>
מחזירה את המספר 35, ואילו הקריאה ל-
<span dir="ltr">`findIndex()`</span>
מחזירה את המספר 2, שהוא המיקום (האינדקס) של המספר 35 בתוך המערך.

שתי המתודות הנ"ל שימושיות למציאת אלמנט במערך התואמים לתנאי מסויים. אם רק נרצה למצוא ערך אז המתודות
<span dir="ltr">`indexOf()`</span>
ו-
<span dir="ltr">`lastIndexOf()`</span>
יהיו עדיפות.

### מתודת <span dir="ltr">`fill()`</span>

המתודה
<span dir="ltr">`fill()`</span>
ממלאת אלמנט אחד או יותר במערך בערך ספציפי. כשמועבר הערך, המתודה
<span dir="ltr">`fill()`</span>
מחליפה את כל הערכים המערך עם ערך זה. כשמועבר

לדוגמא:

<div dir="ltr">

```js
let numbers = [1, 2, 3, 4];

numbers.fill(1);

console.log(numbers.toString());    // 1,1,1,1
```

</div>

בדוגמא לעיל הקריאה ל-
<span dir="ltr">`numbers.fill(1)`</span>
שינתה את כל הערכים במערך
`numbers`
ל-1. אם נרצה לשנות רק חלק מהאלמנטים במערך ולא את כולם נוכל לכלול אינדקס התחלתי ואינדקס סיום
(
אינדקס שאינו כולל
-
exclusive
),
כמו בדוגמא הבאה:

<span dir="ltr">

```js
let numbers = [1, 2, 3, 4];

numbers.fill(1, 2);

console.log(numbers.toString());    // 1,2,1,1

numbers.fill(0, 1, 3);

console.log(numbers.toString());    // 1,0,0,1
```
</span>

בקריאה
<span dir="ltr">`numbers.fill(1,2)`</span>
המספר 2 מורה להתחיל את מילוי האלמנטים במערך החל מהאינדקס 2.
אינדקס הסיום לא מצויין בקריאה זו בעזרת ארגומנט שלישי, כך שערך התכונה-
`numbers.length`
ישמש בתור אינדקס סופי ולכן שני האלמנטים האחרונים במערך
קיבלו גם הם את הערך 1.

לעומת זאת, הקריאה
<span dir="ltr">`numbers.fill(0, 1, 3)`</span>
מכניסה את הערך 0 באינדקסים 1 ו-2
(כזכור, אינדקס הסיום אינו כולל).
הקריאה למתודה
<span dir="ltr">`fill()`</span>
יחד עם הארגומנטים במיקום השני והשלישי מאפשרת לנו למלא מספר רב של אלמנטים במערך בפעם אחת מבלי לכתוב מחדש את המערך.

I>
שימו לב: אם אינדקס ההתחלה או הסיום הינם שליליים אז ערכים אלו מצטרפים לאורך המערך על מנת לקבוע את המיקום הסופי.
לדוגמא: אינדקס התחלה של 1- נותן בתור אינדקס את הערך
<span dir="ltr">`array.length - 1`</span>
כאשר
`array`
הוא המערך שעליו המתודה
<span dir="ltr">`fill()`</span>
פועלת.

### <span dir="ltr">copyWithin()</span>

המתודה
<span dir="ltr">`copyWithin()`</span>
דומה למתודה
<span dir="ltr">`fill()`</span>
בכך שהיא משנה מספר רב של אלמנטים במערך באותו זמן. אך במקום להגדיר ערך בודד לפריט במערך, המתודה
<span dir="ltr">`copyWithin()`</span>
מאפשרת לנו להעתיק ערכים של פריטים במערך מתוך המערך עצמו. לשם כך יש להעביר 2 ארגומנטים למתודה
<span dir="ltr">`copyWithin()`</span>:
האינדקס שממנו נתחיל למלא ערכים והאינדקס שממנו מתחילים הערכים שיועתקו.

כך למשל, כדי להעתיק ערכים מתוך שני הפריטים הראשונים במערך אל שני הפריטים האחרונים במערך נוכל לכתוב את הקוד הבא:

<div dir="ltr">

```js
let numbers = [1, 2, 3, 4];

// השמת ערכים החל מאינדקס 2
// העתקת ערכים החל מאינדקס 0
numbers.copyWithin(2, 0);

console.log(numbers.toString());    // 1,2,1,2
```
</div>

הקוד לעיל מעתיק ערכים לתוך המערך
`numbers`
החל מאינדקס מספר 2, כך שהפריטים באינדקס 2 ו-3 ישוכתבו. העברת הערך
`0`
בתור הארגומנט השני למתודה
<span dir="ltr">`copyWithin()`</span>
מורה לפונקציה להתחיל להעתיק ערכים החן מאינדקס מספר 0 ולהמשיך עד אשר לא נותרו עוד אלמנטים במערך לכתוב אליהם.

כברירת מחדל, המתודה
<span dir="ltr">`copyWithin()`</span>
תמיד מעתיקה ערכים עד לסוף המערך, אך באפשרותכם לספק ארגומנט שלישי על מנת להגביל את מספר האלמנטים במערך שישוכתבו. הארגומנט השלישי הוא אינדקס סיום לא כולל
<span dir="ltr">(exclusive end index)</span>
שבו נפסקת העתקת הערכים. להלן דוגמה:

<div dir="ltr">

```js
let numbers = [1, 2, 3, 4];

// השמת ערכים החל מאינדקס 2
// העתקת ערכים החל מאינדקס 0
// הפסקת השמת ערכים כשמגיעים לאינדקס 1
numbers.copyWithin(2, 0, 1);

console.log(numbers.toString());    // 1,2,1,4
```
</div>

בדוגמה זו, רק הערך באינדקס 0 מועתק מכיוון ואינדקס הסיום האופציונלי מוגדר לפי הערך
`1`.
האלמנט האחרון במערך נותר ללא שינוי.

I>
בדומה למתודה
<span dir="ltr">`fill()`</span>,
במידה ונעביר מספר שלילי עבור ארגומנט כלשהו למתודה
<span dir="ltr">`copyWithin()`</span>
אזי אורך המערך יתווסף לאותו מספר כדי לקבוע את האינדקס הסופי.

ייתכן כי מקרי השימוש במתודות
<span dir="ltr">`fill()`</span>,
ו-
<span dir="ltr">`copyWithin()`</span>
אינם ברורים כרגע. זה המצב מפני שהמתודות הללו נוצרו במקור על מערכים מקובעים
<span dir="ltr">(Typed Arrays)</span>
ולאחר מכן התווספו על מערכים רגילים כדי לשמור על עקביות. ואולם, כפי שתלמדו בסעיף הבא, אם תשתמשו ב
<span dir="ltr">Typed Arrays</span>
בכדי לשנות את הסיביות עבור ערך נומרי, אותן מתודות הופכות לשימושיות מאוד.

## מערכים מקובעים - Typed Arrays

מערכים מקובעים
<span dir="ltr">(Typed Arrays)</span>
הינם מערכים שנוצרו למטרה מיוחדת שהיא עבודה עם ערכים נומריים. תחילתם של
<span dir="ltr">Typed Arrays</span>
עם טכנולוגיית
WebGL,
שהיא הרחבה של הטכנולוגיה
<span dir="ltr">Open GL ES 2.0</span>
אשר נועדה לשימוש בדפי רשת ביחד עם אלמנט מסוג
`<canvas>`.
<span dir="ltr">Typed Arrays</span>
נוצרו כחלק מאותה הרחבה על מנת לבצע חישובים אריתמטיים מהירים ברמת הסיביות בג׳אווהסקריפט.

פעולות אריתמטיות בערכים נומריים בג׳אווהסקריפט היו איטיים מדי עבור
WebGL
מכיוון שהמספרים נשמרו בפורמט נקודה צפה בת 64 סיביות ואז הומרו למספרים שלמים בני 32 סיביות בעת הצורך. מערכים בינאריים נוצרו כדי לעקוף מגבלה זו וכדי לספק ביצועים טובים יותר עבור אופרציות אריתמטיות. הרעיון הכללי הוא שניתן להתייחס לכל מספר כאל מערך של סיביות ובצורה זו להשתמש במתודות המוכרות שזמינות לשימוש על מערכים בג׳אווהסקריפט.

אקמהסקריפט 6 אימצה לחיקה
<span dir="ltr">Typed Arrays</span>
כחלק רשמי של השפה על מנת להבטיח תאימות טובה יותר בין מנועי ריצה של ג׳אווהסקריפט כמו גם שיפור האינטרקציה בין מערכים שונים. בעוד שגרסת אקמהסקריפט 6 של
<span dir="ltr">Typed Arrays</span>
אינה זהה לזו שבגרסת
WebGL,
יש מספיק קווי דמיון כדי להתייחס לגרסת אקמהסקריפט 6 כאל התפתחות טבעית של גרסת
WebGL
ולא כאל דרך שונה להשיג אותה תוצאה.

### סוגי נתונים נומריים

מספרים בג׳אווהסקריפט נשמרים בתקן
IEEE 754
שמשתמש ב 64 סיביות כדי לייצר ייצוג נקודה צפה
<span dir="ltr">(floating-point)</span>
של המספר.
תקן זה מייצג גם מספרים שלמים וגם שברים בג׳אווהסקריפט, כאשר המרה בין שני הסוגים מתרחשת באופן תדיר.
<span dir="ltr">Typed Arrays</span>
מאפשרים שמירה ומניפולציה של
8
סוגים נומריים שונים:

1. מספר באורך 8 סיביות עם סימן (int8)
1. מספר באורך 8 סיביות ללא סימן (uint8)
1. מספר באורך 16 סיביות עם סימן (int16)
1. מספר באורך 16 סיביות ללא סימן (uint16)
1. מספר באורך 32 סיביות עם סימן (int32)
1. מספר באורך 32 סיביות ללא סימן (uint32)
1. נקודה צפה באורך 32 סיביות (float32)
1. נקודה צפה באורך 64 סיביות (float64)

אם נייצג מספר שנכנס לתוך
<span dir="ltr">Typed Array</span>
מסוג
int8
בתור מספר רגיל של ג׳אווהסקריפט אזי המשמעות היא כי ״נבזבז״ 56 סיביות.
אותן סיביות היו יכולות לשמש אותנו כדי לשמור ערכים נוספים במערכים מסוג
int8,
או כל מספר אחר שדורש פחות מ 56 סיביות.
שימוש יעיל יותר בסיביות הוא אחד מהמקרים
ש
<span dir="ltr">Typed Arrays</span>
באים לעזור לנו.

כל הפעולות והאובייקטים שקשורים ל
<span dir="ltr">Typed Arrays</span>
סובבים סביב אותם 8 סוגי נתונים שצויינו לעיל. אך על מנת להשתמש בהם יש צורך לייצר אובייקט מסוג חוצץ מערך
<span dir="ltr">(Array Buffer)</span>/
(באפר)
כדי לשמור את הנתונים.

I>
בספר זה אתייחס לאותם סוגים באמצעות הקיצורים שמופיעים בסוגריים לעיל. הקיצורים הללו אינם מופיעים בקוד עצמו, הם פשוט מהווים קיצור לתיאור האמיתי שארוך יותר בצורה משמעותית.

### חוצץ מערך - Array Buffer

הבסיס לכל
<span dir="ltr">Typed Arrays</span>
הוא
*חוצץ מערך*
(*Array Buffer*)
שהוא איזור בזיכרון המחשב שיכול להכיל מספר מוגדר של בתים. יצירת
<span dir="ltr">`Array Buffer`</span>
דומה לשימוש בפונקציה
<span dir="ltr">`malloc()`</span>
בשפת סי למען הקצאת זיכרון מבלי להגדיר מראש מה אותו אזור בזיכרון המחשב מכיל. ניתן ליצור
<span dir="ltr">`Array Buffer`</span>
על ידי שימוש בקונסטרקטור
`ArrayBuffer`
כמו בדוגמה הבאה:

<div dir="ltr">

```js
let buffer = new ArrayBuffer(10);   // הקצאת 10 בתים
```
</div>

יש להעביר כארגומנט את מספר הבתים שה
<span dir="ltr">`Array Buffer`</span>
אמור להכיל בעת הקריאה לקונסטרקטור. המשתנה מסוג
`let`
מוגדר עבור
<span dir="ltr">`Array Buffer`</span>
בגודל 10 בתים. לאחר שיצרנו
<span dir="ltr">`Array Buffer`</span>
ניתן לקבל את מספר הבתים בו על ידי קריאת התכונה
`byteLength`:

<div dir="ltr">

```js
let buffer = new ArrayBuffer(10);   // הקצאת 10 בתים
console.log(buffer.byteLength);     // 10
```
</div>

ניתן להשתמש במתודה
<span dir="ltr">`slice()`</span>
כדי ליצור
<span dir="ltr">`Array Buffer`</span>
חדש שמכיל חלק מ
<span dir="ltr">`Array Buffer`</span>
קיים. המתודה
<span dir="ltr">`slice()`</span>
של
<span dir="ltr">`ArrayBuffer`</span>
פועלת בדומה למתודה
<span dir="ltr">`slice()`</span>
במערך רגיל: מעבירים אליו כארגומנטים את אינדקס ההתחלה ואת אינדקס הסוף, והוא יחזיר מופע חדש של
`ArrayBuffer`
שמורכב מאותם אלמנטים מהמערך המקורי.
לדוגמה:

<div dir="ltr">

```js
let buffer = new ArrayBuffer(10);   // הקצאת 10 בתים

let buffer2 = buffer.slice(4, 6);
console.log(buffer2.byteLength);    // 2
```
</div>

בקוד שבדוגמה לעיל המשתנה
`buffer2`
נוצר על ידי שליפת הבתים באינדקס 4 ו-5. ממש כמו בגרסת המערך של המתודה עם אותו השם, הארגומנט השני למתודה
<span dir="ltr">`slice()`</span>
אינו ערך כוללני, כלומר הוא ערך אקסקלוסיבי
(exclusive).

יש לזכור שיצירת איזור לשמירה בזיכרון המחשב אינה עוזרת לנו ללא היכולת לכתוב נתונים לאותו איזור בזיכרון. לשם כך עלינו ליצור מצג.

I> <span dir="ltr">`ArrayBuffer`</span>
תמיד מייצג את מספר הבתים שנקבע בעת יצירתו. ניתן לשנות את הנתונים בתוך
<span dir="ltr">`ArrayBuffer`</span>,
אך לא ניתן לשנות את גודל ה
<span dir="ltr">`ArrayBuffer`</span>
עצמו.

### ביצוע שינויים בחוצץ באמצעות מצג

<span dir="ltr">`ArrayBuffer`</span>
מייצג איזורים בזיכרון המחשב.
*מצגים*
(*views*)
/
(*וויו*)
הם הממשק בו יש להשתמש כדי לבצע שינויים באותו איזור בזיכרון.
<span dir="ltr">`view`</span>
פועל על
<span dir="ltr">`Array Buffer`</span>
או על חלק מתוך הבתים של
<span dir="ltr">`Array Buffer`</span>,
וקורא או כותב נתונים בתוך אחד מסוגי הנתונים הנומרים. האובייקט מסוג
`DataView`
הינו
<span dir="ltr">`view`</span>
כללי של
<span dir="ltr">`Array Buffer`</span>
שמאפשר לנו לבצע פעולות על כל 8 סוגי הנתונים הנומריים.

על מנת להשתמש באובייקט מסוג
`DataView`
יש ליצור תחילה מופע של
`ArrayBuffer`
ולהשתמש בו בכדי ליצור
`DataView`
חדש.
לדוגמה:

<div dir="ltr">

```js
let buffer = new ArrayBuffer(10),
    view = new DataView(buffer);
```
</div>

האובייקט
`view`
בדוגמה לעיל בעל גישה מלאה לכל 10 הבתים בתוך
`buffer`.
ניתן גם ליצור
`view`
עבור
חלק מן ה
buffer.
יש לספק אינדקס התחלתי וכארגומנט אופציונלי ניתן לספק את מספר הבתים לכלול החל מאותו מיקום. כאשר לא מספקים את מספר הבתים,
`DataView`
יתקדם מאותו מיקום עד סוף הבאפר כברירת המחדל.
לדוגמה:

<div dir="ltr">

```js
let buffer = new ArrayBuffer(10),
    view = new DataView(buffer, 5, 2);      // בתים באינדקס 5 ו 6
```
</div>

בדוגמה לעיל,
`view`
פועל אך ורק על הבתים באינדקס 5 ו 6. גישה זו מאפשרת לנו ליצור מספר
`views`
עבור אותו
ArrayBuffer,
מה שיכול להיות שימושי ביותר אם נרצה להשתמש באזור אחד בזיכרון עבור האפליקציה שלנו מאשר להקצות זיכרון באופן דינמי בעת הצורך.

#### קריאת הנתונים במצג

ניתן לקבל את הנתונים ב
`view`
על ידי קריאת התכונות הבאות שמוגדרות לקריאה בלבד:

* `buffer` - הבאפר שאליו קשור הוויו
* `byteOffset` - הארגומנט השני לקונסטרקטור
`DataView`,
במידה וקיים
(0 בתור ברירת מחדל)
* `byteLength` - הארגומנט השלישי לקונסטרקטור
`DataView`,
במידה וקיים
(התכונה
`byteLength`
של הבאפר כברירת המחדל
)

על ידי שימוש בתכונות הללו ניתן לדעת היכן מופעל
view
מסוים, כמו בדוגמה הבאה:

<div dir="ltr">

```js
let buffer = new ArrayBuffer(10),
    view1 = new DataView(buffer),           // בחירת כל הבתים
    view2 = new DataView(buffer, 5, 2);     // בחירת הבתים באינדקס 5 ו 6

console.log(view1.buffer === buffer);       // true
console.log(view2.buffer === buffer);       // true
console.log(view1.byteOffset);              // 0
console.log(view2.byteOffset);              // 5
console.log(view1.byteLength);              // 10
console.log(view2.byteLength);              // 2
```
</div>

הקוד לעיל יוצר את המשתנה
`view1`,
שהוא
view
של כל
ArrayBuffer,
ואת המשתנה
`view2`,
שפועל על חלק קטן יותר של
ArrayBuffer.
ל-
views
אלו תכונות
`buffer`
זהות בערכן מאחר ושניהם פועלים על אותו
ArrayBuffer.
ואולם, התכונות בשם
`byteOffset`
ו
`byteLength`
שונות בערכן בעבור כל
view.
הן מייצגות את אותו חלק של
ArrayBuffer
שעליו פועל ה
view.

מן הסתם, אינפורמציה לגבי הזיכרון אינה שימושית במיוחד לבדה. עלינו לכתוב אל הזיכרון ולקרוא ממנו על מנת לקבל תועלת מכך.

#### קריאה וכתיבת נתונים

בעבור כל אחד משמונת סוגי הנתונים הנומריים, לפרוטוטייפ של
`DataView`
קיימת מתודה שמאפשרת כתיבת נתונים ומתודה נוספת לקריאת נתונים מתוך
ArrayBuffer.
שמות המתודות מתחילים במילים
"get"
או
"set"
ולאחריהן יופיע קיצור שם סוג הנתונים. ראו למשל דוגמה לרשימת מתודות לקריאה וכתיבה שיכולות לפעול על
<span dir="ltr">Typed Arrays</span>
מהסוגים
int8
ו-
uint8:

* <span dir="ltr">`getInt8(byteOffset)`</span> - קריאת ערך מסוג
int8
שמתחיל באינדקס
`byteOffset`
* <span dir="ltr">`setInt8(byteOffset, value)`</span> - כתיבת ערך מסוג
int8
שמתחיל באינדקס
`byteOffset`
* <span dir="ltr">`getUint8(byteOffset)`</span> - קריאת ערך מסוג
uint8
שמתחיל באינדקס
`byteOffset`
* <span dir="ltr">`setUint8(byteOffset, value)`</span> - כתיבת ערך מסוג
uint8
שמתחיל באינדקס
`byteOffset`

המתודות שמתחילות במילה
"get"
מקבלות ארגומנט יחיד: הבית שממנו יש להתחיל לקרוא נתונים. המתודות מסוג
"set"
מקבלות שני ארגומנטים:
הבית שממנו יש להתחיל לקרוא נתונים
והערך שייכתב.

אף על פי שהוצגו מתודות לשימוש עם ערכים בגודל 8 סיביות, אותן המתודות קיימות עבור ערכים בגודל 16
ו-32
סיביות. יש להחליף את הספרה
`8`
בכל מתודה בערך
`16`
או
`32`
בנוסף לכל המתודות הללו לאובייקט מסוג
`DataView`
עבור מספרים מסוג נקודה צפה
(שברים)
יש את המתודות הבאות:

* <span dir="ltr">`getFloat32(byteOffset, littleEndian)`</span> - קריאת ערך מסוג
float32
שמתחיל במיקום
`byteOffset`
* <span dir="ltr">`setFloat32(byteOffset, value, littleEndian)`</span> - כתיבת ערך מסוג
float32
שמתחיל במיקום
`byteOffset`
* <span dir="ltr">`getFloat64(byteOffset, littleEndian)`</span> - קריאת ערך מסוג
float64
שמתחיל במיקום
`byteOffset`
* <span dir="ltr">`setFloat64(byteOffset, value, littleEndian)`</span> - כתיבת ערך מסוג
float64
שמתחיל במיקום
`byteOffset`

המתודות שמתייחסות לנקודה צפה שונות רק בכך שהן מקבלות ארגומנט בוליאני אופציונלי שתפקידו להודיע לפונקציה האם הערך לקריאה או כתיבה הינו מסוג
<span dir="ltr">Little Endian</span>.
(
*Little-endian*
משמעותו שהבית הפחות חשוב נמצא במיקום 0
ואיננו הבית האחרון
)

כדי לראות מתודות
"set"
ו-
"get"
בפעולה ראו את הדוגמה הבאה:

<div dir="ltr">

```js
let buffer = new ArrayBuffer(2),
    view = new DataView(buffer);

view.setInt8(0, 5);
view.setInt8(1, -1);

console.log(view.getInt8(0));       // 5
console.log(view.getInt8(1));       // -1
```
</div>

הקוד בדוגמה לעיל משתמש ב
ArrayBuffer
בגודל 2 בתים על מנת לשמור שני ערכים מסוג
int8.
הערך הראשון מוגדר במיקום 0 והשני במיקום 1, כל ערך תופס בית אחד שלם
(8 סיביות).
ערכים אלו נקראים בעזרת המתודה
<span dir="ltr">`getInt8()`</span>.
הדוגמה משתמשת בערכים מסוג
int8,
אך ניתן להשתמש בכל אחד משמונה סוגי הנתונים הנומריים עם המתודות התואמות להם.

views
מאפשרים לנו לקרוא ולכתוב בכל צורה שנרצה ובכל זמן, ללא תלות באופן בו נשמרו הנתונים קודם לכן. כך למשל כתיבת שני ערכים מסוג
int8
לתוך
ArrayBuffer
ולאחר מכן קריאתו באמצעות מתודה שקוראת ערך מסוג
int16
הינן פעולות תקינות לחלוטין, כפי שניתן לראות בדוגמה הבאה:

<div dir="ltr">

```js
let buffer = new ArrayBuffer(2),
    view = new DataView(buffer);

view.setInt8(0, 5);
view.setInt8(1, -1);

console.log(view.getInt16(0));      // 1535
console.log(view.getInt8(0));       // 5
console.log(view.getInt8(1));       // -1
```
</div>

הקריאה לפונקציה
<span dir="ltr">`view.getInt16(0)`</span>
קוראת את כל הבתים ב
view
ומפרשת אותם בתור המספר 1535.
כדי להבין למה זה קרה, הביטו בתרשים
10-1,
שמראה איך כל קריאה לפונקציה
<span dir="ltr">`setInt8()`</span>
משפיעה על ה
ArrayBuffer.

<!--![תרשים 10-1: הבאפר לאחר שתי קריאות לפונקציה](images/Ch 10 Graphic.jpg)-->

<div dir="ltr">

```
new ArrayBuffer(2)      0000000000000000
view.setInt8(0, 5);     0000010100000000
view.setInt8(1, -1);    0000010111111111
```
</div>

ה
ArrayBuffer
מתחיל עם 16 סיביות שכולן בעלות הערך 0.
כתיבת הערך
`5`
לבית הראשון באמצעות
<span dir="ltr">`setInt8()`</span>
מוסיף מספר סיביות עם הערך 1
(
בייצוג של 8 סיביות הערך עבור 5 הינו
00000101
).
כתיבת הערך
-1
לבית השני משנה את כל הסיביות באותו בית לערך
1,
שהוא הייצוג הנומרי עבור
-1
בשיטת
משלים ל-2 המתמטית. לאחר הקריאה השניה אל
<span dir="ltr">`setInt8()`</span>
ה
ArrayBuffer
מכיל 16 סיביות והמתודה
<span dir="ltr">`getInt16()`</span>
קוראת את אותן סיביות בתור מספר שלם בן 16 סיביות, שערכו הדצימלי הוא 1535.

האובייקט מסוג
`DataView`
שימושי מאוד במקרים בהם מערבבים מספר סוגי נתונים שונים בצורה זו. ואולם, אם משתמשים רק בסוג נתונים אחד אזי ה
views
הספציפיים לסוג מסויים הינה בחירה מוצלחת יותר.

#### מערכים מקובעים הם מצגים

<span dir="ltr">Typed Arrays</span>
של אקמהסקריפט 6 הם למעשה
view
מסוג ספציפי עבור
ArrayBuffer.
במקום להשתמש באובייקט
`DataView`
פשוט על מנת לעבוד עם
ArrayBuffer,
ניתן להשתמש באובייקטים שאוכפים סוג נתונים מסוים. ישנם שמונה סוגים של
view
מותאמים לסוג נתונים מסוים בהתאמה לשמונה סוגי הנתונים הנומריים, בנוסף לאפשרות לעבוד עם ערכים מסוג
`uint8`.

טבלה 10-1 מראה גרסה מקוצרת של הרשימה המלאה של
views
מותאמים לסוג מסוים מתוך סעיף 22.2 של אפיון
אקמהסקריפט 6.

<!--{ title="Table 10-1: Some Type-Specific Views in ECMAScript 6" }-->
<!-- @Nicholas: I noticed the table title was throwing off the markdown preview
     output somehow, so I commented it out for now. /JG -->


|שם הקונסטרקטור|גודל האלמנט (בתים)|תיאור                        |מקבילה בשפת סי|
|----------------|------------|-----------------------------------|-----------------|
|`Int8Array`     |1           |מספר שלם בגודל 8 סיביות עם סיבית סימן בשיטת משלים ל-2|`signed char`|
|`Uint8Array`    |1           |מספר שלם בגודל 8 סיביות ללא סיבית סימן             |`unsigned char`|
|`Uint8ClampedArray`|1        |מספר שלם בגודל 8 סיביות ללא סיבית סימן (המרה תחומה)|`unsigned char`|
|`Int16Array`    |2           |מספר שלם בגודל 16 סיביות עם סיבית סימן בשיטת משלים ל-2|`short`|
|`Uint16Array`   |2           |מספר שלם בגודל 16 סיביות ללא סיבית סימן            |`unsigned short`|
|`Int32Array`    |4           |מספר שלם בגודל 32 סיביות עם סיבית סימן בשיטת משלים ל-2|`int`|
|`Uint32Array`   |4           |מספר שלם בגודל 32 סיביות ללא סיבית סימן   |`int`|
|`Float32Array`  |4           |מספר מסוג נקודה צפה בגודל 32 סיביות בתקן IEEE           |`float`|
|`Float64Array`  |8           |מספר מסוג נקודה צפה בגודל 64 סיביות בתקן IEEE          |`double`|

העמודה השמאלית מכילה את רשימת הקונסטרקטורים עבור
<span dir="ltr">Typed Arrays</span>,
והעמודות האחרות מתארות את סוג הנתונים שכל
<span dir="ltr">Typed Array</span>
יכול להכיל. מערך מסוג
`Uint8ClampedArray`
זהה למערך מסוג
`Uint8Array`
אלא אם ערכים ב
ArrayBuffer
קטנים מ-0 או גדולים יותר מהערך
255.

מערך מסוג
`Uint8ClampedArray`
ממיר ערכים קטנים יותר מ-0 ל-0
(
  <span dir="ltr">-1</span>
  הופך ל-0, לדוגמה
)
וממיר ערכים גבוהים יותר מ-255 ל-255
(המספר 300 יהפוך ל 255).

פעולות ב
<span dir="ltr">Typed Array</span>,
עובדות רק בעבור סוג נתונים מסוים. לדוגמה, כל הפעולות על מערכים מסוג
`Int8Array`
משתמשות בערכים מסוג
`int8`.
גודל אלמנט ב
<span dir="ltr">Typed Array</span>
תלוי גם בסוג המערך. בעוד שאלמנט במערך מסוג
`Int8Array`
הוא בגודל בית אחד, מערך מסוג
`Float64Array`
משתמש ב-8 בתים עבור כל אלמנט. למרבה המזל, האלמנטים נקראים באמצעות גישה על ידי אינדקס נומרי, בדיוק כמו מערכים רגילים, מה שמאפשר לנו להימנע משימוש בקריאות ישירות למתודות
"set"
ו-
"get"
כמו ב
`DataView`.

<!--
A> ### Element Size
A>
A> Each typed array is made up of a number of elements, and the element size is the number of bytes each element represents. This value is stored on a `BYTES_PER_ELEMENT` property on each constructor and each instance, so you can easily query the element size:
A>
A> ```js
A> console.log(UInt8Array.BYTES_PER_ELEMENT);      // 1
A> console.log(UInt16Array.BYTES_PER_ELEMENT);     // 2
A>
A> let ints = new Int8Array(5);
A> console.log(ints.BYTES_PER_ELEMENT);            // 1
A> ```
-->

### גודל אלמנט

כל
<span dir="ltr">Typed Array</span>
מורכב ממספר אלמנטים, וגודל כל אלמנט הינו מספר הבתים שכל אלמנט מייצג. ערך זה מופיע בתכונה
`BYTES_PER_ELEMENT`
על הקונסטרקטור ועל כל מופע, כך שניתן למצוא בקלות  את גודל האלמנט:

<div dir="ltr">

```js
console.log(UInt8Array.BYTES_PER_ELEMENT);      // 1
console.log(UInt16Array.BYTES_PER_ELEMENT);     // 2

let ints = new Int8Array(5);
console.log(ints.BYTES_PER_ELEMENT);            // 1
```
</div>

### יצירת מצגים לפי סוג נתונים

הקונסטרקטורים ל
<span dir="ltr">Typed Arrays</span>
מקבלים מספר סוגי ארגומנטים, לכן יש מספר דרכים ליצור
<span dir="ltr">Typed Arrays</span>.
ראשית, ניתן ליצור
<span dir="ltr">Typed Array</span>
חדש על ידי העברת אותם ארגומנטים ש
`DataView`
מקבל
(
  ArrayBuffer,
  ארגומנט הזחת בתים אופציונלי,
  ארגומנט גודל בתים אופציונלי
).
לדוגמה:

<div dir="ltr">

```js
let buffer = new ArrayBuffer(10),
    view1 = new Int8Array(buffer),
    view2 = new Int8Array(buffer, 5, 2);

console.log(view1.buffer === buffer);       // true
console.log(view2.buffer === buffer);       // true
console.log(view1.byteOffset);              // 0
console.log(view2.byteOffset);              // 5
console.log(view1.byteLength);              // 10
console.log(view2.byteLength);              // 2
```
</div>

בקוד לעיל, שני ה-
views
הם מופעים של
`Int8Array`
שמשתמשים במשתנה
`buffer`.
שני ה-
views
`view1`
ו-
`view2`
מקבלים את התכונות
`buffer`, `byteOffset`, `byteLength`
שקיימות על מופעי
`DataView`.
קל לעבור מ-
`DataView`
אל
<span dir="ltr">Typed Array</span>
כל עוד משתמשים בסוג נומרי אחד.

הדרך השניה ליצור
<span dir="ltr">Typed Array</span>
היא להעביר מספר בודד לקונסטרקטור. המספר מייצג את מספר האלמנטים
(לא מספר הבתים)
שיהיו במערך.
הקונסטרקטור ייצור
ArrayBuffer
חדש עם מספר הבתים המתאים כדי לייצג את אותם אלמנטים וניתן לקרוא את מספר האלמנטים במערך על ידי שימוש בתכונה
`length`.
לדוגמה:

<div dir="ltr">

```js
let ints = new Int16Array(2),
    floats = new Float32Array(5);

console.log(ints.byteLength);       // 4
console.log(ints.length);           // 2

console.log(floats.byteLength);     // 20
console.log(floats.length);         // 5
```
</div>

המערך
`ints`
נוצר עם מקום עם 2 אלמנטים. כל מספר שלם בן 16 סיביות משתמש בשני בתים, ולכן למערך החדש מוקצים 4 בתים.
המערך
`floats`
נוצר עם מקום עבור 5 אלמנטים, לכן מוקצים 20 בתים
(4
בתים לכל אלמנט).
בשני המקרים נוצר באפר חדש שניתן לגשת אליו במידת הצורך על ידי התכונה
`buffer`.

<!--
W> If no argument is passed to a typed array constructor, the constructor acts as if `0` was passed. This creates a typed array that cannot hold data because zero bytes are allocated to the buffer.
-->
אם לא מועבר ארגומנט לקונסטרקטור
<span dir="ltr">Typed Array</span>,
הקונסטרקטור מתנהג כאילו הועבר הארגומנט
`0`.
זה יוצר
<span dir="ltr">Typed Array</span>
שלא יכול לשמור נתונים מאחר ואפס בתים מוקצים לבאפר.

הדרך השלישית ליצור
<span dir="ltr">Typed Array</span>
היא להעביר אובייקט בתור ארגומנט בודד לקונסטרקטור. האובייקט יכול להיות אחד מהבאים:

* **<span dir="ltr">Typed Array</span>** -
כל אלמנט מועתק לאלמנט חדש על ה
<span dir="ltr">Typed Array</span>
החדש. לדוגמה, אם נעביר ערך מסוג
int8
לתוך קונסטרקטור
`Int16Array`
הערכים מסוג
int8
יועתקו לתוך מערך של ערכים מסוג
int16.
ל
<span dir="ltr">Typed Array</span>
החדש יהיה באפר שונה מזה שהועבר כארגומנט.

* **אובייקט איטרבילי** -
האיטרטור של האובייקט נקרא כדי לשלוף את הפריטים שיועברו ל
<span dir="ltr">Typed Array</span>.
הקונסטרקטור יזרוק שגיאה במידה וישנו אלמנט שאינו תקין עבור סוג המצג.

* **מערך** -
האלמנטים במערך מועתקים ל
<span dir="ltr">Typed Array</span>
החדש.
הקונסטרקטור יזרוק שגיאה במידה וישנו אלמנט שאינו תקין עבור סוג הנתונים.

* **אובייקט דמוי מערך** -
מתנהג בדיוק כמו מערך.

בכל אחד מהמקרים, נוצר
<span dir="ltr">Typed Array</span>
חדש עם נתונים מאובייקט המקור. זה יכול להיות שימושי במיוחד כאשר נרצה לאתחל
<span dir="ltr">Typed Array</span>
עם ערכים מסוימים. לדוגמה:

<div dir="ltr">

```js
let ints1 = new Int16Array([25, 50]),
    ints2 = new Int32Array(ints1);

console.log(ints1.buffer === ints2.buffer);     // false

console.log(ints1.byteLength);      // 4
console.log(ints1.length);          // 2
console.log(ints1[0]);              // 25
console.log(ints1[1]);              // 50

console.log(ints2.byteLength);      // 8
console.log(ints2.length);          // 2
console.log(ints2[0]);              // 25
console.log(ints2[1]);              // 50
```
</div>

הדוגמה לעיל יוצרת מערך בשם
`ints1`
מסוג
`Int16Array`
ומאתחלת אותו באמצעות מערך עם שני ערכים.
בהמשך נוצר מערך
`ints2`
מסוג
`Int32Array`
ואליו מועבר כארגומנט המערך
`ints1`.
הערכים 25 ו-50 מועתקים מן
`ints1`
אל
`ints2`
מאחר ולשני המערכים יש באפר שונה. אותם מספרים מיוצגים בשני
<span dir="ltr">Typed Arrays</span>
אך ל
`ints2`
יש 8 בתים שאיתם ניתן לייצג את הנתונים בעוד של
`ints1`
ישנם רק ארבעה בתים.

## דימיון בין מערכים רגילים למערכים מקובעים

מערכים רגילים ו
<span dir="ltr">Typed Arrays</span>
דומים במספר דרכים, וכפי שראיתם בפרק זה,
<span dir="ltr">Typed Arrays</span>
יכולים לעבוד כמו מערכים רגילים בהרבה מצבים. כך ניתן לדוגמה לבדוק כמה אלמנטים מכיל
<span dir="ltr">Typed Array</span>
באמצעות התכונה
`length`,
וניתן לגשת לאלמנטים בתוך
<span dir="ltr">Typed Array</span>
באופן ישיר באמצעות אינדקס נומרי. לדוגמה:

<div dir="ltr">

```js
let ints = new Int16Array([25, 50]);

console.log(ints.length);          // 2
console.log(ints[0]);              // 25
console.log(ints[1]);              // 50

ints[0] = 1;
ints[1] = 2;

console.log(ints[0]);              // 1
console.log(ints[1]);              // 2
```
</div>

בקוד שבדוגמה לעיל, נוצר מערך חדש מסוג
`Int16Array`
שמכיל שני אלמנטים.
הפריטים במערך נקראים ונכתבים באמצעות שימוש באינדקס הנומרי שלהם, ואותם ערכים נשמרים ומומרים לערכים מסוג
int16
באופן אוטומטי כחלק מהפעולה. הדימיון ממשיך בדברים אחרים.

I>
בניגוד למערכים רגילים, לא ניתן לשנות את גודל המערך הבינארי באמצעות התכונה
`length`.
התכונה
`length`
אינה ניתנת לכתיבה, וכל ניסיון לשנות אותה לא ישנה דבר במצב רגיל ואף יזרוק שגיאה במצב קפדני.

### מתודות דומות

<span dir="ltr">Typed Arrays</span>
משתמשים במספר רב של מתודות שמבחינה פונקציונלית מקבילות למתודות של מערך רגיל. ניתן להשתמש במתודות המערך הבאות על
<span dir="ltr">Typed Arrays</span>:

<div dir="ltr">

* `copyWithin()`
* `entries()`
* `fill()`
* `filter()`
* `find()`
* `findIndex()`
* `forEach()`
* `indexOf()`
* `join()`
* `keys()`
* `lastIndexOf()`
* `map()`
* `reduce()`
* `reduceRight()`
* `reverse()`
* `slice()`
* `some()`
* `sort()`
* `values()`

</div>

חשוב לזכור, שאף על פי שהמתודות הללו פועלות כמו המקבילות שלהן על
<span dir="ltr">`Array.prototype`</span>,
הן אינן לגמרי זהות. המתודות של
<span dir="ltr">Typed Array</span>
מבצעות בדיקות נוספות כדי לבדוק את סוג הנתונים הפנימי וכאשר מצפים לקבל מערך בתור הערך המוחזר נקבל
<span dir="ltr">Typed Array</span>
במקום מערך רגיל
(
הודות לתכונה
<span dir="ltr">`Symbol.species`</span>
).
להלן דוגמה פשוטה להמחשת ההבדל:

<div dir="ltr">

```js
let ints = new Int16Array([25, 50]),
    mapped = ints.map(v => v * 2);

console.log(mapped.length);        // 2
console.log(mapped[0]);            // 50
console.log(mapped[1]);            // 100

console.log(mapped instanceof Int16Array);  // true
```
</div>

הקוד לעיל משתמש במתודה
<span dir="ltr">`map()`</span>
על מנת ליצור מערך חדש שמבוסס על הערכים שמופיעים במערך
`ints`.
פונקציית המיפוי מכפילה כל ערך במערך ומייצרת מערך חדש מסוג
`Int16Array`.

### איטרטורים זהים

ל
<span dir="ltr">Typed Arrays</span>
יש את אותם שלושה איטרטורים כמו מערכים רגילים. אלו מגיעים מן המתודות
<span dir="ltr">`entries(), keys(), values()`</span>.
המשמעות היא שניתן להשתמש באופרטור הפיזור ולולאות
<span dir="ltr">`for-of`</span>
עם
<span dir="ltr">Typed Arrays</span>.
לדוגמה:

<div dir="ltr">

```js
let ints = new Int16Array([25, 50]),
    intsArray = [...ints];

console.log(intsArray instanceof Array);    // true
console.log(intsArray[0]);                  // 25
console.log(intsArray[1]);                  // 50
```
</div>

הקוד לעיל יוצר מערך חדש בשם
`intsArray`
שמכיל את אותם נתונים כמו המערך הבינארי
`ints`.
בדומה לאובייקטים איטרבילים אחרים, אופרטור הפיזור מאפשר המרה קלה של
<span dir="ltr">Typed Arrays</span>
למערכים רגילים.

### המתודות <span dir="ltr">of(), from()</span>

לכל ה
<span dir="ltr">Typed Arrays</span>
יש מתודות סטטיות בשם
<span dir="ltr">of()</span>
ו-
<span dir="ltr">from()</span>
שעובדות כמו המתודות
<span dir="ltr">Array.of()</span>
ו-
<span dir="ltr">Array.from()</span>.
ההבדל הוא שהמתודות של
<span dir="ltr">Typed Arrays</span>
מחזירים
<span dir="ltr">Typed Array</span>
במקום מערך רגיל. להלן מספר דוגמאות שמשתמשות באותן מתודות על מנת ליצור
<span dir="ltr">Typed Arrays</span>:

<div dir="ltr">

```js
let ints = Int16Array.of(25, 50),
    floats = Float32Array.from([1.5, 2.5]);

console.log(ints instanceof Int16Array);        // true
console.log(floats instanceof Float32Array);    // true

console.log(ints.length);       // 2
console.log(ints[0]);           // 25
console.log(ints[1]);           // 50

console.log(floats.length);     // 2
console.log(floats[0]);         // 1.5
console.log(floats[1]);         // 2.5
```

</div>

המתודות
<span dir="ltr">of()</span>
ו-
<span dir="ltr">from()</span>
משמשות אותנו ליצירת מערך מסוג
`Int16Array`
ומסוג
`Float32Array`,
בהתאמה. המתודות הללו מבטיחות לנו שיצירת
<span dir="ltr">Typed Arrays</span>
תהיה קלה ופשוטה כמו יצירת מערכים רגילים.

## הבדלים בין <span dir="ltr">Typed Arrays</span> למערכים רגילים

ההבדל החשוב ביותר בין
<span dir="ltr">Typed Arrays</span>
למערכים רגילים הוא ש
<span dir="ltr">Typed Arrays</span>
אינם מערכים רגילים.
<span dir="ltr">Typed Arrays</span>
לא יורשים
מהקונסטרקטור
`Array`
והמתודה
<span dir="ltr">Array.isArray()</span>
מחזירה את הערך
`false`
כאשר היא מקבלת מערך בינארי כארגומנט.
לדוגמה:

<div dir="ltr">

```js
let ints = new Int16Array([25, 50]);

console.log(ints instanceof Array);     // false
console.log(Array.isArray(ints));       // false
```
</div>

מאחר והמשתנה
`ints`
הוא
<span dir="ltr">Typed Array</span>
הוא איננו נחשב מופע של
`Array`
ולא ניתן לזהותו כמערך. ההפרדה הזו חשובה מכיוון
שלמרות שמערכים רגילים ו-
<span dir="ltr">Typed Arrays</span>
דומים יש עדיין הבדלים רבים ביניהם.

### הבדלים בהתנהגות

בעוד שמערכים רגילים יכולים לגדול ולהתכווץ בזמן שעובדים איתם,
<span dir="ltr">Typed Arrays</span>
תמיד נשארים באותו גודל. לא ניתן להגדיר ערך לאינדקס נומרי שאינו קיים ב
<span dir="ltr">Typed Arrays</span>
כמו שאפשר במערכים רגילים, מכיוון ו
<span dir="ltr">Typed Arrays</span>
מתעלמים מפעולה כזו.
לדוגמה:

<div dir="ltr">

```js
let ints = new Int16Array([25, 50]);

console.log(ints.length);          // 2
console.log(ints[0]);              // 25
console.log(ints[1]);              // 50

ints[2] = 5;

console.log(ints.length);          // 2
console.log(ints[2]);              // undefined
```
</div>

למרות שבדוגמה לעיל ביצענו השמה של הערך
`5`
לאינדקס הנומרי
`2`,
המערך
`ints`
לא גדל. התכונה
`length`
לא משתנה והערך לא נשמר.

הערכים ב
<span dir="ltr">Typed Arrays</span>
עוברים בדיקות לוודא שרק סוגי נתונים תקינים מופיעים במערך. הערך
0
משמש במקום ערכים שאינם תקינים.
לדוגמה:

<div dir="ltr">

```js
let ints = new Int16Array(["הי"]);

console.log(ints.length);       // 1
console.log(ints[0]);           // 0
```

</div>

הקוד מנסה להשתמש בערך המחרוזת
`"הי"`
בתוך מערך מסוג
`Int16Array`.
מחרוזות אינן נתון תקין בתוך
<span dir="ltr">Typed Arrays</span>,
ולכן הערך
`0`
יוכנס למערך במקום זאת.
תכונת
`length`
של המערך מקבלת את הערך
`1`,
ואף על פי שאינדקס
`ints[0]`
קיים, הוא מכיל רק את הערך
`0`.

כל המתודות שמשנות ערכים ב
<span dir="ltr">Typed Arrays</span>
פועלות תחת אותן מגבלות. לדוגמה, אם הפונקציה שמועברת למתודה
<span dir="ltr">map()</span>
מחזירה ערך שאינו תקין אזי הערך
`0`
ישמש במקומו.

<div dir="ltr">

```js
let ints = new Int16Array([25, 50]),
    mapped = ints.map(v => "הי");

console.log(mapped.length);        // 2
console.log(mapped[0]);            // 0
console.log(mapped[1]);            // 0

console.log(mapped instanceof Int16Array);  // true
console.log(mapped instanceof Array);       // false
```
</div>

מאחר והמחרוזת
`"הי"`
אינה מספר שלם בן 16 סיביות, היא מוחלפת בערך
`0`.
הודות להתנהגות זו של תיקון שגיאות,
<span dir="ltr">Typed Arrays</span>
לא צריכים לדאוג לגבי שגיאות כאשר משתמשים בערכים לא תקינים בעת פעולות על המערך.

### מתודות חסרות

בעוד ש
<span dir="ltr">Typed Arrays</span>
משתמשים בהרבה מתודות כמו של מערכים רגילים, עדיין חסרות להם מספר מתודות של מערך.
המתודות הבאות אינן זמינות על
<span dir="ltr">Typed Arrays</span>:

<div dir="ltr">

* `concat()`
* `pop()`
* `push()`
* `shift()`
* `splice()`
* `unshift()`

</div>

מלבד המתודה
<span dir="ltr">concat()</span>
המתודות ברשימה למעלה יכולות לשנות את גודל המערך.
<span dir="ltr">Typed Arrays</span>
לא מסוגלים לשנות את גודלם, ולכן מתודות אלו אינן מופיעות ב
<span dir="ltr">Typed Arrays</span>.
המתודה
<span dir="ltr">concat()</span>
אינה זמינה כיוון שתוצאת חיבור שני
<span dir="ltr">Typed Arrays</span>
(
  בייחוד אם הם מכילים סוגי נתונים שווים
)
אינה ידועה והדבר נוגד את מטרת השימוש ב
<span dir="ltr">Typed Arrays</span>.

### מתודות נוספות

ל
<span dir="ltr">Typed Arrays</span>
יש שתי מתודות שלא מופיעות על מערכים רגילים:
המתודות
<span dir="ltr">set()</span>
ו-
<span dir="ltr">subarray()</span>.
מתודות אלו הינן הפוכות זו לזו בפעולתן, כיוון שהמתודה
<span dir="ltr">set()</span>
מעתיקה
<span dir="ltr">Typed Array</span>
אחד לתוך
<span dir="ltr">Typed Array</span>
שני, בעוד שהמתודה
<span dir="ltr">subarray()</span>.
מעבירה חלק מ
<span dir="ltr">Typed Array</span>
קיים ל
<span dir="ltr">Typed Array</span>
חדש.

המתודה
<span dir="ltr">set()</span>
מקבלת מערך
(רגיל או
<span dir="ltr">Typed Array</span>)
וארגומנט מיקום פוטנציאלי שממנו יש להכניס את הנתונים. אם לא נעביר דבר נקודת ההתחלה תהיה באינדקס 0. הנתונים מתוך המערך שהועבר כארגומנט מועתקים ל
<span dir="ltr">Typed Array</span>
שהוא המטרה ודואג שרק סוגי נתונים תקינים יועתקו.
דוגמה:

<div dir="ltr">

```js
let ints = new Int16Array(4);

ints.set([25, 50]);
ints.set([75, 100], 2);

console.log(ints.toString());   // 25,50,75,100
```
</div>

הקוד לעיל יוצר מערך מסוג
`Int16Array`
עם ארבעה אלמנטים. הקריאה הראשונה למתודה
<span dir="ltr">`set()`</span>
מעתיקה שני ערכים לאלמנט הראשון והשני במערך
`ints`.
הקריאה השנייה ל-
<span dir="ltr">`set()`</span>
משתמשת במיקום של
`2`
כדי לסמן שהערכים צריכים להיכנס למערך החל מהאלמנט השלישי.

המתודה
<span dir="ltr">`subarray()`</span>.
מקבלת כארגומנט רשות אינדקס התחלה ואינדקס סיום
(
אינדקס הסיום הוא לא אינדקס כולל, ממש כמו במתודה
<span dir="ltr">`slice()`</span>.
)
ומחזיר
<span dir="ltr">Typed Array</span>
חדש. ניתן להשמיט את שני הארגומנטים כדי לייצר עותק מלא של ה
<span dir="ltr">Typed Array</span>.
לדוגמה:

<div dir="ltr">

```js
let ints = new Int16Array([25, 50, 75, 100]),
    subints1 = ints.subarray(),
    subints2 = ints.subarray(2),
    subints3 = ints.subarray(1, 3);

console.log(subints1.toString());   // 25,50,75,100
console.log(subints2.toString());   // 75,100
console.log(subints3.toString());   // 50,75
```
</div>

שלושה
<span dir="ltr">Typed Arrays</span>
נוצרים מהמערך המקורי
`ints`
בדוגמה זו.
המערך
`subints1`
הוא עותק של
`ints`
ומכיל את אותו מידע. מכיוון שהמערך
`subints2`
מעתיק נתונים החל מאינדקס 2, הוא מכיל רק את שני האלמנטים האחרונים של המערך
`ints`
(75
ו-
100).
המערך
`subints3`
מכיל רק את שני האלמנטים האמצעיים של
`ints`
מכיוון שהמתודה
<span dir="ltr">`subarray()`</span>
נקראה בעזרת אינדקס התחלה יחד עם אינדקס סיום.

## סיכום

גרסת אקמהסקריפט 6 ממשיכה את העבודה שנעשתה באקמהסקריפט 5 על ידי הפיכת מערכים לשימושיים יותר. ישנן עוד שתי צורות ליצור מערכים:
אלו הן המתודות
<span dir="ltr">`Array.of()`</span>
ו-
<span dir="ltr">`Array.from()`</span>.
המתודה
<span dir="ltr">`Array.from()`</span>
יכולה להמיר אובייקטים איטרביליים ודמויי מערך למערכים. שתי המתודות מתקבלות בירושה ממערך ואינן משתמשות בתכונה
<span dir="ltr">`Symbol.species`</span>
בכדי להחליט איזה סוג נתונים נומרי להחזיר
(מתודות אחרות שמתקבלות בירושה כן משתמשות ב
<span dir="ltr">`Symbol.species`</span>
כשהן מחזירות מערך
).

ישנן כמה מתודות חדשות. המתודות
<span dir="ltr">`fill()`</span>
ו-
<span dir="ltr">`copyWithin()`</span>
מרשות לנו לשנות אלמנטים של מערך בו-במקום. המתודות
<span dir="ltr">`find()`</span>
ו-
<span dir="ltr">`findIndex()`</span>
יעילות למציאת הערך הראשון במערך שתואם לקריטריון מסוים. הראשונה מחזירה את האלמנט הראשון שמתאים לקריטריון והאחרונה מחזירה את האינדקס בו מופיע האלמנט.


<span dir="ltr">Typed Arrays</span>,
אינם מערכים מבחינה טכנית, כיוון שאינם יורשים מ-
`Array`,
אך הם נראים ומתנהגים בדומה למערכים.
<span dir="ltr">Typed Arrays</span>
מכילים אחד מתוך שמונה סוגי נתונים נומריים ובנויים כשבבסיסים נמצאים אובייקטים מסוג
`ArrayBuffer`
שמייצגים את הסיביות שמרכיבות את המספר, או סדרת המספרים.

<span dir="ltr">Typed Arrays</span>
מהווים דרך יעילה יותר לביצוע אריתמטיקה ברמת הסיביות כיוון שהערכים לא משתנים מבחינת הפורמט בו הם שמורים, כמו שקורה בערך מספרי רגיל של ג׳אווהסקריפט.

</div>
