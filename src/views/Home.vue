<template>
  <div class="home">
    
    <Donar msg="Beneficios"/>
  </div>
</template>

<script>
// @ is an alias to /src
import Donar from '@/components/Donar.vue'

export default {
  name: 'Home',
  components: {
    Donar
  }
}
</script>