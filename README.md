# Kundur Two-Area Test System

RAMSES/STEPSS implementation of the classic Kundur two-area, four-machine test system for small-signal and transient stability studies.

The system comprises two symmetric areas connected by a weak tie, each with two 900 MVA synchronous generators (11 buses, 60 Hz). It is the standard benchmark for inter-area oscillation studies: the included scenario applies a load step and the two dynamic data variants (with and without power system stabilizers) demonstrate the damping of the resulting inter-area mode.

## Repository Files

| File | Description |
|------|-------------|
| `lf.dat` | Power-flow data (buses, lines, transformers, operating point) |
| `dyn.dat` | Dynamic data: machines, `exc_kundur` AVR/PSS, thermal governors — PSS enabled |
| `dyn_noPSS.dat` | Same as `dyn.dat` with the PSS gain (KSTAB) set to zero |
| `disturb.dst` | Disturbance scenario: +0.5 pu active-power step on load L9 at t = 1 s, 60 s horizon |
| `obs.dat` | Observables selection |
| `solveroptions.dat` | Solver settings |
| `sim.cfg` | RAMSES Java GUI simulation configuration (file paths must be adapted locally) |

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
