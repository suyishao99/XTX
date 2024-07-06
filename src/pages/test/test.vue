<template>
  <view class="container">
    <uni-file-picker :mode="'image'" @success="onFileUpload"></uni-file-picker>
    <canvas ref="canvas" class="canvas" canvas-id="segmentationCanvas"></canvas>
  </view>
</template>

<script>
export default {
  data() {
    return {
      segmentationData: [
        [
          [972.953125, 415.5875244140625],
          [970.2875366210938, 418.2531433105469],
          [970.2875366210938, 431.5812683105469],
          [972.953125, 434.24688720703125],
          [986.28125, 434.24688720703125],
          [988.9468994140625, 431.5812683105469],
          [988.9468994140625, 418.2531433105469],
          [986.28125, 415.5875244140625],
        ],
      ],
      image: null,
    }
  },
  methods: {
    onFileUpload(event) {
      const file = event.tempFiles[0]
      this.image = file.path
      this.drawImage()
    },
    drawImage() {
      const ctx = uni.createCanvasContext('segmentationCanvas', this)
      const image = this.image

      uni.getImageInfo({
        src: image,
        success: (res) => {
          const { width, height } = res

          // Adjust canvas size based on image size
          this.$refs.canvas.width = width
          this.$refs.canvas.height = height

          ctx.drawImage(image, 0, 0, width, height)
          this.drawSegmentation(ctx)
        },
      })
    },
    drawSegmentation(ctx) {
      ctx.setStrokeStyle('red')
      ctx.setLineWidth(2)

      this.segmentationData.forEach((segment) => {
        ctx.beginPath()
        segment.forEach((point, index) => {
          const [x, y] = point
          if (index === 0) {
            ctx.moveTo(x, y)
          } else {
            ctx.lineTo(x, y)
          }
        })
        ctx.closePath()
        ctx.stroke()
      })

      ctx.draw()
    },
  },
}
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.canvas {
  margin-top: 20px;
  border: 1px solid #000;
}
</style>
