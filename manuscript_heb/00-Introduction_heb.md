<div dir="rtl">

# הקדמה

תכונות השפה הקרויה   JavaScript  מוגדרת בתקן הקרוי   ECMA-262.  
<BR/>
שפת התכנות המוגדרת בתקן זה נקראת  ECMAScript. <BR/>
מה שמוכר לך כ  JavaScript  בדפדפנים וב  Node.js הוא למעשה הרחבות  של   ECMAScript. 
דפדפנים וסביבית Node.js מוסיפים פונקציונאליות  בעזרת אוביקטים נוספים ומטודות , אבל עדיין  ה core של השפה נשאר  <BR/>
ECMAScript. 
ההתפתחות הפרואקטיבית של סטנדרט  ECMA-262  חיונית להצלחתה של ה    JavaScript  כמכלול. <BR/>
ספר זה סוקר השינויים שנעשו בשפה  בעדכון החשוב המרכזי  : ECMAScript 6. 


## הדרך ל-  ECMAScript 6

ב 2007  JavaScript  היתה בצומת דרכים.   
הפופולאריות של  Ajax גברה  בעידן של אפליקציות ווב דינאמיות,   
בעוד   JavaScript לא השתנה מאז הגרסה השלישית   של התקן  ECMA-262  שפורסמה ב  1999.
TC-39, הועדה האחראית לקדם את התפתחות      ECMAScript  הכינה דראפט  גדול ל      ECMAScript 4  שהיה מאוד מאסיבי בכיסוי נושאים , 
ושהציג שינויים קטנים וגדולים בשפה. 
היכולות העדכניות כללו  סינטאקס חדש,   modules, classes,  classes inheritance. 
הסקופ הרחב של    ECMAScript 4  גרמה לשינוי דרמטי בועדה   TC-39 ,  כאשר מספר חברי ועדה שברו  שגרסה זו ניסתה להשיג יותר מדי.  
קבוצה של מנהיגים  מ    Yahoo, Google, and Microsoft  יצרו  דראפט אלטרנטיבי  עבור    ECMAScript  שבתחילה כונה   ECMAScript 3.1 שכלל  מעט שינויי סינטאקס , והתרכז בפרופרטי אטריביוטס  , תמיכה ב    native JSON  והוספת מטודות לאוביקטים קיימים .
למרות שהיה ניסיון מסויים לאמץ  את   ECMAScript 3.1    ו   ECMAScript 4  זה כשל בתומכי שתי המחנות  והיה קושי רב ל לגשר בין  שני הפרספקטיבות של שני המחנות לגבי הדרך שבה השפה צריכה להתפתח. 
בשנת 2008   Brendan Eich  היוצר של JavaScript  הכריז  שהועדה   TC-39   צריכה להתרכז ב   ECMAScript 3.1 . 
הוא טען שיש להמתין עם עיקר שינויי הסינטאקס   של  ECMAScript 4  לשלב מאוחר יותר,   ושכל חברי הועדה יעסקו  בהבאת   ECMAScript 3.1  ו ECMAScript 4  לתקן חדש   שכונה ECMAScript Harmony.  
לבסוף  ECMAScript 3.1  הוגדר כתקן גרסה 5   של    ECMA-262  וכונה ECMAScript 5. הועדה  לא פירסמה את ECMAScript 4 בכדי לא לגרום לבילבול . 
אז החלה הועדה לעסוק   ב   ECMAScript Harmony , ולבסוף פורסם כ   ECMAScript 6  כתקן עם רוח  "הרמונית".  
ECMAScript 6  הגיע למצב של     feature complete  במהלך שנת 2015  ופוסמה פורמאלית כ   "ECMAScript 2015" (אבל עדיין הכוונה ל   ECMAScript 6 השם המקובל בקרב תכנתים בעוךם).  
בתקן זה התכונות  כוללות  מנעד רחב של שינויים  החל מאוביקטים חדשים   , תבניות  ועד לשינויי סינטאקס . 
המלהיב ב  ECMAScript 6   הוא שהשינויים  בשפה מוכוונים היטב  לפתרון בעיות שמפתחים נתקלו בהם בפועל.

 
## על ספר זה 
הבנה טובה  של התכונות והיכולות של   ECMAScript 6   הוא מפתח לכל מפתחי ה  JavaScript המתקדמים לאימוץ התקנים החדשים. 
התכונות בשפה המוצגת ב    ECMAScript 6  מציגות הבסיס  עליו אפליקציות   JavaScript   יבנו בעתיד הנראה לעין. 
 למטרה זו נכתב ספר זה.  תקוותי שתקראו ספר זה למוד על התכונות והיכולות  של  ECMAScript 6  כך שתהיו מוכנים  להתחיל להשתמש בתכונות והיכולות החדשות בכל עת שתזקקו להם . 

 
