<template>
	<view>
		{{params}}
	</view>
</template>

<script>
	export default {
		data() {
			return {
				params:''
			}
		},
		onLoad(options) {
			this.params = options.params;
		},
		methods: {
			
		}
	}
</script>

<style>

</style>
