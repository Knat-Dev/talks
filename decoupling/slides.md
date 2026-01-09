---
theme: ./theme
title: Decoupling in Angular
info: |
  ## Decoupling in Angular
  Letting the Code Tell You What It Needs

  Dor Peled · @Knat-Dev

  Runtime: ~2,900 words in speaker notes.
  120 wpm (with pauses) = ~24 min.
layout: a-main-cover-2
highlighter: shiki
transition: slide-left
mdc: true
---

# Decoupling in Angular

## Letting the Code Tell You What It Needs

<div class="flex items-center gap-6 mt-4">
  <img src="/assets/profile.jpg" class="w-24 h-24 rounded-full object-cover" alt="Dor Peled" />
  <div>
    <div class="font-bold text-xl">Dor Peled</div>
    <div class="opacity-80">Software Engineer @ Coralogix</div>
    <div class="flex gap-4 mt-3 text-lg">
      <a href="https://github.com/Knat-Dev" target="_blank" class="opacity-75 hover:opacity-100"><carbon-logo-github /></a>
      <a href="https://www.linkedin.com/in/dor-peled-swe/" target="_blank" class="opacity-75 hover:opacity-100"><carbon-logo-linkedin /></a>
    </div>
  </div>
</div>

<!--
BEATS:<br>• פתיחה — דור, Coralogix<br>• הרמת ידיים: מי הוסיף "רק עוד input אחד"?<br>• ככה מתחיל God Component<br>• מחפשים "tells" — כמו בפוקר<br>• Docs = syntax, Pain = architecture<br>• הצגת הסיפור של ה-grid

[0:00 - 1:15]

היי כולם. אני דור — software engineer ב-Coralogix.

בואו נעשה רגע הרמת ידיים — מי פה הוסיף פעם "רק עוד input אחד" לקומפוננטה? עוד boolean flag אחד?

[מחכה לידיים]

כן. גם אני. ככה God Components מתחילים. לא מתוך כוונות רעות — אלא מתוך feature requests סבירים לגמרי.

היום אנחנו מחפשים tells. כמו tells בפוקר. הרגע הזה שבו אתם מסתכלים על קוד ומרגישים ש"משהו פה לא בסדר" אבל לא יודעים להסביר בדיוק מה.

דוקומנטציה מלמדת סינטקס. כאב מלמד ארכיטקטורה. אני הולך להראות לכם את הכאב — כדי שתזהו אותו לפני שהוא יעלה לכם ביוקר.

בואו נדבר על קומפוננטה שלימדה אותי כל מה שאני יודע על decoupling.

[מבט לקהל — קשר עין]
-->

---
layout: center
---

<div class="flex flex-col items-center gap-6">
  <img src="/assets/qr-slides.png" class="w-48 rounded-lg shadow-lg" />
  <div class="text-center">
    <div class="text-2xl font-bold">slides.knat.dev</div>
    <div class="text-gray-400 text-sm mt-2">Follow along on your device</div>
  </div>
</div>

<!--
BEATS:<br>• QR code — לעקוב אחרי הסליידים

מי שרוצה לעקוב מהטלפון או מהלפטופ — סרקו רגע את ה-QR.
-->

---
layout: default
---

# The Grid

At work, I built a grid wrapper. It started simple.

Then it didn't.

<v-click>
<img src="/assets/this-is-fine.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
BEATS:<br>• בעיה אמיתית — grid wrapper בעבודה<br>• התחיל פשוט, פיצ'רים נערמו<br>• ה-Context menu גרם לצימוד (coupling)<br>• כאב כפול ב-Drawers<br>• [CLICK MEME] הרמת ידיים<br>• אני אראה לכם איך לתקן את זה

[1:30 - 3:00]

לפני שאני מלמד אתכם משהו, בואו נראה את הבעיה האמיתית.

בעבודה, בניתי grid component. ביום הראשון: client-side fetching, row data, וזהו.

ואז הפיצ'רים התחילו להיערם. Cell renderers, Row styles, Expand/collapse. ואז הגיע ה-context menu — ופה זה נהיה גרוע. ה-context menu הכריח אותנו לייצר coupling. כל צרכן של ה-grid היה חייב לספק את אותם דברים.

השתמשנו בו בדף הראשי. סבבה. אז הוספנו אותו ל-drawer. זה נהיה מלוכלך. צימוד ל-global state, סרוויסים בכל מקום.

ואז הוספנו אותו לעוד drawer... אותו כאב בדיוק.

[Click for meme]

הרמת ידיים — מי פה עבד על קומפוננטה כזו?

[הפסקה לידיים]

כן. זה קורה באמת. ואני הולך להראות לכם איך תיקנו את זה. אבל קודם, בואו נלמד את הכלים.
-->

---
layout: section
---

# The Laboratory

A <span class="text-emerald-400 font-mono">ListComponent</span> built to fail.

So we can dissect it.

<!--
BEATS:<br>• אי אפשר להכניס grid שלם לסלייד אחד<br>• בניתי ListComponent עם אותם "פשעים"<br>• כתבתי קוד גרוע בכוונה

[3:00 - 3:30]

ה-Grid המקורי עמוס מדי בפרטים מהדומיין — אי אפשר להכניס אותו לסלייד בצורה ברורה.

אז בניתי ListComponent שמבצע בדיוק את אותם פשעים ארכיטקטוניים. כתבתי אותו גרוע בכוונה.

בואו ננתח אותו.
-->

---
layout: default
---

# The God List

```ts [list.ts]
@Component({ selector: 'app-list' })
export class ListComponent {
  items = input<Item[]>([]);
  // ... loading, error, etc.
  sortable = input(false);        // flag
  showHeader = input(false);      // flag  
  persistState = input(false);    // flag + friends below

  storageKey = input<string>();   // only if persistState
  initialState = input<ListState>();
  #storage = inject(StorageService);
  // ... more services

  ngOnInit() {
    if (this.persistState()) { /* 30 lines... */ }
  }
}
```

_"What features am I today?"_

<!--
BEATS:<br>• Inputs למידע, פלאגים לפיצ'רים<br>• Inputs שתלויים אחד בשני, סרוויסים בשימוש חלקי<br>• תבנית ה-God Component<br>• שואל "מה אני היום?" במקום פשוט להיות רשימה

[3:30 - 4:30]

הנה זה. List component שמנסה לעשות יותר מדי.

Inputs למידע... אבל גם inputs לפלאגים של sorting. פלאגים של storage keys. סרוויסים מוזרקים שמשתמשים בהם רק לפעמים.