### תאימות ל  Node.js ודפדפנים 
סביביות רבות של   JavaScript,  כדפדפנים שונים  ו  Node.js , עובדים על יישום בפועל  של   ECMAScript 6 . 
ספר זה אינו עוסק בבעיות יישום התקן בסביבות השונות  אלא מתרכז בהגדרות התקן  והתנהגות נכונה. 
לכן , יתכן שבסביבות מסויימות תתקלו בבעיות תאימות לתקן שלא נכללות בספר זה. 
 
### למי מיועד ספר זה 

ספר זה נועד להדריך אלו המכירים  JavaScript  ו ECMAScript 5.  בעוד שהבנת JavaScript לעומק אינה הכרחית לשימוש בספר זה ,  הוא יסיעע לכם להבין ההבדלים בין   ECMAScript 5  ו ECMAScript 6. 
במיוחד , ספר זה נועד  למעבר  מהיר לתכנות מתקדם  למפתחים בסביבת   Node.JS וקוד לדפדפנים המעונינים ללמוד על התפתחויות  עדכניות  בשפה וליישם אותם. 
ספר זה אינו למתחילים שמעולם לא תכנתו ב  JavaScript. הנכם אמורים להיות בעלי הבנה בסיסית טובה  בשפה בכדי להשתמש  בספר זה. 
 
 
###  תוכן ענינים 

כל אחד מ - 13 הפרקים בספר מכסה  אספקט אחר  ב   ECMAScript 6.  פרקים רבים מתחילים בדיון על  הבעיות ש  ECMAScript 6 נועדו לפתור ,   בכדי להציג יריעה רחבה לסיבות  ששינוים אלו הוגדרו.   
כל פרק מכיל מספר דוגמאות קוד לסייע לך ללמוד סינטאקס חדש וקונספטים חדשים. 



<B>פרק  1 : כיצד עובד Block Binding </B>  הפרק מדבר על    `let` and `const` , החלפת block-level gcur  var. 

<B> פרק 2 : Strings ו Regular Expressions </B> הפרק סוקר פונקציונאליות נוספת חדשה לעיבוד ומניפולאציות על מחרוזות string ו בחינה  וכן מציג הקונספט החדש  template strings. 

 

</div>




### Overview

Each of this book's thirteen chapters covers a different aspect of ECMAScript 6. Many chapters start by discussing problems that ECMAScript 6 changes were made to solve, to give you a broader context for those changes, and all chapters include code examples to help you learn new syntax and concepts.

**Chapter 1: How Block Bindings Work** talks about `let` and `const`, the block-level replacement for `var`.

**Chapter 2: Strings and Regular Expressions** covers additional functionality for string manipulation and inspection as well as the introduction of template strings.

**Chapter 3: Functions in ECMAScript 6** discusses the various changes to functions. This includes the arrow function form, default parameters, rest parameters, and more.

**Chapter 4: Expanded Object Functionality** explains the changes to how objects are created, modified, and used. Topics include changes to object literal syntax, and new reflection methods.

**Chapter 5: Destructuring for Easier Data Access** introduces object and array destructuring, which allow you to decompose objects and arrays using a concise syntax.

**Chapter 6: Symbols and Symbol Properties** introduces the concept of symbols, a new way to define properties. Symbols are a new primitive type that can be used to obscure (but not hide) object properties and methods.

**Chapter 7: Sets and Maps** details the new collection types of `Set`, `WeakSet`, `Map`, and `WeakMap`. These types expand on the usefulness of arrays by adding semantics, de-duping, and memory management designed specifically for JavaScript.

**Chapter 8: Iterators and Generators** discusses the addition of iterators and generators to the language. These features allow you to work with collections of data in powerful ways that were not possible in previous versions of JavaScript.

