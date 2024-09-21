## Streaming Over RTSP with FFmpeg and MediaMTX

RTSP (Real-Time Streaming Protocol) is commonly used for streaming media across IP networks, particularly in real-time applications like live camera feeds, video conferencing, or multimedia streaming within a local network. FFmpeg can be used to send or receive streams over RTSP.

RTSP allows for better control over streaming (start, pause, stop), and when combined with RTP for data transport, it becomes a reliable protocol for multimedia distribution on local networks.

Below is a step-by-step guide on how to stream media over RTSP using FFmpeg.

---

### 1. **Setting Up an MediaMTX Server**

Before streaming, you need an RTSP server to handle the stream. There are several open-source RTSP servers available. Two popular options are:
- **ffserver** (deprecated but still usable with older FFmpeg versions)
- **MediaMTX (formerly rtsp-simple-server)** MediaMTX is a ready-to-use and zero-dependency real-time media server and media proxy that allows to publish, read, proxy, record and playback video and audio streams. It has been conceived as a "media router" that routes media streams from one end to the other.

### **Install MediaMTX Server**

#### **Linux Installation** (via precompiled binaries)
1. Download the latest release from the [MediaMTX GitHub page](https://github.com/aler9/mediamtx/releases).
2. Extract the tar.gz file and run the binary:
   ```bash
   wget https://github.com/aler9/mediamtx/releases/download/v0.0.0/mediamtx_v0.0.0_linux_amd64.tar.gz
   tar -xzvf mediamtx_v0.0.0_linux_amd64.tar.gz
   mv mediamtx_v0.0.0_linux_amd64 mediamtx
   cd mediamtx
   ./mediamtx
   ```
   This starts the MediaMTX server, which listens for incoming connections by default on `port 8554`.

   **For more info:** Check the detailed guide in this repository.

#### **Windows Installation**
1. Download the Windows executable from the [GitHub releases page](https://github.com/aler9/mediamtx/releases).
2. Run the executable.

---

### 2. **Streaming Over RTSP Using FFmpeg**

Now that the MediaMTX server is running, you can use FFmpeg in another terminal to stream media to this server. The MediaMTX Server will receive the media and make it available to clients on the network in a specified path.

#### **Streaming a File Over RTSP**:

```bash
ffmpeg -re -i input.mp4 -c:v libx264 -c:a aac -f rtsp rtsp://192.168.250.148:8554/stream
```

- **`-re`**: Reads the input in real-time.
- **`-i input.mp4`**: Specifies the input media file.
- **`-c:v libx264`**: Encodes the video using the H.264 codec.
- **`-c:a aac`**: Encodes the audio using AAC codec.
- **`-f rtsp`**: Specifies RTSP as the output format.
- **`rtsp://192.168.250.148:8554/stream`**: The RTSP server’s address and stream path. Replace `192.168.250.126` with the server's IP address and ensure port `8554` is open.

The MediaMTX Server will now stream the media to the specified endpoint (`/stream`).

#### **Streaming Live Content (e.g., Webcam)**:

If you want to stream live content, such as a webcam, you can use this command:

```bash
ffmpeg -f v4l2 -i /dev/video0 -c:v libx264 -f rtsp rtsp://192.168.250.148:8554/live
```

- **`-f v4l2`**: Specifies Video4Linux as the input device (Linux webcam).
- **`/dev/video0`**: The device file for your webcam.
- **`-c:v libx264`**: Encodes the video using H.264.
- **`-f rtsp`**: Streams over RTSP.
- **`rtsp://192.168.250.148:8554/live`**: Specifies the RTSP stream URL.

---

### 3. Playing the RTSP Stream with FFPlay

To view the RTSP stream on the client side, you can use the following commands in the terminal:

```bash
ffplay rtsp://192.168.250.148:8554/stream
```

or

```bash
ffplay rtsp://192.168.250.148:8554/live
``` 

This will allow you to play the RTSP stream on your client machine.

### 4. **Receiving the RTSP Stream in VLC**

In the command line:

```
vlc
rtsp://192.168.250.148:8554/stream
```

1. Open VLC on the client machine (which is connected to the local network).
2. Go to **Media → Open Network Stream**.
3. Enter the RTSP URL of the stream:

   ```bash
   rtsp://192.168.250.148:8554/stream
   ```

Replace `192.168.250.148` with the MediaMTX server’s IP address, and `/stream` is the stream path used earlier.

VLC will now display the live or pre-recorded stream being broadcast over the local network.

---

Here’s a rewritten version of the commands with explanations:

### 4. Other FFmpeg and FFplay Commands with Explanations

1. **Streaming a Video in Loop Using FFmpeg:**

   ```
   ffmpeg -re -stream_loop -1 -i bangla.mp4 -an -c:v copy -c:a copy -f rtsp -rtsp_transport udp rtsp://192.168.1.88:8554/live
   ```
   - `-re`: Real-time mode to stream media as if it was captured live.
   - `-stream_loop -1`: Loop the input video indefinitely.
   - `-i bangla.mp4`: Input file.
   - `-an`: Disable audio in the stream.
   - `-c:v copy -c:a copy`: Copy the video and audio codecs without re-encoding.
   - `-f rtsp`: Output format is RTSP.
   - `-rtsp_transport udp`: Use UDP for RTSP transmission.
   - `rtsp://192.168.1.88:8554/live`: RTSP destination URL.

2. **Streaming to a Specific RTSP Channel:**
   ```
   ffmpeg -re -stream_loop -1 -i bangla.mp4 -an -c:v copy -c:a copy -f rtsp -rtsp_transport udp rtsp://192.168.1.88:8554/Streaming/Channels/102
   ```
   Similar to the previous command but streams to a different RTSP channel (`/Streaming/Channels/102`).

3. **Re-encoding Video with Different Settings:**
   ```
   ffmpeg -re -stream_loop -1 -i bangla.mp4 -an -c:v libx264 -b:v 1000k -vf "scale=1280:720" -c:a aac -f rtsp -rtsp_transport udp rtsp://192.168.1.88:8554/Streaming/Channels/102
   ```
   - `-c:v libx264`: Re-encode video with H.264 codec.
   - `-b:v 1000k`: Set video bitrate to 1000 kbps.
   - `-vf "scale=1280:720"`: Scale the video to 1280x720 resolution.
   - `-c:a aac`: Use AAC codec for audio.

4. **Streaming from a Webcam:**
   ```
   ffmpeg -f dshow -i video="A4tech HD 720P PC Camera" -r 10 -f rtsp rtsp://192.168.1.88:8554/live
   ```
   - `-f dshow`: Use DirectShow for capturing input on Windows.
   - `-i video="A4tech HD 720P PC Camera"`: Select the input device (webcam).
   - `-r 10`: Set the frame rate to 10 fps.

5. **Saving a Stream from Channel 102:**
   ```
   ffmpeg -rtsp_transport tcp -i rtsp://admin:password@192.168.1.1:554/Streaming/Channels/102 -y abc.mp4
   ```
   - `-rtsp_transport tcp`: Use TCP for RTSP transmission.
   - `-i rtsp://.../Channels/102`: RTSP stream input.
   - `-y abc.mp4`: Save the stream as `abc.mp4`.

6. **Re-encoding Stream with Specific Codecs:**
   ```
   ffmpeg -rtsp_transport tcp -i rtsp://admin:password@192.168.1.1:554/Streaming/Channels/102 -c:v libx264 -c:a aac output.mp4
   ```
   - Re-encodes the video with H.264 (`libx264`) and the audio with AAC (`aac`), saving it as `output.mp4`.

7. **Changing Frame Rate of a Stream:**
   ```
   ffmpeg -rtsp_transport tcp -i rtsp://admin:password@192.168.1.1:554/Streaming/Channels/102 -r 30 30.mp4
   ```
   - `-r 30`: Set the frame rate to 30 fps.
   - Saves the output as `30.mp4`.

---

### Hikvision Camera Streaming with FFplay

Hikvision cameras typically offer two RTSP streams:
- **Channel 101:** Main stream (high quality).
- **Channel 102:** Sub stream (lower quality).

8. **Playing Default Channel 101:**
   ```
   ffplay rtsp://admin:password@192.168.1.1:554
   ```
   This plays the default high-quality stream (Channel 101) from the Hikvision camera.

9. **Playing Specific Channels:**
   ```
   ffplay rtsp://admin:password@192.168.1.1:554/Streaming/Channels/101
   ffplay rtsp://admin:password@192.168.1.1:554/Streaming/Channels/102
   ```
   These commands explicitly play Channel 101 (main stream) and Channel 102 (sub stream), respectively.

10. **Using TCP for Error-Free Streaming:**
   ```
   ffplay -rtsp_transport tcp rtsp://admin:password@192.168.1.1:554/Streaming/Channels/101
   ffplay -rtsp_transport tcp rtsp://admin:password@192.168.1.1:554/Streaming/Channels/102
   ```
   - `-rtsp_transport tcp`: Use TCP to avoid errors and packet loss during streaming.

11. **Real-time Playback Without Buffering:**
   ```
   ffplay -fflags nobuffer -rtsp_transport tcp rtsp://admin:password@192.168.1.1:554/Streaming/Channels/102
   ```
   - `-fflags nobuffer`: Disable buffering for real-time playback.
   
12. **Scaling the Video:**
   ```
   ffplay -rtsp_transport tcp rtsp://admin:password@192.168.1.1:554/Streaming/Channels/102 -r 30
   ```
   - `-r 30`: Set playback frame rate to 30 fps.

---

These commands provide different ways to stream, capture, or re-encode video using FFmpeg and play RTSP streams using FFplay, useful for IP cameras, multimedia streaming, or re-encoding tasks.


