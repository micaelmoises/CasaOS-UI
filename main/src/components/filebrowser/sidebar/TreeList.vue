<!--
  * @LastEditors: zhanghengxin ezreal.zhang@icewhale.org
  * @LastEditTime: 2023/4/24 上午11:20
  * @FilePath: /CasaOS-UI/src/components/filebrowser/sidebar/TreeList.vue
  * @Description:
  *
  * Copyright (c) 2023 by IceWhale, All Rights Reserved.

  -->

<!--
 * @Author: JerryK
 * @Date: 2022-03-03 13:10:35
 * @LastEditors: Jerryk jerry@icewhale.org
 * @LastEditTime: 2023-02-06 17:54:39
 * @Description:
 * @FilePath: /CasaOS-UI/src/components/filebrowser/sidebar/TreeList.vue
-->
<template>
	<ul>
		<!-- Root List Start -->
		<tree-list-item v-for="item in rootDataList" :key="item.path" :isActive="isActive" :item="item"
						iconColor="casa-color-blue"></tree-list-item>
		<!-- Root List End -->

		<!-- Data List Start -->
		<tree-list-item v-for="item in dataList" :key="item.path" :isActive="isActive"
						:isShare="checkSharevisibility(item)"
						:item="item" iconColor="casa-color-blue"></tree-list-item>
		<!-- Data List End -->

	</ul>
</template>

<script>
import {mixin} from '@/mixins/mixin';
import events  from '@/events/events';
import has     from 'lodash/has'

import TreeListItem from './TreeListItem.vue';

export default {
	mixins: [mixin],
	inject: ['filePanel'],
	components: {
		TreeListItem,
	},
	props: {
		path: {
			type: String,
			default: ""
		},
		autoLoad: {
			type: Boolean,
			default: false
		},
		isActive: {
			type: Boolean,
			default: true
		},
	},
	data() {
		return {
			rootDataList: [
				{
					name: 'Root',
					icon: 'folder-root',
					pack: 'casa',
					path: '/',
					visible: true,
					selected: true,
					extensions: null
				},
			],

			initFolders: [
				{
					name: 'DATA',
					icon: 'folder-data',
					pack: 'casa',
					path: '/DATA',
					visible: true,
					selected: true,
					extensions: null
				},
				{
					name: 'Documents',
					icon: 'folder-documents',
					pack: 'casa',
					path: '/DATA/Documents',
					visible: true,
					selected: true,
					extensions: null
				},
				{
					name: 'Downloads',
					icon: 'folder-downloads',
					pack: 'casa',
					path: '/DATA/Downloads',
					visible: true,
					selected: true,
					extensions: null
				},
				{
					name: 'Gallery',
					icon: 'folder-gallery',
					pack: 'casa',
					path: '/DATA/Gallery',
					visible: true,
					selected: true,
					extensions: null
				},
				{
					name: 'Media',
					icon: 'folder-media',
					pack: 'casa',
					path: '/DATA/Media',
					visible: true,
					selected: true,
					extensions: null
				},

			],
			dataList: [],
			shortcutList: [],
		}
	},
	async created() {
		// Get the shortcut detail for the first time and save it to store
		try {
			await this.$store.dispatch('GET_SHORTCUT_DATA')
		} catch (e) {
			console.log(e);
		}
		this.getNewList()
	},

	mounted() {
		this.$EventBus.$on(events.RELOAD_FILE_LIST, this.getNewList);

		this.shortcutList = this.$store.state.shortcutData
		this.dataList = [...this.initFolders, ...this.shortcutList]

	},
	methods: {
		async getNewList() {
			const newList = await this.$api.folder.getList(this.rootDataList[0].path)
			const dataList = await this.$api.folder.getList(this.initFolders[0].path)
			this.shortcutList = this.$store.state.shortcutData
			this.dataList = [...this.initFolders, ...this.shortcutList]
			let contactList = [];
			contactList.push(...newList.data.data.content, ...dataList.data.data.content, ...this.shortcutList);
			this.dataList.forEach(dir => {
				dir.visible = contactList.some(item => item.path == dir.path && item.is_dir);
				const isInArray = contactList.find(item => item.path == dir.path && item.is_dir)
				dir.extensions = isInArray ? isInArray.extensions : null;
			})
		},

		checkSharevisibility(item) {
			const extensions = item.extensions
			if (extensions === null) {
				return false
			} else {
				if (has(extensions, 'share')) {
					return extensions.share.shared === "true"
				} else {
					return false
				}
			}
		},

	},
}
</script>

