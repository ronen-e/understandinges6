<div dir="rtl">

# פרומיס ותכנות אסינכרוני

אחד ההיבטים החשובים בג׳אווהסקריפט הינו הקלות בה היא מטפלת בתכנות אסינכרוני. בתור שפה שנוצרה לעולם האינטרנט, היה צורך עבור ג׳אווהסקריפט עוד מן ההתחלה להגיב לפעולות משתמש אסינכרוניות כמו לחיצה על כפתורי עכבר או מקלדת.
Node.js
הפך תכנות אסינכרוני בג׳אווהסקריפט לנפוץ יותר כאשר השתמש בפונקציות קולבק בתור אלטרנטיבה לאירועים בקוד. ככל שיותר תוכנות עברו להשתמש בתכנות אסינכרוני כדבר שבשגרה, אירועים ופונקציות קולבק לא נתנו מענה מספק למפתחים.
אוביקטים מסוג
*פרומיס*
(*Promises*)
מהווים את הפתרון לבעיה זו.
(פרומיסים בקיצור)

פרומיס מהווה אופציה נוספת עבור תכנות אסינכרוני והוא בדומה לפתרונות כמו
futures
ו-
deferreds
בשפות אחרות.
פרומיס מגדיר קוד שירוץ מאוחר יותר
(בדומה לאירועים וקולבקים)
ובנוסף מגדיר באופן מפורש האם הקוד מצליח או נכשל במשימותו. ניתן לקשור פרומיסים ביחד על בסיס תוצאה של הצלחה או כשלון בדרכים שמקלות על הבנת הקוד ופתרון שגיאות בו.

על מנת להבין היטב כיצד עובד פרומיס, חשוב להבין את העקרונות עליהם הוא מבוסס.

## הרקע לתכנות אסינכרוני

מנועי ריצה של ג׳אווהסקריפט בנויים על בסיס לולאת אירועים שפועלת בתהליך בודד.
<span dir="ltr">(single-threaded event loop)</span>.
*תהליך בודד*
<span dir="ltr">(*Single-threaded*)</span>
משמעותו שרק יחידת קוד אחת רצה בכל זמן נתון. וזאת בניגוד לשפות כמו
Java
או
C++,
היכן שתהליכים יכולים לאפשר למספר יחידות קוד לרוץ בכל זמן נתון. שמירה על מצב הנתונים
(
״סטייט״ -
state
)
כמו גם הגנה עליו מפני שינוי של יחידות קוד מרובות בעלות גישה לסטייט אינה דבר קל ומהווה מקור נפוץ לבאגים בתוכנה מבוססת תהליכים מרובים.

מנועי ריצה של ג׳אווהסקריפט יכולים להריץ רק יחידת קוד אחת בכל זמן נתון, ולכן עליהם לעקוב אחר הקוד אותו יש להריץ. הקוד הזה נשמר בתוך
*תור המשימות*
(*job queue*).
בכל פעם שיחידת קוד מוכנה להרצה מוסיפים אותה לתור המשימות. כאשר מנוע הריצה של ג׳אווהסקריפט סיים להריץ את הקוד, אזי לולאת האירועים מריצה את המשימה הבאה בתור.
*לולאת האירועים*
(*event loop*)
הינה תוכנית בתוך מנוע הריצה של ג׳אווהסקריפט שמנטרת את הרצת הקוד ומנהלת את תור המשימות. שימו לב שכמו כל תור רגיל, הרצת המשימות מתחילה במשימה הראשונה בתור וממשיכה עד המשימה האחרונה בתור.

### מודל האירועים

כאשר משתמש לוחץ על כפתור או על מקש המקלדת נורה
*אירוע*
(*event*)
כדוגמת
`onclick`.
האירוע יכול להגיב ללחיצה על ידי הוספת משימה חדשה לסוף תור המשימות. זו הצורה הבסיסית ביותר של תכנות אסינכרוני. הקוד שמטפל באירוע
(event handler)
לא ירוץ עד אשר נורה האירוע, וכאשר ירוץ יהיה לו את ההקשר
(context)
המתאים.
לדוגמה:

<div dir="ltr">

```js
let button = document.getElementById("my-btn");
button.onclick = function(event) {
    console.log("Clicked");
};
```

</div>

בדוגמה לעיל, הקוד
<span dir="ltr">`console.log("Clicked")`</span>
לא ירוץ עד אשר לוחצים על
`button`.
כאשר
`button`
נלחץ,
הפונקציה שחוברה אל
`onclick`
נוספת לסוף תור המשימות ותרוץ כאשר כל המשימות לפניו יסיימו את עבודתן.

אירועים עובדים היטב עבור אינטרקציות פשוטות, אך חיבור מספר רב של פעולות אסינכרוניות נפרדות מורכב יותר כיוון שיש לעקוב אחר יוצר האירוע
(
`button`
בדוגמה הקודמת
)
בעבור כל אירוע בנפרד.
בנוסף, יש לוודא שכל מטפלי האירועים חוברו לתהליך לפני שהאירוע נורה בפעם הראשונה.
חישבו לדוגמה על מצב שבו
`button`
נלחץ לפני החיבור של הפונקציה אל
`onclick`.
במצב כזה לא יקרה דבר.
לכן למרות שאירועים שימושיים עבורנו בכדי להגיב לאינטרקציות משתמש בדף אינטרנט ופונקציונליות דומה שאינה קורה בתדירות גבוהה, הם אינם גמישים דיו עבור צרכים מורכבים יותר.

### שיטת הקולבק

כאשר נוצר
Node.js
הוא קידם את מודל התכנות האסינכרוני על ידי הפיכת שיטת הקולבק לנפוצה יותר. שיטת הקולבק דומה למודל האירועים מכיוון שהקוד האסינכרוני לא ירוץ עד לזמן מאוחר יותר. היא שונה מכיוון שהפונקציה לה קוראים מועברת בתור ארגומנט, כמו בדוגמה הבאה:

<div dir="ltr">

```js
readFile("example.txt", function(err, contents) {
    if (err) {
        throw err;
    }

    console.log(contents);
});
console.log("Hi!");
```

</div>

הדוגמה משתמשת בסגנון הקולבק המסורתי
*שגיאה-קודם*
(*error-first*)
של
Node.js.
הפונקציה
<span dir="ltr">`readFile()`</span>
מיועדת לקרוא מתוך קובץ בדיסק הקשיח
(זהותו מופיעה בתור הארגומנט הראשון)
ולאחר מכן להריץ את פונקציית הקולבק
(הארגומנט השני)
כאשר הפעולה מסתיימת. במידה וקיימת שגיאה, אזי הארגומנט
`err`
עבור הקולבק הוא אוביקט השגיאה.
אם אין שגיאה אזי הארגומנט
`contents`
מכיל את תוכן הקובץ בתור מחרוזת.

בעזרת שיטת הקולבק, הפונקציה
<span dir="ltr">`readFile()`</span>
מתחילה לרוץ באופן מיידי ועוצרת כאשר היא מתחילה לקרוא מהדיסק. כלומר הפקודה
<span dir="ltr">`console.log("Hi!")`</span>
מופעלת מיד לאחר הקריאה לפונקציה
<span dir="ltr">`readFile()`</span>,
לפני שהפקודה
<span dir="ltr">`console.log(contents)`</span>
תדפיס פלט כלשהו.
כאשר הפונקציה
<span dir="ltr">`readFile()`</span>
מסיימת את פעולתה, היא תוסיף משימה חדשה לתור המשימות עם פונקציית הקולבק והארגומנטים שלה. המשימה אזי תרוץ לאחר תום פעולת כל המשימות שנמצאות לפניה בתור.

שיטת הקולבק יותר גמישה מעבודה עם אירועים מכיוון שקל יותר לחבר מספר קריאות קולבק ביחד.
לדוגמה:

<div dir="ltr">

```js
readFile("דוגמה.txt", function(err, contents) {
    if (err) {
        throw err;
    }

    writeFile("דוגמה.txt", function(err) {
        if (err) {
            throw err;
        }

        console.log("הקובץ נכתב!");
    });
});
```

</div>

בקוד לעיל, קריאה מוצלחת לפונקציה
<span dir="ltr">`readFile()`</span>
מובילה לקריאה אסינכרונית נוספת, הפעם של הפונקציה
<span dir="ltr">`writeFile()`</span>.
שימו לב לתבנית הבסיסית של בדיקת הערך
`err`
קיימת בשתי הפונקציות. כאשר הפונקציה
<span dir="ltr">`readFile()`</span>
מסתיימת, היא מוסיפה משימה לתור המשימות שמסתיימת בקריאה לפונקציה
<span dir="ltr">`writeFile()`</span>
(בהנחה שלא היו שגיאות).
או אז, הפונקציה
<span dir="ltr">`writeFile()`</span>
מוסיפה משימה חדשה לתור המשימות כאשר תסיים את פעולתה.

שיטה זו עובדת היטב, אך ייתכן ועד מהרה תמצאו עצמכם בתוך מה שנקרא
״גיהנום הקולבקים״
(*callback hell*).
גיהנום הקולבקים קורה כאשר יותר מדי פונקציות קולבק קוראות אחת לשניה.
לדוגמה:

<div dir="ltr">

