# CLAUDE.md — Project Context & Engineering Guidelines for `power-deck`

## 1. Project Overview
`power-deck` is an R&D and manufacturing engineering repository for **custom-shaped, walkable marine photovoltaic-thermal (PV/T) solar panels** that replace deck non-skid surfaces while actively cooling the solar cells and collecting thermal energy via a **`1–2 mm` micro-channel polymer/composite substrate**.

Primary reference document: [`custom-solar-non-skid-pvt-guide.md`](custom-solar-non-skid-pvt-guide.md).

---

## 2. Core Engineering Rules & Material Constraints (Do Not Violate)

When generating designs, calculations, BOMs, CAD/CAM scripts, or manufacturing instructions in this repository, always adhere to these physical and chemical constraints:

1. **Encapsulant Chemistry (POE/TPO Only — Never EVA):**
   * Always specify **POE (Polyolefin Elastomer)** or **TPO** for marine lamination.
   * **Never specify standard EVA (Ethylene Vinyl Acetate):** EVA hydrolyzes under marine moisture and thermal cycling to release **acetic acid**, which corrodes copper interconnects, attacks polycarbonate, and causes delamination.
2. **Walkable Point-Load Armor (Index-Matched E-Glass Scrim):**
   * Include a **`50–100 g/m²` woven E-glass fiberglass scrim** sandwiched inside the top POE encapsulant above the solar cells.
   * Its refractive index (`n ≈ 1.51–1.54`) matches cured POE (`n ≈ 1.49`), keeping optical transmission loss `<1.5%` while preventing cell cracking from heel loads or dropped deck hardware.
3. **Top Non-Skid Wear Layer:**
   * **`200–300 µm` corona/plasma-treated ETFE film**, textured *in-situ* during vacuum lamination using a negative high-temperature silicone/PTFE embossing mat to achieve **DIN 51130 R11/R12** wet traction and low-angle light trapping.
4. **Solar Cell & Interconnect Topology:**
   * Use **Back-Contact (`IBC` / `ABC` / `HPBC`)** silicon cells (`24%–26.5%` efficiency, e.g., *Maxeon Gen 3/6* with solid copper foundation or *Aiko ABC*).
   * To fill curved or trapezoidal non-skid deck islands (`80%–88%` active area coverage), scribe full wafers with a fiber/UV laser into **current-matched fractional strips** (`1/2`, `1/3`, `1/4`, `1/6`, or mitered edge strips).
   * Connect cells via a **CNC-routed Flex-PCB (`FPC`) / tin-plated copper foil backplane** rather than rigid linear tabber ribbons, with **ideal MOSFET or Schottky bypass diodes** every `6–10 cells` to prevent rigging/stanchion shading collapse.
5. **Polycarbonate (`PC`) `1–2 mm` Micro-Channel Substrate Constraints:**
   * **Thermal Physics:** With `1.5 × 1.5 mm` channels separated by `0.5–1.0 mm` ribs and capped by a **`0.38–0.50 mm` (`15–20 mil`) top wall**, heat conducts vertically (`0.4 mm` path) rather than laterally. At `500 W/m²`, $\Delta T_{\text{wall}} \approx 1.0\text{°C}$ and laminar fluid convection $\Delta T_{\text{fluid}} \approx 0.38\text{°C}$ ($h \approx 1,333\text{ W/m}^2\text{K}$).
   * **Mandatory Two-Stage Lamination for Polycarbonate:** Standard solar POE vacuum lamination runs at **`145°C–150°C`** under `0.8–1.0 bar` bladder pressure, which exceeds Polycarbonate's Heat Deflection Temperature (`HDT ≈ 130°C`) and approaches its Glass Transition ($T_g = 147\text{°C}$). **Never put an un-pressurized hollow polycarbonate micro-channel sheet into a `150°C` vacuum laminator or the `1–2 mm` channels will collapse.**
     * *Correct Workflow:* Laminate the `~1.5 mm` non-skid PV top skin at `150°C` in Stage 1; test it electrically; then vacuum-bond it onto the capped polycarbonate micro-channel substrate in Stage 2 at **room temperature (`25°C–50°C`)** using `150 µm` thermally conductive acrylic PSA (`3M 8815`) or `2-part thermally conductive polyurethane`. *(If single-shot `150°C` lamination is required, specify **`PVDF` (`Kynar`)** for the micro-channel substrate).*
   * **Hydrolysis & Environmental Stress Cracking (`ESC`) Prevention:**
     * Keep polycarbonate loop temperatures **`<50°C–55°C`**.
     * Use **pure USP food-grade Propylene Glycol + Deionized Water** (never automotive ethylene glycol containing amine, borate, or silicate inhibitors that craze polycarbonate).
     * Never use **acetoxy-cure (vinegar) silicone** or ketone/ester solvents on polycarbonate; specify **neutral-cure alkoxy silicone**, **polyurethane (`Sikaflex 295 UV`)**, **UV urethane-acrylate (`Loctite AA 3311`)**, or **acrylic PSA**.

---

## 3. CAD/CAM & Software Conventions (Upcoming Tooling)

When writing Python scripts for deck template processing, solar cell packing, or CNC G-code / DXF generation:
* **Coordinate System & Units:** All CAD, DXF, SVG, and G-code files must use **millimeters (`mm`)** in 2D flat-pattern space.
* **Perimeter Safety Margin:** Always inset active solar cells and fluid micro-channels by **`12.0 to 15.0 mm`** from the outer panel cut boundary (and around any interior fastener/hardware cutouts) to preserve a solid, un-channeled waterproof perimeter seal.
* **Electrical String Matching:** All cells wired in series within a single sub-string must have **equal active surface area** (`±1%`) so $I_{mp}$ is matched; cells of different fractional areas must be placed in separate parallel sub-strings or paired with independent MPPT channels.
* **Fluid Channel Geometry:**
  * Standard CNC micro-channel dimensions: **`1.5 mm` to `2.0 mm` channel width**, **`1.2 mm` to `1.5 mm` channel depth**, **`0.8 mm` to `1.0 mm` rib width**.
  * Prefer **bifurcated parallel manifolds** or **multi-lane serpentine paths** that avoid sharp `90°` dead-zones (use minimum `2.0 mm` internal fillet radii) to keep total panel pressure drop `<0.15 bar` at `0.5–1.0 L/min`.
  * Route both `Supply` and `Return` manifolds to a single underside **Delrin/G10 through-deck boss (`35–45 mm` OD)** that also carries the potted rear-exit DC wiring.
