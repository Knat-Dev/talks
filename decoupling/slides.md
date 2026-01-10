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

# Decoupling in Angular - test

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

שלום חברים, אני דור, מהנדס תוכנה ב Coralogix.

בואו נתחיל בשאלה קטנה. מי פה הוסיף פעם "רק עוד input אחד" לקומפוננטה? עוד בוליאן קטן וזהו?

[מחכה לידיים]

כן. גם אני. ככה נולד God Component. לא מתוך כוונה רעה — מדרישות פיצ'ר הגיוניות לגמרי.

היום אנחנו מחפשים רמזים, כמו בפוקר. הרגע הזה שאתם מסתכלים על הקוד והבטן אומרת לכם "משהו פה מסריח", אבל אתם לא יודעים לשים על זה את האצבע.

דוקומנטציה מלמדת סינטקס. ארכיטקטורה לומדים אחרי ששברת דבר או שניים.

בואו נדבר על הקומפוננטה שלימדה אותי כל מה שאני יודע על decoupling.

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

מי שרוצה לעקוב מהנייד או מהלפטופ — תנו סריקה.
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

לפני שנתחיל עם התיאוריה, בואו נראה תכלס מה קרה.

בעבודה בניתי קומפוננטת גריד. בהתחלה זה היה פשוט: fetch בדפדפן, מציג דאטה, הכל טוב.

ואז הגיעו הדרישות. Cell renderers. Row styles. Expand/collapse. ואז — Context menu. ופה זה התחיל להסתבך. כל מי שהשתמש בגריד היה צריך פתאום להביא את כל זה. נוצר צימוד פסיכי.

בדף הראשי? סבבה. ואז שמנו אותו בתוך drawer. בלאגן. פתאום הוא תלוי בסטייט גלובלי, מזריק סרוויסים שלא קשורים לכלום.

ואז עוד drawer... ואותו כאב ראש בדיוק.

[Click for meme]

מי פה תחזק פעם קומפוננטה כזו?

[הפסקה לידיים]

בדיוק. זה אמיתי. אני אראה לכם איך יצאנו מזה. אבל קודם, בואו נבין באילו כלים נשתמש.
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

ה-Grid המקורי גדול מדי — אי אפשר להכניס אותו לסלייד.

אז בניתי ListComponent שעושה בדיוק את אותן טעויות. כתבתי אותו עקום בכוונה.

בואו נפתח אותו ונראה מה קורה בפנים.
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

תראו את הדבר הזה. רשימה שמנסה לעשות הכל.

Inputs לדאטה... סבבה. אבל גם inputs ל-flags של מיון. ל-storage keys. סרוויסים שמוזרקים אבל נוגעים בהם רק במסלולים מסוימים.

תראו את persistState. הוא לא בא לבד. הוא גורר איתו את storageKey, את initialState, ועוד סרוויס. הם באים בחבילה.

זה לגמרי God Component. הוא שואל כל בוקר: "איזה פיצ'רים בא לכם שאני אהיה היום?" במקום פשוט להיות רשימה.

בואו נפרק את זה לגורמים.
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

תקשיבו רגע: צימוד זה לא דבר רע. Coupling זה חלק טבעי מהמערכת. קומפוננטות צריכות לדבר. דאטה עובר, איוונטים עולים. הכל טוב.

הבעיה היא כשהצימוד מוסתר. HIDDEN coupling.

כשאני רואה את ה-dependencies — ב-inputs, ב-constructor — אני יכול לנהל אותם. אני מבין מי נגד מי.

אבל כשזה קבור בתוך boolean flags? מפוזר בתוך ה-template? שם אתם מאבדים שליטה. נוגעים פה, נשבר שם.

אז השאלה היא לא "איך מעיפים את הצימוד?", אלא "איך הופכים אותו לגלוי (VISIBLE)?"

Inputs הם ברירת המחדל שלכם. תישארו שם כמה שאפשר. אבל כשהם כבר לא מספיקים, יש לכם שלושה דפוסי חילוץ. כל אחד חושף סוג אחר של צימוד.

[חד וברור — זה המסר המרכזי]
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

