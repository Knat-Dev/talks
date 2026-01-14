---
theme: ./theme
title: Decoupling in Angular
info: |
  ## Decoupling in Angular
  Letting the Code Tell You What It Needs

  Dor Peled · @Knat-Dev

  Runtime: ~2,700 words in speaker notes.
  120 wpm (with pauses) = ~22 min.
layout: a-main-cover-2
highlighter: shiki
transition: slide-left
mdc: true
---

# Decoupling in Angular

## Letting the Code Tell You What It Needs

<template #right>
  <img src="/assets/qr-slides.png" class="w-48 rounded-lg shadow-lg border-4 border-white" />
  <div class="text-center mt-4">
    <div class="text-xl font-bold text-white">slides.knat.dev</div>
    <div class="text-white/70 text-sm mt-1">Follow along</div>
  </div>
</template>

<div class="absolute bottom-[80px] left-[80px] flex items-center gap-3">
  <img src="/assets/profile.jpg" class="w-12 h-12 rounded-full object-cover border-2 border-white/50" />
  <div class="text-white/80 text-sm flex items-center gap-1">Dor Peled <span class="opacity-50">·</span> <span class="text-xs opacity-70">@Knat-Dev</span></div>
</div>

<!--
BEATS:<br>• ברכה + הוק<br>• QR code לעקוב<br>• מעבר לאבאוט מי

[0:00 - 0:20]

שלום לכולם!

היום נדבר על איך מזהים שהקוד מתחיל להסתבך — ומה עושים כשהוא נהיה bottleneck.

מי שרוצה לעקוב מהנייד — תנו סריקה ל-QR.

קצת עליי...
-->

---
layout: default
---

<template #title>

# About Me

</template>

<div class="flex items-center gap-12">
  <!-- Avatar -->
  <img src="/assets/profile.jpg" class="w-40 h-40 rounded-full object-cover border-4 shrink-0" style="border-color: var(--cx-green);" alt="Dor Peled" />

  <!-- Name & Role -->
  <div class="shrink-0">
    <div class="font-bold text-2xl text-gray-800 flex items-center gap-2">Dor Peled <span class="text-gray-400 font-normal">·</span> <span class="text-lg font-normal text-gray-500">@Knat-Dev</span></div>
    <div class="text-lg text-gray-600">Software Engineer @ Coralogix</div>
    <div class="text-sm text-gray-500 mt-1">
      Angular, large codebases, and hard trade-offs
    </div>
    <div class="flex gap-4 mt-4 text-lg">
      <a href="https://github.com/Knat-Dev" target="_blank" class="text-gray-600 hover:text-gray-800"><carbon-logo-github /></a>
      <a href="https://www.linkedin.com/in/knat-dev" target="_blank" class="text-gray-600 hover:text-gray-800"><carbon-logo-linkedin /></a>
    </div>
  </div>

  <!-- Outside of work -->
  <div class="flex flex-col gap-3">
    <div class="flex items-center gap-3">
      <span class="text-2xl">🎸</span>
      <span class="text-gray-600">
        Playing guitar ~20 years (metal bands in the past)
      </span>
    </div>
    <div class="flex items-center gap-3">
      <span class="text-2xl">🤘</span>
      <span class="text-gray-600">Metal — taste, not background noise</span>
    </div>
    <div class="flex items-center gap-3">
      <span class="text-2xl">📚</span>
      <span class="text-gray-600">Fantasy & long-form worlds</span>
    </div>
    <div class="flex items-center gap-3">
      <span class="text-2xl">🖥️</span>
      <span class="text-gray-600">Homelab tinkering</span>
    </div>
  </div>
</div>

<!--
BEATS:<br>• היכרות קצרה — דור, Coralogix, Angular בקודבייס גדול<br>• מחוץ לעבודה — גיטרה, מטאל, פנטזיה<br>• הקשר בין מטאל לקוד — טעם, איטרציה, זיהוי של "לא נכון"<br>• מעבר לתוכן

[0:20 - 0:45]

אני דור, מהנדס בקורלוג'יקס.
חיי ונושם קוד, כמעט תמיד עובד על פרוייקט חדש בצד.

מחוץ לעבודה — אני מנגן גיטרה כבר מעל 20 שנה.
ניגנתי בלהקות מטאל, כתבתי והקלטתי מוזיקה.

בואו נצלול.
-->

---
layout: default
---

<template #title>

# Let's Talk Code

</template>

Who here has added _"just one more input"_ to a component?

One more boolean and that's it?

<!--
BEATS:<br>• הרמת ידיים: מי הוסיף "רק עוד input אחד"?<br>• הרמת ידיים: מי חיכה שמישהו אחר יסיים באותו קובץ?<br>• ככה מתחיל Mega Component<br>• מחפשים "tells" — כמו בפוקר

[0:30 - 1:30]

מי פה הוסיף פעם "רק עוד input אחד" לקומפוננטה? עוד בוליאן קטן וזהו? בהרמת יד.

[מחכה לידיים]

ומי מכם פעם חיכה שמישהו אחר יסיים לעבוד על אותו קובץ... כי לא הייתה לכם ברירה?

[מחכה]

כן, גם אני. ככה נולד Mega Component. לא מתוך כוונה רעה — מדרישות פיצ'ר הגיוניות לגמרי.

היום אנחנו מחפשים סימנים מוקדמים. אתם רואים קוד ומרגישים שהוא לא בשליטה, אבל קשה להסביר למה.

בואו נראה את הקומפוננטה שלימדה אותי את כל זה.

[מבט לקהל — קשר עין]
-->

---
layout: default
---

<template #title>

# The Grid

</template>

<div class="grid grid-cols-2 gap-12 mt-8">
<div>

**Started simple...**

```ts
@Component({ selector: 'app-grid' })
export class GridComponent<T> {
  data = input<T[]>([]);
  loading = input(false);
  options = input<GridOptions<T> | null>(null);
}
```

</div>
<div>

**Then requirements came...**

<v-clicks>

- Custom templates
- Row styles
- Expand / Collapse
- Context menu
- Lazy loading
- Persistence
- Global state deps...
- "Make it generic & reusable"

</v-clicks>

</div>
</div>

<img v-click src="/assets/this-is-fine.jpg" class="absolute top-10 right-10 w-96 rounded-lg shadow-xl" />

<!--
[1:30 - 2:30]

לפני שנתחיל עם התיאוריה, בואו נראה תכלס מה קרה.

בעבודה בניתי קומפוננטת Grid. בהתחלה זה היה פשוט, תראו פה משמאל: היו לנו שלושה inputs, data, loading ו-options, וזהו.