**Chapter 9: Introducing JavaScript Classes** introduces the first formal concept of classes in JavaScript. Often a point of confusion for those coming from other languages, the addition of class syntax in JavaScript makes the language more approachable to others and more concise for enthusiasts.

**Chapter 10: Improved Array Capabilities** details the changes to native arrays and the interesting new ways they can be used in JavaScript.

**Chapter 11: Promises and Asynchronous Programming** introduces promises as a new part of the language. Promises were a grassroots effort that eventually took off and gained in popularity due to extensive library support. ECMAScript 6 formalizes promises and makes them available by default.

**Chapter 12: Proxies and the Reflection API** introduces the formalized reflection API for JavaScript and the new proxy object that allows you to intercept every operation performed on an object. Proxies give developers unprecedented control over objects and, as such, unlimited possibilities for defining new interaction patterns.

**Chapter 13: Encapsulating Code with Modules** details the official module format for JavaScript. The intent is that these modules can replace the numerous ad-hoc module definition formats that have appeared over the years.

**Appendix A: Smaller ECMAScript 6 Changes** covers other changes implemented in ECMAScript 6 that you'll use less frequently or that didn't quite fit into the broader major topics covered in each chapter.

**Appendix B: Understanding ECMAScript 7 (2016)** describes the two additions to the standard that were implemented for ECMAScript 7, which didn't impact JavaScript nearly as much as ECMAScript 6.

### Conventions Used

The following typographical conventions are used in this book:

* *Italics* introduces new terms
* `Constant width` indicates a piece of code or filename

Additionally, longer code examples are contained in constant width code blocks such as:

```js
function doSomething() {
    // empty
}
```

Within a code block, comments to the right of a `console.log()` statement indicate the output you'll see in the browser or Node.js console when the code is executed, for example:

```js
console.log("Hi");      // "Hi"
```

If a line of code in a code block throws an error, this is also indicated to the right of the code:

```js
doSomething();          // error!
```

### Help and Support

You can file issues, suggest changes, and open pull requests against this book by visiting: [https://github.com/nzakas/understandinges6](https://github.com/nzakas/understandinges6)

<!-- I would suggest leaving the note above out of the print version, since readers
won't be able to file issues on it. /JG -->

If you have questions as you read this book, please send a message to my mailing list: [http://groups.google.com/group/zakasbooks](http://groups.google.com/group/zakasbooks).

## Acknowledgments

Thanks to Jennifer Griffith-Delgado, Alison Law, and everyone at No Starch Press for their support and help with this book. Their understanding and patience as my productivity slowed to a crawl during my extended illness is something I will never forget.

I'm grateful for the watchful eye of Juriy Zaytsev as tech editor and to Dr. Axel Rauschmayer for his feedback and several conversations that helped to clarify some of the concepts discussed in this book.

Thanks to everyone who submitted fixes to the version of this book that is hosted on GitHub: ShMcK, Ronen Elster, Rick Waldron, blacktail, Paul Salaets, Lonniebiz, Igor Skuhar, jakub-g, David Chang, Kevin Sweeney, Kyle Simpson, Peter Bakondy, Philip Borisov, Shaun Hickson, Steven Foote, kavun, Dan Kielp, Darren Huskie, Jakub Narębski, Jamund Ferguson, Josh Lubaway, Marián Rusnák, Nikolas Poniros, Robin Pokorný, Roman Lo, Yang Su, alexyans, robertd, 404, Aaron Dandy, AbdulFattah Popoola, Adam Richeimer, Ahmad Ali, Aleksandar Djindjic, Arjunkumar, Ben Regenspan, Carlo Costantini, Dmitri Suvorov, Kyle Pollock, Mallory, Erik Sundahl, Ethan Brown, Eugene Zubarev, Francesco Pongiluppi, Jake Champion, Jeremy Caney, Joe Eames, Juriy Zaytsev, Kale Worsley, Kevin Lozandier, Lewis Ellis, Mohsen Azimi, Navaneeth Kesavan, Nick Bottomley, Niels Dequeker, Pahlevi Fikri Auliya, Prayag Verma, Raj Anand, Ross Gerbasi, Roy Ling, Sarbbottam Bandyopadhyay, and Shidhin.

Also, thanks to everyone who supported this book on Patreon: Casey Visco.

