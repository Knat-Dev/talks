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

<div class="flex items-center gap-6 mt-8">
  <img src="/assets/profile.jpg" class="w-24 h-24 rounded-full object-cover" alt="Dor Peled" />
  <div>
    <div class="font-bold text-xl">Dor Peled</div>
    <div class="opacity-80">Software Engineer @ Coralogix</div>
    <div class="flex gap-4 mt-3 text-lg">
      <a href="https://github.com/Knat-Dev" target="_blank" class="opacity-75 hover:opacity-100"><carbon-logo-github /></a>
      <a href="https://www.linkedin.com/in/knat-dev" target="_blank" class="opacity-75 hover:opacity-100"><carbon-logo-linkedin /></a>
    </div>
  </div>
</div>

<template #right>
  <img src="/assets/qr-slides.png" class="w-48 rounded-lg shadow-lg border-4 border-white" />
  <div class="text-center mt-4">
    <div class="text-xl font-bold text-white">slides.knat.dev</div>
    <div class="text-white/70 text-sm mt-1">Follow along</div>
  </div>
</template>

<!--
BEATS:<br>• היכרות קצרה — דור, Coralogix, קומפוננטות משותפות<br>• QR code לעקוב<br>• מעבר מהיר לתוכן

[0:00 - 0:30]

שלום, אני דור, מהנדס תוכנה בקורלוג'יקס.
אני עובד על קומפוננטות משותפות ודיזיין סיסטמס.

מי שרוצה לעקוב מהנייד — תנו סריקה.

היום נדבר על איך מזהים שהקוד מתחיל להסתבך — ומה עושים לפני שהוא נהיה bottleneck.

בואו נצלול.
-->

---
layout: default
---

# Let's Talk Code

Who here has added _"just one more input"_ to a component?

One more boolean and that's it?

<!--
BEATS:<br>• הרמת ידיים: מי הוסיף "רק עוד input אחד"?<br>• הרמת ידיים: מי חיכה שמישהו אחר יסיים באותו קובץ?<br>• ככה מתחיל Mega Component<br>• מחפשים "tells" — כמו בפוקר

[0:30 - 1:30]

בואו נדבר קוד.

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

# The Grid

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

- Cell renderers
- Row styles
- Expand / Collapse
- Context menu
- SSRM fetching
- Persistence
- Global state deps...

</v-clicks>

</div>
</div>

<img v-click src="/assets/this-is-fine.jpg" class="absolute bottom-10 right-10 w-64 rounded-lg shadow-xl" />

<!--
[1:30 - 2:30]

לפני שנתחיל עם התיאוריה, בואו נראה תכלס מה קרה.

בעבודה בניתי קומפוננטת Grid. בהתחלה זה היה פשוט, תראו פה משמאל: היו לנו שלושה inputs, data, loading ו-options, וזהו.

ואז הגיעו הדרישות...

[click] Cell renderers - צריך לעצב תאים שונים

[click] Row styles - סטיילים לשורות

[click] Expand/Collapse - להרחיב ולכווץ שורות

[click] Context menu - תפריט קונטקסט

[click] SSRM - Server Side Row Model fetching

[click] Persistence - שמירת מצב

[click] Global state deps - תלויות בסטייט גלובלי

[click] מי פה תחזק פעם קומפוננטה כזו? אני אגיד לכם מה קרה לנו.
-->

---
layout: default
---

# The Price We Paid

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

# The Mega List

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

# The Journey

From Mega Component to clean architecture:

<v-clicks>

1. **Map** - Identify what varies where
2. **Extract** - Pull behaviors out of the component
3. **Interface** - Define contracts for swappable behaviors
4. **Compose** - Make behaviors optional with directives
5. **Name** - Bundle patterns with hostDirectives

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

# The Map

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

# The Tell

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

# The Structural Flag

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

# Tool 1: Content Projection

Separating the **WHAT**

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

שני סלוטים, אחד ל-header ואחד לכל השאר.

הקומפוננטה אומרת: "אני אסגור לכם את הפינה של המעטפת (wrapper). שימו בפנים מה שבא לכם."

Cards, Modals, Panels. המעטפת קבועה, התוכן משתנה.

[מהר — הקוד ברור]
-->

---
layout: default
---

# The Next Ceiling

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

# The Implementation Branch

```ts [list.ts]
save(state: ListState) {
  if (this.isAdmin()) {
    this.api.post('/preferences', state);
  } else {
    localStorage.setItem(this.key, JSON.stringify(state));
  }
}
```

The component knows **too much** about the "how".

