<template>
	<div id="app">
		
		<b-container>
			
			<b-row class="justify-content-md-center">
				<b-col lg="7" cols="12">
					<h3>Информация</h3>
					<div class="info-panel">
						<b-row>
							<b-col cols="3">
								<div class="info-block">
									<div class="info-block__head">Всего блоков:</div>
									<div class="info-block__body">{{ this.list.length }}</div>
								</div>
							</b-col>
							<b-col cols="9">
								<div class="info-block">
									<div class="info-block__head">Выделенных блоков:</div>
									<div class="info-block__body">
										<span>{{ countSelected }}</span>
										(из них <span class="red">красных: {{ redSelected }},</span>
										<span class="green">зеленых: {{ greenSelected }}</span>)
									</div>
								</div>
							</b-col>
						</b-row>
					</div>
					
					<h3>Список блоков</h3>
					<div class="items-list" v-if="list">
						<transition-group name="fade" mode="in-out">
							<text-block v-for="(item, i) in list"
							            :key="`item_${i}`"
							            :complex="item.complex"
							            :index="i">
								{{ item.text }}
							</text-block>
						</transition-group>
					</div>
					
					<div class="add-item">
						<b-button variant="primary" @click="addItem">Добавить блок</b-button>
					</div>
				</b-col>
			</b-row>
		
		</b-container>
	</div>
</template>

<script>
	import TextBlock  from './components/TextBlock'
	import loremIpsum from 'lorem-ipsum'
	
	export default {
		name: 'app',
		components: {
			TextBlock
		},
		data () {
			return {
				list: null,
				countSelected: 0,
				redSelected: 0,
				greenSelected: 0
			}
		},
		
		created () {
			this.generateList()
			
			this.$root.$on('removeTextBlock', (index) => {
				this.list.splice(index, 1)
			})
			
			this.$root.$on('selectBlock', (item) => {
				console.log(item)
				this.countSelected += item.selected ? 1 : -1
				
				if (item.selected && item.complex) {
					this.redSelected += item.color === 'red' ? 1 : 0
					this.greenSelected += item.color === 'green' ? 1 : 0
				} else if (!item.selected && item.complex) {
					this.redSelected += item.color === 'red' ? -1 : 0
					this.greenSelected += item.color === 'green' ? -1 : 0
				}
			})
		},
		
		beforeDestroy () {
			this.$root.$off('removeTextBlock')
		},
		
		methods: {
			generateList () {
				this.list = Array.from({length: 5})
				
				for (let i in this.list) {
					this.list[i] = this.generateItem()
				}
			},
			
			generateItem () {
				let obj = {}
				
				obj.complex = !Math.round(Math.random())
				obj.text = loremIpsum({count: 4})
				
				return obj
			},
			
			addItem () {
				this.list.push(this.generateItem())
			}
		}
	}
</script>

<style lang="scss">
	#app {
		font-family: 'Avenir', Helvetica, Arial, sans-serif;
		-webkit-font-smoothing: antialiased;
		-moz-osx-font-smoothing: grayscale;
		text-align: center;
		color: #2c3e50;
		margin-top: 60px;
		margin-bottom: 40px;
		
		h3 {
			margin-bottom: 10px;
			text-align: left;
		}
	}
	
	.info-panel {
		margin-bottom: 40px;
		background-color: #eaeaea;
		border-radius: 5px;
		padding: 20px;
	}
	
	.items-list {
		margin-bottom: 40px;
	}
	
	.info-block {
		text-align: left;
		&__head {
			font-size: 16px;
			margin-bottom: 8px;
		}
		&__body {
			span {
				margin-right: 20px;
				
				&:last-child {
					margin-right: 0;
				}
			}
		}
	}
	
	.red {
		color: #f57474;
	}
	
	.green {
		color: #16b316;
	}
	
	.fade-enter-active,
	.fade-leave-active {
		transition: opacity .5s;
	}
	
	.fade-enter, .fade-leave-to {
		opacity: 0;
	}
</style>
