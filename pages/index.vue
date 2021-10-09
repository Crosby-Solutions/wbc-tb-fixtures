<template>
  <div class="root">
    <script src="https://identity.netlify.com/v1/netlify-identity-widget.js" />
    <h1 class="text-xl">
      WBC Tee-ball 2021-22 Fixtures
    </h1>
    <h2 class="text-lg pt-4">
      ToDo List:
    </h2>
    <ul>
      <li>[x] Build content system</li>
      <li>[x] Deploy to Nelify</li>
      <li>[x] Set up access to admin</li>
      <li>[2/5] Input content</li>
      <li>[ ] Build display</li>
      <li>[x] Hook up content query</li>
      <li>[ ] Build Filter UI</li>
      <li>[ ] Hook filters into the query</li>
      <li>[ ] Persist filters to local storage</li>
    </ul>
    <h2 class="text-lg pt-4">
      Stretch functionality - TBD
    </h2>
    <ul>
      <li>[ ] Team Pages</li>
      <li>[ ] Manager contact info</li>
      <li>[ ] Venue maps</li>
      <li>[ ] Diamond Overlays</li>
    </ul>
    <Club
      v-for="club in clubs"
      :key="club.slug"
      :club="club"
    />
    <!-- <p>
      Divisions: {{ divisions.map(d => d.division) }}
    </p>
    <p>
      Teams:
    </p>
    <ul>
      <li
        v-for="team in teams.map(t => t.slug)"
        :key="team"
      >
        {{ team }}
      </li>
    </ul>
    <p>Rounds (Sorted): {{ roundsSorted.map(r => "Division " + r.division + " Round " + r.round + " ("+ r.date + ")") }}</p> -->
    <p>Games: </p>
    <div class="selections flex flex-row w-full justify-evenly">
      <div class="select-rounds">
        <p>Select Rounds</p>
        <select
          id="roundSelect"
          v-model="selectedRounds"
          multiple
          :size="14"
        >
          <option
            v-for="round in listRounds"
            :key="round.label"
            :value="round.value"
          >
            {{ round.label }}
          </option>
        </select><br>
        <button @click="selectRounds('All')">
          Select All
        </button>
        <button @click="selectRounds('None')">
          Select None
        </button>
        <!-- <button @click="selectRounds('Next')">
      Select Next
    </button> -->
        <!-- <p>Selected Rounds: {{ selectedRounds }}</p> -->
      </div>
      <div class="select-divisions">
        <p>
          Select Divisions
        </p>
        <select
          id="divisionSelect"
          v-model="selectedDivisions"
          multiple
          :size="5"
        >
          <option
            v-for="div in listDivisions"
            :key="div.label"
            :value="div.value"
          >
            {{ div.label }}
          </option>
        </select>
        <!-- <p>
          Selected Divisions: {{ selectedDivisions }}
        </p> -->
      </div>
      <div class="select-teams">
        <p>Select Teams</p>
        <select
          id="teamSelect"
          v-model="selectedTeams"
          multiple
          :size="10"
        >
          <option
            v-for="team in listTeams"
            :key="team.value"
            :value="team.value"
          >
            {{ team.label }}
          </option>
        </select>
        <br>
        <button @click="selectTeams('Willetton')">
          All Willetton Teams
        </button>
        NOT YET IMPLEMENTED
        <button @click="selectRounds('All')">
          All Games At Willetton
        </button>
        <!-- <p>Selected Teams: {{ selectedTeams }}</p> -->
      </div>
    </div>
    <div
      v-if="selectedDivisions.length"
      class="selectTeams"
    />
    <div class="gamelist flex flex-row flex-wrap w-full">
      <Game
        v-for="game in listGames"
        :key="game.slug"
        :game="game"
        :home="getTeamFromID(game.teamHome)"
        :away="getTeamFromID(game.teamAway)"
        :club-home="getClubFromTeamID(game.teamHome)"
        :club-away="getClubFromTeamID(game.teamAway)"
      />
    </div>
  </div>
</template>

