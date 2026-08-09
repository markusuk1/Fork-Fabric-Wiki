# Replacement World-Shell Pipeline

> Sources: SHELL-001 proof, SHELL-002 clean GOTY world, SPAWN-001 explorer launch, BRIDGE-002 greeting correction, NPC-001 idle correction, INTERACT-001 native-crime evidence and PERCEPT-001 combat evidence, 2026-08-09
> Raw: SHELL-001 Proof, SHELL-002 Clean GOTY Evidence, Village Spawn Decision, Seyda Neen Launch Verification, Greeting Fault Diagnosis, Greeting Runtime Verification, NPC-001 Evidence, INTERACT-001 Evidence
> Commit: unknown
> Updated: 2026-08-09

## Current baseline

The project can reproducibly derive clean replacement Morrowind, Tribunal, and
Bloodmoon masters from the user's owned GOG GOTY data. The generated world
retains terrain, cells, pathgrids, buildings, doors, containers, activators,
ordinary object definitions, and original asset references. It contains no
legacy world characters, creatures, actor spawn lists, actor placements,
legacy dialogue/journals, executable legacy scripts, book prose, or ownership.
It generates empty logical `Hello`, `Idle`, `Thief` and `Alarm` voice-dialogue
containers because native NPC ambient and crime evaluation require those engine
substrates even when no authored response exists.

Generated YAML and masters are ignored, local-only Bethesda derivatives. Git
stores only source pins, transformation policy, implementation, tests, and
evidence.

These engine-facing exceptions are intentional:

- `NPC::player` and Tribunal's override are retained as player templates, not
  placed world NPCs.
- Bethesda's `Script::Main` is replaced with an inert project-generated
  `begin Main / end Main` stub because OpenMW requests a global Main script.
- Each master's `Dialogue::Hello` override is replaced with a generated
  `Voice` container whose INFO map is empty. No Bethesda greeting survives.
- Each master's `Dialogue::Idle` override is replaced with the same empty
  `Voice` shape. No Bethesda idle line survives.
- The base master's `Dialogue::Thief` and `Dialogue::Alarm` are replaced with
  empty `Voice` shapes. These keys let native crime mechanics run without
  retaining any legacy response, voice selection, actor or quest chain.
- Every master's `Dialogue::Attack` and `Dialogue::Hit` overrides are replaced
  with the same empty shape. Real actor Hit/Died operation requires these
  logical combat-response keys even when no response line is authored.

## Repeatable flow

1. Place the three owned masters under `.generated/world-shell/source/` and
   verify the SHA-256 pins in `shell-002-clean-world.json`.
2. Convert each to inline DeltaPlugin YAML under its canonical basename:
   `Morrowind`, `Tribunal`, and `Bloodmoon`.
3. Run `build-clean-world-shell.py inventory`. It constructs one
   case-insensitive union of actor/spawn IDs across all masters and reports
   exact per-master mutation counts.
4. Run `build`. It refuses output unless every source hash and expected count
   matches, then writes all three clean YAML files.
5. Exact declared engine records are generated: inert `Main` plus empty
   `Hello`, `Idle`, `Thief`, `Alarm`, `Attack` and `Hit` dialogue keys where
   present in the canonical masters.
6. Reverse-convert each YAML under the same canonical basename and expose the
   `.omwaddon` binary as the corresponding `.esm` in ignored clean storage.
7. Convert the generated binaries back to inline YAML and run `verify`. This
   checks the source actor/spawn union, all forbidden record/field conditions,
   the generated Main, and every retained record-type count.
8. Run representative OpenMW cell checks and the village encounter-duration
   check in `scripts/test-village-runtime.ps1`.

```text
python scripts\build-clean-world-shell.py inventory --manifest config\world-shell\shell-002-clean-world.json --source-root .generated\world-shell\source
python scripts\build-clean-world-shell.py build --manifest config\world-shell\shell-002-clean-world.json --source-root .generated\world-shell\source --output-root .generated\world-shell\clean
python scripts\build-clean-world-shell.py verify --manifest config\world-shell\shell-002-clean-world.json --source-root .generated\world-shell\source --clean-root .generated\world-shell\roundtrip
```

DeltaPlugin conversion must retain canonical basenames. Embedded cell-reference
namespaces include master names, and renaming can cause skipped references.

## Transformation contract

The builder removes these record types, except declared generated/engine
templates:

- `NPC`;
- `Creature`;
- `LevelledCreatures`;
- `Dialogue` (including journal/dialogue content represented by the record);
- `Script`; and
- `Startup`.

Declared `replace_records` bodies are emitted exactly and audited after binary
round trip. `Dialogue::Hello`, `Dialogue::Idle`, `Dialogue::Thief`,
`Dialogue::Alarm`, `Dialogue::Attack` and `Dialogue::Hit` are the only Dialogue
exceptions. All three source overrides of Hello/Idle/Attack/Hit and the base
Thief/Alarm records become `type: Dialogue`,
`dialogue_type: Voice`, `info: {}`. Retaining an original record is forbidden
because its INFO chain carries legacy actors, conditions, voice associations
and prose.

