# KLAW — Fabrication & Assembly Files

Production files for **JLCPCB** (PCB fab + PCBA assembly). One reversible board
serves both keyboard halves: the **front face is the right half**, the mirrored
**back face is the left half**.

- **Board size:** ~128 × 97 mm
- **Layers:** 2 (F.Cu / B.Cu), 1.6 mm
- **Assembly:** 7 BOM line items (24 placements/board) machine-assembled, all on
  the front face; LEDs + hot-swap sockets hand-soldered; switches, controller,
  and OLED user-supplied (see `../bom/`).

> These files are committed from the design in `../pcb/klaw_1/`; don't hand-edit
> them. Regenerate with `kicad-cli` after design changes.

## Files

| File | Purpose |
|---|---|
| `klaw_1-gerbers.zip` | Upload to JLCPCB for PCB fab (copper, mask, paste, silk, edge, drills) |
| `klaw_1-BOM.csv` | JLCPCB assembly BOM (Comment / Designator / Footprint / LCSC part #) |
| `klaw_1-CPL.csv` | JLCPCB placement file (Designator / Mid X / Mid Y / Layer / Rotation) |
| `klaw_1-*.gbr`, `klaw_1-*.drl` | The individual layers inside the zip, for inspection |

## Ordering notes

- The BOM/CPL cover the **front-face build (right half)** — all 24 placements on
  Top. For a pair: order the boards from one gerber upload, use assembly for the
  right-half boards, and hand-populate the back face of the others for the left
  half (or place a second assembly order with a bottom-side placement file).
- Through-hole parts (TRRS jack, JST battery connector, encoder) are included in
  the assembly BOM — JLC assembles THT via standard service.
- The buzzer (`C201047`) runs low on stock; check availability before ordering.
- Per-key LEDs (`C2886570`) and hot-swap sockets (`C41430893`) are deliberately
  **not** in the assembly BOM — they're reverse-mount/back-face parts to
  hand-solder; quantities and prices are in `../bom/`.

## Regenerating

```sh
cd pcb/klaw_1
kicad-cli pcb export gerbers -o ../../fab/ \
  --layers F.Cu,B.Cu,F.Mask,B.Mask,F.Paste,B.Paste,F.Silkscreen,B.Silkscreen,Edge.Cuts \
  --no-protel-ext klaw_1.kicad_pcb
kicad-cli pcb export drill --excellon-separate-th -o ../../fab/ klaw_1.kicad_pcb
kicad-cli pcb export pos --format csv --units mm --side both -o pos.csv klaw_1.kicad_pcb
# BOM/CPL: filter to Assembly=JLC parts (see schematic Assembly fields)
```
