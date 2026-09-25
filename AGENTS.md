# Agents — Build AI Multi-Agent Lab (V4)

กติการ่วมสำหรับ **Claude Code** และ **OpenCode**
สินค้า = เว็บ personal branding: Astro 7 + Node adapter + better-sqlite3 ใน root นี้ · Node ≥ 22.12
Docs/คำสั่งเขียนบน **Windows PowerShell** — macOS/Linux ใช้ `cp` แทน `copy` ได้

หลัง Lab 00: `/init` แล้ว **merge** — อย่าลบ Ownership / สี่เสา / Native harness

## สี่เสาหลัก (รายละเอียด: `COURSE.md`)

1. **Multi-Agent** — หน้าที่และความจำแยก (`.claude/agents/` · `.opencode/agents/` คนละ CLI)
2. **Sub-Agent** — spawn ใช้แล้วทิ้ง; สิ่งที่ต้องจำต่อ = เขียนลง `docs/` เท่านั้น
3. **การประสานงาน** — handoff ผ่าน docs / issues / PR / review สำคัญกว่าแชทเดียว
4. **Swarm** — หลายตัวได้; **เพดาน 20 turns** แล้วหยุดสรุปช่องว่าง (Lab 05b)

ใช้ skill **`public-site-safe`** (มีทั้ง `.claude/skills/` และ `.opencode/skills/`) ทุกงาน implement / swarm / ship

## คำสั่ง & การตรวจสอบ

```powershell
npm install            # template ไม่มี node_modules มาให้ — ติดตั้งใน Lab 00
npm run dev            # Astro dev server (127.0.0.1:4321)
npm test               # unit (tests/*.test.ts ไม่รวม labs) — ต้องเขียวเสมอ
npm run test:labs      # tests/labs/** — ตั้งแต่ template ยัง RED (stub โยน NOT_IMPLEMENTED จน Lab 05 เขียน) — ไม่ใช่ regression
npm run test:e2e       # Playwright — ต้องรัน `npm run dev` ค้างไว้ก่อน (baseURL 127.0.0.1:4321 · override ด้วย PLAYWRIGHT_BASE_URL)
npm run build          # ต้องผ่านก่อน PR — CI (.github/workflows/ci.yml) รัน npm ci + npm test + build บน Node 22
npm start              # รันได้เฉพาะหลัง `npm run build` (dist/server/entry.mjs)
node scripts/create-course-issues.mjs   # สร้าง issues คอร์ส — หลัง npm install
```

- `npm test` มี test จับ **ข้อความคอร์ส/แล็บหลุดไป render หน้าเว็บ** (`tests/public-site.test.ts`) — อ้าง Lab ได้เฉพาะโค้ดคอมเมนต์ / PR / `docs/`
- SQLite: `DATA_DIR` กำหนดที่เก็บ (default `./data/site.sqlite`) · lab tests ใช้ `data/vitest-lab`
- `.env` จาก `.env.example` — ห้าม commit · `opencode.json` / `.mcp.json` สร้างเองใน Lab 00 จาก `*.example`

## สี่ชั้นความรู้ (อ่านก่อนลงมือ)

| ชั้น | ไฟล์หลัก |
|---|---|
| Rules | ไฟล์นี้ · `CLAUDE.md` · skill `public-site-safe` |
| Context | `COURSE.md` · `docs/PROFILE.md` · `docs/DECISIONS.md` |
| State (Hot) | `docs/STATUS.md` · `docs/OPEN_LOOPS.md` |
| Artifacts | `src/` · tests · `docs/QA.md` · PR |

**Hot / Warm / Cold:** Hot = STATUS + OPEN_LOOPS + handoff ล่าสุด · Warm = PROFILE/DECISIONS/Ownership · Cold = `_cli-*` / logs เก่า
**Proposed vs Approved:** `DEBATE.md` = ยังไม่ปิด · `DECISIONS.md` = อนุมัติแล้วเท่านั้น

### Start-of-session (≤ 8 บรรทัด)

ก่อนเริ่มงานทุกครั้ง:

1. อ่าน `docs/STATUS.md` และ `docs/OPEN_LOOPS.md` — ถ้ายังไม่มี (ก่อน Lab 00 copy จาก `*.md.example`) ให้ชี้ผู้เรียน copy ก่อน อย่าถือว่า repo พัง
2. ถ้ามี handoff ล่าสุดใน `docs/handoffs/` ที่ส่งถึงคุณ — อ่านด้วย
3. สรุปให้คนดู: Current goal · Latest D-id (ถ้ามี) · Open loops · Blockers — **ไม่เกิน 8 บรรทัด**
4. ถ้าข้อมูลขัดแย้งระหว่างไฟล์ — หยุดวิเคราะห์ก่อนแก้โค้ด
5. **ห้าม**สมมุติว่าคุณรู้สิ่งที่เกิดในแชทของ CLI อีกฝั่ง ถ้าไม่มีเขียนใน `docs/`

จบงานที่เปลี่ยนสถานะ: อัปเดต `STATUS.md` / `OPEN_LOOPS.md` (และ handoff ถ้าสลับ harness)

