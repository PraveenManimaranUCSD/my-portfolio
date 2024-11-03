<svelte:head>
    <title>Praveen Manimaran: Personal Portfolio</title>
</svelte:head>

<script>
    import projects from '$lib/projects.json';
    import Project from '$lib/Project.svelte';

    // Fetch the GitHub profile data
    let profilePromise = fetch("https://api.github.com/users/PraveenManimaranUCSD");
</script>

<h1>Praveen Manimaran</h1>
<p>Masters Student at UC San Diego</p>


<img src="/images/NEW_PIC.png" alt="Picture of me in San Francisco 2022" />

<!-- About Section -->
<p>Hey! I am Praveen Manimaran. I recently graduated from UC Santa Barbara with a degree in Statistics and Data Science. My journey into the world of data began with a fascination for statistics, but I soon discovered my true passion 
	lies in the dynamic field of data science and machine learning. I'm currently furthering my education and skills by pursuing a Master's degree in Data Science at UC San Diego, where I'm eager to explore the limitless possibilities 
	that data-driven insights offer. Feel free to explore my work, and if you'd like to connect or collaborate, don't hesitate to reach out. Let's explore the data-driven future together!</p>

<!-- Latest Projects Section -->
<h2>Latest Projects</h2>

<div class="projects highlights">
  {#each projects.slice(0, 3) as project}
    <Project data={project} hLevel={3}/>
  {/each}
</div>

<!-- GitHub Profile Data Section -->
<h2>GitHub Profile Data</h2>

{#await profilePromise}
  <p>Loading...</p>
{:then response}
  {#await response.json()}
    <p>Decoding...</p>
  {:then data}
    <p>Username: {data.login}</p>
    <p>Followers: {data.followers}</p>
    <p>Following: {data.following}</p>
    <p>Public Repositories: {data.public_repos}</p>
    <img src="{data.avatar_url}" alt="Profile picture" width="100" />
  {:catch error}
    <p class="error">Something went wrong while decoding: {error.message}</p>
  {/await}
{:catch error}
  <p class="error">Something went wrong with the fetch request: {error.message}</p>
{/await}
