# Welcome to MMLL
MMLL is a Musical Machine Listening Library for javascript's Web Audio API. It provides a variety of higher level musical listening facilities for computer music, such as onset detection, (major-minor) chord detection, beat tracking and auditory modelling. All listening objects can run live, or could be called in a simulated block by block way offline, faster than realtime.

## Author and License

The library was developed by [Nick Collins](http://composerprogrammer.com/index.html) as part of the AHRC funded [MIMIC](https://mimicproject.com/about) project (Musically Intelligent Machines Interacting Creatively). MMLL is released under an MIT license, see the included COPYING.txt file.

## Usage

The library can be used just for the machine listening objects, used within your own audio callback (e.g., as part of a ScriptProcessorNode), or via a quick set-up frontend that hides Web Audio API details and  has you write setup and audio callback functions analogous to Processing's setup and draw.

The latter method is the one explained here, but expert Web Audio API people should find it easy enough to just take the analyzers for their own work. Only including the precompiled MMLL.js file is needed to deploy the library, though from the home directory of the library you can compile it afresh via the shell script provided (it is just a concatenation of the js source files).

The typical expectation of a machine listening object is that we are working at 44.1KHz sampling rate and that a mono (single channel) input block of samples will be provided for analysis. The objects deal with accumulating samples ready for processing (often via an FFT) themselves and the user doesn't have to worry about that part. However, objects should cope at other standard sampling rates such as 48KHz, 88.2KHz and 96 KHz, even if performance is sub-optimal (for example, the onset detector was developed based on evaluation over a corpus of 44.1KHz samples, so works best at this home rate).

The [minimal code example](Examples/minimalexample.html) is reproduced below and linked here too. Note how the machine listener object is prefixed with MMLL, and the SetUp function is passed the sampling rate, needed for initialising the listener. The CallBack is where the main action happens, as each new block of input samples is passed in. The input and output arguments hold MMLLInput and MMLLOutput objects, which make the channels of input and output audio accessible, as well as a special input.monoinput which is a single channel ready for the listener. If a stereo sound file is loaded or two channel live input requested, the monoinput will be the average of the left and right channels. The output object assumes a stereo output for now, exposing the left and right channel data arrays.

```
var audioblocksize = 256; //lowest latency possible

var setup = function SetUp(sampleRate) { <br>
sensorydissonance = new MMLLSensoryDissonance(sampleRate); <br>
};

var callback = function CallBack(input,output,n) {

var dissonance = sensorydissonance.next(input.monoinput);

console.log(dissonance);

for (i = 0; i < n; ++i) {<br>
    output.outputL[i] = input.inputL[i];<br>
    output.outputR[i] = input.inputR[i];<br>
    }<br>

};

var gui = new MMLLBasicGUISetup(callback,setup,audioblocksize,true,true);
```


The Examples folder provides a test example for each listener currently available in the library.

Happy MMLLing
