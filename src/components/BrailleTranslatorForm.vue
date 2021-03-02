<template>
    <div class="container">
        <b-row class="m-3">
            <b-col class="col-sm-12">
                <h2>Braille Translator</h2>
                <div class="row mt-5">
                    <div class="col-sm-6">
                        <select class="custom-select" name="valueFrom" id="valueFrom">
                            <option selected>Select an option</option>
                            <option :value="1">Spanish</option>
                            <option :value="2">Braille</option>
                        </select>
                    </div>
                    <div class="col-sm-6">
                        <select class="custom-select" name="valueResult" id="valueResult">
                            <option selected>Select an option</option>
                            <option :value="1">Spanish</option>
                            <option :value="2">Braille</option>
                        </select>
                    </div>
                </div>
            </b-col>
        </b-row>  
        <b-row class="m-3 mt-4">
            <b-col class="col-md-12">
                <div>
                    <b-form-group class="form-horizontal"
                        label-for="input-formatter">
                        <b-form-textarea
                            id="input-formatter"
                            rows="3"
                            no-resize
                            required
                            v-model="valueResult"
                            placeholder="Enter text"
                            :formatter="formatter">
                        </b-form-textarea>
                         <b-form-input
                            class="text-center text-dark input-size mt-3"
                            v-model="valueResult"
                            type="text"
                            readonly
                            style="font-size: 2rem">
                        {{ valueResult }}
                        </b-form-input>
                    </b-form-group >        
                </div>
            </b-col>
        </b-row>
    </div>
    
</template>

<script>
import br from 'braille'


let code = br.toBraille('ANOTHER WORD'); //  ⠑⠭⠁⠍⠏⠇⠑⠀⠞⠑⠭⠞⠀⠂⠆⠒
let text = br.toText('⠁⠝⠕⠞⠓⠑⠗⠀⠺⠕⠗⠙'); //  ANOTHER WORD
console.log(code + ' ' + text)



export default {
    name: 'BrailleTranslatorForm',
    data() {
      return {
          valueFrom: '',
          valueResult: ''
      }
    },
    methods: {
      formatter(value) {
        return value.toLowerCase()
      }
    }
};
</script>
<style scoped>

</style>