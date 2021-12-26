<script>

  import { question, unattempted } from "../data-store.js";
  import { attempted } from "../data-store.js";
  import { allques } from "../data-store.js";
  import { currentcorrect } from "../data-store.js";
  import { allincorrect } from "../data-store.js";
  import { current } from "../data-store.js";
  import { selectedanswer } from "../data-store.js";
  import { disable1,disable2 } from "../data-store.js";
  import {correctques} from "../data-store.js";
  import Nav from "./Nav.svelte";
  import {timetaken} from "../data-store.js";
  import Review from "./Review.svelte";

  let Heading;
  var minutes=0;
  var Raw_Unattempted;
  var result = 0.0;
  var showall=false;
  var showcorrect=false;
  var showincorrect=false;
  var count = 0;
  var dummyarray = [];
  var correctlength;
  var review = false;
  var currentselect = [];
  var incorrect = [];
  var Current_Correct=[];
  var showunattempt=false;
  var questioncorrect=[];

  if($timetaken>60){
    minutes++;

  }

 

  attempted.subscribe((items) => {
    let t = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });

    dummyarray = [...t];
    count = t.length;
  });

  unattempted.subscribe((items) => {
    let t = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });

    Raw_Unattempted = t.length;
  });

  currentcorrect.subscribe((items) => {
    let t = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });

    Current_Correct=[...t];
    correctlength = t.length;
  });

  $: correctlength = correctlength;

  $: result = Math.round((correctlength / 11) * 100);

  function r(x) {
    review = true;
    $: current.update((its) => {
      return x;
    });

    if (x == 0) {
      $disable2 = true;
   
    }

    if(x>0){
      $disable1.set(false);
    }
   
    
  }

  selectedanswer.subscribe((items) => {
    let t = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });
    currentselect = [...t];
  });

  allincorrect.subscribe((items) => {
    let t = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });

    incorrect = [...t];
  });


 correctques.subscribe((items) => {
    let t = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });
    questioncorrect = [...t];
  });


 
</script>

<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">


<header>
    <Nav Heading={"uCertify Test Result"}/>
  </header>

<div class="container">
  
  <div class="items" on:click={()=>{showall=true;showcorrect=true;showincorrect=false;showunattempt=false;}} >
    <b class="result">
      <i class="fa fa-bar-chart result"></i>
      {result}%
    </b><br>
    Result</div>

    <div class="items" on:click={()=>{showall=true;showcorrect=false;showincorrect=false;showunattempt=false;}} >
      <b class="all-items">
        <i class="fa fa-bars all-items">{$allques.length}</i>
        </b><br>
        All Items</div>

    <div class="items" on:click={()=>{showcorrect=true;showincorrect=false;showall=false;showunattempt=false;}}>
      <b class="correct">
        <i class="fa fa-check correct"></i>
        {correctlength}
      </b><br>
      Correct</div>

    <div class="items" on:click={()=>{showincorrect=true;showcorrect=false;showall=false;showunattempt=false;}} >
      <b class="incorrect">
        <i class="fa fa-close incorrect"></i>
        {count - correctlength}
      </b><br>
        Incorrect</div>

    <div class="items" on:click={()=>{showunattempt=true;showcorrect=false;showincorrect=false;showall=false;}}>
      <b class="unattempt">
        <i class="fa fa-eye-slash unattempt"></i>
        {11-count}
      </b><br>
      Unattempted</div>
  
  <br />
  
</div>
<p class="time">Time Taken: {minutes}min :{$timetaken}sec</p>