<!--
BEATS:<br>• if-else על מימוש — הקומפוננטה מכירה את כל האופציות<br>• Admin = שרת, אחרת = localStorage<br>• מה קורה כשמוסיפים אופציה שלישית? רביעית?<br>• זה ה-tell של Strategy

[9:45 - 10:00]

תסתכלו על זה.

if isAdmin — שמור לשרת.
else — שמור ל-localStorage.

הקומפוננטה מכירה את כל האופציות. היא יודעת יותר מדי.

מה קורה כשמוסיפים cookies? IndexedDB? Session storage?
עוד else-if? ועוד אחד?

זה ה-tell. כשיש לכם if-else על מימושים שונים — זה Strategy.
-->

---
layout: section
---

# Tool 2: Strategy via DI

Separating the **HOW**

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

| Context | Storage      |
| ------- | ------------ |
| Admin   | Server       |
| Public  | localStorage |
| Preview | Don't save   |

<!--
BEATS:<br>• ה-if-else צריך להיעלם<br>• ה"לאן" תלוי בקונטקסט: אדמין=שרת, ציבורי=localStorage, תצוגה מקדימה=noop<br>• אותה קומפוננטה, התנהגות שונה<br>• פעם: עוד בוליאנים. היום: לקומפוננטה לא אכפת

[10:30 - 10:45]

ה-if-else הזה צריך להיעלם מהקומפוננטה.

אבל לאן? תלוי בקונטקסט.

Admin? תשמור לשרת. Public? תשמור ב-localStorage. Preview? אל תשמור כלום.

אותה קומפוננטה בדיוק, התנהגות שונה לגמרי.

פעם היינו מוסיפים עוד if, עוד בוליאני, היום? לקומפוננטה פשוט לא אכפת.

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

# Strategy: Two Implementations

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

# The Next Ceiling

```ts [list.ts]
export class ListComponent {
  #storage = inject(STORAGE_STRATEGY);
  #sorter = inject(SORT_STRATEGY);
  #filter = inject(FILTER_STRATEGY);
}
```

Strategy solved **HOW**. But what about **WHETHER**?

<v-click>
<img src="/assets/one-does-not-simply.jpg" class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-120 rounded-lg shadow-xl" />
</v-click>

<!--
BEATS:<br>• Strategy עבד — עכשיו נחשפה בעיה חדשה<br>• כל שלושת ה-strategies מוזרקים תמיד<br>• אי אפשר לראות בטמפלט מה דלוק<br>• Strategy מחליף, Directives מוסיפים

[12:15 - 13:00]

Strategy עבד. ה-HOW יצא מהקומפוננטה. אפס if-ים.

[פאוזה]

אבל ההצלחה הזו חשפה משהו חדש.

תסתכלו על הקוד הזה. שלושה strategies מוזרקים.
מה אם המיון הוא אופציונלי? מה אם הסינון אופציונלי?

פה אנחנו מזריקים את שלושתם. תמיד.
אי אפשר להיפטר מהם — רק להחליף אותם.

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

# The Hidden Behavior

```ts [list.ts]
sortable = input(false);

ngOnInit() {
  if (this.sortable()) {
    this.items = this.items.sort(/*...*/);
  }
}
```

Behavior buried inside the component. Invisible from outside.

<!--
BEATS:<br>• sortable עם לוגיקה מוסתרת בתוך הקומפוננטה<br>• אי אפשר לראות מבחוץ אם הרשימה ממויינת<br>• הפלאג שולט בהתנהגות פנימית<br>• זה ה-tell של Directives

[13:00 - 13:15]

תסתכלו על sortable.

הפלאג קיים, והלוגיקה קבורה בתוך ngOnInit.

מבחוץ? אי אפשר לדעת שהרשימה הזו ממויינת בלי לקרוא את הקוד.

זה ה-tell. כשיש לכם התנהגות מוסתרת מאחורי פלאג — זו דירקטיבה שמחכה לצאת.
-->

---
layout: section
---

# Tool 3: Directives

Separating the **WHETHER**

Is it on or off? Composable opt-ins.

<div class="text-sm text-gray-500 mt-8">
  <span class="text-yellow-500">Short term:</span> "Just inject it everywhere"
  <span class="mx-4">→</span>
  <span class="text-red-400">Long term:</span> Hidden features, null-check hell
</div>

<!--
BEATS:<br>• כלי 3: Directives — מפריד את ה-WHETHER<br>• showHeader ← projection (WHAT). persistState ← strategy (HOW). מה נשאר? sortable (WHETHER)<br>• Short term: נזריק בכל מקום. Long term: פיצ'רים נסתרים, null-check hell<br>• Directive = היחידה הכי קטנה ששרדה את החילוץ