```js
method1(function(err, result) {

    if (err) {
        throw err;
    }

    method2(function(err, result) {

        if (err) {
            throw err;
        }

        method3(function(err, result) {

            if (err) {
                throw err;
            }

            method4(function(err, result) {

                if (err) {
                    throw err;
                }

                method5(result);
            });

        });

    });

});
```

</div>

חיבור מספר קריאות אסינכרוניות אחת לשניה כמו בדוגמה לעיל יוצר רשת מסועפת של קוד שקשה להבין ולתקן בה שגיאות. פונקציות קולבק גם מקשות עלינו לממש פונקציונליות מורכבת יותר. מה אם נרצה ששתי פעולות אסינכרוניות ירוצו במקביל ולקבל עדכון כאשר שתיהן הסתיימו? מה אם נרצה להתחיל שתי פעולות אסינכרוניות באותו זמן אך לטפל רק בתוצאת הפעולה שהסתיימה ראשונה?

במקרים אילו נצטרך לעקוב אחר מספר פונקציות קולבק ופעולות ניקיון והשלמה ואובייקטים מסוג פרומיס מקלים עלינו לעשות זאת.

## עקרונות בסיס לפרומיס

פרומיס הינו מעטפת לתוצאה של פעולה אסינכרונית. במקום להירשם לאירוע או להעביר פונקציית קולבק בתור ארגומנט, הפונקציה יכולה להחזיר פרומיס, כמו בדוגמה הבאה:

<div dir="ltr">

```js
// הפונקציה מבטיחה להסתיים בנקודת זמן עתידית
let promise = readFile("example.txt");
```
</div>

בדוגמה זו, הפונקציה
<span dir="ltr">`readFile()`</span>
לא מתחילה לקרוא את הקובץ באופן מיידי. זה יקרה רק מאוחר יותר. תחת זאת, הפונקציה מחזירה אובייקט פרומס שמייצג את פעולת הקריאה האסינכרונית כך שיהיה ניתן לעבוד איתו בהמשך.
הזמן המדויק שבו ניתן יהיה לעבוד עם התוצאה תלוי באופן שבו הפרומיס מתנהל, או במה שנקרא ״מחזור החיים של הפרומיס״
<span dir="ltr">(The Promise Lifecycle)</span>.

### מחזור החיים של הפרומיס

כל פרומיס עובר מחזור חיים קצר שמתחיל במצב המתנה
(*pending*),
שמשמעותו שהפעולה האסינכרונית עוד לא הסתיימה.
פרומיס במצב המתנה נחשב לפרומיס
*לא פתור*
(*unsettled*).
הפרומיס בדוגמה האחרונה נמצא במצב המתנה למן הרגע שהפונקציה
<span dir="ltr">`readFile()`</span>
מחזירה אותו. ברגע שהפעולה האסינכרונית מסתיימת הפרומיס נחשב לפרומיס
*פתור*
(*settled*)
ונמצא באחד משני מצבים אפשריים:

1. *הושלם / הצליח*
(*Fulfilled*):
הפעולה האסינכרונית הושלמה בהצלחה.
1. *נדחה*
(*Rejected*):
הפעולה האסינכרונית לא הושלמה בהצלחה עקב שגיאה או סיבה אחרת.

תכונה פנימית בשם
`[[PromiseState]]`
מקבלת את הערך
`"pending"`, `"fulfilled"`,
או
`"rejected"`
כדי לייצג את מצב הפרומיס. התכונה אינה חשופה על אובייקט הפרומיס עצמו, כך שלא ניתן לדעת באמצעות פקודה מהו מצב הפרומיס. אך ניתן לבצע פעולה כאשר הפרומיס משנה את מצבה על ידי שימוש במתודת
<span dir="ltr">`then()`</span>.

המתודה
<span dir="ltr">`then()`</span>
קיימת על כל פרומיס ומקבלת שני ארגומנטים. הארגומנט הראשון הוא הפונקציה שתופעל כאשר הפרומיס תושלם בהצלחה. כל נתון נוסף שקשור לפעולה האסינכרונית מועבר לפונקציית ההצלחה. הארגומנט השני הוא הפונקציה שתופעל כאשר הפרומיס נדחה. בדומה לפונקציית ההצלחה, פונקציית הדחייה תקבל כל נתון נוסף שקשור לדחיה.

I> כל אוביקט שמממש מתודת
<span dir="ltr">`then()`</span>
באופן כזה נחשב לאוביקט
*ד׳נבילי*
(*thenable*).
כל פרומיס הוא ד׳נבילי אבל לא כל אובייקט ד׳נבילי הוא פרומיס.

שני הארגומנטים עבור
<span dir="ltr">`then()`</span>
הם אופציונליים, כך שניתן לפעול בהתאם לכל צירוף של הצלחה ודחייה. ראו למשל את השילוב הבא של קריאות למתודה
<span dir="ltr">`then()`</span>

<div dir="ltr">

```js
let promise = readFile("example.txt");

promise.then(function(contents) {
    // הצלחה
    console.log(contents);
}, function(err) {
    // דחייה
    console.error(err.message);
});

promise.then(function(contents) {
    // הצלחה
    console.log(contents);
});

promise.then(null, function(err) {
    // דחייה
    console.error(err.message);
});
```
</div>

כל שלושת הקריאות למתודה
<span dir="ltr">`then()`</span>
פועלות על אותו פרומיס. הקריאה הראשונה מאזינה גם להצלחה וגם לדחייה. הקריאה השנייה מאזינה אך ורק להצלחה. לא ידווח על דחייה.
השלישית מאזינה אך ורק לדחייה ולא תדווח על הצלחה.

לאובייקטים מסוג פרומיס יש מתודה בשם
<span dir="ltr">`catch()`</span>
שמתנהגת באותה צורה כמו המתודה
<span dir="ltr">`then()`</span>
כאשר היא מקבלת רק פונקציית דחייה כארגומנט. כך לדוגמה הקריאות בדוגמה הבאה אל
<span dir="ltr">`catch()`</span>
ואל
<span dir="ltr">`then()`</span>
הינן למעשה זהות מבחינה פונקציונלית.

<div dir="ltr">

```js
promise.catch(function(err) {
    // דחייה
    console.error(err.message);
});

// זהה לקוד

promise.then(null, function(err) {
    // דחייה
    console.error(err.message);
});
```

</div>

הרעיון מאחורי המתודות
<span dir="ltr">`then()`</span>
ו-
<span dir="ltr">`catch()`</span>
הוא להשתמש בהן ביחד כדי לטפל בתוצאת פעולות אסינכרוניות. שיטה זו טובה יותר מאשר שימוש באירועים ופונקציות קולבק מכיוון והיא מראה באופן ברור את תוצאת הפעולה
(
    אירועים נוטים שלא לפעול כאשר יש שגיאה ובפונקציות קולבק צריך לעקוב אחר ארגומנט השגיאה
).
חשוב לזכור שאם לא נחבר פונקציית דחייה לפרומיס אזי לא תדווח הדחייה. לכן תמיד כדאי לחבר פונקצית דחיה לפרומיס גם אם הפונקציה רק מדפיסה את הדחיה כפלט.

פונקציה שמטפלת בהצלחה או כשלון עדיין תרוץ גם אם מוסיפים אותה לתור המשימות לאחר שהפרומיס פתור. זה מאפשר לנו להוסיף פונקציות לטיפול דחייה והצלחה בכל זמן שנרצה ומבטיח שהן ייקראו.
לדוגמה:

<div dir="ltr">

```js
let promise = readFile("example.txt");

// מטפל ההצלחה המקורי
promise.then(function(contents) {
    console.log(contents);

    // הוספת מטפל הצלחה נוסף
    promise.then(function(contents) {
        console.log(contents);
    });
});
```

</div>

בקוד לעיל, מטפל ההצלחה מוסיף בתוכו עוד מטפל הצלחה לאותו פרומיססס. הפרומיס כבר הצליח באותה נקודה ולכן מוסיפים את מטפל ההצלחה החדש לתור המשימות וקוראים לו כאשר הדבר מתאפשר. מטפלי דחייה עובדים באותו אופן.

I> כל קריאה למתודות
<span dir="ltr">`then()`</span>
ו-
<span dir="ltr">`catch()`</span>
יוצרת משימה חדשה להריץ כאשר הפונקציה נפתרת. המשימות האלו מגיעות לתור משימות נפרד ששמור רק לאוביקטים מסוג פרומיס. הפרטים המדוייקים של תור המשימות הנפרד אינם חשובים מבחינת הבנת אופן פעולת הפרומיס, כל עוד אנו מבינים כיצד תור משימות עובד באופן כללי.

### יצירת פרומיס לא פתור

אוביקטים חדשים מסוג פרומיס נוצרים על ידי שימוש בקונסטרקטור
`Promise`.
הקונסטרקטור מקבל ארגומנט אחד: פונקציה שידועה בתור פונקציית
*ההרצה*
(*executor*),
שמכילה את הקוד לאתחול הפרומיס.
ה-
`executor`
מקבלת כארגומנטים שתי פונקציות בשם
<span dir="ltr">`resolve()`</span>
ו-
<span dir="ltr">`reject()`</span>.
הפונקציה
<span dir="ltr">`resolve()`</span>
נקראת לאחר שפונקציית ה-
`executor`
הסתיימה בהצלחה כדי לאותת שהפרומיס עוברת למצב הצלחה, בעוד שהפונקציה
<span dir="ltr">`reject()`</span>
מאותתת שפונקציית ה
`executor`
נכשלה.

