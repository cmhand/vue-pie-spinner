<style lang="scss">
.spinner {
	.spinner-wrapper {
	  position: relative;
	  min-height: 16rem;
	}

	.chevron {
		width: 0;
		height: 0;
		border: 8px solid transparent;
		border-bottom: 8px solid black;
		position: absolute;
		top: -5px;
		left: 50%;
		margin-left: -8px;
		z-index: 1;
	}

	.chevron:after {
		content: '';
		position: absolute;
		left: 50%; 
		margin-left: -8px;
		top: 8px;
		width: 0;
		height: 0;
		border: 8px solid transparent;
		border-top: 16px solid #333;
	}

	.circle {
		width: 240px;
		height: 240px;
		position: relative;
		border-radius: 200px;
		box-shadow: 0px 0px 20px -5px;
		margin: 1em auto;
		transition: 1.5s ease-out;
		box-sizing: content-box;
	}

	.spin {
		transform: rotate(1080deg);
	}

	.knob {
		position: absolute;
		top: 50%; left: 50%;
		margin-left: -1rem;
		margin-top: -1.5rem;
		width: 2rem; height: 2rem;
		border-radius: 50%;
		background: #fa1414;
		background: -webkit-linear-gradient(-90deg, rgba(239,197,202,0.4) 0, rgba(210,75,90,0.6) 25%, rgba(186,39,55,0.6) 35%, rgba(186,39,55,0.6) 65%, rgba(241,142,153,0.4) 100%), #f90404;
		background: -moz-linear-gradient(180deg, rgba(239,197,202,0.4) 0, rgba(210,75,90,0.6) 25%, rgba(186,39,55,0.6) 35%, rgba(186,39,55,0.6) 65%, rgba(241,142,153,0.4) 100%), #f90404;
		background: linear-gradient(180deg, rgba(239,197,202,0.4) 0, rgba(210,75,90,0.6) 25%, rgba(186,39,55,0.6) 35%, rgba(186,39,55,0.6) 65%, rgba(241,142,153,0.4) 100%), #f90404;
		background-position: 50% 50%;
		box-shadow: 0 0 10px -1px #333;
		content: '';
	}
}
</style>

<template>
	<div class="spinner">
		<div class="spinner-wrapper">
			<div class="chevron"></div>
			<div class="circle">
				<canvas height="240" width="240"></canvas>
			</div>
			<div class="knob">
			</div>
		</div>
	</div>
</template>

<script>
	export default {
		props: [],
		data() {
			return {
				color: ['red','orange','yellow','green','cyan','blue', "indigo", "violet"],
				slices: 0,
				sliceDeg: 0,
				deg: this.rand(0, 360),
				speed: 0,
				slowDownRand: 0,
				ctx: null,
				width: 0, // size
				center: 0,      // center
				isStopped: false,
				lock: false,
				currentDegrees: 0,
			}
		},
		mounted() {
			this.slices = this.color.length;
			this.sliceDeg = 360/this.slices;
			let canvas = this.$el.querySelector("canvas");
			this.ctx = canvas.getContext('2d');
			this.width = canvas.width;
			this.center = this.width / 2;
			this.drawImg()
		},
		computed: {},
		methods: {
			spin() {
				setTimeout(() => {
					this.isStopped = true;
				}, 500);
				this.isStopped = false;
				this.speed = 15;
				this.animate();
			},
			selectRandom() {
				this.$emit('onSpin');
			},
			rand(min, max) {
				return Math.random() * (max - min) + min;
			},
			deg2rad(deg) {
				return deg * Math.PI/180;
			},
			drawSlice(deg, color) {
				this.ctx.beginPath();
				this.ctx.fillStyle = color;
				this.ctx.moveTo(this.center, this.center);
				this.ctx.arc(this.center, this.center, this.width/2 - 10, this.deg2rad(deg), this.deg2rad(deg+this.sliceDeg));
				this.ctx.lineTo(this.center, this.center);
				this.ctx.fill();
			},
			drawImg() {
				this.ctx.clearRect(0, 0, this.width, this.width);
				for(var i = 0; i < this.slices; i++){
					this.drawSlice(this.deg, this.color[i]);
					this.deg += this.sliceDeg;
				}
			},
			animate() {
				this.deg += this.speed;
				this.deg %= 360;

				// Increment speed
				if(!this.isStopped && this.speed < 10){
					this.speed = this.speed + 1 * 0.5;
				}
				// Decrement Speed
				if(this.isStopped){
					if(!this.lock){
					  this.lock = true;
					  this.slowDownRand = this.rand(0.950, 0.998);
					} 
					this.speed = this.speed > 0.4 ? this.speed *= this.slowDownRand : 0;
				}
				// Stopped!
				if(this.lock && !this.speed){
					this.selectRandom();
					return; 
				}

				this.drawImg();
				window.requestAnimationFrame( this.animate );
			}
		}
	}
</script>