<template>
  <div class="game border m-4 flex flex-col justify-between">
    <div class="headline flex flex-row justify-between p-1 bg-gray-200">
      <div class="round text-left">
        Rd: {{ game.round }}
      </div>
      <div class="division text-right">
        Division {{ game.division }}
      </div>
    </div>
    <div class="lineup flex flex-row justify-around p-2">
      <div
        class="homeTeam p-4 relative"
      >
        <div 
          :style="styleObjectHome"
          class="logo-background"
        />
        <div class="text-overlay">
          <span class="text-base">
            {{ home.club }}
          </span>
          <br>
          <span class="text-2xl">
            {{ home.team }}
          </span>
        </div>
      </div>
      <div class="versus p-4">
        vs
      </div>
      <div
        class="awayTeam p-4 relative"
      >
        <div 
          :style="styleObjectAway"
          class="logo-background"
        />
        <div class="text-overlay">
          <span class="text-base">
            {{ away.club!=="Bye" ? away.club : "" }}
          </span>
          <br>
          <span class="text-2xl">
            {{ away.team }}
          </span>
        </div>
      </div>
    </div>
    <div class="footline relative flex flex-row justify-between p-1 mt-8 bg-gray-200">
      <div class="time mt-auto">
        {{ game.day }} {{ game.time }}
      </div>
      <a :href="clubHome.venue">
        <div class="diamond absolute bottom-0 right-0 m-4 border-2 w-10 h-10 text-center bg-green-800">
          <div class="text text-center text-2xl text-white">
            <!-- don't show bye -->
            {{ game.diamond !== 0 ? game.diamond : "" }} 
          </div>
        </div>
      </a>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    game: {
      required: true,
      type: Object
    },
    home: {
      required: true,
      type: Object,
    },
    away: {
      required: true,
      type: Object
    },
    clubHome: {
      required: true,
      type: Object,
    },
    clubAway: {
      required: true,
      type: Object
    }
  },
  computed: {
    styleObjectHome() {
      return {
        backgroundImage: `url(${this.clubHome.logo})`,
      }
    },
    styleObjectAway() {
      return {
        backgroundImage: `url(${this.clubAway.logo})`,
      }
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
.text-overlay {
  z-index: 1;
  text-align: center;
  @apply w-full h-full;
}
.logo-background {
  opacity: 0.15;
  background-size: contain;
  background-repeat: no-repeat;
  background-position: center;
  z-index: -1;
  @apply w-full h-full absolute top-0 left-0;
}

</style>
