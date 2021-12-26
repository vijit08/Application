<script>
  import Button from "./Button.svelte";
  import EndTestModal from "./EndTestModal.svelte";
  import { question } from "../data-store.js";
  import { correctans } from "../data-store.js";
  import { allques } from "../data-store.js";
  import { attempted } from "../data-store.js";
  import { currentcorrect } from "../data-store.js";
  import { selectedanswer } from "../data-store.js";
  import { createEventDispatcher } from "svelte";
  import { currentitem } from "../data-store.js";
  import { counter } from "../data-store.js";
  import { disable1 } from "../data-store.js";
  import { disable2 } from "../data-store.js";
  import { list } from "../data-store.js";
  import {correctques} from "../data-store.js"
  import {isopen} from "../data-store.js"
  import {timetaken} from "../data-store.js";
  
  let dispatch = createEventDispatcher();
  var count = 0;
  let seconds = 12;
  var secs=0;
  let minutes = 60;
  var timer;
  let correct = [];
  let questions = [];
  let correctall = [];
  let Main_Array = [];
  var questioncorrect=[];
  var iscorrect = [];
  var Selected = [];
  var currentselect = [];

  //=========================================MAIN LOGIC FUNCTION================================================
  function toggleattempt(z, event) {
    Selected.push(event);

    for (let i = 0; i < correct.length; i++) {
      let x = JSON.parse(correct[i].content_text).question;
    }

    selectedanswer.update((its) => {
      return [...Selected];
    });

    selectedanswer.subscribe((items) => {
      let t = items.filter((c, index) => {
        return items.indexOf(c) === index;
      });
      currentselect = [...t];
    });

    //==========================================TO CHECK CURRENT ANSWER IS RIGHT OR NOT==========================
    if ($correctans.includes(event)) {
      iscorrect.push(event);
      questioncorrect.push(z);

      correctques.update(items=>{
        return [...questioncorrect];
      })

      currentcorrect.update((its) => {
        return [...iscorrect];
      });
      currentcorrect.subscribe((item) => {});
    }

    //===============================================================================================================
    Main_Array.push(z);

    //==================ATTEMPTED LOGIC==================================
    attempted.update((its) => {
      return [...Main_Array];
    });
  }

  attempted.subscribe((items) => {
    let t = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });

    count = t.length;
  });
  

  //===========================CORRECT ANSWERS OF ALL QUESTIONS LOGIC=============================================
  question.subscribe((items) => {
    correct = items;
  });

  for (let i = 0; i < correct.length; i++) {
    let x = JSON.parse(correct[i].content_text).question;
    questions.push(x);

    if (JSON.parse(correct[i].content_text).answers[0].is_correct == 1) {
      correctall.push(JSON.parse(correct[i].content_text).answers[0].answer);
    }
    if (JSON.parse(correct[i].content_text).answers[1].is_correct == 1) {
      correctall.push(JSON.parse(correct[i].content_text).answers[1].answer);
    }

    if (JSON.parse(correct[i].content_text).answers[2].is_correct == 1) {
      correctall.push(JSON.parse(correct[i].content_text).answers[2].answer);
    }
    if (JSON.parse(correct[i].content_text).answers[3].is_correct == 1) {
      correctall.push(JSON.parse(correct[i].content_text).answers[3].answer);
    }
  }

  correctans.update((items) => {
    return [...correctall];
  });

  //=====================================================================================================================

  //========================================ALL QUESTIONS LOGIC========================================================
  allques.update((items) => {
    return [...questions];
  });
  //===================================================================================================================

  //=====================================TIMER LOGIC========================================================
  function stop() {
   clearInterval(timer);
  
  
 }
  
 var timer = setInterval(() => {
        seconds--;
        secs+=1;
        timetaken.set(secs);
        
        if (minutes>0&&seconds == 0) {
          minutes--;
          seconds = 60;
        }
          
          if (minutes == 0) {
            minutes = 0;
          }

          if (minutes==0&&seconds == 0) {
            stop();
            window.alert('TIME IS UP');

          }
        
        
  }, 1000);
 
  //=============================================================================================================

  //==========================================NEXT AND PREVIOUS BUTTON LOGIC====================================
  function next() {
    disable1.set(false);
    disable2.set(false);

    if ($counter == 9) {
      disable1.set(true);
    }
    dispatch("n");
  }

  function prev() {
    disable1.set(false);
    if ($counter == 1) {
      disable1.set(false);
      disable2.set(true);
    }
    
    dispatch("p");
  }
  //===============================================================================================================

  function end() {
    //END TEST EVENT
    dispatch("e");
    stop();
   
  }

  function lis() {
    //OPEN LIST EVENT
    dispatch("l");
  }

