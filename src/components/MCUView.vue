<template>
  <div class="display">
    <h1 class="title">{{ mcu.title }}</h1>
    <div class="description">
      <em>{{ mcu.description }}</em>
      <br>
      <br>
      <div class="length">
        <span>Total Length: </span>
        <span>{{ Math.floor(mcu.length / 60) }} hrs {{ mcu.length % 60 }} mins</span>
      </div>
    </div>
    <div class="eps" v-if="mcu.eps">
      <table>
        <t-body>
          <tr class="headers">
            <th>ID</th>
            <th>Title</th>
            <th>Length</th>
            <th>Watched</th>
          </tr>

          <tr v-for="ep in mcu.eps" :key="ep.id">
            <td>{{ ep.id }}</td>
            <td>{{ ep.title }}</td>
            <td>{{ Math.floor(ep.length / 60) }} hrs {{ ep.length % 60 }} mins</td>
            <td>
              <input type="checkbox" v-model="ep.watched" @change="updateMCU(mcu)" />
            </td>
          </tr>
        </t-body>
      </table>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'MCUView',
  props: {
    mcu: {
      type: Object,
      required: true
    }
  },
  methods: {
    updateMCU(mcu) {
      axios.put(`http://localhost:3000/mcu/${mcu.id}`, mcu)
    }
  }
}
</script>