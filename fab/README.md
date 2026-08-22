# KLAW — Fabrication & Assembly Files

Production files for **JLCPCB** (PCB fab + PCBA assembly). One reversible board
serves both keyboard halves — same gerbers, two assembly configurations:

- **Right half** = components on the **Top** (front) face
- **Left half** = the same components at the same locations on the **Bottom**
  (back) face

- **Board size:** ~128 × 97 mm
- **Layers:** 2 (F.Cu / B.Cu), 1.6 mm
- **Assembly per board:** 7 BOM line items, 40 placements, single side —
  including the 17 reverse-mount SK6812MINI-EA LEDs. Hand-soldered: hot-swap
  sockets (component face) and the EC11 encoder (inserted from the keycap side —
  its knob must face away from the component face, so it is deliberately NOT in
  the assembly files). Switches, controller, and OLED are user-supplied (see `../bom/`).
- **Orientation:** the populated face is the keyboard's underside; keycaps and
  the encoder knob go on the unpopulated face. For the left-half order JLCPCB
  shows all parts on the Bottom layer — that is correct, not a mistake.
- LED placement rows use synthetic designators (`LED1`…`LED21`, numbered by key)
  since the LEDs share footprints with the switches; JLCPCB matches them by
  coordinates. The LED bodies protrude ~0.2 mm past the far face — JLC may add
  an assembly-fixture charge for this.

> These files are committed from the design in `../pcb/klaw_1/`; don't hand-edit
> them. Regenerate with `kicad-cli` after design changes.

## How to order a pair

Place **two PCBA orders**, both using the **same** `klaw_1-gerbers.zip`:

| Order | Gerbers | BOM | CPL | Assembly side |
|---|---|---|---|---|
| Right halves | `klaw_1-gerbers.zip` | `right-half/klaw_1-right-BOM.csv` | `right-half/klaw_1-right-CPL.csv` | Top |
| Left halves | `klaw_1-gerbers.zip` | `left-half/klaw_1-left-BOM.csv` | `left-half/klaw_1-left-CPL.csv` | Bottom |

JLCPCB's minimum order is 5 boards / 2 assembled, so a typical pair order is:
2× assembled right + 2× assembled left, with spare bare boards left over.

> **Check the placement preview.** The left-half CPL is the top-side data
> mirrored to the bottom (`Layer=Bottom`, rotation negated). Bottom-side
> rotation conventions vary between tools, so in JLCPCB's "parts placement"
> review screen verify each part sits on its pads with pin 1 matching the
> silkscreen — especially the diodes, TRRS, and encoder — and nudge rotations
> there if needed. JLC's DFM review also flags misalignments before production.

## Files

| File | Purpose |
|---|---|
| `klaw_1-gerbers.zip` | Upload to JLCPCB for PCB fab (copper, mask, paste, silk, edge, drills) — shared by both orders |
| `right-half/` | Assembly BOM + CPL for the right half (Top side) |
| `left-half/` | Assembly BOM + CPL for the left half (Bottom side) |
| `klaw_1-*.gbr`, `klaw_1-*.drl` | The individual layers inside the zip, for inspection |

## Ordering notes

- Through-hole parts (TRRS jack, JST battery connector) are included in the
  assembly BOM — JLC assembles THT via standard service. The encoder is THT but
  hand-soldered (opposite-side part, see above).
- The buzzer (`C201047`) runs low on stock; check availability before ordering.
- Hot-swap sockets (`C41430893`) are the one hand-solder part — they're not in
  the assembly BOM; quantities and prices are in `../bom/`.

## Regenerating

```sh
cd pcb/klaw_1
kicad-cli pcb export gerbers -o ../../fab/ \
  --layers F.Cu,B.Cu,F.Mask,B.Mask,F.Paste,B.Paste,F.Silkscreen,B.Silkscreen,Edge.Cuts \
  --no-protel-ext klaw_1.kicad_pcb
kicad-cli pcb export drill --excellon-separate-th -o ../../fab/ klaw_1.kicad_pcb
kicad-cli pcb export pos --format csv --units mm --side both -o pos.csv klaw_1.kicad_pcb
# right CPL: Assembly=JLC refs, Layer=Top, as exported
# left CPL:  same refs/coords, Layer=Bottom, rotation = (360 - rot) % 360
```
