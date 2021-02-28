<template>
  <b-row>
    <b-col cols="12">
      <h2>Image Scanner AI</h2>
      <!-- <b-button @click="enableWebCam">Hola</b-button> -->
      <section id="demos">
        <div id="liveView" class="camView">
          <b-button
            class="mb-3"
            variant="primary"
            id="webcamButton"
            @click="enableWebCam"
            >Enable Webcam <b-icon icon="camera-video"></b-icon
          ></b-button>
          <video id="webcam" autoplay width="640" height="480"></video>
        </div>
      </section>
    </b-col>
  </b-row>
</template>

<script>
import * as cocoSsd from "@tensorflow-models/coco-ssd";
export default {
  name: "ImageScanner",
  methods: {
    enableWebCam() {
      const video = document.getElementById("webcam");
      const liveView = document.getElementById("liveView");
      const demosSection = document.getElementById("demos");
      const enableWebcamButton = document.getElementById("webcamButton");
      // Check if webcam access is supported.
      function getUserMediaSupported() {
        return !!(
          navigator.mediaDevices && navigator.mediaDevices.getUserMedia
        );
      }

      // If webcam supported, add event listener to button for when user
      // wants to activate it to call enableCam function which we will
      // define in the next step.
      if (getUserMediaSupported()) {
        enableWebcamButton.addEventListener("click", enableCam);
      } else {
        console.warn("getUserMedia() is not supported by your browser");
      }

      // Enable the live webcam view and start classification.
      function enableCam(event) {
        // Only continue if the COCO-SSD has finished loading.
        if (!model) {
          return;
        }

        // Hide the button once clicked.
        event.target.classList.add("removed");

        // getUsermedia parameters to force video but not audio.
        const constraints = {
          video: true,
        };

        // Activate the webcam stream.
        navigator.mediaDevices.getUserMedia(constraints).then(function(stream) {
          video.srcObject = stream;
          video.addEventListener("loadeddata", predictWebcam);
        });
      }

      var children = [];

      function predictWebcam() {
        // Now let's start classifying a frame in the stream.
        model.detect(video).then(function(predictions) {
          // Remove any highlighting we did previous frame.
          for (let i = 0; i < children.length; i++) {
            liveView.removeChild(children[i]);
          }
          children.splice(0);

          // Now lets loop through predictions and draw them to the live view if
          // they have a high confidence score.
          for (let n = 0; n < predictions.length; n++) {
            // If we are over 66% sure we are sure we classified it right, draw it!

            if (predictions[n].score > 0.66) {
              const p = document.createElement("p");
              p.innerText =
                predictions[n].class +
                " - with " +
                Math.round(parseFloat(predictions[n].score) * 100) +
                "% confidence.";
              p.style =
                "margin-left: " +
                predictions[n].bbox[0] +
                "px; margin-top: " +
                (predictions[n].bbox[1] - 10) +
                "px; width: " +
                (predictions[n].bbox[2] - 10) +
                "px; top: 0; left: 0;";

              const highlighter = document.createElement("div");
              highlighter.setAttribute("class", "highlighter");
              highlighter.style =
                "left: " +
                predictions[n].bbox[0] +
                "px; top: " +
                predictions[n].bbox[1] +
                "px; width: " +
                predictions[n].bbox[2] +
                "px; height: " +
                predictions[n].bbox[3] +
                "px;";

              liveView.appendChild(highlighter);
              liveView.appendChild(p);
              children.push(highlighter);
              children.push(p);
            }
          }

          // Call this function again to keep predicting when the browser is ready.
          window.requestAnimationFrame(predictWebcam);
        });
      }

      // Store the resulting model in the global scope of our app.
      var model = undefined;

      // Before we can use COCO-SSD class we must wait for it to finish
      // loading. Machine Learning models can be large and take a moment
      // to get everything needed to run.
      // Note: cocoSsd is an external object loaded from our index.html
      // script tag import so ignore any warning in Glitch.
      cocoSsd.load().then(function(loadedModel) {
        model = loadedModel;
        // Show demo section now model is ready to use.
        demosSection.classList.remove("invisible");
      });
    },
  },
};
</script>

<style>
video {
  display: block;
  width: calc(100% - 20px);
}

section {
  opacity: 1;
  transition: opacity 500ms ease-in-out;
}

.removed {
  display: none;
}

.invisible {
  opacity: 0.2;
}

.camView {
  position: relative;
  float: left;
  width: calc(100% - 20px);
  margin: 10px;
  cursor: pointer;
}

.camView p {
  position: absolute;
  padding: 5px;
  background-color: rgba(13, 27, 158, 0.85);
  color: #fff;
  border: 1px dashed rgba(255, 255, 255, 0.7);
  z-index: 2;
  font-size: 12px;
}

.highlighter {
  background: rgba(33, 33, 43, 0.25);
  border: 1px dashed #fff;
  z-index: 1;
  position: absolute;
}
</style>
