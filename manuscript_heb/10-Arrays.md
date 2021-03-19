<div dir="rtl">

# יכולות מערך משופרות

המערך הינו אובייקט ג'אווהסקריפט במהותו. עם זאת, בעוד היבטים אחרים של ג'אווהסקריפט התפתחו במהלך הזמן, מערכים נותרו זהים עד ש-
ECMAScript 5
הציגה מספר שיטות להפוך אותם לקלים יותר לשימוש.
ECMAScript 6
המשיכה את מגמת השיפור של המערכים על ידי הוספת פונקציונליות רבה יותר, כמו שיטות יצירה חדשות של מערכים, הפיכת כמה שיטות שימושיות לנוחות יותר לכתיבת מערכים מאופיינים.

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
הוסיפה מתודות חדשות ליצירת מערכים בג'אווהסקריפט היא לסייע למפתחים להמנע מהצורך להשתמש בקונסטרקטור של מערך.
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

<span dir="ltr">

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
</span>

בכדי ליצור מערך באמצעות מתודת
<span dir="ltr">`Array.of()`</span>
פשוט נעביר לה את הערכים הרצויים במערך. כך למשל בדוגמה הראשונה לעיל נוצר מערך המכיל שני מספרים. המערך שבדוגמה השניה מכיל מספר אחד והמערך האחרון מכיל מחרוזת אחת. הדבר דומה לשימוש במערך ליטראלי ואכן ניתן להשתמש במערך ליטראלי במקום
<span dir="ltr">`Array.of()`</span>
ליצירת מערכים מובנים במרבית המקרים.
אך אם נרצה להעביר את הקונסטרקטור של המערך לפונקציה אז יתכן שנרצה להשתמש במתודה
<span dir="ltr">`Array.of()`</span>
על מנת להבטיח התנהגות עקבית.

לדוגמה:
<span dir="ltr">

```js
function createArray(arrayCreator, value) {
  return arrayCreator(value);
}

let items = createArray(Array.of, value);
```
</span>

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
`Symbol.species`
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

<span dir="ltr">

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
</span>

קוד זה מקביל מבחינה פונקציונלית לדוגמה הקודמת והוא עובד כיוון שהוא מגדיר את הערך
`this`
עבור
<span dir="ltr">`slice()`</span>
עבור אובייקט דמוי מערך.
מכיוון ש-
<span dir="ltr">`slice()`</span>
זקוק רק לאינדקס נומרי ולתכונה
`length`
כדי לתפקד כראוי הרי שכל אובייקט דמוי מערך יעבוד.

על אף שטכניקה זו מצריכה פחות קוד הרי שהקריאה
<span dir="ltr">`Array.prototype.slice.call(arrayLike)`</span>
לא בהכרח מתרגמת ל-"המר את
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

<span dir="ltr">

```js
function translate() {
    return Array.from(arguments, (value) => value + 1);
}

let numbers = translate(1, 2, 3);

console.log(numbers);   // 2,3,4
```
</span>

בדוגמה זו, למתודת
<span dir="ltr">`Array.from()`</span>
מועברת הפונקציה
<span dir="ltr">(value) => value + 1</span>
כפונקציית מיפוי, אשר מוסיפה 1 לכל פריט במערך לפני אחסון הפריט. אם פונקציית המיפוי נמצאת באובייקט ניתן גם להעביר ארגומנט שלישי למתודה
<span dir="ltr">`Array.from()`</span>
המייצג את הערך
`this`
עבור פונקציית המיפוי.

<span dir="ltr">

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
</span>

דוגמה זו מעבירה
<span dir="ltr">`helper.add()`</span>
כפונקציית מיפוי עבור ההמרה. היות והמתודה
<span dir="ltr">`helper.add()`</span>
משתמשת בתכונה
`this.diff`
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

<span dir="ltr">

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
</span>

מכיוון שהאובייקט
`numbers`
איטרבילי, ניתן להעביר אותו ישירות למתודה
<span dir="ltr">`Array.from()`</span>
על מנת להמיר את ערכיו אל מערך. פונקציית המיפוי מוסיפה 1 לכל מספר, כך שהמערך המתקבל מכיל 2, 3 ו-4 במקום
1, 2
ו-3.

I>
שימו לב: אם אובייקט הוא אובייקט דמוי מערך וגם אובייקט איטרבילי, אז המתודה
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

הנה דוגמא להמחשה:

<span dir="ltr">

```js
let numbers = [25, 30, 35, 40, 45];

console.log(numbers.find(n => n > 33));         // 35
console.log(numbers.findIndex(n => n > 33));    // 2
```
</span>

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

<span dir="ltr">

```js
let numbers = [1, 2, 3, 4];

numbers.fill(1);

console.log(numbers.toString());    // 1,1,1,1
```

</span>

בדוגמא שלעיל הקריאה ל-
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


(שורה 232)





</div>
