---
title: "Sine wave generator using Golang"
date: 2023-03-22T10:55:38+05:30
author: Thila
tags: [go]
categories: ["go"]
# menu: mainx
---

Let's build a continuous [sine wave](https://en.wikipedia.org/wiki/Sine_wave) generator with Go. This could be the first step into creating your ground breaking synthesizer, who knows :) But some theory first before we dive in.

### How digital audio works

We know that sound waves are vibrations travelling through air. These vibrations cause air *pressure* changes from sound source (Generator in our case) to receiver (our ears) over *time*. It is important to note that air particles do not travel with vibrations to receiver. So fundamentally the air pressure and the time is only needed to capture sound from the receiver's perspective. 

Number of pressure variations per second is called the *frequency* of sound. The size of the pressure difference is called the *amplitude* of the sound. Silence is when the sound pressure remains at the atmospheric pressure.

### Sample rate and Nyquist theorem

In order to represent sounds in the digital world, we need to take samples of sound pressure levels at a regular intervals. Number of samples taken per second is known as the *sample rate*. A higher sample rate can accurately represent an audio signal at the cost of increased processing power and memory while a lower sample rate may miss higher frequencies. So it's clear that sample rate depends on the frequency of the signal. Since human hearing is limited to 20hz to 20Khz frequency range, our sample rate should support this range. But how do we know the optimum sample rate for reproducing an audio signal in our audible frequency spectrum? Well *[Nyquist–Shannon sampling theorem](https://en.wikipedia.org/wiki/Nyquist%E2%80%93Shannon_sampling_theorem)* states that it's possible to reproduce any sound if the sampling rate is two times the highest frequency of the source signal. Since most humans cannot listen to frequencies above 20khz, a sample rate of 40khz is theoretically sufficient. But the CD quality standard is defined as 44.1kHz/44000Hz. 

### What we are going to build?

We are going to build a sound wave generator that's going to emit a sine wave with a given frequency continuously in Go.

We will be using [beep](https://github.com/faiface/beep) go package to process audio signals and playback sounds. Beep uses [oto](https://github.com/hajimehoshi/oto) under the hood for audio playback.

### Setting up project

Create a new directory and initialize root module

```bash
go mod init github.com/nuwan89/sine-generator 
```

This will create the root module as follows:

```go
module github.com/nuwan89/sine-generator

go 1.17
```
### Installing Go dependencies

```bash
go get github.com/faiface/beep 
go get github.com/hajimehoshi/oto
```

### Initializing beep

Stick the below code inside the `main` function. Don't worry we'll go through each line.

```go
func main() {
	sr := beep.SampleRate(44100)
	speaker.Init(sr, sr.N(time.Second/10)) // sr.N(time.Second/10) = buffer size for duration 1/10 second
	sine, _ := SineTone(sr, 100)
	speaker.Play(sine)
        select {} // makes the program hang forever
}
```

- Firstly we define the sample rate as 44100Hz.
- Then we initialize the Speaker with the sample rate and the buffer size (one tenth of a second). 
  - Instead of reading samples in real-time, the speaker reads from a buffer with a delay. This allows the audio to be played back in a continuous manner without drop outs and glitches.  
- *SineTone* is a builder function for a Streamer that we are going to implement in which its only job is to stream infinite  sequence of audio *samples*. It needs a sample rate and a frequency (100Hz)
- Finally we can blast audio through the speakers using the given Streamer :)

```go
type SineWave struct {
	sampleFactor float64 // Just for ease of use so that we don't have to calculate every sample
	phase        float64
}

func (g *SineWave) Stream(samples [][2]float64) (n int, ok bool) {
	for i := range samples { // increment = ((2 * PI) / SampleRate) * freq
		v := math.Sin(g.phase * 2.0 * math.Pi) // period of the wave is thus defined as: 2 * PI.
		samples[i][0] = v
		samples[i][1] = v
		_, g.phase = math.Modf(g.phase + g.sampleFactor)
	}

	return len(samples), true
}

func (*SineWave) Err() error {
	return nil
}
```
- *SineWave* is just a struct which stores properties of our sine wave. Phase of the sound wave is basically the current position of the sine wave. This is represented by radians. 

- *Stream* function is the most important one here. It receives a two-dimensional array which we are going to fill. Each dimension represents left and right channel of the signal.
- We are looping through each sample point and calculate the sine value of the phase. 
- We need to factor in frequency for calculating the next phase. 
- `math.Modf` gives us the fractional component so that the phase cycles between 0 - 1

I have copied the SineTone initializer method for the sake of completeness. 

```go

func SineTone(sr beep.SampleRate, freq float64) (beep.Streamer, error) {
	dt := freq / float64(sr)

	if dt >= 1.0/2.0 {
		return nil, errors.New("samplerate must be at least 2 times grater then frequency")
	}

	return &SineWave{dt, 0.1}, nil
}
```
Oh and one more thing.. You need to run tidy command to add import statements in your module file:

```bash
go mod tidy
```

Finally you can run it using:

```bash
go run main.go
```

Enjoy the never ending 100Hz sine wave awesomeness! :)

[GitHub project](https://github.com/nuwan89/sine-wave-generator-example)
