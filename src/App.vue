<template>
  <div id="app">
		<div class="w-full">
			<h2>Treemap Simulator</h2>
		</div>
		<div class="w-full sm:w-1/2 border-light">
			<div class="w-full">
				<h3>Input Area</h3>
			</div>
			<div class="input-area">
				<h4>Number of Rows:</h4>
				<RowInput :value.sync="row" />
				<h4>Data:</h4>
				<textarea v-model="dataString" type="text"/>
				<small>Note: Array more than 50 elements will be trimmed.</small>
				<div class="button" @click="addRandomData">Add Random data</div>
			</div>
			
		</div>
		<div class="w-full sm:w-1/2 border-light" >
			<div class="w-full">
				<h3>Result </h3>
			</div>
			<Treemap :dataList="convertedData	" :row="row" :isLoad.sync="isLoad"/>
		</div>
    
  </div>
</template>

<script>
import Treemap from './components/Treemap'
import RowInput from './components/RowInput'

export default {
  name: 'App',
  components: {
    Treemap, RowInput,
  },
	data() {
		return {
			row: 1,
			dataString: "[{ name: 'A', weight: 3	, value: -0.02 }]",
			dummyData: [
					{ name: 'A', weight: 3	, value: -0.02 }, 
					{ name: 'B', weight: 5, value: 0.05 }, 
					{ name: 'C', weight: 14, value: 0.015 }, 
					{ name: 'D', weight: 10, value: -0.01 }, 
					{ name: 'E', weight: 3, value: 0.01 },
					{ name: 'F', weight: 8, value: 0.015 }, 
					{ name: 'G', weight: 4, value: -0.01 }, 
					{ name: 'H', weight: 3, value: 0.01 },
				],
			dataList: [{ name: 'A', weight: 3	, value: -0.02 }],
			isLoad: true,
		}
	},
	computed: {
		convertedData() {
			// console.log(this.dataString.replace(/[\n\t ]/i,''));
			let arr = eval(this.dataString) || [];
			if (arr.length >= 1){
				return arr.slice(0,50);
			}
			return [];
		}
	},
	methods:{
		addRandomData(){
			this.dummyData.push({name: String.fromCharCode(97 + Math.floor(Math.random() * 26)), weight: Math.floor(Math.random() * 50), value: -1 * Math.floor(Math.random() * 2)*(Math.random() * 1 ).toFixed(2)})
			this.dataString = JSON.stringify(this.dummyData);
		}
	},
	watch: {
		row() {
			this.isLoad = true;
		},
		dataString() {
			console.log("data change");
			this.isLoad = true;
		}
	}
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
	height: auto;
  color: #2c3e50;
	display: flex;
	align-items: stretch;
	justify-content: center;
	flex-wrap: wrap;
	max-width: 1240px;
	margin: 0 auto;
  /* margin-top: 60px; */
}
input, textarea {
	background: rgba(255,255,255,0.6);
	border: 1px #ddd solid;
	border-radius: 10px;
	outline: none;
}
textarea {
	font-family: 'Courier New', monospace;
	padding: 12px;
	/* resize: none; */
}
.w-full {
	width: 100%;
}
.w-1\/2 {
	width: 50%;
}
@media only screen and (min-width: 960px)  {
	.sm\:w-1\/2 {
		width: 50%;
	}
}

.border-light {
	border: 1px #ddd solid;
	box-sizing: border-box;
}
.input-area {
	height: 100%;
}
textarea {
	width: 80%;
	height: 400px;
}
small {
	display: block;
	margin-top: 8px;
}
.button {
	padding: 12px 20px;
	border-radius: 40px;
	background: #00c797;
	color: white;
	font-weight: 600;
	width: fit-content;
	margin: 16px auto 36px;
	cursor: pointer;
}
.button:active {
	background: #039a76;
}
@media only screen and (min-width: 960px)  {
	textarea {
		width: 70%;
		height: 400px;
		margin-bottom: 0;
	}
}


</style>
