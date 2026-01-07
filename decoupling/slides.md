---
theme: ./theme
title: Decoupling in Angular
info: |
  ## Decoupling in Angular
  Letting the Code Tell You What It Needs

  Dor Peled · @Knat-Dev
layout: a-main-cover-2
highlighter: shiki
transition: slide-left
mdc: true
# Hebrew branch - speaker notes are RTL (see style.css)
lang: he
---

# Decoupling in Angular

## Letting the Code Tell You What It Needs

**Dor Peled** · @Knat-Dev

<!--
[0:00 - 1:00]

היי לכולם, אני דור. Software engineer ב-Coralogix.

אז ככה, היום אנחנו הולכים לבנות קומפוננטה ביחד. טוב - אני אכתוב את הקוד, אבל אני רוצה שתעזרו לי לזהות כשמשהו לא בסדר.

אתם מכירים את הרגעים האלה שמסתכלים על קוד וחושבים "משהו פה לא מרגיש נכון, אבל אני לא יכול להסביר למה"? זה מה שאנחנו מחפשים. הסימנים.

כולנו מכירים את הכלים - inputs, outputs, content projection, DI, directives. דוקומנטציה מלמדת syntax. כאב מלמד ארכיטקטורה. אני רוצה להראות לכם את הכאב כדי שתזהו אותו לפני שהוא נושך אתכם.

אז צפו בקוד איתי. כשאתם רואים משהו שגורם לכם לאי-נוחות - זה הסימן.

[לסרוק את החדר - לגרום להרגשה שאנחנו עושים את זה ביחד]
-->

---
layout: center
---

<div class="flex flex-col items-center gap-6">
  <img src="/assets/qr-slides.png" class="w-64 rounded-lg shadow-lg" />
  <div class="text-center">
    <div class="text-xl text-gray-500 font-bold">slides.knat.dev</div>
    <div class="text-gray-400 text-sm mt-2">Follow along on your device</div>
  </div>
</div>

<!--
רגע - סרקו את זה אם אתם רוצים לעקוב על הטלפון או הלפטופ.
-->

---
layout: section
---

# About Me

**Dor Peled** · @Knat-Dev

Software Engineer @ Coralogix

_I tend to stay with hard things longer than is comfortable_

<!--
[1:00 - 2:00]

קצת עליי.

אני נוטה להישאר עם בעיות קשות יותר זמן ממה שנוח. כשקוד נהיה מבולגן, אני לא בורח. אני יושב עם זה. מנסה להבין מה באמת קורה.

Angular codebases זה אותו דבר. הם לא נכשלים מיד. הם צוברים complexity בשקט. פיצ'ר אחרי פיצ'ר. ואם לא נשארים איתם מספיק זמן, מפספסים את הסימנים.

אז ההרצאה הזו היא לא best practices. זה pattern recognition. אני אראה לכם מה למדתי לחפש - ואתם תגידו לי אם אתם רואים את זה גם.

[לשמור את זה קצר - 45 שניות]
-->

---
layout: default
---

# Day 1

```ts [list.ts]
@Component({ selector: 'app-list' })
export class ListComponent {
  items = input<Item[]>([]);
  loading = input(false);
}
```

Clean. Simple. Works great.

<!--
[2:00 - 2:45]

יאללה, בואו נבנה משהו. ListComponent. פשוט.

יום ראשון. שני inputs - items, loading. תסתכלו על הקוד הזה. מה אתם חושבים?

נקי, נכון? פשוט. קל ל-test. זה קוד טוב.

וזה באמת קוד טוב. כרגע, זו בדיוק ה-abstraction הנכונה.

תהנו מהרגע הזה. הוא לא יחזיק מעמד.

[הפסקה קצרה - לתת להם להעריך את השקט לפני הסערה]
-->

---
layout: default
---

# Day 60

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

<v-click>
<img src="/assets/this-is-fine.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
[2:45 - 4:00]

יום שישים. Product מגיע. "אפשר להוסיף sorting?" בטח. "אופציה ל-header?" אוקיי. "לזכור state?" סבבה.

עכשיו תסתכלו על הקוד הזה. באמת תסתכלו.

Boolean flags - sortable, showHeader, persistState. Dependent inputs - storageKey, initialState. Output אחד. שני services ש-injected. Lifecycle code.

