# webrtc-to-go

How to download, build and hack WebRTC

$ git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git

$ mkdir -p webrtc/src

$ cd webrtc/src

$ git clone https://github/maojie/webrtc.git

$ cd ../

$ vi .gclient # Paste the following item
solutions = [
  {
    "url": "https://webrtc.googlesource.com/src.git",
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
