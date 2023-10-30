**How to set up your own private RTMP server using nginx**

Most people who stream enjoy using services such as Twitch.tv or Ustream to deliver video to viewers, and that works well enough. But sometimes you want some more control over your stream, or you want other people to be able to stream to you, or you want to stream to multiple places, or any number of things that requires you to have access to an actual RTMP stream from an RTMP server. This guide will cover the very basics of setting up a simple RTMP server on a Linux computer. Don't worry, it's not too complicated, but having familiarity with Linux will certainly help.

A couple things you can do with your own RTMP server that you might be interested in:

- Stream to multiple external channels
- Import other people's streams to incorporate for your own purposes (I use it in my casts for multiple camera angles, like in this video I did of a Tribes Ascend broadcast with multiple camera angles)
Alright, so how do you do these kinds of things?

Step 1: Get a Server Box

Believe it or not, RTMP is actually extremely light on system resources. Essentially it just grabs data from the input and forwards it on to the output, simple data transfer. Don't believe me? My RTMP server for a long time was a Raspberry Pi, a $35 mini-computer, sitting under my desk, and it was capable of hosting at least 3 simultaneous streams, and I never even stressed it to see how many more it could handle. So I assure you, even a cheap old box would suffice.

If you don't have your own box, a VPS can also work. I recommend Linode or Digital Ocean as providers. Just make sure you have enough bandwidth...remember that bandwidth usage will be (the size of a stream) * (the number of people uploading + the number of people downloading). So when I have 2 streamers stream to my server, and I download both of them, I can chew up 10GB of bandwidth in 2 hours.

I recommend using Ubuntu for the server software for the sake of ease, but you can obviously use whatever you want. As long as you get the dependencies for nginx somewhere besides apt, you can follow this guide just fine.

Note to Windows users: This guide focuses on using Linux. If you want to use Windows, you can find Windows binaries for nginx with the RTMP module already included here: http://nginx-win.ecsds.eu/download/

Note to Mac users: You can install nginx with the RTMP module via Homebrew: http://brew.sh/homebrew-nginx/

If you are hosting your server in your home, you will have to forward TCP port 1935 to the box...this varies by router, so look up how to set up port forwarding for your router. Also, I recommend using a dynamic DNS service to overcome dynamic IP issues that come up with residential hosting.

Step 2: Installing nginx with RTMP module

Log into your box, and make sure you have the necessary tools to build nginx using the following command:


$ sudo apt-get install build-essential libpcre3 libpcre3-dev libssl-dev

Now a bit of info about nginx (pronounced "engine-X"). nginx is an extremely lightweight web server, but someone wrote a RTMP module for it, so it can host RTMP streams too. However, to add the RTMP module, we have to compile nginx from source rather than use the apt package. Don't worry, it's really easy. Just follow these instructions. :)

From your home directory, download the nginx source code:


$ wget http://nginx.org/download/nginx-1.15.1.tar.gz

As of this writing, the latest stable version of nginx is 1.15.1. You can find the latest version on the nginx download page.

Next, get the RTMP module source code from git:


$ wget https://github.com/sergey-dryabzhinsky/nginx-rtmp-module/archive/dev.zip

Unpack/unzip them both, and enter the nginx directory:

$ tar -zxvf nginx-1.15.1.tar.gz
$ unzip dev.zip
$ cd nginx-1.15.1
Now we build nginx:

$ ./configure --with-http_ssl_module --add-module=../nginx-rtmp-module-dev
$ make
$ sudo make install
And nginx is installed! By default it installs to /usr/local/nginx, so to start the server run the following command:


$ sudo /usr/local/nginx/sbin/nginx

And to test to make sure nginx is running, point your browser to http://<your server ip>/ and you should get the "Welcome to nginx!" page.

Step 3: Configuring nginx to use RTMP

Open your config file, located by default at /usr/local/nginx/conf/nginx.conf and add the following at the very end of the file:


	Code:

rtmp {
server {
listen 1935;
chunk_size 4096;

application live {
live on;
record off;
}
}
}
This is an extremely basic configuration with a "live" application that simply forwards the RTMP stream on to whoever requests it. You can play with it some more later. Here's the whole configuration guide, which shows you how to forward streams to other places (such as Twitch), save recordings of uploads, output stats, etc.

Restart nginx with:

$ sudo /usr/local/nginx/sbin/nginx -s stop
$ sudo /usr/local/nginx/sbin/nginx
Step 4: Testing!

Your server should now be ready to accept RTMP streams! Let's try it out.

Create a new profile in OBS, and change your Broadcast Settings thusly:

Streaming Service: Custom
Server: rtmp://<your server ip>/live
Play Path/Stream Key: test
You may be wondering where that play path "test" came from. Well, we just made it up, just now. You can basically make up any play path and stream to it, and put that path into an RTMP player, and it will play back. For simple purposes, authentication isn't necessary in my experience.

You should now be able to start streaming to your server. If you hit "Start Streaming" and don't get an error from OBS, that's a good sign.

So how do you watch it? The easiest way to do so is with VLC (v2.1.0 or later). Just Open a Network Stream and enter in rtmp://<your server ip>/live/test as the URL. If it all worked right, then you should now be seeing your stream in VLC!

You now have a working RTMP server! Congrats!

What now?

You can add the stream to OBS itself using the Media source or VLC source, or use something like JWPlayer to play back the RTMP stream on a web site you set up.

You can also use your RTMP server to forward to other streaming services and channels! Underneath the "record off;" line in your nginx.conf, add the following:


push rtmp://<other streaming service rtmp url>/<stream key>

And any stream streamed to that application will be forwarded on to the other service, as well as being served up from the server! You can add multiple "pushes" to forward the stream to multiple locations.

FAQ

Q: Why nginx? Why not crtpmserver/Red5/Wowza?
A: I've tried crtmpserver (aka rtmpd) before, and it works, but is harder to use. If you need to use RTSP, though, instead of RTMP, then I recommend it, since the nginx RTMP module doesn't do RTSP. Red5 seemed overly complicated and heavy to me, and written in Java...you can use it if you want though. I haven't investigated it deeply. Wowza is not free. You can use what you like, but so far nginx is lightweight, easy to use, and free, so you're going to have to pry it from my cold, dead hands.

Q: How do I X?
A: This FAQ is still in progress...ask questions so I can add them here!