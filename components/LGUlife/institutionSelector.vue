<template>
	<view class="topBar">
		<view v-for="(inst) in institutions" :key='inst.id' class="instItem" @tap="changeInst(inst.id)">{{inst.name}}</view>
	</view>
</template>

<script>
	export default {
		name: 'institutionSelector',
		emits: ['changeInst'],
		data() {
			return {
				institutions: [
					{id: 0, name: "全部"},
					{id: 1, name: "学生会"},
					{id: 2, name: "书院"},
					{id: 3, name: "学院"},
					{id: 4, name: "学生事务处（OSA）"},
					{id: 5, name: "社团"},
					{id: 6, name: "吃喝玩乐"},
					{id: 7, name: "新东方"},
					{id: 8, name: "HELPHUB"},
				]
			}
		},
		methods: {
			changeInst(x) {
				this.$emit('changeInst', x);
			}
		},
	}
</script>

<style>
	.instItem {
		
	}
</style>