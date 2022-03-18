<style>
.letter{
  border: 2px solid gray;
  border-radius: 3px;
  margin: 2px;
  font-size: 1.8rem;
  font-weight: 700;
  height: 3rem;
  width: 3rem;
  display: flex;
  justify-content: center;
  align-items: last baseline;
  text-transform: uppercase;
  background: #D5DBDB;
}
.row{
 display: flex;
 align-items: center;
}
.more-button{
  margin: 3px;
  color: green;
}
.delete-button{
  margin: 3px;
  color: red;
}
.error-message{
  margin: 3px;
  color: red;
}
.suggested-move{
  style: "display:none";
}
</style>

<h2>Last Resortle</h2>

This is a script written by Pat Devlin to help solve the popular wordle puzzle.  Simply enter each word that you've guessed so far into the text box.  Select the revealed color of each letter by clicking on the corresponding square.


<div>
<b>New word:</b> <input type="text" id="wordle-input-box" onchange="makeNewRow()" maxlength="5" size="7"><button type="button" onclick="makeNewRow()" class="more-button">Enter</button>
</div>

<div id="input-board">

</div>

<br>



<div id="submit">
<button type="button" onclick="submitWordle()">Solve wordle!</button>
<label for="spoiler-mode">Hide output?</label><input type="checkbox" id="spoiler-mode" onclick="toggleAllSpoilers()" checked>
</div>
<div id="error-submitting" class="error-message"></div>
<em><span id="thinking-while-submitting" class="message" font-style="italic">Click above to solve</span></em>

<div id="wordle-output">




<br>

<span id="output-message" hidden></span>

<b onclick="toggleSpoilers('consistent-words')">Number of solutions: </b><span id="number-of-words-consistent" class="consistent-words spoilers"></span>
<br>
<span onclick="toggleSpoilers('suggested-move')"><h3>Suggested Guesses:</h3>
<b>Random guess: </b><span id="random-move-suggestion" class="suggested-move spoilers"></span>
<br><br>
<b>BestEntropy guess: </b><span id="entropy-move-suggestion" class="suggested-move spoilers"></span>
<br><br>
<b>BestMax guess: </b><span id="min-max-move-suggestion" class="suggested-move spoilers"></span>
<br><br>
<b>BestAverage guess: </b><span id="ave-move-suggestion" class="suggested-move spoilers"></span>
</span>
<br>
<h3 onclick="toggleSpoilers('word-list')">List of possible answers:</h3>
<span id="list-of-words-consistent" class="word-list spoilers"></span>

</div>

*To avoid spoilers, you can select that the output of the function be hidden.  If so, individual pieces of output can then be revealed or hidden by clicking on their corresponding text.




<script>
function makeNewRow() {
  let word = document.getElementById("wordle-input-box").value;
  let board=document.getElementById("input-board");
  if(word.length != 5){
    return;
  }
  if(word == board.lastChild.getAttribute("data-word")){
    return;
  }

  
  if(board.lastChild.getAttribute("data-is-template") == "T"){
    board.removeChild(board.lastChild);  
  }

  let row = document.createElement("div");
  row.className = "row";
  row.setAttribute("data-is-template", "F");
  let letterString = document.createElement("div");
  letterString.className = "row";

  row.setAttribute("data-word", word);
  let newWord = word + "     ";
  for( let j = 0; j < 5; j++){
    let box = document.createElement("div");
    box.className = "letter";
    box.setAttribute("data-color", "_");
    box.setAttribute("onclick", "changeColor(this)");
    box.innerHTML=newWord[j];
    letterString.appendChild(box);
  };
  row.appendChild(letterString);

  let deleteButton = document.createElement("button");
  deleteButton.setAttribute("type", "button");
  deleteButton.setAttribute("onclick", "deleteRow(this)");
  deleteButton.className = "delete-button";
  deleteButton.innerHTML = "&#10006;";
  row.appendChild(deleteButton);
  
  board.appendChild(row);
};

function makeTemplateRow() {
  let board=document.getElementById("input-board");

  let row = document.createElement("div");
  row.className = "row";
  row.setAttribute("data-is-template", "T");
  let letterString = document.createElement("div");
  letterString.className = "row";

  let word = "^^^^^";
  row.setAttribute("data-word", word);
  let newWord = word + "     ";
  for( let j = 0; j < 5; j++){
    let box = document.createElement("div");
    box.className = "letter";
    box.setAttribute("data-color", "_");
    box.innerHTML=newWord[j];
    letterString.appendChild(box);
  };
  row.appendChild(letterString);
 
  board.appendChild(row);
};

makeTemplateRow();

function deleteRow(element) {
  let row = element.parentNode;
  let board = row.parentNode;
  row.parentNode.removeChild(row);
  if(board.children.length == 0){
    makeTemplateRow();
  }
};


function changeColor(element) {
  switch(element.getAttribute("data-color")){
    case "_":
      element.setAttribute("data-color","?");
      element.style.background='#FFFF00';
      break;
    case "?":
      element.setAttribute("data-color","$");
      element.style.background='#00FF00';
      break;
    default:
      element.setAttribute("data-color","_");
      element.style.background='#D5DBDB';
      break;
  }
};

