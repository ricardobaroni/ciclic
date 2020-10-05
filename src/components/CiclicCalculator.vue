<template>
  <div>
    <div class="min-h-screen flex items-start justify-center bg-gray-100 py-12 px-4 sm:px-6 lg:px-8">
      <div class="max-w-md w-full">
          <Header />
          <Simulator v-if="!userResponse" v-on:store="userResponse = $event" />
          <Result v-else :userResponse="userResponse" @back="userResponse = null" />
      </div>
    </div>
  </div>
</template>

<script>
import Header from './Header.vue';
import Result from './Result.vue';
import Simulator from './Simulator.vue';

export default {
  name: 'CiclicCalculator',
  components: {
    Simulator,
    Result,
    Header
  },
    data () {
      return {
          userResponse:null,
      }
  },
}
</script>

