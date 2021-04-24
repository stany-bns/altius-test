<template>
  <div class="row">
    {{this.billardTableWidth}}
    {{this.billardTableHeight}}
    <div id="billard-table" class="bg relative-position items-center justify-center q-my-lg" :style="`height:${billardTableHeight}; width:${billardTableWidth}`">
      <div v-for="(x, index) in matrix" :key="index" class="row">
        <div v-for="(y, i) in x" :key="i" :style="`width: ${size}px; height: ${size}px; border:1px solid`" class="text-center">
        </div>
      </div>

      <div v-show="!one.hidden" :id="one.id" class="absolute" :class="one.class" :style="`width: ${size}px; height: ${size}px; border:1px solid; border-radius:100%; top:${one.y}px; left:${one.x}px`">
        {{one.id}}
      </div>

      <div v-show="!two.hidden" :id="two.id" class="absolute" :class="two.class" :style="`width: ${size}px; height: ${size}px; border:1px solid; border-radius:100%; top:${two.y}px; left:${two.x}px`">
        {{two.id}}
      </div>

      <div v-show="!three.hidden" :id="three.id" class="absolute" :class="three.class" :style="`width: ${size}px; height: ${size}px; border:1px solid; border-radius:100%; top:${three.y}px; left:${three.x}px`">
        {{three.id}}
      </div>

      <div v-show="!four.hidden" :id="four.id" class="absolute" :class="four.class" :style="`width: ${size}px; height: ${size}px; border:1px solid; border-radius:100%; top:${four.y}px; left:${four.x}px`">
        {{four.id}}
      </div>

    </div>
    <div class="row col-12 text-center justify-center">
        <q-btn class="q-mx-sm" color="primary" label="launch" @click="launch" />
        <q-btn class="q-mx-sm" color="primary" label="stop" @click="clear" />
    </div>
    <div class="row col-12  text-center justify-center">
      <div class="text-h6">
        BILLARD SUMILATION
      </div>
      <test />
    </div>
  </div>
</template>

<script>

import test from './index2'
export default {
  components: {
    test
  },
  data: () => ({
    start: false,
    interval: '',
    n: 8,
    t: 100,
    size: 40,
    step: 20,
    balls: {
      one: {
        id: 'ball-3',
        class: 'bg-red',
        xOffset: 0,
        x: 0,
        yOffset: 0,
        y: 0,
        xDirection: 280,
        yDirection: 0,
        hidden: false,
        paroi: false
      },
      two: {
        id: 'ball-4',
        class: 'bg-light-blue',
        xOffset: 0,
        x: 280,
        yOffset: 0,
        y: 0,
        xDirection: -280,
        yDirection: 0,
        hidden: false,
        paroi: false
      },
      three: {
        id: 'ball-1',
        class: 'bg-red',
        xOffset: 0,
        x: 0,
        yOffset: 0,
        y: 280,
        xDirection: 280,
        yDirection: 0,
        hidden: false,
        paroi: false
      },
      four: {
        id: 'ball-2',
        class: 'bg-light-blue',
        xOffset: 0,
        x: 280,
        yOffset: 0,
        y: 280,
        xDirection: -280,
        yDirection: 0,
        hidden: false,
        paroi: false
      }
    },
    matrix: []
  }),
  computed: {
    one () {
      return this.balls.one
    },
    two () {
      return this.balls.two
    },
    three () {
      return this.balls.three
    },
    four () {
      return this.balls.four
    },
    billardTableHeight () {
      return this.size * this.n
    },
    billardTableWidth () {
      return this.size * this.n
    },
    ballLimitX: () => this.billardTableWidth - this.size,
    ballLimitY: () => this.billardTableHeight - this.size
  },
  methods: {
    init () {},
    createMatrix: function (n) {
      let counter = 1

      const matrix = Array(this.n).fill(0).map(x => {
        return Array(this.n).fill(0)
      })

      for (let index = 0; index < matrix.length; index++) {
        const element = matrix[index]

        for (let i = 0; i < element.length; i++) {
          matrix[index][i] = counter
          counter++
        }
      }

      this.matrix = matrix
    },
    xBallCollisionOrientation (ball, { key, limit }) {
      console.log('x collision')

      this.balls[key].xDirection = this.balls[key].xDirection.toString().match(/^-/) ? (240 / 180) * 45 : (-240 / 180) * 45
      this.balls[key].yDirection = this.balls[key].yDirection.toString().match(/^-/) ? (240 / 180) * 45 : (-240 / 180) * 45
    },
    xParoiBallCollisionOrientation (ball, { key, limit }) {
      console.log(`x-paroi => ${this.balls[key].id}`)

      this.balls[key].x = this.balls[key].x.toString().match(/^-/) ? parseInt(this.balls[key].x.toString().replace('-', '')) : this.balls[key].x
      this.balls[key].y = this.balls[key].y.toString().match(/^-/) ? parseInt(this.balls[key].y.toString().replace('-', '')) : this.balls[key].y

      this.balls[key].xDirection = this.balls[key].xDirection.toString().match(/^-/) ? (240 / 180) * 90 : (240 / 180) * 90
      this.balls[key].yDirection = this.balls[key].yDirection.toString().match(/^-/) ? (-240 / 180) * 90 : (240 / 180) * 90

      console.log(limit)
    },
    yParoiBallCollisionOrientation (ball, { key, limit }) {
      console.log('y-paroi')
    },
    setBallsDirection () {
      for (const key in this.balls) {
        const ball = document.getElementById(this.balls[key].id)

        // check collision

        for (const k in this.balls) {
          if (key !== k) {
            const otherBall = document.getElementById(this.balls[k].id)
            const collisionX = ball.offsetLeft === otherBall.offsetLeft
            const collisionY = ball.offsetTop === otherBall.offsetTop

            if (collisionX && collisionY) this.xBallCollisionOrientation(ball, { key, limit: ball.offsetLeft })
          }
        }

        if (this.balls[key].x < 0 || this.balls[key].x > 280) {
          this.xParoiBallCollisionOrientation(ball, { key, limit: { x: this.balls[key].x, y: this.balls[key].y } })
        } else if (this.balls[key].y < 0 || this.balls[key].y > 280) {
          this.yParoiBallCollisionOrientation(ball, { key, limit: { x: this.balls[key].x, y: this.balls[key].y } })
        } else {
          this.balls[key].x = this.balls[key].x + (this.balls[key].xDirection / this.step)
          this.balls[key].y = this.balls[key].y + (this.balls[key].yDirection / this.step)
        }
      }
    },
    launch () {
      this.interval = setInterval(() => {
        this.setBallsDirection()
      }, this.t)
    },
    clear () {
      clearInterval(this.interval)
    }

  },
  async mounted () {
    /* for (const key in this.balls) {
      this.balls[key].hidden = true
    } */
    this.createMatrix()
  }
}
</script>

<style>

</style>
