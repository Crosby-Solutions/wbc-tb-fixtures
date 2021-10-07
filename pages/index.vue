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
      <li>[ ] Input content</li>
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
    <p>Clubs: {{ clubs }}</p>
    <p>Divisions: {{ divisions }}</p>
    <p>Teams: {{ teams }}</p>
    <p>Rounds: {{ rounds }}</p>
    <p>Games: {{ games }}</p>
  </div>
</template>

<script>
export default {
   async asyncData({ $content }) {
    const clubs = await $content("clubs").fetch();
    const divisions = await $content("divisions").fetch();
    const teams = await $content("teams").fetch();
    const rounds = await $content("rounds").fetch();
    const games = await $content("games").fetch();

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
