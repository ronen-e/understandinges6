<div dir="rtl">
# קידוד עם מודולים

גישת ה ״שיתוף הכל״ של js לטעינת קוד היא אחד ההיבטים המועדים לשגיאה והמבלבלים ביותר בשפה. שפות אחרות משתמשות במושגים כמו חבילות כדי להגדיר את היקף הקוד, אך לפני ECMAScript 6, כל מה שהוגדר בכל קובץ JavaScript של יישום שיתף סקופ גלובלי אחד. כאשר אפליקציות ווב הפכו מורכבים יותר והחלו להשתמש בקוד JavaScript עוד יותר, גישה זו גרמה לבעיות שונות כמו: התנגשויות שמות ובעיות אבטחה. מטרה אחת של ECMAScript 6 הייתה לפתור את בעיית הסקופ ולהביא קצת סדר ליישומי JavaScript. לשם נכנסים מודולים.

## מה זה Modules?

_מודולים_ הם קבצי JavaScript הנטענים במצב שונה (בניגוד ל _סקריפטים_, הנטענים באופן המקורי ש- JavaScript עבד). מצב שונה זה הכרחי מכיוון שלמודולים יש סמנטיקה שונה מאוד מאשר סקריפטים:

1. קוד המודול פועל באופן אוטומטי במצב קפדני (strict mode), ואין דרך לבטלו
1. משתנים שנוצרו ברמה העליונה של מודול אינם מתווספים אוטומטית לסקופ הגלובלי המשותף. הם קיימים רק בסקופ העליון של המודול.
1. הערך של `this` ברמה העליונה של המודול הוא `undefined`.
1. מודולים אינם מאפשרים הערות בסגנון HTML בקוד (מאפיין שאריות מימי הדפדפן המוקדמים של JavaScript).
1. על המודולים לייצא כל מה שאמור להיות זמין לקוד מחוץ למודול.
1. מודולים רשאים לייבא ממודולים אחרים.

הבדלים אלה עשויים להיראות קטנים במבט ראשון, אך הם מייצגים שינוי משמעותי באופן טעינת קוד ה- JavaScript והערכתו, עליו אדון במהלך פרק זה. הכוח האמיתי של המודולים הוא היכולת לייצא ולייבא רק חלקים שאתה זקוק להן, ולא כל מה שיש בקובץ. הבנה טובה של ייצוא וייבוא הינה יסודית להבנת האופן שבו מודולים שונים מתסריטים.

## ייצוא בסיסי

אתה יכול להשתמש במילת המפתח `export` כדי לחשוף חלקים מהקוד שפורסם למודולים אחרים. במקרה הפשוט ביותר, אתה יכול להציב `export` מול כל הצהרת משתנה, פונקציה או מחלקה כדי לייצא אותו מהמודול, כך:

</div>

```js
// export data
export var color = "red";
export let name = "Nicholas";
export const magicNumber = 7;

// export function
export function sum(num1, num2) {
  return num1 + num1;
}

// export class
export class Rectangle {
  constructor(length, width) {
    this.length = length;
    this.width = width;
  }
}

// this function is private to the module
function subtract(num1, num2) {
  return num1 - num2;
}

// define a function...
function multiply(num1, num2) {
  return num1 * num2;
}

// ...and then export it later
export { multiply };
```

<div dir="rtl">

יש לשים לב לדוגמא זו לכמה דברים. ראשית, מלבד מילת המפתח `export` , כל הצהרה זהה לחלוטין לדרך הרגילה. לכל פונקציה או מחלקה מיוצאת יש גם שם; הסיבה לכך היא שהייצוא של פונקציות והצהרות כיתות דורשות שם. אינך יכול לייצא פונקציות או מחלקות אנונימיות באמצעות תחביר זה אלא אם כן אתה משתמש במילת המפתח 'ברירת מחדל' (נדון בפירוט בסעיף "ערכי ברירת מחדל במודולים").

לאחר מכן שקול את הפונקציה `multiply()` , שאינה מיוצאת כאשר היא מוגדרת. זה עובד כי לא תמיד צריך לייצא הצהרה: אתה יכול גם לייצא הפניות. לבסוף, שים לב כי דוגמה זו אינה מייצאת את הפונקציה `subtract()`. פונקציה זו לא תהיה נגישה מחוץ למודול זה מכיוון שמשתנים, פונקציות או מחלקות שלא מיוצאים במפורש נשארים פרטיים למודול.

