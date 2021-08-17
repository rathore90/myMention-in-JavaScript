<template>
    <div class="textarea_div" id="textarea" contenteditable type="text" @keyup="handleInput" data-text="Enter text here"></div>
</template>

<script>
  import { defineComponent } from 'vue';
  import usePostState from "../../views/NewPost/NewPostState";
  export default defineComponent({
    components: {  },
    setup() {
      const {state} = usePostState();
      var global_variable = 1;
      function handleInput(event) {
        let textArea = document.getElementById("textarea");
        if(event.key == '@' || event.key == '2') {
          
          let dropdown_container = document.createElement("div");
          dropdown_container.id = "auto-dropdown" + global_variable;
          dropdown_container.classList.add('autocomplete');
          dropdown_container.style.display = "inline-block";
          let inner_input = document.createElement("input");
          inner_input.id = "myInput" + global_variable;
          inner_input.setAttribute("autocomplete", "off");
          inner_input.classList.add('dropdown_inner');
          dropdown_container.appendChild(inner_input);
          textArea.appendChild(dropdown_container);
          inner_input.focus();
          autocomplete(document.getElementById("myInput" + global_variable), users);
          global_variable++;
        }
      }
      function getWidthOfInput(value) {
        var tmp = document.createElement("span");
        tmp.className = "input-element tmp-element";
        tmp.innerHTML = value.replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;');
        document.body.appendChild(tmp);
        var theWidth = tmp.getBoundingClientRect().width;
        document.body.removeChild(tmp);
        return theWidth + 20;
      }
      function adjustWidthOfInput(inner_input, value) {
        inner_input.style.width = getWidthOfInput(value) + "px";
      }
    // auto complete
    function autocomplete(inp, arr) {     
    /*the autocomplete function takes two arguments, the text field element and an array of possible autocompleted values:*/
    var currentFocus;
    /*execute a function when someone writes in the text field:*/
    inp.addEventListener("input", function (e) {
     if(e.key == 'Tab') {
       e.preventDefault();
        inp.value=document.querySelectorAll(".mention-input")[0].value;
        var span = document.createElement('span');
          span.classList.add('span-content');
          span.innerHTML = inp.value;
          span.contentEditable = 'false';
            let inputEle = document.getElementsByClassName('autocomplete');
            if (inputEle.length > 0) {
                inputEle[inputEle.length - 1].remove();
            }
          document.querySelector("#textarea").appendChild(span);
          /*insert the value for the autocomplete text field:*/
          closeAllLists(); 
          placeCaretAtEnd(document.querySelectorAll(".span-content:last-child")[0])
       return true;
     } else {
      var a, b, i, val = this.value;
      /*close any already open lists of autocompleted values*/
      closeAllLists();
      if (!val) {
        return false;
      }
    currentFocus = -1;
    /*create a DIV element that will contain the items (values):*/
    a = document.createElement("DIV");
    a.setAttribute("id", this.id + "autocomplete-list");
    a.setAttribute("class", "autocomplete-items");
    /*append the DIV element as a child of the autocomplete container:*/
    this.parentNode.appendChild(a);
    /*for each item in the array...*/
    for (i = 0; i < arr.length; i++) { 
      /*check if the item starts with the same letters as the text field value:*/ 
      if(arr[i]['displayName'].substr(0, val.length).toUpperCase()==val.toUpperCase()) 
        { /*create a DIV element for each matching element:*/ 
          b=document.createElement("DIV"); 
          /*make the matching letters bold:*/ 
          b.innerHTML="<strong>" +
          arr[i]['displayName'].substr(0, val.length) + "</strong>" ; 
          b.innerHTML +=arr[i]['displayName'].substr(val.length); 
          /*insert a input field that will hold the current array item's value:*/ 
          b.innerHTML +="<input class='mention-input' type='hidden' value='" + arr[i]['displayName'] + "'>"; 
          /*execute a function when someone clicks on the item value (DIV element):*/ 
          b.addEventListener("click", function() { 
            inp.value=this.getElementsByTagName("input")[0].value;
            var span = document.createElement('span');
            span.classList.add('span-content');
            span.contentEditable = 'false';
            span.innerHTML = inp.value;
             let inputEle = document.getElementsByClassName('autocomplete');
              if (inputEle.length > 0) {
                  inputEle[inputEle.length - 1].remove();
              }
            document.querySelector("#textarea").appendChild(span);
            /*insert the value for the autocomplete text field:*/
            closeAllLists(); 
            placeCaretAtEnd(document.querySelectorAll(".span-content:last-child")[0])
            // adjustWidthOfInput(span, inp.value)
          }); 
          a.appendChild(b); 
        } 
      }    
     }
    }); 
 
    /*execute a function presses a key on the keyboard:*/ 
    inp.addEventListener("keydown", function (e) { 
      if(e.key == 'Backspace' && inp.value.length <= 0) {
        document.getElementsByClassName("autocomplete")[0].remove();
        document.getElementById("textarea").focus();
      }
      var x=document.getElementById(this.id + "autocomplete-list" ); 
      if (x) x=x.getElementsByTagName("div"); 
        if (e.keyCode==40) { 
        /*If the arrow DOWN key is pressed, increase the currentFocus variable:*/ 
        currentFocus++; 
        /*and and make the current item more visible:*/
        addActive(x); 
      } 
      else if (e.keyCode==38) { 
        //up /*If the arrow UP key is pressed, decrease the currentFocusvariable:*/ currentFocus--; /*and and make the current item more visible:*/ 
        addActive(x); 
      } else if (e.keyCode==13) { 
        /*If the ENTER key is pressed, prevent the form from being submitted,*/ 
        e.preventDefault(); if
        (currentFocus> -1) {
        /*and simulate a click on the "active" item:*/
        if (x) x[currentFocus].click();
        } 
      }
      });
      function addActive(x) {
      /*a function to classify an item as "active":*/
      if (!x) return false;
      /*start by removing the "active" class on all items:*/
      removeActive(x);
      if (currentFocus >= x.length) currentFocus = 0;
      
      if (currentFocus < 0) currentFocus=(x.length - 1); /*add class "autocomplete-active" :*/
        x[currentFocus].classList.add("autocomplete-active"); } 
        
      function removeActive(x) { 
        /*a function to remove the "active" class from all autocomplete items:*/ 
        for (var i=0; i < x.length; i++) {
        x[i].classList.remove("autocomplete-active"); } 
      } 
      function closeAllLists(elmnt) { 
        /*close all autocomplete lists in the document, except the one passed as an argument:*/ 
        var x=document.getElementsByClassName("autocomplete-items"); 
        for (var i=0; i < x.length; i++) { 
          if (elmnt !=x[i] && elmnt !=inp) { 
            x[i].parentNode.removeChild(x[i]); 
          } 
        } 
      } 
      function placeCaretAtEnd(el) {
        el.focus();
        if (typeof window.getSelection != "undefined"
              && typeof document.createRange != "undefined") {
                
          var range = document.createRange();
          range.selectNode(el);
          console.log(range);
          range.collapse(false);
          var sel = window.getSelection();
          sel.removeAllRanges();
          sel.addRange(range);
        } else if (typeof document.body.createTextRange != "undefined") {
          var textRange = document.body.createTextRange();
          textRange.moveToElementText(el);
          textRange.collapse(false);
          textRange.select();
        }
      }
      
      /*execute a function when someone clicks in the document:*/ 
      document.addEventListener("click", function (e) { 
        closeAllLists(e.target); 
      }); 
    } 
    /*An array containing all the country names in the world:*/ 
    var users = [{
      'uid': 'uid1',
      'displayName': 'Prerana Shrestha',
      'email': 'prerana@sweetlegs.ca'
    },
    {
      'uid': 'uid11',
      'displayName': 'Sudip Shrestha',
      'email': 'prerana@sweetlegs.ca'
    },
    {
      'uid': 'uid12',
      'displayName': 'Pardeep',
      'email': 'pardeep@sweetlegs.ca'
    },
    {
      'uid': 'uid123',
      'displayName': 'Chris',
      'email': 'Chris@sweetlegs.ca'
    },
    {
      'uid': 'uid124',
      'displayName': 'Everyone',
      'email': 'Email'
    }]; 
      return {
        handleInput,
        state,
      };
    }
  });
