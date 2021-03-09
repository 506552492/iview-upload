<template>
    <div style="height: 100%;">
        <img :src="src" style="width:100%;max-width:100%;height: 100%;" @click="handleView">
    </div>
</template>

<script>
	import Viewer from 'viewerjs';
	export default {
		props: {
			value: {
				type: Boolean,
				default: false
			},
			src: String
		},
		components: {
			//组件
		},
		data(){
			//数据
			return {
				msg: 'Hello World!'
			};
		},
		mounted(){
			//初始化
			this.$nextTick(() => {
				this.viewer = {};
				this.viewer = new Viewer(this.$el.children[0], {
					navbar: false,
					loop: false,
					fullscreen: false,
					hide: () => {
						this.viewer.destroy();
					}
				});
			})
		},
		computed: {
			//实时计算
		},
		watch: {
			//实时监听
		},
		methods: {
			//方法
			handleView(){

				this.$nextTick(() => {
					this.viewer = {};
					this.viewer = new Viewer(this.$el.children[0], {
						navbar: false,
						loop: false,
						fullscreen: false,
						hide: () => {
							this.viewer.destroy();
						}
					});
				});
			}
		}
	};
</script>
