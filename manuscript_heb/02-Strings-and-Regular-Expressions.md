<div dir="rtl">

# מחרוזות וביטויים רגולריים

מחרוזות נחשבות לאחד מסוגי הנתונים החשובים ביותר בעולם התכנות. הן מופיעות כמעט בכל שפת תכנות גבוהה, והיכולת לעבוד איתן בצורה טובה היא ידע הכרחי עבור מפתחים על מנת ליצור תוכנות מוצלחות. בהתאם לכך ביטויים רגולריים חשובים גם הם בזכות הכוח הנוסף שהם נותנים למפתחים ביחס לעבודה עם מחרוזות. 
בהמשך לכך, היוצרים של אקמהסקריפט 6 שיפרו מחרוזות וביטויים רגולריים באמצעות הוספת תכונות חדשות ויכולות שהיו חסרות עד עתה.
פרק זה נותן סקירה של שני סוגי השינויים.

## תמיכה טובה יותר ב Unicode

לפני המהדורה השישית מחרוזות בג׳אווהסקריפט היו מורכבות מתווים בקידוד של 16 ביטים
(UTF-16). 
כל רצף בן 16-ביט נחשב ל 
*יחידת קוד* 
שמייצגת תו אחד. כל התכונות והמתודות של מחרוזת, כמו תכונת 
`length` 
ומתודת ה
<span dir="ltr">`charAt()`</span> 
היו מבוססים על יחידות אלו בנות 16 ביט.
16 ביטים הספיקו פעם להכלת כל התווים. 
זה איננו המקרה הודות לערכת התווים שחשפה על ידי סולם התווים יוניקוד.

### נקודות קוד של UTF-16

הגבלת אורך התווים ל 16 ביט לא הייתה אפשרית עבור המטרה המוצהרת של מפתחי קידוד יוניקוד לספק מזהה ייחודי לכל תו. המזהים הייחודיים הללו שנקראים 
*נקודות קוד* 
 הם לא יותר מאשר מספרים שמתחילים מ-0. 
ניתן לחשוב על נקודות קוד בתור מזהה לתו, כאשר מספר מייצג תו מסוים. 
קידוד תוים חייב למפות נקודות קוד 
(code points) 
אל יחידות קוד 
(code units).

תחת הכללים של קידוד
UTF-16 
נקודות קוד יכולות להיות מורכבות ממספר רב של יחידות קוד.
את 
2^16^ 
נקודות הראשונות ב 
UTF-16 
מייצגים בתור יחידות קוד בודדות בנות 16 ביט. 
הטווח הזה נקרא בשם 
*המישור המולטילשוני הבסיסי* 
(*Basic Multilingual Plane* - BMP). 
כל מה שמצוי מחוץ לטווח הזה נמצא בתוך אחד מני מה שנקרא 
*מישורים נוספים* 
(*supplementary planes*) 
במצב כזה לא ניתן עוד לייצג נקודות קוד ביחידות בודדות בנות 16 ביט. 
קידוד 
UTF-16 
פותר את בעיה זו באמצעות מה שנקרא 
*זוגות חלופיים* 
(*surrogate pairs*) 
ובעצם מייצג נקודת קוד בודדת באמצעות 2 יחידות קוד בנות 16 ביט.
 ומכאן כל תו בתוך מחרוזת יכול שיהיה יחידת קוד בודדת עבור תווים מתוך ה 
BMP 
שתופס 16 ביט בזיכרון, 
או שיהיה מורכב מ 2 יחידות קוד עבור תווים מתוך המישורים הנוספים
שתופס 32 ביט בזיכרון.

באקמהסקריפט 5, כל הפעולות שניתן לבצע על מחרוזת מבוצעות על יחידות קוד בנות 16 ביט,
ומכאן ניתן לקבל תוצאות בלתי צפויות ממחרוזות בקידוד 
UTF-16 
אשר מכילות זוגות חלופיים,
כמו שניתן לראות בדוגמה הבאה:

<div dir="ltr">

```js
var text = "𠮷";

console.log(text.length);           // 2
console.log(/^.$/.test(text));      // false
console.log(text.charAt(0));        // ""
console.log(text.charAt(1));        // ""
console.log(text.charCodeAt(0));    // 55362
console.log(text.charCodeAt(1));    // 57271
```

</div>

`"𠮷"`
הוא תו יוניקוד בודד שמיוצג באמצעות זוגות חלופיים, ולכן, הפעולות שמבוצעות עליו בדוגמה הקודמת מתייחסות אליו כאל מחרוזת בעלת שני תווים בני 16 ביט כל אחד. 
המשמעות היא:

* האורך של המשתנה `text` הוא 2, במקום 1
* ביטוי רגולרי שמנסה לבודד תו אחד נכשל מכיוון שמבחינתו מדובר בשני תווים
* המתודה <span dir="ltr">`charAt()`</span> לא מסוגלת להחזיר תו תקין מכיוון שאף יחידת קוד בת 16 תווים מבין הזוגות החלופיים אינה ניתנת להדפסה

המתודה 
<span dir="ltr">`charCodeAt()`</span> 
גם היא אינה מסוגלת לזהות את התו בצורה תקינה. היא מחזירה את המספר התואם בן 16 ביט עבור כל יחידת קוד בנפרד, אבל זו הדרך הטובה ביותר להתקרב אל הערך האמיתי של המשתנה
`text` 
באקמהסקריפט מהדורה 5

אקמהסקריפט מהדורה 6 לעומת זאת, נותן מענה לבעיות אלו ונותן תמיכה לעבודה עם זוגות חלופיים.
בהמשך נדון במספר דוגמאות עקרוניות ליכולות החדשות שהתווספו

### <span dir="ltr">codePointAt()</span>

מתודה אחת שהתווספה לאקמהסקריפט 6 על מנת לתת תמיכה מלאה בקידוד 
UTF-16
היא המתודה
<span dir="ltr">`codePointAt()`</span>, 
שמחזירה את ערך נקודת הקוד של 
Unicode 
שתואמת למיקום נתון בתוך מחרוזת. 
המתודה מקבלת את מיקום נקודת הקוד ולא את מיקום התו הבודד ומחזירה ערך מספרי, כפי שניתן לראות בדוגמה הבאה:

