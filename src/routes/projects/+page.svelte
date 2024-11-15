<script>
  import Pie from '$lib/Pie.svelte';
  import projects from '$lib/projects.json';
  import Project from '$lib/Project.svelte';
  import * as d3 from 'd3';

  // Group projects by year and count the number of projects per year
  const rolledData = d3.rollups(
      projects,
      v => v.length,
      d => d.year
  );

  // Format the grouped data to match the expected structure
  const pieData = rolledData.map(([year, count]) => {
      return { value: count, label: year };
  });

  // Declare a search query variable
  let query = '';

  // Reactive statement to filter projects based on the query
  let filteredProjects;
  $: filteredProjects = projects.filter((project) => {
      let values = Object.values(project).join('\n').toLowerCase();
      return values.includes(query.toLowerCase());
  });

  // Variable to hold the selected wedge index from Pie.svelte
  let selectedYearIndex = -1;

  // Reactive variable to hold the selected year based on selectedYearIndex
  let selectedYear;
  $: selectedYear = selectedYearIndex > -1 ? pieData[selectedYearIndex].label : null;

  // New filtered array based on selected year
  let filteredByYear;
  $: filteredByYear = selectedYear 
      ? filteredProjects.filter(project => project.year === selectedYear)
      : filteredProjects;
</script>

<!-- Search Input Field -->
<input
  type="search"
  bind:value="{query}"
  aria-label="Search projects"
  placeholder="🔍 Search projects…"
/>

<!-- Display the pie chart with project data by year and bind selected index -->
<Pie data="{pieData}" bind:selectedIndex="{selectedYearIndex}" />

<!-- Display the total number of projects found -->
<h1>{filteredByYear.length} Projects</h1>

<!-- Display each filtered project -->
<div class="projects">
  {#each filteredByYear as p}
      <Project data={p} />
  {/each}
</div>

<style>
  .projects {
      display: grid;
      gap: 2em;
      grid-template-columns: repeat(auto-fill, minmax(250px, 1fr)); /* Adjusts layout for multiple projects */
  }

  /* Additional styling for the search input */
  input[type="search"] {
      width: 100%;
      padding: 0.5em;
      margin-bottom: 1em;
      font-size: 1rem;
  }
</style>
