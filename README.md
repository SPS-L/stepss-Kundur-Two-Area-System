# Kundur Two-Area Test System

RAMSES/STEPSS implementation of the classic Kundur two-area, four-machine test system for small-signal and transient stability studies.

The system comprises two symmetric areas connected by a weak tie, each with two 900 MVA synchronous generators (11 buses, 60 Hz). It is the standard benchmark for inter-area oscillation studies: the included scenario applies a load step and the two dynamic data variants (with and without power system stabilizers) demonstrate the damping of the resulting inter-area mode.

## Repository Files

| File | Description |
|------|-------------|
| `lf.dat` | Power-flow data (buses, lines, transformers, operating point) |
| `dyn.dat` | Dynamic data: machines, `exc_kundur` AVR/PSS, thermal governors, PSS enabled |
| `dyn_noPSS.dat` | Same as `dyn.dat` with the PSS gain (KSTAB) set to zero |
| `disturb.dst` | Disturbance scenario: +0.5 pu active-power step on load L9 at t = 1 s, 60 s horizon |
| `obs.dat` | Observables selection |
| `solveroptions.dat` | Solver settings |
| `sim.cfg` | RAMSES Java GUI simulation configuration (file paths must be adapted locally) |
| `kundur.svg` | One-line diagram template, with `%A`-`%U` placeholder codes substituted by the Helios `1` command |

## Quick Start

With [stepss](https://stepss.sps-lab.org/):

```python
import stepss
case = stepss.cfg()
case.addData('lf.dat')
case.addData('dyn.dat')          # or dyn_noPSS.dat
case.addData('solveroptions.dat')
case.addDst('disturb.dst')
case.addObs('obs.dat')
sim = stepss.sim()
sim.execSim(case)
```

Or run the RAMSES executable directly, adapting the file paths in `sim.cfg`. Run from the repository root so that relative paths resolve.

### One-line diagram

`kundur.svg` is a one-line diagram template in the HELIOS placeholder format, laid out after
the classic two-area scheme. Solving the power flow and rendering the template fills in
every number:

```python
from stepss.helios import HeliosSession

with HeliosSession() as pf:
    pf.load_file("lf.dat")
    pf.solve()
    pf.write_diagram("kundur.svg", "kundur_solved.svg")
```

The same substitution is available from the HELIOS text interface with the `1` command. The
template annotates the voltage magnitude and phase angle at all 11 buses, the active and
reactive output of G1 to G4, the load and the shunt compensation at buses 7 and 9, and the
active and reactive flow at both ends of all 8 lines and 4 step-up transformers. Flows are
given at the bus end shown, positive out of the bus into the branch.

## Status

**Runs.** Both dynamic data variants initialise and simulate.

| Case | Result |
|------|--------|
| `dyn.dat` (PSS in service) | runs to the 60 s `STOP`; inter-area mode near 0.63 Hz, damped at about 6 % |
| `dyn_noPSS.dat` (PSS disabled) | integration stops at t = 36.5 s when `SIM_MINMAXVOLT` sees bus 8 fall through 0.6 pu |

The second is **the expected result, not a failure**: with the stabiliser off the inter-area
mode is unstable, the machines pull out of step, and the monitoring controller in
`solveroptions.dat` halts the run rather than integrating past pole slip. Everything recorded
up to that instant is in the trajectory file and is usable.

`InterAreaOscillations.ipynb` runs both cases and compares them.

Verified against **stepss 3.76** (RAMSES 3.76, HELIOS 1.4.1) on Linux.

## Documentation

The data formats are documented in the STEPSS user guide at [stepss.sps-lab.org](https://stepss.sps-lab.org/).

## Citation

If you use this test system in your research, please cite the original source of the system data:

> P. Kundur, *Power System Stability and Control*, McGraw-Hill, 1994 (two-area system, Example 12.6).

## License

This repository is licensed under the [Apache License 2.0](LICENSE).

## Authors

Developed and maintained by the [Sustainable Power Systems Laboratory (SPS-L)](https://sps-lab.org/) at the Cyprus University of Technology, under the direction of Dr. Petros Aristidou.

RAMSES implementation of the system data by Dr. Thierry Van Cutsem (University of Liège), 2024.