שלושה דפוסים. אתם מכירים את כולם. החכמה היא לדעת מתי לשלוף אותם.

Inputs זה הבסיס. הם מייצרים VISIBLE coupling. תישארו שם כל עוד זה עובד.

אבל שימו לב ל-Tells:

Structural flags — בוליאנים שמשנים את ה-DOM? פה עוברים ל-content projection. תחלצו את ה-@if.

Behavioral bundles — פלאג שסוחב איתו לוגיקה וסרוויסים? Strategy via DI. תנו ל-injector לשבור את הראש.

Composable opt-ins — התנהגויות שאפשר להוסיף אחת על השנייה? Directives. שיהיה גלוי בטמפלט.

אנחנו נעבור על כל אחת. נלמד לזהות את ה-tell.

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

תראו את אלה. sortable, showHeader, persistState.

כל אחד מהם הוא feature toggle. הקומפוננטה שואלת: "מה בא לכם שאני אהיה היום?"

זה ה-Tell. כמו בפוקר. כשיש ערימה של בוליאנים? הקומפוננטה בדיכאון, היא מנסה להיות הכל.

אבל שימו לב — זה רק קצה הקרחון. תסתכלו שוב על ה-God List. רואים ש-storageKey ו-initialState רלוונטיים רק כש-persistState הוא true? הם "מטיילים" ביחד.

נתחיל פשוט. אחד הפלאגים האלה הוא עוף מוזר.

[פאוזה דרמטית]
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

בואו נתחיל עם showHeader. הוא שונה מ-sortable.

הוא לא משנה התנהגות — הוא משנה מבנה. Structure. איזה דברים יושבים ב-DOM.

תראו את זה. showHeader. ובטמפלט? @if שמחליט אם לרנדר Header.

למה שהרשימה תחליט אם יש לה כותרת? זה לא דאטה. זו לא התנהגות. זו אחריות של מי שמשתמש בה (ה-parent).

בונוס: אם ה-parent מזריק את זה פנימה, הרשימה לא צריכה להכיר את ה-Header Component בכלל. ה-imports נקיים יותר.

זה החילוץ הכי קל שיש.

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

תראו מה קורה כשהוצאנו את ה-if החוצה.

לפני: מעבירים true. הקומפוננטה מרנדרת מבפנים.

אחרי: אין בוליאני. ה-parent פשוט... שם את ההדר שם. או שלא.

ה-@if הפך ל-ng-content. הבוליאני התאדה.

Content projection זה לא פיצ'ר עיצובי. זה מה שקורה כשאתם מפסיקים לקבל החלטות מבניות בתוך הקומפוננטה.

[זה רגע ה"אהה!" — תוכן מוזרק הוא פשוט if שהוצאנו החוצה]
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

תשתמשו בזה כשמה שמשתנה זה ה-Structure. מה יושב ב-DOM.

כמו מסגרת לתמונה. המסגרת קובעת את הגודל והצורה. אתם בוחרים את התמונה.

הקומפוננטה אחראית על ה-layout. אתם אחראים על ה-content.

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

שני סלוטים. אחד ל-header, אחד לכל השאר.

הקומפוננטה אומרת: "אני אסגור לכם את הפינה של המעטפת (wrapper). שימו בפנים מה שבא לכם."

Cards, Modals, Panels. המעטפת קבועה, התוכן משתנה.

[מהר — הקוד ברור]
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

יפה. showHeader טופל. פחות אחד.

אבל מה עם persistState? והחברים שלו — storageKey, initialState, והסרוויסים?

מישהו פה ניסה פעם לעשות ng-content ל-HttpClient? להזריק localStorage לתוך סלוט ב-DOM?

[חיוך לקהל]

לא עובד. Content projection זה לדברים ויזואליים. ל-DOM.

כשמה שמשתנה זה "מה קורה" (Behavior) ולא "מה מופיע" (Structure) — אנחנו צריכים כלי אחר.

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

או A או B. תבחרו אחד. בחיים לא שניהם ביחד.

Server או LocalStorage. פרודקשן או Mock. רק מימוש אחד רץ.

הקומפוננטה לא יודעת מה זה. היא רק מבקשת: "תביאו לי משהו שיודע לשמור". ה-DI מזריק לה את המימוש הנכון.