תסתכלו על persistState. זה פלאג. אבל הוא סוחב איתו את storageKey, initialState, וסרוויס. הם "מטיילים" ביחד.

זו התבנית של ה-God Component. הוא שואל "אילו פיצ'רים אני היום?" במקום פשוט להיות רשימה.

אותם פשעים כמו ב-grid. עכשיו בואו נפרק אותו.
-->

---
layout: section
---

# The Real Problem

Coupling isn't bad.

## Hidden coupling is.

<!--
BEATS:<br>• צימוד (Coupling) הוא לא רע — צימוד *נסתר* הוא הבעיה<br>• תלויות גלויות (inputs, constructors) = קל לנהל<br>• קבור בתוך פלאגים/טמפלטים = אובדן שליטה<br>• שאלה: איך הופכים coupling לגלוי?<br>• Inputs הם ברירת המחדל, שלוש תבניות חילוץ כשזה נכשל

[4:30 - 5:15]

הנה העניין: Coupling זה לא דבר רע. הקומפוננטות שלכם חייבות לדבר ביניהן. דאטה זורם, איוונטים עולים. זה בסדר גמור.

הבעיה היא HIDDEN coupling.

כשאפשר לראות דפנדנסיז — ב-inputs, ב-constructors, ב-imports — אפשר להבין אותם. אפשר לנהל אותם.

אבל כש-coupling קבור בתוך boolean flags? פזור בתוך טמפלטים? שם מאבדים שליטה. משנים משהו פה, ומשהו נשבר שם.

אז השאלה היא לא "איך אני מסיר coupling?", אלא "איך אני הופך את ה-coupling לגלוי (VISIBLE)?"

זה מה שאנחנו הולכים לעשות. Inputs הם ברירת המחדל — תישארו שם כל עוד אתם יכולים. אבל כש-inputs נכשלים, יש לכם שלוש תבניות חילוץ (extraction patterns). כל אחת הופכת סוג אחר של coupling למפורש (explicit).

[טון בטוח וברור — זה הרעיון המרכזי]
-->

---
layout: image-right
---

::left::

# Three Extraction Patterns

Inputs are your default.

**When they fail, reach for these.**

::default::

| The Tell            | Pattern            |
| ------------------- | ------------------ |
| Structural flags    | Content Projection |
| Behavioral bundles  | Strategy via DI    |
| Composable opt-ins  | Directives         |

<!--
BEATS:<br>• שלוש תבניות חילוץ — אתם מכירים אותן, החכמה היא מתי<br>• Inputs = default, visible coupling<br>• Structural flags ← Content Projection<br>• Behavioral bundles ← Strategy via DI<br>• Composable opt-ins ← Directives<br>• נזהה את ה-tell ביחד

[5:15 - 6:15]

שלוש תבניות חילוץ. אתם בטח מכירים את כולן. החכמה היא לדעת מתי לשלוף כל אחת.

Inputs הם ברירת המחדל. הם יוצרים VISIBLE coupling — אפשר לראות מה קומפוננטה צריכה. תישארו שם כל עוד אפשר.

אבל שימו לב ל-tells.

Structural flags — בוליאנים ששולטים באיזה DOM קיים? Content projection. לחלץ את ה-@if החוצה.

Behavioral bundles — פלאג שסוחב איתו סרוויסים וקוד של lifecycle? Strategy via DI. תנו ל-injector להחליט.

Composable opt-ins — התנהגויות שצריכות להיערם אחת על השנייה (stack)? Directives. גלוי בתוך הטמפלט.

לכל אחת מהן אני אראה קוד. נזהה את ה-tell ביחד.

[הצבעה קצרה על כל שורה וממשיכים]
-->

---
layout: default
---

# The Tell

```ts [list.ts] {1-3}
sortable = input(false);
showHeader = input(false);
persistState = input(false);
```

Boolean flags. The component asking _"what features am I?"_

But look closer at **The God List**...

<!--
BEATS:<br>• Boolean flags = feature toggles, הקומפוננטה שואלת "מה אני?"<br>• זה ה-tell — כמו בפוקר<br>• אבל האמת עמוקה יותר: חלק מה-inputs "מטיילים" ביחד (storageKey + persistState)<br>• נתחיל פשוט: פלאג אחד שלא דומה לאחרים

[6:15 - 7:15]

תסתכלו על אלה. Boolean flags. sortable, showHeader, persistState.

כל אחד הוא feature toggle. הקומפוננטה שואלת: "אילו פיצ'רים אני היום?"

זה ה-tell — כמו בפוקר. כשיש ערימה של בוליאנים? הקומפוננטה מנסה להיות יותר מדי דברים.

אבל הנה העניין — ונחזור לזה בהמשך — בוליאנים הם רק פני השטח. תסתכלו שוב על ה-God List. רואים איך storageKey ו-initialState רלוונטיים רק כש-persistState הוא true? הם "מטיילים" ביחד. האמת עמוקה יותר.

כרגע, בואו נתחיל פשוט. אחד הפלאגים האלה לא דומה לאחרים.

[הפסקה — לזרוע את הזרע, לא להסביר עדיין]
-->

---
layout: default
---

# The Structural Flag

```ts [list.ts]
// From The God List:
showHeader = input(false);
```

```html [list.html]
@if (showHeader()) {
  <app-header />
}
<div class="list-body">...</div>
```

Why does the **list** decide whether a header exists?

<!--
BEATS:<br>• showHeader הוא שונה — הוא שולט ב-STRUCTURE, לא בהתנהגות<br>• @if בטמפלט שמרנדר את ההדר<br>• למה הרשימה מחליטה? זו העבודה של ה-parent<br>• בונוס: תלויות נקיות יותר — הרשימה לא עושה import להדר<br>• החילוץ הכי פשוט

[7:15 - 7:45]

בואו נתחיל עם showHeader. זה שונה מ-sortable או מ-persistState.

זה לא משנה התנהגות — זה שולט במבנה (Structure). אילו אלמנטים קיימים ב-DOM.

תסתכלו על זה. showHeader. ואיפשהו בטמפלט, יש @if שמרנדר הדר.

למה ה-LIST מחליט אם הדר קיים? זה לא דאטה. זו לא התנהגות. זו האחריות של ה-parent.

בונוס: אם ה-parent מזריק אותו פנימה (projects it), הרשימה לא צריכה לייבא את ה-header component. גרף תלויות נקי יותר — הרשימה לא צריכה להכיר את הטיפוס של ההדר בכלל.

זה החילוץ הכי פשוט שאנחנו יכולים לעשות.

