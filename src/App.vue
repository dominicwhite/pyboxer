<template>
  <div class="full-width">
    <div id="canvas-container">
      <canvas id="canvas" ref="canvas" v-bind:width="canvasWidth+'px'" v-bind:height="canvasHeight+'px'">
      </canvas>
    </div>
    <div id="canvas-controls">
      <input type="file" v-on:change="loadImage($event)">
      <button id="boxdraw">Draw box</button>
        <div>Value of ctrlPressed is {{ctrlPressed}}</div>
    </div>
    <data-table-component name="Data" />
  </div>
</template>

<script>

import DataTableComponent from './components/DataTableComponent.vue';

export default {
    data: function() {
        return {
            canvasHeight: 550,
            scale: 1,
            offsetX: 0,
            offsetY: 0,
            boxedLetters: [],
            tempBoxCoords: [0, 0, 0, 0],
            ctrlPressed: false,
            isDrawing: false,
            image: null
        };
    },
    computed: {
        canvasWidth: function(){
            const b = document.getElementById('app');
            console.log(Math.round(0.9 * b.offsetWidth));
            return Math.round(0.9 * b.offsetWidth);
        }
    },
    mounted: function(){
        window.addEventListener('keydown', (k) => {
            if (k.key === 'Control'){
                this.ctrlPressed = true;
                console.log(this.ctrlPressed);
            }
        });
        window.addEventListener('keyup', (k) => {
            if (k.key === 'Control'){
                this.ctrlPressed = false;
                console.log(this.ctrlPressed);
            }
        });
        const canvas = this.$refs.canvas;
        canvas.onmousedown = this.beginDraw;
        canvas.onmouseup = this.endDraw;
        canvas.onmousemove = this.midDraw;
    },
    components: {
        DataTableComponent
    },
    methods: {
        loadImage: function(e) {
            console.log(e.target.files);
            const canvas = this.$refs.canvas;
            console.log(canvas);
            const ctx = canvas.getContext('2d');
            const img = new Image();
            const file = e.target.files[0];
            if (file.type.match('image.*')) {
                const reader = new FileReader();
                reader.readAsDataURL(file);
                reader.onload = (evt) => {
                    if (evt.target.readyState === FileReader.DONE) {
                        img.src = evt.target.result;
                        img.onload = () => {
                            this.scale = Math.max(img.width/this.canvasWidth, img.height/this.canvasHeight);
                            ctx.drawImage(img, 0, 0, img.width / this.scale, img.height / this.scale);
                            // canvas.onmousedown = this.beginDraw;
                            // canvas.onmouseup = this.endDraw;
                        }
                    }
                }
            }
        },
        beginDraw: function(click){
            if (this.ctrlPressed){
                this.tempBoxCoords = [click.clientX, click.clientY, 0, 0];
                console.log(this.tempBoxCoords);
                this.isDrawing = true;
                console.log('isDrawing:',this.isDrawing);
            }
        },
        midDraw: function(position){
            if (this.isDrawing) {
                console.log('last coords:', this.tempBoxCoords);
                const canvas = this.$refs.canvas;
                const ctx = canvas.getContext('2d');
                ctx.clearRect(...this.tempBoxCoords);
                let coords = [this.tempBoxCoords[0], this.tempBoxCoords[1]];
                coords.push(position.clientX - this.tempBoxCoords[0]);
                coords.push(position.clientY - this.tempBoxCoords[1]);
                this.drawRect(coords);
                this.tempBoxCoords = coords;
            }
        },
        endDraw: function(click){
            if (this.isDrawing){
                console.log(click);
                this.isDrawing = false;
                const canvas = this.$refs.canvas;
                const ctx = canvas.getContext('2d');
                ctx.clearRect(...this.tempBoxCoords);
                let coords = [this.tempBoxCoords[0], this.tempBoxCoords[1]];
                coords.push(click.clientX - this.tempBoxCoords[0]);
                coords.push(click.clientY - this.tempBoxCoords[1]);
                this.drawRect(coords);
                this.tempBoxCoords = coords;
            }
        },
        drawRect: function(coords){
            console.log(coords);
            const canvas = this.$refs.canvas;
            const ctx = canvas.getContext('2d');
            ctx.beginPath();
            ctx.lineWidth="6";
            ctx.strokeStyle="red";
            ctx.rect(coords[0], coords[1], coords[2], coords[3]);
            ctx.stroke();
        }
  }
}
</script>

<style>
#canvas {
  background-color: gray;
}
</style>