וזה יום שישים. תדמיינו שישה חודשים. תדמיינו שנה שנייה.

אנחנו לא ירשנו את הבלאגן הזה. אנחנו בנינו אותו. בקשת פיצ'ר הגיונית אחת בכל פעם.

[קליק למימ]

מי עבד על קומפוננטה כזה? תרימו יד.

[לעצור בשביל הידיים - להכיר בהם]

כן. גם אני. מפה אנחנו מתחילים היום. את זה אנחנו הולכים לפרק בצורה שיטתית.

[להגדיר את התזה]
-->

---
layout: section
---

# The Real Problem

Coupling isn't bad.

## Hidden coupling is.

<!--
[4:00 - 5:00]

אז ככה. Coupling זה לא רע. ה-קומפוננטות חייבים לדבר. Data זורם. Events מתפשטים. זה בסדר.

HIDDEN coupling זו הבעיה.

כשאתם יכולים לראות dependencies - ב-inputs, ב-constructors, ב-imports - אתם יכולים לחשוב עליהם. לנהל אותם.

אבל כש-coupling קבור ב-boolean flags? מפוזר על פני templates? אז מאבדים שליטה. משנים משהו פה, משהו נשבר שם.

אז השאלה היא לא "איך אני מסיר coupling?" אלא "איך אני הופך coupling ל-VISIBLE?"

זה מה שאנחנו הולכים לעשות. Inputs זה ה-default שלכם - תישארו שם כמה שאפשר. אבל כש-inputs לא עובדים, יש לכם שלושה escape hatches. כל אחד הופך סוג אחר של coupling ל-explicit.

[ברור ובטוח - זה הרעיון המרכזי]
-->

---
layout: image-right
---

::left::

# Three Escape Hatches

Inputs are your default.

**When they fail, reach for these.**

::default::

| The Tell            | Escape Hatch       |
| ------------------- | ------------------ |
| Structural flags    | Content Projection |
| Behavioral bundles  | Strategy via DI    |
| Composable opt-ins  | Directives         |

<!--
[5:00 - 6:00]

שלושה escape hatches. אתם מכירים את כולם. הטריק הוא לזהות מתי להגיע לכל אחד.

Inputs זה ה-default שלכם. הם יוצרים VISIBLE coupling - אתם יכולים לראות מה קומפוננטה צריך. תישארו שם כמה שאפשר.

אבל שימו לב לסימנים.

Structural flags - booleans ששולטים באיזה DOM קיים? Content projection. לחלץ את ה-@if.

Behavioral bundles - flag שגורר איתו services ו-lifecycle code? Strategy via DI. לתת ל-injector להחליט.

Composable opt-ins - behaviors שצריכים להיערם? Directives. גלויים ב-template.

לכל escape hatch, אני אראה לכם את הקוד, ונזהה את הסימן ביחד.

[להצביע על כל שורה בקצרה - ואז להמשיך]
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

But look closer at our Day 60 code...

<!--
[6:00 - 7:00]

תסתכלו על אלה. Boolean flags. sortable, showHeader, persistState.

כל אחד הוא feature toggle. ה-קומפוננטה שואל: "איזה פיצ'רים אני מריץ היום?"

זה הסימן - כמו בפוקר. כשאתם רואים boolean flags מצטברים, ה-קומפוננטה מנסה להיות יותר מדי דברים.

אבל הנה העניין - ונחזור לזה - boolean flags זה רק פני השטח. תסתכלו אחורה על קוד יום 60. שימו לב איך storageKey ו-initialState רלוונטיים רק כש-persistState הוא true? הם נוסעים ביחד. האמת עמוקה יותר מסתם flags.

בינתיים, בואו נתחיל עם המקרה הכי פשוט. אחד מה-flags האלה לא כמו האחרים.

[לעצור - לשתול את הזרע, לא להסביר עדיין]
-->

---
layout: default
---

# The Structural Flag

