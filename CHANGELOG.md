For 1.x changes, go [here](https://github.com/twilio/twilio-video.js/blob/support-1.x/CHANGELOG.md).

2.0.0-beta6 (in progress)
=========================

New Features
------------

- The [Dominant Speaker](https://www.twilio.com/docs/video/detecting-dominant-speaker)
  and [Network Quality](https://www.twilio.com/docs/video/using-network-quality-api)
  APIs are generally available.

- Previously, Room emitted "reconnecting" and "reconnected" events while recovering
  from a disruption in your media connection. Now, it will emit these events while
  recovering from a disruption in your signaling connection as well. As of now, this
  is an opt-in feature and can be enabled with a temporary ConnectOptions flag as follows:

  ```js
  const { connect } = require('twilio-video');
  const room = await connect(token, {
    _useTwilioConnection: true
  });
  room.on('reconnecting', error => {
    if (error.code === 53001) {
      console.log('Reconnecting your signaling connection!', error.message);
    } else if (error.code === 53405) {
      console.log('Reconnecting your media connection!', error.message);
    }
  });
  ```

  When you opt in for this feature, you join a Room using our new signaling transport,
  which enables us to detect and recover from disruptions in your signaling connection.
  Whenever your signaling connection is interrupted, the signaling back-end waits
  for you to reconnect for a period of 30-45 seconds, before it determines that you
  have left the Room. As a result, if you close the tab/browser or navigate away from
  your web application without disconnecting from the Room, the other Participants
  will only be notified after the reconnecting period is over. So, we recommend that
  you disconnect from the Room when you detect a tab/browser close or page navigation
  as follows:

  ```js
  window.addEventListener('beforeunload', () => {
    room.disconnect();
  });
  ```

  After twilio-video.js@2.0.0 is generally available, we plan to make this an opt-out
  feature in twilio-video.js@2.1.0, followed by removing our existing SIP-based
  signaling transport altogether in twilio-video.js@2.2.0.

  **Non-compatible changes:** The new signaling transport will:
  - disconnect you from the Room with an [AccessTokenExpiredError](https://www.twilio.com/docs/api/errors/20104)
    if while reconnecting, it detects that your AccessToken is expired. Therefore,
    we recommend that you create an AccessToken with the [ttl](https://www.twilio.com/docs/libraries/reference/twilio-node/3.29.1/AccessToken.html)
    set to the maximum allowed session duration, which is currently *14400 seconds (4 hours)*.
  - reject AccessTokens containing configuration profiles, which were deprecated
    when we [announced](https://www.twilio.com/blog/2017/04/programmable-video-peer-to-peer-rooms-ga.html#room-based-access-control)
    the general availability of twilio-video.js@1.0.0. Use the [Programmable Video REST API](https://www.twilio.com/docs/video/api)
    if you want to override the default settings while creating a Room.

- twilio-video.js will now support versions of Safari that enable Unified Plan as
  the default SDP format. As of now, Unified Plan is enabled by default in the latest
  Safari Technology Preview. (JSDK-2306)

Bug Fixes
---------

- Fixed a bug where `Room.getStats` was throwing a TypeError in Electron 3.x. (JSDK-2267)
- Fixed a bug where the LocalParticipant sometimes failed to publish a LocalTrack
  to a group Room due to media negotiation failure. (JSDK-2219)
- Removed workaround for this [Firefox bug](https://bugzilla.mozilla.org/show_bug.cgi?id=1481335)
  on Firefox 65 and above. (JSDK-2280)
- Fixed a bug where passing invalid RTCIceServer urls in ConnectOptions did not
  raise a MediaConnectionError. (JSDK-2279)

2.0.0-beta5 (January 7, 2019)
=============================

New Features
------------

- `Room.getStats` on Firefox will now consume the spec-compliant `RTCIceCandidateStats`
  available in [versions 65 and above](https://www.fxsitecompat.com/en-CA/docs/2018/rtcicecandidatestats-has-been-updated-to-the-latest-spec/). (JSDK-2235)
- Participants will now be able to stay in the Room and recover their media
  connections if the media server becomes unresponsive, instead of being
  disconnected. (JSDK-2245)
- `Room.getStats` is now supported on versions of Safari that enable Unified Plan
  as the default SDP format. It is not supported on earlier versions due to this
  [Safari bug](https://bugs.webkit.org/show_bug.cgi?id=192601).
  We have updated the documentation to reflect this behavior. (JSDK-2201)
- `Room.getStats` on Chrome now uses the WebRTC 1.0 compliant version of the
  RTCPeerConnection's `getStats` API. (JSDK-2182)
- Worked around Firefox 63's [deprecation](https://blog.mozilla.org/webrtc/getstats-isremote-65/)
  of the `isRemote` property in `RTCInboundRTPStreamStats` and `RTCOutboundRTPStreamStats`. (JSDK-2222)

2.0.0-beta4 (November 29, 2018)
===============================

Bug Fixes
---------

  - Fixed a bug where, when a Safari Participant joins a Room after a Firefox Participant,
    it did not receive video frames for VideoTracks published by the Firefox Participant. (JSDK-2224)

2.0.0-beta3 (November 20, 2018)
===============================

Bug Fixes
---------

- Fixed a bug where unpublishing a LocalTrack from within one of its event
  listeners that have been added before publishing it to the Room throws a
  TypeError. (JSDK-2212)

Note for Electron developers
----------------------------

- twilio-video.js will no longer be usable on Electron 2.x or below. Please
  upgrade to Electron 3.x or higher.

2.0.0-beta2 (October 1, 2018)
=============================

Bug Fixes
---------

- Fixed a bug where calling a LocalVideoTrack's `stop` method did not stop the
  video capture, and thereby did not turn the camera light off. (JSDK-2156)
- Fixed a bug where calling LocalParticipant's `unpublishTrack` on a LocalTrack
  that was being published to a Room also stopped the LocalTrack. (JSDK-2169) 

2.0.0-beta1 (August 10, 2018)
=============================

Breaking Changes
----------------

- Google Chrome, starting from version 72, will enable Unified Plan as the default
  SDP format. This version of twilio-video.js will choose Plan B as the SDP format
  in order to continue supporting Google Chrome versions 72 and above until Unified
  Plan support is added. For more details, please refer to this [advisory](https://support.twilio.com/hc/en-us/articles/360012782494-Breaking-Changes-in-Twilio-Video-JavaScript-SDKs-December-2018-).
- RemoteParticipant no longer emits the deprecated "trackAdded" and "trackRemoved"
  events. Use the "trackSubscribed" and "trackUnsubscribed" events instead.
- LocalParticipant no longer contains the deprecated `addTrack`, `addTracks`,
  `removeTrack` and `removeTracks` methods. Use `publishTrack`, `publishTracks`,
  `unpublishTrack`, and `unpublishTracks` instead.
- RemoteTrack no longer has the deprecated `id` property. Use the `sid` or `name`
  properties instead.
- RemoteTrack no longer has the deprecated `isSubscribed` property. Use the
  corresponding RemoteTrackPublication's `isSubscribed` property instead.
- RemoteTrack no longer emits the deprecated "unsubscribed" event. Use the
  corresponding RemoteTrackPublication's "unsubscribed" event instead.
- Participant's `trackPublications` collection is now renamed to `tracks`.
  Similarly, `audioTrackPublications` is now renamed to `audioTracks`,
  `dataTrackPublications` is now renamed to `dataTracks`, and
  `videoTrackPublications` is now renamed to `videoTracks`. Participant no
  longer maintains the deprecated Track-based collections.
- We removed support for Bower.
