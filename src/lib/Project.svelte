<script>
  export let data = {};
  let p = data; // Temporary alias for testing
  export let hLevel = 2;
</script>

<article>
  <svelte:element this={'h' + hLevel}>{data.title}</svelte:element>
  <img src={data.image} alt={data.title} />
  <p>{data.description}</p>
  <div class="year">c. {data.year}</div>
</article>

<style>
  /* Add any specific styling for the component here */
  
  /* Constrain image size */
  img {
    max-width: 100%; /* Ensures the image does not exceed the container width */
    max-height: 200px; /* Adjust the height as necessary */
    object-fit: cover; /* Crops the image if necessary while maintaining aspect ratio */
  }

  article {
    margin-bottom: 1.5rem; /* Add some spacing between project articles */
  }

  .year {
    font-family: Baskerville, serif;
    font-style: italic;
    font-variant-numeric: oldstyle-nums;
    color: #555;
    margin-top: 8px;
  }
</style>