[לתת להם להרגיש את ה"אי-נוחות" בקוד]
-->

---
layout: default
---

# Extract the @if

```html [before]
<!-- Before: component decides structure -->
<app-list [showHeader]="true" />
```

```html [after]
<!-- After: parent decides structure -->
<app-list>
  <app-header header></app-header>
</app-list>
```

The `@if` becomes a **slot**. The boolean disappears.

<!--
BEATS:<br>• Before: showHeader=true, קומפוננטה מרנדרת פנימית<br>• After: בלי בוליאני, ה-parent מזריק או לא<br>• @if ← ng-content slot, הבוליאני נעלם<br>• Content projection = חילוץ החלטות מבניות (structural)

[7:45 - 8:15]

תראו מה קורה כשמחלצים את התנאי החוצה.

לפני: showHeader שווה ל-true. הקומפוננטה מרנדרת את ההדר פנימית.

אחרי: אין בוליאני. ה-parent פשוט... שם את ההדר שם. או שלא.

ה-@if הופך ל-ng-content slot. הבוליאני פשוט נעלם.

Content projection הוא לא "פיצ'ר של layout". זה מה שקורה כשמחלצים החלטות מבניות מתוך קומפוננטות.

[זה רגע ה"אהה!" — content projection הוא הסרה של תנאים]
-->

---
layout: section
---

# Tool 1: Content Projection

When **structure** varies

Not behavior. Structure.

<!--
BEATS:<br>• כלי 1: Content Projection<br>• כש-inputs שולטים ב-STRUCTURE (איזה DOM קיים)<br>• אנלוגיה של מסגרת תמונה: מסגרת = צורה, אתם = התמונה<br>• הקומפוננטה בבעלות על ה-layout, אתם בבעלות על ה-content

[8:15 - 8:45]

כלי ראשון. Content projection.

תבנית החילוץ הראשונה. תשתמשו בה כש-inputs שולטים ב-STRUCTURE — איזה DOM קיים, לא מה הוא עושה.

תחשבו על מסגרת לתמונה. המסגרת קובעת את הצורה והגודל. אתם בוחרים את התמונה.

הקומפוננטה היא הבעלים של ה-layout. אתם הבעלים של התוכן.

[מהר — מכינים את הקוד]
-->

---
layout: default
---

# Content Projection

```html [card.html]
<div class="header">
  <ng-content select="[header]" />
</div>
<ng-content />
```

Card owns layout. Consumer owns content via ng-content.

<!--
BEATS:<br>• שני סלוטים: הדר + ברירת מחדל<br>• "אני אטפל במעטפת, אתם תחליטו על התוכן"<br>• כרטיסים, מודאלים, פאנלים — המעטפת עקבית, התוכן משתנה

[8:45 - 9:15]

שני ng-content slots. הדר וברירת מחדל.

הקומפוננטה אומרת: "אני אטפל במעטפת (wrapper). אתם תחליטו מה נכנס בפנים."

כרטיסים, מודאלים, פאנלים. המעטפת נשארת עקבית, התוכן משתנה.

[מהר — הקוד מדבר בעד עצמו]
-->

---
layout: default
---

# The Limit

Doesn't work for:

- _"Save to localStorage vs server"_
- _"Persist state when flag is true"_

You need different **behavior**, not structure.

<!--
BEATS:<br>• showHeader טופל — אחד פחות<br>• אבל מה עם persistState וחברים (סרוויסים, inputs)?<br>• אי אפשר לעשות ng-content ל-HttpClient<br>• Content projection = אלמנטים ב-DOM בלבד<br>• מה קורה vs מה מופיע ← כלי אחר

[9:15 - 9:45]

אז טיפלנו ב-showHeader. הבוליאני נעלם. אחד פחות.

אבל תסתכלו שוב על ה-God List. מה עם persistState? והחברים שלו — storageKey, initialState, הסרוויסים?

אנחנו יכולים לעשות ng-content ל-HttpClient? לשים localStorage בתוך סלוט?

[לתת להם לצחוק]

לא. Content projection נועד לאלמנטים ב-DOM. דברים ויזואליים.

כשהשינוי הוא סביב "מה קורה" (WHAT HAPPENS), ולא "מה מופיע" (WHAT APPEARS) — זה tell אחר. תבנית חילוץ אחרת.

[מעבר ל-strategy]
-->

---
layout: section
---

# Tool 2: Strategy via DI

**A** or **B**

Never both.

<!--
BEATS:<br>• כלי 2: Strategy via DI<br>• או א' או ב', לעולם לא שניהם<br>• שרת או localStorage, אמיתי או mock<br>• הקומפוננטה לא יודעת מה היא קיבלה — ה-DI מספק<br>• אפס if-statements, ההחלטה חיה במקום אחר

[9:45 - 10:15]

כלי שני. Strategy via DI.

א' או ב'. בוחרים אחד. לעולם לא שניהם בו זמנית.

שרת או localStorage. API אמיתי או mock. רק מימוש אחד פעיל.

הקומפוננטה לא יודעת איזה. היא פשוט אומרת "אני צריכה משהו שיכול לשמור". ה-DI מספק את הנכון.

אין if-statements בתוך הקומפוננטה. ההחלטה חיה במקום אחר.

בואו נראה איך זה נראה.

[מכינים את הדוגמה]
-->

---
layout: default
---

# The Behavioral Bundle

```ts [list.ts] {1,3-5,7-8,10-13}
persistState = input(false);

storageKey = input<string>();
initialState = input<ListState>();
stateSaved = output<void>();

#api = inject(ApiService);
#storage = inject(StorageService);

ngOnInit() {
  if (this.persistState()) {
    // 30 lines of restoration logic...
  }
}
```

Remember I said the truth was deeper? **This is it.**

<!--
BEATS:<br>• האמת עמוקה יותר מפלאגים — הנה זה<br>• persistState + storageKey + initialState + סרוויסים + lifecycle<br>• הכל משנה רק אם persistState=true — הם "מטיילים" ביחד<br>• אי אפשר לעשות content-project להתנהגות<br>• מה עושים כשחבילה שלמה (BUNDLE) צריכה לזוז?

[10:15 - 11:00]

זוכרים שאמרתי שהאמת עמוקה יותר מסתם boolean flags?

הנה זה. תסתכלו שוב על הקוד — רק על החלק של ה-persistence.

פלאג אחד — persistState. אבל הוא לא לבד.

storageKey — איפה לשמור. initialState — מה לשחזר. stateSaved — להודיע כשסיימנו.