ואז הגיעו הדרישות...

[click] Custom templates - תבניות מותאמות לתאים שונים

[click] Row styles - סטיילים לשורות

[click] Expand/Collapse - להרחיב ולכווץ שורות

[click] Context menu - תפריט קונטקסט

[click] Lazy loading - טעינה מדורגת מהשרת

[click] Persistence - שמירת מצב

[click] Global state deps - תלויות בסטייט גלובלי

[click] Make it generic & reusable - צוותים אחרים ראו, רצו, וביקשו "תעשו את זה גנרי לכל החברה"

[click] מי פה תחזק פעם קומפוננטה כזו? אני אגיד לכם מה קרה לנו.
-->

---
layout: default
---

<template #title>

# The Price We Paid

</template>

<div class="grid grid-cols-3 gap-6 mt-8">

<div class="text-center">
<div class="text-4xl mb-3">🧠</div>
<div class="font-bold text-red-400">Cognitive Load</div>
<div class="text-sm text-gray-400 mt-2">Had to hold the entire Grid in your head to make any change</div>
</div>

<div class="text-center">
<div class="text-4xl mb-3">🚧</div>
<div class="font-bold text-red-400">Bottleneck</div>
<div class="text-sm text-gray-400 mt-2">Only 2 people "understood" it. Everyone else was afraid to touch it</div>
</div>

<div class="text-center">
<div class="text-4xl mb-3">🐌</div>
<div class="font-bold text-red-400">Slow Delivery</div>
<div class="text-sm text-gray-400 mt-2">Simple features took weeks. Bugs hid in the complexity</div>
</div>

</div>

<div v-click class="text-center mt-12 text-xl">
  <span class="text-gray-400">Technical debt</span> <span class="text-red-400 font-bold">compounds</span><span class="text-gray-400">. Every shortcut today is a tax on every change tomorrow.</span>
</div>

<!--
[2:30 - 3:30]

הנה מה שזה עלה לנו.

Cognitive Load — כדי לעשות שינוי קטן, היית צריך להחזיק בראש את כל הגריד. 2000 שורות.

Bottleneck — רק שניים מהצוות "הבינו" את הקוד. כל השאר פחדו לגעת.

Slow Delivery — פיצ'רים פשוטים לקחו שבועות. באגים התחבאו במורכבות.

[click]

Technical debt מצטבר. כל קיצור דרך היום הוא מס על כל שינוי מחר.

זה מה שזה עולה.
-->

---
layout: default
---

<template #title>

# The Mega List

</template>

```ts [list.ts]
export class ListComponent {
  items = input<Item[]>([]);
  // ... loading, error, etc.
  sortable = input(false);       // flag
  showHeader = input(false);     // flag
  persistState = input(false);   // flag + friends below

  storageKey = input<string>();  // only if persistState
  #storage = inject(StorageService);
  // ... more services
}
```

_"What features am I today?"_

<!--
BEATS:<br>• הגריד גדול מדי — בניתי גרסה מוקטנת עם אותן טעויות<br>• Inputs למידע, פלאגים לפיצ'רים<br>• Inputs שתלויים אחד בשני, סרוויסים בשימוש חלקי<br>• תבנית ה-Mega Component<br>• שואל "מה אני היום?" במקום פשוט להיות רשימה

[3:30 - 4:15]

הגריד גדול מדי לסלייד, אז בואו נבנה דוגמה קטנה יותר שמראה את אותן בעיות בדיוק.

לפני שנסתכל על הקוד, אני רוצה שתחשבו על הקודבייס שלכם.

תדמיינו את הקומפוננטה ההיא. אתם יודעים על מי אני מדבר.
זו עם 15 inputs.
זו שכל PR נוגע בה.
זו שמפתחים חדשים שואלים "אני באמת צריך לשנות את זה?"

יש לכם אותה בראש?

עכשיו תסתכלו על זה.

Inputs לדאטה... סבבה, אבל גם inputs ל-flags של מיון, ל-storage keys, סרוויסים שמוזרקים אבל נוגעים בהם רק במסלולים מסוימים.

תראו את persistState, הוא לא בא לבד, הוא גורר איתו את storageKey, את initialState, ועוד סרוויס, הם באים בחבילה.

זה לגמרי Mega Component. הוא שואל כל בוקר: "איזה פיצ'רים בא לכם שאני אהיה היום?" במקום פשוט להיות רשימה.

מהרגע הזה מתחילים merge conflicts, פחד לגעת, ומפתחים שמחכים לאותו קובץ.

בואו נפרק את זה לגורמים.
-->

---
layout: default
---

<template #title>

# The Journey

</template>

From Mega Component to clean architecture:

<v-clicks>

1. **Map** - The **WHERE** (Identify context)
2. **Extract** - The **WHAT** (Content Projection)
3. **Interface** - The **HOW** (Strategy via DI)
4. **Compose** - The **WHETHER** (Directives)
5. **Name** - The **NAME** (hostDirectives)

</v-clicks>

<!--
BEATS:<br>• המפה שלנו — חמישה צעדים<br>• Map → Extract → Interface → Compose → Name<br>• נעבור על כל אחד בפירוט

[4:15 - 4:45]

לפני שנצלול, הנה המפה.

חמישה צעדים, מ-Mega Component לארכיטקטורה נקייה:

[click] Map — למפות מה משתנה ואיפה. בלי זה אתם יורים בחושך.

[click] Extract — להוציא התנהגויות החוצה מהקומפוננטה.

[click] Interface — להגדיר חוזים להתנהגויות שמחליפות אחת את השנייה.

[click] Compose — להפוך התנהגויות לאופציונליות עם דירקטיבות.

[click] Name — לקבץ דפוסים חוזרים עם hostDirectives.

זו הדרך שלנו. בואו נתחיל עם Map.

[פאוזה קצרה — לתת להם לספוג את המפה]
-->

---
layout: default
---

<template #title>

# The Map (WHERE)

</template>

| Feature | Main Page | Admin Panel | Preview |
|---------|-----------|-------------|---------|
| items | ✓ | ✓ | ✓ |
| showHeader | ✓ | ✗ | ✓ |
| sortable | ✓ | ✓ | ✗ |
| filterable | ✗ | ✓ | ✓ |
| persistState | ✗ | ✓ | ✗ |

**Baseline** = always there. **The rest** = opt-in.

<!--
BEATS:<br>• הכלי של Map — מטריצת פיצ'ר × קונטקסט<br>• items בכולם = baseline, נשאר בקומפוננטה<br>• showHeader משתנה = Content Projection<br>• persistState רק במקום אחד = Strategy או Directive<br>• עכשיו יודעים מה לחלץ ולאן