## ייבוא בסיסי

ברגע שיש לך מודול עם ייצוא, אתה יכול לגשת לפונקציונליות במודול אחר באמצעות מילת המפתח `import`. שני החלקים של משפט `import` הם המזהים שאתה מייבא והמודול ממנו יש לייבא מזהים אלה. זו הצורה הבסיסית של ההצהרה:

</div>

```js
import { identifier1, identifier2 } from "./example.js";
```

<div dir="rtl">

הסוגריים המסולסלות לאחר `import` מציינת את החלקים לייבוא ממודול נתון. מילת המפתח `from` מציינת את המודול שממנו ניתן לייבא את החלק הנתון. המודול מוגדר על ידי מחרוזת המייצגת את הנתיב למודול (הנקרא _ specifule_module_). הדפדפנים משתמשים באותו פורמט נתיב שעשוי להעביר לאלמנט `<script>`, כלומר עליך לכלול סיומת קובץ. לעומת זאת, Node.js עוקב אחר המוסכמה המסורתית שלה להבדיל בין קבצים וחבילות מקומיים בהתבסס על קידומת מערכת קבצים. לדוגמה,`example` תהיה חבילה ו- `./example.js` תהיה קובץ מקומי.

I> רשימת החלקים לייבוא נראית דומה לאובייקט , אך הוא אינו כזה.

בעת ייבוא חלק ממודול, הקוד פועלת כאילו הוגדרה באמצעות `const`. כלומר, אינך יכול להגדיר משתנה אחר בעל אותו שם (כולל ייבוא חלק נוספת באותו שם), להשתמש במזהה לפני משפט `import` או לשנות את ערכו.

### ייבוא חלק קוד כיחידה

נניח שהדוגמה הראשונה בסעיף "ייצוא בסיסי" היא במודול עם שם הקובץ `example.js`. ניתן לייבא ולהשתמש בחלקים מאותו מודול במספר דרכים. למשל, אתה יכול פשוט לייבא מזהה אחד:

</div>

```js
// import just one
import { sum } from "./example.js";

console.log(sum(1, 2)); // 3

sum = 1; // error
```

<div dir="rtl">

למרות ש- `example.js` מייצאת יותר מפונקציה אחת בלבד, דוגמה זו מייבאת רק את הפונקציה `sum()`. אם תנסה להקצות ערך חדש ל `sum`, התוצאה היא שגיאה, מכיוון שלא תוכל להקצות מחדש חלקים מיובאות.

W> הקפד לכלול את `/`, `./`, או `../` בתחילת הקובץ שאתה מייבא לצורך התאמה טובה ביותר בין דפדפנים ו- Node.js.

### ייבוא חלקי קוד מרובים

אם ברצונך לייבא חלקים מרובים ממודול הדוגמה, תוכל לפרט אותן באופן מפורש כדלקמן:

</div>

```js
// import multiple
import { sum, multiply, magicNumber } from "./example.js";
console.log(sum(1, magicNumber)); // 8
console.log(multiply(1, 2)); // 2
```

<div dir="rtl">

כאן, שלוש חלקי קוד מיובאים ממודול הדוגמה: `sum`, `multiply` ו`magicNumber`. לאחר מכן הם משמשים כאילו הם הוגדרו באופן מקומי.

### ייבוא כל המודול

יש גם מקרה מיוחד המאפשר לך לייבא את כל המודול כאובייקט יחיד. כל הייצוא זמין אז לאובייקט כמאפיינים. לדוגמה:

</div>

```js
// import everything
import * as example from "./example.js";
console.log(example.sum(1, example.magicNumber)); // 8
console.log(example.multiply(1, 2)); // 2
```

<div dir="rtl">

בקוד זה, כל החלקים המיוצאים ב- `example.js` נטענות לאובייקט שנקרא`example`. לאחר מכן ניתן יהיה לייצא את הייצוא הנקוב (פונקציית `sum()`, פונקצית `multiple()` ו- `magicNumber` ) כתכונות ב `example`. פורמט ייבוא זה נקרא ייבוא _שמם_ (_namespace import_ ) מכיוון שהאובייקט `example` אינו קיים בתוך הקובץ `example.js`.במקום זה נוצר כדי לשמש כאובייקט מרחב שמות עבור כל החברים המיוצאים של `example.js` .

