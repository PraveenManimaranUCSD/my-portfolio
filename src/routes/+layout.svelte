<script>
    import { page } from '$app/stores';
    import '../style.css';
  
    let pages = [
      { url: './', title: 'Home' },
      { url: './projects', title: 'Projects' },
      { url: './resume', title: 'Resume' },
      { url: './contact', title: 'Contact' },
      { url: 'https://github.com/PraveenManimaranUCSD', title: 'GitHub' }
    ];
  </script>
  
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
  
  </style>
  