ואז הסרוויסים. ApiService לשרת. StorageService לדפדפן. וקוד ה-lifecycle.

כל זה רלוונטי רק אם persistState הוא true. הם מטיילים ביחד. הם יחידה התנהגותית אחת.

תובנת מפתח: אי אפשר לעשות content-projection להתנהגות. אי אפשר לשים סרוויס בתוך ng-content slot.

אז מה עושים כשחבילה שלמה של התנהגות צריכה לזוז?

[זו החשיפה — מכינים את ה-Strategy]
-->

---
layout: image-right
---

::left::

# Strategy: The Problem

That whole bundle needs to **move**. But where?

::default::

| Context | Storage      |
| ------- | ------------ |
| Admin   | Server       |
| Public  | localStorage |
| Preview | Don't save   |

<!--
BEATS:<br>• החבילה צריכה לזוז החוצה<br>• ה"לאן" תלוי בקונטקסט: אדמין=שרת, ציבורי=localStorage, תצוגה מקדימה=noop<br>• אותה קומפוננטה, התנהגות שונה<br>• פעם: עוד בוליאנים. היום: לקומפוננטה לא אכפת

[11:00 - 11:30]

כל החבילה הזו — הפלאג, ה-inputs, הסרוויסים, קוד ה-lifecycle — צריכה לצאת אל מחוץ לקומפוננטה.

אבל לאן היא הולכת? זה תלוי בקונטקסט.

אדמין דשבורד? שמירה לשרת. תצוגה ציבורית? localStorage. מצב תצוגה מקדימה? לא לשמור בכלל.

אותה קומפוננטה. התנהגויות אחסון שונות.

הדרך הישנה: עוד בוליאני, עוד if-statement. הדרך החדשה: הקומפוננטה לא יודעת. ולא אכפת לה.

[מהר — מכינים את הפתרון]
-->

---
layout: default
---

# Strategy: The Interface

```ts [storage-strategy.ts]
export interface StorageStrategy {
  save(key: string, data: unknown): Promise<void>;
  load<T>(key: string): Promise<T | null>;
}

export const STORAGE_STRATEGY =
  new InjectionToken<StorageStrategy>('StorageStrategy');
```

<!--
BEATS:<br>• ממשק (Interface): מה (save/load), לא איך<br>• InjectionToken = מפתח לחיפוש<br>• (Promise לסליידים, גם Observable עובד)<br>• רק החוזה, עוד אין מימוש

[11:30 - 11:45]

שלב ראשון: להגדיר מה אנחנו צריכים. save() ו-load(). לא איך — רק שזה "יכול".

InjectionToken הוא מפתח החיפוש שלנו ב-DI.

הערה: אני משתמש ב-Promise כדי לשמור על הסליידים נקיים. אפשר להשתמש גם ב-Observable — אותו דפוס, פרימיטיב אסינכרוני שונה.

עדיין אין מימוש. רק החוזה.

[מהר — קוד הגדרה]
-->

---
layout: default
---

# Strategy: LocalStorage

```ts [local-storage.ts]
export class LocalStorageStrategy
  implements StorageStrategy
{
  async save(key: string, data: unknown) {
    localStorage.setItem(key, JSON.stringify(data));
  }
  async load<T>(key: string) {
    const item = localStorage.getItem(key);
    return item ? JSON.parse(item) as T : null;
  }
}
```

Uses localStorage. Works offline.

<!--
BEATS:<br>• LocalStorageStrategy — אחסון דפדפן, עובד אופליין

[11:45 - 12:00]

מימוש ראשון. LocalStorageStrategy.

אחסון דפדפן. סינכרוני. עובד אופליין.

[מהר — עוברים לבא]
-->

---
layout: default
---

# Strategy: Server

```ts [server-storage.ts]
export class ServerStorageStrategy
  implements StorageStrategy
{
  #api = inject(ApiService);

  async save(key: string, data: unknown): Promise<void> {
    await firstValueFrom(
      this.#api.post<void>('/api/preferences', { key, data })
    );
  }
  async load<T>(key: string): Promise<T | null> {
    return firstValueFrom(
      this.#api.get<T | null>(`/api/preferences/${key}`)
    );
  }
}
```

Uses HttpClient. Syncs across devices.

<!--
BEATS:<br>• ServerStorageStrategy — קריאת API, מסנכרן בין מכשירים<br>• מבחוץ? זהה. הרשימה לא יודעת מה היא קיבלה<br>• אפשר להוסיף NoopStrategy לתצוגה מקדימה

[12:00 - 12:30]

מימוש שני. ServerStorageStrategy.

קריאת API. מסנכרן בין מכשירים.

מבחוץ? זהה. הרשימה קוראת ל-save() ול-load(). היא לא יודעת מה היא קיבלה מתחת למכסה המנוע.

אפשר להוסיף NoopStrategy לתצוגה מקדימה. אותו ממשק, לא עושה כלום.

[ממשיכים]
-->

---
layout: default
---

# Strategy: The Provider

```ts [admin-dashboard.ts]
@Component({
  providers: [
    {
      provide: STORAGE_STRATEGY,
      useClass: ServerStorageStrategy,
    },
  ],
})
export class AdminDashboard {}
```

**Zero if-statements.** Context decides, not component.

<!--
BEATS:<br>• ההחלטה חיה ב-PROVIDER<br>• מפתח: פרוביידר שמוגבל לעץ התחתון (subtree) — הזרקה היררכית<br>• אפס if-statements בקומפוננטה<br>• טיפ למקצוענים: NoopStrategy בשורש, לדרוס לפי קונטקסט<br>• Strategy = בחירה בין אפשרויות. Opt-in = directives<br>• זה צימוד גלוי (visible coupling)

[12:30 - 13:15]

פה ההחלטה חיה. ב-provider.

AdminDashboard אומר: "בעץ שלי, תשתמש ב-ServerStorageStrategy."

תובנת מפתח: ה-provider הזה מוגבל לתת-העץ של AdminDashboard — הוא לא גלובלי. זה הקסם של הזרקה היררכית.

הרשימה מזריקה את STORAGE_STRATEGY. היא מקבלת את גרסת השרת. אפס if-statements.

דף אחר? מספק LocalStorageStrategy. אותה רשימה, התנהגות שונה.

טיפ: תספקו NoopStorageStrategy בשורש האפליקציה, ותדרסו איפה שצריך. בטוח כברירת מחדל, מותאם אישית לפי הצורך.

