# Repository Guidelines

## Project Structure & Module Organization
This is an STM32F103C8 field-oriented-control firmware project generated with STM32CubeMX and built with Keil MDK-ARM.

- `Src/` and `Inc/`: CubeMX-generated application, peripheral initialization, interrupts, and public headers.
- `MDK-ARM/`: Keil project files plus hand-written FOC, PID, current-sensing, encoder, and math modules (`foc_*.c`, `pid.c`).
- `Drivers/`: vendored STM32F1 HAL and CMSIS sources; avoid editing these unless updating the vendor package.
- `20251211_FOC.ioc`: authoritative CubeMX hardware configuration.
- `MDK-ARM/20251211_FOC/`: compiler output; treat generated binaries and object files as build artifacts.

Keep each module’s `.c` and `.h` files paired. Place reusable motor-control logic in `MDK-ARM/`; keep board/peripheral glue in `Src/` and `Inc/`.

## Build, Test, and Development Commands
From the repository root in PowerShell:

```powershell
& 'D:\App\Keil\Keil_v5\UV4\UV4.exe' -b '.\MDK-ARM\20251211_FOC.uvprojx'
& 'D:\App\Keil\Keil_v5\UV4\UV4.exe' -r '.\MDK-ARM\20251211_FOC.uvprojx'
```

The first command performs an incremental build; the second rebuilds all sources with ARM Compiler 6.22. Open `20251211_FOC.ioc` in STM32CubeMX when changing clocks, pins, DMA, or peripherals, then regenerate code. Keep custom changes inside `/* USER CODE BEGIN */` blocks so regeneration preserves them.

## Coding Style & Naming Conventions
Use C11-compatible embedded C, four-space indentation in hand-written modules, braces on the next line, and fixed-width types such as `int32_t`. Follow existing names: `FOC_ParkTransform()` for public functions, `PID_Controller` for types, `PWM_Period` for established configuration symbols, and `UPPER_SNAKE_CASE` for new macros. Document units and scaling explicitly (for example, Q10 or radians × 1000). Avoid heap allocation and blocking work inside interrupts.

## Testing Guidelines
No automated test framework is present. Every change must compile without new warnings. For control changes, perform a hardware smoke test covering startup, encoder direction/offset, current sampling, PWM enable/disable, and UART telemetry. Record the board setup, supply voltage, motor, and observed behavior in the pull request.

## Commit & Pull Request Guidelines
Git history is not included in this repository snapshot, so no existing commit convention can be inferred. Use short, imperative subjects, optionally scoped, for example: `foc: clamp q-axis voltage command`. Keep generated-code changes separate from control-algorithm changes. Pull requests should explain the motivation, affected peripherals/modules, build result, hardware-test evidence, linked issue, and any CubeMX regeneration or configuration changes.
