<script>
    export let data = {};
    let p = data; // Temporary alias for testing
    export let hLevel = 2;
  </script>
  
  <article>
    <h2>{p.title}</h2>
    <img src="{p.image}" alt="{p.title}" />
    <p>{p.description}</p>
  </article>
  
  <style>
    /* Add any specific styling for the component here */
  </style>
  

<article>
    <svelte:element this={'h' + hLevel}>{data.title}</svelte:element>
    <img src="{data.image}" alt="{data.title}" />
    <p>{data.description}</p>
  </article>
  