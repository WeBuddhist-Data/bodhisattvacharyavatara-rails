# Handoff: generating English + Hindi day plans (Bodhisattva Challenge)

First written 2026-10-02, last updated 2026-11-23 (after Day 155). Point a fresh session at this file, then say e.g. "create day 156". Read this whole file and the `dalai-lama-plan-translation` skill before starting.

## Status

- **Done through Day 155** (Chapter 6, verse 100), both `en/` and `hi/`. Chapter 5 (Days 74-116) is complete; Chapter 6 output folders are `en/Days/Chapter-6 D117-D170/` and `hi/Days/Chapter-6 D117-D170/`.
- **Next: Day 156.** Tibetan files were populated through at least Day 170 on 2026-11-23 (checked up to Day 200); later days were not re-checked. Run `wc -c` on each Tibetan file before generating; an empty file is a stop.
- Chapter 6 term choices (approved 2026-10-27 with all other pending terms): ཁོང་ཁྲོ་ / ཁྲོ་བ་ = anger / क्रोध; ཞེ་སྡང་ = hatred / द्वेष; བཟོད་པ་ = patience / धैर्य; ཡིད་མི་བདེ་བ་ = unhappiness / मन का दुख; གཉེན་པོ་ = antidote / उपाय. Keep anger and hatred distinct.
- Days 15-36 (Chapter 2) are older rails-authored files in a different style from 37+. Never touched or reconciled.
- The user's request pattern is simply "create day N" or "N-M". If a day is skipped in the request, check whether it exists and ask once (Day 85 was skipped once; user said fill it).

## Where things live (vault root `bodhisattvacharyavatara-rails`, via the device bridge at `$HOME/mnt/bodhisattvacharyavatara-rails/`)

| What | Path (under `3-TRANSFORMATIONS/`) |
|---|---|
| Tibetan source per day | `Plans/Dalai Lama/Chapter-<C> D<a>-D<b>/Day-<n>-Ch<C>-V<v1>-<v2>.md` |
| English output | `Plans/the-bodhisattva-challenge/en/Days/Chapter-<C> D<a>-D<b>/<n>-ch<C>-v<v1>-<v2>-eng.md` |
| Hindi output | `.../hi/Days/Chapter-<C> D<a>-D<b>/<n>-ch<C>-v<v1>-<v2>-hi.md` |
| Termbases (source of truth) | `Plans/the-bodhisattva-challenge/en/termbase-translation.md` and `hi/termbase-translation.md` |
| Verse text (EN) | `Translations/AI_translation/english/bca-english-plain.md` (block IDs like `^5-12`) |
| Verse text (HI) | `Translations/AI_translation/hindi/bca-hindi-plain.md` |
| Skill | `dalai-lama-plan-translation` (read it first; it is the contract) |

Frontmatter on every output: `day, chapter, verse (string "a-b"), generated_by: dalai-lama-plan-translation, translated_from, verse_source, pending_terms (list or []), status: draft`. Copy the structure of any existing file, e.g. `en/Days/Chapter-5 D74-D116/74-ch5-v1-3-eng.md` and its `-hi` twin.

## What gets translated (only these)

From the Tibetan day file: **ངོ་སྤྲོད། (Introduction)**, **གོ་རྟོགས། (Commentary)**, and under 📿: **ཉམས་ལེན་དངོས། (Actual Practice)** and **དེའི་འགྲེལ་བཤད། (Explanation, starts with the `_(category)_` label)**.
Everything else (refuge/bodhicitta block, root-verse block, dedication, the trailing verse repeat) is NOT translated fresh. Verses come verbatim from the two plain-translation files by block ID (`grep -B4 "\^5-12$" file`).

Output sections: `## Today's Verse` / `## आज का श्लोक`, `## 1) Introduction to Today's Practice`, `## 2) Commentary Explanation`, `## 3) Today's Practice` (`**Actual Practice:**` + `**Explanation:**`). Hindi headings use `१) आज के अभ्यास का परिचय`, `२) अर्थ और व्याख्या`, `३) आज का अभ्यास`, `**मुख्य अभ्यास:**`, `**व्याख्या:**`.