[13:15 - 14:00]

כלי שלישי, Directives. זה מפריד את ה-WHETHER — האם ההתנהגות קיימת בכלל.

מה המחיר? בטווח הקצר, "נזריק את זה בכל מקום". בטווח הארוך? פיצ'רים נסתרים שאי אפשר לראות בטמפלט, null-check hell.

בואו נסכם: Content Projection מפריד את ה-WHAT — מה התוכן. Strategy מפריד את ה-HOW — איך זה עובד. Directives? מפריד את ה-WHETHER — האם זה בכלל קורה.

בואו נחזור לרשימה, את ה-Header העפנו עם Projection (WHAT), את ה-Persistence העפנו עם Strategy (HOW).

מה נשאר? sortable.

זה לא משנה מבנה, זו לא חבילה של סרוויסים, זו פשוט... התנהגות, או שהרשימה ממויינת, או שלא.

בלי "אולי", בלי תנאים, יש או אין — WHETHER.

זו דירקטיבה, חתיכת התנהגות קטנה שאפשר להדביק איפה שרוצים.

[הגדרה מחדש — WHAT/HOW/WHETHER]
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
      const sorted = [...this.#list.items()].sort(/*...*/);
      this.#list.displayItems.set(sorted);
    });
  }
}
```

Directive owns sort config. List just displays.

<!--
BEATS:<br>• סלקטור: app-list[sortable] — בלי האטריביוט = לא קיים<br>• הדירקטיבה היא הבעלים של ה-inputs (sortKey, sortDir)<br>• הרשימה חושפת displayItems signal — הדירקטיבה כותבת אליו<br>• הסלקטור הופך את הצימוד לגלוי ומפורש

[14:00 - 14:30]

תראו את ה-Selector: `app-list[sortable]`.

בלי האטריביוט? הדירקטיבה לא קיימת, כמעט אפס overhead.

הדירקטיבה לוקחת בעלות על ה-inputs שלה: sortKey, sortDir. הרשימה רק חושפת signal של displayItems, והדירקטיבה מעדכנת אותו.

כן, היא מזריקה את הקומפוננטה של הרשימה, הצימוד הזה מכוון.

שיניתם sortKey? ה-Effect רץ, הרשימה מתמיינת.

ה-effect ננקה אוטומטית ב-destroy של הדירקטיבה.

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
BEATS:<br>• PersistDirective — אותו דפוס<br>• מפתח: מזריק את STORAGE_STRATEGY — הכלים נערמים ביחד!<br>• **רגע ה-Aha** — הכלים לא נפרדים, הם שרשרת<br>• לכל כלי יש תפקיד אחד — קל לבדוק, קל להרכיב (compose)

[14:30 - 15:15]

הנה Persistable.

אבל רגע — תסתכלו על השורה הזו.

inject(STORAGE_STRATEGY)

[פאוזה ארוכה — לתת לזה לשקוע]

פה קורה החיבור.

הדירקטיבה הזו משתמשת ב-Strategy שהגדרנו קודם.
היא לא יודעת אם זה שרת או localStorage — היא רק יודעת לשמור.

[פאוזה]

פה רואים שהכלים מתחברים.
הדירקטיבה תלויה ב-Strategy. זה מאפשר לה להישאר קטנה.

זו לא רשימת טכניקות. זה רצף החלטות.
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

[15:15 - 15:45]

תסתכלו על ה-HTML.

רשימה פשוטה? בלי כלום.
רוצים מיון? תוסיפו `sortable`. הדירקטיבה מביאה איתה את ה-Inputs שלה.
רוצים הכל? שימו את הכל.

הכל גלוי, מפתח חדש פותח את הקובץ ורואה *בדיוק* מה הרשימה הזו עושה, לא צריך לנחש.

כל דירקטיבה עצמאית — אפשר לשלב אותן בכל סדר, כל דף בוחר את מה שהוא צריך.

Persist זה סיפור אחר, הוא לא משנה את הדאטה, הוא מתבונן במצב הסופי.

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

[15:45 - 16:30]

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

# The Promotion Rule

Two times is coincidence.

**Three times is a concept.**

## Name it.

<!--
BEATS:<br>• פעם 1 = קוד. פעם 2 = מקריות. פעם 3 = קונספט — תנו לו שם<br>• סייג: אותה משמעות, אותה סיבה. סיבות שונות = אל תקבצו<br>• שם = אפשר לדבר על זה, לתעד, לבדוק, לפתח במקום אחד<br>• דפוסים בלי שם נוטים ל-drift (סיפור: הסינון כבוי "זמנית")<br>• 3 פעמים אותה משמעות? תנו לזה שם

[16:30 - 17:15]

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

# hostDirectives

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
BEATS:<br>• hostDirectives — מקבץ דירקטיבות<br>• PowerList: אטריביוט אחד, שלוש התנהגויות<br>• Input forwarding מפורש — אין קסם<br>• Persistable בלי forwarding = preset קבוע<br>• רוצים להוסיף התנהגות רביעית? קובץ אחד. וזהו

[17:15 - 17:45]

באנגולר יש לנו את `hostDirectives`.

`PowerList` מאגדת את כל השלוש. אטריביוט אחד שמביא איתו את כל החבילה.

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

[17:45 - 18:00]

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

[18:00 - 18:30]

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
  <div class="mt-8 text-sm font-bold" style="color: var(--cx-green);">Team Superpowers:</div>
  <div class="grid grid-cols-5 gap-4 mt-4 text-xs text-gray-400">
    <div>Clear scope</div>
    <div>Parallel work</div>
    <div>Split ownership</div>
    <div>Visible in template</div>
    <div>Shared vocabulary</div>
  </div>
</div>

<!--
[18:30 - 18:45]

חמישה שלבים, סיימנו את המסע.

כל אחד נתן לנו סופרפאואר אחר:

Map — היקף ברור. יודעים בדיוק מה לחלץ לפני שמתחילים.

Extract — עבודה במקביל. מפתח אחד על הרשימה, אחר על ההדר.

Interface — פיצול בעלות. מי שכותב את ServerStorageStrategy לא צריך להכיר את הקומפוננטה.

Compose — גלוי בטמפלט. מפתח חדש רואה sortable ומבין מיד.

Name — שפה משותפת. אומרים "powerList" בדיילי וכולם יודעים.

עכשיו בואו נשים גבולות גזרה.
-->

---
layout: section
---

# When NOT to Use These

Each tool has limits.

<!--
BEATS:<br>• מעקות בטיחות קצרים — מתי לא להשתמש

[18:45 - 19:00]

מתי לא להשתמש בזה? בואו נשים גבולות גזרה.

[מהר]
-->

---
layout: default
---

# Guardrails

| Pattern            | Don't use when...                         |
| ------------------ | ----------------------------------------- |
| Content Projection | You need behavior, not structure          |
| Strategy via DI    | Behaviors should be optional/composable   |
| Directives         | Same bundle repeated — name it instead    |
| hostDirectives     | Things are unrelated — don't bundle them  |

<!--
BEATS:<br>• Inputs = זולים, תבניות חילוץ = יקרות<br>• אל תרוצו לכלי כשיש inputs שעובדים<br>• Guardrails — מתי להישאר עם הפשוט

[19:00 - 19:15]

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

# The Circle Closes

Remember the price we paid?

<!--
BEATS:<br>• סגירת מעגל — חוזרים לכאב האנושי<br>• כל בעיה שציינו בהתחלה — עכשיו פתורה<br>• זה לא היה על patterns, זה היה על לשחרר את הצוות

[19:15 - 19:45]

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

[19:45 - 20:30]

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

# The Takeaway

Good abstractions aren't chosen.

## They're discovered.

<img src="/assets/qr-slides.png" class="absolute bottom-8 right-8 w-28 opacity-80" />

<!--
BEATS:<br>• אבסטרקציות טובות לא נבחרות — הן מתגלות<br>• סגירת מעגל לגריד<br>• אתגר: מחר בבוקר, קומפוננטה אחת, פלאג אחד, שאלה אחת<br>• "שם הגילוי מתחיל"

[20:30 - 21:30]

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
    <div class="font-bold text-xl text-left">Dor Peled</div>
    <div class="opacity-80">Software Engineer @ Coralogix</div>
    <div class="flex gap-4 mt-3 text-lg">
      <a href="https://github.com/Knat-Dev" target="_blank" class="opacity-75 hover:opacity-100"><carbon-logo-github /></a>
      <a href="https://www.linkedin.com/in/knat-dev" target="_blank" class="opacity-75 hover:opacity-100"><carbon-logo-linkedin /></a>
    </div>
  </div>
</div>

<div class="text-left mt-4">Questions?</div>

<!--
BEATS:<br>• תודה רבה<br>• קומפוננטה שנלחמת בכם? בואו נדבר<br>• שאלות?

[21:30 - 22:30]

תודה רבה לכולם.

אם יש לכם קומפוננטה שעושה לכם את המוות — בואו נדבר. אני פה.

שאלות?

[פתוח, חיוך]
-->
