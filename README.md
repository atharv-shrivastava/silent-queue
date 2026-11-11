# SilentQueue Lab

A browser-based proof of concept for testing whether acoustic measurements can distinguish room conditions and support a **single-room calibrated occupancy estimate**. This is an experiment, not a validated crowd counter.

## Measurement modes

The app provides three modes:

1. **Frequency response** — plays six audible sine tones (250, 500, 1000, 2000, 4000 and 6000 Hz) and estimates relative microphone level at each tone.
2. **Reverberation (RT60)** — plays a 3-second exponential swept sine (120 Hz to 6.5 kHz), records the microphone response, estimates an impulse response using regularized FFT deconvolution, calculates a Schroeder backward-integrated energy-decay curve, and extrapolates an approximate RT60 from a T20 fit over the -5 dB to -25 dB section.
3. **People count demo** — collects reverberation measurements labelled with known occupancy counts, then estimates the nearest matching count for a new measurement using a small nearest-neighbour model.

## People count demo: how to use it

This mode needs calibration data from the **same room and same hardware setup**. Do not move the laptop/speaker or microphone between training and estimation.

1. Select **People count demo**.
2. Set the number of people currently in the room in the calibration input.
3. Click **Record calibration sample** and wait for the sweep to finish.
4. Repeat several times for that same count.
5. Repeat for other known counts, for example 0, 1, 2, 5 and 10 people.
6. Collect at least 4 samples across at least 2 different occupancy levels before estimating. For a useful experiment, aim for 3–5 or more repeated samples at each count.
7. Click **Estimate people in room** to record a fresh sweep and compare its decay profile against stored calibration samples.

Calibration samples are stored in the browser's local storage. Use **Clear training data** to start over. They are not uploaded to a server.

The demo uses a small nearest-neighbour comparison over the estimated RT60/early-decay values and points sampled from the decay curve. It returns the closest calibrated occupancy class. It is intended only to show the end-to-end concept; it is not a validated machine-learning model.

## Run it

Open `index.html` through a local web server or GitHub Pages. Microphone access requires a secure context (HTTPS or localhost).

For a simple local server with Python:

```bash
python -m http.server 5500
```

Then visit http://localhost:5500.

Use the latest Chrome or Edge for the prototype. Reverberation mode uses `MediaRecorder` to capture microphone audio and `decodeAudioData()` to process the recording.

## Experimental procedure

- Use the same laptop, browser, speaker volume and microphone for all measurements.
- Mark speaker/laptop and microphone positions. Do not move either between tests.
- Keep doors, windows and furniture unchanged.
- Keep people in consistent positions during each labelled calibration set.
- Repeat each occupancy condition several times.
- Test the empty room repeatedly to measure normal measurement variation.
- Change only occupancy when testing whether people affect the acoustic response.

## What the readings mean

The frequency-response mode measures the combined **speaker + room + microphone + device processing** path. Its dBFS values are not calibrated sound-pressure levels or an isolated room frequency response.

The reverberation mode estimates a room impulse response by deconvolving the known swept signal from the recorded microphone signal. It uses Schroeder backward integration to create an energy-decay curve. RT60 is extrapolated from a T20 fit. This is a single broadband exploratory estimate, not frequency-band RT60 analysis or a calibrated ISO 3382 measurement.

## Important limitations

- The people-count demo predicts the closest occupancy pattern in its saved calibration samples. It does not detect or track individual people.
- A response change is not proof that occupancy changed. People, furniture, doors, background noise and device processing can all affect readings.
- A different room, laptop, microphone, speaker level or device position requires a new calibration.
- Laptop speakers and built-in microphones vary considerably and may not reproduce the sweep evenly.
- Browser microphone recording may still be affected by operating-system processing, automatic gain, echo cancellation, compression and hardware latency, even when the app requests audio processing to be disabled.
- The displayed count is an **experimental estimate**, not a verified count or a guaranteed accurate result. It may be wrong, especially with few calibration samples or overlapping measurements.
- The sweep is audible. Start at a low volume and do not place speakers close to ears.
- If the decay fit is not long or clean enough, the app reports that an RT60 estimate is unavailable rather than inventing one.

This is a raw PoC. The next milestone is to collect repeated, labelled measurements and test whether occupancy-related changes are distinguishable from normal measurement variation.

## Privacy

Microphone audio is processed in the browser. Raw recordings are not uploaded or retained by the app. Frequency baselines, reverberation baselines and people-count calibration samples are saved in this browser's local storage until cleared.
