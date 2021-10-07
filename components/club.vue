<template>
  <div
    class="club h-40 w-80"
    :style="styleObject"
  >
    <a
      :href="club.website"
      target="_blank"
    >
      <h1 :style="{background: club.color2}">{{ club.club_full }}</h1>
    </a>
  </div>
</template>

<script>
export default {
  props: {
    club: {
      required: true,
      type: Object
    }
  },
  computed: {
    styleObject: function() {
      return {
          background: this.club.color3,
          color: this.club.color1,
          backgroundImage: `url(${this.club.logo})`,
          backgroundSize: 'contain',
          backgroundRepeat: 'no-repeat',
          backgroundPosition: 'center'
        }
      }
    }
}
</script>

<style>

</style>
