<script>
	import Timer from '$lib/components/Timer.svelte';

	let names = [
		'Μελίνα',
		'Αρτέμης',
		'Έφη',
		'Βάσος',
		'Φρύνη',
		'Σάββας',
		'Σώτος',
		'Αχιλλέας'
	];

	let defaultMinutes = 10;
	let defaultSeconds = 0;
	let timers = [];

	function resetAllTimers() {
		for (const t of timers) {
			if (t && t.resetTo) {
				t.resetTo(defaultMinutes, defaultSeconds);
			}
		}
	}

	let showGearOverlay = false;

	// Editing logic for names
	let editingIndex = null;
	let editedName = '';
	let newName = '';

	function startEditName(index) {
		editingIndex = index;
		editedName = names[index];
	}

	function saveEditName() {
		if (editedName.trim()) {
			names[editingIndex] = editedName.trim();
			names = [...names];
		}
		editingIndex = null;
	}

	function deleteName(index) {
		names.splice(index, 1);
		names = [...names];
	}

	function addNewName() {
		if (newName.trim()) {
			names = [...names, newName.trim()];
			newName = '';
		}
	}

	function moveNameUp(index) {
		if (index > 0) {
			const temp = names[index];
			names[index] = names[index - 1];
			names[index - 1] = temp;
			names = [...names];
		}
	}

	function moveNameDown(index) {
		if (index < names.length - 1) {
			const temp = names[index];
			names[index] = names[index + 1];
			names[index + 1] = temp;
			names = [...names];
		}
	}
</script>

<style>
    :global(:root) {
        --border-radius: 20px;
    }


    h1 {
        color: white;
        text-align: center;
        font-size: 3rem;
        margin-top: 2rem;
        text-shadow: 0 0 2px black;
        /*text-decoration: underline;*/
    }

    .top-bar {
        display: flex;
        justify-content: space-between;
        align-items: center;
    }


    .top-controls {
        display: flex;
        justify-content: flex-end;
        align-items: center;
        padding: 1rem;
        position: relative;
    }

    .top-controls .inputs {
        display: flex;
        align-items: center;
        gap: 0.5rem;
    }

    .top-controls label {
        color: white;
        text-shadow: 0 0 1px black;
        font-weight: bold;
    }

    .top-controls input {
        width: 50px;
        padding: 0.3rem;
        border: 1px solid #502379;
        background: black;
        color: white;
        border-radius: var(--border-radius);
        text-align: center;
    }

    .top-controls button {
        background: black;
        color: white;
        border: 1px solid #502379;
        border-radius: var(--border-radius);
        padding: 0.3rem 0.5rem;
        cursor: pointer;
        font-weight: bold;
        text-shadow: 0 0 1px black;
        margin-left: 1rem;
    }

    .top-controls button:hover {
        background: #1d1d1d;
    }

    .gear-button {
        background: black;
        color: white;
        border: 1px solid #502379;
        border-radius: var(--border-radius);
        padding: 0.3rem;
        margin-left: 0.5rem;
        cursor: pointer;
        font-size: 1.2rem;
    }

    .gear-button:hover {
        background: #1d1d1d;
    }

    .container {
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
        gap: 1rem;
        padding: 1rem;
        justify-items: center;
    }

    /* Overlay for name management */
    .overlay {
        position: fixed;
        top: 0; left: 0; right: 0; bottom: 0;
        background: rgba(0,0,0,0.7);
        display: flex;
        justify-content: center;
        align-items: center;
        z-index: 100000;
    }

    .overlay-box {
        background: #2b1240;
        border: 2px solid #502379;
        border-radius: var(--border-radius);
        padding: 1.5rem;
        color: white;
        text-shadow: 0 0 1px black;
        display: flex;
        flex-direction: column;
        gap: 1rem;
        width: 300px;
        max-height: 80vh;
        overflow-y: auto;
    }

    .overlay-box h2 {
        margin: 0;
        font-size: 1.5rem;
        text-align: center;
        text-decoration: underline;
    }

    .name-item {
        display: flex;
        align-items: center;
        gap: 0.5rem;
        flex-wrap: wrap;
    }

    .name-item span {
        flex: 1;
    }

    .name-item input {
        flex: 1;
        padding: 0.3rem;
        border: 1px solid #502379;
        background: black;
        color: white;
        border-radius: var(--border-radius);
    }

    .name-item button {
        background: black;
        color: white;
        border: 1px solid #502379;
        border-radius: var(--border-radius);
        padding: 0.3rem 0.5rem;
        cursor: pointer;
        font-weight: bold;
        font-size: 0.8rem;
    }

    .name-item button:hover {
        background: #1d1d1d;
    }

    .new-name {
        display: flex;
        gap: 0.5rem;
    }

    .new-name input {
        flex: 1;
        padding: 0.3rem;
        border: 1px solid #502379;
        background: black;
        color: white;
        border-radius: var(--border-radius);
    }

    .overlay-box .actions {
        display: flex;
        justify-content: space-between;
    }

    .footer {
        text-align: center;
        padding: 1rem;
        color: white;
        text-shadow: 0 0 1px black;
        font-size: 0.9rem;
    }

    .logo-title {
        display: flex;
        align-items: center;
        gap: 1rem;
        padding-left: 2rem;
        flex-direction: row; /* Default layout */
        text-align: left;
    }

    @media (max-width: 800px) { /* Adjust the breakpoint as needed */
        .logo-title {
            flex-direction: column; /* Stack the elements vertically */
            align-items: center; /* Center align the logo and text */
            gap: 0.5rem; /* Adjust spacing for smaller screens */
            text-align: center; /* Center the text */
        }

        .logo-title h1 {
            font-size: 2rem; /* Optional: Adjust font size for better fit */
        }

        .logo {
            height: 40px; /* Optional: Adjust logo size for smaller screens */
        }
    }


    .logo {
        height: 50px;
        width: auto;
        /*background: white; !* White background behind the logo *!*/
        border-radius: 10px;
        padding: 0.03rem; /* Optional padding to visually separate the logo from the background edges */
    }

    .highlighted-link {
        color: #ff2557; /* Highlight color */
				opacity: 0.3;
        /*text-decoration: underline; !* Underline the text *!*/
        font-weight: bold; /* Make it bold for emphasis */
    }

    .highlighted-link:hover {
        color: #af6cbd; /* Slightly darker color on hover */
        text-decoration: none; /* Optional: remove underline on hover */
    }
