<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:0B1220,45:1D4ED8,100:60A5FA&height=190&section=header&text=byteforge&fontSize=60&fontColor=EFF6FF&fontAlignY=34&desc=full-stack%20engineer%20%C2%B7%20frontend-first%20%C2%B7%20canvas%20and%20SVG&descSize=17&descAlignY=56&descAlign=50" alt="byteforge — full-stack engineer, frontend-first, canvas and SVG" />

<a href="#-featured">
  <img src="https://readme-typing-svg.demolab.com?font=JetBrains+Mono&weight=600&size=21&pause=1100&color=60A5FA&center=true&vCenter=true&width=760&lines=Full-stack+engineer%2C+frontend-first.;React+%2B+TypeScript+on+top%2C+Node+%2B+MongoDB+underneath.;Editors%2C+dashboards%2C+drag-and-drop%2C+canvas+and+SVG.;Specs+with+objective+pass+%2F+fail+criteria.;Every+claim+on+this+page+is+one+click+from+proof." alt="Full-stack engineer, frontend-first. React + TypeScript on top, Node + MongoDB underneath. Editors, dashboards, drag-and-drop, canvas and SVG." />
</a>

<br/>

<img src="https://img.shields.io/badge/React-19-61DAFB?style=for-the-badge&logo=react&logoColor=black&labelColor=0B1220" alt="React 19" />
<img src="https://img.shields.io/badge/TypeScript-5-3178C6?style=for-the-badge&logo=typescript&logoColor=white&labelColor=0B1220" alt="TypeScript 5" />
<img src="https://img.shields.io/badge/Node.js-20-339933?style=for-the-badge&logo=nodedotjs&logoColor=white&labelColor=0B1220" alt="Node.js" />
<img src="https://img.shields.io/badge/MongoDB-Mongoose-47A248?style=for-the-badge&logo=mongodb&logoColor=white&labelColor=0B1220" alt="MongoDB" />
<img src="https://img.shields.io/badge/Playwright-e2e-2EAD33?style=for-the-badge&logo=playwright&logoColor=white&labelColor=0B1220" alt="Playwright" />

</div>

---

## 👋 Hi, I'm Byte Forge

Full-stack engineer based in Madrid, strongest on the frontend. I design and build complete features end to end — the React + TypeScript interface, the Node/Express API behind it, the MongoDB or PostgreSQL schema underneath — and I care as much about *proving* a feature behaves correctly as about shipping it.

The work I enjoy most sits where product design meets engineering: editors, dashboards, drag-and-drop, interfaces that draw on a `<canvas>` or SVG, and the state management that keeps them consistent under rapid input.

<div align="center">

| | |
|---|---|
| 🎨 **Frontend & UI** | React 19, TypeScript, Vite, Zustand / Redux Toolkit, TanStack Query, Tailwind, Canvas 2D, SVG |
| ⚙️ **Backend** | Node.js, Express, REST API design, JWT auth, validation (Zod / Joi), rate limiting |
| 🗄️ **Data** | MongoDB + Mongoose, PostgreSQL, Redis |
| 🧪 **Verification** | Playwright, Vitest, Jest, React Testing Library, specs with objective pass / fail criteria |
| 🚀 **Ship** | Docker, GitHub Actions, Vercel / Netlify / Render |

</div>

<div align="center">
  <img src="https://skillicons.dev/icons?i=react,ts,js,html,css,tailwind,redux,vite,nodejs,express,mongodb,postgres,redis,docker,githubactions,figma&perline=8" alt="React, TypeScript, JavaScript, HTML, CSS, Tailwind, Redux, Vite, Node.js, Express, MongoDB, PostgreSQL, Redis, Docker, GitHub Actions, Figma" />
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

<img src="https://raw.githubusercontent.com/byteforge9190/byteforge9190/main/assets/fullstack-blueprint.svg" alt="Full-stack feature blueprint: one spec drives the interface, API and data layers, each verified by its own tests" width="100%" />

> "It looks right" is where the work starts, not where it ends.

- **Design the whole feature, not one layer.** Schema, API contract and UI state are decided together, so the interface never has to paper over a bad data model.
- **Write the expected behaviour down before coding** — including what should *not* happen (no history entry on a marquee, no nudge with an empty selection).
- **Prefer tests that assert on state** over screenshots; keep the few pixel checks explicit and manual.
- **Treat ambiguity as a defect in the spec.** If two reasonable implementations would disagree, fix the spec — don't grade the guess.
- **Document the traps** instead of letting people fall into them.

---

## 🤝 Reach me

<div align="center">

<a href="https://github.com/byteforge9190/canvas-task-alignment/issues"><img src="https://img.shields.io/badge/Open%20an%20issue-24292F?style=for-the-badge&logo=github&logoColor=white" alt="Open an issue on GitHub" /></a>

<br/><br/>
<sub>Open to remote full-stack / frontend engineering work and contract task-design roles. The quickest way to reach me is an issue or discussion on any of my repos.</sub>

</div>

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:60A5FA,55:1D4ED8,100:0B1220&height=110&section=footer" alt="" />
