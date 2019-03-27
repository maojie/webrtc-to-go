# webrtc-to-go

How to **download**, **build** and **hack** WebRTC against the GFW.

## <u>Before You Start</u>

First, you need a ladder (public server) to the freedom (can get the full WebRTC source code).

```bash
# Start typing the following command from your client machine
# ~/.ssh/id_rsa.pub stands for the identified public key to access the server
# [port] stands for the opened ssh port of the server
# [username] stands for the user name of the server
# [x.x.x.x] stands for the ip address of the server
$ ssh -i ~/.ssh/id_rsa.pub -p [port] [username]@[x.x.x.x] # Login to the server
$ cd ~
$ sudo apt-get install git
$ git clone https://chromium.googlesource.com/chromium/tools/depot_tools
$ export PATH=~/depot_tools/:$PATH
$ mkdir webrtc && cd webrtc
$ fetch --nohooks webrtc # May take a long time
$ vi .gclient # Append one line to cache all third_party repos
cache_dir = "/home/[username]/webrtc-cache"
$ gclient sync --nohooks --verbose # Should take a long time
$ cd ~/webrtc/src
$ git remote add mirror https://github.com/[username]/webrtc.git
$ git push mirror master
$ cd ~/depot_tools
$ git remote add mirror https://github.com/[username]/depot_tools.git
$ git push mirror master
```

## Configure Gitolite

```bash
$ ssh-keygen -t rsa -f gitolite # You'll get the following two files
~/.ssh/gitolite and ~/.ssh/gitolite.pub
$ scp -i ~/.ssh/id_rsa.pub -P [port] ~/.ssh/gitolite.pub [username]@[x.x.x.x]
$ ssh -i ~/.ssh/id_rsa.pub -p [port] [username]@[x.x.x.x] # Login to the server
$ sudo apt-get install gitolite3 # On Ubuntu 14.04.6 TLS
$ gitolite setup -pk gitolite.pub # You'll get the following two repos
/home/[username]/repositories/gitolite-admin.git
/home/[username]/repositories/testing.git
$ exit # Exit from the server
$ vi ~/.ssh/config # Add the following content
host [x.x.x.x]
  HostName [x.x.x.x]
  IdentityFile ~/.ssh/gitolite
  User [username]
$ git clone ssh://[username]@[x.x.x.x]:[port]/gitolite-admin.git
# gitolite-admin has two directories
gitolite-admin/conf/gitolite.conf to manage the remote repositories
gitolite-admin/keydir/ to manage the public keys
```

## Mirror WebRTC third_party repos using Gitolite 

```bash
$ ssh -i ~/.ssh/id_rsa.pub -p [port] [username]@[x.x.x.x] # Login to the server
$ ln -s /home/[username]/webrtc-cache/boringssl.googlesource.com-boringssl /home/[username]/repositories/boringssl.git
$ ln -s /home/[username]/webrtc-cache/chromium.googlesource.com-catapult /home/[username]/repositories/catapult.git
$ mkdir -p /home/[username]/repositories/chromium/deps
$ ln -s /home/[username]/webrtc-cache/chromium.googlesource.com-chromium-deps-icu /home/[username]/repositories/chromium/deps/icu.git
...
$ mkdir -p /home/[username]/repositories/webm
$ ln -s /home/[username]/webrtc-cache/chromium.googlesource.com-webm-libvpx/ /home/[username]/repositories/webm/libvpx.git
# Won't list all the repos here, create all the related links above
```

## Expose all the repos using gitolite-admin

```bash
$ cd gitolite-admin # The cloned gitolite-admin at the client side
$ vi conf/gitolite.conf # Expose all the related repos
repo boringssl
  RW+ = gitolite
repo catapult
  RW+ = gitolite
repo chromium/deps/icu
  RW+ = gitolite
...
repo webm/libvpx
  RW+ = gitolite
$ git add conf/gitolite.conf
$ git commit -m "Expose all the related repos"
$ git push origin master
```

## Download WebRTC Source Code

```bash
$ git clone https://github.com/[username]/depot_tools.git
$ export DEPOT_TOOLS_UPDATE=0
$ mkdir -p webrtc
$ cd webrtc/
$ git clone https://github.com/[username]/webrtc.git src
$ vi .gclient # Paste the following content
solutions = [
  {
    "url": "https://github.com/[username]/webrtc.git",
    "managed": False,
    "name": "src",
    "deps_file": "DEPS",
    "custom_deps": {},
  },
]
$ vi src/DEPS # Modify the following lines
Replace 'https://chromium.googlesource.com' to 'ssh://[username]@[x.x.x.x]:[port]'
Replace 'https://boringssl.googlesource.com' to 'ssh://[username]@[x.x.x.x]:[port]'
$ gclient sync --nohooks --verbose
```


