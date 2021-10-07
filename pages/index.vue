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
      <li>[ ] Hook up content query</li>
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
    <p>
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
    <p>Rounds: {{ rounds }}</p>
    <p>Games: {{ games }}</p>
  </div>
</template>

<script>
import Club from "@/components/club.vue"
export default {
  components: {
    Club
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
      .sortBy('divisionNumber', 'asc') // alphabetical sort
      .fetch();
    console.log('teams: ', teams)
    const rounds = await $content("_rounds").fetch();
    console.log('rounds: ', rounds)
    const games = await $content("_games").fetch();
    console.log('games: ', games)

    return {
      clubs,
      divisions,
      teams,
      rounds,
      games
    };
  },
};
</script>
<style>
  @tailwind base;
  @tailwind components;
  @tailwind utilities;
</style>
