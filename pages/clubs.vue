<template>
  <div class="clubs">
    <Club
      v-for="club in clubs"
      :key="club.slug"
      :club="club"
    />
  </div>
</template>

<script>
import Club from "@/components/club.vue"
export default {
  components: {
    Club,
  },
    async asyncData({ $content }) {
    const clubs = await $content("_clubs").fetch();
    }
}
</script>

<style>

</style>