בלי if-ים בקומפוננטה. ההחלטה קרתה עוד לפני שהיא נוצרה.

בואו נראה קוד.

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

אמרתי לכם שהבוליאנים הם רק קצה הקרחון? הנה הקרחון.

תראו את החלק של ה-persistence.

פלאג אחד — persistState. אבל הוא לא לבד.

storageKey (איפה לשמור), initialState (ממה להתחיל), stateSaved (הודעה שסיימנו).

ואז הסרוויסים. ApiService, StorageService. והלוגיקה ב-ngOnInit.

כל הדבר הזה רלוונטי רק אם persistState הוא true. זו חבילה אחת. Bundle. הם הולכים ביחד.

אי אפשר לעשות לזה content-projection. אי אפשר לדחוף סרוויס ל-HTML.

אז איך מזיזים חבילה שלמה של התנהגות החוצה?

[החשיפה — מכינים את ה-Strategy]
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

כל החבילה הזו — הפלאג, האינפוטים, הסרוויסים — צריכה לעוף מהקומפוננטה.

אבל לאן? תלוי בקונטקסט.

Admin? תשמור לשרת. Public? תשמור ב-localStorage. Preview? אל תשמור כלום.

אותה קומפוננטה בדיוק. התנהגות שונה לגמרי.

פעם היינו מוסיפים עוד if, עוד בוליאני. היום? לקומפוננטה פשוט לא אכפת.

[מכינים את הפתרון]
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

דבר ראשון: Interface. מגדירים "מה" (save ו-load). לא "איך".

InjectionToken זה המפתח שלנו ל-DI.

הערה קטנה: שמתי Promise שיהיה קריא בסלייד. Observable זה אותו רעיון בדיוק.

כרגע יש לנו רק חוזה. אין מימוש.

[קצר ולעניין]
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

שומר בדפדפן. עובד Offline. פשוט.

[נקסט]
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

פונה ל-API. מסנכרן בין מכשירים.

מבחוץ? זה נראה אותו דבר בדיוק. הרשימה קוראת ל-save. היא לא יודעת שזה הולך לשרת בקצה העולם.

אפשר גם לעשות NoopStrategy שלא עושה כלום (ל-Preview).

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

פה קורה הקסם. ב-Provider.

ה-AdminDashboard אומר: "תחתיי, כל מי שמבקש Storage — מקבל ServerStorageStrategy."

וזה הקטע החזק של Angular — הזרקה היררכית. זה משפיע רק על העץ שמתחת לקומפוננטה הזאת.

אפס if-ים בקומפוננטה.

טיפ של אלופים: תשימו NoopStrategy ברוט של האפליקציה. ככה כברירת מחדל שום דבר לא נשבר, ורק איפה שצריך — דורסים.

חשוב לזכור: Strategy זה למימושים אקסקלוסיביים. או זה או זה. אם אתם רוצים להוסיף התנהגויות (Composition) — לזה יש Directives.

זה Visible Coupling. אני מסתכל על הקוד ומבין בדיוק מה קורה.

[להדגיש: scoped providers, אפס if-statements]
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

רגע, יש פה בעיה.

Strategy זה מעולה כשאנחנו מחליפים מימוש (Swap). אבל מה אם המיון הוא אופציונלי? מה אם הסינון אופציונלי?

פה אנחנו מזריקים את שלושתם. תמיד. אי אפשר להיפטר מהם, רק להחליף אותם.

[Click for meme]

אפשר להפוך את ההזרקה ל-Optional, אבל אז כל הקוד שלכם מלא ב-null checks. "אם יש sorter אז תמיין...". סיוט.

אנחנו צריכים משהו שמוסיף התנהגות (Add), לא מחליף אותה. ושאפשר לראות את זה בטמפלט בעיניים.

זה ה-Tell השלישי. Composable opt-ins.
Strategy מחליף. Directive מוסיף.

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

בואו נחזור לרשימה. את ה-Header העפנו עם Projection. את ה-Persistence העפנו עם Strategy.

מה נשאר? sortable.

