# FFmpeg
## threads
Just append -threads

## Copying audio/video 
Can be used to remove audio/video.
```
ffmpeg -i test.webm -codec:a copy out.webm
ffmpeg -i test.webm -codec:v copy out.webm
```

## Draw box
Draws a box with transparency.
```
ffmpeg -i test.webm -vf "drawbox=x=10:y=10:w=100:h=100:color=pink@0.5:t=max" out.webm
ffmpeg -i test.webm -vf "drawbox=x=10:y=10:w=100:h=100:color=black@1.0:t=max" out.webm
```

## Specify start time and duration
Start and duration 
```
ffmpeg -i test.webm -ss 00:00:0.0 -t 10 out.webm
ffmpeg -i test.webm -ss 00:00:0.0 -t 5 out.webm
ffmpeg -i test.webm -ss 00:05:0.0 out.webm
```

## Crop video
Draw box, insert crop specify start and duration.
```
ffmpeg -i in.mp4 -filter:v "crop=out_w:out_h:x:y" out.mp4
ffmpeg -i test.webm -threads 4 -vf "drawbox=x=0:y=150:w=200:h=100:color=black@1.0:t=max,crop=1100:400:200:130" -ss 00:05:0.0 -t 5 out.webm
ffmpeg -i test.webm -threads 4 -vf "drawbox=x=0:y=150:w=200:h=100:color=black@1.0:t=max,crop=1100:410:200:130" -ss 00:05:0.0 -t 5 out.webm
```