```ts [list.ts]
// From our Day 60 component:
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
[7:00 - 7:30]

בואו נתחיל עם showHeader. הזה שונה מ-sortable או persistState.

הוא לא משנה behavior - הוא שולט ב-structure. אילו DOM elements קיימים.

תסתכלו על זה. showHeader. ואיפשהו ב-template, @if שמרנדר header.

למה ה-LIST מחליט אם header קיים? זה לא data. זה לא behavior. זה עניין של ה-parent.

בונוס: אם ה-parent עושה project, ה-list לא צריך לעשות import ל-header קומפוננטה. Dependency graph נקי יותר - ה-list לא צריך לדעת על ה-type של ה-header בכלל.

זו ה-extraction הכי פשוטה שאנחנו יכולים לעשות.

[לתת להם להרגיש את אי-הנוחות]
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
[7:30 - 8:00]

תראו מה קורה כשמחלצים את ה-conditional.

לפני: showHeader שווה true. Component מרנדר את ה-header פנימית.

אחרי: בלי boolean. ה-parent פשוט... שם את ה-header שם. או לא.

בלוק ה-@if הופך ל-ng-content slot. ה-boolean נעלם.

Content projection זה לא "layout feature". זה מה שקורה כשמחלצים החלטות structural מ-קומפוננטות.

[זה רגע ה-"אהה" - content projection זה הסרה של conditionals]
-->

---
layout: section
---

# Tool 1: Content Projection

When **structure** varies

Not behavior. Structure.

<!--
[8:00 - 8:30]

כלי ראשון. Content projection.

ה-escape hatch הראשון שלנו. להשתמש בזה כש-inputs שולטים ב-STRUCTURE - איזה DOM קיים, לא מה הוא עושה.

תחשבו על מסגרת תמונה. המסגרת מחליטה על צורה וגודל. אתם בוחרים את התמונה.

Component מחזיק את ה-layout. אתם מחזיקים את ה-content.

[מהיר - להגדיר את הקוד]
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
[8:30 - 9:00]

שני ng-content slots. Header ו-default.

Component אומר: "אני אטפל ב-wrapper. אתם תחליטו מה נכנס פנימה."

Cards, modals, panels. Shell עקבי, תכנים משתנים.

[מהיר - הקוד מדבר בעד עצמו]
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
[9:00 - 9:30]

אז טיפלנו ב-showHeader. ה-boolean הזה נעלם. אחד למטה.

אבל תסתכלו אחורה על יום 60. מה עם persistState? והחברים שלו - storageKey, initialState, ה-services?

אפשר לעשות ng-content ל-HttpClient? אפשר לשים localStorage ב-slot?

לא. Content projection זה ל-DOM nodes. דברים ויזואליים.

כשה-variation היא על מה שקורה, לא מה שנראה - זה כלי אחר.

[מעבר ל-strategy]
-->

---
layout: section
---

# Tool 2: Strategy via DI

**A** or **B**

Never both.

<!--
[9:30 - 10:00]

כלי שני. Strategy via DI.

A או B. בחרו אחד. אף פעם לא שניהם באותו זמן.

Server או localStorage. Real API או mock. Implementation אחד פעיל.

Component לא יודע איזה. הוא רק אומר "אני צריך משהו שיכול לשמור." DI מספק את הנכון.

בלי if-statements ב-קומפוננטה. ההחלטה חיה במקום אחר.

בואו אראה לכם איך זה נראה.

[להגדיר את הדוגמה]
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
[10:00 - 10:45]

זוכרים שאמרתי קודם שהאמת עמוקה יותר מסתם boolean flags?

זהו. תסתכלו על ה-Day 60 קומפוננטה שלנו שוב - רק החלק של persistence.

Flag אחד - persistState. אבל הוא לא לבד.

storageKey - איפה לשמור. initialState - מה לשחזר. stateSaved - להודיע כשסיים.

ואז ה-services. ApiService לשרת. StorageService לדפדפן. וה-lifecycle code.

כל זה רלוונטי רק אם persistState הוא true. הדברים האלה נוסעים ביחד. הם יחידה התנהגותית.

והנה ה-insight המפתח: אי אפשר לעשות content-project ל-behavior. אי אפשר לשים service ב-ng-content slot.

אז מה עושים כשחבילה שלמה של behavior צריכה לזוז?

[זה ה-reveal - להגדיר את Strategy]
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
[10:45 - 11:15]

כל החבילה הזו - ה-flag, ה-inputs, ה-services, ה-lifecycle code - צריכה לזוז החוצה מה-קומפוננטה.

אבל לאן זה הולך תלוי ב-context.

Admin dashboard? לשמור לשרת. Public view? localStorage. Preview mode? לא לשמור בכלל.

אותו קומפוננטה, behavior שונה של storage.

הדרך הישנה: עוד boolean flag, עוד if-statement. הדרך החדשה: ה-קומפוננטה לא יודע. לא אכפת לו.

[מהיר - להגדיר את הפתרון]
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
[11:15 - 11:30]

שלב ראשון: להגדיר מה אנחנו צריכים. save() ו-load(). לא איך - רק שזה יכול.

InjectionToken הוא ה-lookup key שלנו.

הערה: אני משתמש ב-Promise כדי לשמור על slides נקיים. אפשר למדל את זה גם כ-Observable - אותו pattern, async primitive שונה.

עדיין בלי implementation. רק ה-contract.

[מהיר - קוד setup]
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
[11:30 - 11:45]

Implementation ראשון. LocalStorageStrategy.

Browser storage. סינכרוני. עובד offline.

[מהיר - להמשיך לבא]
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
[11:45 - 12:15]

Implementation שני. ServerStorageStrategy.

API call. מסנכרן בין devices.

מבחוץ? זהה. List קורא ל-save() ו-load(). לא יודע איזה הוא קיבל.

אפשר להוסיף NoopStrategy ל-preview. אותו interface, לא עושה כלום.

[להמשיך לזוז]
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
[12:15 - 13:00]

פה ההחלטה חיה. ב-provider.

AdminDashboard אומר: "ב-subtree שלי, תשתמשו ב-ServerStorageStrategy."

Key insight: ה-provider הזה scoped ל-AdminDashboard קומפוננטה subtree - לא global. זה הקסם של hierarchical injection.

List עושה inject ל-STORAGE_STRATEGY. מקבל את גרסת השרת. אפס if-statements.

דף אחר? מספק LocalStorageStrategy. אותו list, behavior שונה.

Pro tip: לספק NoopStorageStrategy ב-root, לדרוס איפה שצריך. Safe by default, מותאם אישית per context.

```ts
// The third implementation - does nothing, safely
export class NoopStorageStrategy implements StorageStrategy {
  async save() {}
  async load() { return null; }
}
```

ה-list אף פעם לא משתנה. Mode חדש? לכתוב strategy חדש, לספק אותו איפשהו. List לא יודע, לא אכפת לו.

חשוב: Strategy זה ל-implementations שמבטלים זה את זה. אחד פעיל בכל פעם. אם אתם רוצים opt-in feature composition - להוסיף behaviors, לא להחליף אותם - אל תמשיכו להוסיף tokens. לזה יש directives.

זה visible coupling. אתם יכולים לראות בדיוק איזה behavior כל context מקבל.

[להדגיש: scoped providers, אפס if-statements, visible coupling]
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
[13:00 - 13:45]

אבל תסתכלו על הקוד הזה. מה הבעיה?

שלושה strategies שעשו להם inject. אתם יכולים להחליף implementations. אבל ה-feature set בלתי נראה ב-template.

כן, ל-Angular יש optional injection. אבל אז ה-קומפוננטה שלכם מלא בבדיקות `if (this.sorter)`. אתם בודקים אם behaviors קיימים במקום להרכיב אותם.

[קליק למימ]

DI strategy זה לא הביטוי הכי נקי של opt-in composition. אתם מסיימים עם nullable dependencies או no-op defaults, וה-feature set כבר לא גלוי ב-template.

אנחנו צריכים behaviors שמצטברים. להוסיף מה שאתם רוצים, לדלג על מה שלא. גלוי ב-template.

זה כלי שלוש.

[מעבר]
-->

---
layout: section
---

# Tool 3: Directives

What's **left** after the right extractions.

The smallest unit of reusable behavior.

<!--
[13:45 - 14:30]

כלי שלוש. Directives.

תסתכלו אחורה על יום 60. טיפלנו ב-showHeader - זה content projection עכשיו. טיפלנו ב-persistState והחבילה שלו - זה strategy עכשיו.

מה נשאר? sortable.

זה לא שולט ב-structure. זה לא חבילה של dependent inputs ו-services. זה פשוט... behavior. או שה-list הוא sortable, או שלא.

בלי branching. בלי שאלות context. נוכח או נעדר.

זה directive. היחידה הכי קטנה של behavior שניתן לשימוש חוזר ששרדה extraction.

Directives זה לא "עוד כלי אחד." זה מה שנשאר כשעשיתם את ה-extractions האחרים נכון.

[למסגר מחדש - directives הם בלתי נמנעים, לא שרירותיים]
-->

---
layout: default
---

# Directive: Sortable

```ts [sortable.ts]
@Directive({ selector: 'app-list[sortable]' })
export class Sortable {
  #list = inject(ListComponent);

