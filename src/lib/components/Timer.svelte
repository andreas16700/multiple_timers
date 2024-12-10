<style>
    :global(:root) {
        --border-radius: 20px;
        --border-color-active: #d8b3ff; /* Light purple border when active */
    }

    .timer {
        border: 2px solid #502379;
        background: rgba(0,0,0,0.5);
        border-radius: var(--border-radius);
        color: white;
        text-shadow: 0 0 2px black;
        position: relative;
        display: flex;
        flex-direction: column;
        align-items: center;
        padding: 1rem;
        user-select: none;
        width: 250px;
        height: 250px;
        box-sizing: border-box;
        transition: transform 0.3s, border-color 0.3s;
    }

    .timer.active {
        transform: scale(1.15);
        border-color: var(--border-color-active);
    }

    .header {
        width: 100%;
        display: flex;
        justify-content: center;
        align-items: center;
        margin-bottom: 0.5rem;
        position: relative;
    }

    .title {
        font-size: 1.5rem;
        font-weight: bold;
        cursor: default;
        flex: 1;
        text-align: center;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
        border-radius: var(--border-radius);
    }

    /* When active, make the title 25% bigger and bolder */
    .timer.active .title {
        font-size: 1.875rem;
        font-weight: 900;
    }

    .title-edit-overlay {
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        background: rgba(0,0,0,0.8);
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        z-index: 10000;
        border-radius: var(--border-radius);
    }

    .title-edit-box {
        background: #2b1240;
        border: 2px solid #502379;
        border-radius: var(--border-radius);
        padding: 1rem;
        color: white;
        text-shadow: 0 0 1px black;
        display: flex;
        flex-direction: column;
        align-items: stretch;
        gap: 0.5rem;
        width: 200px;
    }

    .title-edit-box input {
        padding: 0.3rem;
        border: 1px solid #502379;
        color: white;
        background: black;
        border-radius: var(--border-radius);
    }

    .time {
        font-size: 2rem;
        font-weight: bold;
        margin: 1rem 0;
    }

    .controls {
        display: flex;
        gap: 0.5rem;
    }

    button {
        background: black;
        color: white;
        border: 1px solid #502379;
        border-radius: var(--border-radius);
        padding: 0.3rem 0.5rem;
        cursor: pointer;
        font-weight: bold;
        text-shadow: 0 0 1px black;
    }

    button:hover {
        background: #1d1d1d;
    }

    .progress-container {
        width: 100%;
        height: 10px;
        background: #aaa;
        border-radius: var(--border-radius);
        margin-top: 0.5rem;
        position: relative;
    }

    .progress-bar {
        height: 100%;
        background: #502379;
        border-radius: var(--border-radius);
    }

    .context-menu {
        position: fixed;
        background: #2b1240;
        border: 1px solid #502379;
        border-radius: var(--border-radius);
        z-index: 9999;
    }

    .context-menu ul {
        list-style: none;
        padding: 0;
        margin: 0;
    }

    .context-menu li {
        padding: 0.5rem;
        color: white;
        cursor: pointer;
        border-bottom: 1px solid #502379;
    }

    .context-menu li:last-child {
        border-bottom: none;
    }

    .context-menu li:hover {
        background: #1b0a30;
        border-radius: var(--border-radius);
    }

    .edit-overlay {
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        background: rgba(0,0,0,0.8);
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        z-index: 10000;
        border-radius: var(--border-radius);
    }

    .edit-box {
        background: #2b1240;
        border: 2px solid #502379;
        border-radius: var(--border-radius);
        padding: 1rem;
        color: white;
        text-shadow: 0 0 1px black;
        display: flex;
        flex-direction: column;
        align-items: stretch;
        gap: 0.5rem;
        width: 200px;
    }

    .edit-box input {
        padding: 0.3rem;
        border: 1px solid #502379;
        color: white;
        background: black;
        border-radius: var(--border-radius);
    }

    .time-adjust {
        display: flex;
        gap: 0.5rem;
        justify-content: center;
        /* Reduced margin to bring closer to the progress bar */
        margin-top: 0.3rem;
    }
</style>

