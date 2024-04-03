* 2.1. What information might this feature expose to Web sites or other parties, and for what purposes is that exposure necessary?

This feature does not expose any information to Web sites. It is a constructor that creates new RTCEncodedVideoFrames or RTCEncodedAudioFrames containing data received as input.


* 2.2. Do features in your specification expose the minimum amount of information necessary to enable their intended uses?
Yes.


* 2.3. How do the features in your specification deal with personal information, personally-identifiable information (PII), or information derived from them?
The feature does not deal with personal information, PII or information derived from them.


* 2.4. How do the features in your specification deal with sensitive information?
The feature does not deal with sensitive information.

* 2.5. Do the features in your specification introduce new state for an origin that persists across browsing sessions?
No.

* 2.6. Do the features in your specification expose information about the underlying platform to origins?
No.

* 2.7. Does this specification allow an origin to send data to the underlying platform?
No. However, this is part of WebRTC encoded transform, which allows data to be sent via an RTCPeerConnection. The constructor subject of this proposal only allows the additional ability to send customized metadata as part of an encoded frame.

* 2.8. Do features in this specification enable access to device sensors?
No.

* 2.9. Do features in this specification enable new script execution/loading mechanisms?
No.

* 2.10. Do features in this specification allow an origin to access other devices?
No.

* 2.11. Do features in this specification allow an origin some measure of control over a user agent’s native UI?
No.

* 2.12. What temporary identifiers do the features in this specification create or expose to the web?
None.

* 2.13. How does this specification distinguish between behavior in first-party and third-party contexts?
No distinction.

* 2.14. How do the features in this specification work in the context of a browser’s Private Browsing or Incognito mode?
No difference.

* 2.15. Does this specification have both "Security Considerations" and "Privacy Considerations" sections?
This is part of WebRTC Encoded Transform, which has a [Privacy and Security considerations section](https://w3c.github.io/webrtc-encoded-transform/#privacy).

* 2.16. Do features in your specification enable origins to downgrade default security protections?
No

* 2.17. How does your feature handle non-"fully active" documents?
No special handling.

* 2.18. What should this questionnaire have asked?
N/A
