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

## Loading data from CSV

Sonic Pi includes a native method to read CSV files. The below snippet shows you how to load a CSV file and then save the first column as a data array in the variable `first_column`.

```
require "csv"

file = "path/file.csv"

data = CSV.parse(File.read(file), headers: true, col_sep: ",")

# read first row and convert every observation to an integer. if you want to convert it to factor, use .map(&:to_f)
first_column = data.by_col[1].map(&:to_i) 

```

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

## Conditional statements

Sonic Pi has a built in "if" statement which can be handy. The below statement sets a 50/50 chance of playing a given note:

```
play 53, amp: 0.3, release: 2 if one_in(2)
```

To make this more interesting, you can combine it with the `normalise` function documented above to control the frequency of notes based on a piece of data:

```
value = normalise(dataPoint,data.min,data.max,1,10)

play 53, amp: 0.3 if one_in(value)
```


## Sonifying distributions

Let's say you have a dataset with distribution values, like 25% are X, 60% are Y and 15% are Z. If you wanted to represent this sonically, you might map each group (X,Y, and Z) to a different note and then want to spread those notes according to their percentage values. The below snippet shows how to do this:

```
# if you have a dataset with a distribution of values
# this dataset means: 25% should be the note C3, 60% E3 and 15% G3
data = [
  [:C3, 0.25],
  [:E3, 0.60],
  [:G3, 0.15]
]

# create an empty array
note_distribution = []

# define length of the array
len = 10

# create an array with length of len and distribute the note-values according to the percentage
data.length.times do |i|
  n = data[i][1] * len.to_f
  n.times do
    note_distribution << data[i][0]
  end
end

# shuffle the array
note_distribution = note_distribution.shuffle
puts note_distribution


# now in the following live-loop, the notes are played according to their distribution in the dataset
live_loop :distribute do
  note = note_distribution.tick
  synth :piano, note: note
  sleep 0.5
end
```

You can also do this with effects, panning, whatever you want!
