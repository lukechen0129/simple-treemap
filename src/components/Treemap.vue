<template>
	
	<div class="container">
		<div v-for="(node, index) of finalTree" :key="`${node.name}-${index}`" class="item" 
			:class="[{}, node.value >= 0 ? 'pos' : 'neg']" 
			:style="`width: ${node.weight / estimatedWidth * 100}%; `"
		>
			<span> {{node.name}} </span>
			<span> {{(node.value * 100)}}% </span>
		</div>
		<div class="overlay" v-if="parentIsLoad">
			<img src="../assets/spinner.png" alt=""/>
		</div>
	</div>
</template>
<script>
	export default {
		name: 'Treemap',
		props: {
			row: {
				required: true,
				type: Number,
			},
			dataList: {
				required: true,
				type: Array,
				default: ()=> [],
			},
			isLoad: {
				required: true,
			}
		},
		data() {
			return {
				tempResult: [],
				finalTree: [],
				forcedWidth: 0,
				isLoading: true,
			}

		},
		computed: {
			estimatedWidth() {
				if (this.dataList == null) return [];
				// console.log(this.dataList);
				var total = 0;
				this.dataList.forEach( x => {
					total += x.weight;	
				});
				let estimated = Math.ceil(total/this.row);
				// console.log(`estimated: ${estimated}`);
				// console.log(estimated > this.sortedNodeList[0].weight ? estimated: this.sortedNodeList[0].weight);
				return estimated > this.sortedNodeList[0].weight ? estimated + this.forcedWidth: this.sortedNodeList[0].weight + this.forcedWidth; // avoid estimated width smaller than largest weight in nodeList
			},
			sortedNodeList() {
				// eslint-disable-next-line vue/no-side-effects-in-computed-properties
				let sorted = this.dataList.sort((a,b) => a.weight < b.weight && 1 || -1 );
				sorted.forEach((x, idx) => { sorted[idx].id = idx; });
				return sorted;
			},
			parentIsLoad:{
				get() {
					return this.isLoad;
				},
				set(val) {
					this.$emit('update:isLoad', val);
				}
			}
		},
		mounted() {
			this.toTreeReady();
		},
		watch: {
			// eslint-disable-next-line no-unused-vars
			row(newVal, oldVal) {
				console.log(`row changed: from ${oldVal} to ${newVal}.`);
				this.finalTree = [];
				this.tempResult = [];
				this.forcedWidth = 0;
				this.toTreeReady();
			},
			// eslint-disable-next-line no-unused-vars
			dataList(newVal, oldVal){
				console.log(`data changed.${newVal.length} : ${oldVal.length}`);
				this.finalTree = [];
				this.tempResult = [];
				this.forcedWidth = 0;
				this.toTreeReady();
			}
		},
		methods: {
			toTreeReady(){
				let tempList = [...this.sortedNodeList];
				tempList = this.reIndex(tempList);
				// this.printList(tempList);
				let tempWidth = this.estimatedWidth;
				let i = 0;
				while( tempList.length > 0) {
					tempWidth = this.estimatedWidth;
					let combi = null;
					while (combi == null) {
						combi =  this.subsetSum(tempList, tempWidth);
						if (combi != null){
							combi.forEach((x, index)=> {
								this.finalTree.push(x);
								tempList.splice(x.id - index, 1);
							})
							// this.printList(this.finalTree);
							i++;
							this.tempResult = [];
							tempList = this.reIndex(tempList);
						} else {
							tempWidth--;
						}
					}
					
				}
				if (i >= this.row+ 1) {
					console.log("too many rows.");
					this.forcedWidth++;
					this.finalTree = [];
					this.tempResult = [];
					this.toTreeReady();
					
				} 
				this.parentIsLoad = false;
				
			},
			subsetSum(arr, target, partial=[]) {
				let  n, remaining;
				var s = 0;
				if (partial.length > 0) {
					partial.forEach((x) => { s += x.weight; });
				} else {
					s = 0;
				}
				// console.log(`sum = ${s}`);
				if ( s == target ) {
					// return partial;
					this.tempResult.push(partial);
					return partial;
				}
				if (s >= target) {
					return null;
				}
				for(let i=0 ;i < arr.length;i++) {
					n = arr[i];
					remaining = arr.slice(i+1);
					let tempResult = this.subsetSum(remaining, target, partial.concat([n]));
					if (tempResult != null) {
						return tempResult;
					}
				}

			},
			reIndex(arr) {
				arr.forEach((x, idx) => { arr[idx].id = idx; });
				return arr;
			},
			printList(arr) {
				console.log(JSON.stringify(arr));
			}
		}
	}
</script>

<style>
	.container {
		min-width: 40vh;
		height: 1200px;
		display: flex;
		flex-direction: row;
		align-items: stretch;
		justify-content: flex-start;
		flex-wrap: wrap;
		background: #bbb;
		position: relative;
	}
	.item { 
		background: black; 
		border: 1px #ddd solid;
		box-sizing: border-box;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}
	.pos { background: #42e88d; }
	.neg { background: #fb7c7c; }
	.overlay {
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background: #f1f2f3;
		opacity: 0.6;
		display: flex;
		align-items: center;
		justify-content: center;
	}
	img {
		width: 120px;
		height: 120px;
		animation: spin 1s linear infinite forwards;
	}
	@keyframes spin {
		0% { transform: rotate(0); }
		50% { trasform: rotate(180deg); }
		100% { transform: rotate(360deg); }
	}
	
</style>