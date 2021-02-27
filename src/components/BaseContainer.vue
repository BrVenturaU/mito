<template>
    <b-row>
        <b-col cols="12">
            <div class="card mb-2 mx-auto shadow" style="max-width: 70%">
                <img src="@/assets/card-header-img.svg" class="img-fluid card-img-top" alt="App Image">
                <b-tabs content-class="my-4" justified active-nav-item-class="font-weight-bold text-white bg-dark">
                    <b-tab title="Text To Speech" active>
                        <TextToSpeechForm></TextToSpeechForm> 
                    </b-tab>
                    <b-tab title="Image to Text">
                        <OcrForm></OcrForm>
                    </b-tab>
                    <b-tab title="Braille Translator">
                        <BrailleTranslatorForm></BrailleTranslatorForm> 
                    </b-tab>
                    <b-tab title="Image Scanner AI">
                        <ImageScannerForm></ImageScannerForm> 
                    </b-tab>
                </b-tabs>
            </div>
        </b-col>
    </b-row>
</template>
<script>
import BrailleTranslatorForm from './BrailleTranslatorForm.vue'
import ImageScannerForm from './ImageScannerForm.vue'
import OcrForm from './OcrForm.vue'
import TextToSpeechForm from './TextToSpeechForm.vue'
export default {
    name: 'BaseContainer',
    components:{
        BrailleTranslatorForm,
        ImageScannerForm,
        OcrForm,
        TextToSpeechForm
    }
}
</script>
<style>
     a.nav-link{
        border: none !important;
        border-radius: 0px !important;
        padding: 1rem;
    }
</style>