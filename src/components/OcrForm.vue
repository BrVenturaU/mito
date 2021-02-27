<template>
    <div class="container">
        <b-row>
            <b-col cols="12">
                <h2 class="mb-4">Image to Text (OCR)</h2>
                <b-form enctype="multipart/form-data">
                    <b-form-file 
                        accept="image/*"
                        v-model="file"
                        :state="Boolean(file)"
                        placeholder="Choose a file or drop it here..."
                        drop-placeholder="Drop file here..."
                        required></b-form-file>
                        <div class="my-3">Selected file: {{ file ? file.name : '' }}</div>
                    <b-button class="mr-2" variant="primary">Scan</b-button>
                    <b-button type="reset" variant="danger">Reset</b-button>
                </b-form>
            </b-col>
        </b-row>
    </div>
</template>
<script>

export default {
    name: 'OcrForm',
    data() {
        return {
            file: null
        }
    },
    methods: {
        
    },
}
</script>
<style scoped>

</style>