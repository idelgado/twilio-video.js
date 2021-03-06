Common Issues
=============

Having an issue with twilio-video.js? Unable to see remote Participants' Tracks?
Review this list of common issues to determine whether or not your issue is
known or a workaround is available. Please also take a look at the
[CHANGELOG.md](CHANGELOG.md) to see if your issue is known for a particular
release. If your issue hasn't been reported, consider submitting
[a new issue](https://github.com/twilio/twilio-video.js/issues/new).

Network Handoff (opt-in)
------------------------

If you have opted in for reconnecting to the Room when the signaling connection
is interrupted, then the signaling back-end will raise an [AccessTokenInvalidError](https://www.twilio.com/docs/api/errors/20101)
if the reconnect attempt takes too long. In the near future, a different TwilioError
will be raised that better describes this behavior.

Firefox 64/65 Participants may sometimes experience media loss in Group Rooms
-----------------------------------------------------------------------------

Mozilla introduced a [regression](https://bugzilla.mozilla.org/show_bug.cgi?id=1526477)
in Firefox 64, because of which Firefox 64/65 Participants in a Group Room may
sometimes experience media loss. For more details, please refer to this [issue](https://github.com/twilio/twilio-video.js/issues/565).

Firefox 63+ Incompatible with Mobile SDKs 1.x/2.x in Peer-to-Peer Rooms
-----------------------------------------------------------------------

Firefox 63 [introduced](https://blog.mozilla.org/webrtc/how-to-avoid-data-channel-breaking/)
a new SDP format for data channel negotiation. This new SDP format has caused
incompatibility with the 1.x and 2.x Android and iOS Video SDKs when used with
Peer-to-Peer Rooms. Please refer to this [issue](https://github.com/twilio/twilio-video.js/issues/544)
to find out if your app is impacted and how to overcome it.

Chrome and Firefox Beta, Canary, Nightly, etc., Releases
--------------------------------------------------------

We always ensure compatibility with the current Chrome and Firefox stable
releases; however, because some of the APIs we rely upon, like WebRTC, are under
active development in the browsers, we cannot guarantee compatibility with
Canary or Nightly releases. We will, however, stay abreast of changes in browser
beta releases so that we can adopt changes in advance of each browser's next
stable release.

Safari
------

### Safari 12.1 Participants cannot publish Track(s) of the same kind as the unpublished Track(s)

Because of this Safari 12.1 (currently in Beta) [bug](https://bugs.webkit.org/show_bug.cgi?id=195489),
once a Participant unpublishes a MediaTrack of any kind (audio or video), it will
not be able to publish another MediaTrack of the same kind. DataTracks are not affected.
We have escalated this bug to the Safari Team and keeping track of related developments.

### Network Quality API not working

We are working to fix a bug in twilio-video.js where Safari clients do not
receive Network Quality Score updates in a Group Room. (JSDK-2133)

### Experimental support

twilio-video.js 1.2.1 introduces experimental support for Safari 11 and newer.
Support for Safari is "experimental" because, at the time of writing, Safari
does not support VP8. This means you may experience codec issues in Group Rooms.
You may also experience codec issues in Peer-to-Peer (P2P) Rooms containing
Android- or iOS-based Participants who do not support H.264. However, P2P Rooms
with browser-based Participants should work.

Also, there is an existing bug in twilio-video.js where publication of
DataTracks after joining a Group Room never reaches completion (JSDK-2161).
While we work to fix this bug, you can work around this by publishing your
DataTracks while connecting to a Room using the `tracks` property of
ConnectOptions.

### Angular

There is a misinteraction between one of Angular's libraries, Zone.js, and
Safari's RTCPeerConnection APIs. For more information, see [here](https://github.com/angular/zone.js/issues/883)
for the issue filed against Zone.js and [here](https://bugs.webkit.org/show_bug.cgi?id=175802)
for the issue filed against WebKit. In order to work around this issue, you
should include Zone.js's webapis-rtc-peer-connection.js in your app, after
loading Zone.js. For example,

```html
<script src="node_modules/zone.js/dist/zone.js"></script>
<script src="node_modules/zone.js/dist/webapis-rtc-peer-connection.js"></script>
```

Microsoft Edge
--------------

Although Microsoft Edge includes some WebRTC support, it also includes some
limitations that make it difficult to support today. We plan on adding Edge
support to twilio-video.js, but we may do so by leveraging Edge's ORTC APIs
instead.

Internet Explorer and WebRTC-incompatible Browsers
--------------------------------------------------

twilio-video.js requires WebRTC, which is not supported by Internet Explorer.
While twilio-video.js will load in Internet Explorer and other browsers that
do not support WebRTC, attempting to connect to a Room or attempting to acquire
LocalTracks will fail.

Firefox
-------

### RemoteDataTrack Properties (`maxPacketLifeTime` and `maxRetransmits`)

Firefox has not yet implemented getter's for RTCDataChannel's
`maxPacketLifeTime` and `maxRetransmits` properties. As such, we cannot raise
accurate values for the `maxPacketLifeTime` and `maxRetransmits` properties on
RemoteDataTrack. (Setting these values still works, though!) See below for
issues on the Firefox bug tracker:

* [Bug 881532](https://bugzilla.mozilla.org/show_bug.cgi?id=881532)
* [Bug 1278384](https://bugzilla.mozilla.org/show_bug.cgi?id=1278384)

Also, there is an existing bug in twilio-video.js where publication of
DataTracks after joining a Group Room never reaches completion (JSDK-2161).
While we work to fix this bug, you can work around this by publishing your
DataTracks while connecting to a Room using the `tracks` property of
ConnectOptions.

Aggressive Browser Extensions and Plugins
-----------------------------------------

Some browser extensions and plugins will disable WebRTC APIs, causing
twilio-video.js to fail. Examples of such plugins include

* uBlockOrigin-Extra
* WebRTC Leak Prevent
* Easy WebRTC Block

These are unsupported and likely to break twilio-video.js. If you are having
trouble with twilio-video.js, ensure these are not running.
