# SonicPi Code Cookbook

Some "recipes" (ie code snippets) that can be used for sonification using Sonic Pi.

*Sonic Pi is an open source, community project maintained by Sam Aaron. Please consider supporting [his Patreon](https://www.patreon.com/samaaron)!*

## Normalising data

One of the most common [techniques in sonification](https://github.com/Decibels-Sonification/sonification-resources#methods-of-sonification) is to translate data values into a musical variable, also called **parameter mapping**. 

Here is a basic normalise function:

```ruby
def normalise(x, xmin, xmax, ymin, ymax)
  xrange = xmax - xmin
  yrange = ymax - ymin
  ymin + (x - xmin) * (yrange.to_f / xrange)
end
```

Given a data value, you can use the above function to "translate" it like so and store it in the variable `translatedValue`:

```ruby
translatedValue = normalise(originalValue, 0.01, 1, 50, 120)
```

In the above example, we set the range of the data (ie the `xmin` and `xmax`) as 0.01 and 1 (perhaps your data is in percentages). The output range is 50 - 120. You can set these numbers to whatever you like. In this example, perhaps this value will be used to change the "cutoff" of a synthesizer.

## Loading data from CSV

Sonic Pi includes a native method to read CSV files. The below snippet shows you how to load a CSV file and then save the first column as a data array in the variable `first_column`.

```ruby
require "csv"

file = "path/file.csv"

data = CSV.parse(File.read(file), headers: true, col_sep: ",")

# read first row and convert every observation to an integer. if you want to convert it to factor, use .map(&:to_f)
first_column = data.by_col[1].map(&:to_i) 

```

## Looping through data

If you have many values that you want to loop through and sonify, you can use the `tick` method:

```ruby
data = [32, 28, 42, 40, 40, 36, 46, 42]

live_loop :dataloop do
   value = data.tick
   synth :prophet, note: value, release: 1, attack: 0.1, cutoff: 80, amp: 0.3
   sleep 0.5
   
   if (look == data.length-1)
     stop
   end
 end
 ```
 
 The above recipe will "tick" through all the values in the `data` array. In this case, it stores each value in the `value` variable and then uses that value to pass in a MIDI note number to the synth.
 
 In order to prevent infinite looping through the array, the `if` statement at the end checks to see when you are at the end of the `data` array, and then stops the loop. 

## Conditional statements

Sonic Pi has a built in "if" statement which can be handy. The below statement sets a 50/50 chance of playing a given note:

```ruby
play 53, amp: 0.3, release: 2 if one_in(2)
```

To make this more interesting, you can combine it with the `normalise` function documented above to control the frequency of notes based on a piece of data:

```ruby
value = normalise(dataPoint,data.min,data.max,1,10)

play 53, amp: 0.3 if one_in(value)
```


## Sonifying distributions

Let's say you have a dataset with distribution values, like 25% are X, 60% are Y and 15% are Z. If you wanted to represent this sonically, you might map each group (X,Y, and Z) to a different note and then want to spread those notes according to their percentage values. The below snippet shows how to do this:

```ruby
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

## Advanced data loading
Here's a longer snippet that develops some of the above techniques to load, process, and normalise a dataset with multiple columns. It doesn't use the CSV library, instead it reads in the data line by line from the file.

```ruby

normParameters = {"year" => nil,
                  "column1" => {"note" => :e4, "pan" => -0.7,
                               "x1" => 0, "x2" => 200,
                               "y1" => 0, "y2" => 100},
                  "column2" => {"note" => :a4, "pan" => 0.7,
                             "x1" => 50, "x2" => 100,
                             "y1" => 0, "y2" => 1}
                  }

# -------------
# load the data
# -------------
puts("Loading data")

data = Hash.new # set up empty array to hold the data

# Store each line from the file in an array
lines = [];
File.open('path/file.csv') do |file| # Open the file
  file.each_line do |line| # loop over each line
    lines.push(line) #strip off any whitespace
  end
end

puts("Data loaded - there are #{lines.length} lines in the file, including the header.")

# -------------------
# processing the data
# -------------------

colheaders = lines[0].split(",") #split the first line into an array

colheaders.each { |colhead| # loop over that array
  colhead.strip! # strip whitespace from each header
  data[colhead] = Array.new # add header as a key in the data hash
}

puts("Header parsed - columns are:", colheaders)

# Then loop over each remaining lines
lines[1..lines.length-1].each do |line| # Loop over each line
  
  # loop over each column header
  colheaders.length.times do |colindex|
    
    # Log the data into the hash
    data[colheaders[colindex]].push(line.split(",")[colindex].strip)
    
  end
end

puts("Track will be #{(lines.length-1) / current_bpm} minutes long")

# -----------------------------
# define normalisation function
# -----------------------------
puts ("Defining normalisation function")

define :normalize do |x, xmin, xmax, ymin, ymax|
  
  xrange = xmax - xmin
  yrange = ymax - ymin
  
  return (x == "") ? "-" : ymin + (x.to_f - xmin) * (yrange.to_f / xrange.to_f)
  
end

# ------------------
# normalise the data
# ------------------
puts ("Normalising the data")

ndata = Hash.new # new hash for normalised data

# Loop over each column header
colheaders.each do |header|
  
  # Make a new array to store the normalised data for that column header
  ndata[header] = Array.new
  
  if (normParameters[header] == nil) # For non-data rows
    ndata[header] = data[header] # Don't normalise at all - just pass the data through
    
  else
    # Loop over each datapoint
    data[header].each do |datum|
      
      # Normalise it according to the parameters set at the top
      ndatum = normalize(datum, normParameters[header]["x1"], normParameters[header]["x2"], normParameters[header]["y1"], normParameters[header]["y2"])
      
      # Clip down things that exceed the parameters
      if (ndatum > normParameters[header]["y2"])
        ndatum = normParameters[header]["y2"]
      end
      
      # Push it into the appropriate array
      ndata[header].push(ndatum)
    end
  end
end

puts ("Data normalised!")
puts ndata
```
## Note density function
Here's a function called noteDensity that can be used to map data to the number of sounds in a time period. The denser the sounds, the higher the number, producing an effect like a Geiger counter.

```ruby

# -------------
# Load data
# -------------
data = [1, 5, 11, 2, 9, 16, 5, 6] # Set up placeholder array between 1 and 16


# ---------------------------
# Define noteDensity function
# ---------------------------
puts ("Defining noteDensity function")

define :noteDensity do |array, level|
  possibles = (0..array.length-1).to_a # Make an array of indexes
  
  # Run a loop "level" times
  level.times do
    chosen = possibles.sample(1) # Pick one random index from the possibles array
    possibles.delete(chosen) # Remove it from the possibles array
    array[chosen] = 1 # Set it to "on"
  end
  
  return array
  
end

# ------------------
# Play sonification!
# ------------------

counter = 0; # Initialise data counter


# START THE LOOP!
data.length.times do |datum|
  
  puts counter
  
  maxClicks = 16 # Set max clicks per datapoint
  tickGap = 3.0/maxClicks # Set gap between clicks
  
  # Choose a variable to sonify
  geig = data[counter.to_i] # Get the datapoint for that variable for that row
  
  puts(geig)
  
  # Set up array of data
  geigArray = Array.new(maxClicks) {|i| 0 } # Create an array of 0s
  noteDensity(geigArray, geig) # Add data to the array
  
  # loop over each array
  geigArray.each do |pulse|
    if (pulse == 1) # If the pulse is on...
      use_synth :beep
      play 72, release: 0.05 # Play a short beep sound
    end
    
    sleep tickGap # Add gap between ticks
  end
  
  sleep 1
  
  # Increment the counter
  counter += 1
  
end

```
