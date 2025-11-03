"# Js.p-Blurry-Loading" 


<img width="1868" height="900" alt="image" src="https://github.com/user-attachments/assets/4a6098d5-2f9f-49a5-a93b-7dd28be16409" />
<img width="1718" height="877" alt="image" src="https://github.com/user-attachments/assets/3c12c0a4-896b-4225-987e-e2eced269376" />
<img width="1653" height="831" alt="image" src="https://github.com/user-attachments/assets/935d6122-edec-4233-8206-19aa273de6e2" />
<img width="1662" height="874" alt="image" src="https://github.com/user-attachments/assets/31a5395c-fc83-4cfa-919b-a393628e5573" />







.


מסך טעינה (Loading Screen) שמתחיל מטושטש מאוד ונעשה חד בהדרגה, יחד עם טקסט שעולה מ־0% עד 100%.

הפרויקט מדגים HTML, CSS ו-JavaScript ברמת מתחילים-מתקדמים (DOM, setInterval, מניפולציה של style).

---

## איך זה נראה?

1. הרקע (תמונה מלאה על כל המסך) מתחיל עם טשטוש חזק (blur).
2. יש טקסט לבן גדול במרכז: `0%`.
3. בכל צעד, האחוזים עולים: `1%`, `2%`, ... `100%`.
4. תוך כדי:

   * המספר הופך שקוף (נעלם בהדרגה).
   * התמונה ברקע נהיית חדה (פחות blur).

ב-100% המסך נראה "נחשף" בצורה חלקה.

---

## מה לומדים פה

### 1. בניית שלד דף עם HTML

```html
<section class="bg"></section>
<div class="loading-text">0%</div>
```

* `section.bg` היא התמונה ברקע.
* `div.loading-text` הוא הטקסט של האחוזים.
* בסוף ה-HTML אנחנו מחברים את קובץ ה-JS עם:

```html
<script src="script.js"></script>
```

לומדים: איך ה-JS שולט באלמנטים דרך ה־class שלהם.

---

### 2. עיצוב דף ברמה של "Landing Screen" עם CSS

דברים חשובים שנלמדים ב-CSS כאן:

#### מיקום במרכז המסך

```css
body {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
}
```

* שימוש ב-`flexbox` כדי למרכז את הטקסט גם אנכית וגם אופקית.
* `100vh` = כל גובה החלון.

#### רקע שפורש את כל המסך

```css
.bg {
  background: url('...') no-repeat center center/cover;
  position: absolute;
  top: -30px;
  left: -30px;
  width: calc(100vw + 60px);
  height: calc(100vh + 60px);
  filter: blur(0px);
  z-index: -1;
}
```

מה לומדים כאן:

* איך לשים תמונת רקע על כל המסך.
* איך להשתמש ב-`position: absolute` כדי לשים אותה מאחורי הטקסט.
* למה `z-index: -1` → התמונה הולכת מאחורי הטקסט.
* מה זה `filter: blur(...)` ואיך טשטוש ויזואלי עובד ב-CSS.

בנוסף: שימוש ב־Google Fonts (`Ubuntu`) עם `@import`.

---

### 3. בחירת אלמנטים מהדף (DOM Selection)

ב-JavaScript:

```js
const loadText = document.querySelector('.loading-text')
const bg = document.querySelector('.bg')
```

מה לומדים:

* `document.querySelector` מחזיר אלמנט לפי סלקטור CSS.
* שומרים אותם במשתנים כדי שנוכל לשנות אותם כל רגע.

---

### 4. setInterval ולוגיקת טיימר

```js
let load = 0
let int = setInterval(blurring, 30)
```

מה קורה פה:

* `load` מתחיל מ־0 (אחוז הטעינה).
* `setInterval(blurring, 30)` אומר:
  "הרץ את הפונקציה `blurring` כל 30 מילישניות".

זו דרך ליצור אנימציה בלי CSS Animation:
אנחנו מעדכנים את המסך שוב ושוב באמצעות JS.

כמו לולאה בזמן אמת.

---

### 5. עצירת האנימציה (clearInterval)

```js
function blurring() {
  load++

  if (load > 99) {
    clearInterval(int)
  }

  ...
}
```

מה לומדים פה:

* בכל קריאה של `blurring()` האחוז עולה ב־1.
* ברגע שהגענו ל-100, אנחנו מפסיקים את ה־interval.
* `clearInterval(int)` עוצר את הטיימר.

זה חשוב למניעת ריצה אינסופית.

---

### 6. עדכון טקסט וסטייל ב־Live

```js
loadText.innerText = `${load}%`
loadText.style.opacity = scale(load, 0, 100, 1, 0)
bg.style.filter = `blur(${scale(load, 0, 100, 30, 0)}px)`
```

#### מה לומדים פה:

* `innerText` → לשנות טקסט שמופיע על המסך.
* `style.opacity` → לשנות שקיפות עם JavaScript.

  * מתחילים ב-1 (אטום) ונגמרים ב-0 (שקוף).
* `style.filter = blur(...)` → לשנות את כמות הטשטוש של התמונה לפי ההתקדמות.

שימו לב:

* בתחילת הטעינה יש טשטוש גדול (30px).
* בסוף הטעינה יש טשטוש 0px (חד לגמרי).

---

### 7. פונקציית scale 

```js
const scale = (num, in_min, in_max, out_min, out_max) => {
  return ((num - in_min) * (out_max - out_min)) / (in_max - in_min) + out_min
}
```

מה הפונקציה עושה:

* "תרגום" מספר מטווח אחד לטווח אחר.

דוגמה:

* יש לי מספר בין 0 ל-100 (האחוזים).
* אני רוצה להפוך אותו למספר בין 30 ל-0 (רמת ה-blur).
* או למספר בין 1 ל-0 (שקיפות).

במקום לעשות חישוב ידני בכל פעם → קוראים ל-scale.

זה קונספט חשוב בממשק משתמש:
לתרגם מדד מתקדם (Progress) לאפקט ויזואלי (Opacity, Blur, זום, צבע וכו').

---



1. **מניפולציה על ה-DOM**
   אנחנו משנים אלמנטים חיים על המסך (טקסט, CSS) בעזרת JavaScript.

2. **אנימציה בלי ספריות**
   אין כאן GSAP, אין Framer Motion – רק `setInterval` ו-CSS `filter`.

3. **Math לפרונטאנד**
   הפונקציה `scale()` מראה איך לוגיקה מתמטית שווה חוויית משתמש חלקה.

4. **שכבות בעמוד**
   שימוש ב-`z-index` ו-`position: absolute` כדי לשים רקע מתחת לטקסט.

5. **UI Loading State**
   זה דוגמה אמיתית למסך טעינה שאפשר לשים לפני אפליקציה עד שהשרת מוכן / נתונים נטענים.

---

## מבנה הקבצים

```text
project-folder/
├─ index.html
├─ style.css
└─ script.js
```

* `index.html` – המבנה של העמוד.
* `style.css` – העיצוב (פונטים, רקע, טשטוש).
* `script.js` – הלוגיקה של האנימציה.

---

## סיכום קצר

:

* איך לבחור אלמנטים עם `querySelector`
* איך לשנות טקסט חי ב-DOM
* איך לשנות CSS דרך JS (`style.opacity`, `style.filter`)
* איך ליצור אנימציה מבוססת זמן עם `setInterval`
* איך לתרגם ערך מטווח אחד לטווח אחר עם פונקציית `scale`
* איך לבנות מסך "Loading" יפה שמרגיש מקצועי

:
JavaScript → CSS → חוויית משתמש.

