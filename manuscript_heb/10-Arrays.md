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
ותחביר מילולי. שתי הגישות מצריכות רישום של פריטי המערך בנפרד ובדרך כלל הן מוגבלות למדי.
אפשרויות להמרת אובייקט דמוי מערך (כלומר, אובייקט עם אינדקס מספרי ורכיב
`length`
) למערך היו גם הן מוגבלות ולעיתים קרובות דרשו קוד נוסף.
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

כך לדוגמה:
<span dir="ltr">
```js
let items = new Array(2);
console.log(items.length); // 2
console.log(items[0]); // undefined
console.log(items[1]); // undefined
```</span>

זאת לעומת
<span dir="ltr">
```js
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
```</span>

כאשר מועבר לקונסטרקטור של המערך ערך מספרי כארגומנט יחיד ערך זה יהווה את האורך
`length`
של המערך. לעומת זאת, אם מועבר לארגומנט של הקונסטרקטור ערך יחיד שאינו מספרי, ערך זה יהפוך לפריט האחד והיחיד במערך. אם מספר ערכים (מספריים או לא) מועברים לקונסטרקטור כארגומנטים ערכים אלו יהפכו אף הם לפריטים במערך. התנהגות זו הינה מסוכנת ומבלבלת כיוון שלא תמיד נוכל לדעת אלו סוגים של מידע הועברו לקונסטרקטור בתור ארגומנטים.

ECMAScript 6
מציגה את המתודה
<span dir="ltr">`Array.of()`</span>
כדי לפתור את הבעיה הזו.
<span dir="ltr">`Array.of()`</span>
פועלת באופו דומה לקונסטרקטור של
`Array`
אך אין לה מקרה מיוחד לגבי ערך מספרי יחיד. המתודה הזו יוצרת תמיד מערך המכיל את הארגומנטים שלו למספר הארגומנטים או לסוגם.
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
```</span>

בכדי ליצור מערך באמצעות מתודת
<span dir="ltr">`Array.of()`</span>
פשוט נעביר לה את הערכים הרצויים במערך. כך למשל בדוגמה הראשונה לעיל נוצר מערך המכיל שני מספרים. המערך שבדוגמה השניה מכיל מספר אחד והמערך האחרון מכיל מחרוזת אחת. הדבר דומה לשימוש במערך ליטראלי ואכן ניתן להשתמש במערך מילולי במקום
<span dir="ltr">`Array.of()`</span>
ליצירת מערכים מקוריים במרבית המקרים.
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
```</span>

בקוד זה הפונקציה
<span dir="ltr">`createArray()`</span>
מקבלת פונקציה היוצרת מערך וערך להכניס לתוך המערך. נוכל להעביר
<span dir="ltr">`Array.of()`</span>
בתור הארגומנט הראשון לפונקציה
<span dir="ltr">`createArray()`</span>
על מנת ליצור מערך חדש. במקרה זה יהיה זה מסוכן להעביר רק
`Array`
ישירות מבלי שנוכל להבטיח שהערך
`(value)`
לא יהיה מספר.

- שימו לב: המתודה
<span dir="ltr">`Array.of()`</span>
  אינה משתמשת במאפיין
  `Symbol.species`
  (הנדון בפרק 9) על מנת לקבוע את סוג הערך החוזר. במקום זאת היא משתמשת בקונסטרקטור הנוכחי
  (`this`
  בתוך מתודת ה-
  <span dir="ltr">`of()`</span>)
  על מנת לקבוע את סוג הנתונים הנכון להחזרה.


### <span dir="ltr">Array.from()</span>

