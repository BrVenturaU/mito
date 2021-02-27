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
            copied: false
        }
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
            let getText = async () => {
                await worker.load();
                await worker.loadLanguage('eng');
                await worker.initialize('eng', OEM.LSTM_ONLY);
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
</script>
<style scoped>

</style>