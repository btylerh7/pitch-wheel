<script lang="ts">
    import Wheel from './lib/Wheel.svelte';
    import AboutModal from './lib/AboutModal.svelte';
</script>

<header>
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100" width="100">
        <path d="M 0 50 l 100 0" stroke="red" stroke-width="3" />
    </svg>
    <h1>Find your pitch!</h1>
    <button commandfor="about-modal" command="show-modal">?</button>
    <AboutModal />
    <Wheel />
</header>