[4:45 - 5:15]

הנה הכלי של Map — מטריצה פשוטה.

שורות = פיצ'רים. עמודות = איפה הרשימה בשימוש.

items בכל מקום? זה הבסיס, נשאר בקומפוננטה.

showHeader משתנה בין דפים? זה סימן ל-Content Projection.

persistState רק בדף אחד? זה לא צריך להיות בקומפוננטה בכלל.

עכשיו יש לנו מפה. אנחנו יודעים מה לחלץ.

[פאוזה — לתת להם לספוג]
-->

---
layout: section
---

# The Real Problem

Coupling isn't bad.

## Hidden coupling is.

<!--
BEATS:<br>• צימוד (Coupling) הוא לא רע — צימוד *נסתר* הוא הבעיה<br>• תלויות גלויות (inputs, constructors) = קל לנהל<br>• קבור בתוך פלאגים/טמפלטים = אובדן שליטה<br>• שאלה: איך הופכים coupling לגלוי?<br>• Inputs הם ברירת המחדל, שלוש תבניות חילוץ כשזה נכשל

[5:15 - 5:45]

עכשיו שיש לנו מפה, בואו נדבר על מה אנחנו מחפשים.

צימוד זה לא דבר רע. Coupling זה חלק טבעי מהמערכת. קומפוננטות צריכות לדבר. דאטה עובר, איוונטים עולים. הכל טוב.

הבעיה היא כשהצימוד מוסתר, HIDDEN coupling.

כשאני רואה את ה-dependencies, ב-inputs, ב-constructor, אני יכול לנהל אותם, אני מבין מי נגד מי.

אבל כשזה קבור בתוך boolean flags? מפוזר בתוך ה-template? שם אתם מאבדים שליטה, נוגעים פה, נשבר שם.

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

[5:45 - 6:30]

שלושה דפוסים, אתם מכירים את כולם, החכמה היא לדעת מתי לשלוף אותם.

Inputs זה הבסיס, הם מייצרים VISIBLE coupling, תישארו שם כל עוד זה עובד.

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

<template #title>

# The Tell

</template>

```ts [list.ts] {1-3}
sortable = input(false);
showHeader = input(false);
persistState = input(false);
```

Boolean flags. The component asking _"what features am I?"_

But look closer at **The Mega List**...

<!--
BEATS:<br>• Boolean flags = feature toggles, הקומפוננטה שואלת "מה אני?"<br>• זה ה-tell — כמו בפוקר<br>• אבל האמת עמוקה יותר: חלק מה-inputs "מטיילים" ביחד (storageKey + persistState)<br>• נתחיל פשוט: פלאג אחד שלא דומה לאחרים

[6:30 - 7:15]

תראו את אלה, sortable, showHeader, persistState.

כל אחד מהם הוא feature toggle. הקומפוננטה שואלת: "מה בא לכם שאני אהיה היום?"

זה ה-Tell. כמו בפוקר. כשיש ערימה של בוליאנים הקומפוננטה בדיכאון, היא מנסה להיות הכל.

אבל שימו לב — זה רק קצה הקרחון. תסתכלו שוב על ה-Mega List. רואים ש-storageKey ו-initialState רלוונטיים רק כש-persistState הוא true? הם "מטיילים" ביחד.

נתחיל פשוט. אחד הפלאגים האלה הוא עוף מוזר.

[פאוזה דרמטית]
-->

---
layout: default
---

<template #title>

# The Structural Flag

</template>

```ts [list.ts]
// From The Mega List:
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

הוא לא משנה התנהגות, הוא משנה מבנה — Structure — מה קיים ב-DOM.

תראו את זה. showHeader, ובטמפלט? @if שמחליט אם לרנדר Header.

[פאוזה — מסתכל על הקהל]

עכשיו, מה קורה כשהבקשה הבאה מגיעה?

"תציג אייקון רק בימי חמישי."
"תציג את ההדר רק ללקוחות מארה"ב."
"תציג באנר רק בתקופת מבצעים."

כמה inputs נוסיף?
כמה @ifs עד שזה נשבר?
כמה תנאים עד שנודה שהקומפוננטה הזו יודעת יותר מדי?

[פאוזה — לתת להם להרגיש]

הקומפוננטה שואלת: "איך אני אמור להיראות?"
אבל זו לא העבודה שלה.

למה שהרשימה תחליט אם יש לה כותרת?
זה לא דאטה. זו לא התנהגות. זו אחריות של מי שמשתמש בה.

בואו נחלץ את ה-@if.
-->

---
layout: default
---

<template #title>

# Extract the @if

</template>

```html [before]
<!-- Before: component decides structure -->
<app-list [showHeader]="true" />
```

```html [after]
<!-- After: parent decides structure -->
<app-list>
  <app-list-header header />
