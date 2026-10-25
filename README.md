name: 24/7 YouTube Stream

on:
  workflow_dispatch:

jobs:
  stream:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v3

    - name: Install FFmpeg
      run: |
        sudo apt-get update
        sudo apt-get install -y ffmpeg

    - name: Download Video File with Retries
      run: |
        until wget --no-check-certificate -c -O video.mp4 "https://google.com"; do
          echo "Download failed, retrying in 5 seconds..."
          sleep 5
        done

    - name: Start Live Stream
      run: |
        while true; do
          ffmpeg -re -stream_loop -1 -i video.mp4 -c:v libx264 -preset veryfast -b:v 1500k -maxrate 1500k -bufsize 3000k -pix_fmt yuv420p -g 60 -c:a aac -b:a 128k -ar 44100 -f flv "rtmp://://youtube.com{{ secrets.YOUTUBE_STREAM_KEY }}"
          echo "Stream crashed with code $?. Restarting in 5 seconds..."
          sleep 5
        done