<div dir="ltr">


```js
var text = "𠮷a";

console.log(text.charCodeAt(0));    // 55362
console.log(text.charCodeAt(1));    // 57271
console.log(text.charCodeAt(2));    // 97

console.log(text.codePointAt(0));   // 134071
console.log(text.codePointAt(1));   // 57271
console.log(text.codePointAt(2));   // 97
```

</div>

המתודה
<span dir="ltr">`codePointAt()`</span>
מחזירה את אותו ערך שמחזירה המתודה 
<span dir="ltr">`charCodeAt()`</span>
אלא אם כן היא פועלת על תווים שאינם שייכים ל
BMP.
התו הראשון במשתנה 
הינו תו אחד שכזה ומורכב משתי יחידות קוד, ומכאן ערך התכונה 
`length` 
הוא 3 ולא 2. 
המתודה 
<span dir="ltr">`charCodeAt()`</span>
מחזירה רק את יחידת הקוד הראשונה עבור המיקום 0 במחרוזת, ולעומת זאת 
<span dir="ltr">`codePointAt()`</span>
מחזירה את נקודת הקוד המלאה, אף אם נקודת הקוד מורכבת ממספר יחידות קוד. 
שתי המתודות מחזירות את אותו ערך עבור מיקום מספר 1
(יחידת הקוד השנייה של התו הראשון) 
ועבור מיקום מספר 2 
(התו `"a"`).

שימוש במתודה
<span dir="ltr">`codePointAt()`</span>
על תו הינו הדרך הקלה ביותר לבדוק האם אותו תו מיוצג על ידי יחידת קוד בודדת או שתי יחידות קוד. להלן פונקציה שיכולה לשמש לבדיקה זו:

<div dir="ltr">


```js
function is32Bit(c) {
    return c.codePointAt(0) > 0xFFFF;
}

console.log(is32Bit("𠮷"));         // true
console.log(is32Bit("a"));          // false
```

</div>


הגבול העליון של תווים בני 16-ביט מיוצג במספר הקסאדצימלי בתור המספר
`FFFF`, 
לכן כל נקודת קוד מעל מספר זה חייבת להיות מיוצגת על ידי שתי יחידות קוד, ובסך הכל 32 ביטים

### <span dir="ltr">String.fromCodePoint()</span>

כאשר אקמהסקריפט מספקת דרך לעשות פעולה כלשהי, במקרים רבים היא גם מספקת דרך לעשות את הפעולה ההפוכה. ניתן להשתמש ב 
<span dir="ltr">`codePointAt()`</span> 
בכדי להשיג את ערך נקודת הקוד עבור תו כלשהו בתוך מחרוזת, בעוד ש 
<span dir="ltr">`String.fromCodePoint()`</span> 
 
מייצרת לנו תו בודד מתוך נקודת קוד נתונה. לדוגמה:

<div dir="ltr">

```js
console.log(String.fromCodePoint(134071));  // "𠮷"
```

</div>


ניתן לחשוב על 
<span dir="ltr">`String.fromCodePoint()`</span> 
כאל שיפור של מתודת
<span dir="ltr">`String.fromCharCode()`</span> 
שתיהן נותנות את אותה תוצאה עבור כל התווים ב
BMP. 
קיים הבדל אך ורק כאשר בודקים נקודות קוד עבור תווים מחוץ ל-
BMP.

### <span dir="ltr">normalize()</span>

היבט אחד מעניין של 
Unicode 
הוא שתווים שונים יכולים להיחשב זהים למטרת מיון או השוואה. ישנן שתי שיטות להגדיר שוויון כזה. 
ראשית,
*שוויון קאנוני* 
(*canonical equivalence*) 
לפיו שתי סדרות של נקודות קוד נחשבות שוות מבכל הבחינות. 
לדוגמה, שילוב של שני תווים יכול להיות בעל שוויון קאנוני לתו אחד. 
 השיטה השנייה היא שוויון מבחינת 
*התאמה*
(*compatibility*). 
לפיו, שתי סדרות תואמות של נקודות קוד נראות שונות אך יכולות לשמש אחת במקום השנייה במצבים מסוימים.

מסיבות אלו, שתי מחרוזות אשר מייצגות באופן עקרוני את אותו הטקסט ייתכן שיכילו סדרות שונות של נקודות קוד. לדוגמה, התו
"æ" 
והמחרוזת בת שני התווים
"ae" 
יכולים לשמש אחד במקום השני אך אינם שווים זה לזה, אלא אם כן מעבירים אותם לצורה אחידה ״מנורמלת״.

אקמהסקריפט 6 תומך בנורמליזציה של קידוד
Unicode
על ידי המתודה
<span dir="ltr">`normalize()`</span>. 
המתודה יכולה לקבל פרמטר אחר מסוג מחרוזת שמייצג את אחת מצורות הנורמליזציה האפשריות:

<div dir="ltr">
* Normalization Form Canonical Composition (`"NFC"`)
* Normalization Form Canonical Decomposition (`"NFD"`)
* Normalization Form Compatibility Composition (`"NFKC"`)
* Normalization Form Compatibility Decomposition (`"NFKD"`)
</div>

צורת הנורמליזציה של המתודה כברירת מחדל הינה נורמליזציה מסוג
`"NFC"`

הסבר נרחב על ההבדלים בין ארבע צורות הנרמול נמצא מחוץ למטרות ספר זה. אולם חשוב לזכור שכאשר משווים מחרוזות יש לנרמל אותן קודם לכן לאותה צורה.
לדוגמה:

<span dir="ltr">

```js
var normalized = values.map(function(text) {
    return text.normalize();
});

normalized.sort(function(first, second) {
    if (first < second) {
        return -1;
    } else if (first === second) {
        return 0;
    } else {
        return 1;
    }
});
```

</span>

הקוד ממיר את המחרוזות בתוך מערך 
`values`
לצורה מנורמלת על מנת למיין את המערך לפי סדר האלף בית. 
ניתן למיין את המערך המקורי על ידי קריאה ל
<span dir="ltr">`normalize()` </span> 

