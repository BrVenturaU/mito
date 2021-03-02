<template>
    <div class="container">
        <b-row class="m-3">
            <b-col class="col-sm-12">
                <h2>Braille Translator</h2>
                <div class="row mt-5">
                    <div class="col-sm-6">
                        <select class="custom-select" name="selectedValueFrom" id="selectedValueFrom" @change="onChangeSelectedValue">
                            <option v-for="option of options" :key="option.id" :value="option.id">{{option.name}}</option>
                        </select>
                    </div>
                    <div class="col-sm-6">
                        <select class="custom-select" name="selectedValueResult" id="selectedValueResult" @change="onChangeSelectedValue">
                            <option v-for="option of options" :key="option.id" :value="option.id">{{option.name}}</option>
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
                            v-model="valueFrom"
                            @keyup="onTypingText"
                            placeholder="Enter text"
                            :class="{'braille':selectedValueFrom == 2}">
                        </b-form-textarea>
                         <b-form-input
                            :class="['text-center', 'text-dark', 'input-size mt-3', selectedValueResult == 2 ? 'braille' : '']"
                            :value="valueResult"
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

export default {
    name: 'BrailleTranslatorForm',
    data() {
      return {
          valueFrom: '',
          valueResult: '',
          selectedValueFrom: 0,
          selectedValueResult: 0,
          fontClass: '',
          options: [{id:0, name:'Select an option'}, {id:1, name:'Text'}, {id:2, name:'Braille'}]
      }
    },
    methods: {
        capitalizeText(text){    
            this.valueResult = text.charAt(0).toUpperCase() + text.slice(1).toLowerCase();
        },
        onChangeSelectedValue(event){
            let value = event.target.value
            if(event.target.id == "selectedValueFrom")
                this.selectedValueFrom = parseInt(value)
            else
                this.selectedValueResult = parseInt(value)

            this.onTypingText()
        },
        onTypingText(){
            let vm = this
            
            if(vm.selectedValueFrom != 0 && vm.selectedValueResult != 0){
                switch (vm.selectedValueFrom) {
                    case vm.selectedValueResult:
                        vm.valueResult = vm.valueFrom
                        break;

                    case 1:{
                        vm.valueResult = br.toBraille(vm.valueFrom);
                        console.log(vm.valueResult);
                        break;
                    }
                    case 2:{
                        let braille = br.toBraille(vm.valueFrom);
                        vm.capitalizeText(br.toText(braille));
                        console.log(vm.valueResult)
                        break;
                    }
                }
            }

        }
    }
};
</script>
<style scoped>
@font-face {
  font-family: 'Braille6';
  src: url('../assets/Braille6-ANSI.ttf'); /* IE9 Compat Modes */
  src: url('../assets/Braille6-ANSI.ttf') format('embedded-opentype'), /* IE6-IE8 */
       url('../assets/Braille6-ANSI.ttf') format('woff2'), /* Super Modern Browsers */
       url('../assets/Braille6-ANSI.ttf') format('woff'), /* Pretty Modern Browsers */
       url('../assets/Braille6-ANSI.ttf')  format('truetype'), /* Safari, Android, iOS */
       url('../assets/Braille6-ANSI.ttf') format('svg'); /* Legacy iOS */
}
.braille{
    font-family: 'Braille6', Fallback, sans-serif;
}
</style>