<template>
	<div class="upload-videos">
        <div class="video-dropzone" ref="videodropzone">
            <div class="dropzone-display">
                <div class="p-5">
                    <img src="/cloud-computing.svg" />
					<small>Icon by <a href="https://www.flaticon.com/authors/smartline" title="Smartline">Smartline</a> from <a href="https://www.flaticon.com/" title="Flaticon">www.flaticon.com</a></small>
                    <h3>Drop or click here to upload your content</h3>
                    <p>Multiple files supported</p>
                </div>
            </div>
        </div>

        <uploading-video
            v-for="(file, index) in files"
            v-bind:key="file.file.uniqueIdentifier + index"
            :file="file.file"
            :status="file.status"
            :progress="file.progress"
            @cancel="cancelFile"
        />
	</div>
</template>

<script>
import Resumable from 'resumablejs'

import UploadingVideo from './UploadingVideo'

export default {
    components: {
        UploadingVideo
    },
    data(){
        return {
            files: [], // our local files array, we will pack in extra data to force reactivity
            r: false
        }
    },
    methods: {
        // finds the file in the local files array
        findFile(file){
            return this.files.find(item => item.file.uniqueIdentifier === file.uniqueIdentifier && item.status !== 'canceled') ?? {}
        },
        // cancel an individual file
        cancelFile(file){
            this.findFile(file).status = 'canceled'
            file.cancel()
        }
    },
	mounted(){
        // init resumablejs on mount
		this.r = new Resumable({
			target:'/api/upload-advanced',
			query:{upload_token:'my_token'},
            maxChunkRetries: 1,
            testChunks: false,
		});

		// Resumable.js isn't supported, fall back on a different method
		if(!this.r.support) return alert('Your browser doesn\'t support chunked uploads. Get a better browser.');

        this.r.assignBrowse(this.$refs.videodropzone);
		this.r.assignDrop(this.$refs.videodropzone);

        // set up event listeners to feed into vues reactivity
        this.r.on('fileAdded', (file, event) => {
            file.hasUploaded = false
            console.log('this.files', this.files)
            // keep a list of files with some extra data that we can use as props
            this.files.push({
                file,
                status: 'uploading',
                progress: 0
            })
            this.r.upload()
        })
        this.r.on('fileSuccess', (file, event) => {
            this.findFile(file).status = 'success'
        })
        this.r.on('fileError', (file, event) => {
            this.findFile(file).status = 'error'
        })
        this.r.on('fileRetry', (file, event) => {
            this.findFile(file).status = 'retrying'
        })
        this.r.on('fileProgress', (file) => {
            // console.log('fileProgress', progress)
            var localFile = this.findFile(file)
            // if we are doing multiple chunks we may get a lower progress number if one chunk response comes back early
            var progress = file.progress()
            if( progress > localFile.progress)
                localFile.progress = progress

        })
	}
}
</script>

<style lang="scss">

.p-5 {
	padding: 3rem;
}

.upload-videos {
	display: flex;
	flex-direction: column;
}

.video-dropzone {
    height: 340px;
    width: 340px;
    padding: 16px;
	display: flex;
	align-self: center;
	margin-bottom: 1.2rem;
    cursor: pointer;

    * {
        pointer-events: none;
    }

    .dropzone-display {
        height: 100%;
        width: 100%;
		display: flex;
		align-items: center;
		justify-content: center;
		flex-direction: column	;
        border: 4px dashed #222;
        border-radius: 32px;

        img {
            width: 64px;
        }

		small {
			font-size: 0.65em;
			display: block;
		}
    }
}
</style>
