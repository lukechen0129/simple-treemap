<template>
	<div>
		<div class="row-container">
			<span @click="changeRow(-1)"> − </span>
			<input v-model="parentValue" type="number" min="1" max="50"/>
			<span @click="changeRow(+1)"> + </span>
		</div>
		<div class="error-msg" v-show="showErr">Invalid input. Please input number of row between 1 to 50. <br/> Changing to closest valid number.</div>
	</div>
	
</template>

<script>
export default {
	name: "RowInput",
	props: {
		value: {
			require: true,
			type: Number,
		}
	},
	data() {
		return {
			showErr: false,
		}
	},
	computed: {
		parentValue: {
			get() {
				return this.value;
			},
			set(val) {
				if (!((parseInt(val) <= 0) || (parseInt(val) > 50))) {
					// console.log(`match! ${val}`);
					this.showErr = false;
					this.$emit('update:value', parseInt(val));
				}else if (parseInt(val) <= 0) {
					this.showErr = true;
					this.$emit('update:value', 1);
				}else {
					this.showErr = true;
					this.$emit('update:value', 50);
				}
				
			}
		}
	},
	methods: {
		changeRow(sign) {
			if (sign > 0 && this.parentValue <= 50) {
				this.parentValue++;
			} else if (this.parentValue >= 1) {
				this.parentValue--;
			}
		}
	}
}
</script>
<style scoped>
	.row-container {
		display: flex;
		align-items: center;
		justify-content: center;	
	}
	input {
		width: 60px;
		height: 40px;
		text-align: center;
		font-size: 16px;
		margin: 0 16px;
	}
	input::-webkit-outer-spin-button,
	input::-webkit-inner-spin-button {
		-webkit-appearance: none;
		margin: 0;
	}

	/* Firefox */
	input[type=number] {
		-moz-appearance: textfield;
	}
	span {
		border-radius: 50%;
		background: #00c797;
		color: #fff;
		display: flex;
		align-items: center;
		justify-content: center;
		width: 36px;
		height: 36px;
		font-size: 16px;
		font-weight: bold;
		cursor: pointer;
	}
	span:active {
		background: #039a76;
	}
	.error-msg {
		color: #f66;
	}
</style>