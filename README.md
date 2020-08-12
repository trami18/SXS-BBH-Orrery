# SXS-BBH-Orrery
This repo has everything you need in order to create your own BBH Orrery.

&#x1F539; These are instructions for how to create a visualization of one or more bbh collisions  using preview. I have multiple scripts that you can use. You can find the scripts you will need here for now: git@github.com:sxs-collaboration/bbh-orrery.git

## Functions of each script

:wavy_dash: IMPORTANT: All of these scripts need to be in the same directory!!! 

- ```OrreryGuts.py``` creates csv files that contains everything needed for the main script.creates csv files containing a list of shift times for each specific simulation in the order. 

-  ```Inspiral.py``` and ```Ringdown.py``` are the scripts that load everything in paraview ready to create animation. 

- Gravitational Wave Plot Script , creates png of wave plots with transparent background. 


## Running bbh simulations on Orca:

1]   $ mkdir "name of simulation”

2]   $ cd in that directory and load spec modules 

3]   $ PrepareID -t bbh2 -reduce-ecc # this creates an Ecc0 directory
    
4]   $ cd Ecc/ 

5]   $ emacs Params.input  # add or change any initial data for simulation

6]   $ ./StartJob.sh   #starts job on Orca

7]   $ Showqueue # check to see if your job is running. 

8]   once the simulation has been running for a while, cd into PATH where simulation lives.

9]   cd into highest Ecc# and check if it is finished. 

10] mkdir a Visualizations directory in your bbh directory

11]  Join  HorizonsDump.h5, Horizons.h5, and  rh_FiniteRadii_CodeUnits.h5

12] ExtractH5 for both Horizons.h5 and rh_FiniteRadii_CodeUnits.h5

13] cd into both extracted-Horizons and extracted-rh_FiniteRadii_CodeUnits, check that you see .dir directories inside both. 


## Steps for creating wave images

&#x1F539; If you don’t need gravitational waves in your movie, simply remove all of the stuff in the scripts involving clips and gravitational wave png readers. 

1] open a jupyter notebook and create a copy Gravitational Wave Plot Script 

2] Make sure that “gw_event_paths” has the correct paths and information needed to create each wave plot. [“PATH/to/.h5”,”Luminosity distance”,”GWNAME”,”color for plot”,”total solar mass”]

3] Once the images have been generated you can download them from orca and crop them like this:
```	
	$convert GW1*.png -crop 210x190+30+0 output.png *correct

	$convert GW1*.png -crop 153x190+11+0 output.png

	$convert *WAVE.png -crop 213.3x190+26+0 output.png*correctchristodoulou

	$convert *WAVE.png -crop 214x190+17+0 output.png ****correctedarea51 
```


![https://github.com/trami18/Visualizing-BBH-Collisions-With-Paraview/blob/master/wave%20image1.png]

&#x1F539; After resizing them to 640x480 pixels, crop the titles like this to create images of only the titles:
```convert GW1*.png -crop 250x55+100 GWtitle.png```

```convert chris*.png -crop 205x25+0 GWtitle.png```
```convert *whitetitle.png -crop 108x26+30 *.png```

4] load each image in preview and add a clip.

5] you should see something like this:

![https://github.com/trami18/Visualizing-BBH-Collisions-With-Paraview/blob/master/image2.png]

&#x1F539; You may notice that the image looks blue when you load it in preview, but worry not! You simply need to uncheck the box in the properties window called “ Map Scalars”. 

![https://github.com/trami18/Visualizing-BBH-Collisions-With-Paraview/blob/master/image3.png]

6] You want to move the clip plane so that the red line is at the 0.0 tick because we set time zero to be merger time.

7] After you have decided what x-coord pt. you need to clip at, create a track in animation for your clip. This should be set as ex: Clip 1, Clip Type -Offset . 

8] Once the track has been added you can double click it and edit the start time of the clip and end time of the clip. You also want to set the start time value pt. as -#pt. and end time value as +#pt. Write these values down. 

9] You want to make sure that you get the correct clipping times and values. Run a test before using these values for the movie.

## Steps to create movie

1] open get_post_shift.py

2] This is not the most practical way of doing this; however, after trying lots of different methods this is the easiest for now. You are going to add the path leading to the new GW event’s pvd files in the correct order. Meaning, if the new GW event came before one of the events in the list, you need to make sure that you add the PATH in the correct part of the list. 

3] open Mega_Csv_Writer.py , make sure to set the correct base directory located at the top of the script.You need to do the same thing you did to get_post_shift.py for each of the lists. You also need to add the mass and event name to the corresponding lists, in the correct order. ORDER IS VERY IMPORTANT!!!

4] Now you run get_post_shift.py FIRST.Then run Mega_Csv_Writer.py SECOND. You should see that some files called “a.csv, b.csv, c.csv, shiftab.csv, shiftc.csv” have been created. The order in which you run these scripts is important!!!

5] I always check that all of the csv files are correct.

6] open PreRingdown_Overhead.py and Ringdown_Overhead.py, make sure that the base directory (dir. containing all of the python scripts and csv files) is correct.

7] open preview, in view tab select python shell.

8] double click and a python shell should appear on the screen. click run script and select PreRingdown_Overhead.py.

9] this script will ask you to enter a few things. 

10] There are few things I am still working on automating in my script. you will have to manually edit the clip tracks in preview if you are choosing to show the gravitational wave images in your movie. 

11] You want to make sure that you change the background image to the one that you need and edit the text box if need be. 

