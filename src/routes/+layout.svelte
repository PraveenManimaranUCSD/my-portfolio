<script>
    import { page } from '$app/stores';
    import '../style.css';
  
    // Define safe reference to localStorage
  let localStorage = globalThis.localStorage ?? {};

    // Retrieve saved color scheme or default to 'light dark'
    let colorScheme = localStorage.colorScheme ?? 'light dark';

    // Reference to <html> element
    let root = globalThis?.document?.documentElement;

    // Reactively apply color scheme to the <html> element
    $: root?.style.setProperty('color-scheme', colorScheme);

    // Reactively save color scheme to localStorage whenever it changes
    $: localStorage.colorScheme = colorScheme;


    let pages = [
      { url: './', title: 'Home' },
      { url: './projects', title: 'Projects' },
      { url: './resume', title: 'Resume' },
      { url: './contact', title: 'Contact' },
      { url: './meta', title: 'Meta' },
      { url: 'https://github.com/PraveenManimaranUCSD', title: 'GitHub' }
    ];

  </script>
  

<label class="color-scheme">
    Theme:
    <select id="color-scheme-selector" bind:value={colorScheme}>
    <option value="light dark">Automatic</option>
    <option value="light">Light</option>
    <option value="dark">Dark</option>
    </select>
</label>

    
  <nav>
    {#each pages as p}
      <a 
        href="{p.url}"
        class:current={'.' + $page.route.id === p.url}
        target={p.url.startsWith("http") ? "_blank" : null}
      >
        {p.title}
      </a>
    {/each}
  </nav>
  
  <slot />
  
  <style>
    nav {
      display: flex;
      gap: 1em;
      margin-bottom: 2em;
      border-bottom-width: 1px;
      border-bottom-style: solid;
      border-bottom-color: var(--border-color);
      padding-bottom: 0.1em;
    }
  
    nav a {
      flex: 1;
      text-align: center;
      padding: 0.5em;
      text-decoration: none;
      color: inherit;
    }
  
    .current {
      font-weight: bold;
      color: #007bff; 
      border-bottom-width: 0.4em;
      border-bottom-style: solid;
      border-bottom-color: var(--border-color);
      padding-bottom: 0.1em;
    }
  
    /* Theme switcher styles */
    .color-scheme {
    position: absolute;
    top: 1rem;
    right: 1rem;
    font-size: 80%; 
    font-family: inherit; 
    }

    select {
    padding: 0.5em;
    margin-left: 0.5em;
    font-family: inherit; 
    }

  </style>
  