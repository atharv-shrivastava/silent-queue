# SilentQueue Lab

A browser-based proof of concept for testing whether acoustic measurements can distinguish room conditions. This is an experiment, not a validated crowd counter.

## Measurement modes

The app has two measurement methods. Each method has its own saved baseline and check workflow.

1. **Frequency response** — plays six audible sine tones (250, 500, 1000, 2000, 4000 and 6000 Hz) and estimates the relative microphone level at each tone using a Goertzel calculation.
2. **Reverberation (RT60)** — plays a 3-second exponential swept sine from 120 Hz to 6.5 kHz, records the microphone response, estimates an impulse response using regularized FFT deconvolution, and calculates a Schroeder backward-integrated energy-decay curve. It fits the -5 dB to -25 dB part of the decay (T20) and extrapolates an approximate RT60.

For either method:
- **Set room baseline** saves the current measurement in this browser.
- **Check room** repeats the measurement and compares it with that method's saved baseline.

## Run it

Open `index.html` through a local web server or GitHub Pages. Microphone access requires a secure context (HTTPS or localhost).

For a simple local server with Python:

```bash
python -m http.server 5500
```

Then visit http://localhost:5500.

Use the latest Chrome or Edge for the prototype. The reverberation mode uses `MediaRecorder` to capture the microphone and `decodeAudioData()` to process the recording.

## Experimental procedure

- Use the same laptop, browser, speaker volume and microphone for baseline and check.
- Mark the speaker/laptop and microphone positions. Do not move either between tests.
- Keep doors, windows and furniture unchanged.
- Run at least five repeated measurements for each occupancy condition.
- Change only one variable at a time, such as the number of people.
- Test the empty room repeatedly too. This reveals normal measurement variation.

## What the readings mean

The frequency-response mode measures the combined **speaker + room + microphone + device processing** path. Its dBFS values are not calibrated sound-pressure levels and are not an isolated room frequency response.

The reverberation mode estimates a room impulse response by deconvolving the known swept signal from the recorded microphone signal. It then uses Schroeder backward integration to create an energy-decay curve. RT60 is extrapolated from a T20 fit over the -5 dB to -25 dB section. The fit quality and usable fit window are shown in the app.

## Important limitations

- The app does not count people or estimate an exact occupancy percentage.
- A change in RT60 or frequency response does not prove that occupancy changed. People, furniture, doors, background noise and device processing can all affect readings.
- This is a single broadband exploratory measurement, not frequency-band RT60 analysis.
- Laptop speakers and built-in microphones vary considerably and may not reproduce the sweep evenly.
- Browser microphone recording may still be affected by operating-system processing, automatic gain, echo cancellation, compression and hardware latency, even though the app requests echo cancellation, noise suppression and automatic gain control to be disabled.
- The result is not a calibrated ISO 3382 measurement. Treat it as a comparative experiment using the same hardware and setup, not as an absolute acoustic measurement.
- The sweep is audible. Start at a low volume and do not place speakers close to ears.
- If the decay fit is not long or clean enough, the app reports that an RT60 estimate is unavailable rather than inventing one.

This is an experimental PoC. The next milestone is to collect repeated measurements in controlled occupancy conditions and test whether the changes are larger than normal measurement variation.

## Privacy

Microphone audio is processed in the browser. Raw recordings are not uploaded or retained by the app. Baselines are saved in this browser's local storage until cleared.
