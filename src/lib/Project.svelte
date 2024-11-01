<script>
    export let data = {};
    let p = data; // Temporary alias for testing
  </script>
  
  <article>
    <h2>{p.title}</h2>
    <img src="{p.image}" alt="{p.title}" />
    <p>{p.description}</p>
  </article>
  
  <style>
    /* Add any specific styling for the component here */
  </style>
  