<script>
  import { question } from "../data-store.js";
  import { current } from "../data-store.js";
  import { createEventDispatcher } from "svelte";
  import { disable1, disable2 } from "../data-store.js";
  import Button from "./Button.svelte";
  import Nav from "./Nav.svelte";
  import {selectedanswer} from "../data-store.js";
  import App from "../App.svelte";
  import {rev} from "../data-store.js";
  import {startpage} from "../data-store.js";
  import {isconfirm} from "../data-store.js";
  

  let dispatch = createEventDispatcher();
  var explain = true;
  var currentselect=[];
  var home=false;
  let Heading;
  var exp;

  selectedanswer.subscribe((items) => {
    let t = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });
    currentselect = [...t];
  });
  
  
  if ($current >0) {
    disable2.set(false);
    
  }
  if($current==0){
    disable1.set(false);
  }

  if($current>=10){
    disable1.set(true);
  }
  
  function next() {
    $current += 1;

    disable1.set(false);
    disable2.set(false);

    if ($current == 10) {
      disable1.set(true);
    }
  }

  function prev() {
    $current -= 1;
    disable2.set(false);
    disable1.set(false);
    
    if ($current == 0) {
      disable1.set(false);
      disable2.set(true);
    }
  }

  function dash() {
    home=true;
    rev.set(false);
    startpage.set(true);
    isconfirm.set(false);
    location.reload();
  
  }

</script>

<header>
  <Nav Heading={"uCertify Test Review"}/>
</header>

<div class="review">
  <section class="section">
    {#each $question as dataItem, i (dataItem)}
      {#if i == $current}
      <div class="main-question" >
        <div class="number">{i+1}.</div>
        <div class="box" >{JSON.parse(dataItem.content_text).question}</div>
      </div> 
        <div class="question-section" class:top-shift={i==2} >
          {#each JSON.parse(dataItem.content_text).answers as ans, index (ans)}
            <label for="ans{index}" id="option{index}"  class="radio" class:bold={ans.is_correct==1} class:not-bold={currentselect.includes(ans.answer)&&ans.is_correct==0}>{index+1}
              
              <input class="radio_input" type="radio" name="ans" id="ans{index}" is_correct={ans.is_correct} value={ans.answer} checked={ans.answer&&ans.is_correct==1?true : false} disabled/>
              
              <div class:radio_radio={ans.is_correct == 1||ans.answer} class:wrong={currentselect.includes(ans.answer)&&ans.is_correct == 0}/>{@html ans.answer}
            </label>
   
          
          {#if explain}
            <div class="explanation" class:hide={ans.is_correct==0}>
              {#if ans.is_correct==1}
              {@html (JSON.stringify(JSON.parse(dataItem.content_text).explanation).replace('"'," ").replace(/(?:\\[rn]|[\r\n]+)+/g,"").replace("Answer",index+1).replace(/Answer option.*/, '').replace(/Answer.*/,'') )}
              
              {/if}
            </div>
          {/if}   {/each}    
        </div>
        <div class="bottom-nav">
          <Button style=button margin=btn-bottom type="button" id="prev" name="Prev-btn" caption="Previous" disabled={$disable2} on:click={prev}/>
          <div class="numbering">
            <b>{i + 1} of 11</b>
          </div>
          <Button style=button margin=btn-bottom type="button" id="next" name="Next-btn" caption="Next" disabled={$disable1} on:click={next}/>
          <Button style=button margin=btn-bottom type="button"  id="dash" name="DashBoard-btn" caption="DashBoard" on:click={dash}/>
        </div>
      {/if}
    {/each}
  </section>

  {#if home}
<App/>
  {/if}


 
</div>

<style>
  .review {
    position: fixed;
    top: 81px;
    margin-left: 0;
    width: 100%;
    height: 100vh;
    background: #fff;
    border-radius: 5px;
    z-index: 100;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
    overflow: hidden;
  }

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
    left: 4.8rem;
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


  .numbering {
    margin-left: 20px;
  }

  .explanation {
    
    border-radius: 5px;
    background-color: #ADD8E6;
    width: 70%;
    margin-left: 20px;
    margin-top: 0;
    margin-right: 20px;
    margin-bottom: 20px;
    padding: 20px;
    box-shadow: 0px 6px 12px rgba(0, 0, 0, 0.26);
    font-weight: 700;
    font-size: 14px;
   
  }

  .radio {
    display: inline-flex;
    align-items: center;
    cursor: center;
    margin-left: 18px;
    margin-right: 18px;
    margin-bottom: 20px;
    font-size: 18px;
    
  }

  .radio_input {
    margin-left: 4rem;
    display: none;
  
  }

  .radio_radio {
    width: 0.9em;
    height: 0.9em;
    background-color: #f5f5f0;
    border: 1px solid #000000;
    border-radius: 50%;
    margin-right: 10px;
    box-sizing: border-box;
    padding: 2px;
    margin-left: 20px;
    
  }

  .radio_radio:after {
    content: "";
    width: 100%;
    height: 100%;
    display: block;
    background: #40BE59;
    border-radius: 50%;
    transform: scale(0);
  }

  .radio_input:checked + .radio_radio::after  {
    transform: scale(1);
  }

  .wrong {
    width: 0.9em;
    height: 0.9em;
    background-color: #f5f5f0;
    border: 1px solid #000000;
    border-radius: 50%;
    margin-right: 10px;
    box-sizing: border-box;
    padding: 2px;
  }

  .wrong:after {
    content: "";
    width: 100%;
    height: 100%;
    display: block;
    background: #FF0000;
    border-radius: 50%;
    transform: scale(1);
  }

  .bold {
    font-weight: 700;
    color:#40BE59;
  }

  .not-bold {
    font-weight: 700;
    color:#FF0000;
  }

  .hide {
    display: none;
  }

  .top-shift{
    top: 0;
  }
</style>
