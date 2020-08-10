<div dir="rtl">

# הקדמה

תכונות השפה הקרויה JavaScript מוגדרות בתקן הקרוי ECMA-262. 
שפת התכנות המוגדרת בתקן זה נקראת ECMAScript. <BR/>
מה שמוכר לך כ-JavaScript בדפדפנים וב-Node.js הוא למעשה הרחבות של ECMAScript. 
דפדפנים וסביבת Node.js מוסיפים פונקציונאליות בעזרת אובייקטים נוספים ומתודות, אבל עדיין הבסיס של השפה נשאר <BR/>
ECMAScript. 
ההתפתחות הפרואקטיבית של סטנדרט ECMA-262 חיוני להצלחתה של ה-JavaScript כמכלול. <BR/>
ספר זה סוקר את השינויים שנעשו בשפה בעדכון החשוב המרכזי: ECMAScript-6. 


## הדרך ל- ECMAScript 6

בשנת 2007, JavaScript היתה בצומת דרכים.
הפופולאריות של Ajax גברה בעידן של אפליקציות ווב דינאמיות, בעוד ש-JavaScript לא השתנה מאז הגרסה השלישית של התקן ECMA-262 שפורסמה בשנת 1999.
TC-39 - הוועדה האחראית לקידום התפתחות ECMAScript הכינה טיוטה גדולה ל-ECMAScript 4 שהיתה מאוד מאסיבית בכיסוי נושאים, והציגה שינויים קטנים וגדולים בשפה. 
היכולות העדכניות כללו מבנה חדש, מודולים, מחלקות וירושה.
השינוי הרחב של ECMAScript 4 גרם לשינוי דרמטי בוועדה TC-39, כאשר מספר חברי ועדה טענו שגרסה זו ניסתה להשיג יותר מדי.
קבוצה של מובילים מגוגל, יאהו ומייקרוסופט יצרו טיוטה אלטרנטיבית עבור ECMAScript שבתחילה כונתה ECMAScript 3.1 וכללה מעט שינויים מבניים והתרכזה ב-property attributes, תמיכה ב Native JSON והוספת מתודות לאובייקטים קיימים.
למרות שהיה ניסיון מסויים לאמץ את ECMAScript 3.1 ו-ECMAScript 4 - ניסיון זה כשל בקרב תומכי שני המחנות והיה קושי רב לגשר בין שתי הפרספקטיבות לגבי הדרך שבה השפה צריכה להתפתח.
בשנת 2008 - Brendan Eich, היוצר של JavaScript הכריז שהועדה TC-39 צריכה להתרכז ב-ECMAScript 3.1.
הוא טען שיש להמתין עם עיקר השינויים המבניים של ECMAScript 4 לשלב מאוחר יותר, ושכל חברי הוועדה יעסקו בהבאת ECMAScript 3.1 ו-ECMAScript 4 לתקן חדש שכונה ECMAScript Harmony.
לבסוף ECMAScript 3.1 הוגדר כתקן גרסה 5 של ECMA-262 וכונה ECMAScript 5. הועדה לא פירסמה את ECMAScript 4 בכדי לא לגרום לבלבול.
לאחר הוצאת גרסה 5, החלה הועדה לעסוק ב ECMAScript Harmony, אשר לבסוף פורסם כ-ECMAScript 6 כתקן עם רוח "הרמונית".
ECMAScript 6 הגיעה למצב של feature complete במהלך שנת 2015 ופורסמה פורמאלית כ "ECMAScript 2015" (כאשר עדיין הכוונה היא ל-ECMAScript 6 - השם המקובל בקרב מתכנתים בעולם). 
בתקן זה התכונות כוללות מנעד רחב של שינויים החל מאובייקטים חדשים, תבניות ועד לשינויים מבניים.
החלק המלהיב ב-ECMAScript 6 הוא שהשינויים בשפה מוכוונים היטב לפתרון בעיות שמפתחים נתקלו בהם בפועל.
 
##על ספר זה 

הבנה טובה של התכונות והיכולות של ECMAScript 6 היא מפתח לכל מפתחי ה-JavaScript המתקדמים לאימוץ התקנים החדשים.
התכונות בשפה המוצגת כ-ECMAScript 6 מציגות את הבסיס עליו אפליקציות JavaScript יבנו בעתיד הנראה לעין.
למטרה זו נכתב ספר זה. תקוותי שתקראו ספר זה על מנת ללמוד על התכונות והיכולות של ECMAScript 6 כך שתהיו מוכנים להתחיל להשתמש בתכונות והיכולות החדשות בכל עת שתזדקקו להם.
 
###תאימות ל Node.js ודפדפנים 