</app-list>
```

The **@if** becomes a **slot**. The boolean disappears.

<!--
BEATS:<br>• Before: showHeader=true, קומפוננטה מרנדרת פנימית<br>• After: בלי בוליאני, ה-parent מזריק או לא<br>• @if ← ng-content slot, הבוליאני נעלם<br>• Content projection = חילוץ החלטות מבניות (structural)

[7:45 - 8:15]

תראו מה קורה כשהוצאנו את ה-if החוצה.

לפני: מעבירים true, הקומפוננטה מרנדרת מבפנים.

אחרי: אין בוליאני, ה-parent פשוט... שם את ההדר שם, או שלא.

ה-@if הפך ל-ng-content, הבוליאני התאדה.

Content projection זה לא פיצ'ר עיצובי, זה מה שקורה כשאתם מפסיקים לקבל החלטות מבניות בתוך הקומפוננטה.

[זה רגע ה"אהה!" — תוכן מוזרק הוא פשוט if שהוצאנו החוצה]
-->

---
layout: section
---

<template #title>

# Tool 1: Content Projection

</template>

Separating the <span style="color: var(--cx-green); font-weight: bold;">WHAT</span>

What content appears. Not how it behaves.

<div class="text-sm text-gray-500 mt-8">
  <span class="text-yellow-500">Short term:</span> "Just one more @if"
  <span class="mx-4">→</span>
  <span class="text-red-400">Long term:</span> Template spaghetti, impossible to test
</div>

<!--
BEATS:<br>• כלי 1: Content Projection — מפריד את ה-WHAT<br>• כש-inputs שולטים ב-STRUCTURE (איזה DOM קיים)<br>• Short term: עוד @if אחד. Long term: template spaghetti<br>• הקומפוננטה בבעלות על ה-layout, אתם בבעלות על ה-content

[8:15 - 8:45]

כלי ראשון, Content projection. זה מפריד את ה-WHAT — מה התוכן שמופיע.

מה המחיר אם לא נעשה את זה? בטווח הקצר, "רק עוד @if אחד". בטווח הארוך? Template spaghetti, בלתי אפשרי לבדוק.

כמו מסגרת לתמונה, המסגרת קובעת את הגודל והצורה, אתם בוחרים את התמונה.

הקומפוננטה אחראית על ה-layout, אתם אחראים על ה-content.

[מהר — מכינים את הקוד]
-->

---
layout: default
---

<template #title>

# Content Projection

</template>

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

שני סלוטים, אחד ל-header ואחד לכל השאר.

הקומפוננטה אומרת: "אני אסגור לכם את הפינה של המעטפת (wrapper). שימו בפנים מה שבא לכם."

Cards, Modals, Panels. המעטפת קבועה, התוכן משתנה.

[מהר — הקוד ברור]
-->

---
layout: default
---

<template #title>

# The Next Ceiling

</template>

Content Projection solved **WHAT** appears.

But what about **HOW** it behaves?

- _"Save to localStorage vs server"_
- _"Persist state when flag is true"_

<!--
BEATS:<br>• הצלחנו עם WHAT — עכשיו נחשף ה-HOW<br>• Content projection = אלמנטים ב-DOM בלבד<br>• אי אפשר לעשות ng-content ל-HttpClient<br>• ההצלחה חשפה תקרה חדשה

[9:15 - 9:45]

רגע. עשינו מהלך טוב. הוצאנו החלטה מבנית החוצה, הבוליאני נעלם, ה-parent מחליט.

[פאוזה]

ואז קרה משהו צפוי.
ההצלחה הזו חשפה תקרה חדשה.

אנחנו יודעים עכשיו ש-Content Projection פותר את ה-WHAT — מה מופיע.
אבל מה עם ה-HOW — איך הדברים נעשים?

מישהו פה ניסה פעם לעשות ng-content ל-HttpClient?

[חיוך]

לא עובד. ng-content זה ל-DOM. לא להתנהגות.

אם WHAT פתרנו, עכשיו צריך כלי שמפריד את ה-HOW.
וזה מה ששלח אותנו לחפש את הכלי הבא.
-->

---
layout: default
---

<template #title>

# The Implementation Branch

</template>

```ts [list.ts]
save(state: ListState) {
  if (this.isProd()) {
    this.api.post('/preferences', state);
  } else {
    this.mockApi.save(state); // dev mode
  }
}
```

The component knows **too much** about the "how".

<!--
BEATS:<br>• if-else על מימוש — הקומפוננטה מכירה את כל האופציות<br>• Prod = API אמיתי, Dev = Mock<br>• מה קורה כשמוסיפים אופציה שלישית? רביעית?<br>• זה ה-tell של Strategy

[9:45 - 10:00]

תסתכלו על זה.

if isProd — שמור ל-API אמיתי.
else — שמור ל-Mock.

הקומפוננטה מכירה את כל האופציות. היא יודעת יותר מדי.

מה קורה כשמוסיפים staging? Testing? עוד סביבה?
עוד else-if? ועוד אחד?

זה ה-tell. כשיש לכם if-else על מימושים שונים — זה Strategy.
-->

---
layout: section
---

<template #title>

# Tool 2: Strategy via DI

</template>

Separating the <span style="color: var(--cx-green); font-weight: bold;">HOW</span>

How it's done. A or B, never both.

<div class="text-sm text-gray-500 mt-8">
  <span class="text-yellow-500">Short term:</span> "Just add an if for server mode"
  <span class="mx-4">→</span>
  <span class="text-red-400">Long term:</span> if-forests, untestable without mocks
</div>

<!--
BEATS:<br>• כלי 2: Strategy via DI — מפריד את ה-HOW<br>• או א' או ב', לעולם לא שניהם<br>• Short term: עוד if לשרת. Long term: יערות של if-ים<br>• הקומפוננטה לא יודעת מה היא קיבלה — ה-DI מספק<br>• אפס if-statements, ההחלטה חיה במקום אחר

[10:00 - 10:30]

כלי שני, Strategy via DI. זה מפריד את ה-HOW — איך הדברים נעשים.

מה המחיר? בטווח הקצר, "רק עוד if לשרת". בטווח הארוך? יערות של if-ים, אי אפשר לבדוק בלי mocks מורכבים.

Content Projection הפריד את ה-WHAT — מה מופיע. Strategy מפריד את ה-HOW — איך זה עובד.

או A או B, תבחרו אחד, בחיים לא שניהם ביחד.

Server או LocalStorage, פרודקשן או Mock, רק מימוש אחד רץ.

הקומפוננטה לא יודעת מה זה. היא רק מבקשת: "תביאו לי משהו שיודע לשמור". ה-DI מזריק לה את המימוש הנכון.

בלי if-ים בקומפוננטה. ההחלטה קרתה עוד לפני שהיא נוצרה.

בואו נראה קוד.

[מכינים את הדוגמה]
-->

---
layout: image-right
---

::left::

# Strategy: The Problem

That **if-else** needs to disappear. But where does the decision go?

::default::

| Context     | Storage    |
| ----------- | ---------- |
| Production  | Real API   |
| Development | Mock API   |
| Testing     | In-memory  |

<!--
BEATS:<br>• ה-if-else צריך להיעלם<br>• ה"לאן" תלוי בקונטקסט: Production=API אמיתי, Dev=Mock, Testing=In-memory<br>• אותה קומפוננטה, התנהגות שונה<br>• פעם: עוד בוליאנים. היום: לקומפוננטה לא אכפת

[10:30 - 10:45]

ה-if-else הזה צריך להיעלם מהקומפוננטה.

אבל לאן? תלוי בקונטקסט.

Production? API אמיתי. Development? Mock. Testing? In-memory.

אותה קומפוננטה בדיוק, התנהגות שונה לגמרי.

פעם היינו מוסיפים עוד if, עוד בוליאני, היום? לקומפוננטה פשוט לא אכפת.

[מכינים את הפתרון]
-->

---
layout: default
---

<template #title>

# Strategy: The Interface

</template>

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

[10:45 - 11:00]

דבר ראשון, Interface, מגדירים "מה", save ו-load, לא "איך".

InjectionToken זה המפתח שלנו ל-DI.

הערה קטנה, שמתי Promise שיהיה קריא בסלייד, Observable זה אותו רעיון בדיוק.

כרגע יש לנו רק חוזה, אין מימוש.

[קצר ולעניין]
-->

---
layout: default
---

<template #title>

# Strategy: Two Implementations

</template>

<div class="grid grid-cols-2 gap-4">
<div>

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

</div>
<div>

```ts [server-storage.ts]
export class ServerStorageStrategy
  implements StorageStrategy
{
  #api = inject(ApiService);

  async save(key: string, data: unknown) {
    await firstValueFrom(this.#api.post(...));
  }
  async load<T>(key: string) {
    return firstValueFrom(this.#api.get(...));
  }
}
```

</div>
</div>

Same interface. Different "how".

<!--
BEATS:<br>• שני מימושים — localStorage ו-Server<br>• אותו ממשק, "איך" שונה<br>• הרשימה לא יודעת מה היא קיבלה

[11:00 - 11:30]

שני מימושים. משמאל localStorage — שומר בדפדפן, עובד Offline.

מימין Server — פונה ל-API, מסנכרן בין מכשירים.

אותו ממשק בדיוק. הרשימה קוראת ל-save, היא לא יודעת לאן זה הולך.

[ממשיכים]
-->

---
layout: default
---

<template #title>

# Strategy: The Provider

</template>

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

[11:30 - 12:15]

פה ההחלטה עוברת ל-provider.

ה-AdminDashboard אומר: "תחתיי, כל מי שמבקש Storage — מקבל ServerStorageStrategy."

וזה הקטע החזק של Angular, הזרקה היררכית, זה משפיע רק על העץ שמתחת לקומפוננטה הזאת.

אפס if-ים בקומפוננטה.

טיפ של אלופים, תשימו NoopStrategy ברוט של האפליקציה, ככה כברירת מחדל שום דבר לא נשבר, ורק איפה שצריך, דורסים.

חשוב לזכור, Strategy זה למימושים אקסקלוסיביים, או זה או זה, אם אתם רוצים להוסיף התנהגויות, Composition, לזה יש Directives.

זה Visible Coupling, אני מסתכל על הקוד ומבין בדיוק מה קורה.

[להדגיש: scoped providers, אפס if-statements]
-->

---
layout: default
---

<template #title>

# The Next Ceiling

</template>

```ts [list.ts]
export class ListComponent {
  #storage = inject(STORAGE_STRATEGY);
  #sorter = inject(SORT_STRATEGY);
  #filter = inject(FILTER_STRATEGY);
  // ... and 5 more tokens
}
```

**Too many tokens.** Strategy solved **HOW**. But what about **WHETHER**?

<v-click>
<img src="/assets/one-does-not-simply.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
BEATS:<br>• Strategy עבד — עכשיו נחשפה בעיה חדשה<br>• יותר מדי tokens מוזרקים תמיד<br>• אי אפשר לראות בטמפלט מה דלוק<br>• Strategy מחליף, Directives מוסיפים

[12:15 - 13:00]

Strategy עבד. ה-HOW יצא מהקומפוננטה. אפס if-ים.

[פאוזה]

אבל ההצלחה הזו חשפה משהו חדש.

תסתכלו על הקוד הזה. שלושה tokens, ועוד חמישה שלא נכנסו לסלייד.
הקומפוננטה מזריקה את כולם. תמיד. גם כשלא צריך.

מה אם המיון הוא אופציונלי? מה אם הסינון לא רלוונטי בכלל?
הקומפוננטה עדיין סוחבת את כל ה-tokens האלה.

[Click for meme]

וזה עוד לא הבעיה העיקרית.
הבעיה היא שאי אפשר לראות בטמפלט מה דלוק.
מפתח חדש פותח את הקובץ — ואין לו מושג מה הרשימה הזו עושה בלי לקרוא קוד.

Strategy פתר את ה-HOW.
עכשיו צריך כלי שפותר את ה-WHETHER — ושאפשר לראות אותו בעיניים.
-->

---
layout: default
---

<template #title>

# The Hidden Behavior

</template>

```ts [list.ts]
persistState = input(false);
#storage = inject(STORAGE_STRATEGY); // always injected!

ngOnInit() {
  if (this.persistState()) {
    this.#storage.save(this.storageKey(), this.state());
  }
}
```

Service injected even when not needed. Invisible from outside.

<!--
BEATS:<br>• persistState עם service מוזרק תמיד<br>• אי אפשר לראות מבחוץ אם הרשימה נשמרת<br>• הפלאג שולט בהתנהגות פנימית, אבל ה-service תמיד שם<br>• זה ה-tell של Directives

[13:00 - 13:15]

תסתכלו על persistState.

הפלאג קיים, והלוגיקה קבורה בתוך ngOnInit.

אבל יותר גרוע — ה-STORAGE_STRATEGY מוזרק תמיד. גם כש-persistState הוא false.

מבחוץ? אי אפשר לדעת שהרשימה הזו נשמרת בלי לקרוא את הקוד.

זה ה-tell. כשיש לכם התנהגות מוסתרת מאחורי פלאג — זו דירקטיבה שמחכה לצאת.
-->

---
layout: section
---

<template #title>

# Tool 3: Directives

</template>

Separating the <span style="color: var(--cx-green); font-weight: bold;">WHETHER</span>

Is it on or off? Composable opt-ins.

<div class="text-sm text-gray-500 mt-8">
  <span class="text-yellow-500">Short term:</span> "Just inject it everywhere"
  <span class="mx-4">→</span>
  <span class="text-red-400">Long term:</span> Hidden features, null-check hell
</div>

<!--
BEATS:<br>• כלי 3: Directives — מפריד את ה-WHETHER<br>• showHeader ← projection (WHAT). Strategy נתן לנו HOW. מה נשאר? persistable (WHETHER)<br>• Short term: נזריק בכל מקום. Long term: פיצ'רים נסתרים, null-check hell<br>• Directive = היחידה הכי קטנה ששרדה את החילוץ + מביאה את ה-dependencies שלה

[13:15 - 14:00]

כלי שלישי, Directives. זה מפריד את ה-WHETHER — האם ההתנהגות קיימת בכלל.

מה המחיר? בטווח הקצר, "נזריק את זה בכל מקום". בטווח הארוך? פיצ'רים נסתרים שאי אפשר לראות בטמפלט, null-check hell.

בואו נסכם: Content Projection מפריד את ה-WHAT — מה התוכן. Strategy מפריד את ה-HOW — איך השמירה עובדת.

אבל רגע — Strategy נתן לנו את ה-HOW. הוא לא פתר את ה-WHETHER.

הרשימה עדיין מזריקה את STORAGE_STRATEGY תמיד, גם כשלא צריך אותו.

Directives פותרים את זה. ההזרקה עוברת לדירקטיבה.
אין דירקטיבה? אין הזרקה.

בלי "אולי", בלי תנאים, יש או אין — WHETHER.

זו דירקטיבה, חתיכת התנהגות קטנה שמביאה איתה את ה-dependencies שלה.

[הגדרה מחדש — WHAT/HOW/WHETHER]
-->

---
layout: default
---

<template #title>

# Directive: Persistable

</template>

```ts [persistable.ts]
@Directive({ selector: 'app-list[persistable]' })
export class Persistable {
  #list = inject(ListComponent);
  #storage = inject(STORAGE_STRATEGY);
  storageKey = input.required<string>();

  constructor() {
    effect(() => {
      const state = this.#list.state();
      this.#storage.save(this.storageKey(), state);
    });
  }
}
```

Directive owns persistence. List doesn't know it's being saved.

<!--
BEATS:<br>• סלקטור: app-list[persistable] — בלי האטריביוט = לא קיים<br>• הדירקטיבה היא הבעלים של ה-storageKey<br>• הדירקטיבה מזריקה את ה-Strategy — לא הרשימה<br>• הסלקטור הופך את הצימוד לגלוי ומפורש

[14:00 - 14:30]

תראו את ה-Selector: `app-list[persistable]`.

בלי האטריביוט? הדירקטיבה לא קיימת. אפס overhead.

וזה הקטע החזק — תזכרו את הבעיה? הרשימה הזריקה STORAGE_STRATEGY גם כשלא היה צריך?

עכשיו הדירקטיבה מזריקה את ה-Strategy, לא הרשימה.
הרשימה לא יודעת בכלל שמישהו שומר אותה.

הדירקטיבה מחזיקה את storageKey, מאזינה לשינויים ב-state, ושומרת.

ה-effect ננקה אוטומטית ב-destroy של הדירקטיבה.

[מהר — להראות את הדפוס]
-->

---
layout: default
---

<template #title>

# Directives: Usage

</template>

```html [app.html]
<!-- Simple -->
<app-list [items]="data" />

<!-- With persistence -->
<app-list persistable storageKey="admin-list" [items]="data" />

<!-- Full-featured -->
<app-list sortable sortKey="date" sortDir="desc"
          filterable persistable storageKey="main" [items]="data" />
```

**Visible in the template.** Look at the HTML, know what it does.

<!--
BEATS:<br>• פשוט: בלי דירקטיבות. עם persistence: persistable + storageKey. מלא: כל השלוש<br>• מפתח: גלוי בטמפלט — תסתכלו על ה-HTML, תדעו מה הוא עושה<br>• כל דף בוחר את השילוב שלו — מורכבות בבחירה (opt-in)

[14:30 - 15:00]

תסתכלו על ה-HTML.

רשימה פשוטה? בלי כלום. אפס injections מיותרים.
רוצים persistence? תוסיפו `persistable`. הדירקטיבה מביאה איתה את ה-storageKey.

וזה הקטע — ה-STORAGE_STRATEGY מוזרק רק כשיש persistable.
הרשימה הפשוטה? בלי storage service. בלי null checks.

הכל גלוי, מפתח חדש פותח את הקובץ ורואה *בדיוק* מה הרשימה הזו עושה.

כל דירקטיבה עצמאית — אפשר לשלב אותן בכל סדר, כל דף בוחר את מה שהוא צריך.

[זה ה-Payoff של ה-visibility]
-->

---
layout: default
---

<template #title>

# The Sign

</template>

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

[15:00 - 15:45]

אבל רגע, תסתכלו על זה.

שלושה דפים שונים, אותו שילוב בדיוק, קופי פייסט.

הצימוד מתחבא בתוך החזרתיות, "אנחנו תמיד שמים את השלושה האלה ביחד".

מה קורה כשרוצים להוסיף רביעי? צריך לרוץ על כל האפליקציה, שכחתם אחד? הכל מתחיל להתפרק, סטייה בין המקומות.

[Click for meme]

שלוש פעמים אותו דבר? זה לא מקרי, זה קונספט שפשוט אין לו עדיין שם.

[מכינים את חוק הקידום]
-->

---
layout: section
---

<template #title>

# The Promotion Rule

</template>

Two times is coincidence.

**Three times is a concept.**

## Name it.

<!--
BEATS:<br>• פעם 1 = קוד. פעם 2 = מקריות. פעם 3 = קונספט — תנו לו שם<br>• סייג: אותה משמעות, אותה סיבה. סיבות שונות = אל תקבצו<br>• שם = אפשר לדבר על זה, לתעד, לבדוק, לפתח במקום אחד<br>• דפוסים בלי שם נוטים ל-drift (סיפור: הסינון כבוי "זמנית")<br>• 3 פעמים אותה משמעות? תנו לזה שם

[15:45 - 16:30]

הנה הכלל:

פעם אחת? זה קוד.
פעמיים? צירוף מקרים.
שלוש פעמים? זה קונספט. תנו לו שם.

סייג חשוב, זה חייב להיות מאותה סיבה, אם הסיבות שונות, אל תאחדו סתם, כפילויות שווא.

כשאתם נותנים למשהו שם, הוא הופך לישות, אפשר לתעד אותו, לבדוק אותו, לדבר עליו בדיילי.

בלי שם? הדברים מתפזרים, ראיתי פעם שלוש רשימות "זהות", שבאחת מהן מישהו כיבה את המיון "זמנית", חצי שנה אחר כך אף אחד לא זכר למה.

תנו לזה שם.

[מסר חזק]
-->

---
layout: default
---

<template #title>

# hostDirectives

</template>

```ts [power-list.ts]
@Directive({
  selector: 'app-list[powerList]',
  hostDirectives: [
    { directive: Sortable, inputs: ['sortKey', 'sortDir'] },
    { directive: Filterable, inputs: ['filterKey'] },
    Persistable,
  ],
})
export class PowerList {}
```

Forward inputs explicitly. **No magic.**

<!--
BEATS:<br>• hostDirectives — מקבץ דירקטיבות<br>• PowerList: אטריביוט אחד, שלוש התנהגויות<br>• Trade-off: מאבדים visibility בטמפלט, מקבלים קונספט עם שם + מקום אחד לעדכן<br>• Input forwarding מפורש — אין קסם<br>• Persistable בלי forwarding = preset קבוע

[16:30 - 17:00]

באנגולר יש לנו את `hostDirectives`.

`PowerList` מאגדת את כל השלוש. אטריביוט אחד שמביא איתו את כל החבילה.

כן, אנחנו מאבדים את ה-visibility בטמפלט שדיברנו עליה קודם.
אבל מה אנחנו מקבלים בתמורה? קונספט עם שם, ומקום אחד לעדכן את כל המקומות.

הבהרה חשובה — אין פה קסם.

אם אני רוצה להעביר sortKey דרך powerList, אני חייב לחשוף את זה מפורשות.
רואים את inputs: ['sortKey', 'sortDir']? זה ה-forwarding.

Persistable בלי forwarding — זה preset קבוע. אין לו knobs.

זה מכוון. אתם בוחרים מה לחשוף.

רוצים להוסיף התנהגות רביעית? מקום אחד וסגרנו את הפינה.

[קלות תחזוקה]
-->

---
layout: default
---

<template #title>

# Before/After

</template>

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

[17:00 - 17:15]

לפני: רשימת מכולת של אטריביוטים.
אחרי: `powerList`.

[Click for meme]

בקוד זה רץ אותו דבר, בראש? זה עולם אחר.

מישהו חדש מצטרף לצוות, רואה `powerList`, ומבין מיד מה קורה פה. הפכנו רשימה של דברים לקונספט עם משמעות.

[קצר]
-->

---
layout: default
---

<template #title>

# Coordinator Directive

</template>

```ts [auto-saveable.ts]
@Directive({
  selector: 'app-list[autoSaveable]',
  hostDirectives: [DirtyTrackable, Debounceable, Persistable],
})
export class AutoSaveable {
  #dirty = inject(DirtyTrackable);
  #debounce = inject(Debounceable);
  #persist = inject(Persistable);

  #autoSave = effect(() => {
    if (this.#dirty.isDirty()) {
      this.#debounce.run(() => this.#persist.save());
    }
  });
}
```

When A and B **must** work together.

<!--
BEATS:<br>• Coordinator directive — כשדירקטיבות חייבות לעבוד ביחד<br>• DirtyTrackable + Debounceable = עצמאיות<br>• AutoSaveable מתאם: כשזה dirty, תריץ debounce לשמירה<br>• צימוד מכוון? תנו לו בית — עם שם, שאפשר לבדוק

[17:15 - 17:45]

דפוס אחרון, Coordinator.

הפרודקט מבקש: "תשמור אוטומטית, אבל רק כשזה Dirty, ועם Debounce."

יש לנו דירקטיבה ל-Dirty, יש לנו דירקטיבה ל-Debounce, הן לא מכירות אחת את השנייה.

ה-Coordinator מחבר ביניהן. "כשאתה Dirty, תריץ את זה ב-Debounce."

זה Coupling, אבל הוא מכוון, תנו לו בית, תנו לו שם, תכתבו לו טסט.

[מובן]
-->

---
layout: center
---

<div class="text-center">
  <div class="text-gray-400 text-sm uppercase tracking-widest mb-6">The Journey</div>
  <div class="flex justify-center gap-4">
    <div class="px-4 py-2 rounded-full text-sm font-semibold shadow-lg" style="background: var(--cx-green); color: #111827;">
      ✓ Map
    </div>
    <div class="px-4 py-2 rounded-full text-sm font-semibold shadow-lg" style="background: var(--cx-green); color: #111827;">
      ✓ Extract
    </div>
    <div class="px-4 py-2 rounded-full text-sm font-semibold shadow-lg" style="background: var(--cx-green); color: #111827;">
      ✓ Interface
    </div>
    <div class="px-4 py-2 rounded-full text-sm font-semibold shadow-lg" style="background: var(--cx-green); color: #111827;">
      ✓ Compose
    </div>
    <div class="px-4 py-2 rounded-full text-sm font-semibold shadow-lg" style="background: var(--cx-green); color: #111827;">
      ✓ Name
    </div>
  </div>
</div>

<!--
[17:45 - 17:52]

חמישה שלבים, סיימנו את המסע.
-->

---
layout: center
---

<div class="text-center">
  <div class="text-gray-400 text-sm uppercase tracking-widest mb-6">Team Superpowers</div>
  <div class="flex justify-center gap-8 mt-8">
    <div class="text-center">
      <div class="text-2xl mb-2">🎯</div>
      <div class="text-sm text-gray-300">Clear scope</div>
    </div>
    <div class="text-center">
      <div class="text-2xl mb-2">⚡</div>
      <div class="text-sm text-gray-300">Parallel work</div>
    </div>
    <div class="text-center">
      <div class="text-2xl mb-2">🧪</div>
      <div class="text-sm text-gray-300">Testable in isolation</div>
    </div>
    <div class="text-center">
      <div class="text-2xl mb-2">📍</div>
      <div class="text-sm text-gray-300">Single point of change</div>
    </div>
    <div class="text-center">
      <div class="text-2xl mb-2">💬</div>
      <div class="text-sm text-gray-300">Shared vocabulary</div>
    </div>
  </div>
</div>

<!--
[17:52 - 18:00]

כל אחד נתן לנו סופרפאואר אחר:

Clear scope — יודעים בדיוק מה לחלץ לפני שמתחילים.

Parallel work — מפתח אחד על הרשימה, אחר על ההדר, בלי לחכות.

Testable in isolation — כל חלק אפשר לבדוק בנפרד.

Single point of change — רוצים להוסיף התנהגות? מקום אחד.

Shared vocabulary — אומרים "powerList" בדיילי וכולם יודעים.

עכשיו בואו נשים גבולות גזרה.
-->

---
layout: section
---

<template #title>

# When NOT to Use These

</template>

Each tool has limits.

<!--
BEATS:<br>• מעקות בטיחות קצרים — מתי לא להשתמש

[18:00 - 18:15]

מתי לא להשתמש בזה? בואו נשים גבולות גזרה.

[מהר]
-->

---
layout: default
---

<template #title>

# Guardrails

</template>

| Pattern            | Don't use when...                         |
| ------------------ | ----------------------------------------- |
| Content Projection | You need behavior, not structure          |
| Strategy via DI    | Behaviors should be optional/composable   |
| Directives         | Same bundle repeated — name it instead    |
| hostDirectives     | Things are unrelated — don't bundle them  |

<!--
BEATS:<br>• Inputs = זולים, תבניות חילוץ = יקרות<br>• אל תרוצו לכלי כשיש inputs שעובדים<br>• Guardrails — מתי להישאר עם הפשוט

[18:15 - 18:30]

Inputs זה זול. Extraction patterns עולים מורכבות.
תשתמשו רק כשהמחיר כבר שם.

אל תשלפו תותחים כבדים אם input פשוט סוגר את הפינה.

Content Projection? רק למבנה. לא להתנהגות.
Strategy? לא לדברים אופציונליים.
Directives? אל תעשו קופי פייסט, תנו שם.
hostDirectives? תקבצו רק דברים שיש ביניהם קשר אמיתי.

עכשיו שיש לנו את הכלים, בואו נחזור להתחלה.
-->

---
layout: section
---

<template #title>

# The Circle Closes

</template>

Remember the price we paid?

<!--
BEATS:<br>• סגירת מעגל — חוזרים לכאב האנושי<br>• כל בעיה שציינו בהתחלה — עכשיו פתורה<br>• זה לא היה על patterns, זה היה על לשחרר את הצוות

[18:30 - 19:00]

זוכרים את המחיר ששילמנו?

Cognitive Load — עכשיו כל כלי עושה דבר אחד. אפשר להחזיק בראש.

Bottleneck — עכשיו כל אחד יכול לעבוד על הדירקטיבה שלו. אין תור לאותו קובץ.

Slow Delivery — עכשיו שינוי ב-strategy זה קובץ אחד. לא PR שנוגע בכל מקום.

המדד שלי היה פשוט: כמה מקומות צריך לגעת בשביל שינוי קטן. ירד מ"הרבה" ל"אחד".

[פאוזה]

זה לא היה על patterns.
זה היה על לשחרר את הצוות.

לפני שנסיים, הנה המפה שתיקחו הביתה.
-->

---
layout: image-right
---

::left::

# Decision Framework

Inputs are your default. When they fail:

**WHERE, WHAT, HOW, WHETHER, NAME**

::default::

| The Tell           | Pattern            | Separates   |
| ------------------ | ------------------ | ----------- |
| Cross-context drift | Map Feature × Context | The **WHERE** (Map) |
| Structural flags   | Content Projection | The **WHAT**    |
| Behavioral bundles | Strategy via DI    | The **HOW**     |
| Composable opt-ins | Directives         | The **WHETHER** |
| Same combo 3x      | hostDirectives     | The **NAME**    |

<!--
BEATS:<br>• זוכרים את המסע? Map → Extract → Interface → Compose → Name<br>• הנה הסיכום עם ה-Tells והשאלות: WHERE/WHAT/HOW/WHETHER/NAME<br>• Cross-context drift → Map: Feature × Context (WHERE)<br>• Structural flags → Content Projection (WHAT)<br>• Behavioral bundles → Strategy via DI (HOW)<br>• Composable opt-ins → Directives (WHETHER)<br>• 3 פעמים אותה משמעות → hostDirectives (NAME)

[19:00 - 19:45]

זוכרים את המסע? Map, Extract, Interface, Compose, Name.

הנה הסיכום, תצלמו את זה. חמש שאלות: WHERE, WHAT, HOW, WHETHER, NAME.

תסתכלו על השורה הראשונה — Map זה WHERE. איפה הקומפוננטה בשימוש ומה משתנה? המטריצה.

כל השאר זה אותו רעיון: כל כלי עונה על שאלה אחרת.

WHAT — מה התוכן? Content Projection.
HOW — איך זה נעשה? Strategy.
WHETHER — האם זה בכלל קורה? Directives.
NAME — איך קוראים לזה? hostDirectives.

Inputs זה הבית, תישארו שם.

אבל כשזה נשבר, תחפשו את ה-Tell:

קומפוננטה אחת בכמה קונטקסטים מתחילה לסטות? **מטריצה** — זה Map.
בוליאני שמשנה מבנה? **Content Projection** — זה Extract.
חבילת התנהגות? **Strategy** — זה Interface.
פיצ'רים אופציונליים? **Directives** — זה Compose.
קופי-פייסט שלוש פעמים? **hostDirectives** — זה Name.

הקוד מדבר אליכם. העבודה שלכם היא להקשיב, ולבחור את הכלי הנכון.

[לתת להם לצלם]
-->

---
layout: section
---

<template #title>

# The Takeaway

</template>

Good abstractions aren't chosen.

## They're discovered.

<img src="/assets/qr-slides.png" class="absolute bottom-8 right-8 w-28 opacity-80" />

<!--
BEATS:<br>• אבסטרקציות טובות לא נבחרות — הן מתגלות<br>• סגירת מעגל לגריד<br>• אתגר: מחר בבוקר, קומפוננטה אחת, פלאג אחד, שאלה אחת<br>• "שם הגילוי מתחיל"

[19:45 - 20:45]

"אבסטרקציות טובות לא נבחרות. הן מתגלות."

[פאוזה — לתת לזה לנחות]

זוכרים את הגריד מההתחלה?
2000 שורות. שני אנשים שמבינים. כולם מפחדים לגעת.

עכשיו? אותו גריד. רק שעכשיו יש לו גבולות ברורים.
כל פיצ'ר במקום שלו. כל מפתח יכול לגעת בחלק שלו.

זה לא קרה כי קראנו ספר על Design Patterns.
זה קרה כי הקשבנו לקוד, זיהינו את ה-Tells, וחילצנו בזמן.

[פאוזה]

אז הנה האתגר שלי אליכם.

מחר בבוקר, תמצאו קומפוננטה אחת.
תסתכלו על פלאג בוליאני אחד.
ותשאלו: "מי באמת צריך להיות אחראי על זה?"

אל תשנו את הקוד. רק תחשבו על זה.

שם הגילוי מתחיל.

[סיום]
-->

---
layout: end
---

# Thank You

<div class="flex items-center gap-6 mt-4">
  <img src="/assets/profile.jpg" class="w-20 h-20 rounded-full object-cover" alt="Dor Peled" />
  <div class="text-left">
    <div class="font-bold text-xl text-left flex items-center gap-2">Dor Peled <span class="font-normal opacity-50">·</span> <span class="text-base font-normal opacity-70">@Knat-Dev</span></div>
    <div class="opacity-80">Software Engineer @ Coralogix</div>
    <div class="flex gap-4 mt-3 text-lg">
      <a href="https://github.com/Knat-Dev" target="_blank" class="opacity-75 hover:opacity-100"><carbon-logo-github /></a>
      <a href="https://www.linkedin.com/in/knat-dev" target="_blank" class="opacity-75 hover:opacity-100"><carbon-logo-linkedin /></a>
    </div>
  </div>
</div>

<div class="text-left mt-4 text-3xl font-bold">Questions?</div>

<!--
BEATS:<br>• תודה רבה<br>• קומפוננטה שנלחמת בכם? בואו נדבר<br>• שאלות?

[20:45 - 21:45]

תודה רבה לכולם.

אם יש לכם קומפוננטה שעושה לכם את המוות — בואו נדבר. אני פה.

שאלות?

[פתוח, חיוך]
-->
