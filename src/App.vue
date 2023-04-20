<template>
  <h1 class="title">Marvel Watch Tracker</h1>
  <Suspense>
    <!-- 2 buttons for unwatched and all -->
    <MCUTable />

    <template #fallback>
      <div>Loading...</div>
    </template>
  </Suspense>
</template>

<script>
import MCUTable from '@/components/MCUTable.vue'

export default {
  name: 'App',
  components: {
    MCUTable
  }
}

</script>
