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

table th, table td {
    padding: 12px 15px;
    text-align: center;
}

table thead tr {
    background-color: #009879;
    color: #ffffff;
    text-align: left;
}

table tbody tr {
    border-bottom: 1px solid #dddddd;
}

table tbody tr:nth-of-type(even) {
    background-color: #f3f3f3;
}

table tbody tr:last-of-type {
    border-bottom: 2px solid #009879;
}


table {
    border-collapse: collapse;
    margin: 25px 0;
    font-size: 0.9em;
    font-family: sans-serif;
    min-width: 400px;
    box-shadow: 0 0 20px rgba(0, 0, 0, 0.15);
}

</style>

<h2>Last Resor(d)le</h2>

This is a script written by Pat Devlin to help solve the popular wordle puzzle.  Simply enter each word that you've guessed so far into the text box.  Select the revealed color of each letter by clicking on the corresponding square.  See below for more.


<div>
<b>New word:</b> <input type="text" id="wordle-input-box" onchange="makeNewRow()" maxlength="5" size="7"><button type="button" onclick="makeNewRow()" class="more-button">Enter</button>
</div>

<div id="input-board">

</div>

<br>



<div id="submit">
<button type="button" onclick="submitWordle()">Solve wordle!</button>
<label for="spoiler-mode">Hide output?</label><input type="checkbox" id="spoiler-mode" onclick="toggleAllSpoilers()">
</div>

<div id="error-submitting" class="error-message"></div>
<em><span id="thinking-while-submitting" class="message" font-style="italic">Click above to solve</span></em>


<div>
<button id="reset-colors" onclick="clearColors()">Clear colors</button> <button id="reset-words" onclick="reset()">Reset</button> <button id="random-input" onclick="makeRandomInput()">Random input</button></div>


<div id="wordle-output">

<h2>Output</h2>
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


<h2>About</h2>


<h3>What can this site do?</h3>

<ul>
<li><b>Solve wordle puzzles:</b> After submitting your puzzle, the site gives next guesses as suggested by four different algorithms.</li>
<li><b>Solve "more"-dle puzzles:</b> For more-dle puzzles (dordle, quordle, ...), the 'clear colors' feature is especially helpful.  The sedecordle can very often be solved by starting with the guesses 'train' and 'close' and then repeatedly identifying a word that has only one possible option (thus finding all 16 words using only 18 guesses).</li>
<li><b>Solve absurdle puzzles:</b> Click "Solve wordle!" without providing any input to get a suggested starting word.  Enter the suggested word into absurdle and use the response back here to get the next suggested word.  Because absurdle is adversarial, the BestMax algorithm suggestion is well-suited for this.</li>
<li><b>Play wordle against you:</b> You think of a five-letter word and run this in the same way you would run absurdle</li>
<li><b>Toggle spoilers:</b> To avoid spoilers, you can select that the output of the function be hidden.  If so, individual pieces of output can then be revealed or hidden by clicking on their corresponding text.</li>
<li><b>Puzzle mode:</b> Clicking the Random Input button generates a wordle puzzle with several guesses already made.  You can either use this to demo the site or [by hiding spoilers] you can use it to practice your wordle skills!</li>
</ul>


<h3>Maybe you want to know...</h3>

<ul>
<li><b>Why can't it find my word?</b>
<ul><li>First make sure you're entering it correctly.  For instance, if the answer is BAKER and we guess ARROW, then the second R should not be colored yellow.</li>
<li>If you're entering it correctly, then it's likely that the word isn't in the original wordle dictionary (this happens surprisingly often.  Some sites like squardle take their answers from a dictionary with a lot more words).</li>
<li>In particular, the wordle answer dictionary is missing a lot of five-letter words that end in S.  For instance, it's missing FINDS, HELPS, BOOKS.</li>
</ul></li>
<li><b>Which guess should I use?</b>
<ul>
<li>The site runs four different algorithms to suggest which word to play next.  All of these algorithms are running in "hard mode" which means that each new guess must actually be a plausible answer consistent with all the information we have so far.</li>
<li>The random guess is silly.  You shouldn't use that unless you're having fun.</li>
<li>Each of the other guesses is the result of careful thinking.  Often they agree, but if not, the BestEntropy guess is often slightly better.</li>
</ul>
</li>
</ul>

<h3>How does this all work?</h3>
(Description below is about to get more technical for those who care)

<h4>Big picture</h4>
This site (statically hosted by github) was built simply by writing its index file in html, css, and javascript.  The key functionality is to make an API call that invokes an AWS lambda instance that I set up.  This API call passes the board (with colors) as input to a python script that I wrote, and it receives a handful of outputs which are used to populate the page.

