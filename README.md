![Jetstream](img/jetstream.png)

Jetstream is live-streaming for the people. It doesn't sign you up, gather your data, ask for money, or drive engagement metrics. It *just works*, direct from the browser, with no setup required.

The video and audio is streamed using WebRTC to a Janus server that relays the stream to the viewers.

## Usage

You can use the public jetstream instance at [jetstream.club](https://jetstream.club), or self-host it on your own server.

Usage should be self-explanatory: click the button and send the stream URL to your friends.

## Installation

Clone this git repo on to your server:

    $ git clone https://github.com/jes/jetstream

The HTML part is a static site, so you just need to expose this directory over HTTP.

### Janus

Install [Janus](https://github.com/meetecho/janus-gateway). Make sure you allow connections to it in your firewall.

Jetstream abuses the `videoroom` plugin in Janus. Perhaps one day it'll use a custom plugin, but it's not obvious that that would be better.

### TLS

Some clients refuse to stream the webcam over an unencrypted link, so you'll want to use [Let's Encrypt](https://letsencrypt.org/) to get a certificate. Make sure you configure Janus to use the certificate as well.

### Example nginx config

```
server {
    listen 80;
    server_name jetstream.club www.jetstream.club;
    location / {
        rewrite ^(.*) https://jetstream.club$1 permanent;
    }
}

server {
    listen 443 ssl;
    ssl_certificate /etc/letsencrypt/live/jetstream.club/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/jetstream.club/privkey.pem;
    ssl_protocols TLSv1.2 TLSv1.1 TLSv1;
    ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;

    server_name jetstream.club www.jetstream.club;

    root /home/jes/jetstream;
    index index.html;
}
```

## Help wanted

There are a number of things that could stand to be improved:

 * we'd want a way to find out how many viewers the stream has, and show this to both the publisher and the subscribers (may require a custom Janus plugin)
 * when the publisher goes away and comes back (e.g. reloaded the browser), it would be handy if they were still publishing to the same room id
 * when the publisher goes away and doesn't come back, it would be handy if the subscriber didn't refresh the page every few seconds forever
 * what happens if the stream is really-high resolution? do we need to explicitly scale it down?
 * better user experience on mobile, for both publishers and subscribers
 * switch audio/video source mid-stream
 * more detailed installation instructions (particularly regarding install and setup of Janus)
 * make sure that malicious users can't publish to somebody else's room id
 * let the publisher temporarily disable audio or video mid-stream
 * shorter room ids (maybe short alpha-numeric strings like YouTube videos, maybe base58 of room id number would work?)
 * let the subscribers view in full-screen
 * let the subscribers know when the publisher has disappeared, instead of just freezing

## Contact

Jetstream is a project by [James Stanley](https://incoherency.co.uk/). You can email me at [james@incoherency.co.uk](mailto:james@incoherency.co.uk) or contact me on [Ricochet](https://ricochet.im/) at ricochet:it2j3z6t6ksumpzd.
