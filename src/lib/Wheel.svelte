<script lang="ts">
    import { onMount } from 'svelte';
    import * as Tone from 'tone';
    let percent = $state(10);
    let currentPitch = $state(20);
    let dragging = false;
    let playing = $state(false);
    const radius = 40;
    const circumference = 2 * Math.PI * radius;
    /**
     * Given a percentage value (ex. 45%), subtracts that
     * percentage of the circle's circumference (returns 55%)
     * @example if circumference is 50, percent is 20, returns
     * 40, aka (50 - (50 * 2/100))
     */
    const percentOffset = (percent: number) => {
        const percentVal = circumference * (percent / 100);
        return circumference - percentVal;
    };
    const synth = new Tone.Synth().toDestination();

    // As long as playing is true, play sound
    $effect(() => {
        playing ? synth.triggerAttack(currentPitch) : synth.triggerRelease();
    });

    const setPitch = (percentage: number) => {
        const min = 20;
        const max = 8000;
        const decimal = percentage / 100;
        currentPitch = Math.round((max - min) * decimal + 20);
    };
    const getCenterCoords = (el: HTMLElement) => {
        const boundingRect = el.getBoundingClientRect();
        const cx = boundingRect.left + boundingRect.width / 2;
        const cy = boundingRect.top + boundingRect.height / 2;
        return { cx, cy };
    };

    onMount(async () => {
        const input = document.getElementById('input');
        input!.style.strokeDasharray = circumference.toString();
        input!.style.strokeDashoffset = percentOffset(percent).toString();
        setPitch(currentPitch);
        await Tone.start();
    });
    const handlePointerDown = (e: PointerEvent) => {
        dragging = true;
        const { cx, cy } = getCenterCoords(e.target as HTMLElement);
        moveToClick(e.clientX, e.clientY, cx, cy);
    };
    const handlePointerMove = (e: PointerEvent) => {
        if (dragging == true) {
            const { cx, cy } = getCenterCoords(e.target as HTMLElement);
            moveToClick(e.clientX, e.clientY, cx, cy);
        }
    };
    const handlePointerUp = (e: PointerEvent) => {
        dragging = false;
        const { cx, cy } = getCenterCoords(e.target as HTMLElement);
        moveToClick(e.clientX, e.clientY, cx, cy);
    };
    const moveToClick = (clientX: number, clientY: number, cx: number, cy: number) => {
        // atan takes the coordinates of a point on the circle.
        // Here we take the x and y value of the clicked point, and we convert
        // it to the distance from the center of the circle's coords.
        const dx = clientX - cx;
        const dy = clientY - cy;

        // calculate the angle of the piece of the circle in radians
        let angle = Math.atan2(dx, -dy);

        // atan2 returns -pi to pi, so adding 2pi puts it at the same point on the circle but a positive number!
        if (angle < 0) angle += 2 * Math.PI;

        // a circle's angle is 2pi radians, so dividing angle/full circle gives us the percentage
        // of the angle along the length of the circle. This can be used with the percent function!
        const percent = (angle / (2 * Math.PI)) * 100;
        setPitch(percent);
        document.getElementById('input')!.style.strokeDashoffset =
            percentOffset(percent).toString();
    };
</script>

<div>
    <button onclick={() => (playing = !playing)} type="button">
        {playing ? 'Stop' : 'Start'}
    </button>
    <svg
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 100 100"
        onpointerdown={handlePointerDown}
        onpointermove={handlePointerMove}
        onpointerup={handlePointerUp}
        onpointercancel={handlePointerUp}
        role="slider"
        aria-valuenow={currentPitch}
        tabindex="-1"
    >
        <circle cx="50" cy="50" r="40" id="circle" />
        <circle cx="50" cy="50" r="40" id="input" />
    </svg>
    <p id="current-pitch">{currentPitch} Hz</p>
</div>

<style>
    div {
        display: flex;
        gap: 1rem;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        position: relative;
    }
    button {
        width: fit-content;
    }
    svg {
        width: 500px;
        height: 500px;
    }
    #current-pitch {
        font-size: 2.25rem;
        position: absolute;
        top: 50%;
        left: 50%;
        /* magic numbered the translate Y a little bit */
        transform: translateX(-50%) translateY(-80%);
    }
    #circle {
        stroke: var(--text-color);
        stroke-width: 20px;
        fill: none;
    }
    #input {
        stroke: coral;
        stroke-width: 10px;
        fill: none;
        transform-origin: center;
        transform: rotate(-90deg);
    }
</style>