זה לא משנה מבנה. זו לא חבילה של סרוויסים. זו פשוט... התנהגות. או שהרשימה ממויינת, או שלא.

בלי "אולי", בלי תנאים. יש או אין.

זו דירקטיבה. חתיכת התנהגות קטנה שאפשר להדביק איפה שרוצים.

זה לא "עוד כלי". זה מה שנשאר כשמנקים את כל השאר.

[הגדרה מחדש — זה הזיקוק של ההתנהגות]
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

תראו את ה-Selector: `app-list[sortable]`.

בלי האטריביוט? הדירקטיבה לא קיימת. כמעט אפס overhead.

הדירקטיבה לוקחת בעלות על ה-inputs שלה: sortKey, sortDir. הרשימה רק חושפת signal של displayItems, והדירקטיבה מעדכנת אותו.

כן, היא מזריקה את הקומפוננטה של הרשימה. הצימוד הזה מכוון.

שיניתם sortKey? ה-Effect רץ, הרשימה מתמיינת.

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

הנה Persistable. אותו רעיון.

אבל שימו לב: היא מזריקה את `STORAGE_STRATEGY`. הכלים עובדים ביחד!

ה-Strategy עונה על "איפה שומרים" (שרת/לוקאלי).
ה-Directive עונה על "מתי שומרים" (רק כשיש שינוי).

בפרודקשן הייתי מוסיף פה Debounce, ברור. אבל הפירוק הוא הפואנטה. כל כלי עושה דבר אחד. קל לבדוק, קל לחבר.

[השילוב הוא הכוח]
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

תסתכלו על ה-HTML.

רשימה פשוטה? בלי כלום.
רוצים מיון? תוסיפו `sortable`. הדירקטיבה מביאה איתה את ה-Inputs שלה.
רוצים הכל? שימו את הכל.

הכל גלוי. מפתח חדש פותח את הקובץ ורואה *בדיוק* מה הרשימה הזו עושה. לא צריך לנחש.

הבהרה קטנה, כי מישהו כבר חושב על זה.

הסדר בדרך כלל לא משנה לסינון ולמיון. תסננו ואז תמיינו, או להפך, זה יוצא אותו דבר. לכן אין כאן עדיפות מכוונת.

Persist זה סיפור אחר: הוא לא משנה את הדאטה, הוא מתבונן במצב הסופי.

[זה ה-Payoff של ה-visibility]
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

אבל רגע, תסתכלו על זה.

שלושה דפים שונים. אותו שילוב בדיוק. קופי פייסט.

הצימוד מתחבא בתוך החזרתיות. "אנחנו תמיד שמים את השלושה האלה ביחד".

מה קורה כשרוצים להוסיף רביעי? צריך לרוץ על כל האפליקציה. שכחתם אחד? הכל מתחיל להתפרק. סטייה בין המקומות.

[Click for meme]

שלוש פעמים אותו דבר? זה לא מקרי. זה קונספט שפשוט אין לו עדיין שם.

[מכינים את חוק הקידום]
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

הנה הכלל:

פעם אחת? זה קוד.
פעמיים? צירוף מקרים.
שלוש פעמים? זה קונספט. תנו לו שם.

סייג חשוב: זה חייב להיות מאותה סיבה. אם הסיבות שונות, אל תאחדו סתם — כפילויות שווא.

כשאתם נותנים למשהו שם, הוא הופך לישות. אפשר לתעד אותו, לבדוק אותו, לדבר עליו בדיילי.

בלי שם? הדברים מתפזרים. ראיתי פעם שלוש רשימות "זהות", שבאחת מהן מישהו כיבה את המיון "זמנית". חצי שנה אחר כך אף אחד לא זכר למה.

תנו לזה שם.

[מסר חזק]
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

באנגולר יש לנו את `hostDirectives`.

`PowerList` מאגדת את כל השלוש. אטריביוט אחד שמביא איתו את כל החבילה.

הקלאס ריק. אין לוגיקה. זו רק קומפוזיציה. נתנו שם לקונספט.

רוצים להוסיף התנהגות רביעית? מקום אחד וסגרנו את הפינה.

[קלות תחזוקה]
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

לפני: רשימת מכולת של אטריביוטים.
אחרי: `powerList`.