</script>
<style>
  #user-list{
    display: none;
  }
  .dropdown{
    position: absolute;
    width: 80%;
    height: 50%;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }
  #textarea {
    -moz-appearance: textfield-multiline;
    -webkit-appearance: textarea;
    border-bottom: 1px solid #666666;
    font: medium -moz-fixed;
    font: -webkit-small-control;
    height: auto;
    padding: 10px 2px;
    margin: auto 5px;
    resize: both;
    font-size: 16px;
    background: #ffff;
    min-height: 8vh;
    line-height: 1.4rem;
  }
  #textarea:focus {
    border-bottom: 2px solid rgb(19, 194, 194);
    outline: none;
  }
  #mention-dropdown{
    position: absolute;
    background-color: #fff;
    width: 50%;
    margin-top: 1px;
    box-shadow: 0 1px 2px rgb(204 204 204);
    border-radius: 0 1px 2px 2px;
    overflow: hidden;
    display: none;
    overflow-y: auto;
    z-index: 9;
    transition: width 1s ease-in-out;
  }
  .option{
    padding: 10px 5px;
    cursor: pointer;
    font-size: 18px;
    font: medium -moz-fixed;
    font: -webkit-small-control;
  }
  .option:hover{
    background-color: rgb(201, 189, 189);
  }
  i.selected-user {
    color: #2980B9;
  }
  /*the container must be positioned relative:*/
  .autocomplete {
    position: relative;
    display: inline-block;
  }
  .span-content{
    font-style: italic;
    color: palevioletred;
  }
  .dropdown_inner{
    width: fit-content;
    border: none;
    min-width: 20px;
    font-size: 16px;
  }
  .dropdown_inner:focus {
    outline: none;
  }
  .dropdown_inner input[type=text] {
    width: fit-content;
  }
  .dropdown_inner input[type=submit] {
    color: #fff;
    cursor: pointer;
  }
  .autocomplete-items {
    position: absolute;
    border: 1px solid #d4d4d4;
    z-index: 99;
    top: 100%;
    left: 0;
    right: 0;
    overflow: scroll;
    min-width: max-content;
    max-height: 50vh;
  }
  .autocomplete-items div {
    padding: 10px;
    cursor: pointer;
    background-color: #fff;
    border-bottom: 1px solid #d4d4d4;
  }
  /*when hovering an item:*/
  .autocomplete-items div:hover {
    background-color: #e9e9e9;
  }
  /*when navigating through the items using the arrow keys:*/
  .autocomplete-active {
    background-color: DodgerBlue !important;
    color: #ffffff;
  }
</style>
