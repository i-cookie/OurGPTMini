<template>
	<institutionSelector @changeInst="changeInst"/>
</template>

<script>
	import institutionSelector from '../../components/LGUlife/institutionSelector.vue'
	export default {
		components: {
			institutionSelector
		},
		data() {
			return {
				instId: 0,
				deptId: 0,
				depts: [
					{
						id: 0,
						name: '全部'
					},
					{
						id: 1,
						name:'学术部'
					}
				]
			}
		},
		methods: {
			changeInst(e) {
				this.instId = e;
				console.log(this.instId);
			}
		},
	}
</script>

<style>
</style>