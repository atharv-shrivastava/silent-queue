# SilentQueue Lab

A tiny browser-based proof of concept for testing whether a room's acoustic response changes between two measurements.

## Two modes

1. **Set room baseline** — measure six test tones and save the relative response in this browser.
2. **Check room** — repeat the test and compare the new response against the saved baseline.

## Run it

Open `index.html` through a local web server or GitHub Pages. Microphone access requires a secure context (HTTPS or localhost).

For a simple local server with Python:

```bash
python -m http.server 5500
```

Then visit http://localhost:5500.

## Experimental procedure

- Use the same laptop and browser for both measurements.
- Keep speaker volume fixed.
- Keep the laptop/speaker and microphone position fixed.
- Run the baseline in the room's chosen reference condition.
- Change only one variable at a time for each experiment (for example, occupancy).
- Repeat each measurement several times and keep notes.

## What it measures

The app plays six audible sine tones (250, 500, 1000, 2000, 4000 and 6000 Hz). It reads microphone samples and uses a Goertzel calculation to estimate the relative energy at each test frequency. It compares the current dBFS readings with the saved baseline.

The readings represent the combined **speaker + room + microphone + device processing** path. They are not calibrated sound-pressure measurements or a true isolated room frequency response.

## Important limitations

- It does not count people or estimate an exact occupancy percentage.
- A response change is not proof that occupancy changed. Furniture, doors, device position, background noise, speaker level, automatic audio processing and other conditions can affect the result.
- Laptop speakers and built-in microphones vary considerably.
- Browser echo cancellation and operating-system audio processing can still affect measurements, even though the app requests these features to be disabled.
- The test tones are audible. Start with a low system volume and do not place speakers close to ears.

This is an experimental PoC, not a validated crowd-monitoring product. The next milestone is to collect repeated measurements under controlled occupancy conditions and see whether the differences are consistent and distinguishable.

## Privacy

Microphone data is processed in the browser. The app does not upload audio. The saved baseline stays in the browser's local storage until cleared.
