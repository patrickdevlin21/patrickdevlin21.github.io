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
