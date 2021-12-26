<script>
    export let Heading;
</script>

<div class="nav_bar">
  <div class="img">
    <img src="/images/logo.png" alt="" height="38rem" />
  </div>
  <div class="heading">
    <h1>{Heading}</h1>
  </div>
</div>

<style>

  .nav_bar {
    position: fixed;
    top: 0px;
    left: 0px;
    height: 5rem;
    width: 99.9%;
    display: flex;
    align-items: center;
    background-image: linear-gradient(to right, #FF0000 ,  #FFFF00);
    box-shadow: 0px 6px 12px rgba(0, 0, 0, 0.26);
  
  }

  .img {
    
    width: 20%;
    margin-left: 20px;
  }

  .heading {
    position: relative;
    left: 17rem;
    
  }

</style>