## Single-writer (ไฟล์ร่วมมีคนเขียนคนเดียวต่อรอบ)

- `docs/STATUS.md` และ `docs/OPEN_LOOPS.md` มี **writer คนเดียวต่อรอบ** — สลับ Claude ↔ OpenCode หลัง commit หรือหลังเขียน handoff
- Ownership โค้ดตามตารางด้านล่าง — reviewer อ่านอย่างเดียวจนกว่าจะโอนงานชัดใน handoff
- อย่าให้สอง agent แก้ไฟล์เดียวกันพร้อมกันโดยไม่แยก branch

## Ownership

| Artifact | Owner |
|---|---|
| UI (`src/pages/*.astro`, `src/layouts/`, styles) | Claude · agent `frontend` |
| API + SQLite (`src/lib/db.ts`, `src/pages/api/*`) | OpenCode · agent `backend` |
| E2E / a11y (`docs/QA.md`) | Playwright MCP + either CLI |
| Profile / debate docs | Claude (Lab 01–02 · subagents) |
| Hot state (`STATUS.md` · `OPEN_LOOPS.md`) | ผู้ถืองานรอบนั้น (single-writer) |
| Handoffs (`docs/handoffs/`) | ผู้ส่งงานก่อนสลับ harness |
| Review artifacts | Lab 07 · agent `reviewer` (Claude) / OpenCode review |
| Ship (`docs/SHIP.md`) | Lab 08 |

## ความจำ

| ชนิด | อยู่ที่ | ตัวอย่าง |
|---|---|---|
| ร่วม (shared) | `docs/`, git, PR | STATUS, OPEN_LOOPS, PROFILE, DECISIONS, QA, handoffs |
| แยก (agent-local) | เซสชัน + ไฟล์ agent | frontend ไม่ถือ context backend |
| **Harness persistent** | Claude / OpenCode native | ดูตารางด้านล่าง — **ห้ามสร้าง memory bus เอง — ใช้ของที่ harness มีให้** |
| ทิ้งได้ | Sub-Agent รอบเดียว | Brand/UX/Devil หลังจบ Lab 02 |

### Harness persistent memory (ตรวจใน Lab 00)

| เครื่องมือ | ใช้ของอะไร | ตรวจยังไง |
|---|---|---|
| Claude Code | `memory: project` บน agent → `.claude/agent-memory/<name>/` · auto memory ผ่าน `/memory` | จำข้ามเซสชัน + มีไฟล์ MEMORY |
| OpenCode | `AGENTS.md` + agent file + **resume session** | resume เห็นบริบท · เซสชันใหม่ไม่บังคับ recall ปากเปล่า |

ความจำร่วมของคอร์ส (`docs/`) คนละชั้นกับ harness memory — สิ่งที่ต้องโชว์ข้ามคน/CLI ให้เขียนลง docs
Adapter (`AGENTS.md` / `CLAUDE.md`) ต้อง**ชี้ไป**ไฟล์กลาง — อย่าคัดลอกเนื้อหา STATUS/DECISIONS ซ้ำใน adapter

## Native harness only

harness = ความสามารถถาวรที่ Claude Code / OpenCode มีให้ในตัว (memory, plugin, session) — ใช้ของเดิม ไม่สร้างชั้นเอง

- Plugins project scope: superpowers (oh-my-openagent ยังไม่รองรับ OpenCode v2 — ใช้ native agents)
- **Call ข้าม harness ทำได้** — แต่ละตัวยังรันบน harness ตนเอง: ฝั่ง OpenCode เรียก `claude -p` · ฝั่ง Claude เรียก `opencode run` (headless one-shot · ท่อ = ไฟล์ใน `docs/`)
- **กติกา call:** ฝั่งที่ถูกเรียกเขียนได้**เฉพาะไฟล์รายงาน**ที่ prompt ระบุ (เช่น `docs/review-*.md`) — ห้ามแตะไฟล์ ownership ของผู้เรียก · อย่าให้สอง harness เขียน working tree พร้อมกัน (commit ก่อน)
- ห้ามสร้างระบบส่งข้อความ/สถานะระหว่าง CLI เอง (เช่น ใช้ไฟล์ JSON เป็นท่อส่งงาน) · ห้าม daemon/loop ถาวร
- MCP = งานผลิต — **ไม่ใช่**ท่อระหว่างสอง CLI
- Swarm หยุดเมื่อ done **หรือ** ครบ **20 turns**

## ห้าม

- Commit `.env`, PAT, Coolify webhook, `node_modules`
- เคลม deploy สำเร็จโดยไม่มี URL 200 จริง
- บังคับ tmux บน Windows
- PR เข้า `Onto-IQ/*` — เข้า learner repo เท่านั้น (repo นี้ = template ให้กด "Use this template" ไม่ใช่ fork)
- ปล่อย swarm เกิน 20 turns โดยไม่สรุปหยุด

## Labs

[`SETUP.md`](./SETUP.md) → [`labs/lab-00-project-init`](./labs/lab-00-project-init/README.md) → [`labs/README.md`](./labs/README.md)
