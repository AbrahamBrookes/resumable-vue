<template>
    <div class="uploading-video">
        <span :class="{
            'status-canceled': status == 'canceled'
        }"> {{ file.fileName }} </span>
        <small v-if="status == 'success'">done!</small>
        <small v-else-if="status == 'retrying'">fault, retrying...</small>
        <small v-else-if="status == 'error'">error! cannot upload file</small>
        <small v-else-if="status == 'canceled'">cancelled</small>
        <small v-else>uploading {{ uploadedAmount }}%</small>

        <span v-if="isUploading">
            <button @click="isPaused ? resume() : pause()">{{ isPaused ? "resume" : "pause" }}</button>
            <button @click="cancel()">cancel</button>
        </span>

    </div>
</template>

<script>
export default {
    props: [
        'file',
        'status',
        'progress',
    ],
    data(){
        return {
            isPaused: false // we upload straight away by default
        }
    },
    computed: {
        isUploading(){
            return (this.status !=='success' && this.status !== 'canceled')
        },
        uploadedAmount(){
            return (this.progress * 100).toFixed(2)
        },
    },
    methods: {
        upload(){
            this.file.resumableObj.upload()
            this.isPaused = false
        },
        pause(){
            this.file.pause()
            this.isPaused = true
        },
        resume(){
            this.pause() // not sure why, but we have to call pause again before upload will resume
            this.upload()
        },
        cancel(){
            this.$emit('cancel', this.file)
        }
    }
}
</script>

<style lang="scss">
.uploading-video {
    .status-canceled {
        text-decoration: line-through;
    }
}
</style>