<script>
import Club from "@/components/club.vue"
import Game from "@/components/game.vue"
export default {
  components: {
    Club,
    Game
  },
   async asyncData({ $content }) {
    const clubs = await $content("_clubs").fetch();
    console.log('clubs: ', clubs)
    const divisions = await $content("_divisions")
      .sortBy('age', 'asc') // alphabetical sort
      .fetch();
    console.log('divisions: ', divisions)
    const teams = await $content("_teams")
      .sortBy('division', 'asc') // alphabetical sort
      .sortBy('division_number', 'asc') // alphabetical sort
      .fetch();
    console.log('teams: ', teams)
    const rounds = await $content("_rounds")
      // needs manual sorting as division is alphabetical
      .fetch();
    console.log('rounds: ', rounds)
    const games = await $content("_games")
      .sortBy('division', 'asc') // alphabetical sort
      .sortBy('round', 'asc') // alphabetical sort
      .fetch();
    console.log('games: ', games)

    return {
      clubs,
      divisions,
      teams,
      rounds,
      games
    };
  },
  data () {
    return {
      selectedRounds: [],
      selectedDivisions: ["U8", "U9", "U10", "U11", "U12"],
      selectedTeams: []
    }
  },
  computed: {
    listRounds() {
      return this.rounds
        .filter(r => r.division == "U8") // just use one set of rounds
        .map(r => {
          return {
            label: r.round + "-" + r.division,
            value: r.round
          }
        })
    },
      listDivisions() {
        return this.divisions.map(div => {
          return {
            label: div.division,
            value: div.division
          }
        })
      },
    listTeams() {
      return [...this.teams]
        .filter(t => this.selectedDivisions.includes(t.division))
        .filter(t => t.club) // filter out the Byes
        .map(t => {
          return {
            label: `${t.club} ${t.team} [${t.division}]`,
            value: t.team_id
          }
          })
        .sort((a, b) => {
          return (a.label > b.label) ? 1 : -1
        })
        
    },
    listGames() {
      return this.games
        .filter(g => this.selectedDivisions.includes(g.division)) // from the selected divisions
        .filter(g => this.selectedTeams.includes(g.teamHome) || this.selectedTeams.includes(g.teamAway)) // from the selected teams
        .filter(g => this.selectedRounds.length && this.selectedRounds.includes(g.round)) // from the selected rounds
        .sort((a, b) => a.round > b.round ? 1 : -1) // sorted by round
    },
    roundsSorted() {
      return [...this.rounds]
        .sort((a, b) => {
          const adiv = parseInt(a.division.slice(1))
          const bdiv = parseInt(b.division.slice(1))
          const ard = parseInt(a.round)
          const brd = parseInt(b.round)
          if (adiv > bdiv) return 1
          else if (adiv < bdiv) return -1
          else return (ard > brd) ? 1 : -1
        })
    }
  },
  methods: {
    getTeamFromID(id) {
      return this.teams.find(t => t.team_id === id)
    },
    getClubFromTeamID(id) {
      const club = this.teams.find(t => t.team_id === id).club
      const clubObj = this.clubs.find(c => c.club === club)
      console.log('clubObj: ', clubObj)
      return clubObj
    },
    selectTeams(input) {
      // for selecting all teams by club
      if (this.clubs.map(c => c.club).includes(input)) {
        this.selectedTeams = [...this.teams
          .filter(t => t.club === input)
          .map(t => t.team_id)
        ]
      }
    },
    selectRounds(input) {
      let selection
      switch(input) {
        case 'All':
          selection = this.rounds
            .filter(r => r.division === 'U8')
            .map(r => r.round)
          break;
        case 'None':
          selection = []
          break;
        case 'Next':
        case 'Last':
          break;
        default:
          return
      }
      this.selectedRounds = selection
    }
  }
};
</script>
<style>
  /* @tailwind base;
  @tailwind components;
  @tailwind utilities; */

  button {
    @apply border p-2;
  }
  #teamSelect {
    width: 300px;
  }
  #divisionSelect {
    width: 100px;
  }
  #roundSelect {
    width: 200px;
  }
</style>