הנה דוגמה שמשתמשת בפרומיס בסביבת
Node.js
על מנת לממש את הפונקציה
<span dir="ltr">`readFile()`</span>
שראינו מוקדם יותר בפרק הנוכחי:

<div dir="ltr">

```js
// דוגמת
// Node.js example

let fs = require("fs");

function readFile(filename) {
    return new Promise(function(resolve, reject) {

        // הפעלת הפעולה האסינכרונית
        fs.readFile(filename, { encoding: "utf8" }, function(err, contents) {

            // בדיקת שגיאות
            if (err) {
                reject(err);
                return;
            }

            // הפעולה הושלמה בהצלחה
            resolve(contents);

        });
    });
}

let promise = readFile("example.txt");

// מאזינים גם להצלחה וגם לדחייה
promise.then(function(contents) {
    // הצלחה
    console.log(contents);
}, function(err) {
    // דחייה
    console.error(err.message);
});
```
</div>

בדוגמה לעיל הפעולה האסינכרונית המובנית בתוך
Node.js
של
<span dir="ltr">`fs.readFile()`</span>
עטופה בפרומיס.
פונקציית ה-
`executor`
מעבירה את אובייקט השגיאה אל הפונקציה
<span dir="ltr">`reject()`</span>
או שהיא מעבירה את תוכן הקובץ לפונקציה
<span dir="ltr">`resolve()`</span>

חשוב לזכור שפונקציית ה-
`executor`
רצה מיד כאשר הפונקציה
<span dir="ltr">`readFile()`</span>
נקראת. כאשר קוראים בתוך ה-
`executor`
אל הפונקציות
<span dir="ltr">`resolve()`</span>
או
<span dir="ltr">`reject()`</span>,
מוסיפים משימה חדשה לתור המשימות כדי להביא את הפרומיס למצב פתור.
פעולה זו נקראית
*תזמון משימות*
(*job scheduling*),
ואם אי פעם השתמשתם בפונקציות
<span dir="ltr">`setTimeout()`</span>
או
<span dir="ltr">`setInterval()`</span>,
אז הנושא מוכר לכם. בתזמון משימות, אנו מוסיפים משימה חדשה לתור המשימות ואומרים למעשה,
״אל תרוצי עכשיו, אבל תרוצי בזמן מאוחר יותר״. כך למשל, הפונקציה
<span dir="ltr">`setTimeout()`</span>
מאפשרת לנו להגדיר שיהוי לפני שהמשימה מתווספת לתור המשימות:

<div dir="ltr">

```js
// מוסיפים את הפונקציה לתור המשימות לאחר חצי שניה
setTimeout(function() {
    console.log("Timeout");
}, 500);

console.log("Hi!");
```
</div>

הקוד לעיל מתזמן משימה שתתווסף לתור המשימות לאחר 500 מילישניות.
שתי הקריאות אל
<span dir="ltr">`console.log()`</span>
מייצרות את הפלט הבא:

```
Hi!
Timeout
```

הודות לשיהוי של 500 מילישניות הפלט עבור הפונקציה שהועבר אל תוך
<span dir="ltr">`setTimeout()`</span>
הוצג לאחר הפלט שהגיע מהקריאה אל
<span dir="ltr">`console.log("Hi!")`</span>.

אובייקטים מסוג פרומיס עובדים בצורה דומה. פונקציית ההרצה רצה באופן מיידי, לפני כל קוד אחר שמופיע לאחר מכן. למשל:

<div dir="ltr">

```js
let promise = new Promise(function(resolve, reject) {
    console.log("Promise");
    resolve();
});

console.log("Hi!");
```

</div>

הפלט עבור הקוד בדוגמה הוא:

```
Promise
Hi!
```

קריאה לפונקציה
<span dir="ltr">`resolve()`</span>
מפעילה פעולה אסינכרונית. פונקציות שמועברות אל
<span dir="ltr">`then()`</span>
ו-
<span dir="ltr">`catch()`</span>
רצות באופן אסינכרוני, כיוון שגם הן מתווספות לתור המשימות.
הנה דוגמה:

<div dir="ltr">

```js
let promise = new Promise(function(resolve, reject) {
    console.log("Promise");
    resolve();
});

promise.then(function() {
    console.log("Resolved.");
});

console.log("Hi!");
```

</div>

הפלט עבור דוגמה זו הוא:

```
Promise
Hi!
Resolved
```

שימו לב שאף על פי שהקריאה אל
<span dir="ltr">`then()`</span>
מופיעה לפני הקוד
<span dir="ltr">`console.log("Hi!")`</span>,
היא לא באמת רצה עד מאוחר יותר
(בניגוד לפונקציית ההרצה).
זה קורה מפני שמטפלי ההצלחה והדחייה תמיד נכנסים לסוף תור המשימות לאחר שהסתיימה פעולת פונקציית ההרצה.

### יצירת פרומיס פתור

הקונסטרקטור
`Promise`
הוא הדרך הטובה ביותר ליצירת פרומיס לא פתור עקב האופי הדינמי של פעולת פונקציית ההרצה של הפרומיס.
במידה ותרצו פרומיס שמייצג ערך ידוע מראש, אזי אין הגיון רב בתזמון משימה שרק מעבירה את אותו הערך לפונקציה
<span dir="ltr">`resolve()`</span>.
במקום זאת, קיימות שתי דרכים ליצירת פרומיס פתור בעל ערך מסוים.

#### <span dir="ltr">`Promise.resolve()`</span>

המתודה
<span dir="ltr">`Promise.resolve()`</span>
מקבלת ארגומנט אחד בודד ומחזירה פרומיס שהסתיים בהצלחה.
המשמעות היא שלא יתבצע תזמון משימות, ועלינו להוסיף מטפל הצלחה אחד או יותר על מנת לקבל את אותו הערך.
לדוגמה:

<div dir="ltr">

```js
let promise = Promise.resolve(42);

promise.then(function(value) {
    console.log(value);         // 42
});
```

</div>

הקוד לעיל מייצר פרומיס שעבר בהצלחה ולכן מטפל ההצלחה מקבל את הערך 42 עבור המשתנה
`value`.
אם היינו מוסיפים מטפל דחייה לפרומיס, אזי מטפל הדחייה לא היה נקרא לעולם מאחר והפרומיס לא יכול לשנות מצב לאחר שנקבע ולכן לעולם לא יגיע אל מצב דחוי.

#### <span dir="ltr">`Promise.reject()`</span>

ניתן גם ליצור פרומיס במצב דחוי על ידי שימוש במתודה
<span dir="ltr">`Promise.reject()`</span>.
זה עובד בדיוק כמו
<span dir="ltr">`Promise.resolve()`</span>
מלבד העובדה שהפרומיס יווצר במצב דחוי, כמו בדוגמה הבאה:

<div dir="ltr">

```js
let promise = Promise.reject(42);

promise.catch(function(value) {
    console.log(value);         // 42
});
```
</div>

כל מטפל דחייה נוסף על אותו הפרומיס ייקרא בתורו, אך לא יופעל אף מטפל הצלחה.

I> אם נעביר פרומיס לתוך
<span dir="ltr">`Promise.resolve()`</span>
או
<span dir="ltr">`Promise.reject()`</span>,
הפרומיס מוחזר ללא שינוי.

#### אוביקט ד׳נבילי שאינו פרומיס

גם
<span dir="ltr">`Promise.resolve()`</span>
וגם
<span dir="ltr">`Promise.reject()`</span>
יכולים לקבל אובייקט ד׳נבילי שאינו פרומיס בתור ארגומנט. כאשר מעבירים אובייקט ד׳נבילי שאינו פרומיס נוצר פרומיס חדש שנפתר לאחר הקריאה לפונקציה
<span dir="ltr">`then()`</span>.

אוביקט ד׳נבילי שאינו פרומיס נוצר כאשר לאובייקט יש מתודת
<span dir="ltr">`then()`</span>
שמקבלת ארגומנטים מסוג
`resolve`
וגם
`reject`
כמו בדוגמה הבאה:

<div dir="ltr">

```js
let thenable = {
    then: function(resolve, reject) {
        resolve(42);
    }
};
```
</div>

לאובייקט
`thenable`
בדוגמה לעיל אין שום מאפיינים של פרומיס מעבר למתודה
<span dir="ltr">`then()`</span>.
ניתן לקרוא ל
<span dir="ltr">`Promise.resolve()`</span>
כדי להמיר את
`thenable`
לפרומיס שהושלם בהצלחה:

<div dir="ltr">

```js
let thenable = {
    then: function(resolve, reject) {
        resolve(42);
    }
};

let p1 = Promise.resolve(thenable);
p1.then(function(value) {
    console.log(value);     // 42
});
```
</div>

