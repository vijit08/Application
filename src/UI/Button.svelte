<script>
  export let type = "button";
  export let caption;
  export let href = null;
  export let mode = null;
  export let color = null;
  export let style = null;
  export let disabled = false;
  export let id=null;
  export let name=null;
  export let margin=null;
  
</script>

{#if href}
  <a class="link" {href}>{caption}</a>
{:else}
  <button class="{mode} {color} {style} {margin}" {type} {id} {name} {disabled} on:click>{caption}</button>
{/if}

<style>

  .btn {
    position: relative;
    top: 20rem;
    left: 40rem;
  }

  .btn-bottom {
    margin-left:2rem;
  }
  
  .button,
  .link {
   
    font: inherit;
    border: 1px solid #cf0056;
    background: #cf0056;
    padding: 10px 15px;
    color: white;
    border-radius: 5px;
    box-shadow: 5px 5px 5px rgba(0, 0, 0, 0.26);
    cursor: pointer;
    text-decoration: none;
    font-weight: 700;
  }

  .button:focus {
    outline: none;
  }

  .button:hover,
  .button:active,
  .link:hover,
  .link:active {
    font: inherit;
    border: 1px solid #cf0056;
    background: pink;
    padding: 10px 15px;
    color: black;
    border-radius: 5px;
    box-shadow: 5px 5px 5px rgba(0, 0, 0, 0.26);
    cursor: pointer;
    text-decoration: none;
    font-weight: 700;
  }

  .button:disabled,
  .button:disabled:hover,
  .button:disabled:active {
    background: #ccc;
    border-color: #ccc;
    color: #959595;
    box-shadow: none;
    cursor: not-allowed;
  }

  .success {
    display: none;
  }
  
  .outline {
    background: transparent;
    color: #cf0056;
    box-shadow: none;
  }

  .outline:hover,
  .outline:active {
    background: #ffc7de;
    box-shadow: none;
  }

  .outline:disabled,
  .outline:disabled:hover,
  .outline:disabled:active {
    background: transparent;
    color: #ccc;
  }

  .outline.success {
    border-color: #01a129;
    color: #01a129;
  }

  .outline.success:hover,
  .outline.success:active {
    background: #c2ffd1;
  }
</style>