```ts
// המימוש השלישי — לא עושה כלום, בבטחה
export class NoopStorageStrategy implements StorageStrategy {
  async save() {}
  async load() { return null; }
}
```

הרשימה לעולם לא משתנה. מצב חדש? כותבים אסטרטגיה חדשה, מספקים אותה איפשהו. לרשימה לא אכפת.

חשוב: Strategy נועד למימושים שמוציאים זה את זה (mutually exclusive). רק אחד פעיל בכל פעם. אם אתם רוצים "הרכבה" (opt-in composition) — להוסיף התנהגויות ולא להחליף אותן — בשביל זה יש directives.

זה מה שנקרא צימוד גלוי. אתם יכולים לראות בדיוק איזו התנהגות כל קונטקסט מקבל.

[להדגיש: scoped providers, אפס if-statements, צימוד גלוי]
-->

---
layout: default
---

# The Cue

```ts [list.ts]
export class ListComponent {
  #storage = inject(STORAGE_STRATEGY);
  #sorter = inject(SORT_STRATEGY);
  #filter = inject(FILTER_STRATEGY);
}
```

Features invisible in template. All three always present.

<v-click>
<img src="/assets/one-does-not-simply.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
BEATS:<br>• בעיה: סט הפיצ'רים נסתר בטמפלט<br>• Strategy = להחליף (SWAP), אבל מה עם אופציונלי?<br>• [CLICK MEME]<br>• צריכים התנהגויות שמוסיפות (ADD), לא מחליפות<br>• Strategy מחליף, Directives מוסיפים

[13:15 - 14:00]

אבל תסתכלו על זה. מה הבעיה כאן?

ה-Strategy pattern מעולה לבחירה בין א' ל-ב' — מחליפים מימושים. אבל מה אם sorting הוא אופציונלי? מה אם filtering הוא אופציונלי?

שלוש אסטרטגיות מוזרקות. שלושתן תמיד שם. אפשר להחליף מימושים — אבל אי אפשר לוותר עליהם. Strategy נועד להחלפה, לא להוספה.

[Click for meme]

כן, אפשר להפוך את ההזרקה לאופציונלית. אבל אז הקוד מתמלא בבדיקות null. "אם sorter קיים, תמיין. אם filter קיים, תסנן."

אנחנו צריכים התנהגויות שנערמות (ACCUMULATE) — שמוסיפים אותן, לא מחליפים. והנה המפתח — שזה יהיה גלוי בטמפלט. להסתכל על ה-HTML ולדעת בדיוק מה הרשימה הזו עושה.

זה ה-tell השלישי. Composable opt-ins. אסטרטגיה מחליפה, דירקטיבה מוסיפה.

[מעבר]
-->

---
layout: section
---

# Tool 3: Directives

What's **left** after the right extractions.

The smallest unit of reusable behavior.

<!--
BEATS:<br>• כלי 3: Directives<br>• showHeader ← projection. persistState ← strategy. מה נשאר? sortable<br>• לא מבנה, לא חבילה — רק התנהגות. קיימת או לא<br>• Directive = היחידה הכי קטנה ששרדה את החילוץ<br>• Directives הם לא "עוד כלי" — הם מה ש*נשאר*

[14:00 - 14:45]

כלי שלישי. Directives.

בואו נחזור לרשימה. טיפלנו ב-showHeader — עכשיו זה projection. טיפלנו ב-persistState והחבילה שלו — עכשיו זה strategy.

מה נשאר? sortable.

זה לא שולט במבנה. זו לא חבילה של inputs וסרוויסים שתלויים זה בזה. זו פשוט... התנהגות. או שהרשימה ניתנת למיון, או שלא.

בלי הסתעפויות. בלי שאלות של קונטקסט. קיים או לא קיים.

זו דירקטיבה. היחידה הכי קטנה של התנהגות לשימוש חוזר ששרדה את החילוץ.

דירקטיבות הן לא "סתם עוד כלי בארגז". הן מה ש*נשאר* כשעושים את שאר החילוצים נכון.

[הגדרה מחדש — דירקטיבות הן בלתי נמנעות, לא שרירותיות]
-->

---
layout: default
---

# Directive: Sortable

```ts [sortable.ts]
@Directive({ selector: 'app-list[sortable]' })
export class Sortable {
  #list = inject(ListComponent);

  sortKey = input<string>();
  sortDir = input<'asc' | 'desc'>('asc');

  constructor() {
    effect(() => {
      const items = this.#list.items();
      const key = this.sortKey();
      const dir = this.sortDir();
      if (!key) return;

      const sorted = [...items].sort((a, b) =>
        dir === 'asc' ? a[key] - b[key] : b[key] - a[key]
      );
      this.#list.displayItems.set(sorted);
    });
  }
}
```

Directive owns sort config. List just displays.

<!--
BEATS:<br>• סלקטור: app-list[sortable] — בלי האטריביוט = לא קיים<br>• הדירקטיבה היא הבעלים של ה-inputs (sortKey, sortDir)<br>• הרשימה חושפת displayItems signal — הדירקטיבה כותבת אליו<br>• הסלקטור הופך את הצימוד לגלוי ומפורש

[14:45 - 15:15]

SortableDirective. הסלקטור פונה ל-app-list עם האטריביוט sortable.

אין אטריביוט? הדירקטיבה לא קיימת. אפס overhead.

שימו לב: הדירקטיבה היא הבעלים של ה-inputs של עצמה — sortKey, sortDir. הרשימה רק חושפת displayItems signal שהדירקטיבה מעדכנת.

שימוש ב-inject(ListComponent) על אותו אלמנט מחזיר את המופע של ה-host. כן, הדירקטיבה מכירה את ה-API הפנימי של הקומפוננטה. זה בסדר — הן עוצבו ביחד. הסלקטור הופך את זה למפורש.

ה-Effect מגיב לשינויים ב-items ובקונפיגורציה של המיון. שנו אחד מהם, והרשימה תתמיין מחדש.

[מהר — להראות את הדפוס]
-->

---
layout: default
---

# Directive: Persist

```ts [persistable.ts]
@Directive({ selector: 'app-list[persistable]' })
export class Persistable {
  #list = inject(ListComponent);
  #storage = inject(STORAGE_STRATEGY);

  constructor() {
    effect(() => {
      const key = this.#list.storageKey();
      if (!key) return;
      const state = this.#list.state();   // internal signal
      if (!state.dirty) return;
      this.#storage.save(key, state);
    });
  }
}
```

Notice: injects STORAGE_STRATEGY. **Tools layer together.**