בדוגמה לעיל,
<span dir="ltr">`Promise.resolve()`</span>
קוראת
<span dir="ltr">`thenable.then()`</span>
כדי לקבוע את מצב הפרומיס. מצב הפרומיס עבור
`thenable`
הינו הצלחה מאחר שהפונקציה
<span dir="ltr">`resolve(42)`</span>
נקראת בתוך המתודה
<span dir="ltr">`then()`</span>.
פרומס חדש בשם
`p1`
נוצר במצב הצלחה כאשר הערך מגיע מתוך האובייקט
`thenable`
(הערך הוא 42),
ומטפל ההצלחה עבור
`p1`
מקבל אליו את הערך 42.

ניתן להשתמש ב
<span dir="ltr">`Promise.resolve()`</span>
עם אותם צעדים כדי ליצור פרומיס דחוי מתוך אובייקט ד׳נבילי

<div dir="ltr">

```js
let thenable = {
    then: function(resolve, reject) {
        reject(42);
    }
};

let p1 = Promise.resolve(thenable);
p1.catch(function(value) {
    console.log(value);     // 42
});
```
</div>

הדוגמה לעיל דומה לדוגמה האחרונה מלבד העובדה שהאובייקט
`thenable`
מגיע למצב דחוי.
כאשר ירוץ הקוד
<span dir="ltr">`thenable.then()`</span>,
ייווצר אובייקט פרומיס חדש במצב דחוי עם הערך 42. אותו ערך מועבר למטפל הדחייה עבור
`p1`.

<span dir="ltr">`Promise.resolve()`</span>
ו-
<span dir="ltr">`Promise.reject()`</span>
עובדות בצורה זו על מנת להקל עלינו לעבוד עם אובייקטים ד׳נבילים שאינם מסוג פרומיס. ספריות רבות השתמשו באובייקטים ד׳נבילים לפני שפרומיס הופיע באקמהסקריפט 6, כך שהיכולת להמיר אובייקטים ד׳נביליים לפרומיס סטנדרטי חשובה עבור תאימות לאחור עם ספריות קיימות. כאשר יש ספק האם אובייקט מסויים הינו פרומיס העברתו דרך
<span dir="ltr">`Promise.resolve()`</span>
או
<span dir="ltr">`Promise.reject()`</span>
(בהתאם לתוצאה הרצויה)
הינה הדרך הטובה ביותר לעשות זאת, מאחר ופרומיס שמועבר כארגומנט מוחזר כמות שהוא ללא שינוי.

### שגיאות בפונקציית ההרצה

במידה ונזרקת שגיאה בתוך פונקציית ההרצה
(executor)
ייקרא מטפל הדחיה של הפרומיס. לדוגמה:

<div dir="ltr">

```js
let promise = new Promise(function(resolve, reject) {
    throw new Error("Explosion!");
});

promise.catch(function(error) {
    console.log(error.message);     // "Explosion!"
});
```
</div>

בקוד לעיל, פונקציית ההרצה זורקת שגיאה באופן מכוון. בתוך כל פונקציית הרצה יש בלוק
`try-catch`
שקיים באופן בלתי מפורש
(implicit)
כך שכאשר נזרקת שגיאה היא מועברת למטפל הדחיה. הדוגמה הקודמת מקבילה לקוד הבא:

<div dir="ltr">

```js
let promise = new Promise(function(resolve, reject) {
    try {
        throw new Error("Explosion!");
    } catch (ex) {
        reject(ex);
    }
});

promise.catch(function(error) {
    console.log(error.message);     // "Explosion!"
});
```

</div>

פונקציית ההרצה תופסת כל שגיאה, אך שגיאה מדווחת רק כאשר קיים מטפל דחיה. אחרת השגיאה מושתקת. זה היה לבעיה עבור מפתחים בתחילת השימוש בפרומיס ועל מנת לפתור זאת, סביבות הרצה של ג׳אווהסקריפט מאפשרות לתפוס פרומיס דחוי.

## טיפול גלובלי בפרומיס דחוי

אחד מההיבטים השנויים במחלוקת של פרומיס הינו הכישלון השקט בזמן דחיית פרומיס ללא מטפל דחיה. יש כאלו המאמינים שמדובר במגרעה הגדולה ביותר באפיון השפה כיוון שזהו החלק היחיד בשפת ג׳אווהסקריפט כולה שמשתיק שגיאות.

לא כל כך פשוט לבדוק האם נעשה טיפול בפרומיס דחוי עקב טבעו של הפרומיס. ראו למשל בדוגמה הבאה:

<div dir="ltr">

```js
let rejected = Promise.reject(42);

// אין טיפול בדחיה בנקודת זמן זו

// יותר מאוחר
rejected.catch(function(value) {
    // הדחיה טופלה
    console.log(value);
});
```
</div>

ניתן לקרוא אל
<span dir="ltr">`then()`</span>
או
<span dir="ltr">`catch()`</span>
בכל זמן שנרצה והקוד יעבוד באופן תקין בין אם הפרומיס פתור או לא, כך שקשה לדעת בדיוק מתי פרומיס יטופל. במקרה זה הפרומיס נדחה מיד, אך הדחיה לא מטופלת עד זמן מאוחר יותר.

בעוד שגרסה עתידית של אקמהסקריפט אולי תטפל בבעיה זו, גם דפדפנים וגם
Node.js
פיתחו יכולות שיטפלו בבעיה. יכולות אלו אינן חלק מאפיון השפה אך הן מהוות כלי חשוב בעת שימוש בפרומיס.

### טיפול בדחיות בסביבת Node.js

בסביבת
Node.js
קיימים שני אירועים על אובייקט
`process`
שקשור לטיפול בדחיית פרומיס:

* `unhandledRejection`: האירוע נורה כאשר פרומיס נדחה ואין קריאה למטפל דחיה בתוך תור אחד של לולאת האירועים
* `rejectionHandled`: האירוע נורה כאשר פרומיס נדחה ומטפל דחיה נקרא בתוך תור אחד של לולאת האירועים

האירועים הללו תוכננו לעבוד יחד כדי לעזור בזיהוי אובייקטים מסוג פרומיס שנדחו ולא טופלו.

מטפל עבור אירוע
`unhandledRejection`
מקבל את סיבת הדחיה
(לרוב אובייקט שגיאה)
ואת הפרומיס שנדחה בתור ארגומנטים.
הקוד הבא מראה את
`unhandledRejection`
בפעולה:

<div dir="ltr">

```js
let rejected;

process.on("unhandledRejection", function(reason, promise) {
    console.log(reason.message);            // "Explosion!"
    console.log(rejected === promise);      // true
});

rejected = Promise.reject(new Error("Explosion!"));
```
</div>

הדוגמה לעיל מייצרת פרומיס דחוי עם אוביקט שגיאה בתור ערך הפרומיס ומאזינה לאירוע מסוג
`unhandledRejection`.
מטפל האירועים מקבל את אובייקט השגיאה בתור הארגומנט הראשון ואת הפרומיס בתור הארגומנט השני.

מטפל האירועים עבור

מקבל רק ארגומנט אחד, שהוא הפרומיס שנדחה.
לדוגמה:

<div dir="ltr">

```js
let rejected;

process.on("rejectionHandled", function(promise) {
    console.log(rejected === promise);              // true
});

rejected = Promise.reject(new Error("Explosion!"));

// מעכבים את הוספת מטפל הדחיה
setTimeout(function() {
    rejected.catch(function(value) {
        console.log(value.message);     // "Explosion!"
    });
}, 1000);
```
</div>

בדוגמה זו, האירוע
`rejectionHandled`
נורה כאשר מטפל הדחיה נקרא. אם מטפל הדחיה היה מחובר ישירות לפרומיס
`rejected`
לאחר היווצרותו, אזי האירוע כלל לא היה נורה. במקרה זה, מטפל הדחיה היה נקרא באותו תור של לולאת האירועים שבו הפרומיס
`rejected`
נוצר, דבר שאינו שימושי במיוחד.

כדי לעקוב בצורה נכונה אחר דחיות לא מטופלות, עליך להשתמש באירועים
`rejectionHandled`
ו-
`unhandledRejection`
כדי לשמור רשימה של דחיות שייתכן שלא טופלו. ואז בידקו את הרשימה לאחר תקופת המתנה. לדוגמה:

<div dir="ltr">

```js
let possiblyUnhandledRejections = new Map();

// כאשר דחיה לא מטופלת, מוסיפים אותה למפה
process.on("unhandledRejection", function(reason, promise) {
    possiblyUnhandledRejections.set(promise, reason);
});

process.on("rejectionHandled", function(promise) {
    possiblyUnhandledRejections.delete(promise);
});

setInterval(function() {

    possiblyUnhandledRejections.forEach(function(reason, promise) {
        console.log(reason.message ? reason.message : reason);

        // טיפול בדחיות
        handleRejection(promise, reason);
    });

    possiblyUnhandledRejections.clear();

}, 60000);
```

</div>

