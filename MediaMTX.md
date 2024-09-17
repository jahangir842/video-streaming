### Detailed Notes on **MediaMTX**

**MediaMTX** (formerly known as **RTSP Simple Server**) is an open-source RTSP, RTMP, and HLS server and proxy. It is designed to be a highly efficient and lightweight solution for streaming audio and video media over IP networks. With support for protocols like RTSP, RTMP, WebRTC, and HLS, MediaMTX is a flexible and scalable choice for multimedia streaming within local networks or even over the internet.

### Key Features of MediaMTX
- **Multi-Protocol Support**: Handles RTSP, RTMP, HLS, and WebRTC protocols.
- **Low Latency**: Designed for low-latency real-time media transport.
- **Easy to Use**: Simple configuration and lightweight, making it suitable for home projects as well as enterprise applications.
- **Proxy Functionality**: Can act as a proxy server for other RTSP/RTMP streams.
- **Cross-Platform**: Runs on various operating systems, including Linux, Windows, and macOS.

---

### 1. **Installation**

#### **Linux Installation** (via precompiled binaries)
1. Download the latest release from the [MediaMTX GitHub page](https://github.com/aler9/mediamtx/releases).
2. Extract the tar.gz file and run the binary:
   ```bash
   wget https://github.com/aler9/mediamtx/releases/download/v0.0.0/mediamtx_v0.0.0_linux_amd64.tar.gz
   tar -xzvf mediamtx_v0.0.0_linux_amd64.tar.gz
   cd mediamtx
   ./mediamtx
   ```

#### **Windows Installation**
1. Download the Windows executable from the [GitHub releases page](https://github.com/aler9/mediamtx/releases).
2. Run the executable.

#### **Docker Installation**
1. You can also run MediaMTX in a Docker container:
   ```bash
   docker run --rm -it -p 8554:8554 -p 1935:1935 -p 8888:8888 aler9/mediamtx
   ```
   This will expose:
   - RTSP on port 8554
   - RTMP on port 1935
   - Web interface on port 8888

---

### 2. **Configuration**

MediaMTX uses a simple YAML configuration file to define various server settings, such as protocols, authentication, and stream paths.

By default, MediaMTX creates a `mediamtx.yml` configuration file. To customize the server, edit this file.

#### **Basic Configuration Example**
Here’s a sample configuration:

```yaml
paths:
  all:
    source: publisher
    readUser: viewer
    readPass: viewerpass
    publishUser: publisher
    publishPass: publishpass
```

- **`paths`**: Defines stream paths.
  - **`all`**: Applies these settings to all streams.
  - **`source: publisher`**: Specifies that streams are published by a user.
  - **`readUser`/`readPass`**: Authentication for viewing the stream.
  - **`publishUser`/`publishPass`**: Authentication for publishing the stream.

#### **Starting the Server with Custom Config**
To start the server with a custom configuration file, use:
```bash
./mediamtx --config mediamtx.yml
```

---

### 3. **Protocols Supported by MediaMTX**

#### **RTSP** (Real-Time Streaming Protocol)
- **Use Case**: Real-time media streaming, typically used for IP cameras.
- **Example URL**: 
  ```bash
  rtsp://localhost:8554/stream
  ```
- FFmpeg can be used to send a stream to the MediaMTX RTSP server:
  ```bash
  ffmpeg -re -i input.mp4 -c:v libx264 -f rtsp rtsp://localhost:8554/stream
  ```

#### **RTMP** (Real-Time Messaging Protocol)
- **Use Case**: Typically used for live streaming to platforms like YouTube or Facebook Live.
- **Example URL**: 
  ```bash
  rtmp://localhost:1935/stream
  ```
- To push a stream using FFmpeg:
  ```bash
  ffmpeg -re -i input.mp4 -c:v libx264 -f flv rtmp://localhost:1935/stream
  ```

#### **HLS** (HTTP Live Streaming)
- **Use Case**: Delivering streams over HTTP, allowing users to play video in web browsers or media players.
- **Example URL**: 
  - Access the stream in a web browser or player by visiting:
    ```
    http://localhost:8888/stream/index.m3u8
    ```

#### **WebRTC** (Web Real-Time Communication)
- **Use Case**: Low-latency media streaming directly within web browsers without needing additional plugins or software.
- **Example URL**:
  - In the MediaMTX web interface, you can view WebRTC streams by navigating to:
    ```
    http://localhost:8888
    ```

---

### 4. **Streaming with FFmpeg to MediaMTX**

#### **RTSP Streaming**:
1. Set up MediaMTX to listen for RTSP streams.
2. Use FFmpeg to stream media to the RTSP server:
   ```bash
   ffmpeg -re -i input.mp4 -c:v libx264 -f rtsp rtsp://localhost:8554/stream
   ```

#### **RTMP Streaming**:
1. Ensure MediaMTX is configured to accept RTMP streams.
2. Use FFmpeg to push a stream over RTMP:
   ```bash
   ffmpeg -re -i input.mp4 -c:v libx264 -f flv rtmp://localhost:1935/stream
   ```

#### **Streaming a Webcam or Live Video**:
If you want to stream live from a webcam, you can use FFmpeg:
```bash
ffmpeg -f v4l2 -i /dev/video0 -c:v libx264 -f rtsp rtsp://localhost:8554/live
```
This will broadcast the live feed from `/dev/video0` (your webcam) to the RTSP server.

---

### 5. **Accessing the Stream**

#### **Viewing the RTSP Stream**
To view the stream, open a media player like VLC and enter the RTSP URL:
```bash
rtsp://localhost:8554/stream
```
You can also open this stream using other media players that support RTSP.

#### **Viewing the HLS Stream**
To view the HLS stream, open your browser and navigate to:
```bash
http://localhost:8888/stream/index.m3u8
```

---

### 6. **Proxying RTSP/RTMP Streams**

MediaMTX can also act as a proxy server for other RTSP/RTMP streams, forwarding the incoming streams to clients.

#### **Example Configuration**:
```yaml
paths:
  proxied:
    source: rtsp://192.168.1.100:554/camera
```

In this example, the stream from `rtsp://192.168.1.100:554/camera` will be proxied by MediaMTX and made available to clients connecting to:
```bash
rtsp://localhost:8554/proxied
```

---

### 7. **Security and Authentication**

#### **Enabling Authentication**
To secure your streams, you can enforce authentication for both publishers and viewers by editing the configuration file.

Here’s an example of how to enable authentication:

```yaml
paths:
  securestream:
    readUser: viewer
    readPass: viewerpass
    publishUser: publisher
    publishPass: publishpass
```

In this case, you would need the credentials `viewer/viewerpass` to view the stream and `publisher/publishpass` to publish a stream.

#### **Access Control**
MediaMTX allows you to configure access control for streams by specifying which users or IP addresses are allowed to connect.

---

### 8. **Logging and Monitoring**

MediaMTX provides logging to help you monitor the server’s activity. All interactions between the clients and the server (connections, disconnections, errors, etc.) are logged by default.

The logs can be viewed directly in the terminal or can be configured to output to a log file by specifying the following in the `mediamtx.yml` file:
```yaml
logLevel: info
logFile: /path/to/logfile.log
```

---

### Conclusion

**MediaMTX** is a powerful yet simple server for streaming RTSP, RTMP, and HLS. Its flexibility makes it suitable for a variety of applications, from local IP camera setups to live media streaming on a local network. With support for multiple protocols, easy-to-configure authentication, and lightweight resource requirements, it can be integrated into many multimedia streaming solutions efficiently. Whether you're using it as a standalone server or as a proxy for existing streams, MediaMTX provides the essential tools for seamless media delivery.
