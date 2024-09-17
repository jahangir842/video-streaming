### Streaming Over RTSP with FFmpeg and MediaMTX

RTSP (Real-Time Streaming Protocol) is commonly used for streaming media across IP networks, particularly in real-time applications like live camera feeds, video conferencing, or multimedia streaming within a local network. FFmpeg can be used to send or receive streams over RTSP.

RTSP allows for better control over streaming (start, pause, stop), and when combined with RTP for data transport, it becomes a reliable protocol for multimedia distribution on local networks.

Below is a step-by-step guide on how to stream media over RTSP using FFmpeg.

---

### 1. **Setting Up an MediaMTX Server**

Before streaming, you need an RTSP server to handle the stream. There are several open-source RTSP servers available. Two popular options are:
- **ffserver** (deprecated but still usable with older FFmpeg versions)
- **MediaMTX (formerly rtsp-simple-server)** MediaMTX is a ready-to-use and zero-dependency real-time media server and media proxy that allows to publish, read, proxy, record and playback video and audio streams. It has been conceived as a "media router" that routes media streams from one end to the other.

#### **Install MediaMTX Server**

To install the **MediaMTX Server**, follow these steps:

1. Download the latest version of RTSP Simple Server from the [official GitHub repository](https://github.com/aler9/rtsp-simple-server).

2. Unzip the downloaded file and start the RTSP Simple Server:

   ```bash
   cd media
   ./mediamtx
   ```

   This starts the MediaMTX server, which listens for incoming connections by default on port 8554.

---

### 2. **Streaming Over RTSP Using FFmpeg**

Now that the MediaMTX server is running, you can use FFmpeg in other terminal to stream media to it. The MediaMTX Server will receive the media and make it available to clients on the network.

#### **Command to Stream a File Over RTSP**:

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

### 3. **Receiving the RTSP Stream**

Once the stream is being sent from FFmpeg to the RTSP server, any client on the local network can connect and receive the stream.

#### **Viewing the RTSP Stream in VLC**:

1. Open VLC on the client machine (which is connected to the local network).
2. Go to **Media → Open Network Stream**.
3. Enter the RTSP URL of the stream:

   ```bash
   rtsp://192.168.250.148:8554/stream
   ```

   Replace `192.168.250.148` with the MediaMTX server’s IP address, and `/stream` is the stream path used earlier.

VLC will now display the live or pre-recorded stream being broadcast over the local network.

---

### 4. **Stream Control (Pause, Play, Seek)**

One of the benefits of RTSP is that it provides controls over the stream such as pausing, playing, or seeking.

- **Pause the stream**: VLC or any RTSP client can request to pause the stream.
- **Play the stream**: Resume the stream by sending a play command.
- **Seek within the stream**: If the content is not live, RTSP allows seeking to different positions in the stream.

These controls are managed by the RTSP protocol itself, and the server handles requests from the client.

---

### 5. **Advanced RTSP Streaming with RTP (Real-Time Transport Protocol)**

RTSP typically relies on RTP to handle the transport of media packets. To stream over RTP using RTSP, FFmpeg can be configured to package the media into RTP packets, which the RTSP server will then handle.

#### **Command Example with RTP**:

```bash
ffmpeg -re -i input.mp4 -c:v libx264 -c:a aac -f rtsp rtsp://192.168.1.100:8554/stream
```

By default, FFmpeg will use RTP as the transport protocol for the media when you specify `-f rtsp`.

---

### 6. **Broadcasting to Multiple Clients**

MediaMTX servers allow multiple clients to connect and receive the same stream simultaneously. This makes RTSP ideal for scenarios like local IP camera setups or IPTV.

Once the stream is running on the server, any device on the network can open the RTSP stream in VLC or any RTSP-compatible player, as long as they have the correct URL and the RTSP server has enough resources to handle multiple connections.

---

### 7. **Setting Up Authentication for RTSP Streams**

To secure your stream, you may want to set up authentication on your MediaMTX RTSP server. RTSP Simple Server allows basic authentication, which can be configured in its configuration file (`mediamtx.yml`).

1. Open the configuration file:

   ```bash
   nano rtsp-simple-server.yml
   ```

2. Modify the `paths` section to enable authentication for the stream:

   ```yaml
   paths:
     all:
       source: publisher
       publishUser: user
       publishPass: pass
       readUser: viewer
       readPass: viewerpass
   ```

In this example:
- The publisher (FFmpeg) needs the credentials `user:pass` to stream.
- The clients (e.g., VLC) need the credentials `viewer:viewerpass` to access the stream.

3. Restart the RTSP server for the changes to take effect.

4. When publishing the stream, include the credentials in the URL:

   ```bash
   ffmpeg -re -i input.mp4 -c:v libx264 -c:a aac -f rtsp rtsp://user:pass@192.168.1.100:8554/stream
   ```

For viewing, the RTSP client (like VLC) will prompt for the credentials, or you can enter them in the URL:

```bash
rtsp://viewer:viewerpass@192.168.1.100:8554/stream
```

---

### Conclusion

Streaming over RTSP using FFmpeg is an effective way to broadcast media across a local network. By leveraging RTSP's control mechanisms and RTP for media delivery, you can build low-latency, efficient streaming systems for local IP camera setups, live broadcasts, or internal multimedia distributions.

Here’s a recap of the steps:
1. Set up an RTSP server (e.g., RTSP Simple Server).
2. Stream media using FFmpeg to the RTSP server.
3. Receive and control the stream using VLC or other RTSP clients.
4. Optionally, secure the stream with basic authentication.

With these steps, you can efficiently stream media within your local network using the RTSP protocol.