כאן אנו רואים מעקב פשוט אחר דחיות לא מטופלות. הדוגמה משתמשת במפה כדי לשמור אובייקטים מסוג פרומיס ואת סיבות הדחיה שלהם. כל פרומיס מהווה מזהה, והסיבה לדחיה היא הערך. כל פעם שנורה האירוע
`unhandledRejection`,
הפרומיס וסיבת הדחיה נוספות למפה. בכל פעם שנורה האירוע
`rejectionHandled`,
הפרומיס נמחק מהמפה. כתוצאה מכך המפה
`possiblyUnhandledRejections`
גדלה או קטנה בעת שהאירועים נורים. הקריאה לפונקציה
<span dir="ltr">`setInterval()`</span>
בודקת באופן מחזורי את רשימת הדחיות שלא ברור אם טופלו ומדפיססה את הנתונים למערכת.
(במציאות, סביר להניח שתרצו לעשות משהו כדי לתעד את הדחיה או לטפל בה).
בדוגמה זו משתמשים במפה רגילה ולא מפה חלשה
<span dir="ltr">`(weak map)`</span>
מפני שיש צורך לבדוק את המפה באופן מחזורי כדי לבדוק אילו אובייקטים קיימים בה וזה לא אפשרי עם מפה חלשה.

בעוד שדוגמה זו הינה ספציפית עבור
Node.js,
דפדפנים מימשו מנגנון דומה ליידע מפתחים על דחיות לא מטופלות.

### טיפול בדחיות בדפדפנים

דפדפנים גם הם יורים שני אירועים כדי לעזור לזהות דחיות שלא טופלו. האירועים הללו נורים על ידי אובייקט
`window`
ופועלים למעשה כמו מקביליהם בסביבת
Node.js:

* `unhandledrejection`: האירוע נורה כאשר פרומיס נדחה ולא נעשית קריאה למטפל דחיה בתוך תור אחד של לולאת האירועים.
* `rejectionhandled`: האירוע נורה כאשר פרומיס נדחה ונעשית קריאה למטפל דחיה לאחר תור אחד של לולאת האירועים.

בעוד שהמימוש של
Node.js
מעביר פרמטרים נפרדים למטפל האירוע, מטפל האירוע עבור אירועי דפדפן אלה מקבל אובייקט אירוע עם התכונות הבאות:

* `type`: שם האירוע
 (`"unhandledrejection"` או `"rejectionhandled"`).
* `promise`: אובייקט הפרומיס שנדחה
* `reason`: ערך הפרומיס עבור הדחיה

ההבדל הנוסף בסביבת הדפדפ הוא שערך הדחייה
(`reason`)
קיים עבור שני האירועים.
לדוגמה:

<div dir="ltr">

```js
let rejected;

window.onunhandledrejection = function(event) {
    console.log(event.type);                    // "unhandledrejection"
    console.log(event.reason.message);          // "Explosion!"
    console.log(rejected === event.promise);    // true
};

window.onrejectionhandled = function(event) {
    console.log(event.type);                    // "rejectionhandled"
    console.log(event.reason.message);          // "Explosion!"
    console.log(rejected === event.promise);    // true
};

rejected = Promise.reject(new Error("Explosion!"));
```

</div>

הקוד לעיל מחבר את שני מטפלי האירועים באמצעות צורת הכתיבה של
רמה 0 של
DOM
<span dir="ltr">(DOM Level 0 notation)</span>
עבור
`onunhandledrejection`
ועבור
`onrejectionhandled`.
(
ניתן גם להשתמש בקוד
<span dir="ltr">`addEventListener("unhandledrejection")`</span>
ו-
<span dir="ltr">`addEventListener("rejectionhandled")`</span>
אם תרצו
)
כל מטפל אירוע מקבל אובייקט אירוע שמכיל נתונים עבור הפרומיס הדחוי.
התכונות
`type`,
`promise`,
ו-
`reason`
קיימות עבור שני סוגי האירועים.

הקוד שעוקב אחר דחיות לא מטופלות בדפדפן דומה מאוד לקוד עבור
Node.js:

<div dir="ltr">

```js
let possiblyUnhandledRejections = new Map();

// כאשר דחיה לא מטופלת, מוסיפים אותה למפה
window.onunhandledrejection = function(event) {
    possiblyUnhandledRejections.set(event.promise, event.reason);
};

window.onrejectionhandled = function(event) {
    possiblyUnhandledRejections.delete(event.promise);
};

setInterval(function() {

    possiblyUnhandledRejections.forEach(function(reason, promise) {
        console.log(reason.message ? reason.message : reason);

        // טיפול בדחיות
        handleRejection(promise, reason);
    });

    possiblyUnhandledRejections.clear();

}, 60000);
```
</div>

המימוש לעיל דומה מאוד למימוש עבור
Node.js.
הוא משתמש באותה גישה של שמירת אובייקטים דחויים מסוג פרומיס ואת ערך הדחיה שלהם בתוך מפה ואז בדיקתם בזמן מאוחר יותר. ההבדל היחידי הוא המיקום שממנו מתקבלים הנתונים בתוך מטפלי האירועים.

טיפול בדחיה עבור פרומיס יכול להיות בעייתי, אך התחלנו לראות כמה כוח טמון בו. כעת נעבור לשלב הבא ונחבר/נשרשר מספר אובייקטים מסוג פרומיס ביחד.

## שרשור פרומיסים

עד לנקודה זו פרומיסים לא נראו יותר מאשר מספר שיפורים הדרגתיים על פני שימוש בשילוב של פונקציות קולבק ביחד עם הפונקציה
<span dir="ltr">`setTimeout()`</span>,
אך פרומיס מציע לנו הרבה מעבר לכך. באופן ספציפי, קיימות מספר דרכים לשרשר פרומיסים ביחד על מנת להגיע לפונקציונליות אסינכרונית מורכבת יותר.

כל קריאה ל
<span dir="ltr">`then()`</span>
או
<span dir="ltr">`catch()`</span>
למעשה יוצרת ומחזירה פרומיס אחר. הפרומיס השני נפתר רק לאחר שהראשון נפתר. חשוב על דוגמה זו:

<div dir="ltr">

```js
let p1 = new Promise(function(resolve, reject) {
    resolve(42);
});

p1.then(function(value) {
    console.log(value);
}).then(function() {
    console.log("Finished");
});
```
</div>

הקוד מדפיס את הפלט:

```
42
Finished
```

הקריאה אל
<span dir="ltr">`p1.then()`</span>
מחזירה פרומיס שני שמפעילים את מתודת
<span dir="ltr">`then()`</span>
שלו.
מטפל ההצלחה של
<span dir="ltr">`then()`</span>
השני נקרא רק לאחר שהפרומיס הראשון נפתר. אם מפרקים לחלקים את הדוגמה, היא תיראה כך:

<div dir="ltr">

```js
let p1 = new Promise(function(resolve, reject) {
    resolve(42);
});

let p2 = p1.then(function(value) {
    console.log(value);
})

p2.then(function() {
    console.log("Finished");
});
```
</div>

בדוגמה לא משורשרת זו של הקוד, התוצאה של הקוד
<span dir="ltr">`p1.then()`</span>
נשמרת במשתנה
`p2`,
ואז מתבצעת קריאה אל
<span dir="ltr">`p2.then()`</span>
כדי להוסיף מטפל הצלחה אחרון. כפי שניחשתם, הקריאה אל
<span dir="ltr">`p2.then()`</span>
גם היא מחזירה פרומיס. הדוגמה לעיל פשוט לא משתמשת באותו פרומיס.

### תפיסת שגיאות

שרשור פרומיסים מאפשר לנו לתפוס שגיאות שנובעות ממטפל הצלחה או דחיה של פרומיס קודם.
לדוגמה:

<div dir="ltr">

```js
let p1 = new Promise(function(resolve, reject) {
    resolve(42);
});

p1.then(function(value) {
    throw new Error("Boom!");
}).catch(function(error) {
    console.log(error.message);     // "Boom!"
});
```
</div>

בקוד לעיל, מטפל ההצלחה עבור הפרומיס
`p1`
זורק שגיאה.
הקריאה המשורשרת למתודה
<span dir="ltr">`catch()`</span>
שמופיעה על הפרומיס השנה, יכולה לקבל את אותה שגיאה באמצעות מטפל הדחיה שלה. הדבר נכון גם כאשר מטפל דחיה זורק שגיאה:

<div dir="ltr">

```js
let p1 = new Promise(function(resolve, reject) {
    throw new Error("Explosion!");
});

p1.catch(function(error) {
    console.log(error.message);     // "Explosion!"
    throw new Error("Boom!");
}).catch(function(error) {
    console.log(error.message);     // "Boom!"
});
```
</div>

בדוגמה לעיל, פונקציית ההרצה זורקת שגיאה ומפעילה את מטפל הדחיה עבור
`p1`.
אותה פונקציה מטפלת זורקת שגיאה נוספת שנתפסת על ידי מטפל הדחיה של הפרומיס האחר. הקריאות לפונקציות הנוספות בשרשרת הפרומיס מודעות לשגיאות שמגיעות מתוך השרשרת.

I> מומלץ
שתמיד יהיה מטפל דחיה בסוף שרשרת הפרומיס כדי לוודא שמטפלים בכל שגיאה אפשרית.

### החזרת ערכים בשרשרת פרומיס

היבט חשוב נוסף של שרשרת פרומיס הינו היכולת להעביר נתונים מפרומיס אחד למשנהו. כבר ראיתם כיצד ערך שמועבר לפונקציה
<span dir="ltr">`then()`</span>
שבתוך פונקציית הרצה
(executor)
מועבר למטפל ההצלחה עבור אותו פרומיס. באפשרותנו להמשיך להעביר נתונים בשרשרת על ידי קביעת ערך חזרה ממטפל ההצלחה.
לדוגמה:

