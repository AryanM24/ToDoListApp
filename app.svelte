<style>
  /* Global styles for dark mode */
  #app {
    background-color: #121212; /* Dark background */
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 20px; /* Add padding around the app */
    box-sizing: border-box; /* Ensure padding doesn't affect element width */
  }

  h1 {
    color: #FFFFFF;
    text-align: center;
  }
	h2 {
    color: #FFFFFF;
		font-size:18px;
  }
	

  ul {
    padding: 0;
  }

  li {
    background-color: #1e1e1e;
    border: 1px solid #333;
    padding: 10px;
    border-radius: 8px;
    margin-bottom: 8px;
  }

	label{
		color:#FFFFFF;
	}

  input[type="checkbox"] {
    accent-color: #bb86fc;
  }

  button {
    background-color: #1e1e1e;
    color: #9e9e9e;
    border: none;
    padding: 8px 12px;
    font-size: 14px;
    border-radius: 5px;
    cursor: pointer;
  }

  button:hover {
    background-color: #9a6ce7;
		color: #FFFFFF;
  }

  button:disabled {
    background-color: #3a3a3a;
    color: #777;
    cursor: not-allowed;
  }

  p {
    color: #9e9e9e;
  }

  hr {
    border-color: #333;
  }
</style>


<script>
	// object array
  let checkboxes = [
    { id: 1, label: 'Admire Amruth', checked: false, priority: 'yes', date: '12/2/24', time: '11:59 PM', category: 'Team 2554 Robotics Onboarding'},
    { id: 2, label: 'Develop C++ Interpreter', checked: false, priority: 'yes', date: '12/2/24', time: '11:59 PM', category: 'Team 2554 Robotics Onboarding' },
    { id: 3, label: 'Make a compiler for C ', checked: false, priority: 'no', date: '12/19/24', time: '11:59 PM', category: 'Team 2554 Robotics Onboarding' },
		{ id: 4, label: 'Learn Rust', checked: false, priority: 'yes', date: '12/2/24', time: '11:59 PM', category: 'Team 2554 Robotics Onboarding'},
    { id: 5, label: 'Study for Robotics Test', checked: false, priority: 'no', date: '12/19/24', time: '11:59 PM', category: 'Team 2554 Robotics Onboarding'}
  ];

	function parseDateTime(dateStr, timeStr) {
	  // Split the date (MM/DD/YY format)
	  var dateParts = dateStr.split('/');
	  var month = Number(dateParts[0]);
	  var day = Number(dateParts[1]);
	  var year = Number(dateParts[2]) + 2000;
	  
	  // Parse time
	  var timeParts = timeStr.split(' ');
	  var clockTime = timeParts[0].split(':');
	  var hours = Number(clockTime[0]);
	  var minutes = Number(clockTime[1]);
	  var period = timeParts[1];
	  
	  // Convert to 24-hour format
	  if (period === 'PM' && hours !== 12) {
	    hours += 12;
	  }
	  if (period === 'AM' && hours === 12) {
	    hours = 0;
	  }
	  
	  // Create Date object
	  return new Date(year, month - 1, day, hours, minutes);
	}

	function sortTasksByDate() {
	  var i = 0;
		while (i < checkboxes.length - 1) {
	    var j = 0;
	    while (j < checkboxes.length - i - 1) {
	      var dateA = parseDateTime(checkboxes[j].date, checkboxes[j].time);
	      var dateB = parseDateTime(checkboxes[j+1].date, checkboxes[j+1].time);
	      
	      // Swap if needed
	      if (dateA > dateB) {
	        var temp = checkboxes[j];
	        checkboxes[j] = checkboxes[j+1];
	        checkboxes[j+1] = temp;
	      }
	      j++;
	    }
	    i++;
	  }
	  
	  // Trigger reactivity
	  checkboxes = checkboxes;
	}
	
	// Call the sort function initially to sort the tasks
	sortTasksByDate();
	
	$: uncheckedCount = function() {
    var count = 0;
		var i = 0;
    while (i < checkboxes.length) {
      if (checkboxes[i].checked === false) {
        count = count + 1;
      }
			i++;
    }
    return count;
  }();

	function addTask() {
    var newTaskLabel = prompt("Enter the new task: ");
		var newTaskDate = prompt("Enter the due date for the new task (MM/DD/YY): ");
		var newTaskTime = prompt("Enter the due time for the new task (e.g.: 11:59 PM): ");
		var newTaskCategory = prompt("Enter the catgroy/class of the new task (e.g.: Algebra 2H): ");
		var priority = prompt("Is this task a priority (yes/no): ");
    if (newTaskLabel) {
    checkboxes = [...checkboxes, { id: checkboxes[checkboxes.length - 1].id + 1, label: newTaskLabel, checked: false ,
																	 date: newTaskDate, priority: priority, time: newTaskTime, category: newTaskCategory}];
	  }
		sortTasksByDate();
  }

  // Function to remove completed tasks
  function removeCompleted() {
    var newCheckboxes = [];
		var i = 0;
    while (i < checkboxes.length) {
      if (!checkboxes[i].checked) {
        newCheckboxes.push(checkboxes[i]);
      }
			i++;
    }
    checkboxes = newCheckboxes;
  }

	function removeTask(id) {
    var newCheckboxes = [];
		var i = 0;
    while (i < checkboxes.length) {
      if (checkboxes[i].id !== id) {
        newCheckboxes.push(checkboxes[i]);
      }
			i++;
    }
    checkboxes = newCheckboxes;
  }
	function changePriority(id) {
		var i = 0;
	  while (i < checkboxes.length) {
	    if (checkboxes[i].id === id) {
	      if (checkboxes[i].priority === 'yes') {
	        checkboxes[i].priority = 'no';
	      } else {
	        checkboxes[i].priority = 'yes';
	      }
	      break;
	    }
	    i++;
	  }
	  checkboxes = checkboxes;
	}