זכור, עם זאת, שלא משנה כמה פעמים אתה משתמש במודול בהצהרות `import` , המודול יבוצע רק פעם אחת. לאחר ביצוע הקוד לייבוא המודול, המודול המייצר נשמר בזיכרון ומשמש אותו מחדש בכל פעם שמשפט אחר של `import` מפנה אליו. שקול את הדברים הבאים:

</div>

```js
import { sum } from "./example.js";
import { multiply } from "./example.js";
import { magicNumber } from "./example.js";
```

<div dir="rtl">

למרות שישנן שלוש הצהרות `import` במודול זה,`example.js` יבוצע רק פעם אחת. אם מודולים אחרים באותה יישום היו מייבאים חלקים מ- `example.js` , מודולים אלה ישתמשו באותו מופע מודול בו קוד משתמש.

### מגבלות תחביר המודול

מגבלה חשובה הן של `export` והן של `import` היא שיש להשתמש בהם מחוץ להצהרות ופונקציות אחרות. למשל, קוד זה ייתן שגיאת תחביר:

</div>

```js
if (flag) { export flag; } // syntax error
```

<div dir="rtl">

הצהרת `export` נמצאת בתוך הצהרת 'אם', שאינה מותרת. היצוא לא יכול להיות מותנה או להתבצע באופן דינמי בשום צורה שהיא. אחת הסיבות לכך שקיימת תחביר של מודולים היא לאפשר למנוע JavaScript לקבוע באופן סטטי מה ייצא. ככזה, אתה יכול להשתמש רק ב- `export` ברמה העליונה של המודול.

באופן דומה, אינך יכול להשתמש ב `import` בתוך הצהרה; אתה יכול להשתמש בו רק ברמה העליונה. כלומר, קוד זה נותן גם שגיאת תחביר:

</div>

```js
function tryImport() {
  import flag from "./example.js";
} // syntax error
```

<div dir="rtl">

אינך יכול לייבא חלקים בצורה דינמית מאותה סיבה שלא ניתן לייצא חלקחים בצורה דינמית. מילות המפתח`export` ו `import` נועדו להיות סטטיות כך שכלים כמו עורכי טקסט יוכלו לדעת בקלות איזה מידע זמין ממודול.

### A Subtle Quirk of Imported Bindings

הצהרות `יבוא` של ECMAScript 6 יוצרות אפרות קריאה בלבד למשתנים, פונקציות וקלאסים במקום להתייחס רק לקוד המקור כמו למשתנים רגילים. למרות שהמודול שמייבא את הקוד לא יכול לשנות את ערכו, המודול שמייצא את המזהה יכול. לדוגמה, נניח שתרצה להשתמש במודול זה:

</div>

```js
export var name = "Nicholas";
export function setName(newName) {
  name = newName;
}
```

<div dir="rtl">

כאשר אתה מייבא את שתי הפונקציות האלה, הפונקציה `setName ()` יכולה לשנות את הערך של `שם`:

</div>

```js
import { name, setName } from "./example.js";

console.log(name); // "Nicholas"
setName("Greg");
console.log(name); // "Greg"

name = "Nicholas"; // error
```

<div dir="rtl">

הקריאה ל `setName("Greg")` חוזרת למודול שממנו יוצא `setName()` ומבוצעת שם, ומגדירה שם את `name` ל `"Greg"` . שים לב ששינוי זה משתקף אוטומטית בחלק `name` המיובא. הסיבה לכך היא ש `name` הוא השם המקומי למזהה `name` המיוצא. `name` המשמש בקוד לעיל וה `name` המשמש במודול המיובא ממנו אינם זהים.

## Renaming Exports and Imports

לפעמים, אולי לא תרצה להשתמש בשם המקורי של משתנה, פונקציה או מחלקה שייבאת ממודול. למרבה המזל, אתה יכול לשנות את שם היצוא גם במהלך הייצוא וגם במהלך הייבוא.

במקרה הראשון, נניח שיש לך פונקציה שברצונך לייצא בשם אחר. אתה יכול להשתמש במילת המפתח `as` כדי לציין את השם שהפונקציה אמורה להיות מחוץ למודול:

</div>

```js
function sum(num1, num2) {
  return num1 + num2;
}

export { sum as add };
```

<div dir="rtl">