## Style rules that kept mattering

- CEFR A2-B1: one idea per sentence, mostly under 20 words, active voice. No diacritics in body prose except italicised cited work titles (`_Bodhicaryāvatāra_`, `_बोधिचर्यावतार_`). Cited titles are always italicised.
- Actual Practice is first person, future: "Today, I will..." / "आज मैं ... करूँगा". Commentary and Explanation use "we".
- Fixed words (details in termbases): དགེ་བ་ "doing good"/"अच्छा काम" (never merit/पुण्य); བསོད་ནམས་ "merit"/"पुण्य"; སྡིག་པ་ "wrongdoing"/"बुरा काम" (never sin/पाप in prose); ཉོན་མོངས་ "the afflictions"/"मन के विकार" (never bare क्लेश in prose); ངན་སོང་ "the lower realms"/"बुरी गतियाँ"; ལེ་ལོ་ "laziness"/"आलस" (not आलस्य); བག་མེད་ "carelessness"/"लापरवाही".
- All 15 practice-category labels are in the controlled table in each termbase (Doing good, Avoiding wrongdoing, Taming the mind, Generosity, Patience, Ethics, Diligence, Wisdom, Practice of remembering that nothing lasts, Recognizing the afflictions, Giving up anger, Not feeding the afflictions, Not giving up the effort, Welcoming hardship, Meditation). Use them exactly. A category not in the table is new: log it as pending and keep going.
- Do not translate more than the Tibetan says. Do not add a mood, person, or a "goal/everything/caution" that is not there. Keep story beats and attributions exactly.
- If the Tibetan file ends with a translator's note saying no scholarly commentary exists (Day 90 did), write a plain literal explanation with no invented teacher, story, or citation.

## Standing instruction on new terms

New practice categories, proper names, text titles: **propose a rendering, log it as pending, and keep going. Do not stop to ask.** (User chose "propose and continue" explicitly.) Log in the day file's `pending_terms:` as `"<Tibetan> -> <rendering>"` and in both termbases' Pending table. Flag uncertain identifications in the rendering text itself (e.g. Day 76 sutra title, Day 78 king, Day 85 citation).

## The pipeline used every batch

1. List `Day-*` files in the chapter folder; `cat` each Tibetan source with `device_bash` (staging does NOT work for `Plans/Dalai Lama`).
2. Check the termbases (`grep`) for any new category, name, or title before drafting.
3. Pull the verses by block ID, EN and HI.
4. Draft EN + HI into a fresh scratch dir (`mkdir -p /tmp/chXY`) via `cat > file <<'XEOF'` heredocs through `device_bash`. Each `device_bash` call is a fresh shell. `/tmp` has been wiped between sessions, so never rely on old scratch dirs.
5. **Adversarial audit**: spawn `general-purpose` Agent(s) (about 3 days per agent) told to `cat` the Tibetan, the drafts, and both termbases via `device_bash` (not the Read tool), and to report by class: 1 additions with no Tibetan parent, 2 omissions, 3 meaning shifts (person/mood/attribution), 4 termbase violations, 5 EN/HI divergence, 6 register. Tell it not to flag word counts. Point it at the two or three spots you are least sure of.
6. **Verify every finding yourself against the Tibetan** before fixing. Most were real; reject any that are not.
7. Apply fixes with python read-modify-write (assert the old string exists).
8. `cp` into the vault `en/Days/...` and `hi/Days/...` and **md5sum-compare** every file.
9. Sync pending terms into both termbases with `sync_termbase_pending.py` (same folder as this file). It skips any Tibetan term already in any termbase table, so approved terms are not re-added. All pending terms up to Day 133 were approved on 2026-10-27: reuse those renderings and do not log them again. Six terms from Days 134-140 are pending (unruly mind, monastic, wrong livelihood, flatter, profit, gains): reuse them as listed in the termbase Pending table. Then `git diff` both termbases: it must be purely additive (only the new rows).
10. Report: what was created, real audit findings and fixes, any upstream verse-source deviations, new pending terms. Short.

