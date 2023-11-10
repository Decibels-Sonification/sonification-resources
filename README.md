# Sonification Resources

Data sonification tips, tools, and techniques by [Decibels](https://decibels.community/), a data sonification community. This is a community-built resource. If you'd like to contribute then either submit [a Github issue](https://github.com/Decibels-Sonification/sonification-resources/issues), file a [pull request](https://github.com/Decibels-Sonification/sonification-resources/pulls), or approach us in our [Discord](http://decibels.community) server.

## What is sonification?

Sonification is the practice of turning data into sound, and even music. Just as data can be visualised by transforming it into bars, lines and colours, it can also be sonified – turned into pitches, volume levels and audio effects. Using sonification techniques, you can tell stories with sound, add audio to visualizations, or even create pieces of music, all driven by data.

## Methods of sonification

In 2002, sonification researcher Thomas Hermann [proposed](https://sonification.de/son/techniques/) five categories of different sonification techniques, which we like to simplify down into three.

**Audification** is when you get a bunch of data, and use time-stretching and pitch-shifting to get it into a range that can be heard by the human ear. This is often used by scientists and researchers as an alternative way to interrogate data (especially large amounts of data), it's less commonly used in science communication work, but [Nasa is a fan](https://www.nasa.gov/feature/goddard/2018/sounds-of-the-sun/).

**Earcons** and **auditory icons** are short little audio clips that represent events. Like phone notifications, or the blips of a barcode scanner in a supermarket. In Herman’s explanation, earcons are just abstract pings and bloops, while auditory icons sound like the thing they represent. A cash register cha-ching representing a sale, for example. This can be a simple but very effective way to sonify event data or an important change in state.

Finally, there's **parameter mapping**, which is what most data folks think of when they hear the word sonification. This is where you map data values onto different audio properties or dimensions - which we call sonic variables.

Examples of all of these different sonification types can be found on the [Data Sonification Archive](https://sonification.design/).

## Tools for sonification

The below is a non-exhaustive list of tools that our community members have used in the past for data sonification. Most of these tools are general music-making programs, but also lend themselves well to sonification work.

### Code

- **[Sonic Pi](https://sonic-pi.net/)** - a code-based music creation and performance tool created by Sam Aaron. The syntax is based on the Ruby coding language and built on top of Super Collider.
- **[p5 Sound](https://p5js.org/reference/#/libraries/p5.sound)** - a Javascript library from the p5.js ecosystem that provides useful methods for accessing the [Web Audio API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API).
- **[Tone.js](https://tonejs.github.io/)** - a Web Audio framework written in Javascript for creating interactive music in the browser.
- **[SuperCollider](https://supercollider.github.io/)** - Am open source platform for audio synthesis and algorithmic composition, used by musicians, artists and researchers working with sound. Very flexible and acts as the sound engine for many other audio applications and code libraries (ie see Sonic Pi above).

### Software

- **[Datasonifyer](https://datasonifyer.de/en/)** - Datasonifyer is a web app that lets you map data to frequency, amplitude, filter cutoff and more. It's free to use, and friendly for newcomers.
- **[Holon.ist](https://holon.ist/)** - Holon.ist is a system for flexible processing of sensor data into MIDI and OSC. Available for iPhone and iPad as a Universal iOS app. Connect your studio hardware and favourite music apps, VJ or lighting software, or make your own sounds with embedded Pure Data.
- **[Loud Numbers (VCV)](https://library.vcvrack.com/LoudNumbers/LoudNumbers)** - a plugin for the VCV Rack modular synthesizer simulator that lets you load a CSV file and convert the values to control voltages that you can send to other modules.
- **[Loud Numbers (Norns)](https://norns.community/en/authors/duncan-geere/loud-numbers)** - is a script for the Monome Norns music computer that lets you load a CSV file and convert the values into pitches in a specific sale. When paired with the Monome Crow, it can output control voltages to Eurorack modular synth hardware.

### Hardware

- **[Photon Smasher](https://www.clipsoundandmusic.uk/photonsmasher/)** - mini-sensor device that converts light into sound. "Use a solar cell to convert light into sound and explore a hidden world of rhythms, melodies and textures."
- **[Bleep Labs Sensory Coupler](https://bleeplabs.com/sc/)** - a Eurorack module that converts sensor data into control voltages. "The Sensory Coupler allows you to easily convert touch, proximity, light, magnetic fields, motion, and more into CV and gate."

## Sonification tutorials

### In Ableton
- [Hipster Modular - Road to 270 - Ableton Live Sonification Template](https://www.youtube.com/watch?v=1Md1sJtVnc8)
- [JSON Weather Tutorial - Ableton Live 9 Suite - Sonification](https://www.youtube.com/watch?v=3UP-Fs6b9_k)

### In P5.js
- [Data as Material series](https://youtube.com/playlist?list=PLLx7jIm38p9mATGvW9sUT3Gjk0kGX9sot)

### In Max MSP
- [Generative Sonification by sound artist James Wilkie Music using MaxMSP](https://www.instagram.com/p/CZOkLIHs8Yp/?igshid=OTA3YzJiYTA=)

### Best practices
- [Data Sonification: ‘Charts’ for Radio?](https://nationalpress.org/topic/data-sonification-charts-for-radio/)
