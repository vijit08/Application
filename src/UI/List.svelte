<script>
  import { createEventDispatcher } from "svelte";
  import { attempted, counter, disable1, disable2 } from "../data-store.js";
  import { unattempted } from "../data-store.js";
  import { currentitem } from "../data-store.js";
  import { question } from "../data-store.js";
  import { allques } from "../data-store.js";

  let allitems = true;
  let Remove_Duplicate;
  var count;
  var Raw_Attempt = [];
  var Raw_Unattempted=[];
  var Raw_Ques = [];
  var final = [];
  var dummy = [];
  var showatt = false;
  var showunatt = false;
  var clicked = false;
  const dispatch = createEventDispatcher();

  function closeModal() {
    dispatch("cancel");

    counter.update((its) => {
      return $currentitem;
    });
    
    if ($counter == 10) {
      disable1.set(true);
    }
  }

  allques.subscribe((its) => {
    let Remove_Duplicate = its.filter((c, index) => {
      return its.indexOf(c) === index;
    });

    Raw_Ques = [...Remove_Duplicate];
  });

  attempted.subscribe((items) => {
    let Remove_Duplicate = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });

    Raw_Attempt = [...Raw_Attempt, ...Remove_Duplicate];
    count = Remove_Duplicate.length;
  });

  for (let i of Raw_Attempt) {
    let p = $allques.indexOf(i);

    delete $allques[p];
  }
  
  for (let b of $allques) {
    if (b != undefined) {
      Raw_Unattempted.push(b);
    }
  }

  unattempted.update((its) => {
    return [...Raw_Unattempted];
  });

  unattempted.subscribe((items) => {
    let e = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });

    final = [...final, ...e];
  });

  $: Remove_Duplicate = count;
  $: Raw_Unattempted = Raw_Unattempted.length;

  function showattempt() {
    showatt = true;
    showunatt = !showatt;
    allitems = false;
  }

  function showunattempt() {
    showunatt = !showunatt;
    showatt = false;
    allitems = false;
  }

  function showitems() {
    allitems = true;
    showatt = false;
    showunatt = false;
  }

  function goto(x, event) {
    clicked = true;
    question.subscribe((ies) => {
      var z = ies.indexOf(event);

      currentitem.set(z);
      if ($currentitem == 0 ) {
        counter.set(0);
        disable2.set(true);
        disable1.set(false);
      }

      if ($currentitem > 0) {
        disable2.set(false);
        disable1.set(false);
      }
    });
  }

  question.subscribe((item) => {
    for (let y = 0; y < item.length; y++) {
      let x = JSON.parse(item[y].content_text).question;
      dummy.push(x);
    }
  });

  function gotoa(y, event) {
    let p = dummy.indexOf(event);

    currentitem.update((its) => {
      return p;
    });

    if ($currentitem == 0||$currentitem==1) {
      disable2.set(true);
      disable1.set(false);
    }

    if ($currentitem > 0) {
      disable2.set(false);
      disable1.set(false);
    }
  }
</script>

<div class="modal-backdrop" on:click={closeModal} />
<div class="modal">
  <div class="heads">
    <h2 class="sub-heading" on:click={showitems} class:change={allitems && !showatt && !showunatt}>All Items:{dummy.length}</h2>
    <h2 class="sub-heading" on:click={showattempt} class:change={!allitems && showatt && !showunatt}>Attempted:{Remove_Duplicate}</h2>
    <h2  class="sub-heading" on:click={showunattempt} class:change={!allitems && !showatt && showunatt}>UnAttempted:{Raw_Unattempted}</h2>
  </div>

  {#if allitems}
    <div>
      {#each $question as items, v (items)}
        <div class="all-items" on:click={goto(v, items)}>{v + 1}.{JSON.parse(items.content_text).question}</div>
      {/each}
    </div>
  {/if}

  {#if showatt}
    <div class="container-1">
      <div class="all-items" class:hide={Raw_Attempt.length > 0}>No Questions Attempted</div>
      {#each Raw_Attempt as dataItem, i (dataItem)}
        {#if Raw_Attempt.length > 0}
          <div class="all-items"  on:click={gotoa(i, dataItem)}>{i + 1}.{dataItem}</div>
        {/if}
      {/each}
    </div>
  {/if}

  {#if showunatt}
    <div>
      {#if Raw_Unattempted==0}
        <div class="all-items">ALL ATTEMPTED</div>{/if}
      {#each $unattempted as dos, j (dos)}
        <div class="all-items" on:click={gotoa(j, dos)}>{j + 1}.{dos}</div>
        
      {/each}
    </div>
  {/if}
</div>

<style>

  .modal-backdrop {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100vh;
    background: transparent;
    z-index: 10;
  }

  .modal {
    position: fixed;
    top: 81px;
    width: 30%;
    height: 32.7rem;
    background: #fff;
    border-radius: 5px;
    z-index: 100;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
    overflow-y: scroll;
  }

  .container-1 {
    display: flex;
    flex-direction: column;
    flex-wrap: wrap;
  }

  .all-items {
   
    width: 90%;
    margin-bottom: 10px;
    margin-top: 12px;
    margin-left: 10px;
    margin-right: 10px;
    text-align: center;
    padding: 5px;
    font-size: 15px;
    background: #ADD8E6;
    box-shadow: 0px 6px 12px rgba(0, 0, 0, 0.26);
  }

  .sub-heading {
    text-align: center;
    font-size: 14px;
    margin-left: 8px;
    margin-right: 8px;
    background: #FFC0CB;
    height: 2rem;
    padding: 5px;
  }

  .heads {
    margin-left: 20px;
    display: flex;
    flex-direction: row;
    
  }

  .hide {
    display: none;
  }

  .change {
    color: #ff0000;
    font-weight: 600;
  }
  
  .sub-heading:hover {
    background-color:pink;
    box-shadow: 0px 6px 12px rgba(0, 0, 0, 0.26);
    transition: 0.3s;
  }

</style>