<span dir="ltr">

```js
values.sort(function(first, second) {
    var firstNormalized = first.normalize(),
        secondNormalized = second.normalize();

    if (firstNormalized < secondNormalized) {
        return -1;
    } else if (firstNormalized === secondNormalized) {
        return 0;
    } else {
        return 1;
    }
});
```

</span>

ושוב, הדבר החשוב ביותר בדוגמת הקוד האחרונה היא שגם המשתנה
`first` 
וגם המשתנה 
`second` 
משתנים לצורה מנורמלת אחידה. דוגמאות אלו השתמשו בצורת ברירת המחדל 
(NFC)
אך באותה המידה ניתן לנקוב בשם כל אחת מהצורות האחרות, כמו בדוגמה הבאה: 


<span dir="ltr">

```js
values.sort(function(first, second) {
    var firstNormalized = first.normalize("NFD"),
        secondNormalized = second.normalize("NFD");

    if (firstNormalized < secondNormalized) {
        return -1;
    } else if (firstNormalized === secondNormalized) {
        return 0;
    } else {
        return 1;
    }
});
```

</span>

במידה ומעולם לא היית צריך לדאוג לגבי נורמליזציה של קידוד 
Unicode, 
סביר להניח שלא יהיה לך שימוש רב במתודה זו גם עתה. אך במידה ויהיה צורך לעבוד על 
אפליקציה שמותאמת לשפות אחרות, תגלו עד כמה שימושית המתודה 
<span dir="ltr">
`normalize()`
</span>

מתודות חדשות אינן השיפורים היחידים שאקמהסקריפט 6 מספקת עבור עבודה עם מחרוזות 
Unicode. 
המהדורה השישית הוסיפה שני אלמנטים תחביריים חדשים.

### סימון <span dir="ltr">u</span> בביטוי רגולרי

באמצעות ביטויים רגולריים ניתן לבצע פעולות נפוצות רבות. אך יש לזכור שביטויים רגולריים עובדים כברירת מחדל על יחידות קוד בנות 16 ביט, כאשר כל אחת מייצגת תו בודד. 
על מנת לפתור בעיה זו אקמהסקריפט 6 הגדירה סימון
`u` 
עבור ביטויים רגולריים.
הסימון 
`u`
 מייצג התייחסות ל 
Unicode. 

#### סימון <span dir="ltr">u</span> בפעולה

כאשר ביטוי רגולרי עובד תחת סימון
 `u`, 
הוא עובד על תווים, לא על יחידות קוד. 
המשמעות היא שהביטוי הרגולרי אמור לעבוד כמצופה גם כאשר הוא עובד על זוגות חלופיים. 
לדוגמה:

<div dir="ltr">

```js
var text = "𠮷";

console.log(text.length);           // 2
console.log(/^.$/.test(text));      // false
console.log(/^.$/u.test(text));     // true
```

</div>

הביטוי הרגולרי

<span dir="ltr">`/^.$/`</span>

תואם כל מחרוזות בעלת תו אחד בלבד. 
כאשר משתמשים בו ללא סימון 
`u`,
הביטוי הרגולרי תואם ליחידות קוד בלבד. 
ועקב זאת האות היפנית שבדוגמה
(שמיוצגת על ידי שתי יחידות קוד) 
לא תואמת את הביטוי הרגולרי. 
אך כאשר משתמשים בסימון 
 `u` 
 הביטוי הרגולרי תואם לתווים ולכן התו היפני תואם לביטוי.

#### ספירת נקודות קוד

לרוע המזל, אקמהסקריפט 6 לא הוסיף שיטה לקביעת כמה נקודות קוד קיימות במחרוזת, אך על ידי שימוש עם בסימון 
`u` 
ניתן להשתמש בביטוי רגולרי כדי למצוא זאת. כפי שמודגם בהמשך:

<div dir="ltr">

```js
function codePointLength(text) {
    var result = text.match(/[\s\S]/gu);
    return result ? result.length : 0;
}

console.log(codePointLength("abc"));    // 3
console.log(codePointLength("𠮷bc"));   // 3
```

</div>

בקוד הנתון מבצעים קריאה לפונקציה 
<span dir="ltr">`match()`</span>

כדי לבדוק נוכחות של כל התווים במחרוזת 
`text`
באמצעות הביטוי הרגולרי
<span dir="ltr">`[\s\S]` </span> 
שמופעל גלובלית גם עבור 
<span dir="ltr">Unicode</span>. 
המשתנה 
`result`
מכיל מערך של תוצאות כאשר קיימת התאמה אחת לפחות, כך אורך המערך הוא גם מספר נקודות הקוד בתוך המחרוזת. 
לפי קידוד
<span dir="ltr">Unicode</span> 
 
המחרוזות  
`"abc"` 
ו-
 `"𠮷bc"` 
 שתיהן בעלות שלושה תווים ולכן אורך המערך שווה ל-3.

W> אף על פי
שטכניקה זו עובדת היא אינה מהירה במיוחד, בייחוד כאשר משתמשים בה על מחרוזות ארוכות. לשם כך ניתן להשתמש באיטרטור של מחרוזות
(עליו יוסבר בהרחבה בפרק 8).
כעקרון, נסו להימנע מספירת נקודות קוד ככל האפשר.

#### תמיכה עבור סימון u

מאחר וסימון
`u`
הינו שינוי תחבירי, ניסיון להשתמש בו בסביבת 
ג׳אווהסקריפט שאינה תומכת במהדורה ה 6 של אקמהסקריפט זורק שגיאה תחבירית 
<span dir="ltr">(syntax error)</span>.
הדרך הבטוחה לבדוק האם הסימון
`u`
נתמך היא על ידי שימוש בפונקציה כמו בדוגמה הבאה:

<div dir="ltr">

```js
function hasRegExpU() {
    try {
        var pattern = new RegExp(".", "u");
        return true;
    } catch (ex) {
        return false;
    }
}
```

</div>