<script>
	import { onMount, onDestroy } from 'svelte';

	export let initialName = 'Timer';
	export let initialMinutes = 5;
	export let initialSeconds = 0;

	let name = initialName;
	let duration = initialMinutes * 60 + initialSeconds;
	let timeRemaining = duration;
	let interval;
	let running = false;

	let showContextMenu = false;
	let contextMenuX = 0;
	let contextMenuY = 0;

	let editingDuration = false;
	let editedMinutes = initialMinutes;
	let editedSeconds = initialSeconds;

	let editingName = false;
	let editedName = name;

	function startPause() {
		if (running) {
			clearInterval(interval);
			running = false;
		} else {
			running = true;
			interval = setInterval(() => {
				if (timeRemaining > 0) {
					timeRemaining -= 1;
				} else {
					clearInterval(interval);
					running = false;
				}
			}, 1000);
		}
	}

	function resetTimer() {
		clearInterval(interval);
		timeRemaining = duration;
		running = false;
	}

	export function resetTo(min, sec) {
		clearInterval(interval);
		duration = min * 60 + sec;
		timeRemaining = duration;
		running = false;
	}

	function add15() {
		timeRemaining += 15;
		if (timeRemaining > 36000) timeRemaining = 36000;
	}

	function subtract15() {
		timeRemaining -= 15;
		if (timeRemaining < 0) timeRemaining = 0;
	}

	function formatTime(seconds) {
		const m = Math.floor(seconds / 60);
		const s = seconds % 60;
		return `${m.toString().padStart(2,'0')}:${s.toString().padStart(2,'0')}`;
	}

	function handleRightClickTimer(event) {
		// Context menu on the timer box
		event.preventDefault();
		if (!editingName && !editingDuration) {
			showContextMenu = true;
			contextMenuX = event.clientX;
			contextMenuY = event.clientY;
		}
	}

	function handleRightClickName(event) {
		// Right-clicking the name triggers name editing
		event.preventDefault();
		editingName = true;
		editedName = name;
	}

	function closeContextMenu() {
		showContextMenu = false;
	}

	function editDuration() {
		closeContextMenu();
		editedMinutes = Math.floor(duration / 60);
		editedSeconds = duration % 60;
		editingDuration = true;
	}

	function saveDuration() {
		const totalSec = editedMinutes * 60 + editedSeconds;
		if (totalSec >= 0) {
			duration = totalSec;
			timeRemaining = duration;
			editingDuration = false;
		}
	}

	function progress() {
		return (timeRemaining / duration) * 100 || 0;
	}

	let windowClickHandler;
	onMount(() => {
		windowClickHandler = () => {
			if (!editingDuration && !editingName) {
				showContextMenu = false;
			}
		};
		window.addEventListener('click', windowClickHandler, { capture: true });
	});

	onDestroy(() => {
		if (windowClickHandler) {
			window.removeEventListener('click', windowClickHandler, { capture: true });
		}
	});

	function handleNameKeydown(e) {
		if (e.key === 'Enter') {
			name = editedName.trim() || name;
			editingName = false;
		} else if (e.key === 'Escape') {
			editingName = false;
		}
	}

	function cancelNameEdit() {
		editingName = false;
	}
</script>

<div class="timer {running ? 'active' : ''}" on:contextmenu|preventDefault={handleRightClickTimer}>
	<div class="header">
		<span class="title" on:contextmenu|preventDefault={handleRightClickName}>{name}</span>
	</div>

	<div class="time">{formatTime(timeRemaining)}</div>

	<div class="controls">
		<button on:click={startPause}>{running ? 'Pause' : 'Start'}</button>
		<button on:click={resetTimer}>Reset</button>
	</div>

	<div class="progress-container">
		<div class="progress-bar" style="width: {progress()}%;"></div>
	</div>

	<div class="time-adjust">
		<button on:click={subtract15}>-15</button>
		<button on:click={add15}>+15</button>
	</div>

	{#if showContextMenu && !editingName}
		<div class="context-menu" style="left:{contextMenuX}px; top:{contextMenuY}px;">
			<ul>
				<li on:click={editDuration}>Edit Duration</li>
			</ul>
		</div>
	{/if}

	{#if editingName}
		<div class="title-edit-overlay">
			<div class="title-edit-box">
				<label>Edit Name:</label>
				<input type="text" bind:value={editedName} on:keydown={handleNameKeydown}/>
				<button on:click={() => { name = editedName.trim() || name; editingName=false; }}>Save</button>
				<button on:click={cancelNameEdit}>Cancel</button>
			</div>
		</div>
	{/if}

	{#if editingDuration}
		<div class="edit-overlay">
			<div class="edit-box">
				<label>Minutes:</label>
				<input type="number" bind:value={editedMinutes} min="0"/>
				<label>Seconds:</label>
				<input type="number" bind:value={editedSeconds} min="0" max="59"/>
				<button on:click={saveDuration}>Save</button>
				<button on:click={() => editingDuration=false}>Cancel</button>
			</div>
		</div>
	{/if}
</div>