<div dir="ltr">

```js
let p1 = new Promise(function(resolve, reject) {
    resolve(42);
});

p1.then(function(value) {
    console.log(value);         // "42"
    return value + 1;
}).then(function(value) {
    console.log(value);         // "43"
});
```
</div>

מטפל ההצלחה עבור
`p1`
מחזיר
`value + 1`
כאשר ירוץ. מכיוון שערכו של
`value`
הוא 42
(מפונקציית ההרצה),
מטפל ההצלחה מחזיר לנו את הערך 43.
הערך 43 מועבר למטפל ההצלחה של הפרומיס הנוסף, שמדפיס אותו למסך.

ניתן לבצע את אותו הדבר עם מטפל הדחיה. כאשר קוראים למטפל הדחיה, ניתן להחזיר ערך. אם הדבר נעשה אותו ערך משמש להשלמת הפרומיס הבא בשרשרת.
כמו שקורה בדוגמה הבאה:

<div dir="ltr">

```js
let p1 = new Promise(function(resolve, reject) {
    reject(42);
});

p1.catch(function(value) {
    // מטפל הצלחה ראשון
    console.log(value);         // "42"
    return value + 1;
}).then(function(value) {
    // מטפל הצלחה שני
    console.log(value);         // "43"
});
```
</div>

בדוגמה זו, פונקציית ההרצה קוראת ל-
<span dir="ltr">`reject()`</span>
עם הערך 42. זהו הערך שמועבר למטפל הדחיה של הפרומיס, היכן שמוחזר לנו
`value + 1`.
למרות שערך החזרה מגיע מתוך מטפל דחיה, עדיין משתשמשים בו במטפל ההצלחה עבור הפרומיס הבא בשרשרת. כישלון של פרומיס אחד יכול לגרום להתאוששות השרשרת כולה במקרה הצורך.

### החזרת פרומיס בתוך שרשרת פרומיס

החזרת ערכים פרימיטיביים מתוך מטפלי דחיה והצלחה מאפשרת להעביר מידע בין פרומיסים, אבל מה אם נחזיר אובייקט? אם האובייקט הוא מסוג פרומיס, אזי יש שלב נוסף שיש לעבור על מנת לקבוע כיצד להמשיך.
לדוגמה:

<div dir="ltr">

```js
let p1 = new Promise(function(resolve, reject) {
    resolve(42);
});

let p2 = new Promise(function(resolve, reject) {
    resolve(43);
});

p1.then(function(value) {
    // מטפל הצלחה ראשון
    console.log(value);     // 42
    return p2;
}).then(function(value) {
    // מטפל הצלחה שני
    console.log(value);     // 43
});
```
</div>

בקוד לעיל,
`p1`
מתזמן משימה שתחזיר את הערך
42.
מטפל ההצלחה עבור
`p1`
מחזיר את הפרומיס
`p2`,
פרומיס שכבר נמצא במצב הצלחה. מטפל ההצלחה השני נקרא מכיוון
שהפרומיס
`p2`
כבר הושלם בהצלחה. אם
`p2`
היה נדחה, מטפל הדחיה
(במידה וקיים)
היה נקרא במקום מטפל ההצלחה השני.

הדבר החשוב לדעת לגבי התבנית הזו הינו שמטפל ההצלחה השני אינו מתווסף על
`p2`,
אלא על פרומיס שלישי. מטפל ההצלחה השני מחובר לפרומיס שלישי, מה שהופך את הדוגמה הקודמת מקבילה לדוגמה הבאה:

<div dir="ltr">

```js
let p1 = new Promise(function(resolve, reject) {
    resolve(42);
});

let p2 = new Promise(function(resolve, reject) {
    resolve(43);
});

let p3 = p1.then(function(value) {
    // מטפל הצלחה ראשון
    console.log(value);     // 42
    return p2;
});

p3.then(function(value) {
    // מטפל הצלחה שני
    console.log(value);     // 43
});
```
</div>

בדוגמה לעיל ברור שמטפל ההצלחה השני מחובר לפרומיס
`p3`
ולא אל
`p2`.
חשוב להבין את ההבדל כיוון שמטפל ההצלחה השני לא ייקרא אם
`p2`
יידחה.
לדוגמה:

<div dir="ltr">

```js
let p1 = new Promise(function(resolve, reject) {
    resolve(42);
});

let p2 = new Promise(function(resolve, reject) {
    reject(43);
});

p1.then(function(value) {
    // מטפל הצלחה ראשון
    console.log(value);     // 42
    return p2;
}).then(function(value) {
    // מטפל הצלחה שני
    console.log(value);     // לא קורה
});
```
</div>

בדוגמה לעיל, מטפל ההצלחה השני לא נקרא מכיוון שהפרומיס
`p2`
נדחה. ואולם, באפשרותנו להוסיף מטפל דחיה אם נרצה:

<div dir="ltr">

```js
let p1 = new Promise(function(resolve, reject) {
    resolve(42);
});

let p2 = new Promise(function(resolve, reject) {
    reject(43);
});

p1.then(function(value) {
    // מטפל הצלחה ראשון
    console.log(value);     // 42
    return p2;
}).catch(function(value) {
    // מטפל דחיה
    console.log(value);     // 43
});
```
</div>

בדוגמה זו, מטפל הדחיה נקרא כתוצאה מדחיית הפרומיס
`p2`.
הערך עבור הדחיה,
43,
מועבר לתוך מטפל הדחיה.

החזרת אובייקטים ד׳נבילים
(thenables)
ממטפלי הצלחה או דחיה לא משפיעה על פעולת פונקציית ההרצה של הפרומיס
(executors).
הפרומיס הראשון שהוגדר יריץ את פונקציית ההרצה שלו קודם, לאחר מכן פונקציית ההרצה של הפרומיס השני תרוץ, וכך הלאה, לפי הסדר.
החזרת אובייקטים ד׳נבילים מאפשרת לנו להגדיר פעולות ותגובות נוספות לתוצאת הפרומיס.
אנו משהים את פעולת מטפלי ההצלחה על ידי יצירת פרומיס חדש בתוך מטפל הצלחה.
לדוגמה:

<div dir="ltr">

```js
let p1 = new Promise(function(resolve, reject) {
    resolve(42);
});

p1.then(function(value) {
    console.log(value);     // 42

    // יצירת פרומיס שני
    let p2 = new Promise(function(resolve, reject) {
        resolve(43);
    });

    return p2
}).then(function(value) {
    console.log(value);     // 43
});
```
</div>

בדוגמה זו, פרומיס חדש נוצר בתוך מטפל הצלחה עבור
`p1`.
המשמעות היא שמטפל ההצלחה השני לא ירוץ עד אחרי שהפרומיס
`p2`
הושלם. טכניקה זו שימושית לנו אם נרצה לחכות עד שפרומיס קודם נפתר לפני שמפעילים פרומיס אחר.

## התייחסות למספר פרומיסים

עד כה, כל דוגמה בפרק הנוכחי התייחסה לפרומיס אחד בכל רגע נתון. ואולם לפעמים נרצה לבדוק התקדמות מספר פרומיסים בכדי לקבוע את הפעולה הבאה. אקמהסקריפט 6 מספקת לנו שתי מתודות שפועלות על מספר פרומיסים:
<span dir="ltr">`Promise.all()`</span>
והמתודה
<span dir="ltr">`Promise.race()`</span>

### <span dir="ltr">`Promise.all()`</span>

המתודה
<span dir="ltr">`Promise.all()`</span>
מקבלת ארגומנט אחד, אובייקט איטרבילי
(כמו מערך)
של פרומיסים ומחזירה פרומיס שמושלם בהצלחה רק כאשר כל פרומיס בתוך האובייקט האיטרבילי מושלם בהצלחה.
ראו בדוגמה הבא:

<div dir="ltr">

```js
let p1 = new Promise(function(resolve, reject) {
    resolve(42);
});

let p2 = new Promise(function(resolve, reject) {
    resolve(43);
});

let p3 = new Promise(function(resolve, reject) {
    resolve(44);
});

let p4 = Promise.all([p1, p2, p3]);

p4.then(function(value) {
    console.log(Array.isArray(value));  // true
    console.log(value[0]);              // 42
    console.log(value[1]);              // 43
    console.log(value[2]);              // 44
});
```

</div>

כל פרומיס בדוגמה לעיל מושלם עם מספר. הקריאה אל
<span dir="ltr">`Promise.all()`</span>
יוצרת את הפרומיס
`p4`,
שמושלם כאשר הפרומיסים
`p1`, `p2`,
-ו
`p3`
מושלמים בהצלחה. התוצאה,
מערך שמכיל כל ערך הצלחה,
מועברת למטפל ההצלחה עבור הפרומיס
`p4`.
הערכים מופיעים לפי הסדר לפיו הפרומיסים הועברו למתודה
<span dir="ltr">`Promise.all`</span>,
כך שניתן להתאים את תוצאת הפרומיס לפרומיס עצמו.

במידה ופרומיס אחד שהועבר אל
<span dir="ltr">`Promise.all`</span>
נדחה, הפרומיס שמוחזר נדחה גם הוא באופן מיידי מבלי לחכות לתוצאת הפרומיסים האחרים.
לדוגמה:

