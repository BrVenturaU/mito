<template>
  <b-row class="m-2">
    <b-col cols="12">
      <h3 class="mb-3">{{ validated ? "SpeechToText" : "TextToSpeech" }}</h3>
      <b-form-textarea
        id="textarea"
        placeholder="Type something..."
        rows="5"
        no-resize
      ></b-form-textarea>
      <div class="mt-3">
        <!-- Via array of string IDs passed to directive value -->
        <b-button
          class="p-3"
          block
          variant="outline-info"
          v-b-toggle="['option-a', 'option-b']"
          >Select...</b-button
        >

        <!-- Elements to collapse -->
        <b-collapse id="option-a" class="mt-2">
          <b-button
            id="speech-to-text"
            @click="speechToText"
            class="p-3"
            block
            variant="outline-info"
            v-b-toggle="['option-a', 'option-b']"
            >SpeechToText</b-button
          >
        </b-collapse>
        <b-collapse id="option-b" class="mt-2">
          <b-button
            id="text-to-speech"
            @click="textToSpeech"
            class="p-3"
            block
            variant="outline-info"
            v-b-toggle="['option-a', 'option-b']"
            >TextToSpeech</b-button
          >
        </b-collapse>
      </div>
    </b-col>
    <b-col v-if="validated" md="12" cols="12" class="mb-3">
      <b-icon
        icon="mic-fill"
        animation="fade"
        font-scale="8"
        class="cursor-pointer rounded-circle bg-danger mt-3 p-3"
        variant="light"
        @click="speechToText"
      ></b-icon>
      <p id="action" class="mt-3">{{ (action = "Enable microphone") }}</p>
    </b-col>

    <b-col v-else md="12" cols="12" class="mb-3">
      <select class="form-control mt-3" id="voices" v-model="selectedVoice">
        <option
          v-for="(voice, index) in voiceList"
          :key="index"
          :data-lang="voice.lang"
          :value="index"
        >
          {{ voice.name }} ({{ voice.lang }})
        </option>
      </select>

      <b-icon
        icon="soundwave"
        font-scale="8"
        class="cursor-pointer rounded-circle bg-info mt-3 p-3"
        variant="light"
        @click="textToSpeech"
      ></b-icon>
      <p id="action" class="mt-3">{{ (action = "Listen to the text") }}</p>
    </b-col>
  </b-row>
</template>
<script>
export default {
  name: "TextToSpeechForm",
  data() {
    return {
      validated: true,
      action: "",
      isLoading: true,
      selectedVoice: 0,
      synth: window.speechSynthesis,
      voiceSpeech: new window.SpeechSynthesisUtterance(),
      voices: [],
    };
  },
  mounted() {
    // wait for voices to load
    // I can't get FF to work without calling this first
    // Chrome works on the onvoiceschanged function
    this.voiceList = this.synth.getVoices();

    if (this.voiceList.length) {
      this.isLoading = false;
    }

    this.synth.onvoiceschanged = () => {
      this.voiceList = this.synth.getVoices();
      // give a bit of delay to show loading screen
      // just for the sake of it, I suppose. Not the best reason
      setTimeout(() => {
        this.isLoading = false;
      }, 800);
    };
  },
  computed: {
    selector: function () {
      const selector = {
        // Get textarea reference from template
        textarea: document.querySelector("#textarea"),
        // Get action reference from template
        action: document.querySelector("#action"),
      };
      return selector;
    },
  },
  methods: {
    speechToText() {
      this.validated = true;
      var SpeechRecognition =
        window.SpeechRecognition || window.webkitSpeechRecognition;
      var recognition = new SpeechRecognition();

      // This runs when the speech recognition service starts
      recognition.onstart = () => {
        this.selector.action.innerHTML = "Listening, please speak...";
      };

      // This runs when the speech recognition service ends.
      recognition.onspeechend = () => {
        this.selector.action.innerHTML =
          "Stopped listening, hope you are done...";
        recognition.stop();
        setTimeout(() => {
          this.selector.action.innerHTML = "Enable microphone";
        }, 1000);
      };

      // This runs when the speech recognition service returns result
      recognition.onresult = (event) => {
        let transcript = event.results[0][0].transcript;
        this.selector.textarea.value += `${transcript}`;
      };

      // start recognition
      recognition.start();
    },
    async textToSpeech() {
      this.validated = false;

      setTimeout(() => {
        this.selector.action.innerHTML = "Listen to the Text";
      }, 1000);

      if ("speechSynthesis" in window) {
        // it should be 'craic', but it doesn't sound right
        let message = this.selector.textarea.value;
        if (message === "") {
          this.voiceSpeech.text = `The field is empty, try enter something`;
          this.voiceSpeech.voice = this.voiceList[this.selectedVoice];
          this.synth.speak(this.voiceSpeech);
        } else {
          this.voiceSpeech.text = `The text says: ${this.selector.textarea.value}`;
          this.voiceSpeech.voice = this.voiceList[this.selectedVoice];
          this.synth.speak(this.voiceSpeech);
        }
      } else {
        alert("Sorry, your browser doesn't support the speech synthesis 😥");
      }
    },
  },
};
</script>
<style scoped>
.cursor-pointer {
  cursor: pointer;
}
</style>