הפיכת אובייקטים שאינם מערכים למערכים הייתה תמיד מסורבלת בג'אווהסקריפט. לדוגמה, אם יש לנו אובייקט של ארגומנטים (שהוא אובייקט דמוי מערך) ואנו רוצים להשתמש בו כמערך נצטרך להמיר אותו תחילה למערך. כדי להמיר אובייקט דמוי מערך למערך ב-
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

  //use args
}
```</span>

דרך זו יוצרת באופן ידני מערך בשם
`result`
ומעתיקה כל פריט מהארגומנטים למערך החדש. זה אומנם עובד אבל מצריך שימוש בכמות קוד מיותרת לחלוטין לצורך ביצוע פעולה פשוט. בסופו של דבר, מפתחים גילו כי הם יכולים להפחית את כמות הקוד הנדרשת על ידי שימוש במתודת
<span dir="ltr">slice()</span>
לבניית מערכים מאובייקטים דמויי מערך, כמו דוגמה הבאה:

<span dir="ltr">
```js
function makeArray(arrayLike) {
  return Array.prototype.slice.call(arrayLike);
} 

function doSomething() {
  var args = makeArray(arguments);

  //use args
}
```</span>

קוד זה מקביל מבחינה פונקציונלית לדוגמה הקודמת והוא עובד כיוון שהוא מגדיר את הערך
`<span dir="ltr">this()</span>`
עבור
`<span dir="ltr">slice()</span>`.
מכיוון ש-
`<span dir="ltr">slice()</span>`
זקוק רק לערכים מספריים ולמאפיין
`length`
כדי לתפקד כראוי הרי שכל אובייקט דמוי מערך יעבוד.

על אף שטכניקה זו מצריכה פחות כתיבה הרי שהקריאה
`<span dir="ltr">Array.prototype.slice.call(arrayLike)</span>`
לא בהכרח מתרגמת ל-"המר את
span dir="ltr">arrayLike</span>
למערך. למרבה המזל,
ECMAScript 6
הוסיפה את המתודה
`<span dir="ltr">Array.from()</span>`
כדרך פשוטה ונקייה להמיר אובייקטים למערכים.

בהינתן אובייקט איטרבילי או אובייקט דמוי מערך כארגומנט הראשון, המתודה
`<span dir="ltr">Array.from()</span>`
מחזירה מערך. הנה דוגמה:

<span dir="ltr">
```js
function doSomething() {
  var args = array.from(arguments);

  //use args
}
```</span>

הקריאה
`<span dir="ltr">Array.from()</span>`
יוצרת מערך חדש המבוסס על הערכים בארגומנט כך שהמשתנה
`args`
הוא מופע של מערך המכיל את אותם הערכים באותו הסדר של הארגומנט.

שימו לב: המתודה
`<span dir="ltr">Array.from()</span>`
משתמשת גם במתודה
`this`
על מנת לקבוע את סוג המערך שיש להחזיר.


###המרת מיפוי

אם ברצונך לקחת את המרת המערך צעד קדימה תוכל לספק ל-
`<span dir="ltr">Array.from()</span>`
 פונקציית מיפוי כארגומנט שני. פונקציה זו פועלת על כל איבר באובייקט דמוי מערך וממירה אותו לצורה סופית בלשהי לפני שהיא שומרת את התוצאה באינדקס המתאים במערך הסופי. לדוגמה:

<span dir="ltr">
```js
function translate() {
    return Array.from(arguments, (value) => value + 1);
}

let numbers = translate(1, 2, 3);

console.log(numbers);   // 2,3,4
```</span>

בדוגמה זו, למתודת
`<span dir="ltr">Array.from()</span>`
מועבר
`<span dir="ltr">(value) => value + 1</span>`
כפונקציית מיפוי, ולכן היא מוסיפה 1 לכל פריט במערך לפני אחסון הפריט. אם פונקציית המיפוי נמצאת באובייקט ניתן גם להעביר ארגומנט שלישי למתודה
`<span dir="ltr">Array.from()</span>`
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
```</span>

דוגמה זו מעבירה
`<span dir="ltr">helper.add()</span>`
כפונקציית מיפוי עבור ההמרה. היות והמתודה
`<span dir="ltr">helper.add()</span>`
משתמשת במאפיין
`this.diff`
עליך לספק את הארגומנט השלישי ל-
`<span dir="ltr">Array.from()</span>`
המציין את הערך של
`this`.
הודות לארגומנט השלישי,
`<span dir="ltr">Array.from()</span>`
יכול בקלות להמיר נתונים מבלי לקרוא למתודת
`bind()
או לציין את הערך
`this`
בדרך אחרת.

(שורה 157)








</div>

