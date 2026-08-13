# FPS Validator

English | [日本語](README.ja.md)

An iOS test app that renders Metal content while recording frame intervals and an FPS estimate from `CADisplayLink` callback timestamps. Results can be exported as CSV for repeatable comparisons.

## What it measures

The reported FPS represents the frequency of `CADisplayLink` callbacks. It is not a direct count of completed GPU commands or frames physically presented by the display.

The moving-window estimate is:

$$FPS=\frac{n-1}{t_{last}-t_{first}}$$

The CSV includes frame number, timestamp, target FPS, moving-window FPS, frame duration, and drop classification.

## Using the app

- Choose a target supported by the device's maximum refresh rate
- Configure the moving-average window and drop tolerance
- Run repeated measurements for the same duration
- Compare frame-time data and drop counts, not only average FPS
- Use a physical device for final frame-pacing evaluation

Target frame rates are requests to iOS. Thermal state, Low Power Mode, screen content, device capabilities, and other workloads can change the actual callback timing. Simulator results are also affected by the Mac and should not be treated as device display measurements.

## Build

Open `FPSValidator/FPSValidator.xcodeproj` in Xcode and build for a physical iOS device.

## License

The original code is proprietary and all rights are reserved. See [LICENSE](LICENSE).