## Gotchas learned the hard way

- `device_bash` cannot delete or unlink. `git checkout -- file` fails. Revert a file with `git show HEAD:"<path>" > "<path>"`.
- The termbase Pending tables have different headings: EN `## Pending terms`, HI `## लंबित शब्द`. Column headers differ too. The EN table is padded/aligned (a formatter touched it). **Append-only** is the safe approach. An earlier version that rewrote the whole table reformatted 66 unrelated rows; and an even earlier version matched the wrong path with a regex and corrupted all day numbers. `sync_termbase_pending.py` is the corrected, append-only version. Check its output before trusting it.
- Day number must be parsed from the file basename (`re.match(r'(\d+)-', basename)`). Old Chapter-1 files (`5.md`) have no such prefix and are skipped.
- Mid-session the `/tmp` scratch can vanish (the device VM restarts). If `cd /tmp/chX` fails, rewrite the drafts before fixing.

## Known bugs in the shared verse-source files (NOT fixed upstream; fixed locally in the day files and disclosed)

The plain-translation files are verbatim sources, so verses are normally never edited. These were wrong enough to fix in the day file only:

| Day | Verse | Problem |
|---|---|---|
| 74 | EN 5-2 | Elephant/mind harm comparison reversed vs Tibetan, Hindi, and the day's commentary |
| 78 | HI 5-11 | Declarative turned into a question |
| 81 | EN 5-19 | Two similes merged into a circular line; "wild people" put in the wrong clause |
| 84 | HI 5-28 | पुण्य used for དགེ (doing good) |
| 86 | EN 5-32 | Invented fourth quality "and likewise caution" |

Left as-is on purpose: Day 82 HI 5-22 "भलाई" for doing good (no meaning conflict). Someone should eventually fix these five in the two plain files.

## Termbase state and open questions (as of 2026-11-23)

- On 2026-10-27 the user approved ALL terms that were pending up to Day 133. They now sit in the termbase tables ("Approved terms" section, Persons, Scriptural sources, category labels). Reuse them; never re-log them.
- Six terms from Days 134-140 are pending in both termbases: unruly mind (མ་རུངས་པའི་སེམས་, HI बेक़ाबू मन), monastic (དགེ་སྦྱོང་, HI संन्यासी), wrong livelihood (ལོག་འཚོ་, HI गलत जीविका), flatter (ངོ་བསྟོད་, HI चापलूसी करना), profit (ཁེ་བཟང་, HI मुनाफ़ा), gains (རྙེད་པ་, HI लाभ). Reuse these renderings. Eleven more from Days 141-149 are pending: faith (དད་པ་, श्रद्धा), being under the power of the afflictions (ཉོན་མོངས་པའི་གཞན་དབང་), ignorance (རྨོངས་པ་, HI मोह to match the Hindi verse source), those close to me (རྩ་ལག་, unclear), attachment (ཆགས་པ་, लगाव), eon (བསྐལ་པ་, कल्प), jealousy (ཕྲག་དོག་, ईर्ष्या), someone with bodhicitta (བྱང་ཆུབ་སེམས་པ་), gets ahead (ལམ་འགྲོ་རྒྱུག་ / ལམ་ལྷོང་), tight/relaxed (དོག་པོ་ / ལྷོད་པོ་, तंग/सहज), giver (སྦྱིན་བདག་, देने वाला). Thirteen more from Days 151-155 are pending: praise (བསྟོད་པ་, तारीफ़), fame (གྲགས་པ་ and སྙན་གྲགས་, यश), hook (ལྕགས་ཀྱུ་, HI मछली पकड़ने का काँटा, which collides with काँटा for dart), stay even-minded (བཏང་སྙོམས་, समभाव में रहना), the wise (མཁས་པ་), criticize (སྐྱོན་བརྗོད་, आलोचना करना), distraction (གཡེང་བ་, भटकाव), weariness with samsara (སྐྱོ་བ་, विरक्ति), roots of goodness (དགེ་བའི་རྩ་བ་, अच्छे कामों की जड़ें), respect (བཀུར་སྟི་, सम्मान), bondage (འཆིང་བ་, बंधन), very kind (བཀའ་དྲིན་ཆེ་, बड़ी कृपा).
- Open questions the user has not decided (mention again only if relevant):
  - Day 66 category label "(Giving up anger)" / "(क्रोध को त्यागना)" vs the Chapter 6 rule that ཞེ་སྡང་ = hatred / द्वेष. Left unchanged.
  - Day 130 names differ between languages: "Truth Awakening" vs सत्य-बोधि, "Nalendra" vs नालंदा.
  - Hindi category labels "(अच्छे कर्म करना)" / "(बुरे कर्म न करना)" still use कर्म although prose uses अच्छा काम / बुरा काम. The labels are controlled vocabulary, so use them as written.
  - Some approved terms keep an "unclear" note (for example गྭ་རྒྱ་ལྦ་, ཕྱེ་མ་ལེབ, ཚོགས་འདུམས་པ, བློན་པོའི་གཅིག).
