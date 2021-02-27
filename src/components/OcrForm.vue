<template>
    <div class="container">
        <b-row class="mb-4">
            <b-col cols="12">
                <h2 class="mb-4">Image to Text (OCR)</h2>
                <b-form enctype="multipart/form-data">
                    <b-form-file 
                        accept="image/*"
                        v-model="file"
                        @change="onChangeFile"
                        :state="Boolean(file)"
                        placeholder="Choose a file or drop it here..."
                        drop-placeholder="Drop file here..."
                        required class="mb-3"></b-form-file>

                        <v-select label="NAT" :options="options" v-model="selected" class="mb-3"></v-select>
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
                AFR: 'afr',
                NAT: 'Afrikaans'
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
            vm.isLoading = true;
            const worker = createWorker({
                logger: m => {
                    vm.progress = m.progress * 100;
                    vm.progressMessage = `${m.status} - ${vm.progress.toFixed(2)}%`;
                },
            });
            console.log(Object.entries(vm.selected)[0][1]);
            let getText = async () => {
                await worker.load();
                await worker.loadLanguage(Object.entries(vm.selected)[0][1]);
                await worker.initialize(Object.entries(vm.selected)[0][1], OEM.LSTM_ONLY);
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
        AFR: 'afr',
        NAT: 'Afrikaans'
    },
    {
        AMH: 'amh',
        NAT: 'Amharic'
    },
    {
        ARA: 'ara',
        NAT: 'Arabic'
    },
    {
        ASM: 'asm',
        NAT: 'Assamese'
    },
    {
        AZE: 'aze',
        NAT: 'Azerbaijani'
    },
    {
        AZE_CYRL: 'aze_cyrl',
        NAT: 'Azerbaijani-Cyrillic'
    },
    {
        BEL: 'bel',
        NAT: 'Belarusian'
    },
    {
        BEN: 'ben',
        NAT: 'Bengali'
    },
    {
        BOD: 'bod',
        NAT: 'Tibetan'
    },
    {
        BOS: 'bos',
        NAT: 'Bosnian'
    },
    {
        BUL: 'bul',
        NAT: 'Bulgarian'
    },
    {
        CAT: 'cat',
        NAT: 'Catalan-Valencian'
    },
    {
        CEB: 'ceb',
        NAT: 'Cebuano'
    },
    {
        CES: 'ces',
        NAT: 'Czech'
    },
    {
        CHI_SIM: 'chi_sim',
        NAT: 'Chinese-Simplified'
    },
    {
        CHI_TRA: 'chi_tra',
        NAT: 'Chinese-Traditional'
    },
    {
        CHR: 'chr',
        NAT: 'Cherokee'
    },
    {
        CYM: 'cym',
        NAT: 'Welsh'
    },
    {
        DAN: 'dan',
        NAT: 'Danish'
    },
    {
        DEU: 'deu',
        NAT: 'German'
    },
    {
        DZO: 'dzo',
        NAT: 'Dzongkha'
    },
    {
        ELL: 'ell',
        NAT: 'Greek-Modern-(1453-)'
    },
    {
        ENG: 'eng',
        NAT: 'English'
    },
    {
        ENM: 'enm',
        NAT: 'English-Middle-(1100-1500)'
    },
    {
        EPO: 'epo',
        NAT: 'Esperanto'
    },
    {
        EST: 'est',
        NAT: 'Estonian'
    },
    {
        EUS: 'eus',
        NAT: 'Basque'
    },
    {
        FAS: 'fas',
        NAT: 'Persian'
    },
    {
        FIN: 'fin',
        NAT: 'Finnish'
    },
    {
        FRA: 'fra',
        NAT: 'French'
    },
    {
        FRK: 'frk',
        NAT: 'German-Fraktur'
    },
    {
        FRM: 'frm',
        NAT: 'French-Middle(ca.1400-1600)'
    },
    {
        GLE: 'gle',
        NAT: 'Irish'
    },
    {
        GLG: 'glg',
        NAT: 'Galician'
    },
    {
        GRC: 'grc',
        NAT: 'Greek-Ancient-(-1453)'
    },
    {
        GUJ: 'guj',
        NAT: 'Gujarati'
    },
    {
        HAT: 'hat',
        NAT: 'Haitian-Haitian'
    },
    {
        HEB: 'heb',
        NAT: 'Hebrew'
    },
    {
        HIN: 'hin',
        NAT: 'Hindi'
    },
    {
        HRV: 'hrv',
        NAT: 'Croatian'
    },
    {
        HUN: 'hun',
        NAT: 'Hungarian'
    },
    {
        IKU: 'iku',
        NAT: 'Inuktitut'
    },
    {
        IND: 'ind',
        NAT: 'Indonesian'
    },
    {
        ISL: 'isl',
        NAT: 'Icelandic'
    },
    {
        ITA: 'ita',
        NAT: 'Italian'
    },
    {
        ITA_OLD: 'ita_old',
        NAT: 'Italian-Old'
    },
    {
        JAV: 'jav',
        NAT: 'Javanese'
    },
    {
        JPN: 'jpn',
        NAT: 'Japanese'
    },
    {
        KAN: 'kan',
        NAT: 'Kannada'
    },
    {
        KAT: 'kat',
        NAT: 'Georgian'
    },
    {
        KAT_OLD: 'kat_old',
        NAT: 'Georgian-Old'
    },
    {
        KAZ: 'kaz',
        NAT: 'Kazakh'
    },
    {
        KHM: 'khm',
        NAT: 'Central'
    },
    {
        KIR: 'kir',
        NAT: 'Kirghiz-Kyrgyz'
    },
    {
        KOR: 'kor',
        NAT: 'Korean'
    },
    {
        KUR: 'kur',
        NAT: 'Kurdish'
    },
    {
        LAO: 'lao',
        NAT: 'Lao'
    },
    {
        LAT: 'lat',
        NAT: 'Latin'
    },
    {
        LAV: 'lav',
        NAT: 'Latvian'
    },
    {
        LIT: 'lit',
        NAT: 'Lithuanian'
    },
    {
        MAL: 'mal',
        NAT: 'Malayalam'
    },
    {
        MAR: 'mar',
        NAT: 'Marathi'
    },
    {
        MKD: 'mkd',
        NAT: 'Macedonian'
    },
    {
        MLT: 'mlt',
        NAT: 'Maltese'
    },
    {
        MSA: 'msa',
        NAT: 'Malay'
    },
    {
        MYA: 'mya',
        NAT: 'Burmese'
    },
    {
        NEP: 'nep',
        NAT: 'Nepali'
    },
    {
        NLD: 'nld',
        NAT: 'Dutch-Flemish'
    },
    {
        NOR: 'nor',
        NAT: 'Norwegian'
    },
    {
        ORI: 'ori',
        NAT: 'Oriya'
    },
    {
        PAN: 'pan',
        NAT: 'Panjabi-Punjabi'
    },
    {
        POL: 'pol',
        NAT: 'Polish'
    },
    {
        POR: 'por',
        NAT: 'Portuguese'
    },
    {
        PUS: 'pus',
        NAT: 'Pushto-Pashto'
    },
    {
        RON: 'ron',
        NAT: 'Romanian-Moldavian-Moldovan'
    },
    {
        RUS: 'rus',
        NAT: 'Russian'
    },
    {
        SAN: 'san',
        NAT: 'Sanskrit'
    },
    {
        SIN: 'sin',
        NAT: 'Sinhala-Sinhalese'
    },
    {
        SLK: 'slk',
        NAT: 'Slovak'
    },
    {
        SLV: 'slv',
        NAT: 'Slovenian'
    },
    {
        SPA: 'spa',
        NAT: 'Spanish-Castilian'
    },
    {
        SPA_OLD: 'spa_old',
        NAT: 'Spanish-Castilian-Old'
    },
    {
        SQI: 'sqi',
        NAT: 'Albanian'
    },
    {
        SRP: 'srp',
        NAT: 'Serbian'
    },
    {
        SRP_LATN: 'srp_latn',
        NAT: 'Serbian-Latin'
    },
    {
        SWA: 'swa',
        NAT: 'Swahili'
    },
    {
        SWE: 'swe',
        NAT: 'Swedish'
    },
    {
        SYR: 'syr',
        NAT: 'Syriac'
    },
    {
        TAM: 'tam',
        NAT: 'Tamil'
    },
    {
        TEL: 'tel',
        NAT: 'Telugu'
    },
    {
        TGK: 'tgk',
        NAT: 'Tajik'
    },
    {
        TGL: 'tgl',
        NAT: 'Tagalog'
    },
    {
        THA: 'tha',
        NAT: 'Thai'
    },
    {
        TIR: 'tir',
        NAT: 'Tigrinya'
    },
    {
        TUR: 'tur',
        NAT: 'Turkish'
    },
    {
        UIG: 'uig',
        NAT: 'Uighur-Uyghur'
    },
    {
        UKR: 'ukr',
        NAT: 'Ukrainian'
    },
    {
        URD: 'urd',
        NAT: 'Urdu'
    },
    {
        UZB: 'uzb',
        NAT: 'Uzbek'
    },
    {
        UZB_CYRL: 'uzb_cyrl',
        NAT: 'Uzbek-Cyrillic'
    },
    {
        VIE: 'vie',
        NAT: 'Vietnamese'
    },
    {
        YID: 'yid',
        NAT: 'Yiddish'
    },
];


</script>
<style scoped>

</style>