כאן, הפונקציה`sum()` (`sum` היא השם המקומי) מיוצאת כ-`add () `(`add` הוא השם המיוצא). פירוש הדבר שכאשר מודול אחר מעוניין לייבא פונקציה זו, יהיה עליו להשתמש בשם `add` במקום זאת:

</div>

```js
import { add } from "./example.js";
```

<div dir="rtl">

אם המודול שמייבא את הפונקציה רוצה להשתמש בשם אחר, הוא יכול גם להשתמש ב `as`:

</div>

```js
import { add as sum } from "./example.js";
console.log(typeof add); // "undefined"
console.log(sum(1, 2)); // 3
```

<div dir="rtl">

קוד זה מייבא את הפונקציה `add ()` באמצעות import ומשנה את שמו ל- `sum ()` (השם המקומי). פירוש הדבר שאין במודול זה מזהה בשם `add`.

### ברירת מחדל במודולים

תחביר המודול מותאם באמת לייצוא וייבוא של ערכי ברירת מחדל ממודולים, מכיוון שדפוס זה היה נפוץ למדי במערכות מודולים אחרות, כמו CommonJS (פורמט מודול JavaScript אחר הפופולרי על ידי Node.js). ערך ברירת המחדל עבור מודול הוא משתנה, פונקציה או מחלקה כפי שצוינו על ידי מילת המפתח `default`, וניתן להגדיר רק יצוא ברירת מחדל אחד לכל מודול. שימוש במילת המפתח `default` עם יצוא מרובה יגרום לשגיאת תחביר.

### ייצוא ערכי ברירת מחדל

הנה דוגמה פשוטה המשתמשת במילת המפתח `default`:

</div>

```js
export default function (num1, num2) {
  return num1 + num2;
}
```

<div dir="rtl">

מודול זה מייצא פונקציה כערך ברירת המחדל שלה. מילת המפתח `default` מציינת שמדובר בייצוא ברירת מחדל. הפונקציה אינה דורשת שם מכיוון שהמודול עצמו מייצג את הפונקציה.

אתה יכול גם לציין מזהה כיצוא ברירת המחדל על ידי הצבתו לאחר`export default`, כגון:

</div>

```js
function sum(num1, num2) {
  return num1 + num2;
}

export default sum;
```

<div dir="rtl">

כאן, הפונקציה `sum()` מוגדרת תחילה ומאוחרת מיוצאת כערך ברירת המחדל של המודול. ייתכן שתרצה לבחור בגישה זו אם יש לחשב את ערך ברירת המחדל.

דרך שלישית לציין מזהה כיצוא ברירת המחדל היא באמצעות תחביר שינוי שם כדלקמן:

</div>

```js
function sum(num1, num2) {
  return num1 + num2;
}

export { sum as default };
```

<div dir="rtl">

למזהה `default` יש משמעות מיוחדת בייצוא שמות מחדש ומציין שערך צריך להיות ברירת המחדל עבור המודול. מכיוון ש `default` היא מילת מפתח ב- JavaScript, לא ניתן להשתמש בה לשם משתנה, פונקציה או שם מחלקה (ניתן להשתמש בה כשם מאפיין). אז השימוש ב `default` לשם שינוי יצוא הוא מקרה מיוחד ליצירת עקביות עם אופן ההגדרה של יצוא שאינו ברירת מחדל. תחביר זה שימושי אם ברצונך להשתמש במשפט `default` יחיד כדי לציין יצוא מרובים, כולל ברירת המחדל, בבת אחת.

### ייבוא ערכי ברירת מחדל

באפשרותך לייבא ערך ברירת מחדל ממודול באמצעות התחביר הבא:

</div>

```js
// import the default
import sum from "./example.js";

console.log(sum(1, 2)); // 3
```

<div dir="rtl">

הצהרת ייבוא זו מייבאת את ברירת המחדל מהמודול `example.js`. שים לב כי לא נעשה שימוש בסוגריים מתולתלים, בשונה ממה שהיית רואה בייבוא שאינו ברירת מחדל. השם המקומי `sum` משמש לייצוג כל פונקציית ברירת המחדל שהמודול מייצא. התחביר הזה הוא הנקי ביותר, ויוצרי ECMAScript 6 מצפים שהוא יהיה צורת הייבוא הדומיננטית באינטרנט, מה שמאפשר לך להשתמש באובייקט שכבר קיים.

