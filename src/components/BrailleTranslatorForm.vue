<template>
    <div class="container">
        <b-row class="m-3">
            <b-col class="col-sm-12">
                <h2>Braille Translator</h2>
                <div class="row mt-5">
                    <div class="col-sm-6">
                        <select class="custom-select">
                            <option selected>Select an option</option>
                            <option value="1">Español</option>
                            <option value="2">Braille</option>
                        </select>
                    </div>
                    <div class="col-sm-6">
                        <select class="custom-select">
                            <option selected>Select an option</option>
                            <option value="1">Español</option>
                            <option value="2">Braille</option>
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
                            rows="5"
                            no-resize
                            required
                            v-model="text1"
                            placeholder="Enter text"
                            :formatter="formatter">
                        </b-form-textarea>
                    </b-form-group>
                    <b-form-textarea 
                        rows="5"
                        no-resize
                        required>
                        {{ text1 }}
                    </b-form-textarea>

                </div>
                
            </b-col>
        </b-row>
        <!--
        <b-row class="m-3">
            <b-col class="col-sm-12">
                <div class="row bt">
                    <div class="col-sm-12">
                        <button type="button" class="btn btn-primary">Translate</button>
                    </div>
                </div> 
            </b-col>
        </b-row>-->
            
    </div>
</template>


<script>
export default {
    name: 'BrailleTranslatorForm',
    data() {
      return {
        text1: '',
        text2: ''
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