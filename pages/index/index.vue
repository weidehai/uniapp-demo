<template>
	<view class="content">
		<view v-for="col,index of picColumn.data" :class="['colContainer-'+index,'colContainer']" :style="'width:'+columnWidth+'px'">
			<image v-for="item of col" :src="item.url" mode="center" :style="'height:'+item.height+'px;'+'width:'+columnWidth+'px'"></image>
		</view>
		
	</view>
</template>

<script>
	import manifest from "./manifest.json"
	export default {
		data() {
			return {
				// #ifdef H5
				picColumnNum:4,
				// #endif
				// #ifndef H5
				picColumnNum:2,
				// #endif
				picColumn:{info:[],data:[]},
				columnWidth:0,
				picList:manifest.picList,
				picStyle:""
			}
		},
		onLoad() {
			uni.getSystemInfo({
				success:(res)=> {
					this.initStyle(res.windowWidth)
					this.generatePicColumn()
				}
			})
		},
		methods: {
			calculateColumnWidth(windowWidth){
				// #ifdef H5
				this.columnWidth = Math.floor((windowWidth-17) / this.picColumnNum)
				// #endif
				// #ifndef H5
				this.columnWidth = Math.floor((windowWidth) / this.picColumnNum)
				// #endif
				return this.columnWidth
			},
			generatePicColumn(){
				let candidateCol  = -1
				let minHeight = Number.MAX_SAFE_INTEGER
				if(this.picColumn.data.length==0){
					let initColumn = manifest.picList.splice(0,this.picColumnNum)
					initColumn.forEach((item,index)=>{
						this.picColumn.data.push([item])
						this.picColumn.info[index] = parseInt(item.height)
						if(item.height<minHeight){
							minHeight = item.height
							candidateCol = index
						}
					})
				}
				manifest.picList.forEach((item)=>{
					this.picColumn.data[candidateCol].push(item)
					this.picColumn.info[candidateCol]+=parseInt(item.height) 
					candidateCol = this.findCandidateCol()
				})
				console.log(this.picColumn);
			},
			findCandidateCol(){
				let candidateCol  = -1
				let minHeight = Number.MAX_SAFE_INTEGER
				this.picColumn.info.forEach((height,index)=>{
					if(height<minHeight){
						minHeight = height
						candidateCol = index
					}
				})
				return candidateCol
			},
			initStyle(windowWidth){
				this.picStyle = `width:${this.calculateColumnWidth(windowWidth)}px;`
			}
			
		}
	}
</script>

<style lang="scss">
	.content {
		display: flex;
		flex-wrap: wrap;
		.colContainer{
			display: flex;
			flex-shrink: 0;
			flex-direction: column;
		}
	}
</style>
