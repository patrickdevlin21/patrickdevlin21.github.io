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
</style>

<h2>Wordle Helper!</h2>

This is a script to help with the wordle puzzle.  Simply enter each word that you've guessed so far into the text box.  Select the revealed color of each letter by clicking on the corresponding square.


<div>
<b>New word:</b> <input type="text" id="wordle-input-box" onchange="makeNewRow2()" maxlength="5" size="5"></input><button type="button" onclick="makeNewRow2()" class="more-button">Add</button>
</div>

<div id="input-board">

</div>


<br> <br>


<div id="submit">
<button type="button" onclick="submitWordle()">Get help with the above wordle!</button>
</div>
<div id="error-submitting" class="error-message"></div>
<div id="wordle-output"></div>




<script>
function makeNewRow2() {
  let word = document.getElementById("wordle-input-box").value;
  if(word.length != 5){
    return;
  }
  let board=document.getElementById("input-board");
  
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
      element.setAttribute("data-color","$");
      element.style.background='#00FF00';
      break;
    case "$":
      element.setAttribute("data-color","?");
      element.style.background='#FFFF00';
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
  for(let j =0; j < rows.length; j++){
    if(isGuessBad(rows[j].getAttribute("data-word"))){ 
      errorSpot.innerHTML="Error!  You need to make sure all the words typed are five letters long, and they shouldn't contain any special characters or numbers.";
      return;
    }
  }
  errorSpot.innerHTML = "";
  let output = document.getElementById("wordle-output");
  output.innerHTML="Meep-morp!  Robot brain!  Output will go here.";
};


</script>

Skip to content
Pull requests
Issues
Marketplace
Explore
@patrickdevlin21
patrickdevlin21 /
patrickdevlin21.github.io
Public

Code
Issues
Pull requests
Actions
Projects
Wiki
Security
Insights

    Settings

patrickdevlin21.github.io/index.md
@patrickdevlin21
patrickdevlin21 Update index.md
Latest commit 38d11dd 2 hours ago
History
1 contributor
156 lines (129 sloc) 3.68 KB
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
</style>

## Wordle Helper!

This is a script to help with the wordle puzzle.  Simply type each word that you've guessed so far in the corresponding text box.  Select the revealed color of each letter by clicking on the corresponding square.


<div id="input-board">

</div>


<br> <br>


<div id="submit">
<button type="button" onclick="submitWordle()">Get help with the above wordle!</button>
</div>
<div id="error-submitting" class="error-message"></div>
<div id="wordle-output"></div>




<script>

function makeNewRow() {
  let board=document.getElementById("input-board");
  let row = document.createElement("div");
  row.className = "row";
  let word = document.createElement("div");
  word.className = "row";
  for( let j = 0; j < 5; j++){
    let box = document.createElement("div");
    box.className = "letter";
    box.setAttribute("data-color", "_");
    box.setAttribute("onclick", "changeColor(this)");
    word.appendChild(box);
  };
  row.appendChild(word);

  let inputBox = document.createElement("input");
  inputBox.setAttribute("type", "text");
  inputBox.setAttribute("onchange", "populateGuess(this)");
  row.appendChild(inputBox);

  let moreButton = document.createElement("button");
  moreButton.setAttribute("type", "button");
  moreButton.setAttribute("onclick", "makeNewRow()");
  moreButton.className = "more-button";
  moreButton.innerText = "Another word";
  row.appendChild(moreButton);

  let deleteButton = document.createElement("button");
  deleteButton.setAttribute("type", "button");
  deleteButton.setAttribute("onclick", "deleteRow(this)");
  deleteButton.className = "delete-button";
  deleteButton.innerText = "Remove word";
  row.appendChild(deleteButton);
  
  board.appendChild(row);
};

function deleteRow(element) {
  let row = element.parentNode;
  let board = row.parentNode;
  row.parentNode.removeChild(row);
  if(board.children.length == 0){
    makeNewRow();
  }
};

makeNewRow();


function populateGuess(element){
  let newWord = element.value + "     ";
  let word = element.parentElement.children[0].children;
  for(let j = 0; j < word.length; j++){
    word[j].innerHTML=newWord[j];
  }
};

function changeColor(element) {
  switch(element.getAttribute("data-color")){
    case "_":
      element.setAttribute("data-color","$");
      element.style.background='#00FF00';
      break;
    case "$":
      element.setAttribute("data-color","?");
      element.style.background='#FFFF00';
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
  for(let j =0; j < rows.length; j++){
    if(isGuessBad(rows[j].children[1].value)){ 
      errorSpot.innerHTML="Error!  You need to make sure all the words typed are five letters long, and they shouldn't contain any special characters or numbers.";
      return;
    }
  }
  errorSpot.innerHTML = "";
  let output = document.getElementById("wordle-output");
  output.innerHTML="Meep-morp!  Robot brain!  Output will go here.";
};


</script>

    © 2022 GitHub, Inc.

    Terms
    Privacy
    Security
    Status
    Docs
    Contact GitHub
    Pricing
    API
    Training
    Blog
    About

