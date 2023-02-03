<script>
    let friends = [];
    let selectedFriend = {};
  
    function addFriend(friend) {
      friends = [...friends, friend];
    }
  
    function selectFriend(friend) {
      selectedFriend = friend;
    }
  
    function updateFriend(friend) {
      friends = friends.map(f => f.id === friend.id ? friend : f);
    }
  
    function removeFriend(id) {
      friends = friends.filter(friend => friend.id !== id);
    }
  </script>
  
  <h1>Dinner With Friends</h1>
  
  <h2>Friends List</h2>
  <ul>
    {#each friends as friend}
      <li>
        <p>Name: {friend.name}</p>
        <p>Email: {friend.email}</p>
        <button on:click={() => selectFriend(friend)}>Select</button>
        <button on:click={() => removeFriend(friend.id)}>Remove</button>
      </li>
    {/each}
  </ul>
  
  <h2>Add Friend</h2>
  <form on:submit|preventDefault={() => addFriend({
      id: Math.random(),
      name: event.target.name.value,
      email: event.target.email.value
    })}>
    <input type="text" name="name" placeholder="Name" />
    <input type="email" name="email" placeholder="Email" />
    <button type="submit">Add Friend</button>
  </form>
  
  <h2>Selected Friend</h2>
  {#if selectedFriend.id}
    <form on:submit|preventDefault={() => updateFriend({
      ...selectedFriend,
      name: event.target.name.value,
      email: event.target.email.value
    })}>
      <input type="text" name="name" bind:value={selectedFriend.name} />
      <input type="email" name="email" bind:value={selectedFriend.email} />
      <button type="submit">Update Friend</button>
    </form>
  {:else}
    <p>No friend selected</p>
  {/if}
  <script>
    import { onMount } from 'svelte';
    import { getDinnerDetails } from './dinner-api.js';
  
    let dinner;
  
    onMount(async () => {
      dinner = await getDinnerDetails();
    });
  </script>
  
  <h2>Dinner Details</h2>
  <div>
    <p>Date: {dinner.date}</p>
    <p>Time: {dinner.time}</p>
    <p>Location: {dinner.location}</p>
    <p>Guests:</p>
    <ul>
      {#each dinner.guests as guest}
        <li>{guest}</li>
      {/each}
    </ul>
  </div>
  <script>
    import { updateGuests } from './dinner-api.js';
  
    let newGuest = '';
  
    function handleSubmit(event) {
      event.preventDefault();
      updateGuests(newGuest);
      newGuest = '';
    }
  </script>
  
  <h2>Add Guest</h2>
  <form on:submit|preventDefault={handleSubmit}>
    <input type="text" bind:value={newGuest} />
    <button type="submit">Add</button>
  </form>
  <!-- Add the CSS stylesheet -->
<style>
    .container {
      display: flex;
      flex-direction: column;
      align-items: center;
    }
  
    h1 {
      margin-top: 20px;
    }
  
    form {
      display: flex;
      flex-direction: column;
      align-items: center;
      margin-top: 20px;
      width: 400px;
      padding: 20px;
      border: 1px solid gray;
      border-radius: 5px;
    }
  
    input[type="text"], input[type="number"] {
      width: 100%;
      padding: 10px;
      margin-bottom: 20px;
      border: 1px solid gray;
      border-radius: 5px;
    }
  
    button {
      padding: 10px 20px;
      background-color: blue;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }
  </style>
  
  <!-- Add the form for entering meal information -->
  <div class="container">
    <h1>Add Meal Information</h1>
    <form>
      <input type="text" placeholder="Name of Dish" bind:value={dish} />
      <input type="number" placeholder="Number of Servings" bind:value={servings} />
      <input type="text" placeholder="Name of Cook" bind:value={cook} />
      <button on:click={addMeal}>Add Meal</button>
    </form>
  </div>
  
  <!-- Display the list of meals -->
  <h1>List of Meals</h1>
  {#if meals.length === 0}
    <p>No meals have been added yet.</p>
  {:else}
    <table>
      <thead>
        <tr>
          <th>Dish</th>
          <th>Servings</th>
          <th>Cook</th>
        </tr>
      </thead>
      <tbody>
        {#each meals as meal}
          <tr>
            <td>{meal.dish}</td>
            <td>{meal.servings}</td>
            <td>{meal.cook}</td>
          </tr>
        {/each}
      </tbody>
    </table>
  {/if}
  
  <script>
    import { writable, get } from 'svelte/store';
    import { createEvent, getEvents } from './api.js';
  
    let name = '';
    let date = '';
    let guests = [];
    let events = [];
  
    const error = writable('');
    const success = writable('');
  
    const handleSave = async () => {
      try {
        await createEvent({ name, date, guests });
        name = '';
        date = '';
        guests = [];
        success.set('Event saved successfully!');
      } catch (e) {
        error.set('An error occurred while saving the event');
      }
    };
  
    const handleGetEvents = async () => {
      try {
        events = await getEvents();
      } catch (e) {
        error.set('An error occurred while retrieving events');
      }
    };
  </script>
  
  <script>
    let selectedFriends = [];
  
    function addFriend(friend) {
      selectedFriends = [...selectedFriends, friend];
    }
  
    function removeFriend(friend) {
      selectedFriends = selectedFriends.filter(selectedFriend => selectedFriend !== friend);
    }
  </script>
  
  <div class="container">
    <h2>Select Friends</h2>
    {#each friends as friend}
      <div class="friend-item">
        <input type="checkbox" bind:checked={selectedFriends.includes(friend)} on:change={() => selectedFriends.includes(friend) ? removeFriend(friend) : addFriend(friend)} />
        <p>{friend}</p>
      </div>
    {/each}
  
    <h2>Selected Friends</h2>
    {#if selectedFriends.length === 0}
      <p>No friends selected</p>
    {/if}
    {#each selectedFriends as friend}
      <p>{friend}</p>
    {/each}
  </div>
  <script>
    import { onMount } from "svelte";
  
    let friends = [];
    let newFriend = "";
  
    onMount(async () => {
      const res = await fetch("/api/friends");
      friends = await res.json();
    });
  
    function handleAddFriend() {
      friends = [...friends, newFriend];
      newFriend = "";
    }
  
    function handleDeleteFriend(friend) {
      friends = friends.filter(f => f !== friend);
    }
  </script>
  
  <style>
    .friends {
      display: flex;
      flex-wrap: wrap;
    }
  
    .friend {
      background-color: lightblue;
      border-radius: 4px;
      padding: 0.5rem;
      margin: 0.5rem;
      width: 20%;
    }
  
    input[type="text"] {
      padding: 0.5rem;
      margin-right: 0.5rem;
      border-radius: 4px;
      border: none;
    }
  
    button {
      padding: 0.5rem;
      background-color: lightblue;
      border-radius: 4px;
      border: none;
      cursor: pointer;
    }
  </style>
  
  <h2>Friends</h2>
  
  <div class="friends">
    {#each friends as friend}
      <div class="friend">
        {friend}
        <button on:click={() => handleDeleteFriend(friend)}>Delete</button>
      </div>
    {/each}
  </div>
  
  <input type="text" bind:value={newFriend} placeholder="Add a friend" />
  <button on:click={handleAddFriend}>Add</button>
  <script>
    let friends = [
      { name: "John Doe", email: "john.doe@example.com", dinner: "Italian" },
      { name: "Jane Doe", email: "jane.doe@example.com", dinner: "Mexican" },
      { name: "Jim Smith", email: "jim.smith@example.com", dinner: "Indian" }
    ];
  
    let dinnerTypes = ["Italian", "Mexican", "Indian", "Chinese", "Japanese"];
  
    function addFriend() {
      friends = [
        ...friends,
        { name: "", email: "", dinner: "" }
      ];
    }
  
    function removeFriend(index) {
      friends = friends.filter((friend, i) => i !== index);
    }
  
    function updateFriend(e, index) {
      friends = friends.map((friend, i) => {
        if (i === index) {
          return {
            ...friend,
            [e.target.name]: e.target.value
          };
        }
        return friend;
      });
    }
  </script>
  
  <button on:click={addFriend}>Add Friend</button>
  <table>
    <thead>
      <tr>
        <th>Name</th>
        <th>Email</th>
        <th>Dinner</th>
        <th></th>
      </tr>
    </thead>
    <tbody>
      {#each friends as friend, i}
        <tr>
          <td>
            <input
              type="text"
              bind:value={friend.name}
              name="name"
              on:input={e => updateFriend(e, i)}
            />
          </td>
          <td>
            <input
              type="text"
              bind:value={friend.email}
              name="email"
              on:input={e => updateFriend(e, i)}
            />
          </td>
          <td>
            <select
              bind:value={friend.dinner}
              name="dinner"
              on:input={e => updateFriend(e, i)}
            >
              {#each dinnerTypes as type}
                <option value={type}>{type}</option>
              {/each}
            </select>
          </td>
          <td>
            <button on:click={() => removeFriend(i)}>Remove</button>
          </td>
        </tr>
      {/each}
    </tbody>
  </table>
  <script>
    import { createEventDispatcher } from "svelte";
    export let friends = [];
    const dispatch = createEventDispatcher();
  
    function addFriend() {
      dispatch("add", { name: "New Friend" });
    }
  </script>
  
  <style>
    .friend-list {
      display: flex;
      flex-wrap: wrap;
      justify-content: space-evenly;
    }
  
    .friend-card {
      background-color: #fff;
      box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
      width: 300px;
      height: 400px;
      margin: 20px;
      border-radius: 10px;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      padding: 20px;
    }
  
    .friend-name {
      font-size: 24px;
      font-weight: bold;
      text-align: center;
      margin-bottom: 20px;
    }
  
    .friend-details {
      font-size: 16px;
      text-align: center;
    }
  
    button {
      background-color: #2980b9;
      color: #fff;
      border: none;
      border-radius: 10px;
      padding: 10px 20px;
      font-size: 16px;
      cursor: pointer;
    }
  </style>
  
  <div class="friend-list">
    {#each friends as friend}
    <div class="friend-card">
      <div class="friend-name">{friend.name}</div>
      <div class="friend-details">{friend.email}</div>
      <div class="friend-details">{friend.phone}</div>
      <div class="friend-details">{friend.address}</div>
    </div>
    {/each}
  </div>
  
  <button on:click={addFriend}>Add Friend</button>
  // App.svelte

<script>
  import { onMount } from 'svelte';
  import { createEventDispatcher } from 'svelte';
  import Friend from './Friend.svelte';

  const dispatch = createEventDispatcher();

  let friends = [
    { id: 1, name: 'John', selected: false },
    { id: 2, name: 'Jane', selected: false },
    { id: 3, name: 'Jim', selected: false },
    { id: 4, name: 'Joan', selected: false }
  ];

  let selectedFriends = [];

  function selectFriend(friend) {
    friends = friends.map(f => {
      if (f.id === friend.id) {
        return { ...f, selected: !f.selected };
      }
      return f;
    });
    selectedFriends = friends.filter(f => f.selected);
  }

  function saveDinner() {
    dispatch('dinnerSaved', { friends: selectedFriends });
  }

  onMount(() => {
    console.log('App mounted');
  });
</script>

<style>
  .friends {
    display: flex;
    flex-wrap: wrap;
  }

  .friend {
    padding: 10px;
    margin: 10px;
    border: 1px solid gray;
    border-radius: 10px;
    width: 300px;
    height: 200px;
    text-align: center;
    background-color: white;
  }

  .selected {
    background-color: lightgray;
  }

  button {
    margin-top: 20px;
    padding: 10px 20px;
    border-radius: 20px;
    background-color: lightgray;
    color: white;
    font-size: 18px;
  }
</style>

<div class="friends">
  {#each friends as friend}
    <Friend friend={friend} on:click={() => selectFriend(friend)} />
  {/each}
</div>

<button on:click={saveDinner}>Save Dinner</button>
<script>
    import { onMount } from 'svelte';
  
    let guests = [];
  
    onMount(async () => {
      const res = await fetch('/api/guests');
      guests = await res.json();
    });
  
    function addGuest() {
      guests = [...guests, { id: Date.now(), name: '' }];
    }
  
    function removeGuest(id) {
      guests = guests.filter(guest => guest.id !== id);
    }
  
    function handleGuestNameChange(event, id) {
      guests = guests.map(guest => {
        if (guest.id === id) {
          return { ...guest, name: event.target.value };
        }
        return guest;
      });
    }
  
    async function saveGuests() {
      const res = await fetch('/api/guests', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(guests)
      });
  
      const data = await res.json();
      console.log(data);
    }
  </script>
  
  <style>
    .container {
      max-width: 800px;
      margin: 0 auto;
      text-align: center;
    }
  
    h1 {
      margin-top: 50px;
    }
  
    .guest-list {
      margin-top: 50px;
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
    }
  
    .guest {
      width: 200px;
      height: 200px;
      background-color: #f2f2f2;
      border: 1px solid #ccc;
      border-radius: 10px;
      margin: 20px;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      padding: 20px;
      box-shadow: 2px 2px 8px rgba(0, 0, 0, 0.1);
    }
  
    .guest h2 {
      margin-top: 10px;
    }
  
    .guest input[type='text'] {
      width: 100%;
      padding: 10px;
      font-size: 18px;
      margin-top: 20px;
      border: none;
      border-bottom: 2px solid #ccc;
      transition: border-bottom 0.3s ease-in-out;
    }
  
    .guest input[type='text']:focus {
      border-bottom: 2px solid #333;
      outline: none;
    }
  
    .guest button {
      background-color: #333;
      color: #fff;
      padding: 10px 20px;
      border: none;
      border-radius: 10px;
      margin-top: 20px;
      cursor: pointer;
      transition: background-color 0.3s ease-in-out;
    }
  
    .guest button:hover {
      background-color: #fff;
      color: #333;
   
      <script>
  import { createEventDispatcher } from "svelte";
  export let friend = {};

  const dispatch = createEventDispatcher();

  function saveFriend() {
    dispatch("save", { friend });
  }
</script>

<style>
  form {
    padding: 1em;
  }

  label {
    display: block;
    margin-bottom: 0.5em;
  }

  input,
  textarea,
  select {
    width: 100%;
    padding: 0.5em;
    font-size: 1em;
    border-radius: 5px;
    border: 1px solid #ccc;
    box-sizing: border-box;
  }

  input[type="submit"] {
    background-color: #2196f3;
    color: white;
    border: none;
    padding: 0.5em 1em;
    border-radius: 5px;
    cursor: pointer;
    float: right;
  }
</style>

<form on:submit|preventDefault={saveFriend}>
  <label for="name">Name</label>
  <input
    type="text"
    id="name"
    bind:value={friend.name}
  />

  <label for="email">Email</label>
  <input
    type="email"
    id="email"
    bind:value={friend.email}
  />

  <label for="phone">Phone</label>
  <input
    type="tel"
    id="phone"
    bind:value={friend.phone}
  />

  <label for="notes">Notes</label>
  <textarea id="notes" bind:value={friend.notes}></textarea>

  <input type="submit" value="Save" />
</form>
<script>
    import { onMount } from 'svelte';
    import { getAllDinners } from './store.js';

    let dinners = [];
    let error = '';

    onMount(async () => {
        try {
            dinners = await getAllDinners();
        } catch (err) {
            error = err.message;
        }
    });
</script>

<h2>My Dinners</h2>

{#if error}
    <p>{error}</p>
{:else}
    <table>
        <thead>
            <tr>
                <th>Name</th>
                <th>Date</th>
                <th>Location</th>
                <th>Description</th>
                <th>Friends</th>
                <th></th>
            </tr>
        </thead>
        <tbody>
            {#each dinners as dinner}
                <tr>
                    <td>{dinner.name}</td>
                    <td>{dinner.date}</td>
                    <td>{dinner.location}</td>
                    <td>{dinner.description}</td>
                    <td>
                        {#each dinner.friends as friend, i}
                            {friend}
                            {#if i < dinner.friends.length - 1},{/if}
                        {/each}
                    </td>
                    <td>
                        <a href={`/dinners/${dinner.id}`}>Details</a>
                    </td>
                </tr>
            {/each}
        </tbody>
    </table>
{/if}
// /src/components/AddDinner.svelte
<script>
  import { createEventDispatcher } from 'svelte';

  const dispatch = createEventDispatcher();

  let dinnerName;
  let dinnerDescription;
  let dinnerDate;
  let dinnerLocation;

  function addDinner() {
    dispatch('add', { 
      name: dinnerName,
      description: dinnerDescription,
      date: dinnerDate,
      location: dinnerLocation
    });
    dinnerName = '';
    dinnerDescription = '';
    dinnerDate = '';
    dinnerLocation = '';
  }
</script>

<style>
  form {
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 50%;
    margin: 0 auto;
    padding: 20px;
    border: 1px solid gray;
    border-radius: 5px;
    box-shadow: 2px 2px 5px gray;
  }

  input, textarea {
    width: 100%;
    padding: 10px;
    margin: 10px 0;
    border-radius: 5px;
    border: 1px solid gray;
  }

  button {
    padding: 10px 20px;
    background-color: #4CAF50;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }
</style>

<form on:submit|preventDefault={addDinner}>
  <input type="text" bind:value={dinnerName} placeholder="Dinner name" required />
  <textarea bind:value={dinnerDescription} placeholder="Dinner description" required></textarea>
  <input type="date" bind:value={dinnerDate} required />
  <input type="text" bind:value={dinnerLocation} placeholder="Dinner location" required />
  <button type="submit">Add dinner</button>
</form>
<script>
    export let friends = [
      {
        name: "John Doe",
        email: "johndoe@example.com"
      },
      {
        name: "Jane Doe",
        email: "janedoe@example.com"
      },
      {
        name: "Jim Smith",
        email: "jimsmith@example.com"
      }
    ];
  
    let selectedFriend;
  
    function addFriend() {
      friends = [...friends, { name: "", email: "" }];
    }
  
    function updateFriend(index, field, value) {
      friends = [
        ...friends.slice(0, index),
        {
          ...friends[index],
          [field]: value
        },
        ...friends.slice(index + 1)
      ];
    }
  
    function removeFriend(index) {
      friends = [...friends.slice(0, index), ...friends.slice(index + 1)];
    }
  
    function selectFriend(index) {
      selectedFriend = index;
    }
  </script>
  
  <style>
    .selected {
      background-color: lightblue;
    }
  </style>
  
  <h1>Friends</h1>
  <button on:click={addFriend}>Add Friend</button>
  <table>
    <thead>
      <tr>
        <th>Name</th>
        <th>Email</th>
        <th></th>
      </tr>
    </thead>
    <tbody>
      {#each friends as friend, index}
        <tr
          class:selected={index === selectedFriend}
          on:click={() => selectFriend(index)}
        >
          <td>
            <input
              type="text"
              bind:value={friend.name}
              on:input={e => updateFriend(index, "name", e.target.value)}
            />
          </td>
          <td>
            <input
              type="email"
              bind:value={friend.email}
              on:input={e => updateFriend(index, "email", e.target.value)}
            />
          </td>
          <td>
            <button on:click={() => removeFriend(index)}>Remove</button>
          </td>
        </tr>
      {/each}
    </tbody>
  </table>
  <script>
    import { onMount } from 'svelte';
  
    export let dinner;
  
    let editing = false;
    let newFriend = '';
  
    onMount(async () => {
      dinner = await getDinner(dinner.id);
    });
  
    async function addFriend() {
      if (!newFriend) return;
      await addFriendToDinner(dinner.id, newFriend);
      dinner.friends.push(newFriend);
      newFriend = '';
    }
  
    async function toggleGoing(friend) {
      dinner.friends = dinner.friends.map(f => {
        if (f === friend) {
          f.going = !f.going;
        }
        return f;
      });
      await updateDinner(dinner);
    }
  
    async function removeFriend(friend) {
      dinner.friends = dinner.friends.filter(f => f !== friend);
      await updateDinner(dinner);
    }
  
    async function saveDinner() {
      await updateDinner(dinner);
      editing = false;
    }
  </script>
  
  <style>
    .container {
      max-width: 600px;
      margin: 0 auto;
      padding: 1rem;
    }
  
    h1 {
      text-align: center;
      margin-bottom: 2rem;
    }
  
    h2 {
      margin-bottom: 1rem;
    }
  
    ul {
      list-style: none;
      padding: 0;
      margin: 0;
    }
  
    li {
      display: flex;
      align-items: center;
      margin-bottom: 1rem;
    }
  
    li input[type="checkbox"] {
      margin-right: 1rem;
    }
  
    .form-control {
      padding: .5rem;
      margin-right: 1rem;
      border: 1px solid #ccc;
      border-radius: .25rem;
      width: 20%;
    }
  
    .btn {
      padding: .5rem 1rem;
      border-radius: .25rem;
      background-color: lightblue;
      color: #fff;
      cursor: pointer;
    }
  
    .btn:hover {
      background-color: blue;
    }
  
    .btn + .btn {
      margin-left: 1rem;
    }
  
    .text-danger {
      color: red;
    }
  </style>
  
  <div class="container">
    <h1>Dinner with friends</h1>
    {#if !dinner}
      <p>Loading...</p>
    {:else}
      <h2>{dinner.name}</h2>
      <ul>
        {#each dinner.friends as friend}
          <li>
            <input type="checkbox" bind:checked={friend.going} on:change={() => toggleGoing(friend)} />
            {friend.name}
            {#if editing}
              <button class="btn" on:click={() => removeFriend(friend)}>Remove</button>
            {/if}
          </li>
        {/each}
      </ul>
      {#if editing}
        <            <script>
                export let selectedFriend;
              
                const handleSubmit = event => {
                  event.preventDefault();
                  const formData = new FormData(event.target);
                  selectedFriend.dishes = Array.from(formData.entries())
                    .filter(([key, value]) => key.startsWith('dish-'))
                    .map(([key, value]) => ({ name: value }));
                  selectedFriend.notes = formData.get('notes');
                  dispatch('updateFriend', selectedFriend);
                };
              </script>
              
              <form on:submit={handleSubmit}>
                {#each selectedFriend.dishes as dish, i}
                  <div>
                    <label for={`dish-${i}`}>Dish {i + 1}:</label>
                    <input type="text" name={`dish-${i}`} id={`dish-${i}`} value={dish.name} />
                  </div>
                {/each}
                <div>
                  <label for="notes">Notes:</label>
                  <textarea name="notes" id="notes" value={selectedFriend.notes}></textarea>
                </div>
                <button type="submit">Save changes</button>
              </form>
              import { onMount } from 'svelte';

              onMount(async () => {
              const res = await fetch('/api/dinner');
              const dinners = await res.json();
              store.set({ dinners });
              });
              
              function addDinner() {
              store.update(({ dinners, currentDinner }) => {
              return {
              dinners: [...dinners, currentDinner],
              currentDinner: {
              name: '',
              date: '',
              attendees: [],
              description: '',
              },
              };
              });
              }
              
              function deleteDinner(index) {
              store.update(({ dinners }) => {
              return {
              dinners: dinners.filter((dinner, i) => i !== index),
              };
              });
              }
              
              function handleNameInput(event) {
              store.update(({ currentDinner }) => {
              return {
              currentDinner: {
              ...currentDinner,
              name: event.target.value,
              },
              };
              });
              }
              
              function handleDateInput(event) {
              store.update(({ currentDinner }) => {
              return {
              currentDinner: {
              ...currentDinner,
              date: event.target.value,
              },
              };
              });
              }
              
              function handleAttendeesInput(event) {
              store.update(({ currentDinner }) => {
              return {
              currentDinner: {
              ...currentDinner,
              attendees: event.target.value.split(','),
              },
              };
              });
              }
              
              function handleDescriptionInput(event) {
              store.update(({ currentDinner }) => {
              return {
              currentDinner: {
              ...currentDinner,
              description: event.target.value,
              },
              };
              });
              }
              </script>
              <style>
                .container {
                  max-width: 800px;
                  margin: 0 auto;
                  padding: 20px;
                }
              
                .form-group {
                  margin-bottom: 20px;
                }
              
                .form-control {
                  width: 100%;
                  padding: 10px;
                  border: 1px solid #ccc;
                  border-radius: 5px;
                  font-size: 16px;
                }
              
                .btn {
                  background-color: #4CAF50;
                  color: white;
                  padding: 12px 20px;
                  border: none;
                  border-radius: 4px;
                  cursor: pointer;
                  float: right;
                }
              
                .btn:hover {
                  background-color: #3e8e41;
                }
              
                .dinners {
                  margin-top: 20px;
                }
              
                .dinner {
                  background-color: #f2f2f2;
                  padding: 20px;
                  border-radius: 5px;
                  margin-bottom: 20px;
                }
              
                .dinner h3 {
                  margin-top: 0;
                  margin-bottom: 0;
                }
              
                .dinner p {
                  margin-top: 0;
                  margin-bottom: 0;
                }
              
                .dinner .attend
                function addDinner() {
  const dinner = {
    id: Math.random(),
    date: new Date(),
    guests: [],
    menu: []
  };
  dinners.push(dinner);
  setDinners([...dinners]);
}
<button on:click={addDinner}>Add Dinner</button>
// NewInvite.svelte
<script>
    import { createEventDispatcher, onMount } from 'svelte';
    const dispatch = createEventDispatcher();

    let name = '';
    let date = '';
    let number = '';

    onMount(async () => {
        // get current date
        const today = new Date();
        const dd = String(today.getDate()).padStart(2, '0');
        const mm = String(today.getMonth() + 1).padStart(2, '0');
        const yyyy = today.getFullYear();

        date = `${yyyy}-${mm}-${dd}`;
    });

    function handleSubmit() {
        // validate form inputs
        if (!name || !date || !number) {
            return alert('All fields are required!');
        }
        // dispatch invite event
        dispatch('invite', { name, date, number });
        // reset form inputs
        name = '';
        date = '';
        number = '';
    }
</script>

<style>
    form {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        height: 100%;
    }

    input[type="text"], input[type="number"], input[type="date"] {
        padding: 10px;
        margin: 10px 0;
        width: 100%;
        font-size: 16px;
        box-sizing: border-box;
        border: 2px solid #ccc;
        border-radius: 5px;
    }

    input[type="submit"] {
        padding: 10px 20px;
        margin: 10px 0;
        background-color: blue;
        color: #fff;
        border: none;
        border-radius: 5px;
        cursor: pointer;
    }
</style>

<form on:submit|preventDefault={handleSubmit}>
    <input type="text" bind:value={name} placeholder="Name" />
    <input type="date" bind:value={date} />
    <input type="number" bind:value={number} placeholder="Number of guests" />
    <input type="submit" value="Invite" />
</form>
// Display dinner details
function DinnerDetails({ dinnerId }) {
  const dinner = dinnerStore.getDinnerById(dinnerId);
  const { date, guests, menu, host } = dinner;

  return (
    <section class="dinner-details">
      <h2>Dinner Details</h2>
      <p>Date: {date}</p>
      <p>Host: {host}</p>
      <p>Guests: {guests.join(', ')}</p>
      <p>Menu: {menu.join(', ')}</p>
      <button on:click={() => dinnerStore.removeDinner(dinnerId)}>
        Remove Dinner
      </button>
    </section>
  );
}

// Main component to display all dinners
function App() {
  const [selectedDinnerId, setSelectedDinnerId] = useState();

  const handleSelectDinner = (id) => {
    setSelectedDinnerId(id);
  };

  return (
    <div class="app">
      <header class="header">
        <h1>Dinner With Friends</h1>
        <button on:click={dinnerStore.addDinner}>Add Dinner</button>
      </header>
      <main class="main">
        <DinnerList onSelectDinner={handleSelectDinner} />
        {selectedDinnerId && (
          <DinnerDetails dinnerId={selectedDinnerId} />
        )}
      </main>
    </div>
  );
}

const app = new App({
  target: document.body,
});

export default app;
<script>
    import { onMount } from "svelte";
  
    let dinnerWithFriends = [];
  
    onMount(async () => {
      const res = await fetch("/api/dinners");
      dinnerWithFriends = await res.json();
    });
  
    function addDinner(event) {
      event.preventDefault();
      const newDinner = {
        host: event.target.host.value,
        guest: event.target.guest.value,
        date: event.target.date.value,
        meal: event.target.meal.value,
      };
      dinnerWithFriends = [...dinnerWithFriends, newDinner];
  
      fetch("/api/dinners", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(newDinner),
      });
    }
  </script>
  
  <h2>Add Dinner</h2>
  <form on:submit|preventDefault={addDinner}>
    <div>
      <label for="host">Host:</label>
      <input type="text" id="host" name="host" required />
    </div>
    <div>
      <label for="guest">Guest:</label>
      <input type="text" id="guest" name="guest" required />
    </div>
    <div>
      <label for="date">Date:</label>
      <input type="date" id="date" name="date" required />
    </div>
    <div>
      <label for="meal">Meal:</label>
      <input type="text" id="meal" name="meal" required />
    </div>
    <button type="submit">Add Dinner</button>
  </form>
  <script>
    import { onMount } from 'svelte';
    let dinner_friends = [
      { id: 1, name: 'John', dish: 'Spaghetti Bolognese' },
      { id: 2, name: 'Jane', dish: 'Vegetable Stir-Fry' },
      { id: 3, name: 'Jim', dish: 'BBQ Ribs' }
    ];
  
    function addFriend() {
      let newId = dinner_friends.length + 1;
      let newName = prompt("Enter the name of your friend:");
      let newDish = prompt("Enter the dish your friend is bringing:");
      dinner_friends = [...dinner_friends, { id: newId, name: newName, dish: newDish }];
    }
  
    function removeFriend(id) {
      dinner_friends = dinner_friends.filter(friend => friend.id !== id);
    }
  
    onMount(() => {
      console.log('Dinner With Friends app mounted');
    });
  </script>
  
  <style>
    .container {
      max-width: 800px;
      margin: 0 auto;
      padding: 20px;
      text-align: center;
    }
  
    h1 {
      font-size: 36px;
      margin-bottom: 20px;
    }
  
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 20px;
    }
  
    th, td {
      border: 1px solid #ddd;
      padding: 8px;
      text-align: left;
    }
  
    th {
      background-color: #ddd;
    }
  
    button {
      padding: 10px 20px;
      margin-top: 20px;
    }
  </style>
  
  <div class="container">
    <h1>Dinner With Friends</h1>
    <table>
      <tr>
        <th>Name</th>
        <th>Dish</th>
        <th></th>
      </tr>
      {#each dinner_friends as friend}
      <tr>
        <td>{friend.name}</td>
        <td>{friend.dish}</td>
        <td>
          <button on:click={() => removeFriend(friend.id)}>Remove</button>
        </td>
      </tr>
      {/each}
    </table>
    <button on:click={addFriend}>Add Friend</button>
  </div>
  <script>
    import { createEventDispatcher, onMount } from 'svelte';
    import Modal from './Modal.svelte';

    const dispatch = createEventDispatcher();

    let guests = [];

    onMount(async () => {
        const response = await fetch('/api/guests');
        guests = await response.json();
    });

    let selectedGuest;
    let showModal = false;

    function editGuest(guest) {
        selectedGuest = guest;
        showModal = true;
    }

    function closeModal() {
        selectedGuest = null;
        showModal = false;
    }

    function saveGuest(guest) {
        if (selectedGuest) {
            // update existing guest
            const index = guests.findIndex(g => g.id === selectedGuest.id);
            guests[index] = guest;
        } else {
            // add new guest
            guests.push(guest);
        }

        closeModal();
    }

    function deleteGuest(guest) {
        const index = guests.findIndex(g => g.id === guest.id);
        guests.splice(index, 1);
    }
</script>

{#if showModal}
    <Modal
        guest={selectedGuest}
        on:close={closeModal}
        on:save={saveGuest}
    />
{/if}

<table class="table">
    <thead>
        <tr>
            <th>Name</th>
            <th>Email</th>
            <th>Phone</th>
            <th></th>
        </tr>
    </thead>
    <tbody>
        {#each guests as guest}
            <tr>
                <td>{guest.name}</td>
                <td>{guest.email}</td>
                <td>{guest.phone}</td>
                <td>
                    <button on:click={() => editGuest(guest)}>Edit</button>
                    <button on:click={() => deleteGuest(guest)}>Delete</button>
                </td>
            </tr>
        {/each}
    </tbody>
</table>

<button class="btn btn-primary" on:click={() => editGuest(null)}>Add Guest</button>
<script>
    import { onMount } from 'svelte';
    
    let meals = [
      {
        name: "Steak",
        ingredients: ["Steak", "Potatoes", "Carrots"],
        cookingTime: "45 minutes"
      },
      {
        name: "Pizza",
        ingredients: ["Pizza Dough", "Tomato Sauce", "Cheese", "Pepperoni"],
        cookingTime: "30 minutes"
      },
      {
        name: "Salad",
        ingredients: ["Lettuce", "Tomatoes", "Croutons", "Dressing"],
        cookingTime: "15 minutes"
      }
    ];
    
    let selectedMeal = null;
    
    function selectMeal(meal) {
      selectedMeal = meal;
    }
    
    onMount(async () => {
      const res = await fetch('/api/meals');
      meals = await res.json();
    });
  </script>
  
  <style>
    /* Add some styles for the meal card */
    .meal-card {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      width: 300px;
      height: 300px;
      background-color: #fff;
      box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
      border-radius: 10px;
      margin: 20px;
      cursor: pointer;
    }
    
    .meal-card:hover {
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
    }
    
    .meal-card h2 {
      margin-top: 20px;
    }
    
    .meal-card p {
      margin: 20px;
      text-align: center;
    }
  </style>
  
  {#if selectedMeal === null}
    <h1>Choose a Meal</h1>
    <div class="meal-grid">
      {#each meals as meal}
        <div class="meal-card" on:click={() => selectMeal(meal)}>
          <h2>{meal.name}</h2>
          <p>Cooking Time: {meal.cookingTime}</p>
        </div>
      {/each}
    </div>
  {:else}
    <h1>You Chose: {selectedMeal.name}</h1>
    <p>Cooking Time: {selectedMeal.cookingTime}</p>
    <h2>Ingredients:</h2>
    <ul>
      {#each selectedMeal.ingredients as ingredient}
        <li>{ingredient}</li>
      {/each}
    </ul>
  {/if}
  <script>
    import { onMount } from "svelte";
  
    let dinner = [];
  
    const fetchData = async () => {
      try {
        const response = await fetch("https://jsonplaceholder.typicode.com/posts");
        dinner = await response.json();
      } catch (error) {
        console.error(error);
      }
    };
  
    onMount(async () => {
      await fetchData();
    });
  
    function handleDinnerAdd(event) {
      event.preventDefault();
      const title = event.target.elements.title.value;
      const body = event.target.elements.body.value;
      dinner = [...dinner, { title, body }];
    }
  </script>
  
  <style>
    .form {
      display: flex;
      flex-direction: column;
    }
  
    .form input,
    .form textarea {
      padding: 0.5em;
      margin-bottom: 1em;
    }
  
    .dinner-list {
      list-style: none;
      padding: 0;
      margin: 0;
    }
  
    .dinner-item {
      border: 1px solid #ccc;
      margin-bottom: 1em;
      padding: 1em;
    }
  </style>
  
  <h1>Dinner With Friends</h1>
  
  <form class="form" on:submit|preventDefault={handleDinnerAdd}>
    <input type="text" name="title" placeholder="Title" />
    <textarea name="body" placeholder="Description"></textarea>
    <button type="submit">Add Dinner</button>
  </form>
  
  <ul class="dinner-list">
    {#each dinner as item}
    <li class="dinner-item">
      <h2>{item.title}</h2>
      <p>{item.body}</p>
    </li>
    {/each}
  </ul>
  <script>
    import { onMount } from "svelte";
    import { createDinner, getDinners } from "./api.js";
  
    let dinners = [];
    let newDinner = { name: "", date: "", guests: [] };
  
    onMount(async () => {
      dinners = await getDinners();
    });
  
    function addGuest(guest) {
      newDinner.guests.push(guest);
    }
  
    function resetNewDinner() {
      newDinner = { name: "", date: "", guests: [] };
    }
  
    async function handleCreateDinner() {
      await createDinner(newDinner);
      resetNewDinner();
      dinners = await getDinners();
    }
  </script>
  
  <div class="create-dinner">
    <h2>Create Dinner</h2>
    <div class="form">
      <label for="name">Name:</label>
      <input
        type="text"
        id="name"
        bind:value={newDinner.name}
      />
  
      <label for="date">Date:</label>
      <input
        type="text"
        id="date"
        bind:value={newDinner.date}
      />
  
      <h3>Guests:</h3>
      {#each newDinner.guests as guest (guest.id)}
        <div class="guest">
          <p>{guest}</p>
          <button on:click={() => {
            const index = newDinner.guests.indexOf(guest);
            newDinner.guests.splice(index, 1);
          }}>
            Remove
          </button>
        </div>
      {/each}
      <input type="text" id="guest-input" />
      <button on:click={() => addGuest(document.getElementById("guest-input").value)}>
        Add Guest
      </button>
  
      <button on:click={handleCreateDinner}>Create Dinner</button>
    </div>
  </div>
  
  <div class="dinners">
    <h2>Dinners</h2>
    {#each dinners as dinner}
      <div class="dinner">
        <h3>{dinner.name}</h3>
        <p>Date: {dinner.date}</p>
        <h4>Guests:</h4>
        <ul>
          {#each dinner.guests as guest}
            <li>{guest}</li>
          {/each}
        </ul>
      </div>
    {/each}
  </div>
  <script>
    import { onMount } from 'svelte';
  
    let friends = [];
    let selectedFriend = {};
    let selectedDinner = {};
    let dinners = [];
  
    onMount(async () => {
      const response = await fetch('https://my-json-server.typicode.com/openai/dinner-with-friends/friends');
      friends = await response.json();
      console.log(friends);
    });
  
    function selectFriend(friend) {
      selectedFriend = friend;
      selectedDinner = {};
      dinners = friend.dinners;
    }
  
    function selectDinner(dinner) {
      selectedDinner = dinner;
    }
  </script>
import { Router, Route } from "svelte-routing";

<Router>
  <Route path="/" component={Home} />
  <Route path="/add" component={Add} />
  <Route path="/edit/:id" component={Edit} />
</Router>
<script>
    let selectedFriend = {};

    function updateSelectedFriend(friend) {
        selectedFriend = friend;
    }
</script>

<h2>Friend Details</h2>
{#if selectedFriend}
    <div>
        <p>Name: {selectedFriend.name}</p>
        <p>Email: {selectedFriend.email}</p>
        <p>Phone: {selectedFriend.phone}</p>
        <p>Address: {selectedFriend.address}</p>
        <p>Notes: {selectedFriend.notes}</p>
    </div>
{:else}
    <p>Please select a friend from the list.</p>
{/if}
<script>
    import { onMount } from 'svelte';
  
    let friends = [
      { name: 'John', food: 'Pizza' },
      { name: 'Jane', food: 'Salad' },
      { name: 'Jim', food: 'Steak' },
      { name: 'Jill', food: 'Vegetables' },
    ];
  
    let selectedFriend;
  
    onMount(async () => {
      //fetch data from API and set friends array
      try {
        const res = await fetch('https://my-json-server.typicode.com/<user-name>/<repo-name>/friends');
        const data = await res.json();
        friends = data;
      } catch (err) {
        console.error(err);
      }
    });
  
    function handleSelectFriend(friend) {
      selectedFriend = friend;
    }
  </script>
  
  {#if selectedFriend}
    <div class="selected-friend">
      <h3>{selectedFriend.name}</h3>
      <p>Food choice: {selectedFriend.food}</p>
    </div>
  {/if}
  
  <ul>
    {#each friends as friend (friend.name)}
      <li on:click={() => handleSelectFriend(friend)}>
        {friend.name}
      </li>
    {/each}
  </ul>
  <script>
    import { onMount } from 'svelte';
  
    let friends = [];
  
    onMount(async () => {
      const res = await fetch('https://jsonplaceholder.typicode.com/users');
      friends = await res.json();
    });
  
    function addFriend() {
      friends = [...friends, { id: Math.random(), name: 'New friend' }];
    }
  </script>
  
  <style>
    table {
      border-collapse: collapse;
      width: 100%;
    }
  
    th, td {
      border: 1px solid black;
      padding: 8px;
      text-align: left;
    }
  
    th {
      background-color: #f2f2f2;
    }
  
    button {
      background-color: lightgray;
      border: none;
      padding: 8px 16px;
      cursor: pointer;
    }
  </style>
  
  <h1>Dinner with friends</h1>
  
  <button on:click={addFriend}>Add friend</button>
  
  <table>
    <thead>
      <tr>
        <th>Name</th>
        <th>Email</th>
        <th>Actions</th>
      </tr>
    </thead>
    <tbody>
      {#each friends as friend}
        <tr>
          <td>{friend.name}</td>
          <td>{friend.email}</td>
          <td>
            <button>Invite to dinner</button>
          </td>
        </tr>
      {/each}
    </tbody>
  </table>
  <script>
    export let friends;
  
    let newFriend = {
      name: "",
      email: "",
      dish: ""
    };
  
    function addFriend() {
      friends = [...friends, newFriend];
      newFriend = { name: "", email: "", dish: "" };
    }
  
    function removeFriend(friend) {
      friends = friends.filter(f => f !== friend);
    }
  </script>
  
  <style>
    form {
      display: flex;
      flex-wrap: wrap;
      justify-content: space-between;
      align-items: center;
      margin: 1rem 0;
    }
  
    input[type="text"],
    input[type="email"] {
      width: calc(33.33% - 1rem);
      padding: 0.5rem;
      font-size: 1rem;
      margin-bottom: 1rem;
    }
  
    input[type="submit"] {
      width: calc(33.33% - 1rem);
      padding: 1rem;
      background-color: palevioletred;
      color: white;
      border: none;
      border-radius: 5px;
      font-size: 1rem;
      cursor: pointer;
    }
  
    .friend {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 1rem;
      border-bottom: 1px solid lightgray;
      font-size: 1rem;
    }
  
    .friend-name {
      width: 33.33%;
    }
  
    .friend-email {
      width: 33.33%;
    }
  
    .friend-dish {
      width: 33.33%;
    }
  
    button {
      padding: 0.5rem 1rem;
      background-color: palevioletred;
      color: white;
      border: none;
      border-radius: 5px;
      font-size: 1rem;
      cursor: pointer;
    }
  </style>
<script>
    import { onMount } from 'svelte';
    import { Router, Route } from 'svelte-router';
    import Nav from './Nav.svelte';
    import Home from './Home.svelte';
    import PlanDinner from './PlanDinner.svelte';
    import ViewDinners from './ViewDinners.svelte';
  
    let dinners = [];
  
    onMount(async () => {
      const res = await fetch('/api/dinners');
      dinners = await res.json();
    });
  
    function addDinner(dinner) {
      dinners = [...dinners, dinner];
    }
  
    function updateDinner(updatedDinner) {
      dinners = dinners.map(dinner => {
        if (dinner.id === updatedDinner.id) {
          return updatedDinner;
        }
        return dinner;
      });
    }
  
    function deleteDinner(id) {
      dinners = dinners.filter(dinner => dinner.id !== id);
    }
  </script>
  
  <Router>
    <Nav />
  
    <Route path="/" component={Home} />
    <Route path="/plan-dinner" component={PlanDinner} />
    <Route path="/view-dinners" component={ViewDinners} />
  </Router>
  <script>
    export let dinner;
  
    function handleSave() {
      // dispatch the save event to the parent component
      dispatch("save", { dinner });
    }
  </script>
  
  <h2>{dinner.name}</h2>
  <p>{dinner.description}</p>
  <p>
    <b>Date:</b> {dinner.date}
  </p>
  <p>
    <b>Guests:</b> {dinner.guests.join(", ")}
  </p>
  <button on:click={handleSave}>Save</button>
  <script>
    let friends = [
      { id: 1, name: "Alice", email: "alice@example.com" },
      { id: 2, name: "Bob", email: "bob@example.com" },
      { id: 3, name: "Charlie", email: "charlie@example.com" }
    ];
  
    let selectedFriend;
  
    function askFriendToBringPlate() {
      if (!selectedFriend) {
        alert("Please select a friend first");
        return;
      }
  
      const plate = prompt("What plate would you like this friend to bring?");
      if (!plate) {
        return;
      }
  
      alert(`An email has been sent to ${selectedFriend.name} asking them to bring ${plate}`);
    }
  </script>
  
  <h2>Friends</h2>
  <ul>
    {#each friends as friend (friend.id)}
      <li>
        <label>
          <input type="radio" bind:group="selectedFriend" bind:value="friend" />
          {friend.name} ({friend.email})
        </label>
      </li>
    {/each}
  </ul>
  
  <button on:click="askFriendToBringPlate">Ask friend to bring plate</button>
  <script>
    let friends = [];
    let plates = [];
    let drinks = [];
  
    function addFriend(event) {
      friends = [...friends, event.target.value];
      event.target.value = '';
    }
  
    function askFriendToBringPlate(friend) {
      plates = [...plates, `${friend} will bring a plate.`];
    }
  
    function setDrinks(event) {
      drinks = [...drinks, event.target.value];
      event.target.value = '';
    }
  </script>
  
  <h3>Add Friends</h3>
  <input type="text" bind:value={friend} on:keyup={addFriend} placeholder="Enter a friend's name"/>
  
  <h3>Ask Friend to Bring Plate</h3>
  <select bind:value={selectedFriend} on:change={() => askFriendToBringPlate(selectedFriend)}>
    {#each friends as friend}
      <option value={friend}>{friend}</option>
    {/each}
  </select>
  
  <h3>Set Drinks Menu</h3>
  <input type="text" bind:value={drink} on:keyup={setDrinks} placeholder="Enter a drink"/>
  
  <h3>Friends</h3>
  <ul>
    {#each friends as friend}
      <li>{friend}</li>
    {/each}
  </ul>
  
  <h3>Plates</h3>
  <ul>
    {#each plates as plate}
      <li>{plate}</li>
    {/each}
  </ul>
  
  <h3>Drinks</h3>
  <ul>
    {#each drinks as drink}
      <li>{drink}</li>
    {/each}
  </ul>
  <script>
    let selectedOption = 'Regular';
  
    function handleOptionSelection(event) {
      selectedOption = event.target.value;
    }
  </script>
  
  <label>
    <input type="radio" name="mealOption" value="Regular" checked on:change={handleOptionSelection} />
    Regular
  </label>
  <br />
  <label>
    <input type="radio" name="mealOption" value="Pescatarian" on:change={handleOptionSelection} />
    Pescatarian
  </label>
  <br />
  <label>
    <input type="radio" name="mealOption" value="Vegetarian" on:change={handleOptionSelection} />
    Vegetarian
  </label>
  <br />
  <label>
    <input type="radio" name="mealOption" value="Vegan" on:change={handleOptionSelection} />
    Vegan
  </label>
  function selectDessert() {
    let dessertOption = prompt("What dessert would you like to have? \n 1. Fruit Salad \n 2. Cheesecake \n 3. Ice Cream \n 4. Tiramisu");
  
    switch (dessertOption) {
      case "1":
        dessertOption = "Fruit Salad";
        break;
      case "2":
        dessertOption = "Cheesecake";
        break;
      case "3":
        dessertOption = "Ice Cream";
        break;
      case "4":
        dessertOption = "Tiramisu";
        break;
      default:
        dessertOption = "Invalid Option";
    }
  
    alert(`You have selected ${dessertOption} for dessert.`);
  }
  <script>
    let playlist = [];
    let trackName = '';
    let source = 'Apple Music';
  
    function addTrack() {
      playlist.push({ name: trackName, source });
      trackName = '';
    }
  
    function removeTrack(index) {
      playlist.splice(index, 1);
    }
  </script>
  
  <h3>Music Playlist</h3>
  <div>
    <label for="trackName">Track Name:</label>
    <input id="trackName" type="text" bind:value={trackName} />
  
    <label for="source">Source:</label>
    <select id="source" bind:value={source}>
      <option value="Apple Music">Apple Music</option>
      <option value="Spotify">Spotify</option>
    </select>
  
    <button on:click={addTrack}>Add Track</button>
  </div>
  
  <ul>
    {#each playlist as track, index}
      <li>
        {track.name} ({track.source})
        <button on:click={() => removeTrack(index)}>x</button>
      </li>
    {/each}
  </ul>
  <script>
    let shoppingList = [];
  
    function addItem(item) {
      shoppingList = [...shoppingList, item];
    }
  
    function removeItem(index) {
      shoppingList = [...shoppingList.slice(0, index), ...shoppingList.slice(index + 1)];
    }
  
    function orderOnline() {
      // Code to order the items online
      alert(`Ordering ${shoppingList.join(", ")} from the grocery store`);
    }
  </script>
  
  <h2>Shopping List</h2>
  <ul>
    {#each shoppingList as item, index}
      <li>
        {item}
        <button on:click={() => removeItem(index)}>x</button>
      </li>
    {/each}
  </ul>
  
  <input type="text" bind:value={newItem} />
  <button on:click={() => addItem(newItem)}>Add Item</button>
  
  <button on:click={orderOnline}>Order Online</button>
  <script>
    let theme = 'dark';
  
    function switchTheme() {
      if (theme === 'dark') {
        theme = 'light';
      } else {
        theme = 'dark';
      }
    }
  </script>
  
  <style>
    .app {
      background-color: {theme === 'dark' ? '#000000' : '#FFFFFF'};
      color: {theme === 'dark' ? '#FFFFFF' : '#000000'};
    }
  
    .highlight {
      color: {theme === 'dark' ? '#FFFF00' : '#000000'};
    }
  </style>
  
  <div class="app">
    <button class="highlight" on:click={switchTheme}>Switch Theme</button>
  </div>
  <script>
    let socialShareMessage = "Join me for a dinner party using the Dinner With Friends app!";
  
    function shareOnFacebook() {
      window.open(`https://www.facebook.com/sharer/sharer.php?u=${encodeURIComponent(socialShareMessage)}`);
    }
  
    function shareOnTwitter() {
      window.open(`https://twitter.com/intent/tweet?text=${encodeURIComponent(socialShareMessage)}`);
    }
  
    function shareOnInstagram() {
      window.open(`https://www.instagram.com/?url=${encodeURIComponent(socialShareMessage)}`);
    }
  </script>
  
  <button on:click={shareOnFacebook}>
    Share on Facebook
  </button>
  
  <button on:click={shareOnTwitter}>
    Share on Twitter
  </button>
  
  <button on:click={shareOnInstagram}>
    Share on Instagram
  </button>
  <script>
    let reminders = [];
  
    function addReminder(event) {
      const reminder = {
        text: event.target.value,
        id: Date.now()
      };
  
      reminders = [...reminders, reminder];
      event.target.value = '';
    }
  
    function deleteReminder(id) {
      reminders = reminders.filter(r => r.id !== id);
    }
  </script>
  
  <h3>Reminders</h3>
  
  <div>
    <input type="text" placeholder="Add a reminder" on:input={addReminder} />
  </div>
  
  <ul>
    {#each reminders as reminder}
      <li>
        {reminder.text}
        <button on:click={() => deleteReminder(reminder.id)}>x</button>
      </li>
    {/each}
  </ul>
  <script>
    import { onMount } from "svelte";
    
    let guests = [];
  
    onMount(async () => {
      const storedGuests = localStorage.getItem("guests");
      if (storedGuests) {
        guests = JSON.parse(storedGuests);
      }
    });
  
    function addGuest(guest) {
      guests = [...guests, guest];
      localStorage.setItem("guests", JSON.stringify(guests));
    }
  
    function updateGuest(index, updatedGuest) {
      guests = guests.map((guest, i) => {
        if (i === index) {
          return updatedGuest;
        }
        return guest;
      });
      localStorage.setItem("guests", JSON.stringify(guests));
    }
  
    function deleteGuest(index) {
      guests = guests.filter((_, i) => i !== index);
      localStorage.setItem("guests", JSON.stringify(guests));
    }
  </script>
  
  <form on:submit|preventDefault={() => addGuest({ name, mealPreference, drinkPreference, musicPlaylist, plateAssignment })}>
    <input type="text" bind:value={name} placeholder="Name" />
    <input type="text" bind:value={mealPreference} placeholder="Meal Preference" />
    <input type="text" bind:value={drinkPreference} placeholder="Drink Preference" />
    <input type="text" bind:value={musicPlaylist} placeholder="Music Playlist" />
    <input type="text" bind:value={plateAssignment} placeholder="Plate Assignment" />
    <button type="submit">Add Guest</button>
  </form>
  
  <table>
    <thead>
      <tr>
        <th>Name</th>
        <th>Meal Preference</th>
        <th>Drink Preference</th>
        <th>Music Playlist</th>
        <th>Plate Assignment</th>
        <th>Actions</th>
      </tr>
    </thead>
    <tbody>
      {#each guests as guest, i}
        <tr>
          <td>{guest.name}</td>
          <td>{guest.mealPreference}</td>
          <td>{guest.drinkPreference}</td>
          <td>{guest.musicPlaylist}</td>
          <td>{guest.plateAssignment}</td>
          <td>
            <button on:click={() => deleteGuest(i)}>Delete</button>
            <button on:click={() => {
              const updatedGuest = {...guest};
              updatedGuest.name = prompt("Enter name", guest.name);
              updatedGuest.mealPreference = prompt("Enter meal preference", guest.mealPreference);
              updatedGuest.drinkPreference = prompt("Enter drink preference", guest.drinkPreference);
              updatedGuest.musicPlaylist = prompt("Enter music playlist", guest.musicPlaylist);
              updatedGuest.plateAssignment = prompt("Enter plate assignment", guest.plateAssignment);
              updateGuest
              <script>
  let messages = [];

  function addMessage(text) {
    messages = [...messages, { id: Math.random(), text }];
  }
</script>

<style>
  .chat {
    height: 300px;
    overflow-y: scroll;
    padding: 10px;
  }

  .message {
    margin-bottom: 10px;
  }
</style>

<div class="chat">
  {#each messages as message}
    <div class="message">{message.text}</div>
  {/each}
</div>

<input bind:value={newMessage} placeholder="Type your message here..." />
<button on:click={() => addMessage(newMessage)}>Send</button>
<script>
  import { onMount } from 'svelte';
  let recipes = [];

  onMount(async () => {
    // fetch recipes from API or database
    const response = await fetch('https://your-api.com/recipes');
    recipes = await response.json();
  });

  function filterRecipes(mealType, dietaryRestriction) {
    return recipes.filter(recipe => {
      return recipe.mealType === mealType && recipe.dietaryRestriction === dietaryRestriction;
    });
  }
</script>

<h2>Recipe Database</h2>
<div>
  <label for="meal-type">Meal Type:</label>
  <select id="meal-type" bind:value={mealType}>
    <option value="">All</option>
    <option value="breakfast">Breakfast</option>
    <option value="lunch">Lunch</option>
    <option value="dinner">Dinner</option>
    <option value="dessert">Dessert</option>
  </select>
  <label for="dietary-restriction">Dietary Restriction:</label>
  <select id="dietary-restriction" bind:value={dietaryRestriction}>
    <option value="">All</option>
    <option value="vegetarian">Vegetarian</option>
    <option value="vegan">Vegan</option>
    <option value="pescatarian">Pescatarian</option>
  </select>
</div>
<ul>
  {#each filterRecipes(mealType, dietaryRestriction) as recipe}
    <li>
      <h3>{recipe.title}</h3>
      <p>Meal Type: {recipe.mealType}</p>
      <p>Dietary Restriction: {recipe.dietaryRestriction}</p>
      <p>Ingredients: {recipe.ingredients}</p>
    </li>
  {/each}
</ul>
<script>
  import { onMount } from 'svelte';
  import { loadPaypalScript } from './paypal.js';

  let paypalLoaded = false;

  onMount(async () => {
    paypalLoaded = await loadPaypalScript();
  });

  function checkout() {
    paypal.Buttons({
      createOrder: function(data, actions) {
        return actions.order.create({
          purchase_units: [{
            amount: {
              value: '0.01'
            }
          }]
        });
      },
      onApprove: function(data, actions) {
        return actions.order.capture().then(function(details) {
          alert('Transaction completed by ' + details.payer.name.given_name);
        });
      }
    }).render('#paypal-button-container');
  }
</script>

{#if paypalLoaded}
  <div id="paypal-button-container"></div>
  <button on:click={checkout}>Checkout</button>
{/if}
<script>
  import { onMount } from 'svelte';
  import { createUser, loginUser, logoutUser } from './api.js';

  let errorMessage = '';
  let email = '';
  let password = '';
  let isLoading = false;
  let isLoggedIn = false;

  onMount(async () => {
    // Check if the user is already logged in
    const token = localStorage.getItem('token');
    if (token) {
      isLoggedIn = true;
    }
  });

  const handleLogin = async (e) => {
    e.preventDefault();
    isLoading = true;
    try {
      const res = await loginUser(email, password);
      localStorage.setItem('token', res.token);
      isLoading = false;
      isLoggedIn = true;
      errorMessage = '';
    } catch (error) {
      errorMessage = error.message;
      isLoading = false;
    }
  };

  const handleRegistration = async (e) => {
    e.preventDefault();
    isLoading = true;
    try {
      const res = await createUser(email, password);
      localStorage.setItem('token', res.token);
      isLoading = false;
      isLoggedIn = true;
      errorMessage = '';
    } catch (error) {
      errorMessage = error.message;
      isLoading = false;
    }
  };

  const handleLogout = () => {
    localStorage.removeItem('token');
    isLoggedIn = false;
  };
</script>

{#if !isLoggedIn}
  <form on:submit|preventDefault={handleLogin}>
    <input type="email" bind:value={email} placeholder="Email" />
    <input type="password" bind:value={password} placeholder="Password" />
    {#if errorMessage}
      <p>{errorMessage}</p>
    {:else}
      <button type="submit" disabled={isLoading}>
        Login
      </button>
      <button type="button" on:click={handleRegistration} disabled={isLoading}>
        Register
      </button>
    {/if}
  </form>
{:else}
  <button type="button" on:click={handleLogout}>
    Logout
  </button>
{/if}
<script>
  let inviteModal = false;
  let reminderModal = false;

  function toggleInviteModal() {
    inviteModal = !inviteModal;
  }

  function toggleReminderModal() {
    reminderModal = !reminderModal;
  }

  function sendInvitation(eventId) {
    // send invitation logic here
  }

  function sendReminder(eventId) {
    // send reminder logic here
  }
</script>

<button on:click={toggleInviteModal}>Invite Friends</button>
<button on:click={toggleReminderModal}>Send Reminder</button>

{#if inviteModal}
  <div class="modal">
    <h3>Invite Friends to Event</h3>
    <input type="text" placeholder="Enter friend's email" />
    <button on:click={() => sendInvitation(eventId)}>Send Invitation</button>
    <button on:click={toggleInviteModal}>Close</button>
  </div>
{/if}

{#if reminderModal}
  <div class="modal">
    <h3>Send Reminder to Attendees</h3>
    <input type="text" placeholder="Enter reminder message" />
    <button on:click={() => sendReminder(eventId)}>Send Reminder</button>
    <button on:click={toggleReminderModal}>Close</button>
  </div>
{/if}
<script>
  let eventId;
  let eventMenu = [];

  function addDish(dish) {
    eventMenu.push(dish);
  }

  function assignDish(dish, attendee) {
    let foundDish = eventMenu.find(menuItem => menuItem.name === dish);
    foundDish.assignedTo = attendee;
  }

  async function saveEventMenu() {
    // Save the menu to the database
    try {
      let response = await fetch(`/api/events/${eventId}/menu`, {
        method: 'PUT',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ menu: eventMenu })
      });
      let data = await response.json();
      console.log(data);
    } catch (error) {
      console.error(error);
    }
  }
</script>

<h3>Menu Management</h3>
<form on:submit|preventDefault={saveEventMenu}>
  <label>
    Dish:
    <input type="text" bind:value={dish} />
  </label>
  <button type="button" on:click={() => addDish({ name: dish })}>Add Dish</button>
  <br />
  <br />
  <table>
    <thead>
      <tr>
        <th>Dish</th>
        <th>Assigned To</th>
      </tr>
    </thead>
    <tbody>
      {#each eventMenu as dish}
        <tr>
          <td>{dish.name}</td>
          <td>
            <input type="text" bind:value={dish.assignedTo} />
            <button type="button" on:click={() => assignDish(dish.name, dish.assignedTo)}>Assign</button>
          </td>
        </tr>
      {/each}
    </tbody>
  </table>
  <button type="submit">Save Menu</button>
</form>
<script>
  let events = []; // array to store all the events

  function addEvent(event) {
    events = [...events, event]; // add a new event to the events array
  }

  function editEvent(index, event) {
    events = [
      ...events.slice(0, index), // keep the events before the event to be edited
      event, // replace the event to be edited with the updated event
      ...events.slice(index + 1) // keep the events after the event to be edited
    ];
  }
</script>

<h2>Event Management</h2>

<!-- form to add a new event -->
<form on:submit|preventDefault={e => {
    const formData = new FormData(e.target);
    const newEvent = {
      date: formData.get("date"),
      location: formData.get("location"),
      guests: formData.get("guests").split(",")
    };
    addEvent(newEvent);
  }}>
  <input type="text" name="date" placeholder="Date" />
  <input type="text" name="location" placeholder="Location" />
  <input type="text" name="guests" placeholder="Guests (comma separated)" />
  <button type="submit">Add Event</button>
</form>

<!-- table to display all events -->
<table>
  <thead>
    <tr>
      <th>Date</th>
      <th>Location</th>
      <th>Guests</th>
      <th>Actions</th>
    </tr>
  </thead>
  <tbody>
    {#each events as event (event.date)}
      <tr>
        <td>{event.date}</td>
        <td>{event.location}</td>
        <td>{event.guests.join(", ")}</td>
        <td>
          <!-- button to edit an event -->
          <button on:click={() => {
              const index = events.indexOf(event);
              // create a form with the current event data pre-filled
              const form = new FormData();
              form.append("date", event.date);
              form.append("location", event.location);
              form.append("guests", event.guests.join(","));
              editEvent(index, form);
            }}>
            Edit
          </button>
        </td>
      </tr>
    {/each}
  </tbody>
</table>
<script>
  let eventId;
  let rating;
  let comment;

  function handleSubmit() {
    // logic to send the feedback to the server
    console.log("Event ID: ", eventId);
    console.log("Rating: ", rating);
    console.log("Comment: ", comment);
  }
</script>
<h2>Event Feedback</h2>
<form on:submit|preventDefault={handleSubmit}>
  <input type="hidden" bind:value={eventId} />
  <label for="rating">Rating:</label>
  <select id="rating" bind:value={rating}>
    <option value="1">1</option>
    <option value="2">2</option>
    <option value="3">3</option>
    <option value="4">4</option>
    <option value="5">5</option>
  </select>
  <br />
  <label for="comment">Comment:</label>
  <textarea id="comment" bind:value={comment}></textarea>
  <br />
  <button type="submit">Submit Feedback</button>
</form>
<script>
  let futureEvents = [];

  function addEvent(event) {
    futureEvents.push(event);
  }

  function removeEvent(event) {
    futureEvents = futureEvents.filter(e => e !== event);
  }

  function editEvent(event, newEvent) {
    let index = futureEvents.indexOf(event);
    futureEvents[index] = newEvent;
  }
</script>

<h2>Meal Planning</h2>

<h3>Future Events</h3>
<ul>
  {#each futureEvents as event}
    <li>{event.name} - {event.date}</li>
  {/each}
</ul>

<h3>Add Event</h3>
<input type="text" bind:value={eventName} placeholder="Event Name" />
<input type="date" bind:value={eventDate} placeholder="Event Date" />
<button on:click={() => addEvent({ name: eventName, date: eventDate })}>
  Add Event
</button>

<h3>Edit Event</h3>
<input type="text" bind:value={editEventName} placeholder="Event Name" />
<input type="date" bind:value={editEventDate} placeholder="Event Date" />
<button on:click={() => editEvent({ name: editEventName, date: editEventDate })}>
  Edit Event
</button>

<h3>Remove Event</h3>
<input type="text" bind:value={removeEventName} placeholder="Event Name" />
<button on:click={() => removeEvent({ name: removeEventName, date: removeEventDate })}>
  Remove Event
</button>
<script>
  import { onMount } from "svelte";
  
  let recipes = [];
  
  onMount(async () => {
    // Fetch recipes from a database or API
    const response = await fetch("https://your-api.com/recipes");
    recipes = await response.json();
  });
  
  function addRecipe(recipe) {
    // Add the new recipe to the list and persist it to the database or API
    recipes = [...recipes, recipe];
    fetch("https://your-api.com/recipes", {
      method: "POST",
      body: JSON.stringify(recipe)
    });
  }
</script>

<h1>Food and Recipe Sharing</h1>

<h2>Discover Recipes</h2>
<ul>
  {#each recipes as recipe}
    <li>{recipe.name}</li>
  {/each}
</ul>

<h2>Add a Recipe</h2>
<form on:submit|preventDefault={() => addRecipe({ name: recipeName, ingredients: recipeIngredients, instructions: recipeInstructions })}>
  <label for="recipeName">Recipe Name:</label>
  <input type="text" id="recipeName" bind:value={recipeName} />
  
  <label for="recipeIngredients">Ingredients:</label>
  <input type="text" id="recipeIngredients" bind:value={recipeIngredients} />
  
  <label for="recipeInstructions">Instructions:</label>
  <textarea id="recipeInstructions" bind:value={recipeInstructions}></textarea>
  
  <button type="submit">Add Recipe</button>
</form>
<script>
  let groups = [];

  function addGroup(groupName) {
    groups.push({ name: groupName, members: [], events: [] });
  }

  function removeGroup(groupIndex) {
    groups.splice(groupIndex, 1);
  }

  function addMember(groupIndex, memberName) {
    groups[groupIndex].members.push(memberName);
  }

  function removeMember(groupIndex, memberIndex) {
    groups[groupIndex].members.splice(memberIndex, 1);
  }

  function addEvent(groupIndex, event) {
    groups[groupIndex].events.push(event);
  }

  function removeEvent(groupIndex, eventIndex) {
    groups[groupIndex].events.splice(eventIndex, 1);
  }

  function viewEvents(groupIndex) {
    return groups[groupIndex].events;
  }

  function viewRSVPs(groupIndex, eventIndex) {
    return groups[groupIndex].events[eventIndex].rsvps;
  }
</script>

<h1>Group Management</h1>

<h2>Groups</h2>
<ul>
  {#each groups as group, i}
    <li>
      <b>{group.name}</b>
      <ul>
        <h3>Members</h3>
        <ul>
          {#each group.members as member}
            <li>{member}</li>
          {/each}
        </ul>
        <h3>Events</h3>
        <ul>
          {#each group.events as event, j}
            <li>{event.name} ({event.date})
              <ul>
                <h4>RSVPs</h4>
                <ul>
                  {#each event.rsvps as rsvp}
                    <li>{rsvp.name}: {rsvp.status}</li>
                  {/each}
                </ul>
              </ul>
            </li>
          {/each}
        </ul>
        <button on:click={() => removeGroup(i)}>Remove Group</button>
        <button on:click={() => addMember(i, prompt('Enter member name'))}>Add Member</button>
        <button on:click={() => removeMember(i, prompt('Enter member index'))}>Remove Member</button>
        <button on:click={() => addEvent(i, { name: prompt('Enter event name'), date: prompt('Enter event date'), rsvps: [] })}>Add Event</button>
        <button on:click={() => removeEvent(i, prompt('Enter event index'))}>Remove Event</button>
      </ul>
    </li>
  {/each}
</ul>
<button on:click={() => addGroup(prompt('Enter group name'))}>Add Group</button>
<script>
    import { onMount } from 'svelte';
    let permissionGranted = false;

    onMount(async () => {
        if ('serviceWorker' in navigator && 'PushManager' in window) {
            const registration = await navigator.serviceWorker.ready;
            permissionGranted = await registration.pushManager.permissionState({ userVisibleOnly: true });

            if (permissionGranted === 'denied') {
                console.log('Permission for notifications was denied');
            } else if (permissionGranted === 'default') {
                permissionGranted = await registration.pushManager.requestPermission();
                console.log('Permission for notifications was granted');
            } else {
                console.log('Notification permission was already granted');
            }
        } else {
            console.log('Push messaging is not supported');
        }
    });

    function sendNotification(event, title, options) {
        if (!permissionGranted) {
            console.log('Notification permission was denied');
            return;
        }

        event.waitUntil(
            self.registration.showNotification(title, options)
        );
    }
</script>


         