  constructor() {
    effect(() => {
      const items = this.#list.items(); // signal read
      const sorted = [...items].sort(this.#list.compareFn);
      this.#list.displayItems.set(sorted);
    });
  }
}
```

Only activates with the sortable attribute.

<!--
[14:30 - 15:00]

SortableDirective. Selector מכוון ל-app-list עם sortable attribute.

בלי attribute? Directive לא קיים. אפס overhead.

עושה inject ל-קומפוננטה, מוסיף behavior. אותו element כמו app-list → inject(ListComponent) מפענח את ה-host instance.

כן, ה-directive מכיר את ה-internal API של ה-קומפוננטה. זה בסדר - הם צוות. ה-selector `app-list[sortable]` עושה את זה explicit: ה-directive הזה מעוצב ל-קומפוננטה הזה.

הערה: באפליקציה אמיתית, sort state (direction, column) יהיה גם signal - ה-effect מגיב גם לשינויי items וגם לשינויי sort config.

[מהיר - להראות את ה-pattern]
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
[15:00 - 15:30]

PersistDirective. אותו pattern.

אבל שימו לב - הוא עושה inject ל-STORAGE_STRATEGY. זה ה-key insight: אנחנו עושים decouple ל-persistence לשני צירים.

Strategy עונה: איפה ואיך אנחנו שומרים? Server מול localStorage מול noop.
Directive עונה: מתי אנחנו שומרים? בשינויי state, רק כש-dirty.

כן, ה-effect הזה גורם ל-I/O. ב-production אני מעביר את זה דרך debounced queue עם cancellation. אבל נקודת ה-decomposition היא מה שחשוב פה: איפה חי ב-strategy, מתי חי ב-directive.

כלים נערמים ביחד. לכל אחד עבודה אחת. קל לבדוק לבד, קל להרכיב.

[להצביע על strategy injection - זה ה-payoff]
-->

---
layout: default
---

# Directives: Usage

```html [app.html]
<!-- Simple -->
<app-list sortable [items]="data" />

<!-- Full-featured -->
<app-list sortable filterable persistable [items]="data" />
```

Add sortable, filterable, persist as needed.

<!--
[15:30 - 16:00]

ב-template.

דף פשוט? רק sortable. Full-featured? כל השלושה.

כל דף בוחר את השילוב שלו. Opt-in complexity.

[מהיר - ה-template מדבר בעד עצמו]
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
[16:00 - 16:45]

אבל תסתכלו על הקוד הזה. מה אתם רואים?

שלושה דפים. אותם שלושה directives. Copy-paste.

ה-coupling מוסתר בחזרתיות. "אנחנו תמיד משתמשים בשלושת אלה ביחד."

מה קורה כשמוסיפים חמישי? לצוד כל דף. לעדכן את כולם. פספסתם אחד? עכשיו הם נסחפים לכיוונים שונים.

[קליק למימ]

אותו שילוב שלוש פעמים. זה לא צירוף מקרים. זה concept בלי שם.

[להגדיר את כלל ה-promotion]
-->

---
layout: section
---

# The Promotion Rule

Two times is coincidence.

**Three times is a concept.**

## Name it.

<!--
[16:45 - 17:30]

כלל ה-promotion.

פעם אחת: סתם קוד.
פעמיים: אולי צירוף מקרים.
שלוש פעמים: זה concept. תנו לו שם.

הסתייגות חשובה: שלוש פעמים עם אותו משמעות ואותה סיבה. אם הסיבות שונות, אל תאגדו. זה false duplication.

כשנותנים למשהו שם, אפשר לדבר עליו. לתעד אותו. לבדוק אותו. לפתח אותו במקום אחד.

Patterns ללא שם נסחפים. היו לנו שלושה "full-featured lists." לאחד היה filtering מבוטל "זמנית." שישה חודשים אחר כך? אף אחד לא ידע למה.

רואים את זה שלוש פעמים עם אותה משמעות? תנו לו שם.

[להעביר עם ביטחון - זה זכיר]
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
[17:30 - 18:15]

Angular נותן לנו hostDirectives.

PowerListDirective מאגד שלושה אחרים. Attribute אחד, שלושה behaviors.

תסתכלו על ה-body. ריק. בלי logic. רק composition. זה שם ל-pattern.

להוסיף behavior רביעי? קובץ אחד. סיימנו.

[להראות איך naming יוצר maintainability]
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
[18:15 - 18:45]

לפני: שלושה attributes. אחרי: מילה אחת. powerList.

[קליק למימ]

פונקציונלית זהה. קונספטואלית? עולמות נפרדים.

מפתח חדש רואה "powerList" - מבין מיד מה ה-list הזה עושה.

גרסה ראשונה היא רשימה של דברים. שנייה היא concept.

[קצר - ה-slide ברור]
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

<v-click>
<img src="/assets/galaxy-brain.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
[18:45 - 19:30]

עוד pattern אחד. Coordinator directive.

זוכרים את ה-dirty guard ב-Persistable? זו הייתה הגרסה הפשוטה. בקוד אמיתי, אתם גם רוצים debouncing, ביטול של saves שבדרך, וכו'.

Product אומר: "auto-save כש-dirty, אבל עם debounce."

DirtyTrackable ו-Debounceable הם directives עצמאיים. אף אחד לא מכיר את השני.

AutoSaveable מביא את שניהם, מתאם ביניהם. כש-dirty, לעשות debounce ל-save.

[קליק למימ]

Coupling מכוון? תנו לו בית. מקום אחד. עם שם. Testable.

[לשמור את זה קצר - הם מבינים]
-->

---
layout: section
---

# When NOT to Use These

Each tool has limits.

<!--
[19:30 - 19:45]

גארדריילס מהירים. מתי לא להשתמש בכל escape hatch.

[חלק מהיר]
-->

---
layout: default
---

# Anti-Patterns

| Escape Hatch       | Don't use when...                       |
| ------------------ | --------------------------------------- |
| Content Projection | You need behavior, not structure        |
| Strategy via DI    | Behaviors should be optional/composable |
| Directives         | Same bundle repeated without naming     |
| hostDirectives     | Bundling unrelated things to save typing|

<!--
[19:45 - 20:15]

סיכום מהיר.

Mental model חשוב: Inputs הם זולים - קל לקרוא ולדבג. Escape hatches הם יקרים - יותר קבצים, יותר indirection, debugging קופץ בין DI boundaries ו-directive composition.

אל תגיעו לכלי 3 כש-inputs עדיין עובדים. Complexity צריך להרוויח את מקומו.

Content projection: structure בלבד, לא behavior.
Strategies: לא כש-behaviors צריכים להיות optional.
Directives: מצוין, אבל אל תחזרו על אותה חבילה לאורך דפים - לקדם את זה ל-hostDirectives.
hostDirectives: אל תאגדו דברים לא קשורים רק כדי להקליד פחות. לאגד concepts ששייכים ביחד.

[חד - יש להם את ה-framework]
-->

---
layout: default
---

# Testing Impact

```ts [list.spec.ts]
// Inputs: Easy isolation
const fixture = TestBed.createComponent(List);
fixture.componentRef.setInput('data', mockData);

// Strategy: Mock the interface
TestBed.configureTestingModule({
  providers: [
    { provide: STORAGE_STRATEGY, useValue: mockStorage },
  ],
});

// Directives: Test with and without
const withSorting =
  '<app-list sortable [data]="items" />';
const withoutSorting = '<app-list [data]="items" />';
```

<!--
[20:15 - 20:45]

השפעה על testing - הערה מהירה.

Inputs: הכי קל. להגדיר inputs, לבדוק outputs.

Strategies: לעשות mock ל-interface. לבדוק קומפוננטה ו-implementations בנפרד.

Directives: לבדוק base קומפוננטה לבד, כל directive לבד, ואז combinations.

בחירת pattern משפיעה על איך אתם בודקים. Composability מתפרשת גם ל-test suite שלכם.

[קצר - פרקטי אבל לא הפוקוס]
-->

---
layout: default
---

# Migration Path

From Day 60 mess to clean architecture:

1. **Extract** - Pull behaviors out of the god component
2. **Interface** - Define contracts for swappable behaviors
3. **Compose** - Make remaining behaviors optional with directives
4. **Name** - Bundle common patterns with hostDirectives

<!--
[20:45 - 21:30]

אז יש לכם בלאגן של יום 60. איך מתקנים?

אחת: Extract. לשלוף behaviors החוצה. לא לדאוג לשלמות. רק להוציא אותם מה-god קומפוננטה.

שתיים: Interface. אילו behaviors הם mutually exclusive? אלה הופכים ל-strategies.

שלוש: Compose. Behaviors שנשארו הופכים ל-optional directives.

ארבע: Name. רואים את זה שלוש פעמים? hostDirectives.

לא לעשות הכל בבת אחת. Behavior אחד בכל פעם. כל extraction מקלה על ה-קומפוננטה.

[פרקטי - זו עצה ליום ראשון בבוקר]
-->

---
layout: image-right
---

::left::

# Decision Framework

Inputs are your default. When they fail:

::default::

| The Tell           | Escape Hatch       |
| ------------------ | ------------------ |
| Structural flags   | Content Projection |
| Behavioral bundles | Strategy via DI    |
| Composable opt-ins | Directives         |
| Same combo 3x      | hostDirectives     |

<!--
[21:30 - 22:15]

הנה הכל ביחד.

Inputs זה ה-default שלכם. תישארו שם כמה שאפשר. הם יוצרים visible coupling - זה ניתן לניהול.

אבל כשאתם רואים את הסימנים האלה, תגיעו ל-escape hatch:

Structural flags - booleans ששולטים באיזה DOM קיים? Content projection. לחלץ את ה-@if.

Behavioral bundles - flag שגורר services ו-lifecycle code? Strategy via DI. לתת ל-injector להחליט.

Composable opt-ins - behaviors שצריכים להיערם, נוכחים או נעדרים? Directives. גלויים ב-template.

אותו combo שלוש פעמים עם אותה משמעות? לתת לו שם עם hostDirectives.

כל שורה היא תגובה לסימן. התפקיד שלכם: לזהות את הסימן. לבחור את ה-escape hatch.

[לעצור - לתת להם לצלם]
-->

---
layout: section
---

# The Takeaway

Good abstractions aren't chosen.

## They're discovered.

<img src="/assets/qr-slides.png" class="absolute bottom-8 right-8 w-40 opacity-80" />

<!--
[22:15 - 23:00]

מחשבה אחרונה.

אמרתי לכם בהתחלה שאני נשאר עם דברים קשים יותר זמן ממה שנוח. אי-הנוחות הזו היא איפה שה-discovery קורה.

אנחנו מדברים על "לבחור את ה-abstraction הנכונה" כאילו יש תפריט בהתחלת פרויקט. זה לא ככה עובד.

Abstractions טובות לא נבחרות. הן מתגלות.

אתם כותבים קוד. צופים בו מתפתח. שמים לב איפה הוא נאבק. הקוד מספר לכם - דרך boolean flags, חזרתיות, god קומפוננטות - מה הוא צריך.

התפקיד שלכם הוא לא לנבא את העתיד. זה להקשיב להווה. לשים לב לסימנים.

אל תמהרו לתקן את ה-messy קומפוננטה. תקשיבו לו קודם. בפעם הבאה שתראו isAdmin או isCompactMode... תדעו מה הקוד מספר לכם.

תישארו עם ה-קומפוננטות שלכם מספיק זמן. ה-patterns מתגלים מעצמם.

[קשר עין - לתת לזה לנחות]
-->

---
layout: end
---

# Thank You

**Dor Peled** · @Knat-Dev

Software Engineer @ Coralogix

Questions?

<!--
[23:30 - 24:00]

זהו. תודה רבה.

אם יש לכם קומפוננטה שנלחם בכם - תבואו תמצאו אותי. או אם אתם רוצים לדבר על Angular, decoupling, או למה ל-קומפוננטה שלכם יש 47 inputs... אני פה.

שאלות?

[פתוח, נגיש - למידה אמיתית קורה עכשיו]
-->
