<script>
  import headerLogo from "./logo.svg";

  function data() {
    return {
      beyonkLogo
    };
  }
</script>

<div class="logo">
  {@html headerLogo}
</div>


<style>
  :global(.logo) svg {
    width: 20vw; height: 8vh;
  }
</style>