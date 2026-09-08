# VEQPy 1.3.5 — Vacuum GS GEQDSK exterior continuation

VEQPy 1.3.5 corrects the default LCFS-exterior flux written by
`Equilibrium.to_geqdsk()`. The CHEASE-style ray extrapolation introduced in
1.3.4 is replaced by a current-free Grad-Shafranov solve on the rectangular
GEQDSK grid.

This maintenance release is based directly on VEQPy 1.3.4. It does not include
the later solve-map JVP, native toroidal-flux-radius source closure, or source
API migration currently present on the main development branch.

## Highlights

### Current-free exterior solve

- The exterior satisfies the vacuum Grad-Shafranov equation
  `d2psi/dR2 - (1/R) dpsi/dR + d2psi/dZ2 = 0`.
- Cut-edge stencils impose zero normalized flux increment on the exact
  polygonal LCFS rather than on the nearest rectangular grid nodes.
- Controlled positive Dirichlet data on the rectangular box leaves a narrow
  closed-flux collar near the LCFS and opens the far exterior.
- Normalized exterior topology is independent of the sign of the physical
  poloidal-flux span.
- The solve no longer requires flux surfaces to be star-shaped about the
  magnetic axis.

### Compatibility

- Python 3.12 or newer is required.
- No public symbols or parameters are removed or renamed relative to VEQPy
  1.3.4.
- Calls that omit `psi_outside` now receive the vacuum Grad-Shafranov
  continuation.
- Passing `psi_outside=<physical psi>` explicitly retains the scalar exterior
  override.
- `Geqdsk.boundary` remains the exact, explicitly closed LCFS polygon.

### Scope

The rectangular-box Dirichlet values are controlled export boundary data. They
are not reconstructed from external coil currents, so this continuation is not
a free-boundary equilibrium reconstruction.

## Validation

- Ruff static checks passed.
- The complete local test suite reported 360 passed and 16 conditionally
  skipped tests, with one expected non-finite-input warning.
- An asymmetric analytic LCFS test covers both signs of the physical flux span,
  verifies positive exterior flux, and checks that far-exterior contours open
  at the rectangular box.
- The maximum absolute discrete vacuum Grad-Shafranov residual away from the
  LCFS and box is below `2.0e-12` in the qualification case.
- Explicit `psi_outside` scalar compatibility and asymmetric GEQDSK write/read
  round trips passed.
- The source distribution and wheel passed metadata checks. An isolated Python
  3.12 wheel installation imported version 1.3.5 outside the source tree,
  produced normalized exterior flux from `1.000000001` to `1.100000001`, and
  retained the explicit scalar override.

**Full changelog:** [v1.3.4...v1.3.5][full-changelog]

[full-changelog]: https://github.com/FusionAlpha/veqpy/compare/v1.3.4...v1.3.5
