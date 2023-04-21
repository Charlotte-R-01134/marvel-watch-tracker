<!-- eslint-disable vue/no-parsing-error -->
<template>
  <div class="container">
    <button class="stats" @click="openStats()">Stats</button>
    <div class="options">
      <button :class="{ active: setting }" class="options" @click="setting=true">Unwatched</button>
      <button :class="{ active: !setting }" class="options" @click="setting=false">All</button>
    </div>
    <div class="filter">
      Filter: 
      <select @change="filter($event.target.value)">
        <option value="All">All</option>
        <optgroup label="types">
          <option v-for="typ in types" :key="typ" :value="typ">
            {{ typ }}
          </option>
        </optgroup>
        <optgroup label="ratings">
          <option v-for="rating in ratings" :key="rating" :value="rating">
            {{ rating }}
          </option>
        </optgroup>
      </select>
    </div>
  </div>
  <table>
    <tbody>
      <!-- Headers -->
      <tr class="headers clickable">
        <th @click="changeSort('id')">
          ID
          <span v-if="currSort==='id' && currSortDir === 'asc'">&#8593</span>
          <span v-else-if="currSort==='id' && currSortDir === 'desc'">&#8595</span>
        </th>
        <th @click="changeSort('title')">
          Title
          <span v-if="currSort==='title' && currSortDir === 'asc'">&#8593</span>
          <span v-else-if="currSort==='title' && currSortDir === 'desc'">&#8595</span>
        </th>
        <th @click="changeSort('type')">
          Type
          <span v-if="currSort==='type' && currSortDir === 'asc'">&#8593</span>
          <span v-else-if="currSort==='type' && currSortDir === 'desc'">&#8595</span>
        </th>
        <th @click="changeSort('length')">
          Length
          <span v-if="currSort==='length' && currSortDir === 'asc'">&#8593</span>
          <span v-else-if="currSort==='length' && currSortDir === 'desc'">&#8595</span>
        </th>
        <th @click="changeSort('rating')">
          Rating
          <span v-if="currSort==='rating' && currSortDir === 'asc'">&#8593</span>
          <span v-else-if="currSort==='rating' && currSortDir === 'desc'">&#8595</span>
        </th>
        <th style="cursor: default;">Watched</th>
      </tr>

      <!-- Table Data -->
      <tr v-for="mcu in theList" :key="mcu.id">
        <td>{{  mcu.id }}</td>
        <td class='clickable' @click="openMCU(mcu)">{{  mcu.title }}</td>
        <td>{{  mcu.type }}</td>
        <td>{{ Math.floor( mcu.length / 60) }} hrs {{ mcu.length % 60 }} mins</td>
        <td>
          <select v-if="mcu.rating != 0" v-model="mcu.rating" @change="updateMCU(mcu)">
            <option value="0"></option>
            <option v-for="rating in ratings" :key="rating" :value="rating">
              {{ rating }}
            </option>
          </select>
          <select v-else value="" v-model="mcu.rating" @change="updateMCU(mcu)">
            <option value="0"></option>
            <option v-for="rating in ratings" :key="rating" :value="rating">
              {{ rating }}
            </option>
          </select>
        </td>
        <td>
          <input type="checkbox" 
            @click="watchMCU(mcu)"
            :checked="mcu.watched"
          >
        </td>
    </tr>
    </tbody>
  </table>
  <ModalView v-if="openedMCU" @closeModal="openedMCU = null">
    <MCUView :mcu="openedMCU" />
  </ModalView>
  <ModalView v-if="openedStats" @closeModal="openedStats = null">
    <StatsView />
  </ModalView>
</template>

<script>
import axios from 'axios'
import { ref } from 'vue'
import MCUView from '@/components/MCUView.vue'
import ModalView from '@/components/ModalView.vue'
import StatsView from '@/components/StatsView.vue'

export default {
  components: {
    MCUView,
    ModalView,
    StatsView
  },
  async setup() {
    let { data } = await axios.get('http://localhost:3000/mcu')
    return {
      data: ref(data),
      openedMCU: ref(null),
      openedStats: ref(false)
    }
  },
  data () {
    return {
      types: ['Movie', 'TV Show', 'Oneshot'],
      ratings: [1, 2, 3, 4, 5],
      setting: true,
      currSort: 'id',
      currSortDir: 'asc'
    }
  },
  computed: {
    theList() {
      if (this.setting) {
        return this.unwatchedMCU
      } else {
        return this.sortedMCU
      }
    },
    sortedMCU() {
      // sort by currSort and curr direction
      return this.sort()
    },
    unwatchedMCU() {
      return this.sortedMCU.filter(mcu => !mcu.watched)
    }
  },
  methods: {
    watchMCU(mcu) {
      mcu.watched = !mcu.watched
      this.updateMCU(mcu)
    },
    updateMCU(mcu) {
      axios.put(`http://localhost:3000/mcu/${mcu.id}`, mcu)
    },
    changeSort(header) {
      if (header === this.currSort) {
        this.currSortDir = this.currSortDir === 'asc' ? 'desc' : 'asc'
      } else {
        this.currSort = header
        this.currSortDir = 'asc'
      }
    },
    sort() {
      let sort = this.currSort
      let dir = this.currSortDir
      let sorted = this.data.sort((a, b) => {
        if (a[sort] < b[sort]) {
          return dir === 'asc' ? -1 : 1
        } else if (a[sort] > b[sort]) {
          return dir === 'asc' ? 1 : -1
        } else {
          return 0
        }
      })
      return sorted
    },
    openMCU(mcu) {
      this.openedMCU = mcu
    },
    openStats() {
      this.openedStats = true
    },
    async filter(option) {
      let { data } = await axios.get('http://localhost:3000/mcu')
      if (option.length === 1) {
        this.data = data.filter(mcu => mcu.rating === parseInt(option))
        return
      }
      if (option === 'All') {
        this.data = data
      } else {
        this.data = data.filter(mcu => mcu.type === option)
      }
    }
  }
}
</script>