הפונקציה בדוגמה משתמשת בקונסטרקטור 
`RegExp` 
כדי להעביר את הסימון 
`u`
כארגומנט לפונקציה.
התחביר עובד גם במנועי ריצה ישנים של ג׳אווהסקריפט, אך הקונסטרקטור יזרוק שגיאה בזמן הרצתו במידה והסימון
`u`
אינו נתמך.

I> במידה והקוד שלכם אמור לעבוד במנועי ריצה ישנים של ג׳אווהסקריפט, תמיד השתמשו בקונסטרקטור 
`RegExp` 
כאשר יש צורך להשתמש בסימון 
`u`
זה ימנע שגיאות תחביריות ויאפשר לכן להשתמש בו בצורה תקינה.

## שינויים נוספים

מחרוזות בג׳אווהסקריפט תמיד נשרכו מאחורי יכולות קיימות במחרוזות בשפות אחרות.
לשם הדוגמה, 
היה זה רק באקמהסקריפט מהדורה חמישית שמחרוזות זכו לשיטת
<span dir="ltr">`trim()` </span>
שמאפשרת לקצר רווחים בתחילת וסוף מילה. 
המהדורה השישית של אקמהסקריפט ממשיכה להרחיב את יכולות השפה לעבוד עם מחרוזות

### מתודות לזיהוי תת מחרוזות

מפתחים השתמשו במתודה
<span dir="ltr">`indexOf()`</span> 
כדי לזהות מחרוזות בתוך מחרוזות עוד מאז שג׳אווהסקריפט פותחה לראשונה. 
המהדורה השישית של אקמהסקריפט כוללת את שלושת המתודות הבאות שנועדו לבצע את אותה פעולה:

* המתודה 
<span dir="ltr">`includes()`</span>
מחזירה 
`true`
 אם הטקסט הנתון מופיע בתוך המחרוזת.
 אחרת, יוחזר
`false`.

* המתודה
<span dir="ltr">`startsWith()`</span>
מחזירה
`true`
אם הטקסט הנתון מופיע בתחילת המחרוזת. 
אחרת, יוחזר
`false`.

* המתודה
<span dir="ltr">`endsWith()`</span> 
מחזירה
`true`
אם הטקסט הנתון מופיע בסוף המחרוזת. 
אחרת, יוחזר
`false`.

כל מתודה מקבלת שני ארגומנטים:
הטקסט לחפש ואינדקס אפשרי.
כאשר הארגומנט השני מתקבל אזי המתודות 
<span dir="ltr">`includes()`</span>
ו-
<span dir="ltr">`startsWith()`</span>
מתחילות לחפש מאותו אינדקס, בעוד שהמתודה
<span dir="ltr">`endsWith()`</span> 
מתייחסת לאותו ארגומנט כאל אורך המחרוזת. 
כאשר הארגומנט השני חסר אזי המתודות 
<span dir="ltr">`includes()`</span>
ו-
<span dir="ltr">`startsWith()`</span>
מתחילות לחפש מתחילת המחרוזת בעוד שהמתודה 
<span dir="ltr">`endsWith()`</span> 
מחפשת מסוף המחרוזת
להלן מספר דוגמאות:

<div dir="ltr">

```js
var msg = "Hello world!";

console.log(msg.startsWith("Hello"));       // true
console.log(msg.endsWith("!"));             // true
console.log(msg.includes("o"));             // true

console.log(msg.startsWith("o"));           // false
console.log(msg.endsWith("world!"));        // true
console.log(msg.includes("x"));             // false

console.log(msg.startsWith("o", 4));        // true
console.log(msg.endsWith("o", 8));          // true
console.log(msg.includes("o", 8));          // false
```

</div>

שש הקריאות הראשונות למתודה בדוגמה לא כוללות ארגומנט שני, ולכן החיפוש יתבצע על כל אורך המחרוזת, במידת הצורך. שלוש הקריאות האחרונות בודקות רק חלק מהמחרוזת. הקריאה ל - 
<span dir="ltr">`msg.startsWith("o", 4)` </span>
מתחילה לחפש החל מסמן אינדקס מס׳ 4 של ערך המשתנה
`msg`,
שהוא התו
"o"
בתוך 
"Hello".
הקריאה ל
<span dir="ltr">`msg.endsWith("o", 8)`</span>
מתחילה את החיפוש החל מסמן אינדקס 0 וממשיכה עד לסמן אינדקס 7 
שהוא התו
"o"
בתוך 
"world".
הקריאה ל
<span dir="ltr">`msg.includes("o", 8)`</span>
מתחילה את החיפוש החל מסמן אינדקס 8 שהוא התו
"r" 
בתוך 
"world".

למרות ששלוש המתודות הללו מקלות על זיהוי קיומן של תת מחרוזות, הן כולן מחזירות ערך בוליאני. אם ברצונך למצוא את המיקום של מחרוזת אחת בתוך השנייה יש להשתמש במתודה
<span dir="ltr">`indexOf()`</span>
או
<span dir="ltr">`lastIndexOf()`</span>

W> המתודות 
<span dir="ltr">`startsWith()`</span>,
<span dir="ltr">`endsWith()`</span>,
ו- 
<span dir="ltr">`includes()`</span>
זורקות שגיאה כאשר מעבירים כארגומנט ראשון ביטוי רגולרי במקום מחרוזת. זוהי התנהגות שונה מאשר הקיימת במתודות 
<span dir="ltr">`indexOf()`</span>
ו-
<span dir="ltr">`lastIndexOf()`</span>,
שממירות ביטוי רגולרי למחרוזת ומחפשות את אותה מחרוזת.

### מתודת <span dir="ltr">repeat()</span>

אקמהסקריפט 6 הוסיפה מתודת 
<span dir="ltr">repeat()</span>
למחרוזות, שמקבלת כארגומנט את מספר הפעמים שיש לחזור על המחרוזת. 
המתודה מחזירה מחרוזת חדשה שמכילה את המחרוזת המקורית אשר חוזרים עליה את מספר הפעמים הנתון.
לדוגמה:


<div dir="ltr">

```js
console.log("x".repeat(3));         // "xxx"
console.log("hello".repeat(2));     // "hellohello"
console.log("abc".repeat(4));       // "abcabcabcabc"
```

</div>