</script>

<div id="app">
	<h1>To-do List</h1>
	
	<ul style="list-style-type: none; padding: 0;">
	  {#each checkboxes as checkbox}
	    <li style="display: flex; align-items: center; margin-bottom: 8px; ">
	      {#if checkbox.priority === 'yes'}
	        <button on:click={function () { changePriority(checkbox.id); }} style="border: none; background: none; cursor: pointer; margin-left:-4px;"><span style="color: gold; margin-right: 5px;">★</span></button>
	      {/if}
	      {#if checkbox.priority === 'no'}
	        <button on:click={function () { changePriority(checkbox.id); }} style="border: none; background: none; cursor: pointer; margin-left:-4px;"><span style="color: rgba(255, 215, 0, 0.3); margin-right: 5px;">★</span></button>
	      {/if}
	      <input type="checkbox" bind:checked={checkbox.checked} style="margin-right: 10px;" />
	      <div style="flex-grow: 1;">
	        <div style="display: flex; align-items: center;">
	          <label style="margin-right: 10px;">{checkbox.label}</label>
	        </div>
	        <div style="display: flex; justify-content: space-between; align-items: center;">
	          <p style="color: #5f6368; font-size: 12px; margin: 0;">{checkbox.category}</p>
	          <span style="color: #5f6368; font-size: 12px;">{checkbox.date}, {checkbox.time}</span>
	        </div>
	      </div>
	      <button on:click={function () { removeTask(checkbox.id); }} style="color: red; border: none; background: none; font-size: 15px; cursor: pointer; margin-left: 15px;">X</button>
	    </li>
	  {/each}
	</ul>
	
	<hr>
	
	<h2>Tasks Remaining: {uncheckedCount}</h2>
	
	<div style="display: flex; gap: 10px; margin-bottom: 10px;">
	  <button on:click={addTask}>Add Task</button>
	  <button on:click={removeCompleted}>Remove Completed</button>
	</div>
</div>