<!--
BEATS:<br>• PersistDirective — אותו דפוס<br>• מפתח: מזריק את STORAGE_STRATEGY — הכלים נערמים ביחד!<br>• Strategy = איפה/איך (שרת vs localStorage)<br>• Directive = מתי (כשהסטייט "מלוכלך" / dirty)<br>• לכל כלי יש תפקיד אחד — קל לבדוק, קל להרכיב (compose)

[15:15 - 15:45]

PersistableDirective. אותו דפוס.

אבל שימו לב — היא מזריקה את STORAGE_STRATEGY. תובנת מפתח: אנחנו מפרקים את ה-persistence לשני צירים.

ה-Strategy עונה על: איפה ואיך אנחנו שומרים? שרת לעומת localStorage לעומת noop.
ה-Directive עונה על: מתי אנחנו שומרים? בשינויי סטייט, רק כשהוא dirty.

כן, ה-effect הזה מבצע I/O. בקוד פרודקשן הייתי מעביר את זה דרך תור עם debouncing וביטולים. אבל הפירוק הוא מה שחשוב: ה"איפה" חי ב-strategy, ה"מתי" חי ב-directive.

הכלים עובדים ביחד. לכל אחד יש תפקיד אחד. קל לבדוק לבד, קל להרכיב ביחד.

[להצביע על הזרקת ה-strategy — זה ה-payoff]
-->

---
layout: default
---

# Directives: Usage

```html [app.html]
<!-- Simple -->
<app-list [items]="data" />

<!-- With sorting -->
<app-list sortable sortKey="name" [items]="data" />

<!-- Full-featured -->
<app-list sortable sortKey="date" sortDir="desc"
          filterable persistable [items]="data" />
```

**Visible in the template.** Look at the HTML, know what it does.

<!--
BEATS:<br>• פשוט: בלי דירקטיבות. עם מיון: sortable + inputs. מלא: כל השלוש<br>• מפתח: גלוי בטמפלט — תסתכלו על ה-HTML, תדעו מה הוא עושה<br>• כל דף בוחר את השילוב שלו — מורכבות בבחירה (opt-in)

[15:45 - 16:15]

תסתכלו על הטמפלט.

דף פשוט? אין דירקטיבות. פשוט רשימה.

צריכים מיון? מוסיפים sortable. הדירקטיבה מנהלת את ה-inputs שלה — sortKey, sortDir. לרשימה לא אכפת.

פיצ'רים מלאים? כל שלוש הדירקטיבות. כל אחת מביאה את הקונפיגורציה שלה.

תובנת מפתח: הכל גלוי בטמפלט. מפתח חדש שפותח את הקובץ הזה יודע מיד מה הרשימה הזו עושה.

[מהר — הטמפלט מדבר בעד עצמו]
-->

---
layout: default
---

# The Sign

```html [scattered across pages]
<!-- Page A -->
<app-list sortable filterable persistable [items]="a" />

<!-- Page B -->
<app-list sortable filterable persistable [items]="b" />

<!-- Page C -->
<app-list sortable filterable persistable [items]="c" />
```

Same combo. Three times.

<v-click>
<img src="/assets/distracted-boyfriend.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
BEATS:<br>• שלושה דפים, אותן שלוש דירקטיבות — מועתק (copy-paste)<br>• הצימוד נסתר בתוך החזרתיות<br>• רוצים להוסיף חמישית? צריך לחפש בכל דף. פספסתם אחד? נוצר drift<br>• [CLICK MEME]<br>• אותו שילוב 3 פעמים = קונספט שאין לו שם

[16:15 - 17:00]

אבל תסתכלו על זה. מה אתם רואים?

שלושה דפים. אותן שלוש דירקטיבות. Copy-paste.

הצימוד (coupling) נסתר בתוך החזרתיות (repetition). "אנחנו תמיד משתמשים בשלוש האלה ביחד."

מה קורה כשמוסיפים פיצ'ר חמישי? צריך לצוד כל דף באפליקציה. לעדכן את כולם. פספסתם אחד? עכשיו הם מתחילים להתרחק זה מזה.

[Click for meme]

אותו שילוב שלוש פעמים. זה לא מקרי. זה קונספט שעדיין אין לו שם.

[מכינים את כלל הקידום / promotion rule]
-->

---
layout: section
---

# The Promotion Rule

Two times is coincidence.

**Three times is a concept.**

## Name it.

<!--
BEATS:<br>• פעם 1 = קוד. פעם 2 = מקריות. פעם 3 = קונספט — תנו לו שם<br>• סייג: אותה משמעות, אותה סיבה. סיבות שונות = אל תקבצו<br>• שם = אפשר לדבר על זה, לתעד, לבדוק, לפתח במקום אחד<br>• דפוסים בלי שם נוטים ל-drift (סיפור: הסינון כבוי "זמנית")<br>• 3 פעמים אותה משמעות? תנו לזה שם

[17:00 - 17:45]

כלל הקידום.

פעם אחת: פשוט קוד.
פעמיים: אולי מקריות.
שלוש פעמים: זה קונספט. תנו לו שם.

סייג חשוב: שלוש פעמים עם אותה משמעות, מאותה סיבה. אם הסיבות שונות, אל תקבצו אותן. זה שכפול דמה (false duplication).

כשנותנים למשהו שם, אפשר לדבר עליו. לתעד אותו. לבדוק אותו. לפתח אותו במקום אחד.

דפוסים בלי שם נוטים להתפזר. היו לנו שלוש "רשימות עם כל הפיצ'רים". באחת מהן הסינון היה כבוי "זמנית". חצי שנה אחרי? אף אחד לא ידע למה.

רואים את זה שלוש פעמים עם אותה משמעות? תנו לזה שם.

[להעביר בביטחון — זה מסר זכיר]
-->

---
layout: default
---

# hostDirectives

```ts [power-list.ts]
@Directive({
  selector: 'app-list[powerList]',
  hostDirectives: [
    Sortable,
    Filterable,
    Persistable,
  ],
})
export class PowerList {}
```

One attribute. Three behaviors.

<!--
BEATS:<br>• hostDirectives — מקבץ דירקטיבות<br>• PowerList: אטריביוט אחד, שלוש התנהגויות<br>• גוף הדירקטיבה ריק — רק הרכבה (composition), שם לדפוס<br>• רוצים להוסיף התנהגות רביעית? קובץ אחד. וזהו

[17:45 - 18:15]

אנגולר נותנת לנו את hostDirectives.

PowerListDirective מקבצת שלוש אחרות. אטריביוט אחד, שלוש התנהגויות.

