<script>
  import "./prismtheme.css"
  import { blur } from 'svelte/transition';
   
  import Prism from 'prismjs';
  import "prismjs/components/prism-sql.js"
  export let code = '';
</script>

<pre in:blur>
  <code class="language-sql">
    {@html Prism.highlight(code, Prism.languages['sql'])}
  </code>
</pre>


<style>
  :root {
      --scrollbar-track-color: transparent;
      --scrollbar-color: rgba(0,0,0,.2);
      --scrollbar-active-color: rgba(0,0,0,.4);
      --scrollbar-size: .75rem;
      --scrollbar-minlength: 1.5rem; /* Minimum length of scrollbar thumb (width of horizontal, height of vertical) */
    }

    pre {
      margin: 0 0 0 0; /* Makes the block jump around on transition */  
      font-size:12px;
      display: block;
      scrollbar-width: thin; 
      scrollbar-color: var(--scrollbar-color) var(--scrollbar-track-color);
    }   

    pre::-webkit-scrollbar {
      height: var(--scrollbar-size);
      width: var(--scrollbar-size);
    }
    pre::-webkit-scrollbar-track {
      background-color: var(--scrollbar-track-color);
    }
    pre::-webkit-scrollbar-thumb {
      background-color: var(--scrollbar-color);
      border-radius: 7px;
      background-clip: padding-box;
    }
    pre::-webkit-scrollbar-thumb:hover {
      background-color: var(--scrollbar-active-color);
    }
    pre::-webkit-scrollbar-thumb:vertical {
      min-height: var(--scrollbar-minlength);
      border: 3px solid transparent;
    }
    pre::-webkit-scrollbar-thumb:horizontal {
      min-width: var(--scrollbar-minlength);
      border: 3px solid transparent;
    }
    code {
      display: inline-block;
    }
</style>