עבור מודולים המייצאים גם קוד ברירת מחדל וגם חלק קוד אחד או יותר שאינו ברירת מחדל, באפשרותך לייבא את כל הקוד המיוצא עם משפט אחד. לדוגמה, נניח שיש לך את המודול הזה:

</div>

```js
export let color = "red";

export default function (num1, num2) {
  return num1 + num2;
}
```

<div dir="rtl">

ניתן לייבא הן את `color` והן את פונקציית ברירת המחדל באמצעות הצהרת `import` הבאה:

</div>

```js
import sum, { color } from "./example.js";

console.log(sum(1, 2)); // 3
console.log(color); // "red"
```

<div dir="rtl">

הפסיק מפריד בין השם המקומי המוגדר כברירת מחדל לבין הלא ברירות המחדל, המוקפות גם בסוגריים מסולסלים. זכור כי על ברירת המחדל לבוא לפני אי-ברירת המחדל בהצהרת`import`.

כמו בייצוא ברירות מחדל, ניתן לייבא ברירות מחדל גם בעזרת התחביר המשנה את שם:

</div>

```js
// equivalent to previous example
import { default as sum, color } from "example";

console.log(sum(1, 2)); // 3
console.log(color); // "red"
```

<div dir="rtl">

בקוד זה, שמם של ייצוא ברירת המחדל (`default`) הוא`sum` וייצוא נוסף של `color` מיובא. דוגמא זו מקבילה לדוגמא הקודמת.

## ייצוא חוזר של קוד

יכול להיות שתרצה לייצא מחדש משהו שהמודול שלך ייבא (למשל, אם אתה יוצר ספרייה מכמה מודולים קטנים). באפשרותך לייצא מחדש ערך מיובא עם הדפוסים שכבר נדונו בפרק זה באופן הבא:

</div>

```js
import { sum } from "./example.js";
export { sum };
```

<div dir="rtl">

זה עובד, אבל הצהרה אחת יכולה גם לעשות את אותו הדבר:

</div>

```js
export { sum } from "./example.js";
```

<div dir="rtl">

צורה זו של `export` בודקת את המודול שצוין עבור ההצהרה על `sum` ואז מייצאת אותו. כמובן, אתה יכול גם לבחור לייצא שם אחר באותו ערך:

</div>

```js
export { sum as add } from "./example.js";
```

<div dir="rtl">

כאן, `sum` מיובא מ-`"./example.js"` ואז מיוצא כ `add`.

אם ברצונך לייצא הכל ממודול אחר, תוכל להשתמש בתבנית `*`:

</div>

```js
export * from "./example.js";
```

<div dir="rtl">

כשאתה מייצא הכל, אתה כולל את כל הייצוא ששמו אינו כולל יצוא ברירת מחדל. לדוגמא, אם ל- `example.js` יש ייצוא ברירת מחדל, יהיה עליכם לייבא אותו במפורש ואז לייצא אותו במפורש.

## יבוא ללא הצהרה

ייתכן שמודולים מסוימים לא מייצאים שום דבר, ובמקום זאת רק מבצעים שינויים באובייקטים בסקופ הכללי. למרות שמשתנים, פונקציות ומחלקות ברמה העליונה במודולים אינם מגיעים באופן אוטומטי להיקף הגלובלי, אין זה אומר שמודולים אינם יכולים לגשת להיקף הגלובלי. ההגדרות המשותפות של אובייקטים מובנים כגון `Array` ו `Object` נגישות בתוך מודול ושינויים באובייקטים אלה יבואו לידי ביטוי במודולים אחרים.

לדוגמה, אם ברצונך להוסיף שיטת `pushAll()` לכל המערכים, תוכל להגדיר מודול כזה:

</div>

```js
// module code without exports or imports
Array.prototype.pushAll = function (items) {
  // items must be an array
  if (!Array.isArray(items)) {
    throw new TypeError("Argument must be an array.");
  }

  // use built-in push() and spread operator
  return this.push(...items);
};
```

<div dir="rtl">

זהו מודול תקף למרות שאין ייצוא או יבוא. ניתן להשתמש בקוד זה גם כמודול וגם כתסריט. מכיוון שהוא לא מייצא דבר, אתה יכול להשתמש בייבוא פשוט כדי לבצע את קוד המודול מבלי לייבא קוד כרוך:

</div>

```js
import "./example.js";

let colors = ["red", "green", "blue"];
let items = [];

items.pushAll(colors);
```

<div dir="rtl">

