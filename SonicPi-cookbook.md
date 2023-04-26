# SonicPi Code Cookbook

Some "recipes" (ie code snippets) that can be used for sonification using Sonic Pi.

## Normalising data

One of the most common [techniques in sonification](https://github.com/Decibels-Sonification/sonification-resources#methods-of-sonification) is to translate data values into a musical variable, also called **parameter mapping**. 

Here is a basic normalise function:

```
def normalise(x, xmin, xmax, ymin, ymax)
  xrange = xmax - xmin
  yrange = ymax - ymin
  ymin + (x - xmin) * (yrange.to_f / xrange)
end
```

Given a data value, you can use the above function to "translate" it like so and store it in the variable `translatedValue`:

```
translatedValue = normalise(originalValue, 0.01, 1, 50, 120)
```

In the above example, we set the range of the data (ie the `xmin` and `xmax`) as 0.01 and 1 (perhaps your data is in percentages). The output range is 50 - 120. You can set these numbers to whatever you like. In this example, perhaps this value will be used to change the "cutoff" of a synthesizer.

## Looping through data

If you have many values that you want to loop through and sonify, you can use the `tick` method:

```
data = [32, 28, 42, 40, 40, 36, 46, 42]

live_loop :dataloop do
   value = data.tick
   synth :prophet, note: value, release: 1, attack: 0.1, cutoff: 80, amp: 0.3
   sleep 0.5
   
   if (tick == data.length-1)
     stop
   end
 end
 ```
 
 The above recipe will "tick" through all the values in the `data` array. In this case, it stores each value in the `value` variable and then uses that value to pass in a MIDI note number to the synth.
 
 In order to prevent infinite looping through the array, the `if` statement at the end checks to see when you are at the end of the `data` array, and then stops the loop. 