<div class="table">


  <table >
    <tr>
      <th class="first">Index No</th>
      <th class="second">Questions</th>
      <th class="third">Answers</th>
    </tr>

    {#each $question as dataItem, i (dataItem)}
      <tr class:hidecorrect={showcorrect&&(!questioncorrect.includes(JSON.parse(dataItem.content_text).question)||!dummyarray.includes(JSON.parse(dataItem.content_text).question))} class:hideincorrect={showincorrect&&(questioncorrect.includes(JSON.parse(dataItem.content_text).question)||!dummyarray.includes(JSON.parse(dataItem.content_text).question))}
       class:show={showall&&(questioncorrect.includes(JSON.parse(dataItem.content_text).question)||!questioncorrect.includes(JSON.parse(dataItem.content_text).question)||!dummyarray.includes(JSON.parse(dataItem.content_text).question)||!dummyarray.includes(JSON.parse(dataItem.content_text).question))} 
       class:un={showunattempt&&(dummyarray.includes(JSON.parse(dataItem.content_text).question))}
       >
       

       <td class="center">{i + 1}</td>
        
        <td id="questions" on:click={r(i)}>{JSON.parse(dataItem.content_text).question}</td>

        <td>
          <div class="center">
            {#each JSON.parse(dataItem.content_text).answers as ans, index (ans)}
              <span is_correct={ans.is_correct} class="dot" class:success={(currentselect.includes(ans.answer) && ans.is_correct == 1) || ans.is_correct == 1}
                class:unsuccess={currentselect.includes(ans.answer) && ans.is_correct == 0}
                id="ans{index}"
                value={ans.answer}
              >
               <i>{index + 1}</i> </span>

            {/each}
            {#if !dummyarray.includes(JSON.parse(dataItem.content_text).question)}
            <div class="tooltip"><i class="fa fa-eye-slash top"></i>
              <span class="tooltiptext">Unattempted</span>
            </div>
            {/if}

            {#if dummyarray.includes(JSON.parse(dataItem.content_text).question)&& questioncorrect.includes(JSON.parse(dataItem.content_text).question)}
            <div class="tooltip"><i class="fa fa-check"></i>
              <span class="tooltiptext">Correct</span>
            </div>


            {:else if dummyarray.includes(JSON.parse(dataItem.content_text).question)&& !questioncorrect.includes(JSON.parse(dataItem.content_text).question)}
            <div class="tooltip"><i class="fa fa-close"></i>
              <span class="tooltiptext">Incorrect</span>
            </div>

            {/if}
            
            

        </div>
      </td>
      </tr>
    {/each}
  </table>
</div>

{#if review}
  <Review />
{/if}

<style>

  .top {
    margin-top: 0.2rem;
  }

  .center {
    text-align: center;
  }

  .result {
    color: #A020F0;
    font-size: 17px;

  }

  .all-items {
    color:#009999;
  }

  .correct {
    color:#40BE59;
  }

  .incorrect {
    color:#FF0000;
  }

  .unattempt {
    color: #FFA500;
  }


  .time {
    color: #FF0000;
    font-weight: 700;
    font-size: 20px;
    left: 35rem;
    top: 15rem;
    margin-bottom: 20px;
    position: fixed;

  }

  .container {
    display: flex;
    position: relative;
    top: 5rem;
    left: 4rem;
    font-size: 20px;
    flex-direction: row;
    justify-content: start;
    padding: 10px;
    width: 90%;
  }


  .dot {
    height: 19px;
    width: 15px;
    background-color: #ffff;
    border-radius: 50%;
    display: inline-block;
    border: 1px solid #000000;
    margin-right: 0;
    margin-left: 4px;
    text-align: center;
  }

  .items {
    margin-left: 15px;
    display: inline-block;
    height: 4rem;
    width: 50%;
    padding-top: 12px;
    font-size: 17px;
    text-align: center;
    background-color: #e6ffff;

    border: 1px solid #009999;
  
  }
 

  .table {
    position: relative;
    top: 10rem;
    left: 0;
    right: 6rem;
    border-top: 2px solid #00b3b3;
    overflow: auto;
    height: 20rem;
  }

  .first {
    padding-left: 20px;
    padding-right: 20px;

  }

  .second {
    padding-left: 20px;
    padding-right: 15px;
    
  }

  .third {
    padding-left: 20px;
    padding-right: 20px;
   
  }
  
  .success {
    border: 2px solid #40BE59;
  }
  .unsuccess {
    border: 2px solid #FF0000;
  }
  

  .tooltip {
    position: relative;
    display: inline-block;
    margin-left: 20px;
    font-size:20px;
}

.tooltip .tooltiptext {
visibility: hidden;
width: 80px;
background-color: #000000;
color: #fff;
text-align: center;
border-radius: 6px;
padding: 5px 0;

/* Position the tooltip */
position: absolute;
z-index: 1;
top: -5px;
left: 90%;
}

.tooltip:hover .tooltiptext {
visibility: visible;
}


tr:nth-child(even) {
  background-color: #f2f2f2;
}

tr:nth-child(even):hover {
  background-color:#e6ffff;
  transition:0.3s;
}

tr:nth-child(odd):hover {
  background-color:#e6ffff;
  transition: 0.3s;
}

.items:hover {
  box-shadow: 0px 6px 12px rgba(0,0,0,0.26);
  background-color:#f5f5f0;
  border: none;
  transition: 0.3s;
  
}

.hidecorrect,
.hideincorrect,
.un {
  visibility: hidden;
  position: absolute;
}


.show {
  visibility: visible;
  position: relative;
}
</style>
