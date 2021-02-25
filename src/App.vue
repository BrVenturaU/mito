<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <Header title="Multi Language Interactive Tool"/>
    <BaseForm></BaseForm>
    <BrailleTranslatorForm></BrailleTranslatorForm> 
    <ImageScannerForm></ImageScannerForm> 
    <OcrForm></OcrForm>
    <TextToSpeechForm></TextToSpeechForm> 
  </div>
</template>

<script>
import Header from './components/Header.vue'
import BaseForm from './components/BaseForm.vue'
import BrailleTranslatorForm from './components/BrailleTranslatorForm.vue'
import ImageScannerForm from './components/ImageScannerForm.vue'
import OcrForm from './components/OcrForm.vue'
import TextToSpeechForm from './components/TextToSpeechForm.vue'

export default {
  name: 'App',
  components: {
    Header,
    BaseForm,
    BrailleTranslatorForm,
    ImageScannerForm,
    OcrForm,
    TextToSpeechForm
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
