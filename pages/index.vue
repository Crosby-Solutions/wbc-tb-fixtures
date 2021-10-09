<template>
  <div class="root">
    <script src="https://identity.netlify.com/v1/netlify-identity-widget.js" />
    <h1 class="text-2xl">
      WBC Tee-ball 2021-22 Fixtures
    </h1>

    <h2>Filter Options:</h2>
    <div class="selections mx-auto flex flex-row justify-center gap-4 border-2 rounded-md bg-gray-50">
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
        </select>
        <div class="selectOptions">
          <button @click="selectRounds('All')">
            Select All
          </button>
          <button @click="selectRounds('None')">
            Select None
          </button>
        </div>
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
        </p> --> <br>
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
        <div class="selectOptions">
          <p>
            Select a club:

            <select v-model="selectedClub">
              <option
                v-for="club in listClubs"
                :key="club.value"
                :value="club.value"
              >
                {{ club.label }}
              </option>
            </select>
          </p>
          <div
            v-if="selectedClub"
            class="buttons"
          >
            <button @click="selectTeams(selectedClub)">
              Select All {{ selectedClub }} Teams
            </button><br>
            <input
              id="homeGames"
              v-model="showOnlyHomeGames"
              type="checkbox"
            >
            <label for="homeGames">Show Only Home Games</label>
          </div>
        </div>
      </div>
    </div>

    <h2>Games: </h2>
    <p v-if="!listGames.length">
      No games are selected. Please adjust the filters above.
    </p>
    <div 
      v-else
      class="select flex flex-row justify-start"
    >
      Display as: 
      <div
        v-for="option in displayOptions"
        :key="option.value"
        class="selection"
      >
        <label
          :for="option.value"
          class="p-4"
        >
          <input
            :id="option.value"
            v-model="displayAs"
            :value="option.value"
            name="displayAs"
            type="radio"
          >
          {{ option.display }}
        </label>
      </div>
    </div>
    <!-- display as cards -->
    <div
      v-if="displayAs === 'cards'"
      class="gamelist flex flex-row flex-wrap w-full"
    >
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
    <!-- display as table -->
    <div
      v-else
      class="table-container"
    >
      <table class="w-full">
        <thead>
          <tr>
            <th
              v-for="head in tableHeadings"
              :key="head"
            >
              {{ head }}
            </th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="game in listGames"
            :key="game.game_id"
            :class="{'shade-row': game.round%2==1 }"
          >
            <td>{{ game.round }}</td>
            <td>{{ game.division }}</td>
            <td>{{ getDateFromRound(game.round, game.day) }}</td>
            <td>{{ game.day }}</td>
            <td>{{ game.time }}</td>
            <td>{{ getTeamFromID(game.teamHome).club }} {{ getTeamFromID(game.teamHome).team }}</td>
            <td>{{ getTeamFromID(game.teamAway).club }} {{ getTeamFromID(game.teamAway).team }}</td>
            <td>{{ game.diamond }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import Game from "@/components/game.vue"
export default {
  components: {
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
      selectedTeams: [],
      selectedClub: 'Willetton',
      showOnlyHomeGames: false,
      displayAs: 'cards',
      displayOptions: [{ display: 'Cards', value: 'cards'}, {display: 'Table', value: 'table'}],
      tableHeadings: ['Round', 'Division', 'Date', 'Day', 'Time', 'Home', 'Away', 'Diamond']
    }
  },
  computed: {
    listRounds() {
      return this.rounds
        .filter(r => r.division == "U8") // just use one set of rounds
        .map(r => {
          return {
            label: r.round,
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
    listClubs() {
      return this.clubs.map(club => {
        return {
          label: club.club,
          value: club.club
        }
      })
    },
    listTeams() {
      return [...this.teams]
        .filter(t => this.selectedDivisions.includes(t.division))
        .filter(t => t.club!=='Bye') // filter out the Byes
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
        .filter(g => this.selectedTeams.includes(g.teamHome) || (!this.showOnlyHomeGames && this.selectedTeams.includes(g.teamAway))) // from the selected teams
        .filter(g => this.selectedRounds.length && this.selectedRounds.includes(g.round)) // from the selected rounds
        .sort((a, b) => a.diamond > b.diamond ? 1 : -1) // sorted by diamond
        .sort((a, b) => a.day < b.day ? 1 : -1) // sorted by day
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
    getDateFromRound(round, day) {
      const date = new Date(this.rounds.find(r => r.round === round).date)
      return (+date.getDate() + ((day==='Friday') ? -1 : 0)) + "-" + (+date.getMonth()+1) + "-" + date.getFullYear()
    },
    getTeamFromID(id) {
      return this.teams.find(t => t.team_id === id)
    },
    getClubFromTeamID(id) {
      const club = this.teams.find(t => t.team_id === id).club
      const clubObj = this.clubs.find(c => c.club === club)
      return clubObj
    },
    getRoundFromRound(round) {
      return this.rounds.find(r => r.round === round)
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
.select-rounds,
.select-divisions,
.select-teams {
  @apply bg-green-100 p-4 border border-green-800;
}
  button {
    @apply border p-2 bg-white my-2;
  }
  input, select {
    @apply p-2;
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
.selectOptions {
  @apply py-4 mx-auto text-center;
}
  th, td {
    text-align: left;
    padding: 2px 1rem;
  }
  th:nth-of-type(1), td:nth-of-type(1),
  th:nth-of-type(2), td:nth-of-type(2),
  th:last-of-type, td:last-of-type {
    text-align: center;
  }
  
  th:nth-of-type(3), td:nth-of-type(3) {
    text-align: right;
  }

  th:nth-of-type(6), td:nth-of-type(7),
  th:nth-of-type(6), td:nth-of-type(7) {
    width: 325px
  }

  th {
    @apply bg-green-200;
  }
  .shade-row {
    @apply bg-green-50;
  }
</style>
