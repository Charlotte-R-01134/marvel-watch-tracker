<template>
  <div class="display">
    <h1 class="title">Stats</h1>
    <div class="percentage">
      <div class="total-length">
        <span>Total Length: </span>
        <span>{{ totalLength }}</span>
      </div>
      <div class="watched-length">
        <span>Watched Length: </span>
        <span>{{ watchedLength }}</span>
      </div>
      <div class="watched-percentage">
        <span>Watched Percentage: </span>
        <span>{{ watchedPercentage }}%</span>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default  {
  name: 'StatsView',
  async setup() {
    let { data } = await axios.get('http://localhost:3000/mcu')
    return {
      data
    }
  },
  computed: {
    watchedPercentage() {
      let total = 0
      let watched = 0
      for (let mcu of this.data) {
          total++
          if (mcu.watched) {
            watched++
          }
        }
      return Math.floor((watched / total) * 100)
    },
    totalLength() {
      let total = 0
      for (let mcu of this.data) {
        total += mcu.length
      }
      // convert to [days, hours, minutes
    let days = Math.floor(total / 1440)
    let hours = Math.floor((total % 1440) / 60)
    let minutes = total % 60
    return `${days} days ${hours} hours ${minutes} minutes`
    },
    watchedLength() {
      let total = 0
      for (let mcu of this.data) {
        if (mcu.watched) {
          total += mcu.length
        }
      }
      let days = Math.floor(total / 1440)
      let hours = Math.floor((total % 1440) / 60)
      let minutes = total % 60
      return `${days} days ${hours} hours ${minutes} minutes`
    }
  }
}
</script>