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

מנועי ריצה של ג׳אווהסקריפט יכולים להריץ רק יחידת קוד אחת בכל זמן נתון, ולכן עליהם לעקוב אחר הקוד אותו יש להריץ. הקוד הזה נשמחר בתוך 
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

</div>

### Browser Rejection Handling

Browsers also emit two events to help identify unhandled rejections. These events are emitted by the `window` object and are effectively the same as their Node.js equivalents:

* `unhandledrejection`: Emitted when a promise is rejected and no rejection handler is called within one turn of the event loop.
* `rejectionhandled`: Emitted when a promise is rejected and a rejection handler is called after one turn of the event loop.

While the Node.js implementation passes individual parameters to the event handler, the event handler for these browser events receives an event object with the following properties:

* `type`: The name of the event (`"unhandledrejection"` or `"rejectionhandled"`).
* `promise`: The promise object that was rejected.
* `reason`: The rejection value from the promise.

The other difference in the browser implementation is that the rejection value (`reason`) is available for both events. For example:

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

This code assigns both event handlers using the DOM Level 0 notation of `onunhandledrejection` and `onrejectionhandled`. (You can also use `addEventListener("unhandledrejection")` and `addEventListener("rejectionhandled")` if you prefer.) Each event handler receives an event object containing information about the rejected promise. The `type`, `promise`, and `reason` properties are all available in both event handlers.

The code to keep track of unhandled rejections in the browser is very similar to the code for Node.js, too:

```js
let possiblyUnhandledRejections = new Map();

// when a rejection is unhandled, add it to the map
window.onunhandledrejection = function(event) {
    possiblyUnhandledRejections.set(event.promise, event.reason);
};

window.onrejectionhandled = function(event) {
    possiblyUnhandledRejections.delete(event.promise);
};

setInterval(function() {

    possiblyUnhandledRejections.forEach(function(reason, promise) {
        console.log(reason.message ? reason.message : reason);

        // do something to handle these rejections
        handleRejection(promise, reason);
    });

    possiblyUnhandledRejections.clear();

}, 60000);
```

This implementation is almost exactly the same as the Node.js implementation. It uses the same approach of storing promises and their rejection values in a map and then inspecting them later. The only real difference is where the information is retrieved from in the event handlers.

Handling promise rejections can be tricky, but you've just begun to see how powerful promises can really be. It's time to take the next step and chain several promises together.

## Chaining Promises

To this point, promises may seem like little more than an incremental improvement over using some combination of a callback and the `setTimeout()` function, but there is much more to promises than meets the eye. More specifically, there are a number of ways to chain promises together to accomplish more complex asynchronous behavior.

Each call to `then()` or `catch()` actually creates and returns another promise. This second promise is resolved only once the first has been fulfilled or rejected. Consider this example:

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

The code outputs:

```
42
Finished
```

The call to `p1.then()` returns a second promise on which `then()` is called. The second `then()` fulfillment handler is only called after the first promise has been resolved. If you unchain this example, it looks like this:

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

In this unchained version of the code, the result of `p1.then()` is stored in `p2`, and then `p2.then()` is called to add the final fulfillment handler. As you might have guessed, the call to `p2.then()` also returns a promise. This example just doesn't use that promise.

### Catching Errors

Promise chaining allows you to catch errors that may occur in a fulfillment or rejection handler from a previous promise. For example:

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

In this code, the fulfillment handler for `p1` throws an error. The chained call to the `catch()` method, which is on a second promise, is able to receive that error through its rejection handler. The same is true if a rejection handler throws an error:

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

Here, the executor throws an error then triggers the `p1` promise's rejection handler. That handler then throws another error that is caught by the second promise's rejection handler. The chained promise calls are aware of errors in other promises in the chain.

I> Always have a rejection handler at the end of a promise chain to ensure that you can properly handle any errors that may occur.

### Returning Values in Promise Chains

Another important aspect of promise chains is the ability to pass data from one promise to the next. You've already seen that a value passed to the `resolve()` handler inside an executor is passed to the fulfillment handler for that promise. You can continue passing data along a chain by specifying a return value from the fulfillment handler. For example:

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

The fulfillment handler for `p1` returns `value + 1` when executed. Since `value` is 42 (from the executor), the fulfillment handler returns 43. That value is then passed to the fulfillment handler of the second promise, which outputs it to the console.

You could do the same thing with the rejection handler. When a rejection handler is called, it may return a value. If it does, that value is used to fulfill the next promise in the chain, like this:

```js
let p1 = new Promise(function(resolve, reject) {
    reject(42);
});

p1.catch(function(value) {
    // first fulfillment handler
    console.log(value);         // "42"
    return value + 1;
}).then(function(value) {
    // second fulfillment handler
    console.log(value);         // "43"
});
```

Here, the executor calls `reject()` with 42. That value is passed into the rejection handler for the promise, where `value + 1` is returned. Even though this return value is coming from a rejection handler, it is still used in the fulfillment handler of the next promise in the chain. The failure of one promise can allow recovery of the entire chain if necessary.

### Returning Promises in Promise Chains

Returning primitive values from fulfillment and rejection handlers allows passing of data between promises, but what if you return an object? If the object is a promise, then there's an extra step that's taken to determine how to proceed. Consider the following example:

```js
let p1 = new Promise(function(resolve, reject) {
    resolve(42);
});

let p2 = new Promise(function(resolve, reject) {
    resolve(43);
});

p1.then(function(value) {
    // first fulfillment handler
    console.log(value);     // 42
    return p2;
}).then(function(value) {
    // second fulfillment handler
    console.log(value);     // 43
});
```

In this code, `p1` schedules a job that resolves to 42. The fulfillment handler for `p1` returns `p2`, a promise already in the resolved state. The second fulfillment handler is called because `p2` has been fulfilled. If `p2` were rejected, a rejection handler (if present) would be called instead of the second fulfillment handler.

The important thing to recognize about this pattern is that the second fulfillment handler is not added to `p2`, but rather to a third promise. The second fulfillment handler is therefore attached to that third promise, making the previous example equivalent to this:

```js
let p1 = new Promise(function(resolve, reject) {
    resolve(42);
});

let p2 = new Promise(function(resolve, reject) {
    resolve(43);
});

let p3 = p1.then(function(value) {
    // first fulfillment handler
    console.log(value);     // 42
    return p2;
});

p3.then(function(value) {
    // second fulfillment handler
    console.log(value);     // 43
});
```

Here, it's clear that the second fulfillment handler is attached to `p3` rather than `p2`. This is a subtle but important distinction, as the second fulfillment handler will not be called if `p2` is rejected. For instance:

```js
let p1 = new Promise(function(resolve, reject) {
    resolve(42);
});

let p2 = new Promise(function(resolve, reject) {
    reject(43);
});

p1.then(function(value) {
    // first fulfillment handler
    console.log(value);     // 42
    return p2;
}).then(function(value) {
    // second fulfillment handler
    console.log(value);     // never called
});
```

In this example, the second fulfillment handler is never called because `p2` is rejected. You could, however, attach a rejection handler instead:

```js
let p1 = new Promise(function(resolve, reject) {
    resolve(42);
});

let p2 = new Promise(function(resolve, reject) {
    reject(43);
});

p1.then(function(value) {
    // first fulfillment handler
    console.log(value);     // 42
    return p2;
}).catch(function(value) {
    // rejection handler
    console.log(value);     // 43
});
```

Here, the rejection handler is called as a result of `p2` being rejected. The rejected value 43 from `p2` is passed into that rejection handler.

Returning thenables from fulfillment or rejection handlers doesn't change when the promise executors are executed. The first defined promise will run its executor first, then the second promise executor will run, and so on. Returning thenables simply allows you to define additional responses to the promise results. You defer the execution of fulfillment handlers by creating a new promise within a fulfillment handler. For example:

```js
let p1 = new Promise(function(resolve, reject) {
    resolve(42);
});

p1.then(function(value) {
    console.log(value);     // 42

    // create a new promise
    let p2 = new Promise(function(resolve, reject) {
        resolve(43);
    });

    return p2
}).then(function(value) {
    console.log(value);     // 43
});
```

In this example, a new promise is created within the fulfillment handler for `p1`. That means the second fulfillment handler won't execute until after `p2` is fulfilled. This pattern is useful when you want to wait until a previous promise has been settled before triggering another promise.

## Responding to Multiple Promises

Up to this point, each example in this chapter has dealt with responding to one promise at a time. Sometimes, however, you'll want to monitor the progress of multiple promises in order to determine the next action. ECMAScript 6 provides two methods that monitor multiple promises: `Promise.all()` and `Promise.race()`.

### The Promise.all() Method

The `Promise.all()` method accepts a single argument, which is an iterable (such as an array) of promises to monitor, and returns a promise that is resolved only when every promise in the iterable is resolved. The returned promise is fulfilled when every promise in the iterable is fulfilled, as in this example:

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

