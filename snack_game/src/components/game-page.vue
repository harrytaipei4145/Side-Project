<template>
	<div>
		<button @click="onButtonClick"> {{ buttonText }} </button>
		<span> {{ score }} </span>
		<canvas width='200' height='200' id="game-canvas"/>
	</div>
</template>

<script>
const INTERVAL_VAL = 100;
const BUTTON_TEXT = {
	Start: "Start",
	Stop: "Stop"
};
const CANVAS_WIDTH = 200;
const CANVAS_HEIGHT = 200;
const GRID_SIZE = 10;
const BODY_MARGIN = 0.5;

export default {
	name: 'GamePage',
	data() {
		return {
			buttonText: BUTTON_TEXT.Start,
			score: 0,
		}
	},
	mounted() {
		let canvas = document.getElementById('game-canvas');

		this.ctx = canvas.getContext('2d');
		this.ctx.fillRect(0, 0, CANVAS_WIDTH, CANVAS_HEIGHT);
		this.dataInitial();
	},
	beforeUnmount() {
		this.onStop();
	},
	methods: {
		onButtonClick() {
			if (BUTTON_TEXT.Start === this.buttonText) {
				this.onStart();
			} else {
				this.onStop();
			}
		},
		onStart() {
			this.dataInitial();
			this.interval = setInterval(this.updateCanvas, INTERVAL_VAL);
			this.buttonText = BUTTON_TEXT.Stop;
			document.addEventListener('keydown', this.onKeydown, false);
		},
		onStop() {
			if (this.interval) {
				clearInterval(this.interval);
			}
			this.buttonText = BUTTON_TEXT.Start;
			document.removeEventListener('keydown', this.onKeydown, false);
		},
		onKeydown(e) {
			if ((37 <= e.which) || (40 >= e.which)) {
				this.keyVal = e.which;
			}
		},
		dataInitial() {
			this.score = 0;
			this.bodyList = [{x: CANVAS_WIDTH / 2, y: CANVAS_HEIGHT / 2}];
			this.maxSize = 5;
			this.direction = {x: 0, y: -1};
			this.point = null;
		},
		updateCanvas() {
			this.updateBackground();
			this.updateDirection();
			if (!this.point) {
				this.initPointPos();
			}
			this.updatePoint();
			this.updateBody();
			this.updateScore();
		},
		updateBackground() {
			this.ctx.fillStyle = "black";
			this.ctx.fillRect(0, 0, 200, 200);
		},
		updateDirection() {
			if ((37 === this.keyVal) && (0 === this.direction.x)) {
				// left
				this.direction.x = -1;
				this.direction.y = 0;
			} else if ((38 === this.keyVal) && (0 === this.direction.y)) {
				// top
				this.direction.x = 0;
				this.direction.y = -1;
			} else if ((39 === this.keyVal) && (0 === this.direction.x)) {
				// right
				this.direction.x = 1;
				this.direction.y = 0;
			}  else if ((40 === this.keyVal) && (0 === this.direction.y)) {
				// bottom
				this.direction.x = 0;
				this.direction.y = 1;
			}
			this.keyVal = null;
		},
		initPointPos() {
			this.point = {
				x: Math.floor(Math.random() * CANVAS_WIDTH / GRID_SIZE) * GRID_SIZE,
				y: Math.floor(Math.random() * CANVAS_HEIGHT / GRID_SIZE) * GRID_SIZE
			}
		},
		updatePoint() {
			this.ctx.fillStyle = "red";
			this.ctx.fillRect(this.point.x + BODY_MARGIN, this.point.y + BODY_MARGIN,
							GRID_SIZE - 2 * BODY_MARGIN, GRID_SIZE - 2 * BODY_MARGIN);
		},
		updateBody() {
			let newX = this.bodyList[0].x + this.direction.x * GRID_SIZE;
			let newY = this.bodyList[0].y + this.direction.y * GRID_SIZE;

			if (true === this.isFailed(newX, newY)) {
				this.onStop();
			} else {
				this.bodyList.unshift({
					x: newX,
					y: newY,
				});

				if (this.bodyList.length > this.maxSize) {
					this.bodyList.pop();
				}
			}

			this.ctx.fillStyle = "yellow";
			this.bodyList.forEach((body) => {
				this.ctx.fillRect(body.x + BODY_MARGIN, body.y + BODY_MARGIN,
								GRID_SIZE - 2 * BODY_MARGIN, GRID_SIZE - 2 * BODY_MARGIN);
			});
		},
		isFailed(x, y) {
			if ((x < 0) || (y < 0) || (x >= CANVAS_WIDTH) || (y >= CANVAS_HEIGHT)) {
				return true;
			}

			for (let i = 0; i < this.bodyList.length - 1; i++) {
				if ((this.bodyList[i].x === x) && (this.bodyList[i].y === y)) {
					return true;
				}
			}
			return false;
		},
		updateScore() {
			let isTouch = false;

			this.bodyList.forEach((body) => {
				if ((body.x === this.point.x) && (body.y === this.point.y)) {
					isTouch = true;
				}
			});

			if (true === isTouch) {
				this.point = null;
				this.score++;
				this.maxSize++;
			}
		},
	}
}
</script>

<style>
div {
	width: 500px;
	height: 500px;
}
</style>