function isGuessBad(word){
  if(word.length == 5){
    return false;
  }else{
    return true;
  };
};

function submitWordle(){
  let board=document.getElementById("input-board");
  let rows = board.children;
  let errorSpot = document.getElementById("error-submitting");
  let wordsToSubmit=[];
  for(let j =0; j < rows.length; j++){
    if(isGuessBad(rows[j].getAttribute("data-word"))){ 
      errorSpot.innerHTML="Error!  You need to make sure all the words typed are five letters long, and they shouldn't contain any special characters or numbers.";
      return;
    }
    if(rows[j].getAttribute("data-is-template")=="F"){ //If it's a real row, then append it
//      alert("Got here.  Checking row with word: " + rows[j].getAttribute("data-word"));  
      let colorsOfWord = [];
      let letters = rows[j].children[0].children; //This is an array of the individual boxes for the word
//      alert((letters[0]).getAttribute("data-color"));
      for(let i=0; i < letters.length; i++){
        colorsOfWord.push(letters[i].getAttribute("data-color"));
      }
//      alert(colorsOfWord);
      wordsToSubmit.push([rows[j].getAttribute("data-word"), colorsOfWord]);
//      alert("Ok! Just finished row with word: " + rows[j].getAttribute("data-word"));
//      alert(wordsToSubmit);
    }
  }
//  let myL = [["a", [1,2,3,4,5]]];
//  alert("About to submit with word list: " + wordsToSubmit);  
  errorSpot.innerHTML = "";

  let thinkSpot = document.getElementById("thinking-while-submitting");
  thinkSpot.innerHTML="Processing request!  Please hold.";
  submitWithWords(wordsToSubmit);

};


async function makeAPICall(data = {}) {
  // Default options are marked with *
  const response = await fetch('https://y8r4t6bnud.execute-api.us-east-1.amazonaws.com/getWords', {
    method: 'POST', // *GET, POST, PUT, DELETE, etc.
    mode: 'cors', // no-cors, *cors, same-origin
    cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
    credentials: 'same-origin', // include, *same-origin, omit
    headers: {
      'Content-Type': 'application/json'
      // 'Content-Type': 'application/x-www-form-urlencoded',
    },
    redirect: 'follow', // manual, *follow, error
    referrerPolicy: 'no-referrer', // no-referrer, *no-referrer-when-downgrade, origin, origin-when-cross-origin, same-origin, strict-origin, strict-origin-when-cross-origin, unsafe-url
    body: JSON.stringify(data) // body data type must match "Content-Type" header
  });
  return response.json(); // parses JSON response into native JavaScript objects
}

function submitWithWords(w){
  let b = document.getElementById("input-board");

  makeAPICall({words: w})
   .then(data => {//alert("good job!");
  //   alert(data['msg']);
     console.log(data); // JSON data parsed by `data.json()` call
     updateOutputAfterAPICall(data);
  });
};

function updateOutputAfterAPICall(res){
//  alert("You got here!  Maybe it worked?");
//  alert(res);
//  alert(res['msg']);
//  alert(res['numberWords']);
  document.getElementById("output-message").innerHTML = res['msg'];
  document.getElementById("number-of-words-consistent").innerHTML = res['numberWords'];
  document.getElementById("random-move-suggestion").innerHTML = res['rand'];
  document.getElementById("entropy-move-suggestion").innerHTML = res['ent'];
  document.getElementById("min-max-move-suggestion").innerHTML = res['minMax'];
  document.getElementById("ave-move-suggestion").innerHTML = res['ave'];
  let validWords = res['validWords'];
  if(validWords.length < 20){
    document.getElementById("list-of-words-consistent").innerHTML = validWords.join(', ');
  }else{
    document.getElementById("list-of-words-consistent").innerHTML = validWords.splice(20).join(', ') + ', ...';
  }
  let thinkSpot = document.getElementById("thinking-while-submitting");
  thinkSpot.innerHTML="Request processed!";

//{'msg':'There are ' + str(len(consistentWords)) + ' consistent answers, which is a lot!', 'numberWords':len(consistentWords), 'rand':ranMove, 'ent':'', 'minMax':'', 'ave':'', 'validWords':consistentWords[:40]}),
};


function toggleAllSpoilers(){
  let collection = document.getElementsByClassName("spoilers");
  for(let j=0; j<collection.length; j++){
    if(document.getElementById("spoiler-mode").checked){
      collection[j].style.display = 'none';
    }else{
      collection[j].style.display = 'inline';
    }
  }
};


function toggleSpoilers(className) {
  if(!document.getElementById("spoiler-mode").checked){
    return; //We only toggle spoilers if "avoid spoilers" is unselected.  Otherwise, it's just always revealed on
  }
  let collection = document.getElementsByClassName(className);
  for(let j=0; j < collection.length; j++){
      if (collection[j].style.display === 'none') {
          collection[j].style.display = 'inline';
      } else {
          collection[j].style.display = 'none';
      }
   }
};

function toggleAllSpoilers(){
  let collection = document.getElementsByClassName("spoilers");
  for(let j=0; j<collection.length; j++){
    if(document.getElementById("spoiler-mode").checked){
      collection[j].style.display = 'none';
    }else{
      collection[j].style.display = 'inline';
    }
  }
};
</script>
