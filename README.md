<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:0B1220,45:1D4ED8,100:60A5FA&height=190&section=header&text=byteforge&fontSize=60&fontColor=EFF6FF&fontAlignY=34&desc=senior%20software%20%2F%20UI%20engineer%20%C2%B7%20Java%20and%20Python%20and%20React%20and%20TypeScript%20%C2%B7%20canvas%20and%20SVG&descSize=17&descAlignY=56&descAlign=50" alt="byteforge — senior frontend / UI engineer, React and TypeScript, canvas and SVG" />

<a href="#-featured">
  <img src="https://readme-typing-svg.demolab.com?font=JetBrains+Mono&weight=600&size=21&pause=1100&color=60A5FA&center=true&vCenter=true&width=760&lines=Senior+Frontend+%2F+UI+Engineer.;React+%2B+TypeScript+%E2%80%94+complex+interfaces+and+interactions.;Editors%2C+dashboards%2C+drag-and-drop%2C+canvas+and+SVG.;Design+systems%2C+interaction+models%2C+pixel-level+care.;Specs+with+objective+pass+%2F+fail+criteria." alt="Senior Frontend / UI Engineer. React + TypeScript, complex interfaces and interactions. Editors, dashboards, drag-and-drop, canvas and SVG." />
</a>

<br/>

<img src="https://img.shields.io/badge/React-19-61DAFB?style=for-the-badge&logo=react&logoColor=black&labelColor=0B1220" alt="React 19" />
<img src="https://img.shields.io/badge/TypeScript-5-3178C6?style=for-the-badge&logo=typescript&logoColor=white&labelColor=0B1220" alt="TypeScript 5" />
<img src="https://img.shields.io/badge/Canvas%20%2F%20SVG-UI-F59E0B?style=for-the-badge&labelColor=0B1220" alt="Canvas / SVG" />
<img src="https://img.shields.io/badge/Figma-design-F24E1E?style=for-the-badge&logo=figma&logoColor=white&labelColor=0B1220" alt="Figma" />
<img src="https://img.shields.io/badge/Playwright-e2e-2EAD33?style=for-the-badge&logo=playwright&logoColor=white&labelColor=0B1220" alt="Playwright" />

</div>

---

## 👋 Hi, I'm Senior Software Engineer

Based in Madrid. I design and build complex interfaces in **React + TypeScript + Java + Python** — editors, dashboards, drag-and-drop, interfaces that draw on `<canvas>` and SVG. I work from the design side in: tokens, component systems, interaction models, keyboard and pointer behaviour, accessibility — and I care as much about *proving* an interface behaves correctly as about shipping it.

The work I enjoy most sits where product design meets engineering: editors, dashboards, drag-and-drop, interfaces that draw on a `<canvas>` or SVG, and the state management that keeps them consistent under rapid input.

<div align="center">

| | |
|---|---|
| 🎨 **UI engineering** | React 19, TypeScript, Vite, Zustand / Redux Toolkit, TanStack Query, Tailwind, CSS architecture |
| 🖌️ **Design** | Figma, design tokens, component systems, responsive layout, motion, accessibility (WCAG, keyboard, ARIA) |
| 🧩 **Graphics & interaction** | Canvas 2D, SVG, pointer gestures, selection models, undo / redo, drag-and-drop, hit-testing |
| 🧪 **Verification** | Playwright, Vitest, Jest, React Testing Library, specs with objective pass / fail criteria |
| 🚀 **Ship** | GitHub Actions, Vercel / Netlify, performance budgets, Lighthouse |

</div>

<div align="center">
  <img src="https://skillicons.dev/icons?i=react,ts,js,html,css,tailwind,redux,vite,figma,jest,vitest,git,githubactions,vscode&perline=7" alt="React, TypeScript, JavaScript, HTML, CSS, Tailwind, Redux, Vite, Figma, Jest, Vitest, Git, GitHub Actions, VS Code" />
</div>

---

## 📌 Featured

### [`canvas-task-alignment`](https://github.com/byteforge9190/canvas-task-alignment) — a task spec, its tests, and the reference build

A worked example of how I specify, build and grade frontend work. A React / TypeScript canvas editor, a three-step task written from screenshots of the finished feature, and a test suite that decides *pass* or *fail* without judgement calls.

<a href="https://github.com/byteforge9190/canvas-task-alignment/blob/main/TASK.md">
  <img src="https://raw.githubusercontent.com/byteforge9190/byteforge9190/main/assets/task-pipeline.svg" alt="From screenshot to verifiable task: visual material → task spec → objective criteria → reference build → review guide" width="100%" />
</a>

| | |
|---|---|
| **The task** | `TASK.md`: marquee selection → align / distribute toolbar → keyboard nudge with undo. Precise state contract, worked numeric examples, documented traps (floating-point drift, edge-contact hit-testing). |
| **Success criteria** | 12 unit tests on the geometry + 16 Playwright tests that drive the real UI with pointer and keyboard events and assert on application state, not pixels. |
| **Review process** | `VERIFICATION.md`: a ten-minute reviewer procedure with integrity checks and explicit accept / reject rules. |
| **Branches** | `main` = the starter an implementer receives (tests fail by design) · `solution` = reference implementation (all green) |

```bash
git clone https://github.com/byteforge9190/canvas-task-alignment && cd canvas-task-alignment
npm ci && npx playwright install chromium
git checkout solution && npm test        # 12 unit + 16 e2e pass
git diff main solution --stat            # exactly what the task asks for
```

<!-- Next featured entries go here as they land. Only link work that is actually mine. -->

---

## 🧭 How I work

<img src="https://raw.githubusercontent.com/byteforge9190/byteforge9190/main/assets/interface-blueprint.svg" alt="Interface design blueprint: one spec drives the visual design, interaction model and rendering, each verified by its own checks" width="100%" />

> "It looks right" is where the work starts, not where it ends.

- **Design the interaction model before the components.** Selection, focus, keyboard, pointer and undo rules are decided first; components are built to honour them, not the other way round.
- **Write the expected behaviour down before coding** — including what should *not* happen (no history entry on a marquee, no nudge with an empty selection).
- **Prefer tests that assert on state** over screenshots; keep the few pixel checks explicit and manual.
- **Treat ambiguity as a defect in the spec.** If two reasonable implementations would disagree, fix the spec — don't grade the guess.
- **Document the traps** instead of letting people fall into them.

---

## 🤝 Reach me

<div align="center">

<a href="https://github.com/byteforge9190/canvas-task-alignment/issues"><img src="https://img.shields.io/badge/Open%20an%20issue-24292F?style=for-the-badge&logo=github&logoColor=white" alt="Open an issue on GitHub" /></a>

<br/><br/>
<sub>Open to remote senior frontend / UI engineering work and contract UI task-design roles. The quickest way to reach me is an issue or discussion on any of my repos.</sub>

</div>

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:60A5FA,55:1D4ED8,100:0B1220&height=110&section=footer" alt="" />