It then removes every cell reference whose `object_id` matches any NPC,
creature, or levelled-creature ID found anywhere in the three-source load order.
This cross-master union matters because an expansion can place an actor defined
by an earlier master.

Retained records are sanitized as follows:

- all top-level object `script` attachments become empty;
- Book `text` becomes empty;
- cell-reference `owner` becomes empty; and
- cells whose last reference was removed use `references: {}`.

Do not delete those schema-fixed fields. DeltaPlugin rejects some record shapes
when Book `text`, object `script`, or cellref `owner` is absent. Empty values
remove their semantics while preserving a valid round trip. A bare
`references:` is YAML null, not an empty map.

## Verified scale

SHELL-002 pins a 3,610-ID actor/spawn union and removes:

- 3,047 non-player NPC records;
- 432 creatures;
- 174 actor spawn lists;
- 4,097 legacy dialogue/journal records, while replacing fourteen declared
  Hello/Idle/Thief/Alarm/Attack/Hit records with empty engine-only containers;
- 1,230 legacy scripts and four expansion startup registrations; and
- 12,276 actor/spawn placements.

It clears 1,198 script attachments, 663 Book text values, and 42,338 ownership
values. The binary round trip has no semantic violations or retained-count
drift. World counts such as cells, landscapes, pathgrids, statics, doors,
containers, and activators are unchanged.

## Runtime policy

The clean load order is generated `Morrowind.esm`, generated `Tribunal.esm`,
then generated `Bloodmoon.esm`, with `builtin.omwscripts` supplied by OpenMW.

Once Bethesda's character-generation scripts are gone, `--new-game=1` is not a
valid exact-cell acceptance path: it enters the removed new-game sequence and
defaults the player to exterior `(0,0)`. Automated cell checks use an unquoted
profile `start=<cell>` plus:

```text
--skip-menu=1 --new-game=0
```

This bypasses legacy chargen and dumps the player into the requested cell.
SHELL-002 verified:

- `Seyda Neen, Census and Excise Office`;
- `Mournhold, Great Bazaar`; and
- `Skaal Village, The Greathall`.

The project must build its own character creation, initial spawn, gameplay
scripts, NPCs, quests, dialogue content, and living-world behavior on this
baseline. Project NPCs inherit the empty ambient and crime dialogue substrates
safely.

## Default explorer launch

The user-facing desktop entry is `OpenMW - Empty Seyda Neen.lnk`. It invokes
`scripts/launch-clean-world.ps1`, which validates the installed OpenMW 0.51
binary, resources, profile template, and all three generated clean masters
before opening the game.

The launcher copies `config/world-shell/explorer-openmw.cfg` into ignored
runtime storage. Its start value is deliberately unquoted:

```text
start=Seyda Neen
```

The launch keeps normal sound, input capture, the user's display/GUI settings,
and conservative water baseline. It supplies clean Morrowind, Tribunal, and
Bloodmoon in order and uses `--skip-menu=1 --new-game=0`.

The verified result is exterior `Seyda Neen (-2, -9)`, followed by its
neighboring Bitter Coast and Seyda Neen cells. The process remained healthy for
15 seconds with no engine error, fatal, or missing-script log entry.

Keep fixed explorer spawn policy in the native profile rather than adding a
teleport to the inert Main. Replace it only when project-owned character
creation and spawn selection are ready.

## Validation policy

Completion requires all of the following:

- exact source hashes and mutation counts;
- transformer success and expected-count refusal tests;
- clean YAML-to-binary conversion with no skipped references;
- binary-to-YAML round-trip semantic audit;
- unchanged retained record-type counts; and
- normal OpenMW runtime checks in representative cells across all three masters.

Strict whole-content legacy script compilation is no longer useful for the
clean shell because legacy scripts are absent. Runtime logs should have no
missing-script, engine error, or fatal entry in the target checks.

An NPC check must run beyond the greeting and idle delays. A startup-only smoke
can miss the engine lookup: the first real Ralen encounter produced 5,244
missing-Hello frame errors, and the first four-actor NPC-001 regression later
exposed a missing-Idle error near the idle-chatter delay.
The first real PERCEPT-001 combat run likewise exposed missing `Attack` and
`Hit` engine lookups; runtime acceptance involving damage/death must reject
either missing-dialogue path.
`test-village-runtime.ps1` waits 25 seconds by default, requires four
`villager_ready` markers, rejects missing-Hello, missing-Idle, engine/fatal and
Lua errors, reports CPU consumption, and stops only the isolated processes it
started.

## Distribution and reuse boundary

The generated masters remain derivatives of Bethesda data. They can be used
locally by the owner but cannot be committed or distributed. A distributable
product needs independently licensed assets/content or a user-side generation
step that consumes a legally owned installation.

Non-narrative definitions remain intentionally available for local project
reuse. Quest-specific ordinary objects may still exist as inert world objects,
and semantic labels outside Dialogue and Book text can retain Bethesda names.
Future cleanup should be goal-driven rather than deleting world/object data
that the new project may use.

## See Also

- [OpenMW Ecosystem Reuse Matrix](../architecture/ecosystem-reuse-matrix.md)
- [OpenMW Capability Baseline](capabilities.md)
- [Fork and OpenMW Capability Matrix](../architecture/capability-matrix.md)
