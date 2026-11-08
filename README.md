Memristor Emulator

An analog circuit that behaves like a memristor. Its conductance depends on the history of the voltage applied across it, so its current-voltage curve forms a pinched hysteresis loop.

Built around an AD633 analog multiplier and TL082 op-amps. Designed in KiCad, simulated in LTspice, measured on an FNIRSI 1014D oscilloscope.

Pinched hysteresis loop

How it works

A memristor relates charge and flux. This emulator is flux-controlled: its conductance is set by the integral of the voltage across it.

Stage	Part	What it does
Buffer	U1A (TL082)	Copies V_in without loading the device terminals
Flux integrator	U1B (TL082), R1 + RV1, C1	φ = −(1 / (R_int · C1)) ∫ V_in dt
Multiplier	U2 (AD633)	V_W = V_in · φ / 10
Memristive branch	R3 (Rs)	I = (V_in − V_W) / Rs
Current sense	U3A (TL082)	V_ISENSE = I · Rs (1 V per mA)

Combining the stages gives:

I = V_in · (1 − φ/10) / Rs
G(φ) = (1 − φ/10) / Rs

The conductance rises and falls with the integrated input. Because current is zero whenever voltage is zero, the I–V loop always pinches at the origin. The loop narrows as frequency increases, since φ has less time to build up each cycle. This is the signature memristor behavior.

R2 (1 MΩ) across C1 lets DC leak off so the integrator doesn't drift into saturation.

Hardware
Board: 100 × 70 mm, 2 layers, all through-hole, GND pour on both sides
Supply: ±12 V through a screw terminal, with reverse-polarity diodes and a power LED
ICs: in DIP-8 sockets
RV1: multi-turn trimmer to tune the integrator time constant (about 0.5–5.5 ms)
Outputs: header J3 (V_BUF, I_SENSE, φ, GND) plus test points

The full parts list is in fab/BOM.csv.

Repo layout
memristor_emulator.kicad_pro   KiCad project (open this)
memristor_emulator.kicad_sch   schematic
memristor_emulator.kicad_pcb   board layout
memristor.kicad_sym            custom AD633 symbol
fab/                           Gerbers, drill files, BOM
docs/                          schematic PDF, board renders, plots
Measuring the loop
Power the board with no ICs installed. Check the supply pins on each socket.
Install the ICs and set RV1 to mid-travel (τ ≈ 1 ms).
Apply a 2 Vpk sine to J1 at about 100 Hz.
Put the scope in XY mode: CH1 → J3-1 (V_BUF), CH2 → J3-2 (I_SENSE).
Sweep the frequency and watch the lobes shrink.

Keep the φ peak (J3-3) under ~8 V so G stays positive. It scales as:

φ_peak ≈ A / (2π · f · R_int · C1)

Lower the amplitude, raise the frequency, or turn up RV1 if φ gets too large.

Ideas to try next
Plot loop area vs. frequency and compare it to the ideal model
Measure how AD633 offset and integrator leakage distort the loop
Add a nonlinear (φ²) term for a different memductance curve
Connect two emulators in series or parallel
