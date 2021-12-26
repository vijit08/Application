<script>
  import { createEventDispatcher } from "svelte";
  import { attempted } from "../data-store.js";
  import { unattempted } from "../data-store.js";
  import Button from "./Button.svelte";
  import { question } from "../data-store.js";
  import { allques } from "../data-store.js";
  import { currentcorrect } from "../data-store.js";
  import {current} from "../data-store.js";

  let Raw_Arr = [];
  let count = 0;
  var Raw_Attempt = [];
  var Ques_Str = [];
  var Raw_Unattempted = [];
  var Current_Correct = [];
  const dispatch = createEventDispatcher();
 
  

  allques.subscribe((its) => {
    Ques_Str = [...Ques_Str, ...its];
    
  });

  question.subscribe((items) => {
    Raw_Arr = items;
    
  });

  attempted.subscribe((items) => {
    let t = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });

    Raw_Attempt = [...Raw_Attempt, ...t];
    count = t.length;
  });

  for (let i of Raw_Attempt) {
    let p = Ques_Str.indexOf(i);

    delete Ques_Str[p];
  }

  for (let b of Ques_Str) {
    if (b != undefined) {
      Raw_Unattempted.push(b);
    }
  }

  unattempted.update((its) => {
    return [...its, ...Raw_Unattempted];
  });
  
  
  currentcorrect.subscribe((items) => {
    let t = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });

    Current_Correct = [...Current_Correct, ...t];
  });

  $: final_attempt = count;
  $: final_unattempt = Raw_Unattempted.length;


  function closeModal() {
    dispatch("cancel");

  
  }

  function confirmModal() {
    dispatch("confirm");
    current.set(0);
    
  }

</script>

<div class="modal-backdrop" on:click={closeModal} />
<div class="modal">
  <h2 class="heading">Attempted:{final_attempt}</h2>
  <h2 class="heading">UnAttempted:{final_unattempt}</h2>
  <h2 class="heading">Do You Want To End Test?</h2>

  

  <div class="footer">
    <slot name="footer">
      <div class="btn-primary">
      <Button type="button" style="button" id="close" name="Close" caption="Close" on:click={closeModal}/>
    </div>
      <div class="btn-primary">
      <Button  type="button" style="button" id="confirm" name="Confirm" caption="Confirm" on:click={confirmModal}/>
    </div>
    </slot>
  </div>
</div>

<style>

  .btn-primary {
  margin-right: 20px;
  
  }

  .modal-backdrop {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100vh;
    background: rgba(0, 0, 0, 0.75);
    z-index: 10;
  }

  .modal {
    border:5px solid #00b3b3;
    position: fixed;
    top: 18vh;
    left: 20%;
    width: 60%;
    height: 15rem;
    max-height: 80vh;
    background: #ffff;
    border-radius: 5px;
    z-index: 100;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  }


  .footer {
    position: fixed;
    top: 37vh;
    left: 40%;
    width: 23%;
    display: flex;
    justify-content: center;
    
  }

  .heading {
    text-align: center;
    font-size: 15px;
    font-weight: 700;
  }

  
</style>