תסתכלו על גוף הדירקטיבה. ריק. אין לוגיקה. רק קומפוזיציה. זה פשוט שם שניתן לדפוס מסוים.

רוצים להוסיף התנהגות רביעית? מעדכנים קובץ אחד. סיימנו.

[להראות איך מתן שם יוצר maintainability]
-->

---
layout: default
---

# Before/After

```html [app.html]
<!-- Before: 3 attributes -->
<app-list sortable filterable persistable [items]="data" />

<!-- After: 1 named concept -->
<app-list powerList [items]="data" />
```

<v-click>
<img src="/assets/pam-theyre-different.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
BEATS:<br>• Before: 3 אטריביוטים. After: מילה אחת — powerList<br>• [CLICK MEME]<br>• פונקציונלית זה זהה, קונספטואלית זה עולמות שונים<br>• מפתח חדש רואה "powerList" — מבין מיד<br>• רשימה של דברים ← קונספט

[18:15 - 18:30]

לפני: שלושה אטריביוטים. אחרי: מילה אחת. powerList.

[Click for meme]

פונקציונלית זה זהה לחלוטין. קונספטואלית? עולמות שונים.

מפתח חדש רואה "powerList" — הוא מבין מיד מה הרשימה הזו עושה.

הגרסה הראשונה היא רשימה של דברים. השנייה היא קונספט.

[קצר — הסלייד ברור]
-->

---
layout: default
---

# Coordinator Directive

```ts [auto-saveable.ts]
@Directive({
  selector: 'app-list[autoSaveable]',
  hostDirectives: [DirtyTrackable, Debounceable],
})
export class AutoSaveable {
  #dirty = inject(DirtyTrackable);
  #debounce = inject(Debounceable);

  constructor() {
    effect(() => {
      if (this.#dirty.isDirty()) {
        this.#debounce.run(() => this.save());
      }
    });
  }
}
```

When A and B **must** work together.

<!--
BEATS:<br>• Coordinator directive — כשדירקטיבות חייבות לעבוד ביחד<br>• DirtyTrackable + Debounceable = עצמאיות<br>• AutoSaveable מתאם: כשזה dirty, תריץ debounce לשמירה<br>• צימוד מכוון? תנו לו בית — עם שם, שאפשר לבדוק

[18:30 - 19:00]

עוד דפוס אחד. Coordinator directive.

זוכרים את ה-dirty guard ב-Persistable? זו הייתה הגרסה הפשוטה. בקוד אמיתי, אתם רוצים גם debouncing, ביטול של שמירות קודמות וכו'.

הפרודקט אומר: "תשמור אוטומטית כשזה dirty, אבל עם debounce."

DirtyTrackable ו-Debounceable הן דירקטיבות עצמאיות. אף אחת מהן לא מכירה את השנייה.

AutoSaveable מביאה את שתיהן, ומתאמת ביניהן. כשזה dirty, תעשה debounce לשמירה.

צימוד מכוון? תנו לו בית. מקום אחד. עם שם. שאפשר לכתוב לו טסטים.

[קצר — הם מבינים את זה]
-->

---
layout: section
---

# When NOT to Use These

Each tool has limits.

<!--
BEATS:<br>• מעקות בטיחות קצרים — מתי לא להשתמש

[19:00 - 19:15]

מעקות בטיחות. מתי לא להשתמש בכל אחת מתבניות החילוץ.

[קטע מהיר]
-->

---
layout: default
---

# Anti-Patterns

| Pattern            | Don't use when...                       |
| ------------------ | --------------------------------------- |
| Content Projection | You need behavior, not structure        |
| Strategy via DI    | Behaviors should be optional/composable |
| Directives         | Same bundle repeated without naming     |
| hostDirectives     | Bundling unrelated things to save typing|

<!--
BEATS:<br>• Inputs = זולים, תבניות חילוץ = יקרות<br>• אל תרוצו לכלי 3 כשיש inputs שעובדים<br>• Content projection: מבנה בלבד<br>• Strategies: לא כשהתנהגויות צריכות להיות אופציונליות<br>• Directives: אל תחזרו על אותו מקבץ — תקדמו אותו (promote)<br>• hostDirectives: לקבץ קונספטים, לא דברים לא קשורים רק כדי לחסוך הקלדה

[19:15 - 19:30]

סיכום מהיר.

מודל מנטלי חשוב: Inputs הם זולים — קל לקרוא ולדבג אותם. תבניות חילוץ הן "יקרות" — יותר קבצים, יותר אינדירקציה, הדיבאגינג קופץ בין גבולות של DI והרכבת דירקטיבות.

אל תרוצו לכלי 3 כשה-inputs עדיין עובדים. מורכבות צריכה להוכיח את הערך שלה.

Content projection: למבנה בלבד, לא להתנהגות.
Strategies: לא כשהתנהגויות צריכות להיות אופציונליות.
Directives: מעולה, אבל אל תחזרו על אותו שילוב בכל הדפים — תנו לו שם.
hostDirectives: אל תקבצו דברים לא קשורים רק כדי לחסוך הקלדה. תקבצו קונספטים ששייכים זה לזה.

[חד — יש להם את הפריימוורק]
-->

---
layout: default
---

# Migration Path

From God Component to clean architecture:

1. **Extract** - Pull behaviors out of the god component
2. **Interface** - Define contracts for swappable behaviors
3. **Compose** - Make remaining behaviors optional with directives
4. **Name** - Bundle common patterns with hostDirectives

<!--
BEATS:<br>• 1. Extract — להוציא התנהגויות החוצה, לא לדאוג לשלמות<br>• 2. Interface — בחירה בין אפשרויות ← strategies<br>• 3. Compose — מה שנשאר ← דירקטיבות אופציונליות<br>• 4. Name — שלוש פעמים ← hostDirectives<br>• התנהגות אחת בכל פעם

[19:30 - 20:15]

אז יש לכם God Component. איך מתקנים אותו?

אחד: Extract. תחלצו התנהגויות החוצה. אל תדאגו לגבי שלמות בגרסה הראשונה. פשוט תוציאו אותן מה-god component.

שתיים: Interface. אילו התנהגויות מוציאות אחת את השנייה? אלו יהיו ה-strategies שלכם.

שלוש: Compose. שאר ההתנהגויות הופכות לדירקטיבות אופציונליות.

ארבע: Name. רואים את זה שלוש פעמים? hostDirectives.

אל תעשו הכל בבת אחת. התנהגות אחת בכל פעם. כל חילוץ הופך את הקומפוננטה לקלה יותר.