המתודה משמשת בעיקר ככלי עזר, והיא שימושית מאוד כאשר מבצעים מניפולציה על טקסט. 
היא עוזרת במיוחד כאשר עורכים טקסט שדורש רמות שונות של הזחה, למשל:

<div dir="ltr">

```js
// הזחה על ידי מספר קבוע של רווחים
var indent = " ".repeat(4),
    indentLevel = 0;

// כל פעם שמגדילים הזחה
var newIndent = indent.repeat(++indentLevel);
```

</div>

הקריאה ראשונה למתודה
<span dir="ltr">`repeat()`</span>
יוצרת מחרוזת של ארבעה רווחים, והמשתנה
`indentLevel` 
עוקב אחר רמת ההזחה. 
כך, ניתן לקרוא למתודה 
<span dir="ltr">`repeat()`</span>
עם רמת הזחה שונה שמשנה את מספר הרווחים.

אקמהסקריפט 6 הביאה מספר שינויים מועילים לביטויים רגולריים כלליים. בהמשך יוצגו כמה מהם.

## שינויים בביטויים רגולריים

ביטויים רגולריים לוקחים חלק נכבד בעבודה עם מחרוזות בג׳אווהסקריפט, וכמו חלקים רבים אחרים של השפה הם לא השתנו הרבה בגרסאות הקודמות. 
לעומת זאת, אקמהסקריפט 6 הציגה מספר שיפורים לביטויים רגולריים בד בבד עם השינויים שנעשו במחרוזות.

### הסימון y

אקמהסקריפט 6 הפך את הסימון 
`y` 
לחלק מהשפה לאחר שהוכנס לדפדפן פיירפוקס באופן עצמאי לפיירפוקס בתור הרחבה לביטויים רגולריים בפיירפוקס בלבד. 
הסימון 
`y` 
משפיע על תכונת ה״דביקות״ - 
`sticky` 
של הביטוי הרגולרי, למעשה הוא גורם לחיפוש להתחיל לבדוק התאמות במחרוזת במיקום שנקבע על ידי תכונת 
`lastIndex`
של הביטוי הרגולרי. במידה ואין התאמה באותו מיקום, אזי הביטוי הרגולרי מפסיק לחפש התאמות.
לדוגמה:

<div dir="ltr">

```js
var text = "hello1 hello2 hello3",
    pattern = /hello\d\s?/,
    result = pattern.exec(text),
    globalPattern = /hello\d\s?/g,
    globalResult = globalPattern.exec(text),
    stickyPattern = /hello\d\s?/y,
    stickyResult = stickyPattern.exec(text);

console.log(result[0]);         // "hello1 "
console.log(globalResult[0]);   // "hello1 "
console.log(stickyResult[0]);   // "hello1 "

pattern.lastIndex = 1;
globalPattern.lastIndex = 1;
stickyPattern.lastIndex = 1;

result = pattern.exec(text);
globalResult = globalPattern.exec(text);
stickyResult = stickyPattern.exec(text);

console.log(result[0]);         // "hello1 "
console.log(globalResult[0]);   // "hello2 "
console.log(stickyResult[0]);   // TypeError: Cannot read property '0' of null
```

</div>

בדוגמה זו מובאים שלושה ביטויים רגולריים.
הביטוי בתוך המשתנה 
`pattern`
הינו ללא סימונים, 
הביטוי במשתנה 
`globalPattern`
משתמש בסימון 
`g`,
והביטוי במשתנה
`stickyPattern` 
משתמש בסימון
`y`.
בשלוש הקריאות הראשונות ל - 
<span dir="ltr">`console.log()`</span>
כל שלושת הביטויים מחזירים את המחרוזת 
`"hello1 "` 
עם רווח בסוף המחרוזת.

לאחר מכן התכונה
`lastIndex`
נקבעת לערך 1 עבור כל שלושת הביטויים הרגולריים, כלומר הביטוי הרגולרי יחפש התאמות החל מהתו השני. 
הביטוי הרגולרי ללא סימון מתעלם מהשינוי לתכונה
`lastIndex`
ועדיין מוצא את ההתאמה 
`"hello1 "` . 
הביטוי הרגולרי עם סימון 
`g` 
ממשיך הלאה בחיפוש ומוצא התאמה עבור המחרוזת 
`"hello2 "` 
מכיוון והוא מחפש קדימה החל מהתו השני של המחרוזת 
(`"e"`). 
הביטוי הרגולרי ה״דביק״ לא מוצא התאמה כאשר הוא מחפש החל מהתו השני ולכן ערכו של המשתנה 
`stickyResult` 
הוא 
`null`.

הסימון הדביק שומר את סמן האינדקס של התו הבא לאחר ההתאמה האחרונה בתוך התכונה 
`lastIndex` 
לאחר סיום פעולת החיפוש. 
אם חיפוש מסתיים ללא התאמה אזי ערך התכונה 
`lastIndex` 
מאותחל חזרה ל 0. 
הסימון הגלובלי 
`g` 
מתנהג בצורה זהה כפי שמודגם בהמשך:

<div dir="ltr">

```js
var text = "hello1 hello2 hello3",
    pattern = /hello\d\s?/,
    result = pattern.exec(text),
    globalPattern = /hello\d\s?/g,
    globalResult = globalPattern.exec(text),
    stickyPattern = /hello\d\s?/y,
    stickyResult = stickyPattern.exec(text);

console.log(result[0]);         // "hello1 "
console.log(globalResult[0]);   // "hello1 "
console.log(stickyResult[0]);   // "hello1 "

console.log(pattern.lastIndex);         // 0
console.log(globalPattern.lastIndex);   // 7
console.log(stickyPattern.lastIndex);   // 7

result = pattern.exec(text);
globalResult = globalPattern.exec(text);
stickyResult = stickyPattern.exec(text);

console.log(result[0]);         // "hello1 "
console.log(globalResult[0]);   // "hello2 "
console.log(stickyResult[0]);   // "hello2 "

console.log(pattern.lastIndex);         // 0
console.log(globalPattern.lastIndex);   // 14
console.log(stickyPattern.lastIndex);   // 14
```

</div>

