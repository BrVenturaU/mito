<template>
    <div class="container">
        <b-row class="mb-4">
            <b-col cols="12">
                <h2 class="mb-4">Image to Text (OCR)</h2>
                <b-form enctype="multipart/form-data">
                    <b-form-group
                        class="text-left"
                        label="Add a image: *"
                        label-for="file"
                        >
                        <b-form-file 
                            id="file"
                            accept="image/*"
                            v-model="file"
                            @change="onChangeFile"
                            :state="Boolean(file)"
                            placeholder="Choose a file or drop it here..."
                            drop-placeholder="Drop file here..."
                            required class="mb-3"></b-form-file>
                        </b-form-group>
                        <b-form-group
                            class="text-left"
                            label="Select the image's language: *"
                            label-for="lang"
                        >
                            <v-select id="lang" label="nat" :options="options" v-model="selected" class="mb-3"></v-select>
                        </b-form-group>
                        <b-progress height="2.1rem" v-if="isLoading">
                            <b-progress-bar style="font-size: 1rem;" :value="progress" :label-html="progressMessage" variant="info" striped :animated="true"></b-progress-bar>
                        </b-progress>

                        <div class="my-3">
                            <span><strong>Selected file:</strong> {{ file ? file.name : 'None' }}</span>
                        </div>
                    <b-button class="mr-2" variant="primary" @click="recognize">Scan</b-button>
                    <b-button type="reset" variant="danger" @click="reset">Reset</b-button>
                </b-form>
            </b-col>
        </b-row>
        <b-row>
            <b-col class="text-left" cols="12" v-if="text != ''">
                <b-button class="mb-2 mr-2" variant="success" @click="copy">
                    <b-icon :icon="copied ?'clipboard-check' : 'clipboard'" :animation="copied ? '' : 'throb'" font-scale="1.5"></b-icon>
                </b-button>
                <span v-if="copied">Copied!</span>
                <b-form-textarea
                    id="textarea"
                    v-model="text"
                    placeholder="Result..."
                    :rows="lines"
                ></b-form-textarea>
      
            </b-col>
        </b-row>
    </div>
</template>
<script>
import { createWorker, PSM, OEM } from 'tesseract.js';

export default {
    name: 'OcrForm',
    data() {
        return {
            file: null,
            isLoading: false,
            progress:0,
            progressMessage: '',
            text:'',
            lines:10,
            copied: false,
            selected: {
                name: 'afr',
                nat: 'Afrikaans'
            }
        }
    },
    created(){
        this.options = languajes;
    },
    methods: {
        recognize(){
            let vm = this;
            if(vm.file == null) return
            if(vm.selected == null){
                vm.selected = vm.options[0];
                return
            }
                
            vm.isLoading = true;
            const worker = createWorker({
                logger: m => {
                    vm.progress = m.progress * 100;
                    vm.progressMessage = `${m.status} - ${vm.progress.toFixed(2)}%`;
                },
            });
            let getText = async () => {
                await worker.load();
                await worker.loadLanguage(vm.selected.name);
                await worker.initialize(vm.selected.name, OEM.LSTM_ONLY);
                await worker.setParameters({
                    tessedit_pageseg_mode: PSM.SINGLE_BLOCK,
                });
                const data = await worker.recognize(vm.file);
                await worker.terminate();
                return data;
            }

            getText().then(response =>{
                vm.isLoading = false;
                vm.progress = 0;
                vm.progressMessage = "";
                vm.lines = response.data.lines.length;
                vm.text = response.data.text;
            })
            .catch(error => {
                console.log(error.message);
            });
        },
        copy(){
            var copyText = document.getElementById("textarea");
            copyText.select();
            copyText.setSelectionRange(0, this.text.length); 

            document.execCommand("copy");

            this.copied = true;
        },
        onChangeFile(){
            this.reset();
        },
        reset(){
            this.copied = false;
            this.text = '';
            this.lines = 10;
            this.htmlResult = '';
        }
    },
}

