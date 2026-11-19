<script lang="ts">
    import Wheel from './lib/Wheel.svelte';
    import AboutModal from './lib/AboutModal.svelte';
</script>

<header>
    <h1>Find your pitch!</h1>
    <button title="What is this?" commandfor="about-modal" command="show-modal">?</button>
</header>
<main>
    <AboutModal />
    <Wheel />
</main>

<style>
    header {
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 1rem;
    }
    button {
        height: fit-content;
        background: none;
        border: 2px solid coral;
        border-radius: 50%;
        font-size: 0.5em;
        color: var(--text-color);
        cursor: pointer;
        &:hover {
            transform: scale(1.2);
        }
    }
</style>