<div dir="ltr">

```js
let p1 = new Promise(function(resolve, reject) {
    resolve(42);
});

let p2 = new Promise(function(resolve, reject) {
    reject(43);
});

let p3 = new Promise(function(resolve, reject) {
    resolve(44);
});

let p4 = Promise.all([p1, p2, p3]);

p4.catch(function(value) {
    console.log(Array.isArray(value))   // false
    console.log(value);                 // 43
});
```
</div>

בדוגמה זו, הפרומיס
`p2`
נדחה עם הערך
43.
מטפל הדחיה עבור
`p4`
נקרא באופן מיידי, מבלי להמתין לסיום פעולת הפרומיסים
`p1`
או
`p3`
(
הם עדיין פועלים.
`p4`
פשוט לא ממתין עבורם.
).

מטפל הדחיה תמיד מקבל ערך בודד ולא מערך, וערך זה הינו ערך הדחיה מהפרומיס שנדחה. במקרה הנוכחי, מטפל הדחיה מקבל את הערך
`43`
שהתקבל מהדחיה של הפרומיס
`p2`.

### <span dir="ltr">`Promise.race()`</span>

המתודה
<span dir="ltr">`Promise.race()`</span>
פועלת בצורה שונה על מספר פרומיסים. המתודה מקבלת גם כן אובייקט איטרבילי של פרומיסים ומחזיר פרומיס, אך הפרומיס שמוחזר נפתר ברגע שאחד מהפרומיסים המנוטרים נפתר בהצלחה.
במקום לחכות עבור כל הפרומיסים שיושלמו בהצלחה כמו שעושה המתודה
<span dir="ltr">`Promise.all()`</span>
המתודה
<span dir="ltr">`Promise.race()`</span>
מחזירה את הפרומיס הפתור ברגע שפרומיס אחד מתוך המערך מושלם בהצלחה. לדוגמה:

<div dir="ltr">

```js
let p1 = Promise.resolve(42);

let p2 = new Promise(function(resolve, reject) {
    resolve(43);
});

let p3 = new Promise(function(resolve, reject) {
    resolve(44);
});

let p4 = Promise.race([p1, p2, p3]);

p4.then(function(value) {
    console.log(value);     // 42
});
```
</div>

בקוד שבדוגמה, המשתנה

נוצר כפרומיס פתור בהצלחה, והפרומיסים האחרים מתזמנים משימות.
מטפל ההצלחה עבור
`p4`
נקרא עם הערך 42 ומתעלם משאר הפרומיסים. הפרומיסים שמועברים למתודה
<span dir="ltr">`Promise.race()`</span>
נמצאים במירוץ שבו ינצח הפרומיס שיצליח קודם. אם הפרומיס הראשון שנפתר מצליח אזי הפרומיס שמוחזר גם הוא יצליח. אם הפרומיס הראשון שנפתר נדחה, אזי הפרומיס שמוחזר מהמודה נדחה גם הוא.
הנה דוגמה של דחיה:

<div dir="ltr">

```js
let p1 = new Promise(function(resolve, reject) {
    setTimeout(function() {
        resolve(42);
    }, 100);
});

let p2 = new Promise(function(resolve, reject) {
    reject(43);
});

let p3 = new Promise(function(resolve, reject) {
    setTimeout(function() {
        resolve(44);
    }, 50);
});

let p4 = Promise.race([p1, p2, p3]);

p4.catch(function(value) {
    console.log(value);     // 43
});
```

</div>

בדוגמה לעיל,
`p1`
וגם
`p3`
משתמשים ב
<span dir="ltr">`setTimeout()`</span>
(
שקיים בסביבת
Node.js
וגם בדפדפנים
)
כדי לדחות את השלמת הפרומיס. התוצאה היא ש
`p4`
נדחה מכיוון ש
`p2`
נדחה לפני שאחד מהפרומיסים
`p1`
או
`p3`
מצליח. למרות ש
`p1`
ו-
`p3`
מושלמים בהצלחה לבסוף, מתעלמים מהתוצאה מפני שהיא מתרחשת לאחר שהפרומיס
`p2`
נדחה.

## ירושה בפרומיס

בדיוק כמו באובייקטים מובנים אחרים, ניתן להשתמש בפרומיס בתור בסיס למחלקה נגזרת. זה מאפשר לנו להגדיר וריאציות של פרומיס שתרחיב את מה שהפרומיס המובנה עושה. אם למשל נרצה פרומיס בעל מתודות בשם
<span dir="ltr">`success()`</span>
ו-
<span dir="ltr">`failure()`</span>
בנוסף למתודות הקיימות
<span dir="ltr">`then()`</span>
ו-
<span dir="ltr">`catch()`</span>.
תוכל ליצור פרומיס מסוג זה בדרך הבאה:

<div dir="ltr">

```js
class MyPromise extends Promise {

    // נעשה שימוש בקונסטרקטור הרגיל

    success(resolve, reject) {
        return this.then(resolve, reject);
    }

    failure(reject) {
        return this.catch(reject);
    }

}

let promise = new MyPromise(function(resolve, reject) {
    resolve(42);
});

promise.success(function(value) {
    console.log(value);             // 42
}).failure(function(value) {
    console.log(value);
});
```
</div>

בדוגמה זו,
`MyPromise`
יורש מ
`Promise`
ויש לו שתי מתודות נוספות. המתודה
<span dir="ltr">`success()`</span>
פועלת בדיוק כמו
<span dir="ltr">`failure()`</span>
והמתודה
<span dir="ltr">`then()`</span>
פועלת בדיוק כמו המתודה
<span dir="ltr">`catch()`</span>.

כל מתודה חדשה משתמשת ב
`this`
כדי לקרוא למתודה אותה היא מחקה. המחלקה היורשת מתפקדת בדיוק כמו הפרומיס המובנה, מלבד העובדה שנוכל לקרוא למתודות
<span dir="ltr">`success()`</span>
ו-
<span dir="ltr">`failure()`</span>
אם נרצה.

מאחר ומתודות סטטיות עוברות בירושה, המתודות
<span dir="ltr">`MyPromise.resolve()`</span>,
<span dir="ltr">`MyPromise.reject()`</span>,
<span dir="ltr">`MyPromise.race()`</span>,
<span dir="ltr">`MyPromise.all()`</span>,
כולן קיימות גם הן על הפרומיס של המחלקה הנגזרת. שתי המתודות האחרונות מתנהגות בדיוק כמו המתודות המובנות, אך השתיים שלפניהן שונות במקצת.

המתודות
<span dir="ltr">`MyPromise.resolve()`</span>,
ו-
<span dir="ltr">`MyPromise.reject()`</span>
יחזירו מופע של
`MyPromise`
ללא קשר לערך שמועבר אליהן מפני שהן משתמשות בתכונה
`Symbol.species`
(שהוסברה בפרק 9)
כדי לקבוע את סוג הפרומיס שמוחזר. אם פרומיס מובנה מועבר כארגומנט לתוך אחת מהן, הפרומיס יצליח או יידחה, והמתודה תחזיר מופע של
`MyPromise`
כך שניתן להוסיף לו מטפלי הצלחה ודחיה. למשל:

<div dir="ltr">

```js
let p1 = new Promise(function(resolve, reject) {
    resolve(42);
});

let p2 = MyPromise.resolve(p1);
p2.success(function(value) {
    console.log(value);         // 42
});

console.log(p2 instanceof MyPromise);   // true
```
</div>

בדוגמה לעיל,
`p1`
הינו פרומיס מובנה שמועבר לתוך המתודה
<span dir="ltr">`MyPromise.resolve()`</span>.
התוצאה,
`p2`,
הינה מופע של
`MyPromise`
שאליה הועבר הערך מתוך
`p1`
ישירות אל מטפל ההצלחה.

אם מופע של
`MyPromise`
יועבר לתוך המתודות
<span dir="ltr">`MyPromise.resolve()`</span>
או
<span dir="ltr">`MyPromise.reject()`</span>
הוא יוחזר כמות שהוא מבלי שייפתר. בכל היבט אחר שתי המתודות הללו מתנהגות בדיוק כמו המתודות
<span dir="ltr">`Promise.resolve()`</span>
ו
<span dir="ltr">`Promise.reject()`</span>

### הרצת  משימות אסינכרוניות

בפרק 8, הצגתי לכם גנרטורים וכיצד להשתמש בהם עבור הרצת משימות אסינכרוניות, כמו בדוגמה הבאה:

<div dir="ltr">

```js
let fs = require("fs");

function run(taskDef) {

    // יצירת האיטרטור ושמירתו
    let task = taskDef();

    // התחלת ביצוע המשימות
    let result = task.next();

    // קריאות ריקורסיביות לערך הבא באיטרטור
    function step() {

        // בדיקה אם נותרו עוד פעולות
        if (!result.done) {
            if (typeof result.value === "function") {
                result.value(function(err, data) {
                    if (err) {
                        result = task.throw(err);
                        return;
                    }

                    result = task.next(data);
                    step();
                });
            } else {
                result = task.next(result.value);
                step();
            }

        }
    }

    // התחלת התהליך
    step();

}

// הגדרת פונקציה לשימוש עם מריץ המשימות

function readFile(filename) {
    return function(callback) {
        fs.readFile(filename, callback);
    };
}

// הרצת משימה

run(function*() {
    let contents = yield readFile("config.json");
    doSomethingWith(contents);
    console.log("Done");
});
```