[Click for meme]

בקוד זה רץ אותו דבר. בראש? זה עולם אחר.

מישהו חדש מצטרף לצוות, רואה `powerList`, ומבין מיד מה קורה פה. הפכנו רשימה של דברים לקונספט עם משמעות.

[קצר]
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

דפוס אחרון: Coordinator.

הפרודקט מבקש: "תשמור אוטומטית, אבל רק כשזה Dirty, ועם Debounce."

יש לנו דירקטיבה ל-Dirty. יש לנו דירקטיבה ל-Debounce. הן לא מכירות אחת את השנייה.

ה-Coordinator מחבר ביניהן. "כשאתה Dirty, תריץ את זה ב-Debounce."

זה Coupling, אבל הוא מכוון. תנו לו בית. תנו לו שם. תכתבו לו טסט.

[מובן]
-->

---
layout: section
---

# When NOT to Use These

Each tool has limits.

<!--
BEATS:<br>• מעקות בטיחות קצרים — מתי לא להשתמש

[19:00 - 19:15]

מתי לא להשתמש בזה? בואו נשים גבולות גזרה.

[מהר]
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

תזכרו: Inputs זה זול. דפוסים עולים לכם במורכבות.

אל תשלפו תותחים כבדים אם input פשוט סוגר את הפינה. מורכבות צריכה להצדיק את עצמה.

Content Projection? רק למבנה. לא להתנהגות.
Strategy? לא לדברים אופציונליים.
Directives? אל תעשו קופי פייסט, תנו שם.
hostDirectives? לא לזרוק דברים לא קשורים ביחד רק כדי לחסוך הקלדה. תקבצו רק דברים שיש ביניהם קשר אמיתי.

[סיכום מהיר]
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

אז יש לכם God Component ביד. מה עושים מחר בבוקר?

1. **Extract**: תעיפו את הלוגיקה החוצה. לא משנה לאן, העיקר שתצא.
2. **Interface**: מה מחליף את מה? זה Strategy.
3. **Compose**: מה אופציונלי? זה Directive.
4. **Name**: רואים דפוס חוזר? hostDirectives.

לא הכל בבת אחת. קחו התנהגות אחת, תעשו לה Refactor, ותמשיכו.

[פרקטי]
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

זוכרים את ה-Grid מההתחלה?

הנה איך פתרנו את זה. עשינו מטריצה. פיצ'רים מול דפים.

מה שנמצא בכולם? (Expand/Collapse) — נשאר בקומפוננטה. זה הבסיס.
מה שמשתנה? יצא החוצה.

Context Menu? דירקטיבה.
SSRM Fetching? אסטרטגיה.

פתאום אותו Grid עובד בכל מקום, והוא רזה, נקי וקל לתחזוקה. זה לא תיאוריה, זה הקוד שרץ אצלנו בפרודקשן.

[סגירת מעגל]
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

אז הנה הסיכום. תצלמו את זה.

Inputs זה הבית. תישארו שם.

אבל כשזה נשבר, תחפשו את ה-Tell:

בוליאני שמשנה מבנה? **Content Projection**.
חבילת התנהגות? **Strategy**.
פיצ'רים אופציונליים? **Directives**.
קופי-פייסט שלוש פעמים? **hostDirectives**.

הקוד מדבר אליכם. העבודה שלכם היא להקשיב, ולבחור את הכלי הנכון.

[לתת להם לצלם]
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

משפט לסיום.

זוכרים את "רק עוד input אחד"? שם זה מתחיל.

אבסטרקציות טובות לא בוחרים בתחילת הפרויקט מתוך איזה ספר. הן מתגלות. הן מתחבאות בתוך הקוד המלוכלך שלכם, מחכות שתחלצו אותן.

בפעם הבאה שתראו `isAdmin`, `showCompactMode`... אתם כבר תדעו מה לעשות.

תקשיבו לקוד. הוא יגיד לכם מה הוא צריך.

[שקט בקהל]
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

תודה רבה לכולם.

אם יש לכם קומפוננטה שעושה לכם את המוות — בואו נדבר. אני פה.

שאלות?

[פתוח, חיוך]
-->
