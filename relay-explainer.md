# Explainer - Create functions for encoded data

## Problem to be solved

In some use cases, we need to move encoded data beyond the confines of a single PeerConnection.

One of these cases is relaying incoming encoded data to one or more other PeerConnections as outgoing data [use case 3.2.2](https://w3c.github.io/webrtc-nv-use-cases/#auction)

Addressing this case in requires the ability to:

* Create new encoded frames
* Manipulate encoded frame metadata to conform with the requirements of its destination
* Enqueue those frames on a sending peer connection that is not the same peer connection where the frame originated on

## Approach

The approach proposed as a minimum viable API consists of 2 functions:

* A constructor for encoded frames that takes data from an existing frame + customized metadata
* A class that allows enqueing encoded frames into a sender peer connection and exposing control signals

## Relevant other needed changes

Creating and inserting frames into or removing frames from an encoded media flow requires the other components handling the media flow to
respond in a reasonable manner.

This may include the need for a number of signals and appropriate treatment thereof:

* If the sink is subject to congestion, the inserting entity must be made aware of the current capacity of the sink
* If the sink can require the transmission of I-frames to resynchronize decoding, those requests must be made available to the inserting entity
* If the source is capable of adapting its transmission rate, the handler taking data from it must be able to make the source aware of its current capacity (which may or may not be the same as the downstream capacity).
* If the source is capable of producing I-frames on demand, the handler taking data from it must be able to make those requests appropriately

It is harmful to the model if there are couplings between the source of encoded data and the sink for encoded data that are not exposed to the handler.


## Sample code

Let's take a scenario where P2P relays are used to forward frames. Depending solely on a local peer for the stream is not very reliable in this setup. For consistent, glitch-free, low latency streaming, a redundant PeerConnection delivering the same stream is also setup(`recvPc1` and `recvPc2`). A peer can then choose to relay this stream to the next peer in the network (`relayPc`). We pass the readable stream of the incoming PeerConnections to a `relayFrames` function which reads the frames and creates new frames with updated metadata that conforms to the sender PeerConnection's requirements (`getUnifiedMetadata`). We then write one of these frames(`isDuplicate`) to the relay peer PerConnection. `getUnifiedMetadata` and `isDuplicate` are application-specific functions.

```
// code in main.js file
const worker = new Worker('worker.js');

// Let recvPc1, recvPc2 be the receiving PCs. 
recvPc{1|2}.ontrack = evt => {
  evt.receiver.transform = new RTCRtpScriptTransform(worker, { name: "receiverTransform" });
};


// Let relayPc be the PC used to relay frames to the next peer. 
worker.onmessage = evt => {
  relayPc.replaceTrack(evt.data);
};
```

```
// code in worker.js file
async function relayFrames(reader, writer, encodedSource) {
    if(!reader || !writer || !encodedSource){
      return;
    }
    while (true) {
        const {frame, done} = await reader.read();
        if (done) return;

        let newFrame = new RTCRtpEncodedVideoFrame(frame, getUnifiedMetadata(frame));
        if(!isDuplicate(newFrame)) {
            encodedSource.enqueue(newFrame);
        }
        // Put the original frame back in the receiver PC
        writer.write(frame);
    }
}

// Code to instantiate reader and writer from the RTPReceiver and RTPSender.
onrtctransform = (event) => {
  if (event.transformer.options.name == "receiverTransform") {
    reader = event.transformer.readable;
    writer = event.transformer.writable;
    if (!encodedSource) {
      encodedSource = new RTCRtpSenderEncodedSource();
      postMessage(encodedSource.handle);
    }
  } else {
    return;
  }

  relayFrames(reader, writer, encodedSource);
};
```
