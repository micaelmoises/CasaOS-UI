<!--
  * @LastEditors: zhanghengxin ezreal.zhang@icewhale.org
  * @LastEditTime: 2023/4/24 上午11:20
  * @FilePath: /CasaOS-UI/src/components/filebrowser/uploader/components/drop.vue
  * @Description:
  *
  * Copyright (c) 2023 by IceWhale, All Rights Reserved.

  -->
<template>
	<div v-show="support" ref="drop" :class="dropClass" class="uploader-drop">
		<slot></slot>
	</div>
</template>

<script>
import {supportMixin, uploaderMixin} from '../common/mixins'

const COMPONENT_NAME = 'uploader-drop'

export default {
	name: COMPONENT_NAME,
	mixins: [uploaderMixin, supportMixin],
	data() {
		return {
			dropClass: ''
		}
	},
	methods: {
		onDragEnter() {
			this.dropClass = 'uploader-dragover'
		},
		onDragLeave() {
			this.dropClass = ''
		},
		onDrop() {
			this.dropClass = 'uploader-droped'
		}
	},
	mounted() {
		this.$nextTick(() => {
			const dropEle = this.$refs.drop
			const uploader = this.uploader.uploader
			uploader.assignDrop(dropEle)
			uploader.on('dragenter', this.onDragEnter)
			uploader.on('dragleave', this.onDragLeave)
			uploader.on('drop', this.onDrop)
		})
	},
	beforeDestroy() {
		const dropEle = this.$refs.drop
		const uploader = this.uploader.uploader
		uploader.off('dragenter', this.onDragEnter)
		uploader.off('dragleave', this.onDragLeave)
		uploader.off('drop', this.onDrop)
		uploader.unAssignDrop(dropEle)
	}
}
</script>

<style>
.uploader-drop {
	position: relative;
	padding: 10px;
	overflow: hidden;
	border: 1px dashed #ccc;
	background-color: #f5f5f5;
}

.uploader-dragover {
	border-color: #999;
	background-color: #f7f7f7;
}
</style>