סביבות רבות של JavaScript - הן בדפדפנים שונים והן ב-Node.js , עובדות על יישום בפועל של ECMAScript 6.
ספר זה אינו עוסק בבעיות יישום התקן בסביבות השונות אלא מתרכז בהגדרות התקן והתנהגות נכונה.
לכן - ייתכן שבסביבות מסויימות תתקלו בבעיות תאימות לתקן, אשר אינן נכללות בספר זה.
 
###למי מיועד ספר זה 

ספר זה נועד להדריך את אלו המכירים JavaScript ו-ECMAScript 5. בעוד שהבנת JavaScript לעומק אינה הכרחית לשימוש בספר זה, הוא יסייע לכם להבין את ההבדלים בין ECMAScript 5 ובין ECMAScript 6.
ספר זה נועד למעבר מהיר לתכנות מתקדם עבור מפתחים בסביבת Node.JS וקוד לדפדפנים, אשר מעוניינים ללמוד על התפתחויות עדכניות בשפה וליישם אותן.
ספר זה אינו למתחילים שמעולם לא תכנתו ב JavaScript. הנכם אמורים להיות בעלי הבנה בסיסית טובה בשפה בכדי להשתמש בספר זה. 
 
###תוכן עניינים 

כל אחד מ-13 הפרקים בספר מכסה אספקט אחר ב-ECMAScript 6. פרקים רבים מתחילים בדיון על הבעיות ש-ECMAScript 6 נועדה לפתור, על מנת להציג יריעה רחבה לסיבות לשינויים אלו.
כל פרק מכיל מספר דוגמאות קוד על מנת לסייע לך ללמוד מבנה חדש בשפה וקונספטים תכנותיים חדשים.

<B>פרק 1 : כיצד עובד Block Binding </B> מדבר על `let` ו-`const` - החלפת block-level ב-var.

<B> פרק 2 : Strings ו Regular Expressions </B> סוקר יכולת נוספת חדשה לעיבוד על מחרוזות וכן מציג את הקונספט החדש של template strings.

<B> פרק 3 : Functions in ECMAScript 6 </B> עוסק בשינויים הקשורים בפונקציות, שינויים הכוללים את המבנה של פונקציות חץ, ערך ברירת מחדל לפרמטרים של פונקציה ונושאים נוספים.
 
<B> פרק 4 : הרחבת פונקציונאליות של אובייקטים </B> מסביר את השינוי כיצד אובייקטים נוצרים, עוברים שינוי וכיצד משתמשים בהם. הנושאים כוללים שינוי בליטרל של אובייקט, ומתודות הצגה.

<B> פרק 5 : פעולת הרס אובייקטים עבור גישה פשוטה יותר לנתונים </B> מציג הרס אובייקטים ומערכים, המאפשר לבצע דקומפוז של אובייקטים ומערכים בצורת קצרה ונוחה יותר.

<B> פרק 6 : Symbols ו - Symbols property </B> מציג את הקונספט של Symbols, דרך חדשה להגדיר תכונות אובייקט. Symbols הוא סוג חדש של primitive type שניתן להשתמש בו בצורה מוסתרת (אבל לא נסתרת לחלוטין) לגבי תכונות ומתודות של אובייקט.

<B> פרק 7 : Sets ו Maps </B> מפרט את הסוגים החדשים של `Set`, `WeakSet`, `Map`, ו-`WeakMap`. סוגים חדשים אלו מרחיבים את השימושיות של מערך, מבחינת סמנטיקה וניהול זיכרון שהותאם במיוחד ל-JavaScript.
 
<B> פרק 8 : Iterators ו Generators </B> עוסק בתוספת של Iterators ו Generators לשפה. תכונות אלו מאפשרות עבודה עם אוסף של נתונים בצורה יעילה ועוצמתית שלא התאפשרה בגרסה הקודמת של JavaScript.

<B> פרק 9 : הצגת Classes ב JavaScript </B> מציג את הקונספט הפורמאלי הראשון של Classes ב JavaScript. לעיתים נקודת בילבול בין מה שקיים בשפות תכנות אחרות, התוספת של מבנה של Class ב-JavaScript מאפשר שימוש בשפה בצורה יותר נוחה ובצורה מקוצרת למי שמעוניין.
 
<B> פרק 10 : יכולות משופרות של Array </B> מפרט את השינויים שהוכנסו למערכים רגילים ודרכים מעניינות חדשות בהם מערכים ניתנים לשימוש ב-JavaScript.

<B> פרק 11 : Promises ותכנות אסינכרוני </B> מציג את Promises כחלק חדש בשפה. Promises היו מאמץ שורשי לשינוי שבסוף הצליחו להיות פופולריים בזכות תמיכה מאסיבית בספריות שונות.ECMAScript 6 מציגה Promises בצורה פורמאלית ומאפשרת זמינותם כברירת מחדל.