Each promise here resolves with a number. The call to `Promise.all()` creates promise `p4`, which is ultimately fulfilled when promises `p1`, `p2`, and `p3` are fulfilled. The result passed to the fulfillment handler for `p4` is an array containing each resolved value: 42, 43, and 44. The values are stored in the order the promises were passed to `Promise.all`, so you can match promise results to the promises that resolved to them.

If any promise passed to `Promise.all()` is rejected, the returned promise is immediately rejected without waiting for the other promises to complete:

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

In this example, `p2` is rejected with a value of 43. The rejection handler for `p4` is called immediately without waiting for `p1` or `p3` to finish executing (They do still finish executing; `p4` just doesn't wait.)

The rejection handler always receives a single value rather than an array, and the value is the rejection value from the promise that was rejected. In this case, the rejection handler is passed 43 to reflect the rejection from `p2`.

### The Promise.race() Method

The `Promise.race()` method provides a slightly different take on monitoring multiple promises. This method also accepts an iterable of promises to monitor and returns a promise, but the returned promise is settled as soon as the first promise is settled. Instead of waiting for all promises to be fulfilled like the `Promise.all()` method, the `Promise.race()` method returns an appropriate promise as soon as any promise in the array is fulfilled. For example:

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

In this code, `p1` is created as a fulfilled promise while the others schedule jobs. The fulfillment handler for `p4` is then called with the value of 42 and ignores the other promises. The promises passed to `Promise.race()` are truly in a race to see which is settled first. If the first promise to settle is fulfilled, then the returned promise is fulfilled; if the first promise to settle is rejected, then the returned promise is rejected. Here's an example with a rejection:

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

Here, both `p1` and `p3` use `setTimeout()` (available in both Node.js and web browsers) to delay promise fulfillment. The result is that `p4` is rejected because `p2` is rejected before either `p1` or `p3` is resolved. Even though `p1` and `p3` are eventually fulfilled, those results are ignored because they occur after `p2` is rejected.

## Inheriting from Promises

Just like other built-in types, you can use a promise as the base for a derived class. This allows you to define your own variation of promises to extend what built-in promises can do. Suppose, for instance, you'd like to create a promise that can use methods named `success()` and `failure()` in addition to the usual `then()` and `catch()` methods. You could create that promise type as follows:

```js
class MyPromise extends Promise {

    // use default constructor

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

In this example, `MyPromise` is derived from `Promise` and has two additional methods. The `success()` method mimics `then()` and `failure()` mimics the `catch()` method.

Each added method uses `this` to call the method it mimics. The derived promise functions the same as a built-in promise, except now you can call `success()` and `failure()` if you want.

Since static methods are inherited, the `MyPromise.resolve()` method, the `MyPromise.reject()` method, the `MyPromise.race()` method, and the `MyPromise.all()` method are also present on derived promises. The last two methods behave the same as the built-in methods, but the first two are slightly different.

Both `MyPromise.resolve()` and `MyPromise.reject()` will return an instance of `MyPromise` regardless of the value passed because those methods use the `Symbol.species` property (covered under in Chapter 9) to determine the type of promise to return. If a built-in promise is passed to either method, the promise will be resolved or rejected, and the method will return a new `MyPromise` so you can assign fulfillment and rejection handlers. For example:

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

Here, `p1` is a built-in promise that is passed to the `MyPromise.resolve()` method. The result, `p2`, is an instance of `MyPromise` where the resolved value from `p1` is passed into the fulfillment handler.

If an instance of `MyPromise` is passed to the `MyPromise.resolve()` or `MyPromise.reject()` methods, it will just be returned directly without being resolved. In all other ways these two methods behave the same as `Promise.resolve()` and `Promise.reject()`.

### Asynchronous Task Running

In Chapter 8, I introduced generators and showed you how you can use them for asynchronous task running, like this:

```js
let fs = require("fs");

function run(taskDef) {

    // create the iterator, make available elsewhere
    let task = taskDef();

    // start the task
    let result = task.next();

    // recursive function to keep calling next()
    function step() {

        // if there's more to do
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

    // start the process
    step();

}

// Define a function to use with the task runner

function readFile(filename) {
    return function(callback) {
        fs.readFile(filename, callback);
    };
}

// Run a task

run(function*() {
    let contents = yield readFile("config.json");
    doSomethingWith(contents);
    console.log("Done");
});
```

There are some pain points to this implementation. First, wrapping every function in a function that returns a function is a bit confusing (even this sentence was confusing). Second, there is no way to distinguish between a function return value intended as a callback for the task runner and a return value that isn't a callback.

With promises, you can greatly simplify and generalize this process by ensuring that each asynchronous operation returns a promise. That common interface means you can greatly simplify asynchronous code. Here's one way you could simplify that task runner:

```js
let fs = require("fs");

function run(taskDef) {

    // create the iterator
    let task = taskDef();

    // start the task
    let result = task.next();

    // recursive function to iterate through
    (function step() {

        // if there's more to do
        if (!result.done) {

            // resolve to a promise to make it easy
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

// Define a function to use with the task runner

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

// Run a task

run(function*() {
    let contents = yield readFile("config.json");
    doSomethingWith(contents);
    console.log("Done");
});
```

In this version of the code, a generic `run()` function executes a generator to create an iterator. It calls `task.next()` to start the task and recursively calls `step()` until the iterator is complete.

Inside the `step()` function, if there's more work to do, then `result.done` is `false`. At that point, `result.value` should be a promise, but `Promise.resolve()` is called just in case the function in question didn't return a promise. (Remember, `Promise.resolve()` just passes through any promise passed in and wraps any non-promise in a promise.) Then, a fulfillment handler is added that retrieves the promise value and passes the value back to the iterator. After that, `result` is assigned to the next yield result before the `step()` function calls itself.

A rejection handler stores any rejection results in an error object. The `task.throw()` method passes that error object back into the iterator, and if an error is caught in the task, `result` is assigned to the next yield result. Finally, `step()` is called inside `catch()` to continue.

This `run()` function can run any generator that uses `yield` to achieve asynchronous code without exposing promises (or callbacks) to the developer. In fact, since the return value of the function call is always coverted into a promise, the function can even return something other than a promise. That means both synchronous and asynchronous methods work correctly when called using `yield`, and you never have to check that the return value is a promise.

The only concern is ensuring that asynchronous functions like `readFile()` return a promise that correctly identifies its state. For Node.js built-in methods, that means you'll have to convert those methods to return promises instead of using callbacks.

A> ### Future Asynchronous Task Running
A>
A> At the time of my writing, there is ongoing work around bringing a simpler syntax to asynchronous task running in JavaScript. Work is progressing on an `await` syntax that would closely mirror the promise-based example in the preceding section. The basic idea is to use a function marked with `async` instead of a generator and use `await` instead of `yield` when calling a function, such as:
A>
A> ```js
A> (async function() {
A>     let contents = await readFile("config.json");
A>     doSomethingWith(contents);
A>     console.log("Done");
A> })();
A> ```
A>
A> The `async` keyword before `function` indicates that the function is meant to run in an asynchronous manner. The `await` keyword signals that the function call to `readFile("config.json")` should return a promise, and if it doesn't, the response should be wrapped in a promise. Just as with the implementation of `run()` in the preceding section, `await` will throw an error if the promise is rejected and otherwise return the value from the promise. The end result is that you get to write asynchronous code as if it were synchronous without the overhead of managing an iterator-based state machine.
A>
A> The `await` syntax is expected to be finalized in ECMAScript 2017 (ECMAScript 8).


## Summary

Promises are designed to improve asynchronous programming in JavaScript by giving you more control and composability over asynchronous operations than events and callbacks can. Promises schedule jobs to be added to the JavaScript engine's job queue for execution later, while a second job queue tracks promise fulfillment and rejection handlers to ensure proper execution.

Promises have three states: pending, fulfilled, and rejected. A promise starts in a pending state and becomes fulfilled on a successful execution or rejected on a failure. In either case, handlers can be added to indicate when a promise is settled. The `then()` method allows you to assign a fulfillment and rejection handler and the `catch()` method allows you to assign only a rejection handler.

You can chain promises together in a variety of ways and pass information between them. Each call to `then()` creates and returns a new promise that is resolved when the previous one is resolved. Such chains can be used to trigger responses to a series of asynchronous events. You can also use `Promise.race()` and `Promise.all()` to monitor the progress of multiple promises and respond accordingly.

Asynchronous task running is easier when you combine generators and promises, as promises give a common interface that asynchronous operations can return. You can then use generators and the `yield` operator to wait for asynchronous responses and respond appropriately.

Most new web APIs are being built on top of promises, and you can expect many more to follow suit in the future.