קוד זה מייבא ומבצע את המודול המכיל את המתודה `pushAll()`, כך ש `pushAll()` נוסף לאב טיפוס המערך. פירוש הדבר ש- `pushAll()` זמין כעת לשימוש בכל המערכים שבתוך מודול זה.

I> יבוא ללא כריכות סביר להניח שישמש ליצירת מילוי polyfills.

## טעינת מודולים

בעוד ש- ECMAScript 6 מגדיר את התחביר למודולים, הוא אינו מגדיר כיצד לטעון אותם. זה חלק מהמורכבות של מפרט שאמור להיות אגנוסטי לסביבות היישום. במקום לנסות ליצור מפרט יחיד שיעבוד בכל סביבות ה- JavaScript, ECMAScript 6 מציין רק את התחביר וממצה את מנגנון הטעינה לפעולה פנימית לא מוגדרת הנקראת `HostResolveImportedModule`. דפדפני האינטרנט ו- Node.js נותרו להחליט כיצד ליישם את `HostResolveImportedModule` באופן הגיוני לסביבותיהם.

### שימוש במודולים בדפדפני אינטרנט

עוד לפני ECMAScript 6, היו לדפדפני האינטרנט דרכים מרובות לכלול JavaScript ביישום אינטרנט. אפשרויות טעינת סקריפט אלה הן:

1. טוען קבצי קוד JavaScript באמצעות רכיב `<script>` עם התכונה `src` המציין מיקום ממנו ניתן לטעון את הקוד.
1. הטמעת קוד JavaScript מוטבע באמצעות האלמנט `<script>` ללא התכונה `src`.
1. טוען קבצי קוד JavaScript לביצוע כ worker (כגון web worker או service worker).

על מנת לתמוך במודולים באופן מלא, דפדפני האינטרנט נאלצו לעדכן כל אחד מהמנגנונים הללו. פרטים אלה מוגדרים במפרט ה- HTML ואני אסכם אותם בחלק זה.

#### שימוש במודולים עם `<script>`

התנהגות ברירת המחדל של האלמנט `<script>` היא לטעון קבצי JavaScript כסקריפטים (לא מודולים). זה קורה כאשר התכונה`type` חסרה או כאשר התכונה `type` מכילה סוג תוכן של JavaScript (כגון `"text/javascript"`). האלמנט `<script>` יכול לבצע קוד מוטבע או לטעון את הקובץ שצוין ב- `src` . כדי לתמוך במודולים, הערך `"module"` נוסף כאופציה ב `type` . הגדרת `type` ל `"module"` אומרת לדפדפן לטעון כל קוד או קוד מוטבעים הכלולים בקובץ שצוין על ידי `src` כמודול במקום סקריפט. הנה דוגמה פשוטה:

</div>

```html
<!-- load a module JavaScript file -->
<script type="module" src="module.js"></script>

<!-- include a module inline -->
<script type="module">
  import { sum } from "./example.js";

  let result = sum(1, 2);
</script>
```

<div dir="rtl">

האלמנט הראשון `<Script>` בדוגמה זו טוען קובץ מודול חיצוני באמצעות התכונה `src`. ההבדל היחיד מטעינת סקריפט הוא ש `"module"` ניתן כ `type`. האלמנט השני `<Script>` מכיל מודול המוטמע ישירות בדף האינטרנט. המשתנה `result` אינו נחשף באופן גלובלי מכיוון שהוא קיים רק בתוך המודול (כהגדרתו על ידי האלמנט `<script>`) ולכן אינו מתווסף ל `window` כמאפיין.

כפי שאתה יכול לראות, כולל מודולים בדפי אינטרנט הוא די פשוט ודומה לכלול סקריפטים. עם זאת, ישנם כמה הבדלים באופן טעינת המודולים.

I> יתכן ששמת לב ש- `"module"` אינו סוג תוכן כמו סוג `"text/javascript"` . קבצי JavaScript מודוליים מוגשים עם אותו סוג תוכן כמו קבצי JavaScript של סקריפט, כך שלא ניתן להבדיל רק על פי סוג תוכן. כמו כן, דפדפנים מתעלמים מאלמנטים של `<script>` כאשר `type` אינו מזוהה, כך שדפדפנים שאינם תומכים במודולים יתעלמו אוטומטית משורת `<script type="module">` , ויספקו תאימות טובה לאחור.

