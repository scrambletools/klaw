# KLAW — Fabrication & Assembly Files

Production files for **JLCPCB** (PCB fab + PCBA assembly). One reversible board
serves both keyboard halves — same gerbers, two assembly configurations:

- **Right half** = keycap side is the PCB **front**; machine-assembled parts go
  on the **Bottom** (back) face
- **Left half** = mirror image; machine-assembled parts go on the **Top**
  (front) face

- **Board size:** ~128 × 97 mm
- **Layers:** 2 (F.Cu / B.Cu), 1.6 mm
- **Assembly per board:** 8 BOM line items, 65 placements, **double-sided**
  (JLC Standard assembly). Component face: 18 diodes, 17 reverse-mount
  SK6812MINI-EA LEDs, 17 hotswap sockets. Keycap face: TRRS, buzzer, reset
  switch, encoder.
- **Not assembled:** power switch + JST connector (DNP, wired-first config),
  MCU + OLED (user-supplied, socketed), MX switches + keycaps (plug-in).
  Nothing on the board needs a soldering iron after assembly.
- **Orientation:** the component face is the keyboard's underside. JLCPCB's
  preview shows the right-half order populated on Bottom only and the left-half
  order on Top only — that is correct, not a mistake. Backwards-looking bottom
  silkscreen text in the raw 2D gerber view is also correct (viewer convention).
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
| Right halves | `klaw_1-gerbers.zip` | `right-half/klaw_1-right-BOM.csv` | `right-half/klaw_1-right-CPL.csv` | both (mostly Bottom) |
| Left halves | `klaw_1-gerbers.zip` | `left-half/klaw_1-left-BOM.csv` | `left-half/klaw_1-left-CPL.csv` | both (mostly Top) |

JLCPCB's minimum order is 5 boards / 2 assembled, so a typical pair order is:
2× assembled right + 2× assembled left, with spare bare boards left over.

> **Check the placement preview.** Rotation conventions vary between tools, so
> in JLCPCB's "parts placement" review verify each part sits on its pads —
> diode polarity against the silkscreen arrows, and LED orientation especially —
> and nudge rotations there if needed. JLC's DFM review also flags misalignments.
> Known rotation rules baked into these CPLs (calibrated against JLC's preview —
> re-apply if regenerating): hotswap sockets (HS*) carry a +180° intrinsic tape
> offset on both layers. Bottom-layer rows additionally need +180° over the
> naive (360−θ) mirror ONLY for parts whose footprint has shared/same pads on
> both faces (diodes, TRRS, encoder); parts with per-face mirrored pad patterns
> (LEDs, sockets, buzzer, reset switch) already absorb the flip in the footprint
> and use the plain mirror.

## Files

| File | Purpose |
|---|---|
| `klaw_1-gerbers.zip` | Upload to JLCPCB for PCB fab (copper, mask, paste, silk, edge, drills) — shared by both orders |
| `right-half/` | Assembly BOM + CPL for the right half (Top side) |
| `left-half/` | Assembly BOM + CPL for the left half (Bottom side) |
| `klaw_1-*.gbr`, `klaw_1-*.drl` | The individual layers inside the zip, for inspection |

## Ordering notes

- **Wired-first configuration:** the power switch (PSW1) and JST battery
  connector (BT1) are DNP — the wireless parts return in a future battery
  variant.
- LED (`LED1`…) and socket (`HS1`…) placement rows use synthetic designators —
  they share footprints with the switches; JLCPCB matches by coordinates.
- The 0R configuration resistors (`JP*`) replace KLOR's solder jumpers: each
  order places only its build's subset (keycap-face OLED/trackball set +
  component-face JST-polarity pair), so OLED pin mapping and battery polarity
  are correct per side with no hand bridging. Do NOT add the other side's JP
  rows to an order — the JST pairs are mutually exclusive.
- Confirm C41430893 (sockets) and the THT parts exist in JLCPCB's parts library
  at order time, or pre-order them into your JLC parts account.
- The buzzer (`C201047`) runs low on stock; check availability before ordering.

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