ערך התכונה
`lastIndex` 
משתנה ל 7 לאחר הקריאה הראשונה ל 
<span dir="ltr">`exec()`</span>
ול 14 לאחר הקריאה השנייה
הן עבור המשתנה 
`stickyPattern`
והן עבור המשתנה 
`globalPattern`.

קיימים שני דברים נוספים שחשוב לדעת לגבי הסימון הדביק:

1. מתייחסים לתכונה 
`lastIndex` 
אך ורק כאשר משתמשים במתודות שקיימות על הביטוי הרגולרי, כגון המתודות 
<span dir="ltr">`exec()`</span>
ו- 
<span dir="ltr">`test()`</span>. 
העברת הביטוי הרגולרי למתודה של מחרוזת, כמו 
<span dir="ltr">`match()`</span>
לא תפעיל התנהגות דביקה.

1.  כאשר משתמשים בתו
`^`
כדי לחפש התאמה בתחילת מחרוזת, ביטוי רגולרי דביק יחפש התאמות אך ורק מתחילת המחרוזות
(או מתחילת השורה במצב התאמה רב שורות - `multiline`).
כל עוד ערך התכונה
`lastIndex` 
הוא 0, 
אזי התו
`^`
הופך את הביטוי הרגולרי הדביק לזהה לכזה שאינו דביק. 
אם ערך התכונה
`lastIndex`
אינו תואם להתחלת מחרוזת במצב שורה בודדת
<span dir="ltr">`(single-line mode)`</span>
או לתחילת שורה במצב רב שורות
<span dir="ltr">`(multiline mode)`</span>
הביטוי הרגולרי הדביק  לא ימצא התאמה.

כמו בסימונים אחרים של ביטויים רגולריים, ניתן לזהות את נוכחות הסימון 

על ידי שימוש בתכונה מיוחדת. 
במקרה זה עליכם לבדוק את התכונה
`sticky`,

כפי שמוצג בדוגמה הבאה:

<div dir="ltr">

```js
var pattern = /hello\d/y;

console.log(pattern.sticky);    // true
```

</div>

התכונה
`sticky`
מוגדרת כ 
`true`
במידה והסימון הדביק פעיל, ומקבלת את הערך 
`false`
במידה ולא.
התכונה 
`sticky`
אינה ניתנת לשינוי בדרך אחרת והיא נחשבת לתכונה בעלת ערך שניתן לקריאה בלבד

בדומה לסימון
`u`
הסימון 
`y`
מהווה שינוי תחבירי, ולכן יגרום לשגיאת תחביר 
<span dir="ltr">`(syntax error)`</span>
במנועי ריצה ישנים של ג׳אווהסקריפט.
ניתן להשתמש בגישה הבאה על מנת לזהות תמיכה בו:

<div dir="ltr">

```js
function hasRegExpY() {
    try {
        var pattern = new RegExp(".", "y");
        return true;
    } catch (ex) {
        return false;
    }
}
```

</div>

בדומה לבדיקה עבור הסימון 
`u` 
הפונקציה בדוגמה מחזירה את הערך
`false` 
אם לא ניתן ליצור ביטוי רגולרי עם הסימון
`y`. 
במידה ויש צורך להשתמש בסימון 
`y`
במנועי ריצה ישנים של ג׳אווהסקריפט יש להשתמש בקונסטרקטור 
`RegExp`
על מנת להימנע משגיאה תחבירית.

### Duplicating Regular Expressions

In ECMAScript 5, you can duplicate regular expressions by passing them into the `RegExp` constructor like this:

```js
var re1 = /ab/i,
    re2 = new RegExp(re1);
```

The `re2` variable is just a copy of the `re1` variable. But if you provide the second argument to the `RegExp` constructor, which specifies the flags for the regular expression, your code won't work, as in this example:

```js
var re1 = /ab/i,

    // throws an error in ES5, okay in ES6
    re2 = new RegExp(re1, "g");
```

If you execute this code in an ECMAScript 5 environment, you'll get an error stating that the second argument cannot be used when the first argument is a regular expression. ECMAScript 6 changed this behavior such that the second argument is allowed and overrides any flags present on the first argument. For example:

```js
var re1 = /ab/i,

    // throws an error in ES5, okay in ES6
    re2 = new RegExp(re1, "g");


console.log(re1.toString());            // "/ab/i"
console.log(re2.toString());            // "/ab/g"

console.log(re1.test("ab"));            // true
console.log(re2.test("ab"));            // true

console.log(re1.test("AB"));            // true
console.log(re2.test("AB"));            // false

```

In this code, `re1` has the case-insensitive `i` flag while `re2` has only the global `g` flag. The `RegExp` constructor duplicated the pattern from `re1` and substituted the `g` flag for the `i` flag. Without the second argument, `re2` would have the same flags as `re1`.

### The `flags` Property

Along with adding a new flag and changing how you can work with flags, ECMAScript 6 added a property associated with them. In ECMAScript 5, you could get the text of a regular expression by using the `source` property, but to get the flag string, you'd have to parse the output of  the `toString()` method as shown below:

```js
function getFlags(re) {
    var text = re.toString();
    return text.substring(text.lastIndexOf("/") + 1, text.length);
}

// toString() is "/ab/g"
var re = /ab/g;

console.log(getFlags(re));          // "g"
```

This converts a regular expression into a string and then returns the characters found after the last `/`. Those characters are the flags.

ECMAScript 6 makes fetching flags easier by adding a `flags` property to go along with the `source` property. Both properties are prototype accessor properties with only a getter assigned, making them read-only. The `flags` property makes inspecting regular expressions easier for both debugging and inheritance purposes.

A late addition to ECMAScript 6, the `flags` property returns the string representation of any flags applied to a regular expression. For example:

```js
var re = /ab/g;

console.log(re.source);     // "ab"
console.log(re.flags);      // "g"
```

This fetches all flags on `re` and prints them to the console with far fewer lines of code than the `toString()` technique can. Using `source` and `flags` together allows you to extract the pieces of the regular expression that you need without parsing the regular expression string directly.