const languajes = [
    {
        name: 'afr',
        nat: 'Afrikaans'
    },
    {
        name: 'amh',
        nat: 'Amharic'
    },
    {
        name: 'ara',
        nat: 'Arabic'
    },
    {
        name: 'asm',
        nat: 'Assamese'
    },
    {
        name: 'aze',
        nat: 'Azerbaijani'
    },
    {
        name: 'aze_cyrl',
        nat: 'Azerbaijani-Cyrillic'
    },
    {
        name: 'bel',
        nat: 'Belarusian'
    },
    {
        name: 'ben',
        nat: 'Bengali'
    },
    {
        name: 'bod',
        nat: 'Tibetan'
    },
    {
        name: 'bos',
        nat: 'Bosnian'
    },
    {
        name: 'bul',
        nat: 'Bulgarian'
    },
    {
        name: 'cat',
        nat: 'Catalan-Valencian'
    },
    {
        name: 'ceb',
        nat: 'Cebuano'
    },
    {
        name: 'ces',
        nat: 'Czech'
    },
    {
        name: 'chi_sim',
        nat: 'Chinese-Simplified'
    },
    {
        name: 'chi_tra',
        nat: 'Chinese-Traditional'
    },
    {
        name: 'chr',
        nat: 'Cherokee'
    },
    {
        name: 'cym',
        nat: 'Welsh'
    },
    {
        name: 'dan',
        nat: 'Danish'
    },
    {
        name: 'deu',
        nat: 'German'
    },
    {
        name: 'dzo',
        nat: 'Dzongkha'
    },
    {
        name: 'ell',
        nat: 'Greek-Modern-(1453-)'
    },
    {
        name: 'eng',
        nat: 'English'
    },
    {
        name: 'enm',
        nat: 'English-Middle-(1100-1500)'
    },
    {
        name: 'epo',
        nat: 'Esperanto'
    },
    {
        name: 'est',
        nat: 'Estonian'
    },
    {
        name: 'eus',
        nat: 'Basque'
    },
    {
        name: 'fas',
        nat: 'Persian'
    },
    {
        name: 'fin',
        nat: 'Finnish'
    },
    {
        name: 'fra',
        nat: 'French'
    },
    {
        name: 'frk',
        nat: 'German-Fraktur'
    },
    {
        name: 'frm',
        nat: 'French-Middle(ca.1400-1600)'
    },
    {
        name: 'gle',
        nat: 'Irish'
    },
    {
        name: 'glg',
        nat: 'Galician'
    },
    {
        name: 'grc',
        nat: 'Greek-Ancient-(-1453)'
    },
    {
        name: 'guj',
        nat: 'Gujarati'
    },
    {
        name: 'hat',
        nat: 'Haitian-Haitian'
    },
    {
        name: 'heb',
        nat: 'Hebrew'
    },
    {
        name: 'hin',
        nat: 'Hindi'
    },
    {
        name: 'hrv',
        nat: 'Croatian'
    },
    {
        name: 'hun',
        nat: 'Hungarian'
    },
    {
        name: 'iku',
        nat: 'Inuktitut'
    },
    {
        name: 'ind',
        nat: 'Indonesian'
    },
    {
        name: 'isl',
        nat: 'Icelandic'
    },
    {
        name: 'ita',
        nat: 'Italian'
    },
    {
        name: 'ita_old',
        nat: 'Italian-Old'
    },
    {
        name: 'jav',
        nat: 'Javanese'
    },
    {
        name: 'jpn',
        nat: 'Japanese'
    },
    {
        name: 'kan',
        nat: 'Kannada'
    },
    {
        name: 'kat',
        nat: 'Georgian'
    },
    {
        name: 'kat_old',
        nat: 'Georgian-Old'
    },
    {
        name: 'kaz',
        nat: 'Kazakh'
    },
    {
        name: 'khm',
        nat: 'Central'
    },
    {
        name: 'kir',
        nat: 'Kirghiz-Kyrgyz'
    },
    {
        name: 'kor',
        nat: 'Korean'
    },
    {
        name: 'kur',
        nat: 'Kurdish'
    },
    {
        name: 'lao',
        nat: 'Lao'
    },
    {
        name: 'lat',
        nat: 'Latin'
    },
    {
        name: 'lav',
        nat: 'Latvian'
    },
    {
        name: 'lit',
        nat: 'Lithuanian'
    },
    {
        name: 'mal',
        nat: 'Malayalam'
    },
    {
        name: 'mar',
        nat: 'Marathi'
    },
    {
        name: 'mkd',
        nat: 'Macedonian'
    },
    {
        name: 'mlt',
        nat: 'Maltese'
    },
    {
        name: 'msa',
        nat: 'Malay'
    },
    {
        name: 'mya',
        nat: 'Burmese'
    },
    {
        name: 'nep',
        nat: 'Nepali'
    },
    {
        name: 'nld',
        nat: 'Dutch-Flemish'
    },
    {
        name: 'nor',
        nat: 'Norwegian'
    },
    {
        name: 'ori',
        nat: 'Oriya'
    },
    {
        name: 'pan',
        nat: 'Panjabi-Punjabi'
    },
    {
        name: 'pol',
        nat: 'Polish'
    },
    {
        name: 'por',
        nat: 'Portuguese'
    },
    {
        name: 'pus',
        nat: 'Pushto-Pashto'
    },
    {
        name: 'ron',
        nat: 'Romanian-Moldavian-Moldovan'
    },
    {
        name: 'rus',
        nat: 'Russian'
    },
    {
        name: 'san',
        nat: 'Sanskrit'
    },
    {
        name: 'sin',
        nat: 'Sinhala-Sinhalese'
    },
    {
        name: 'slk',
        nat: 'Slovak'
    },
    {
        name: 'slv',
        nat: 'Slovenian'
    },
    {
        name: 'spa',
        nat: 'Spanish-Castilian'
    },
    {
        name: 'spa_old',
        nat: 'Spanish-Castilian-Old'
    },
    {
        name: 'sqi',
        nat: 'Albanian'
    },
    {
        name: 'srp',
        nat: 'Serbian'
    },
    {
        name: 'srp_latn',
        nat: 'Serbian-Latin'
    },
    {
        name: 'swa',
        nat: 'Swahili'
    },
    {
        name: 'swe',
        nat: 'Swedish'
    },
    {
        name: 'syr',
        nat: 'Syriac'
    },
    {
        name: 'tam',
        nat: 'Tamil'
    },
    {
        name: 'tel',
        nat: 'Telugu'
    },
    {
        name: 'tgk',
        nat: 'Tajik'
    },
    {
        name: 'tgl',
        nat: 'Tagalog'
    },
    {
        name: 'tha',
        nat: 'Thai'
    },
    {
        name: 'tir',
        nat: 'Tigrinya'
    },
    {
        name: 'tur',
        nat: 'Turkish'
    },
    {
        name: 'uig',
        nat: 'Uighur-Uyghur'
    },
    {
        name: 'ukr',
        nat: 'Ukrainian'
    },
    {
        name: 'urd',
        nat: 'Urdu'
    },
    {
        name: 'uzb',
        nat: 'Uzbek'
    },
    {
        name: 'uzb_cyrl',
        nat: 'Uzbek-Cyrillic'
    },
    {
        name: 'vie',
        nat: 'Vietnamese'
    },
    {
        name: 'yid',
        nat: 'Yiddish'
    }
];


</script>
<style scoped>

</style>