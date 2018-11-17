<template>
	<div :class="['text-block', {'is-selected': selected}, complexClass]">
		<div class="text-block__close" @click="removeItem"></div>
		<div class="text-block__content" @click="clickHandler" @dblclick="dblClickHandler">
			<slot/>
		</div>
		
		<b-modal v-model="modalShow" cancel-title="Отмена" ok-title="Да" @ok="confirmRemoving">
			Удалить блок?
		</b-modal>
	</div>
</template>

<script>
	export default {
		name: 'TextBlock',
		props: {
			/**
			 * Enables/disables complex block display
			 * @type {Boolean}
			 */
			complex: {
				type: Boolean,
				default: false
			},
			
			index: {
				type: Number
			}
		},
		
		data () {
			return {
				selected: false,
				dbClick: false,
				modalShow: false,
				timer: 0,
				delay: 200,
				prevent: false
			}
		},
		
		computed: {
			complexClass () {
				if (this.complex) {
					return this.dbClick ? 'text-block--green' : 'text-block--red'
				}
			}
		},
		
		methods: {
			removeItem () {
				if (this.complex) {
					this.modalShow = true
					return
				}
				
				this.selectItem(false)
				this.$root.$emit('removeTextBlock', this.index)
			},
			
			clickHandler () {
				let self = this
				
				this.timer = setTimeout(() => {
					if (!self.prevent) {
						self.selectItem(!self.selected)
					}
					self.prevent = false
				}, self.delay)
			},
			
			dblClickHandler () {
				clearTimeout(this.timer)
				this.prevent = true
				this.dbClick = !this.dbClick
			},
			
			selectItem (value) {
				this.selected = value
				
				this.$root.$emit('selectBlock', {
					selected: this.selected,
					complex: this.complex,
					color: this.complex ? (this.dbClick ? 'green' : 'red') : false
				})
			},
			
			confirmRemoving () {
				this.selectItem(false)
				this.$root.$emit('removeTextBlock', this.index)
			}
		}
	}
</script>

<style lang="scss">
	.text-block {
		position: relative;
		background-color: white;
		border-radius: 5px;
		margin-bottom: 16px;
		transition: background-color 0.3s ease;
		
		&:before {
			content: '';
			position: absolute;
			z-index: 0;
			width: 100%;
			height: 100%;
			top: 0;
			left: 0;
			border: 1px solid gainsboro;
			border-radius: 5px;
			transition: border 0.3s ease;
		}
		
		&:last-child {
			margin-bottom: 0;
		}
		
		&__close {
			position: absolute;
			z-index: 1;
			top: 10px;
			right: 10px;
			width: 32px;
			height: 32px;
			background: url('../assets/cancel.svg') no-repeat center/14px 14px;
			opacity: 0.3;
			cursor: pointer;
			transition: opacity 0.3s ease;
			
			&:hover {
				opacity: 0.5;
			}
		}
		
		&__content {
			position: relative;
			padding: 20px 40px 20px 20px;
			font-size: 16px;
			text-align: left;
			user-select: none;
		}
		
		&.is-selected {
			&:before {
				border: 3px solid #6499ff;
			}
			
		}
		
		&--red {
			background-color: #ffe7e7;
		}
		
		&--green {
			background-color: #caffce;
		}
	}
</style>
