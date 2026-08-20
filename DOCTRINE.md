# RACECARDS — build doctrine

Purpose: turn a Racing Post digital paper (PDF) into one interactive single-file HTML racecard app per meeting, fast, in a fresh chat.

## Workflow (fresh chat)
1. User uploads the whole paper PDF and names the meetings ("do York and Carlisle").
2. Fetch template.html from this repo (raw.githubusercontent.com/igxdev86/racecards/main/template.html).
3. From the PDF (on disk at /mnt/user-data/uploads/), locate per meeting (~4 pages):
   - Race cards with SPOTLIGHT comments, betting forecast, verdict, draw advantage
   - POSTDATA/TOPSPEED grids + OFFICIAL RATINGS (last-6) page
   - Ten-year trends / topdraw / trainers-jockeys / travellers / selection box page
   Rasterise pages at high zoom (pdftoppm -r 200+) and crop regions to read tick grids and superscript OR digits. If a text layer exists, prefer text extraction for tables.
4. Replace in template: const RACES, const TRAINERS, const TRAVEL, header texts, COURSE-tab card tables (draw, favourites, selection box consensus).
5. Verify with jsdom (runScripts:'dangerously'): grid counts per race, default Rank top matches expectation, model buttons paint, cloth click marks winner, no inline onclick anywhere (artifact sandbox blocks them — event delegation only).
6. present_files the finished HTML(s). One file per meeting, named e.g. york-2026-08-20.html.

## Runner schema (14 fields)
[no, draw, form, horse, jockey, trainer, weight, forecastPrice, TS, RPR, note, ticks, OR, last6ORseq]
- jockey claim "(N)" is parsed → gold −N chip, effective weight drives WR + Weight sort
- ticks string: "TrF Gng Dst Crs Drw Abl RcF" using ✓✓/✓/?/✗/– plus optional "|G1" group-entry suffix
- OR "–" for non-handicaps; seq space-separated oldest→latest
- notes: concise paraphrase of the Spotlight comment (never verbatim copy)

## Race object
{t,n,m,draw,pd,rp,nap,v,tr,r[]} — t time, n name, m meta line, draw advantage, pd Postdata pick, rp RP Rating pick, nap verdict pick, v verdict (HTML ok), tr trends line, r runners.

## Features already in the engine (do not rebuild)
Rank default sort (power-rank + market-rank, chip shows P#+M#=total), sorts (Card/Price/RPR/WR/TS/OR/Trend/Draw/Weight), models Power/Fit/Value/P/O/Beta with green→red painting, winner marking via number cloth (in-memory), Beta self-fit to marked winners (grid-search weights, betainfo line), spotlight expand on tap, last-6 OR sparkline + trend arrow, betting forecast panel at bottom of each race, COURSE tab with Model Consensus + four model lists + draw/favourites/trainers-or-jockeys/travellers/selection-box cards.

## Hard rules
- Single file, plain HTML/JS, no frameworks, mobile-first, dark theme (#0b0d10, gold #d7b56d).
- No localStorage/sessionStorage (artifact sandbox) — state in memory.
- No inline onclick attributes — delegated listener on main only.
- Non-runners: exclude from r[], note in meta.
- Models are untested weightings: shortlist filters, not a betting system.