</div>

יש מספר בעיות במימוש הנ״ל. ראשית, עטיפת כל פונקציה בפונקציה שמחזירה פונקציה יכולה לבלבל את הקורא
(אפילו המשפט הנ״ל לא ברור).
שנית, אין דרך להבחין בין ערך חזרה שמיועד לשימוש כפונקציית קולבק עבור מריץ המשימות לבין ערך חזרה שאיננו כזה.

על ידי שימוש בפרומיס ניתן לפשט את התהליך ולוודא שכל פעולה אסינכרונית תחזיר פרומיס. המימוש הנפוץ הזה מאפשר לפשט באופן משמעותי כתיבת קוד אסינכרוני. הנה דוגמה אחת לכך:

<div dir="ltr">

```js
let fs = require("fs");

function run(taskDef) {

    // יצירת האיטרטור
    let task = taskDef();

    // התחלת ביצוע המשימות
    let result = task.next();

    // קריאות ריקורסיביות לערך הבא באיטרטור
    (function step() {

        // בדיקה אם נותרו עוד פעולות
        if (!result.done) {

            // המרה לפרומיס פתור בהצלחה
            let promise = Promise.resolve(result.value);
            promise.then(function(value) {
                result = task.next(value);
                step();
            }).catch(function(error) {
                result = task.throw(error);
                step();
            });
        }
    }());
}

// מגדירים פונקציה לשימוש עם מריץ המשימות

function readFile(filename) {
    return new Promise(function(resolve, reject) {
        fs.readFile(filename, function(err, contents) {
            if (err) {
                reject(err);
            } else {
                resolve(contents);
            }
        });
    });
}

// הרצת משימה

run(function*() {
    let contents = yield readFile("config.json");
    doSomethingWith(contents);
    console.log("Done");
});
```
</div>

בגרסה זו של הקוד, הפונקציה
<span dir="ltr">`run()`</span>
מריצה גנרטור כדי לייצר איטרטור. היא מפעילה את הקוד
<span dir="ltr">`task.next()`</span>
כדי להפעיל את המשימה וקוראת באופן רקורסיבי לפונקציה
<span dir="ltr">`step()`</span>
עד שהאיטרטור סיים.

בתוך הפונקציה
<span dir="ltr">`run()`</span>
אם נותרו משימות להריץ הערך עבור
<span dir="ltr">`result.done`</span>
יהיה
`false`.
בנקודה זו, הערך עבור
<span dir="ltr">`result.value`</span>
צריך להיות פרומיס, אך
<span dir="ltr">`Promise.resolve()`</span>
נקראת רק למקרה שהפונקציה לא החזירה פרומיס.
(
    זכרו,
    <span dir="ltr">`Promise.resolve()`</span>
    אינה משנה ערך מסוג פרומיס שמועבר אליה
    ועוטפת כל דבר אחר בפרומיס.
).
אחר כך, מחברים מטפל הצלחה שמעביר את ערך הפרומיס חזרה לאיטרטור.
לאחר מכן, הערך הבא שמוחזר מהאיטרטור מועבר למשתנה
`result`
ממש לפני שהפונקציה
<span dir="ltr">`step()`</span>
קוראת שוב לעצמה.

מטפל דחיה שומר את תוצאות הדחיה, במידה וקרתה, בתוך אובייקט.
המתודה
<span dir="ltr">`task.throw()`</span>
מעבירה את אובייקט השגיאה בחזרה לתוך האיטרטור, ואם נתפסת שגיאה בתוך המשימה, אזי תוצאת האיטרטור הבאה מועברת לערך
`result`.
לבסוף הפונקציה
<span dir="ltr">`step()`</span>
נקראת בתוך
<span dir="ltr">`catch()`</span>
כדי להמשיך.

הפונקציה
<span dir="ltr">`run()`</span>
יכולה להריץ כל גנרטור שמשתמש ב
`yield`
כדי לההריץ קוד אסינכרוני מבלי הצורך לחשוף פרומיס
(או פונקציית קולבק)
למפתחים. למעשה, מכיוון שערך החזרה של הפונקציה תמיד מומר לפרומיס, הפונקציה יכולה להחזיר משהו אחר מפרומיס. המשמעות היא שקוד סינכרוני ואסינכרוני יעבדו כאשר קוראים לקוד בעזרת
`yield`
ולעולם לא נצטרך לבדוק שהערך המוחזר הינו פרומיס.

הדבר היחיד שנותר לוודא הוא שפונקציות אסינכרוניות כמו
<span dir="ltr">`readFile()`</span>
יחזירו פרומיס שמשקף בצורה נכונה את פעולתה. עבור פונקציות מובנות בסביבת
<span dir="ltr">`Node.js`</span>
המשמעות היא שנצטרך להמיר את הפונקציות כך שיחזירו פרומיס במקום להשתמש בפונקציות קולבק.

<hr>

### הרצת משימות אסינכרוניות בעתיד

בזמן כתיבת שורות אילו נעשית עבודה מתמשכת ליצירת תחביר פשוט יותר להרצת קוד אסינכרוני בג׳אווהסקריפט. נעשית עבודה על תחביר שמשתמש במילה השמורה
`await`
שיחקה את הדוגמה הקודמת על בסיס פרומיס. הרעיון הוא להשתמש בפונקציה שמסומנת בתור
`async`
במקום להשתמש בגנרטור ולהשתמש במילה
`await`
במקום
`yield`
בעת הקריאה לפונקציה.
לדוגמה:

<div dir="ltr">

```js
(async function() {
    let contents = await readFile("config.json");
    doSomethingWith(contents);
    console.log("Done");
})();
```

</div>

המילה השמורה
`async`
שמופיעה לפני
`function`
מהווה אינדיקציה לכך שהפונקציה מיועדת לרוץ אופן אסינכרוני.
המילה
`async`
מאותתת שהקריאה לפונקציה
<span dir="ltr">`readFile("config.json")`</span>
אמורה להחזיר פרומיס, ובמידה ולא, אזי התוצאה תיעטף בפרומיס. בדיוק כמו במימוש של הפונקציה
<span dir="ltr">`run()`</span>
בסעיף הקודם,
`await`
יזרוק שגיאה אם הפרומיס נדחה, אחרת יחזיר את הערך שהגיע מהפרומיס. התוצאה הסופית היא שכעת תוכלו לכתוב קוד אסינכרוני כאילו היה קוד סינכרוני מבלי הצורך לנהל מכונת סטייט פנימי מבוססת איטרטורים.

התחביר עבור
`await`
צפוי להסתיים בגרסת אקמהסקריפט 2017
(ECMAScript 8).
<hr>

## סיכום

פרומיס נועד לשפר תכנות אסינכרוני בג׳אווהסקריפט על ידי הרחבת השליטה והיכולת לבצע קומפוזיציה של פעולות אסינכרוניות יחסית לתכנות מבוסס אירועים וקולבקים. פרומיס מתזמן משימות שיתווספו לתור המשימות של מנוע הריצה בג׳אווהסקריפט לביצוע בזמן עתידי, בעוד שתור משימות משני עוקב אחר מטפלי הצלחה ודחיה של פרומיס כדי להבטיח ריצת קוד נכונה.

לפרומיס יש שלושה מצבים, המתנה, הצלחה ודחיה. פרומיס מתחיל במצב המתנה ומושלם בהצלחה כאשר הקוד מסיים לרוץ באופן הרצוי או נדחה בעת כישלון. בכל אחד מהמקרים ניתן להוסיף מטפלים לפרומיס כשתגיע למצב סופי פתור. המתודה
<span dir="ltr">`then()`</span>
מאפשרת לנו להגדיר מטפל הצלחה ומטפל דחיה והמתודה
<span dir="ltr">`catch()`</span>
מאפשר לנו להגדיר מטפל דחיה.

ניתן לחבר פרומיסים ביחד במגוון דרכים ולהעביר אינפורמציה ביניהם. כל קריאה למתודה
<span dir="ltr">`then()`</span>
יוצרת ומחזירה פרומיס חדש שייפתר כאשר הפרומיס הקודם לו ייפתר. שרשראות כאלו יכולות לשמש אותנו כדי להגיב לסדרה של אירועים אסינכרוניים. ניתן להשתמש במתודות
<span dir="ltr">`Promise.race()`</span>
ו-
<span dir="ltr">`Promise.all()`</span>
בכדי לעקוב אחר התקדמות של מספר פרומיסים ולהגיב בהתאם.

הרצת משימות אסינכרוניות קלה יותר כאשר משלבים גנרטורים ופרומיס, הפרומיס נותן לנו ממשק פעולה קבוע עבור פעולות אסינכרוניות. ניתן להשתמש לאחר מכן בגנרטורים ובאופרטור
`yield`
כדי לחכות לתוצאה באופן אסינכרוני ולהגיב בהתאם.

הרבה ממשקי תכנות אפליקציות חדשים
(APIs)
נבנים על בסיס פרומיס, וניתן לצפות שבעתיד יצטרפו אליהם עוד הרבה.
</div>
