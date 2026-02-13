# Cell-migration-Analysis

A MATLAB toolkit for quantitative analysis of 2D and 3D cell migration trajectories using stochastic motion models. Developed by Pei-Hsun Wu, Ph.D. (Johns Hopkins University).

## Overview

This package implements statistical analysis tools for characterizing cell movement patterns through two mathematical models:

- **Persistent Random Walk (PRW)**: Models isotropic cell motion with directional persistence
- **Anisotropic Persistent Random Walk (APRW)**: Extends PRW to capture preferential migration directions by decomposing motion into primary and non-primary components via Singular Value Decomposition (SVD)

The toolkit takes raw cell trajectory coordinates as input and produces fitted model parameters, ensemble statistics, displacement distributions, and publication-quality figures.

## Project Structure

```
APRW package v4/
├── Core Analysis
│   ├── fit_PRW.m            # Fit isotropic PRW model to trajectories
│   ├── fit_APRW.m           # Fit anisotropic PRW model (directional)
│   ├── get_MSD.m            # Mean squared displacement calculation
│   ├── get_ACF.m            # Velocity autocorrelation function
│   ├── get_dR_PDF.m         # Displacement probability distribution
│   ├── get_dtheta_PDF.m     # Angular displacement distribution
│   ├── get_dR_polarity.m    # Displacement vs. orientation (polar plots)
│   └── get_voft.m           # Velocity over time analysis
├── Simulation
│   ├── sim_PRW.m            # Simulate PRW trajectories from fitted params
│   ├── sim_APRW.m           # Simulate APRW trajectories from fitted params
│   ├── sim_tj_prw.m         # Core PRW trajectory generator
│   └── sim_tj_aprw.m        # Core APRW trajectory generator
├── Utilities
│   ├── get_trajfile.m       # Load trajectory data from Excel/MAT files
│   ├── msd2pse0.m           # Fit PRW model to MSD curve
│   ├── ezmsd0.m             # Calculate MSD from displacement data
│   ├── get_rsq.m            # R-squared goodness-of-fit
│   ├── bjff3.m              # Figure formatting utilities
│   ├── delete_extra_sheet.m # Clean up Excel output sheets
│   ├── show_traj.m          # Visualize cell trajectories
│   └── show_traj_visual2.m  # Alternative trajectory visualization
└── Sample Data
    ├── traj 3d_control.xlsx # Control condition trajectories
    └── traj 3d_100nM.xlsx   # 100nM treatment trajectories
```

## Requirements

- MATLAB R2012 or later
- Statistics and Machine Learning Toolbox (`fit`, `fitoptions`, `fittype`)
- Image Processing Toolbox (`cart2pol`)
- Parallel Computing Toolbox (optional, for `parfor` acceleration)

## Input Data Format

Trajectory data should be provided as Excel (`.xlsx`/`.xls`) or MATLAB (`.mat`) files with the following column structure:

| Column | Description |
|--------|-------------|
| 1 | Cell ID |
| 2 | Time frame |
| 3 | X position |
| 4 | Y position |
| 5 | Z position (optional, for 3D) |

Cells may have different starting times but must share the same trajectory duration. The loader automatically normalizes to the minimum trajectory length.

## Quick Start

### Interactive mode (GUI dialogs)

```matlab
% Fit PRW model - opens file picker, analyzes, saves results
outp = fit_PRW();

% Fit APRW model with directional decomposition
outp = fit_APRW();

% Visualize trajectories
show_traj();
```

### Programmatic mode

```matlab
% Load trajectory data
xys = get_trajfile('traj 3d_control.xlsx');

% Set parameters
dt = 0.5;              % Time interval between frames (hours, minutes, etc.)
param.dim = 3;         % Dimensionality (2 or 3)
param.showfig = 1;     % Display figures
param.saveres = 1;     % Save results to Excel

% Calculate mean squared displacement
msd = get_MSD(xys, dt, param);

% Fit PRW model - returns [P, S, SE, R^2, RMSE] per cell
outp = fit_PRW(xys, dt, param);

% Fit APRW model - returns params for primary & secondary directions
outp = fit_APRW(xys, dt, param);
```

### Simulate trajectories from fitted parameters

```matlab
% Generate synthetic trajectories matching model predictions
sim_traj = sim_PRW(fitted_params, dt, param);
sim_traj = sim_APRW(fitted_params, dt, param);
```

## Analysis Functions

| Function | Output | Description |
|----------|--------|-------------|
| `fit_PRW` | P, S, SE, R², RMSE per cell | Fit isotropic persistent random walk model |
| `fit_APRW` | Params for primary & secondary directions | Fit anisotropic model with directional decomposition |
| `get_MSD` | MSD(time lag) per cell + ensemble average | Mean squared displacement over time |
| `get_ACF` | Velocity autocorrelation per cell | Quantify persistence of direction |
| `get_dR_PDF` | Probability distribution of displacement | Characterize step size distributions |
| `get_dtheta_PDF` | Angular displacement distribution | Turning angle statistics |
| `get_dR_polarity` | Displacement vs. orientation | Directional bias (polar plots) |
| `get_voft` | Velocity over time | Temporal velocity profiles |

## Model Parameters

### PRW Model

The PRW model fits mean squared displacement to:

```
MSD(t) = 2D * S^2 * P * (t - P * (1 - exp(-t/P))) + 2D * SE^2
```

| Parameter | Description |
|-----------|-------------|
| **P** | Persistence time - how long a cell maintains its direction |
| **S** | Speed - displacement per unit time |
| **SE** | Localization/positioning error |
| **R²** | Goodness-of-fit |
| **RMSE** | Root mean squared error |

### APRW Model

Extends PRW by decomposing motion into two orthogonal components using SVD:
- **Primary direction (p)**: Dominant migration axis, with its own P, S, SE
- **Non-primary direction (np)**: Perpendicular axis, with separate P, S, SE

## Configuration

All analysis functions accept a `param` structure:

```matlab
param.dim = 2;              % Dimensionality (2 or 3), default: 2
param.showfig = 1;          % Display figures (0/1), default: 1
param.saveres = 1;          % Save results to Excel (0/1), default: 1
param.markertype = 'bo';    % Plot marker style, default: 'bo'
param.outfigurenum = 301;   % MATLAB figure number
```

Additional function-specific parameters:
- `get_ACF`: `param.tlag` - frame lag for autocorrelation
- `get_dR_PDF`: `param.dxmax` (max displacement), `param.binn` (histogram bins)
- `get_dtheta_PDF`: `param.binnum` - angular bins (default: 6)
- `get_dR_polarity`: `param.binnum` - direction bins (default: 25)
- `sim_PRW`/`sim_APRW`: `param.Nmax` (trajectory length), `param.repeat` (replicates)

## Output

Each analysis produces:
- **Excel spreadsheets** with individual cell metrics, ensemble statistics, and fit quality indicators
- **Publication-quality figures** with log-scale axes, formatted labels, and multiple plot types (scatter, line, polar, histogram)

## Sample Data

Two 3D trajectory datasets are included for testing:
- `traj 3d_control.xlsx` - Control condition
- `traj 3d_100nM.xlsx` - 100nM treatment condition

## License

See repository for license information.
