# Explainer - Constructor for encoded frames

## Problem to be solved

In some use cases, we need to produce RTC encoded frames with custom metadata.

For example, we might want to use different timestamps to introduce a delay.
A more interesting use case is  relaying incoming encoded frames from one peer
connection to one or more other peer connections as outgoing data
[use case 3.2.2](https://w3c.github.io/webrtc-nv-use-cases/#auction).
This is covered by [another explainer](relay-explainer.md)

## Approach

We propose introducing a constructor for encoded frames that takes another frame
as input + customized metadata.

## Relevant other needed changes

Supporting the relay use case requires an API to insert encoded frames into a
peer connection. This is covered by [this explainer](relay-explainer.md).

## Sample code

Consider a scenario where the application wants to introduce a delay for
playback.

```
// code in main.js file
const worker = new Worker('worker.js');

// Let pc be an already configured peer connection.
pc.ontrack = ({receiver}) =>
  receiver.transform = new RTCRtpScriptTransform(worker, {name: "receiverTransform1"});
```

```
// code in worker.js file
onrtctransform = async ({transformer: {readable, writable, options}}) => {
  await readable.pipeThrough(new TransformStream({transform})).pipeTo(writable);
  function transform(frame, controller) {
    // getCustomMetadata is application-defined (e.g., adjust timestamps)
    const newFrame = new RTCRtpEncodedVideoFrame(frame, getUnifiedMetadata(frame));
    controller.enqueue(newFrame);
  }
}
```

For the reliable forwarding use case, see [this explainer](relay-explainer.md).