<B> פרק 12 : Proxies וה-API של ה Reflection </B> מציג את הצורה הפורמאלית של reflection API בשפת JavaScript ואת ה proxy object החדש שמאפשר למפתח לתפוס כל אירוע של שינוי אובייקטים. Proxies מאפשרים למפתח שליטה מוחלטת על האובייקט, וככאלה מוסיפים אפשרויות אינסופיות של תבניות שליטה אינטראקטיביות.

<B> פרק 13 : עטיפת קוד בעזרת Modules </B> מפרט את ה Module פורמט ב JavaScript. המוטיב הוא שמודולים אלו יכולים להחליף מספר רב של ad-hoc module definition format שהופיע ברבות השנים.
 
<B> Appendix A: שינויים קטנים ב ECMAScript 6 </B> סוקר שינויים ב-ECMAScript 6 שהם בשימוש פחות שוטף ולכן לא כוסו בפרקים אחרים.

<B> Appendix B: הבנת ECMAScript 7 </B> מתאר את שתי התוספות לתקן ב-ECMAScript 7, שלא השפיעו על JavaScript כמו ECMAScript 6.


</div>


<div dir="rtl">

###סימונים מוסכמים שבשימוש בספר זה 

להלן הסימונים הטיפוגראפיים והמוסכמות בשימוש בספר זה 

* *Italics* מסמנות הגדרה חדשה 
* `Constant width` מסמן קטע קוד או שם קובץ

בנוסף, קטעי דוגמת קוד ארוכים מוכלים במסגרות בלוק של קוד כדוגמת:
</div>

<div dir="ltr">

```js
function doSomething() {
 // empty
}
```
</div>
<div dir="rtl">
בתוך הקוד בלוק, סימני הערה עם גרשיים בצד ימין מציגים את הפלט שיוצג בדפדפן בקונסול או ב-Node.js בקונסול לאחר הרצת הקוד, לדוגמא לפקודה console.log:

</div>

<div dir="ltr">

```js
console.log("Hi"); // "Hi"
```
</div>

<div dir="rtl">

במידה ושורת קוד תיצור חריגה גם זה יצויין בצד ימין בהערה, לדוגמא:

</div>

<div dir="ltr">

```js
doSomething(); // error!
```
</div>

<div dir="rtl">

###עזרה ותמיכה 

תוכלו להעלות נושאים, הצעות שיפור או פול רקוואסט לספר זה על ידי גלישה לכתובת:

[https://github.com/nzakas/understandinges6](https://github.com/nzakas/understandinges6)

</div>

<div dir="rtl">

### תודות לתורמים לספר 

</div>


Thanks to Jennifer Griffith-Delgado, Alison Law, and everyone at No Starch Press for their support and help with this book. Their understanding and patience as my productivity slowed to a crawl during my extended illness is something I will never forget.

I'm grateful for the watchful eye of Juriy Zaytsev as tech editor and to Dr. Axel Rauschmayer for his feedback and several conversations that helped to clarify some of the concepts discussed in this book.

Thanks to everyone who submitted fixes to the version of this book that is hosted on GitHub: ShMcK, Ronen Elster, Rick Waldron, blacktail, Paul Salaets, Lonniebiz, Igor Skuhar, jakub-g, David Chang, Kevin Sweeney, Kyle Simpson, Peter Bakondy, Philip Borisov, Shaun Hickson, Steven Foote, kavun, Dan Kielp, Darren Huskie, Jakub Narębski, Jamund Ferguson, Josh Lubaway, Marián Rusnák, Nikolas Poniros, Robin Pokorný, Roman Lo, Yang Su, alexyans, robertd, 404, Aaron Dandy, AbdulFattah Popoola, Adam Richeimer, Ahmad Ali, Aleksandar Djindjic, Arjunkumar, Ben Regenspan, Carlo Costantini, Dmitri Suvorov, Kyle Pollock, Mallory, Erik Sundahl, Ethan Brown, Eugene Zubarev, Francesco Pongiluppi, Jake Champion, Jeremy Caney, Joe Eames, Juriy Zaytsev, Kale Worsley, Kevin Lozandier, Lewis Ellis, Mohsen Azimi, Navaneeth Kesavan, Nick Bottomley, Niels Dequeker, Pahlevi Fikri Auliya, Prayag Verma, Raj Anand, Ross Gerbasi, Roy Ling, Sarbbottam Bandyopadhyay, and Shidhin.

Also, thanks to everyone who supported this book on Patreon: Casey Visco.
