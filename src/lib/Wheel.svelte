<script lang="ts">
    import { onMount } from 'svelte';
    let percent = $state(50);
    let dragging = false;
    let playing = $state(false);
    const circleRadius = 40;
    const circleDashArray = 2 * Math.PI * circleRadius;
    const percentOffset = (percent: number) => {
        const percentVal = circleDashArray * (percent / 100);
        return circleDashArray - percentVal;
    };
    const getCenterCoords = (el: HTMLElement) => {
        const boundingRect = el.getBoundingClientRect();
        const cx = boundingRect.left + boundingRect.width / 2;
        const cy = boundingRect.top + boundingRect.height / 2;
        return { cx, cy };
    };
    onMount(() => {
        const circle = document.getElementById('circle');
        const input = document.getElementById('input');
        input!.style.strokeDasharray = circleDashArray.toString();
        input!.style.strokeDashoffset = percentOffset(percent).toString();
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
    const handleClick = (e: MouseEvent) => {
        const { cx, cy } = getCenterCoords(e.target as HTMLElement);
        moveToClick(e.clientX, e.clientY, cx, cy);
    };
    const moveToClick = (clientX: number, clientY: number, cx: number, cy: number) => {
        const dx = clientX - cx;
        const dy = clientY - cy;

        // calculate the angle of the piece of the circle in radians
        let angle = Math.atan2(dx, -dy);

        // atan2 returns -pi to pi, so adding 2pi puts it at the same point on the circle but a positive number!
        if (angle < 0) angle += 2 * Math.PI;

        // a circle's angle is 2pi radians, so dividing angle/full circle gives us the percentage
        // of the angle along the length of the circle. This can be used with the percent function!
        const percent = (angle / (2 * Math.PI)) * 100;
        document.getElementById('input')!.style.strokeDashoffset =
            percentOffset(percent).toString();
    };
</script>

<div>
    <button onclick={() => (playing = !playing)} type="button">{playing ? 'Stop' : 'Start'}</button>
    <svg
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 100 100"
        onpointerdown={handlePointerDown}
        onpointermove={handlePointerMove}
        onpointerup={handlePointerUp}
        onpointercancel={handlePointerUp}
    >
        <circle cx="50" cy="50" r="40" id="circle" />
        <circle cx="50" cy="50" r="40" id="input" />
    </svg>
</div>

<style>
    div {
        display: flex;
        gap: 1rem;
        flex-direction: column;
        align-items: center;
        justify-content: center;
    }
    button {
        width: fit-content;
    }
    svg {
        width: 100px;
        height: 100px;
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
