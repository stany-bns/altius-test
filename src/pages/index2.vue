<template>
  <div>
      <canvas id="canv" width ="320" height ="150"
        style ="width :80% ; height : auto ; background : white ; border : 5px solid maroon; box-shadow : 5px 5px 5px grey;" >
      </canvas>

      <q-btn class="q-mx-sm" color="primary" label="launch" @click="start" />
  </div>
</template>

<script>
export default {
  data: () => ({

  }),
  methods: {
    start () {
      const canvas = document.getElementById('canv')

      const X = canvas.width
      const Y = canvas.height

      const ctx = canvas.getContext('2d')
      ctx.shadowColor = 'grey'
      ctx.shadowOffsetX = 3
      ctx.shadowOffsetY = 3
      ctx.shadowBlur = 3
      const rayon = 6
      // let x = X / 2; let y = Y / 2
      let x = X / 2; let y = Y / 2
      // coordonnées initiales de la boule de billard
      let sensX = 3; let sensY = 3
      ctx.fillStyle = 'red'

      setInterval(animate, 50) // toutes les 50 millisecondes ou 20 fois par seconde (1000/50)

      function animate () {
        ctx.clearRect(0, 0, X, Y)
        ctx.beginPath()
        ctx.arc(x, y, rayon, 0, Math.PI * 2) // définir une boule
        ctx.fill() // dessiner la boule
        if (x > X - rayon || x <= rayon) { // Si boule touche le bord gauche ou droit alors sensX change de signe
          sensX = sensX * -1
        }
        if (y >= Y - rayon || y <= rayon) { // Si boule touche le bord haut ou bas alors sensY change de signe
          sensY = sensY * -1
        }
        x = x + sensX; y = y + sensY
        // nouvelles coordonnées de la boule
      }
    }
  }
}
</script>

<style>

</style>
