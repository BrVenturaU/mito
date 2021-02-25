<template>
    <b-row class="bg-dark mx-auto shadow rounded-lg text-white py-2" style="max-width: 70%; margin-bottom: -4px;">
        <b-col cols="12">
            <h1>{{title}}</h1>
        </b-col>
    </b-row>
</template>
<script>
export default {
    name: 'Header',
    props:{
        title: String
    }
}
</script>
<style scoped>

</style>