#### רצף טעינת מודולים בדפדפני אינטרנט

המודולים הם ייחודיים בכך שבניגוד לתסריטים, הם עשויים להשתמש ב `import` כדי לציין שיש לטעון קבצים אחרים כדי לבצע אותם בצורה נכונה. כדי לתמוך בפונקציונליות זו, `<script type="module">` תמיד מתנהג כאילו מוחל התכונה `defer`.

תכונת `defer` היא אופציונלית לטעינת קבצי סקריפט אך מוחלת תמיד לטעינת קבצי מודול. הורדת קובץ המודול מתחילה ברגע שמנתח ה- HTML נתקל ב- `<script type = "module'>` בתכונה `src` אך אינו מבוצע עד לאחר ניתוח המסמך לחלוטין. המודולים מבוצעים גם לפי סדר הופעתם בקובץ ה- HTML. פירוש הדבר שה- `<script type = "module">` הראשון מובטח תמיד כי יבוצע לפני השני, גם אם מודול אחד מכיל קוד מוטבע במקום לציין `src`. לדוגמה:

</div>

```html
<!-- this will execute first -->
<script type="module" src="module1.js"></script>

<!-- this will execute second -->
<script type="module">
  import { sum } from "./example.js";

  let result = sum(1, 2);
</script>

<!-- this will execute third -->
<script type="module" src="module2.js"></script>
```

<div dir="rtl">

שלושת האלמנטים `<script>` אלה מבצעים לפי סדר הגדרתם, ולכן מובטח ש- `module1.js` יבוצע לפני המודול המוטבע, ומובטח כי המודול המוטבע יבוצע לפני `module2.js`.

כל מודול יכול `import` ממודול אחד או יותר, מה שמסבך את העניינים. זו הסיבה שמודלים מנותחים תחילה לחלוטין כדי לזהות את כל הצהרות ה`import`. כל הצהרת `import` ואז מפעילה אחזור (מהרשת או מהמטמון), ואין מודול מבוצע עד שכל משאבי `import` נטענו והוצאו לראשונה. שלושת האלמנטים `<script>` אלה מבוצעים לפי הסדר. הם מוגדרים, ולכן מובטח ש- `module1.js` יבוצע לפני המודול המוטבע, ומובטח כי המודול המוטבע יבוצע לפני `module2.js`.

כל המודולים, הן אלה שנכללו במפורש באמצעות `<script type =" module ">` וגם אלה שנכללו במשתמע באמצעות `import` , נטענים ומבוצעים לפי הסדר. בדוגמה הקודמת, רצף הטעינה השלם הוא:

1. הורד ונתח `module1.js`.
1. הורד רקורסיבי ונתח את המשאבים `import` ב- `module1.js`.
1. נתח את המודול המוטבע.
1. הורד ורקורסיבית משאבים של `import` במודול המוטבע.
1. הורד ונתח `module2.js`.
1. הורד רקורסיבי ונתח את המשאבים `import` ב- `module2.js`

לאחר השלמת הטעינה, דבר אינו מבוצע עד לאחר ניתוח המסמך לחלוטין. לאחר השלמת ניתוח המסמכים, מתרחשות הפעולות הבאות:

1. ביצוע רקורטיבי של משאבי `import` עבור `module1.js`.
1. ביצוע `module1.js`.
1. בצע רקורטיבי משאבי `import` עבור המודול המוטבע.
1. בצע את המודול המוטבע.
1. ביצוע רקורטיבי של משאבי `import` עבור `module2.js`.
1. ביצוע `module2.js`.

Notice that the inline module acts like the other two modules except that the code doesn't have to be downloaded first. Otherwise, the sequence of loading `import` resources and executing modules is exactly the same.

I> The `defer` attribute is ignored on `<script type="module">` because it already behaves as if `defer` is applied.

#### Asynchronous Module Loading in Web Browsers

You may already be familiar with the `async` attribute on the `<script>` element. When used with scripts, `async` causes the script file to be executed as soon as the file is completely downloaded and parsed. The order of `async` scripts in the document doesn't affect the order in which the scripts are executed, though. The scripts are always executed as soon as they finish downloading without waiting for the containing document to finish parsing.

The `async` attribute can be applied to modules as well. Using `async` on `<script type="module">` causes the module to execute in a manner similar to a script. The only difference is that all `import` resources for the module are downloaded before the module itself is executed. That guarantees all resources the module needs to function will be downloaded before the module executes; you just can't guarantee _when_ the module will execute. Consider the following code:

