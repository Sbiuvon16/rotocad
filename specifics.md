# POLAR & CYLINDRICAL CAD DSL SPECIFICATION

## 1. INITIALIZATION & RESOLUTION

`/head [STEP] [-keep]`
- Initial start (e.g., '/head' or '/head 2000'):
Resets state: theta = 0, r = 0, pen = down, unit = deg.
Defaults to STEP = 1000 angular samples per full revolution (360°).
Step angle formula:
dTheta_step = (2 * PI) / STEP

- Dynamic LOD switch ('/head [STEP] -keep'):
Changes sample density mid-script without resetting cursor, angle,
or existing geometry.

`/tail`
Concludes script parsing and finalizes 2D/3D output generation.

## 2. STATE DIRECTIVES

`/u [deg | rad | <int>]`
Sets coordinate division N for a full revolution (2PI radians):
- deg : N = 360
- rad : N = 2PI (~6.28318)
- `<int>` : N = `<int>` (e.g., `/u 100` sets 1 unit = 3.6°)

Conversion to internal radians:
dTheta_rad = dTheta_input * (2 * PI / N)

`/pen [up | down]`
- up : Moves cursor without creating geometry.
- down : Drawing mode; cursor translation adds vertices/edges.

`/set theta <val>`
Hard-sets cursor angle to `<val>` in active unit space.

`/set r <val>`
Hard-sets base radius to `<val>`. Draws radial bridge line if pen is down.

`/neg [clamp | reflect]`
- clamp : Clamps r < 0 to origin (r = 0).
- reflect : Standard polar inversion: (-r, θ) -> (|r|, θ + PI).

## 3. 2D POLAR PRIMITIVES (r across Δθ)

The final parameter in every primitive is always the angular span (dTheta).

### 2-Parameter Primitives:
`line|<r>|<dTheta>`
Circular arc at constant radius `<r>`.
Formula:
r(φ) = r

`spiral|<r_target>|<dTheta>`
Linear radial interpolation from r_cur to `<r_target>`.
Formula:
r(φ) = r_cur + (r_target - r_cur) * (φ / dTheta)

### 3-Parameter Primitives:
`sin|<amp>|<freq>|<dTheta>`
Harmonic sine modulation.
Formula:
r(φ) = r_cur + amp * sin(freq * φ)

`cos|<amp>|<freq>|<dTheta>`
Harmonic cosine modulation.
Formula:
r(φ) = r_cur + amp * cos(freq * φ)

`inv|<r_base>|<dir>|<dTheta>`
AGMA involute tooth flank generated from base radius `<r_base>`.
dir: 1 (rising flank CCW) or -1 (falling flank CW).
Parametric formulas for roll angle t >= 0:
r(t) = r_base * sqrt(1 + t^2)
θ(t) = dir * (t - atan(t))

## 4. BLOCK REPETITION & LOOPS

`--`
Marks loop block opening.

`-- rep <N>`
Executes enclosed block for a fixed count of `<N>` iterations.

`-- rep ><N>`
Executes enclosed block until cursor angle reaches or passes
threshold `<N>` in the active unit.

## 5. 3D CYLINDRICAL EXTRUSION (/ext)

Extrudes the active 2D polar cross-section along the Z-axis by modulating
radial scale S(z) (percentage slider where 1.0 = 100%) and angular twist:

    (x, y, z) = (
    r_2D * S(z) * cos(θ_2D + twist(z)),
    r_2D * S(z) * sin(θ_2D + twist(z)),
    z_start + z
    )

Syntax:
`/ext <curve_type>|<param1>|[param2]|<dZ> [STEP]`

### 2-Parameter Extrusion Modulators:
`/ext line|<scale>|<dZ> [STEP]`
Constant-scale extrusion.
Formula:
S(z) = scale

`/ext spiral|<end_scale>|<dZ> [STEP]`
Linear radial scale taper (bevel gears, cones, draft angles).
Formula:
S(z) = S_cur + (end_scale - S_cur) * (z / dZ)

`/ext twist|<delta_angle>|<dZ> [STEP]`
Continuous angular twist (helical gears, drill flutes).
Formula:
twist(z) = delta_angle * (z / dZ)

### 3-Parameter Extrusion Modulators:
`/ext sin|<amp>|<freq>|<dZ> [STEP]`
Harmonic radial wave along height (bellows, ribbed sleeves).
Formula:
S(z) = S_cur + amp * sin(freq * z)

`/ext cos|<amp>|<freq>|<dZ> [STEP]`
Harmonic cosine necking (venturis, vases, hourglass shapes).
Formula:
S(z) = S_cur + amp * cos(freq * z)

## 6. COMMENTS & WHITESPACE

`# <text>`
Ignored by parser. Blank lines and surrounding whitespace are stripped.