<p>The repo for the stand-alone python code is available here (for the lambda function, naturally I had to add a script that handles the API request, but this was done in exactly the way you're imagining).  To handle scaling [and out of fear of paying some extra pennies in hosting fees], I also slightly optimized the python code for the lambda function by speeding up all calls by roughly a multiplicative factor of 10.  Memory allocation per call is minimal, so no additional effort was made to optimize that.

<h4>Algorithms</h4>
The back-end is written in python, which was honestly just chosen for the sake of variety.  Another choice for a project like this could have been C or C++, especially since it's relatively easy to imagine writing many simple procedures, which we'll want to call many times (for instance, if we wanted to explore the exponential-size min-max decision tree for this problem).  Although a low-level compiled language would ultimately end up being substantially faster, I figured I'd do this one in python precisely because I have less experience with it lately.

<p>The <b>Random</b> algorithm just outputs a random word consistent with the guesses thus far.  Each of the other three algorithms does the following (as described using the language of set theory):

<ul>
<li>Load the wordle dictionary</li>
<li>Find all words consistent with what we know thus far</li>
<li>For each possible next guess, we consider how that guess might be colored</li>
<ul>
<li>This partitions the set of consistent words into equivalence classes, where two words are in the same class iff they would give our potential guess the same color.</li>
<li>[From an information-theoretic point of view, words in the same equivalence class would therefore be indistinguishable from each other given the guesses we've made thus far]</li>
<li>(In the code, the equivalence classes of this relation are called word classes)</li>
<li>Give this potential guess some "score," which is a function of the size of this partition.</li>
</ul>
<li>Propose the potential guess which has the best "score"</li>
</ul>

For us, the score of a potential guess will be a function of how that guess partitions the set of consistent guesses, and in particular, it will actually just be a function of the word class sizes (i.e., it will be a function of the multiset of block sizes of the partition).  To play with this, you can call splitIntoWordClasses([potentialWord], setOfConsistentWords)


<p><strong>For example!</strong>  Suppose we intially have no information, and we are considering guessing "jazzy".  Then the following is true:
<ul>
<li>there are 1111 possible answers where "jazzy" would be colored all gray</li>
<li>there are 2 possible answers where "jazzy" would have its "j" yellow and all other colors gray</li>
<li>there are 10 possible answers where "jazzy" would have its "j" green and all other colors gray</li>
<li>there is 1 possible answer where "jazzy" would have its "j" green, the "a" yellow, and all other colors gray</li>
<li> Et cetera... </li>
</ul>
Then the score of "jazzy" is a function of the numbers (1111, 2, 10, 1, ...).  This sequence of 'class sizes' tells us that although jazzy is <em>sometimes</em> a convenient guess [e.g., if the j is green and the a is yellow, we know the answer must be JUNTA], it is often times pretty lousy (e.g., half the time, all we find out is that it doesn't share any of those letters).

<p>Although we lose information by only considering this sequence of numbers (1111, 2, 10, 1, ...)---e.g., if we land among the 10 guesses where J is green, how easy it is to distinguish those?---heuristically, this sequence of numbers is likely pretty telling of how good a guess "jazzy" is, and we just need to decide how to linearly  such sequences so that we can pick a 'best' choice.  There a bunch of good options, but three especially natural ones are the following:

<ul>
<li><b>BestEntropy:</b>
<ul>
<li>The score function here is a weighted sum of the logarithms of the terms in the sequence.  Mathematically speaking, this is computing a quantity known as the "entropy" of the random variable of "what is the answer" conditioned on the random variable of "how will this potential guess be colored."</li>
<li>This algorithm has the very elegant description "simply pick the word that minimizes the entropy."</li>
<li>Entropy is the cornerstone of information theory, and it's the subject of <a href="https://people.math.harvard.edu/~ctm/home/text/others/shannon/entropy/entropy.pdf">my favorite research paper of all time</a> (by a lot) [and also the most cited math/cs paper of all time (by a lot!)].</li>
<li>Entropy is essentially "how many yes/no questions would you need to ask to determine the answer," and it is therefore an extremely natural choice.  That said, notice that when we play wordle, we aren't asking yes/no questions (if we were, then minimizing entropy is literally the best you could hope to do).  Instead!  When we guess a word, there are way more than 2 possible outcomes: namely we see which letters get which colors, which is a lot more information than merely a yes/no question (and it's also hard to say how much "information" we'll be able to get out of subsequent guesses since it amounts to weird queries about the space or English words, which is itself really weird)</li>
</ul></li>
<li><b>BestMax:</b>
<ul>
<li>The score function here is just to take the maximum element of the sequence.</li>
<li>This is an algorithm that greedily tries to avoid the worst-case scenario.  That said, it only looks one step ahead, so it's just a first-level approximation of the optimal min-max algorithm.</li>
</ul></li>
<li><b>BestAve:</b>
<ul>
<li>The score function here is a weighted average of the terms of the sequence.</li>
<li>You could say that it tries to minimize a sort of average of worst-case scenarios.  Namely, it imagines it will get a response with probability proportional to each color class, but then it imagines naively testing each word in that color class one at a time.</li>
<li>Equivalently, this algorithm picks the word such that an immediate random follow-up guess has the highest likelihood of working.</li>
</ul></li>
</ul>

<h4>Analysis of algorithms</h4>
Each algorithm finds the next guess in time O(mn), where m is the number of possible words consistent with our information, and n is the set of words we're allowed to say.  In hard mode, we have m=n, so this runs in order O(m^2) time.  If we are given a dictionary and a set of previous guesses, we can find the set of words consistent with these guesses by just one pass through the dictionary*, so this procedure is done in linear time.  As for space, each algorithm only needs O(1) memory.


<p><em>*There's an interesting question here</em> about how this would scale for a dictionary of since N and a set of G guesses.  Unfortunately, here the point is moot since English uses an alphabet of constant size, and the words must be five letters long.  Thus, the information in the guesses very quickly becomes redundant (e.g., we can only get at most 5 letters colored not-black, and a black letter conveys the same information regardless of where it appears in the word).  In the general setting, I would conjecture that we could check N words against G different guesses in time O(N) + o(N*G) [thereby beating the trivial O(N*G) algorithm], but it would probably depend on what a "word" is...


<h4>Performance of algorithms</h4>
Running these algorithms against all possible wordle answers gives us the following distribution for the number of guesses needed.

<div><img width="250" height="150" src="Guesses Needed per Algorithm.png"></div>

<p>Here, the algorithms have almost identical distributions with the largest difference being that BestEntropy does a better job at guessing the word in 3 guesses (especially relative to BestAve).  The best of these algorithms is BestEntropy, and the worst is BestMax.  Each of these simple algorithms guesses the word within 6 tries with the following exceptions:

<table>
  <thead>
    <tr>
        <td></td>
        <td>BestEntropy</td>
        <td>BestMax</td>
        <td>BestAve</td>
    </tr>
  </thead>
  <tbody>
    <tr>
        <td>pound</td>
        <td>4</td>
        <td>4</td>
        <td>7</td>
    </tr>
    <tr>
        <td>water</td>
        <td>4</td>
        <td>7</td>
        <td>7</td>
    </tr>
    <tr>
        <td>winch</td>
        <td>5</td>
        <td>7</td>
        <td>5</td>
    </tr>
    <tr>
        <td>shave</td>
        <td>5</td>
        <td>7</td>
        <td>7</td>
    </tr>
    <tr>
        <td>wound</td>
        <td>5</td>
        <td>7</td>
        <td>8</td>
    </tr>
    <tr>
        <td>wight</td>
        <td>7</td>
        <td>5</td>
        <td>7</td>
    </tr>
    <tr>
        <td>foyer</td>
        <td>7</td>
        <td>7</td>
        <td>7</td>
    </tr>
    <tr>
        <td>graze</td>
        <td>7</td>
        <td>7</td>
        <td>7</td>
    </tr>
    <tr>
        <td>match</td>
        <td>7</td>
        <td>7</td>
        <td>7</td>
    </tr>
    <tr>
        <td>swore</td>
        <td>7</td>
        <td>7</td>
        <td>7</td>
    </tr>
    <tr>
        <td>tatty</td>
        <td>7</td>
        <td>7</td>
        <td>7</td>
    </tr>
    <tr>
        <td>waste</td>
        <td>7</td>
        <td>7</td>
        <td>7</td>
    </tr>
    <tr>
        <td>willy</td>
        <td>7</td>
        <td>7</td>
        <td>7</td>
    </tr>
    <tr>
        <td>goner</td>
        <td>8</td>
        <td>8</td>
        <td>8</td>
    </tr>
    <tr>
        <td>watch</td>
        <td>8</td>
        <td>8</td>
        <td>8</td>
    </tr>
  </tbody>
</table>

Interestingly, there is a considerable amount of overlap among the worst words for these algorithms, with "goner" and "watch" being especially difficult to guess.

<script>
function makeNewRow(word = "", colors = "bbbbb") {
  if(word == ""){
    word = document.getElementById("wordle-input-box").value;
  }
  let board=document.getElementById("input-board");
  if(word.length != 5){
    return;
  }
  if(word == board.lastChild.getAttribute("data-word")){
    return;
  }
  if(colors.length != 5){
    colors = "bbbbb";
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

  for( let j = 0; j < 5; j++){
    let box = document.createElement("div");
    box.className = "letter";
    box.setAttribute("data-color", "_");
    setColor(box, colors[j]);
    box.setAttribute("onclick", "toggleBoxColor(this)");
    box.innerHTML=word[j];
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
  document.getElementById("wordle-input-box").value = "";
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
    setColor(box, "gray");
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
  board.removeChild(row);
  if(board.children.length == 0){
    makeTemplateRow();
  }
};


function setColor(box,c){
  switch(c){
    case "y":
      box.setAttribute("data-color","?");
      box.style.background='#FFFF00';
      break;
    case "g":
      box.setAttribute("data-color","$");
      box.style.background='#00FF00';
      break;
    default:
      box.setAttribute("data-color","_");
      box.style.background='#D5DBDB';
      break;
  }

};

function toggleBoxColor(element) {
  switch(element.getAttribute("data-color")){
    case "_":
      setColor(element,"y");
      break;
    case "?":
      setColor(element,"g");
      break;
    default:
      setColor(element,"b");
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
    document.getElementById("list-of-words-consistent").innerHTML = validWords.splice(0,20).join(', ') + ', ...';
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

function reset(){
  let board = document.getElementById("input-board");
  while(board.firstChild){
    board.removeChild(board.firstChild);
  }
  makeTemplateRow();
};

function clearColors(){
  let board = document.getElementById("input-board");
  let rows = board.children;
  for(let j=0; j<rows.length; j++){
      let letters = rows[j].children[0].children; //This is an array of the individual boxes for the word
      for(let i=0; i < letters.length; i++){
        letters[i].setAttribute("data-color", "_");
        letters[i].style.background='#D5DBDB';
      }
  }
};

function useTemplate(words, colors){
  reset();
  for(let j=0; j<Math.min(words.length, colors.length); j++){
    makeNewRow(words[j], colors[j]);
  }
};

function makeRandomInput(){

let multiple = [ [["raise", "enter"], ["bgbyb", "bbybb"]], [["train", "anvil"], ["bbygy", "yybgb"]], [["pizza"], ["byybb"]], [["sweet", "tooth", "candy"], ["bybbb","bbgbb","bbybb"]], [["pasta", "sauce"], ["bbbyb","bbbby"]], [["board", "games"], ["bbbyy", "bbbyb"]], [["sharp", "knife", "point"], ["bbybb", "bbbbb", "bybbg"]], [["weeps", "cries", "teary"], ["bgbbb", "bybyb", "ygbgb"]], [["audio"], ["bygbb"]], [["input", "words"], ["bbyby", "bybby"]], [["jazzy", "piano"], ["bbbbb","ybbbb"]], [["water", "falls", "storm", "cloud"], ["bbbyy", "bbbby", "gbbgb", "bbbbb"]]]

let oneSolution = [[["pride", "lions","tiger"], ["byyby", "yybbb", "bybyy"]], [["faint", "blobs", "voice"], ["bbbbb", "byybb", "bybby"]], [["couch", "divan", "sleep"], ["ybybb", "bybbb", "ybbbb"]], [["great", "worse", "champ"], ["bgbyy","bbygb", "bbybb"]] , [["close", "train"], ["gbbby", "bybyb"]] , [["price", "stuff"], ["bbygb","bbbyb"]], [["sweet", "tooth", "candy"], ["ybyby","gbbgb","bgbbb"]], [["flock", "birds", "raven"], ["bbbyb", "bbgbb", "ybybb"]], [["poker", "blind", "flush"], ["byybb","ggbbb", "bgbbb"]], [["glove", "hands", "thumb", "frost"], ["byybb", "bbbby", "bbgbb", "bbygb"]],[["raise", "flung", "crazy"], ["yybbb", "bbbbb", "gyybb"]], [["brain", "lobes", "think", "ideas"], ["bybyy", "bbbyb", "ybyyb", "gbgbb"]], [["tying"], ["ybggy"]], ["river", "flows", "delta"], ["bbbgb", "bbbyb", "bybyb"], [["broom", "sweep", "floor", "clean"], ["bbbbb", "bbbgb", "bbbbb", "ybybb"]], [["drink", "water"], ["ygybb", "bybby"]]];


let allOptions = multiple.concat(oneSolution);
var input = allOptions[Math.floor(Math.random()*allOptions.length)];
useTemplate(input[0], input[1]); //"ideas"

submitWordle();
};

</script>