</style>


<main>
	<link href="https://fonts.googleapis.com/css2?family=Ubuntu:wght@400;500;700&display=swap" rel="stylesheet">
	<header class="top-bar">
		<div class="logo-title">
			<img src="/logo.png" alt="Logo" class="logo" />
			<h1>debate</h1>
		</div>
		<div class="top-controls">
			<div class="inputs">
				<label>Default min:</label>
				<input type="number" bind:value={defaultMinutes} min="0" />
				<label>sec:</label>
				<input type="number" bind:value={defaultSeconds} min="0" max="59" />
			</div>
			<button on:click={resetAllTimers}>reset all</button>
			<button class="gear-button" on:click={() => showGearOverlay = true}>⚙️</button>
		</div>
	</header>


	<div class="container">
		{#each names as name, i}
			<Timer
				bind:this={timers[i]}
				initialName={name}
				initialMinutes={defaultMinutes}
				initialSeconds={defaultSeconds}
			/>
		{/each}
	</div>

	{#if showGearOverlay}
		<div class="overlay">
			<div class="overlay-box">
				<h2>Manage Timers</h2>

				{#each names as nm, i}
					{#if editingIndex === i}
						<div class="name-item">
							<input type="text" bind:value={editedName}
										 on:keydown={(e)=>{if(e.key==='Enter') saveEditName(); if(e.key==='Escape') editingIndex=null;}} />
							<button on:click={saveEditName}>Save</button>
							<button on:click={() => editingIndex=null}>Cancel</button>
						</div>
					{:else}
						<div class="name-item">
							<span>{nm}</span>
							<button on:click={() => moveNameUp(i)} disabled={i===0}>↑</button>
							<button on:click={() => moveNameDown(i)} disabled={i===names.length-1}>↓</button>
							<button on:click={() => startEditName(i)}>Edit</button>
							<button on:click={() => deleteName(i)}>Delete</button>
						</div>
					{/if}
				{/each}

				<div class="new-name">
					<input type="text" placeholder="New name" bind:value={newName} on:keydown={(e)=>{if(e.key==='Enter') addNewName();}} />
					<button on:click={addNewName}>Add</button>
				</div>

				<div class="actions">
					<button on:click={() => showGearOverlay = false}>Close</button>
				</div>
			</div>
		</div>
	{/if}

	<footer class="footer">
		made with ❤️ in 🇨🇭 for Volt Cyprus<br />
		source available <a href="https://github.com/andreas16700/multiple_timers" target="_blank" class="highlighted-link">here</a>
	</footer>

</main>