</script>

<section class="section" class:shift={$list}  >
  {#each $question as dataItem, i (dataItem)}
    {#if i == $currentitem}
      <div class="main-question" >
        <div class="number">{i+1}.</div>
        <div class="box" >{JSON.parse(dataItem.content_text).question}</div>
      </div> 
      
      <div class="question-section" class:top-shift={$list&&i==2||i==2}  >
        {#each JSON.parse(dataItem.content_text).answers as ans, index (ans)}
        
        <label for="ans{index}" id="option{index}" class="items">{index+1}.
          <input type="radio" name="ans" id="ans{index}" is_correct={ans.is_correct} value={ans.answer} class="input-items" on:click={toggleattempt(JSON.parse(dataItem.content_text).question,ans.answer)} checked={currentselect.includes(ans.answer) ? true : false}/> {@html ans.answer}
          </label>
        {/each}
      </div>

      <div class="bottom-nav">
        <div class="timer">{minutes}:{seconds.toLocaleString(undefined,{minimumIntegerDigits: 2})}</div>
        
        <Button style=button margin=btn-bottom type="button" id="list" name="List-btn" caption="List" on:click={lis}/>
        <Button style=button margin=btn-bottom type="button" id="prev" name="Prev-btn"caption="Previous" disabled={$disable2} on:click={prev}/>
        
        <div class="numbering">
          <b>{i + 1} of 11</b>
        </div>
        <Button style=button margin=btn-bottom type="button" id="next" name="Next-btn" caption="Next" disabled={$disable1} on:click={next}/>
        <Button style=button margin=btn-bottom type="button" id="end" name="End-btn" caption="End Test" on:click on:click={end}/>

        {#if $isopen}
          <EndTestModal />
        {/if}
      </div>
    {/if}
  {/each}

</section>

<style>

  .main-question {
    position: relative;
    left: 6rem;
    font-weight: 700;
    margin-top: 10px;
    display: inline-flex;
  }

  .number {

    font-weight: 700;
    margin-top: 7px;
    margin-right: 10px;
    display: inline-block;
  }

 .box {

    display: inline-flex;
    height: 5rem;
    width: 85%;
    font-weight: 700;
    font-size: 17px;
    margin-top: 7px;
    margin-bottom: 20px;
    line-height: 20px;
    text-align: left;
  
  }

  .question-section {

    position: relative;
    display: flex;
    flex-direction: column;
    height: 200px;
    bottom: 2rem;
    font-size: 12px;
    font-size: 10px;
    left: 5.4rem;
  }

  .items {
    margin-bottom: 10px;
    font-size: 16px;
    padding: 12px;
    display: inline-flex;
  }

  .bottom-nav {
    position: fixed;
    height: 5rem;
    width: 99.9%;
    bottom: 0;
    left: 0;
    background-image: linear-gradient(to right, #FF0000 , #FFFF00);
    box-shadow: 10px 6px 12px rgba(0, 0, 0, 0.26);
    display: flex;
    top: 38rem;
    height: 4rem;
    align-items: center;
    justify-content: center;
  }

  .section {
    top: 6rem;
    height: 16rem;
    position: fixed;
  }

  .input-items {
    margin-left: 18px;
    margin-right: 18px;
  }

  .numbering {
    margin-left: 20px;
  }

  .timer {
    margin-left: 20px;
    font-weight: 700;
  }

  .shift {
    position: fixed;
    left: 20rem;
    right: 5rem;
  
  }

  .top-shift{
    top: 0;
  }
  
</style>

