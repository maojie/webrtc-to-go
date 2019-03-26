# webrtc-to-go

How to download, build and hack WebRTC

$ git clone https://github.com/maojie/depot_tools.git

$ export DEPOT_TOOLS_UPDATE=0

$ mkdir -p webrtc

$ cd webrtc/

$ git clone https://github.com/maojie/webrtc.git src

$ vi .gclient # Paste the following item

solutions = [
  {
    "url": "https://github.com/maojie/webrtc.git",
    "managed": False,
    "name": "src",
    "deps_file": "DEPS",
    "custom_deps": {},
  },
]
target_os = ['unix', 'android']

$ gclient sync --nohooks

$ cd src

$ ./build/install-build-deps-android.sh

$ gn gen out/Default

$ ninja -C out/Default AppRTCDemo
