# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

@AGENTS.md

# Claude Code — seed คอร์ส (อย่าลบตอน /init)

หลัง Lab 00 ให้ `/init` **merge** — เก็บกฎด้านล่างไว้เสมอ

## สี่เสา (ย่อ)

1. Multi-Agent แยกหน้าที่/ความจำ · 2. Sub-Agent ใช้แล้วทิ้ง · 3. ประสานผ่าน docs/PR · 4. Swarm เพดาน **20 turns**

## Ownership (บังคับ)

| Artifact | Owner |
|---|---|
| UI | Claude · `.claude/agents/frontend.md` |
| API + SQLite | OpenCode · `.opencode/agents/backend.md` |
| docs PROFILE / DEBATE / DECISIONS | Claude (Lab 01–02) |
| Hot state STATUS / OPEN_LOOPS | ผู้ถืองานรอบนั้น (single-writer) |

## Canonical context (อ่านก่อน · อย่าคัดลอกซ้ำในไฟล์นี้)

ก่อนลงมือ:

1. `docs/STATUS.md`
2. `docs/OPEN_LOOPS.md`
3. handoff ล่าสุดใน `docs/handoffs/` (ถ้ามี)
4. ตามงาน: `docs/PROFILE.md` · `docs/DECISIONS.md`

สรุป Goal / Latest D-id / Open loops / Blockers **ไม่เกิน 8 บรรทัด**  
ห้ามสมมุติจากแชท OpenCode ถ้าไม่มีใน `docs/`  
จบงานที่เปลี่ยนสถานะ → อัปเดต STATUS / OPEN_LOOPS · สลับ harness → เขียน handoff จาก [`docs/handoffs/TEMPLATE.md`](docs/handoffs/TEMPLATE.md)

## กฎสั้น

- Root เท่านั้น · plugin **project scope**
- Skill **`public-site-safe`**
- Agent ถาวรใช้ `memory: project` (harness) — ตรวจใน Lab 00 · ห้ามสร้าง memory bus เอง
- MCP ไม่ใช่ท่อ Claude ↔ OpenCode · Cross-CLI เฉพาะ Lab 07
- ห้าม commit `.env` · PR เข้า learner repo เท่านั้น
- Swarm: หยุดเมื่อ done หรือครบ 20 turns
- STATUS/OPEN_LOOPS = single-writer · commit ก่อนสลับ harness

## Commands (เพิ่มจาก /init · คำสั่งหลักอยู่ใน AGENTS.md)

- `better-sqlite3` เป็น native module (ต้อง build ตอน `npm install`)
- ทดสอบไฟล์เดียว: `npx vitest run tests/smoke.test.ts` · ชื่อเคส: `npx vitest run -t "<name>"`
- Lab test ไฟล์เดียว: `npx vitest run --config vitest.labs.config.ts tests/labs/lab05-api.test.ts`
- E2E spec เดียว: `npx playwright test playwright/smoke.spec.ts` (config ไม่ start server ให้)

## Architecture (ภาพรวม)

- Astro `output: 'server'` + `@astrojs/node` standalone → `npm run build` ได้ `dist/server/entry.mjs` (`npm start`) · Docker/Coolify ใช้ entry นี้ และ copy `docs/` เข้า image ด้วย
- **เนื้อหาเว็บมาจาก `docs/PROFILE.md` ตอน runtime** — `src/lib/profile.ts` parse หัวข้อ `## Name / Headline / Bio / Audience / Interests` (Interests = bullet list) และใช้ `FALLBACK` เมื่อหาไฟล์/หัวข้อไม่เจอ · เปลี่ยนชื่อหัวข้อใน PROFILE = หน้าเว็บตกไป fallback
- `src/lib/db.ts` — SQLite ที่ `${DATA_DIR:-./data}/site.sqlite` · `getDb()` สร้างตาราง `contact_messages` / `guestbook` · `insertContact` / `listGuestbook` / `insertGuestbook` เป็น stub ที่ throw `NOT_IMPLEMENTED...` (งาน Lab 05 · owner OpenCode)
- `src/pages/api/*` (`prerender = false`) — แปลง error ที่ขึ้นต้นด้วย `NOT_IMPLEMENTED` เป็น HTTP **501**, error อื่นเป็น 400/500 · ฟอร์ม UI ควรรองรับ 501 ระหว่างที่ backend ยังไม่เสร็จ
- `src/layouts/BaseLayout.astro` ถือ global styles / CSS variables (`--bg`, `--accent`, ...) — หน้าอื่นใช้ layout นี้
- `tests/public-site.test.ts` สแกนเฉพาะ `.astro`/`.html` (ตัด frontmatter + HTML comments) ด้วย regex `lab/labs + เลข` หรือ "แล็บ" · `FALLBACK` ใน `profile.ts` ไม่ถูกสแกนแต่ render จริง — ต้องไม่อ้างคอร์สเช่นกัน

## Labs

ดู [`labs/README.md`](labs/README.md) · เริ่ม [`lab-00-project-init`](labs/lab-00-project-init/README.md)
