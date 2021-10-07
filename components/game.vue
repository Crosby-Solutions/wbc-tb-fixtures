<template>
  <div class="game border m-4 flex flex-col justify-between">
    <div class="headline flex flex-row justify-between">
      <div class="round text-left">
        Rd: {{ game.round }}
      </div>
      <div class="division text-right">
        Division {{ game.division }}
      </div>
    </div>
    <div class="lineup flex flex-row justify-around">
      <div class="homeTeam p-4">
        {{ game.teamHome }}
      </div> <div class="versus p-4">
        vs
      </div><div class="awayTeam p-4">
        {{ game.teamAway }}
      </div>
    </div>
    <div class="footline flex flex-row justify-between">
      <div class="time mt-auto">
        {{ game.day }} {{ game.time }}
      </div>
      <div class="diamond m-4 border-2 w-12 h-12 text-center">
        <div class="text text-center text-2xl">
          {{ game.diamond }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    game: {
      required: true,
      type: Object
    }
  }

}
</script>

<style scoped>
.diamond { 
  transform: rotate(45deg);
}
.text {
  transform: rotate(-45deg);
}

</style>
