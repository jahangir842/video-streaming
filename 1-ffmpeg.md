## FFmpeg (Fast Forward Moving Picture Experts Group)

FFmpeg is a powerful multimedia framework that allows you to encode, decode, transcode, stream, and play audio and video files. It supports a wide variety of formats, making it an essential tool for video/audio processing and manipulation. Below are detailed notes on FFmpeg, covering installation, commands, usage, and various operations.

---

### 1. **Introduction to FFmpeg**
FFmpeg is an open-source library that handles multimedia data. It's highly flexible, supporting almost all formats for video, audio, and images. It can be used for converting files, streaming media, and recording live events.

- **Common Uses:**
  - Convert between audio/video formats.
  - Compress media files.
  - Extract audio from video.
  - Capture and record media streams.
  - Apply filters like rotation, scaling, and more.

---

### 2. **Installation of FFmpeg**

#### **On Linux:**
FFmpeg can be installed via the package manager in most Linux distributions.

- **Ubuntu/Debian:**
  ```bash
  sudo apt update
  sudo apt install ffmpeg
  ```

- **CentOS/RHEL:**
  Enable the EPEL repository first:
  ```bash
  sudo yum install epel-release
  sudo yum install ffmpeg ffmpeg-devel
  ```

#### **On Windows:**
1. Download the latest FFmpeg build from the official [FFmpeg website](https://ffmpeg.org/download.html).
2. Extract the downloaded file.
3. Add the `bin` folder to your system’s PATH variable to use FFmpeg in the command prompt.

#### **On macOS:**
You can install FFmpeg using Homebrew:
```bash
brew install ffmpeg
```

---

### 3. **Basic FFmpeg Command Structure**
FFmpeg commands follow a structure where you input the file, specify various options, and provide the output file.

```bash
ffmpeg [input_options] -i [input_file] [output_options] [output_file]
```

- **`-i`**: Specifies the input file.
- **Input and output options**: Set filters, codecs, and more.

Example:
```bash
ffmpeg -i input.mp4 -vcodec libx264 output.mp4
```

---

### 4. **Basic Commands**

#### **Check File Information**
Before processing a file, you may want to inspect its metadata.

```bash
ffmpeg -i input.mp4
```
This command gives you information like codec, bitrate, frame rate, and duration.

#### **Convert Video Format**
Convert a video from one format to another (e.g., MP4 to AVI).

```bash
ffmpeg -i input.mp4 output.avi
```

#### **Convert Audio Format**
Convert audio from one format to another (e.g., MP3 to WAV).

```bash
ffmpeg -i input.mp3 output.wav
```

#### **Extract Audio from a Video**
If you want to extract only the audio from a video file, use this command:

```bash
ffmpeg -i input.mp4 -q:a 0 -map a output.mp3
```

#### **Cut a Video**
To cut a specific section from a video:

```bash
ffmpeg -i input.mp4 -ss 00:00:30 -to 00:01:00 -c copy output.mp4
```

- `-ss`: Start time (in hh:mm:ss).
- `-to`: End time.

#### **Resize a Video**
To resize or scale a video to a different resolution:

```bash
ffmpeg -i input.mp4 -vf scale=1280:720 output.mp4
```

#### **Compress Video**
Reduce the size of a video by compressing it:

```bash
ffmpeg -i input.mp4 -vcodec libx264 -crf 28 output_compressed.mp4
```

- `-crf` (Constant Rate Factor): Lower values mean better quality but larger file size.

---

### 5. **Advanced Commands**

#### **Convert and Change Codec of Video and Audio**
Change both the format and codec (e.g., convert a video to H.264 codec).

```bash
ffmpeg -i input.mkv -vcodec libx264 -acodec aac output.mp4
```

**Input File:** input.mkv (a Matroska file with potentially multiple audio and video streams)

**Output File:** output.mp4 (an MP4 file with specified codecs)

**Video Codec:** libx264 (H.264 video codec for efficient video compression)

**Audio Codec:** aac (Advanced Audio Codec for high-quality audio)

#### **Extract Images from Video**
To extract frames from a video and save them as image files:

```bash
ffmpeg -i input.mp4 -vf fps=1 frame_%04d.png
```

- `vf` : Video Filter
- `fps=1`: Extracts one frame per second.
- `%04d`: Sequential numbering of the output images.

#### **Add Subtitles to a Video**
If you have an external subtitle file (e.g., `.srt`), you can embed it into the video:

```bash
ffmpeg -i input.mp4 -vf subtitles=subtitle.srt output.mp4
```
- `vf` : Video Filter
  
#### **Create GIF from Video**
Convert a video to a GIF:

```bash
ffmpeg -i input.mp4 -vf "fps=10,scale=320:-1" output.gif
```

- `fps=10`: Sets the frames per second for the GIF.
- `scale=320:-1`: Resizes the width to 320 while maintaining the aspect ratio.

---

### 6. **Working with Audio**

#### **Extract Audio Track**
You can extract an audio track from a video:

```bash
ffmpeg -i input.mp4 -vn -acodec copy output.aac
```

- `-vn`: Disable video.

#### **Merge Two Audio Files**
To merge two audio files together:

```bash
ffmpeg -i input1.mp3 -i input2.mp3 -filter_complex amix=inputs=2:duration=first:dropout_transition=3 output.mp3
```

#### **Add Audio to a Video**
Replace or add an audio track to a video:

```bash
ffmpeg -i input.mp4 -i audio.mp3 -c:v copy -c:a aac -strict experimental output.mp4
```

#### **Adjust Volume**
To increase or decrease the volume of an audio file:

```bash
ffmpeg -i input.mp3 -filter:a "volume=1.5" output.mp3
```

- `volume=1.5`: Increase volume by 50%. Set it lower than 1 to decrease the volume.

---

### 7. **Filters in FFmpeg**

FFmpeg has a wide variety of filters that can be applied to both audio and video. Below are some useful filters.

#### **Video Filters:**

- **Rotate Video:**
  ```bash
  ffmpeg -i input.mp4 -vf "transpose=1" output.mp4
  ```
- `vf` : Video Filter
  
- **Grayscale Filter:**
  ```bash
  ffmpeg -i input.mp4 -vf "hue=s=0" output.mp4
  ```

- **Mirror/Flip Video:**
  ```bash
  ffmpeg -i input.mp4 -vf "hflip" output.mp4
  ```

#### **Audio Filters:**

- **Audio Fade-In/Out:**
  ```bash
  ffmpeg -i input.mp3 -af "afade=t=in:st=0:d=5" output.mp3
  ```

- **Equalizer:**
  Adjust the audio frequency:

  ```bash
  ffmpeg -i input.mp3 -af "equalizer=f=1000:t=q:w=1:g=-10" output.mp3
  ```

---

### 8. **Streaming with FFmpeg**

FFmpeg can be used to stream content live over a network. This is useful for streaming video to platforms like YouTube or Twitch.

#### **Basic RTMP Stream**
Here’s how you can stream to an RTMP server:

```bash
ffmpeg -re -i input.mp4 -c:v libx264 -b:v 2M -f flv rtmp://your_stream_server/live
```

---

### 9. **Batch Processing**
You can use FFmpeg in combination with shell scripting to process multiple files at once.

#### **Convert Multiple Files (Batch Script Example)**
Create a script that converts all `.mkv` files in a directory to `.mp4`:

```bash
for file in *.mkv; do
  ffmpeg -i "$file" "${file%.mkv}.mp4"
done
```

---

### 10. **FFmpeg Documentation and Resources**

To learn more and discover additional commands, the FFmpeg official documentation is your best resource:
- [FFmpeg Documentation](https://ffmpeg.org/documentation.html)

---

By following these notes, you’ll have a solid foundation to explore FFmpeg's powerful multimedia capabilities. Experiment with different options and configurations as FFmpeg is highly versatile and customizable!
