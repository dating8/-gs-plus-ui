<template>
	<div class="mod-config">
		<el-form label-width="100px" :model="searchForm" @keyup.enter.native="dataFormSubmit()">
			
			<el-form-item label="当前时间：">
				<span style="margin-left: 5px;">{{searchForm.creatTime}}</span>
			</el-form-item>
			<el-form-item label="当前金价：">
				<el-row :gutter="10">
				  <el-col :span="12"><el-input v-model="searchForm.goldPrice" clearable></el-input></el-col>
					<el-col :span="2"><span>元/g</span></el-col>
				</el-row>
			</el-form-item>
			<el-form-item label="金价设置：">
				<el-row :gutter="10">
				  <el-col :span="12"><el-input v-model="searchForm.price" clearable></el-input></el-col>
					<el-col :span="2"><span>元/g</span></el-col>
				</el-row>
			</el-form-item>
			<el-form-item>
			  <el-button @click="dataFormSubmit()">提交</el-button>
			</el-form-item>
		</el-form>
	</div>
</template>

<script>
	import {nowDate} from "@/utils/util.js"
	export default {
	  data () {
	    return {
	      searchForm: {
					creatTime: nowDate(),
	        goldPrice: '',
					price:''
	      }
	    }
	  },
		methods:{
			dataFormSubmit(){
				
			}
		}
	}
</script>

<style>
</style>