```html
<!-- no guarantee which one of these will execute first -->
<script type="module" async src="module1.js"></script>
<script type="module" async src="module2.js"></script>
```

In this example, there are two module files loaded asynchronously. It's not possible to tell which module will execute first simply by looking at this code. If `module1.js` finishes downloading first (including all of its `import` resources), then it will execute first. If `module2.js` finishes downloading first, then that module will execute first instead.

#### Loading Modules as Workers

Workers, such as web workers and service workers, execute JavaScript code outside of the web page context. Creating a new worker involves creating a new instance `Worker` (or another class) and passing in the location of JavaScript file. The default loading mechanism is to load files as scripts, like this:

```js
// load script.js as a script
let worker = new Worker("script.js");
```

To support loading modules, the developers of the HTML standard added a second argument to these constructors. The second argument is an object with a `type` property with a default value of `"script"`. You can set `type` to `"module"` in order to load module files:

```js
// load module.js as a module
let worker = new Worker("module.js", { type: "module" });
```

This example loads `module.js` as a module instead of a script by passing a second argument with `"module"` as the `type` property's value. (The `type` property is meant to mimic how the `type` attribute of `<script>` differentiates modules and scripts.) The second argument is supported for all worker types in the browser.

Worker modules are generally the same as worker scripts, but there are a couple of exceptions. First, worker scripts are limited to being loaded from the same origin as the web page in which they are referenced, but worker modules aren't quite as limited. Although worker modules have the same default restriction, they can also load files that have appropriate Cross-Origin Resource Sharing (CORS) headers to allow access. Second, while a worker script can use the `self.importScripts()` method to load additional scripts into the worker, `self.importScripts()` always fails on worker modules because you should use `import` instead.

### Browser Module Specifier Resolution

All of the examples to this point in the chapter have used a relative module specifier path such as `"./example.js"`. Browsers require module specifiers to be in one of the following formats:

- Begin with `/` to resolve from the root directory
- Begin with `./` to resolve from the current directory
- Begin with `../` to resolve from the parent directory
- URL format

For example, suppose you have a module file located at `https://www.example.com/modules/module.js` that contains the following code:

```js
// imports from https://www.example.com/modules/example1.js
import { first } from "./example1.js";

// imports from https://www.example.com/example2.js
import { second } from "../example2.js";

// imports from https://www.example.com/example3.js
import { third } from "/example3.js";

// imports from https://www2.example.com/example4.js
import { fourth } from "https://www2.example.com/example4.js";
```

Each of the module specifiers in this example is valid for use in a browser, including the complete URL in the final line (you'd need to be sure `ww2.example.com` has properly configured its Cross-Origin Resource Sharing (CORS) headers to allow cross-domain loading). These are the only module specifier formats that browsers can resolve by default (though the not-yet-complete module loader specification will provide ways to resolve other formats). That means some normal looking module specifiers are actually invalid in browsers and will result in an error, such as:

```js
// invalid - doesn't begin with /, ./, or ../
import { first } from "example.js";

// invalid - doesn't begin with /, ./, or ../
import { second } from "example/index.js";
```

Each of these module specifiers cannot be loaded by the browser. The two module specifiers are in an invalid format (missing the correct beginning characters) even though both will work when used as the value of `src` in a `<script>` tag. This is an intentional difference in behavior between `<script>` and `import`.

## Summary

ECMAScript 6 adds modules to the language as a way to package up and encapsulate functionality. Modules behave differently than scripts, as they don't modify the global scope with their top-level variables, functions, and classes, and `this` is `undefined`. To achieve that behavior, modules are loaded using a different mode.

You must export any functionality you'd like to make available to consumers of a module. Variables, functions, and classes can all be exported, and there is also one default export allowed per module. After exporting, another module can import all or some of the exported names. These names act as if defined by `let` and operate as block bindings that can't be redeclared in the same module.

Modules need not export anything if they are manipulating something in the global scope. You can actually import from such a module without introducing any bindings into the module scope.

Because modules must run in a different mode, browsers introduced `<script type="module">` to signal that the source file or inline code should be executed as a module. Module files loaded with `<script type="module">` are loaded as if the `defer` attribute is applied to them. Modules are also executed in the order in which they appear in the containing document once the document is fully parsed.