12] Before saving the animation double check that the view animation for PreRingdown_Overhead.py is set at sequence, end time is zero. For the Ringdown_Overhead.py script make sure the view animation is set at sequence, start time is zero and end time is one. 

13] After the png images have are all created use the ffmpeg1080 command, this creates an .mp4 :
	  ffmpeg -r (# of frame rate) -f image2 -start_number (#) -s 1920x1080 -i namepngfiles.%04d.png -vcodec libx264 -crf 20 -pix_fmt yuv420p nameformovie.mp4

14] open your new mp4 and make sure that everything is good. Repeat the steps for ring-down.

15] Once both pre-ring and ring-down mp4 are ready join the two videos with this command:

## Looping a video
source:https://video.stackexchange.com/questions/12905/repeat-loop-input-video-with-ffmpeg

- create a `list.txt` file like this: 
   
   ```file 'input1.mp4'```
   
   ```file 'input2.mp4'```
   
- then use the ffmpeg command:
   
```ffmpeg -f concat -i list.txt -c copy output.mp4```

16] Below are some commands you can run for various different things:

- make bash script executable
``` chmod +x path/to/script.sh```

- get frames from .mp4
``` ffmpeg -i transitionfadesfull.mp4 frame.%04d.png```

- Add music to .mp4
``` ffmpeg -i transitionfadesfull.mp4 -i CrystalWaters.m4a -map 0:0 -map 1:0 -vcodec copy -acodec copy out_music.mp4```

- join/loop .mp4
``` ffmpeg -f concat -i list2.txt -c copy output.mp4```

- fade into .mp4
``` ffmpeg -i preringnew.mp4 -vf "fade=in:0:1,fade=t=in:d=0.08" -acodec copy prefadein.mp4```

``` ffmpeg -r 60 -f image2 -start_number 0 -s 1920x1080 -i creditslide.%04d.png -vf "fade=in:0:24" -vcodec libx264 -crf 20 -pix_fmt yuv420p output.mp4```

- fade out of .mp4
``` ffmpeg -i ringnewcopy.mp4 -vf "fade=out:35:12" -acodec copy testfade.mp4```

- create duplicates of image and name it image.%04d.png
``` for i in {1..25}; do cp test.png "test$i.png”; done```

- finds audio duration time in seconds
``` ffprobe -v error -select_streams v:0 -show_entries stream=duration -of csv=p=0 cut.mp4```

- fades audio 
```ffmpeg -i cut.mp4 -filter_complex "[1:a]afade=t=out:st=$(bc <<< "41.171029-3.0"):d=$fade[a]" -map 0:v:0 -map "[a]" -c:v copy -c:a aac -shortest with_audio.mp4 ```

- correct:it fades the audiofile(.m4a)
```ffmpeg -i CrystalWaters.m4a -filter:a "afade=t=out:st=44:d=3:curve=cub" out.m4a```
- then you add the audio to the .mp4 w/o sound
```ffmpeg -i Full_25.mp4 -i out.m4a -map 0:0 -map 1:0 -vcodec copy -acodec copy cut_music.mp4```

- create .gif from .mp4 using ffmpeg and gif ski
```ffmpeg -i Draft1.mp4 frame%04d.png #creates frames from the video```

```gifski -W 1600 -H 900 --fps 60 -o clip.gif frame*.png #converts png to gif```

-  slows down mp4 by some factor, example below is 5
``` ffmpeg -i Draft1.mp4 -filter:v "setpts=5*PTS" slowDraft1_5x.mp4```

- create gif second way:
#source:https://www.leshylabs.com/blog/dev/2013-08-04 Making_Animated_GIFs_from_the_Linux_Command_Line.html

```ffmpeg -i Draft1.mp4 -r 60 frameTemp.%05d.gif #60 fps```

``` convert -delay 2 -loop 0 frameTemp.*.gif output.gif #100/fps—> 100/60=1.6666, need integer for this, so we round to 2 . the speed is 20% faster though…```


17]  I ended using this bash script to create my GIF:
		
```	
#! /bin/bash

*source:http://samhemann.com/gifs/2017/03/29/mastering-gifs.html*

input="gif_video.mp4"
output="Gif.gif"
palette="./frames*.png"

*Update the fps and width here:*
filters="fps=45,scale=1600:-1:flags=lanczos"
echo -ne  "Creating palette..."
ffmpeg -v warning -i $input -vf "$filters,palettegen" -y $palette
echo -ne "\r\033[KCreating gif..."
ffmpeg -v warning -i $input -i $palette -lavfi "$filters [x]; [x][1:v] paletteuse" -y $output

echo " Finished.”
```

I used 45 frames per second because 60 didn’t look right. 
To make a bash script executable you need to do this on the command line:
-  make bash script executable
```$ chmod +x path/to/bashscriptbname.sh```

- To run the bash script you do this:
```$ ./bashscriptbname.sh```


## Steps to keep GW labels on screen entire time
1] I have a script that creates plots with colorful labels and black labels. This is tedious, I will find a way to automate this. 
2] You download all images , the black lable plots are for the clipping and the colorful labeled plots are to crop out the labels. 


3] you first crop all clipping images:
	$

## Random Commands I Use Daily 

> ffmpeg -i ringdown6.mp4 -vf "fade=out:38:13" -acodec copy ring6fade.mp4

> ffmpeg -i prering6.mp4 -vf "fade=in:0:1,fade=t=in:d=0.08" -acodec copy prefade6.mp4