[פרקטי — זו עצה ליום ראשון בבוקר]
-->

---
layout: default
---

# The Matrix

| Feature | Main Page | Drawer A | Drawer B |
|---------|-----------|----------|----------|
| Expand/collapse | ✓ | ✓ | ✓ |
| Row styles | ✓ | ✓ | ✓ |
| Context menu | ✓ | ✗ | ✓ |
| Column wizard | ✗ | ✓ | ✗ |
| SSRM fetching | ✗ | ✗ | ✓ |

**Baseline** = always there. **The rest** = opt-in.

<!--
BEATS:<br>• זוכרים את ה-grid? הנה איך תיקנו אותו<br>• בנינו מטריצה — פיצ'ר x שימוש<br>• מצאנו את ה-baseline, השאר הפך ל-Strategy או Directive<br>• Payoff להרמת הידיים

[20:15 - 21:45]

זוכרים את ה-grid מההתחלה? הרמת ידיים — מי עבד על קומפוננטה כזו?

[אותן ידיים כמו קודם]

הנה איך תיקנו אותו.

בנינו מטריצה. כל פיצ'ר, כל מקום שבו השתמשנו ב-grid.

Expand/collapse? בכל מקום. Row styles? בכל מקום. זה ה-baseline — נשאר בתוך הקומפוננטה.

Context menu? בחלק מהמקומות — דירקטיבה. Column wizard? מקום אחד — דירקטיבה. SSRM fetching? רק קונטקסט אחד — strategy.

ה-drawer השני? אותו grid בדיוק, פשוט עם דירקטיבות אחרות. ה-drawer השלישי? אותו grid, עם SSRM strategy מסופק ב-DI.

הפריימוורק הזה הוא לא תיאוריה. זה מה ששיפפנו באמת. ה-grid עובד עכשיו.

[Payoff — להתחבר חזרה להרמת הידיים מהפתיחה]
-->

---
layout: image-right
---

::left::

# Decision Framework

Inputs are your default. When they fail:

::default::

| The Tell           | Pattern            |
| ------------------ | ------------------ |
| Structural flags   | Content Projection |
| Behavioral bundles | Strategy via DI    |
| Composable opt-ins | Directives         |
| Same combo 3x      | hostDirectives     |

<!--
BEATS:<br>• Inputs = ברירת מחדל, צימוד גלוי = ניתן לניהול<br>• Structural flags → Content Projection (חילוץ @if)<br>• Behavioral bundles → Strategy via DI (ה-injector מחליט)<br>• Composable opt-ins → Directives (גלוי בטמפלט)<br>• 3 פעמים אותה משמעות → hostDirectives<br>• העבודה שלכם: לזהות את ה-tell, לבחור את תבנית החילוץ

[21:45 - 22:30]

הנה הכל ביחד.

Inputs הם ברירת המחדל שלכם. תישארו שם כל עוד אתם יכולים. הם יוצרים צימוד גלוי — וזה משהו שאפשר לנהל.

אבל כשאתם רואים את ה-tells האלה, תשלפו את תבנית החילוץ המתאימה:

Structural flags — בוליאנים ששולטים באיזה DOM קיים? Content projection. תוציאו את ה-@if.

Behavioral bundles — פלאג שסוחב איתו סרוויסים וקוד lifecycle? Strategy via DI. תנו ל-injector להחליט.

Composable opt-ins — התנהגויות שצריכות להיערם, קיימות או לא? Directives. גלוי בטמפלט.

אותו שילוב שלוש פעמים, עם אותה משמעות? תנו לזה שם עם hostDirectives.

כל שורה היא תגובה ל-tell. העבודה שלכם: לזהות את ה-tell. לבחור את תבנית החילוץ.

[הפסקה — לתת להם לצלם את זה]
-->

---
layout: section
---

# The Takeaway

Good abstractions aren't chosen.

## They're discovered.

<img src="/assets/qr-slides.png" class="absolute bottom-8 right-8 w-28 opacity-80" />

<!--
BEATS:<br>• התחלנו עם "רק עוד input אחד"<br>• עכשיו יש לכם פריימוורק: זהו את ה-tell, בחרו את תבנית החילוץ<br>• אבסטרקציות טובות לא נבחרות — הן מתגלות (DISCOVERED)<br>• פעם הבאה שתראו isAdmin או showCompactMode — אתם תדעו

[22:30 - 23:15]

מחשבה אחרונה.

זוכרים את ההתחלה — "רק עוד input אחד"? שם כל God Component מתחיל.

עכשיו יש לכם פריימוורק. זהו את ה-tell. בחרו את תבנית החילוץ.

Structural flag? Content projection.
Behavioral bundle? Strategy.
Composable opt-in? Directive.
אותו שילוב שלוש פעמים? תנו לו שם.

אבסטרקציות טובות לא נבחרות בקיק-אוף של הפרויקט. הן מתגלות מתוך הקוד שכבר נמצא שם.

בפעם הבאה שתראו isAdmin, showCompactMode, persistState... אתם תדעו מה הקוד מנסה להגיד לכם.

תקשיבו לו.

[הפסקה — לתת לזה לשקוע]
-->

---
layout: end
---

# Thank You

<div class="flex items-center gap-6 mt-4">
  <img src="/assets/profile.jpg" class="w-20 h-20 rounded-full object-cover" alt="Dor Peled" />
  <div>
    <div class="font-bold text-xl">Dor Peled</div>
    <div class="opacity-80">Software Engineer @ Coralogix</div>
    <div class="flex gap-4 mt-3 text-lg">
      <a href="https://github.com/Knat-Dev" target="_blank" class="opacity-75 hover:opacity-100"><carbon-logo-github /></a>
      <a href="https://linkedin.com/in/dorpeled" target="_blank" class="opacity-75 hover:opacity-100"><carbon-logo-linkedin /></a>
      <a href="https://twitter.com/Knat_Dev" target="_blank" class="opacity-75 hover:opacity-100"><carbon-logo-x /></a>
    </div>
  </div>
</div>

Questions?

<!--
BEATS:<br>• תודה רבה<br>• קומפוננטה שנלחמת בכם? בואו נדבר<br>• שאלות?

[23:15 - 24:15]

זהו זה. תודה רבה.

יש לכם קומפוננטה שנלחמת בכם? בואו תמצאו אותי. או אם סתם בא לכם לדבר על אנגולר, decoupling, למה לקומפוננטה שלכם יש 47 אינפוטים... אני פה.

שאלות?

[פתוח, נגיש — הלמידה האמיתית קורית עכשיו]
-->