The changes to strings and regular expressions that this chapter has covered so far are definitely powerful, but ECMAScript 6 improves your power over strings in a much bigger way. It brings a type of literal to the table that makes strings more flexible.

## Template Literals

JavaScript's strings have always had limited functionality compared to strings in other languages. For instance, until ECMAScript 6, strings lacked the methods covered so far in this chapter, and string concatenation is as simple as possible. To allow developers to solve more complex problems, ECMAScript 6's *template literals* provide syntax for creating domain-specific languages (DSLs) for working with content in a safer way than the solutions available in ECMAScript 5 and earlier. (A DSL is a programming language designed for a specific, narrow purpose, as opposed to general-purpose languages like JavaScript.) The ECMAScript wiki offers the following description on the [template literal strawman](http://wiki.ecmascript.org/doku.php?id=harmony:quasis):

> This scheme extends ECMAScript syntax with syntactic sugar to allow libraries to provide DSLs that easily produce, query, and manipulate content from other languages that are immune or resistant to injection attacks such as XSS, SQL Injection, etc.

In reality, though, template literals are ECMAScript 6's answer to the following features that JavaScript lacked all the way through ECMAScript 5:

* **Multiline strings** A formal concept of multiline strings.
* **Basic string formatting** The ability to substitute parts of the string for values contained in variables.
* **HTML escaping** The ability to transform a string such that it is safe to insert into HTML.

Rather than trying to add more functionality to JavaScript's already-existing strings, template literals represent an entirely new approach to solving these problems.

### Basic Syntax

At their simplest, template literals act like regular strings delimited by backticks (`` ` ``) instead of double or single quotes. For example, consider the following:

```js
let message = `Hello world!`;

console.log(message);               // "Hello world!"
console.log(typeof message);        // "string"
console.log(message.length);        // 12
```

This code demonstrates that the variable `message` contains a normal JavaScript string. The template literal syntax is used to create the string value, which is then assigned to the `message` variable.

If you want to use a backtick in your string, then just escape it with a backslash (`\`), as in this version of the `message` variable:

```js
let message = `\`Hello\` world!`;

console.log(message);               // "`Hello` world!"
console.log(typeof message);        // "string"
console.log(message.length);        // 14
```

There's no need to escape either double or single quotes inside of template literals.

### Multiline Strings

JavaScript developers have wanted a way to create multiline strings since the first version of the language. But when using double or single quotes, strings must be completely contained on a single line.

#### Pre-ECMAScript 6 Workarounds

Thanks to a long-standing syntax bug, JavaScript does have a workaround. You can create multiline strings if there's a backslash (`\`) before a newline. Here's an example:

```js
var message = "Multiline \
string";

console.log(message);       // "Multiline string"
```

The `message` string has no newlines present when printed to the console because the backslash is treated as a continuation rather than a newline. In order to show a newline in output, you'd need to manually include it:

```js
var message = "Multiline \n\
string";

console.log(message);       // "Multiline
                            //  string"
```

This should print `Multiline String` on two separate lines in all major JavaScript engines, but the behavior is defined as a bug and many developers recommend avoiding it.

Other pre-ECMAScript 6 attempts to create multiline strings usually relied on arrays or string concatenation, such as:

```js
var message = [
    "Multiline ",
    "string"
].join("\n");

var message = "Multiline \n" +
    "string";
```

All of the ways developers worked around JavaScript's lack of multiline strings left something to be desired.

#### Multiline Strings the Easy Way

ECMAScript 6's template literals make multiline strings easy because there's no special syntax. Just include a newline where you want, and it shows up in the result. For example:

```js
let message = `Multiline
string`;

console.log(message);           // "Multiline
                                //  string"
console.log(message.length);    // 16
```

All whitespace inside the backticks is part of the string, so be careful with indentation. For example:

```js
let message = `Multiline
               string`;

console.log(message);           // "Multiline
                                //                 string"
console.log(message.length);    // 31
```

In this code, all whitespace before the second line of the template literal is considered part of the string itself. If making the text line up with proper indentation is important to you, then consider leaving nothing on the first line of a multiline template literal and then indenting after that, as follows:

```js
let html = `
<div>
    <h1>Title</h1>
</div>`.trim();
```

This code begins the template literal on the first line but doesn't have any text until the second. The HTML tags are indented to look correct and then the `trim()` method is called to remove the initial empty line.

A> If you prefer, you can also use `\n` in a template literal to indicate where a newline should be inserted:
A> {:lang="js"}
A> ~~~~~~~~
A>
A> let message = `Multiline\nstring`;
A>
A> console.log(message);           // "Multiline
A>                                 //  string"
A> console.log(message.length);    // 16
A> ~~~~~~~~

### Making Substitutions

At this point, template literals may look like fancier versions of normal JavaScript strings. The real difference between the two lies in template literal *substitutions*. Substitutions allow you to embed any valid JavaScript expression inside a template literal and output the result as part of the string.

Substitutions are delimited by an opening `${` and a closing `}` that can have any JavaScript expression inside. The simplest substitutions let you embed local variables directly into a resulting string, like this:

```js
let name = "Nicholas",
    message = `Hello, ${name}.`;

console.log(message);       // "Hello, Nicholas."
```

The substitution `${name}` accesses the local variable `name` to insert `name` into the `message` string. The `message` variable then holds the result of the substitution immediately.

I> A template literal can access any variable accessible in the scope in which it is defined. Attempting to use an undeclared variable in a template literal throws an error in both strict and non-strict modes.

Since all substitutions are JavaScript expressions, you can substitute more than just simple variable names. You can easily embed calculations, function calls, and more. For example:

```js
let count = 10,
    price = 0.25,
    message = `${count} items cost $${(count * price).toFixed(2)}.`;

console.log(message);       // "10 items cost $2.50."
```

This code performs a calculation as part of the template literal. The variables `count` and `price` are multiplied together to get a result, and then formatted to two decimal places using `.toFixed()`. The dollar sign before the second substitution is output as-is because it's not followed by an opening curly brace.

Template literals are also JavaScript expressions, which means you can place a template literal inside of another template literal, as in this example:

```js
let name = "Nicholas",
    message = `Hello, ${
        `my name is ${ name }`
    }.`;

console.log(message);        // "Hello, my name is Nicholas."
```

This example nests a second template literal inside the first. After the first `${`, another template literal begins. The second `${` indicates the beginning of an embedded expression inside the inner template literal. That expression is the variable `name`, which is inserted into the result.

### Tagged Templates

Now you've seen how template literals can create multiline strings and insert values into strings without concatenation. But the real power of template literals comes from tagged templates. A *template tag* performs a transformation on the template literal and returns the final string value. This tag is specified at the start of the template, just before the first `` ` `` character, as shown here:

```js
let message = tag`Hello world`;
```

In this example, `tag` is the template tag to apply to the `` `Hello world` `` template literal.

#### Defining Tags

A *tag* is simply a function that is called with the processed template literal data. The tag receives data about the template literal as individual pieces and must combine the pieces to create the result. The first argument is an array containing the literal strings as interpreted by JavaScript. Each subsequent argument is the interpreted value of each substitution.

Tag functions are typically defined using rest arguments as follows, to make dealing with the data easier:

```js
function tag(literals, ...substitutions) {
    // return a string
}
```

To better understand what gets passed to tags, consider the following:

```js
let count = 10,
    price = 0.25,
    message = passthru`${count} items cost $${(count * price).toFixed(2)}.`;
```

If you had a function called `passthru()`, that function would receive three arguments. First, it would get a `literals` array, containing the following elements:

* The empty string before the first substitution (`""`)
* The string after the first substitution and before the second (`" items cost $"`)
* The string after the second substitution (`"."`)

The next argument would be `10`, which is the interpreted value for the `count` variable. This becomes the first element in a `substitutions` array. The final argument would be `"2.50"`, which is the interpreted value for `(count * price).toFixed(2)` and the second element in the `substitutions` array.

Note that the first item in `literals` is an empty string. This ensures that `literals[0]` is always the start of the string, just like `literals[literals.length - 1]` is always the end of the string. There is always one fewer substitution than literal, which means the expression `substitutions.length === literals.length - 1` is always true.

Using this pattern, the `literals` and `substitutions` arrays can be interwoven to create a resulting string. The first item in `literals` comes first, the first item in `substitutions` is next, and so on, until the string is complete. As an example, you can mimic the default behavior of a template literal by alternating values from these two arrays:

```js
function passthru(literals, ...substitutions) {
    let result = "";

    // run the loop only for the substitution count
    for (let i = 0; i < substitutions.length; i++) {
        result += literals[i];
        result += substitutions[i];
    }

    // add the last literal
    result += literals[literals.length - 1];

    return result;
}

let count = 10,
    price = 0.25,
    message = passthru`${count} items cost $${(count * price).toFixed(2)}.`;

console.log(message);       // "10 items cost $2.50."
```

This example defines a `passthru` tag that performs the same transformation as the default template literal behavior. The only trick is to use `substitutions.length` for the loop rather than `literals.length` to avoid accidentally going past the end of the `substitutions` array. This works because the relationship between `literals` and `substitutions` is well-defined in ECMAScript 6.

I> The values contained in `substitutions` are not necessarily strings. If an expression evaluates to a number, as in the previous example, then the numeric value is passed in. Determining how such values should output in the result is part of the tag's job.

#### Using Raw Values in Template Literals

Template tags also have access to raw string information, which primarily means access to character escapes before they are transformed into their character equivalents. The simplest way to work with raw string values is to use the built-in `String.raw()` tag. For example:

```js
let message1 = `Multiline\nstring`,
    message2 = String.raw`Multiline\nstring`;

console.log(message1);          // "Multiline
                                //  string"
console.log(message2);          // "Multiline\nstring"
```

In this code, the `\n` in `message1` is interpreted as a newline while the `\n` in `message2` is returned in its raw form of `"\\n"` (the slash and `n` characters). Retrieving the raw string information like this allows for more complex processing when necessary.

The raw string information is also passed into template tags. The first argument in a tag function is an array with an extra property called `raw`. The `raw` property is an array containing the raw equivalent of each literal value. For example, the value in `literals[0]` always has an equivalent `literals.raw[0]` that contains the raw string information. Knowing that, you can mimic `String.raw()` using the following code:

```js
function raw(literals, ...substitutions) {
    let result = "";

    // run the loop only for the substitution count
    for (let i = 0; i < substitutions.length; i++) {
        result += literals.raw[i];      // use raw values instead
        result += substitutions[i];
    }

    // add the last literal
    result += literals.raw[literals.length - 1];

    return result;
}

let message = raw`Multiline\nstring`;

console.log(message);           // "Multiline\nstring"
console.log(message.length);    // 17
```

This uses `literals.raw` instead of `literals` to output the string result. That means any character escapes, including Unicode code point escapes, should be returned in their raw form. Raw strings are helpful when you want to output a string containing code in which you'll need to include the character escaping (for instance, if you want to generate documentation about some code, you may want to output the actual code as it appears).

## Summary

Full Unicode support allows JavaScript to deal with UTF-16 characters in logical ways. The ability to transfer between code point and character via `codePointAt()` and `String.fromCodePoint()` is an important step for string manipulation. The addition of the regular expression `u` flag makes it possible to operate on code points instead of 16-bit characters, and the `normalize()` method allows for more appropriate string comparisons.

ECMAScript 6 also added new methods for working with strings, allowing you to more easily identify a substring regardless of its position in the parent string. More functionality was added to regular expressions, too.

Template literals are an important addition to ECMAScript 6 that allows you to create domain-specific languages (DSLs) to make creating strings easier. The ability to embed variables directly into template literals means that developers have a safer tool than string concatenation for composing long strings with variables.

Built-in support for multiline strings also makes template literals a useful upgrade over normal JavaScript strings, which have never had this ability. Despite allowing newlines directly inside the template literal, you can still use `\n` and other character escape sequences.

Template tags are the most important part of this feature for creating DSLs. Tags are functions that receive the pieces of the template literal as arguments. You can then use that data to return an appropriate string value. The data provided includes literals, their raw equivalents, and any substitution values. These pieces of information can then be used to determine the correct output for the tag.

</div>