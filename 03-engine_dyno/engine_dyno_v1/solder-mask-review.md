# Engine dyno IC solder-mask review

Updated 2026-09-05. Target: more than 0.19 mm of nominal solder mask between IC pad openings. All 16 U-designated footprints pass; the smallest opening-to-opening gap is 0.20 mm.

| References | Change | Minimum mask gap, before → after |
| --- | --- | --- |
| U7, U10 (INA827) | Pad width 0.48 → 0.45 mm; explicit zero mask expansion | 0.170 → 0.200 mm |
| U3, U9, U13 | Per-pad mask expansion 0.102 → 0.015 mm | 0.036 → 0.210 mm |
| U4, U14, U15 | Per-pad mask expansion 0.102 → 0.075 mm | 0.156 → 0.210 mm |
| U2 | No change; closest gap is signal pad to exposed pad | 0.200 mm |
| U1, U5, U6, U8, U11, U12, U16 | No change | At least 0.299 mm |

For adjacent pads in a row, mask gap = pitch − copper-pad width − twice the mask expansion. These are physical opening dimensions, not a relaxed DRC threshold. The four corresponding project footprint-library files were also updated.

## Solderability

The [TI INA827 datasheet](https://www.ti.com/lit/ds/symlink/ina827.pdf), DGK0008A package drawing and example layout, specifies 0.65 mm pitch and 0.45 mm-wide lands. The package lead width is 0.25–0.38 mm. The revised pads use that recommended land width, retaining the existing 1.47 mm length and original pad centers. Their paste apertures follow the revised pad dimensions, with no paste reduction. This preserves a usable soldering land; the other changed ICs retain their entire original copper and paste geometry. No negative mask expansion covers the nominal copper land.

This is a nominal CAD/Gerber result, not a guarantee of finished mask dimensions or assembly yield. TI prefers mask openings larger than the copper and notes that mask tolerances vary by fabricator. U7/U10 use zero expansion to meet this target. Before fabrication, ask the PCB manufacturer to confirm that its mask registration and CAM adjustments will preserve the required finished bridge and usable exposed land. A standard 0.05 mm expansion per side would reduce their 0.20 mm gap to 0.10 mm. If the requirement is a guaranteed **finished** bridge above 0.19 mm, manufacturer acceptance is still needed; do not shrink these lands further without a new assembly review.

## Verification and manufacturing files

- KiCad 10.0.5 DRC, with zones refilled in memory: zero errors and zero unconnected items before and after. Both runs reported the same 80 warnings for standard footprint libraries unavailable in the isolated CLI configuration.
- Conservative mask bounding-box distances checked across every pair of mask pads within each U footprint, including the U2 exposed pad. All exceed 0.19 mm.
- Independently checked aperture dimensions and flash coordinates in the exported front-mask Gerber; all 16 U footprints pass the same threshold.
- Verified pad positions and nets are unchanged; only U7/U10 copper widths changed. Verified affected local-library pad sizes and mask overrides match the board.
- Fresh Gerbers were exported with zone checking/refill, plus separate PTH/NPTH drill files. Use `fabrication/engine_dyno_v1_mask_020mm.zip` for this revision. The obsolete pre-correction Gerber archive has been removed.

The board's existing global zero mask expansion and routing rules are retained. The old INA827 clearance-rule comment was corrected to describe the new pad gap. Existing unrelated working-tree changes were preserved. Refill zones after reopening the board in KiCad before subsequent edits or exports.