- The `pending_terms:` lists in older day files still name terms that are now approved. The termbase rule clears them the next time each file is touched. Do not touch those files just for that.

## Source limits

- **Check Tibetan file sizes first.** Days 1-170 had content on 2026-11-23; later days may still be 0-byte placeholders. Run `wc -c` on a day's Tibetan file before generating. An empty file is a stop: tell the user the Tibetan plan has to be written first. Do not guess content. (An earlier answer said the plan was ready to Day 365 because it counted files, not content.)
- **Commentary lead-in:** use "valuable"/कीमती only where the Tibetan has རིན་ཐང་ཅན (Days 134-140 did, Days 141-150 did not).
- **Hindi glosses:** the first use of बोधिसत्व and बोधिचित्त in each Hindi file carries the bracket gloss. Check this before copying.
- Folder map for the remaining chapters: Chapter 6 = D117-D170, Chapter 7 = D171-D201, Chapter 8 = D202-D274, Chapter 9 = D275-D341, Chapter 10 = D342-D365.

## How the user likes to work

- No git commits. The user commits himself, and a vault backup process also commits on its own. Leave changes uncommitted and list what changed (new files, edited files).
- Plain, simple explanations. Short final report. He often asks follow-up questions to check his understanding, so answer the question asked first.
- If a day file already exists, do not overwrite it. Ask once. For Days 126-133 he chose "audit the existing files", and I fixed them in place.
- When a batch is done, say which days were produced, the real audit findings and fixes, any upstream verse-source problems, and new pending terms.

## Chapter 6 verse-source issues seen (left as is, disclose in the report)

- EN 6-1: em-dash in the verse. HI 6-32: em-dash. EN 6-57 and HI 6-57: em-dash.
- EN 6-48 says "my misdeeds greatly increase through patience", but the Tibetan day commentary (Day 135) says wrongdoing is purified, and HI 6-48 says पाप मिट जाएंगे. The two verse sources disagree. Not edited.
- EN 6-55, 6-60, 6-61 use "misdeeds" and "good deeds"; HI 6-60 and 6-61 use पाप and पुण्य. These are verse-source wordings and stay as they are. Day prose still uses wrongdoing / doing good.

## Starter prompt for the new session

> Read `3-TRANSFORMATIONS/Plans/the-bodhisattva-challenge/HANDOFF-day-generation.md` and the `dalai-lama-plan-translation` skill. Then create the English and Hindi plans for day N-M (check first that the Tibetan files for those days are not empty), following that pipeline, including the adversarial audit. Do not commit anything.
