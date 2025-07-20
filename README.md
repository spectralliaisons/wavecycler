# wavecycler
## _Analyze, audition, and export wavecycles for the UDO Super 6 or other wavetable synths._

Wavecycles can be obtained from functions here or imported from audio files e.g. to re-sample a patch you created on a synth or some other sound source to be played on another synth.

This means you can record whatever note you want, manually edit the file to trim one wavecycle out of it, then interpolate it here to be the desired number of samples without changing the pitch.

## Configuration
[`wavecycler.ipynb`]("./wavecycler.ipynb") is configured to export files for the UDO Super 6 but can be modified to support any wavetable synth--your synth's manual will tell you how many samples are expected, and the file format.:
- In `wavecycler.ipynb` set `nsamps` to the expected number of samples that must constitute one wavecycle.
- By default `save()` calls `write_ws6()` but you can create a new function to save in a different format. "ws6" is an arbitrary file extension for what the UDO Super 6 requires: raw headerless 16-bit signed integer audio sample values.

## Functionality

### Waveform generator functions
- `interpolated`: interpolate (re-sample) an audio file to meet the target number of samples.
- `sin`: sine wave
- `saw`: sawtooth wave
- `tri`: triangle wave
- `sqr`: square wave
- `sawsqr`: sawtooth + square wave.
- `sawtri`: sawtooth + triangle wave.
- `fm_multi`: multi-operator FM synthesis.
- `fm_parallel`: multi-operator FM synthesis with parallel modulators.

### Operations on waveform generator functions
- `mult`: multiply two waveform generator functions
- `add`: add two waveform generator functions

### Plotting
- `compare`: overlay plots of wavecycles and their spectrograms

### Exporting
- `save`: export waveform generator function results as UDO Super 6 wave data

Inspect wavecycler.ipynb for additional functionality like filtering, ADSR envelopes, transforming audio files into their loudness contours, and more.

## Examples

### Compare sawtooth, triangle, and square waves
`compare([saw, tri, sqr()])`

### Compare a sawtooth to an imported audio file
`compare([saw, interpolated("./examples/MajicalHornz.wav")])`

### Compare a sawtooth to a nice warm horn waveform
`compare([saw, sawtri(0.5, 1, 0.8)])`

### Demonstrate that multiplying a sawtooth with a square wave results in a triangle wave
`compare([tri, mult(saw, sqr())])`

### Compare a sawtooth to an FM waveform
`compare([saw, fm_multi([(1.0, 10.0)])])`

`compare([saw, fm_multi([(1.0, 4.0), (1.0, 1.0), (0.5, 0.0)])])`

### Save an audio file as an UDO Super 6 wavecycle after interpolating it to match the required number of samples
`save(interpolated("./examples/MajicalHornz.wav"), fs/nsamps, 1, "./examples/MajicalHornz")`

## Dependencies
```
pip install notebook
pip install numpy
pip install matplotlib
pip install scipi
pip install pandas
```

## Known Issues
Change the file extension of a saved interpolated file to ".raw" (raw audio file) and append it onto itself. You'll likely see that the waveform has a little notch or jump--it doesn't smoothly transition from the end to the beginning. This introduces a brief (nonharmonic? or at the fundamental frequency?) artifact. Your synth will likely pass this through a low-pass filter anyways though. I believe this occurs because interpolation changes the number of samples that constitute one wavecycle--introducing or eliding samples in between each of the original samples--but the very first and last sample values remain the same as the original file, so the slope between them is the same as the original file. TLDR: all other sample values change to fit the new format _expect_ for the first and last samples.
