<script context="module">
    import { writable, derived } from 'svelte/store';
    import { spring } from 'svelte/motion';

    const coords = spring({x: 0, y: 0}, {
        stiffness: 1, damping: 1
    });

    const coordsOuter = spring({x: 0, y: 0}, {
        stiffness: 0.1, damping: 0.7
    });

    function createCursorStore() {
        const { subscribe, update } = writable({x: 0, y: 0, text: ''});

        return {
            subscribe,
            setText: (event) => {
                return update(n => ({...n, text: event.target.getAttribute('data-cursor')}))
            },
            resetText: () => update(n => ({...n, text: ''})),
            setCoords: ({x, y}) => {
                coords.set({x, y});
                coordsOuter.set({x, y});
                return update(n => ({...n, x, y}));
            },
        };
    }

    const cursorStore = createCursorStore();

    export function useCursor() {
        document.addEventListener('mousemove', cursorStore.setCoords);
        document.querySelectorAll('[data-cursor]').forEach(el => {
            el.addEventListener('mouseenter', cursorStore.setText);
            el.addEventListener('mouseleave', cursorStore.resetText);
        });
    }
</script>

<script>
    import { onMount } from 'svelte';
    onMount( useCursor )
</script>

<div class="circle-cursor circle-cursor--inner" style="transform:translate3d({$coords.x}px, {$coords.y}px, 0px)" class:hovered={$cursorStore.text.length > 1}>
    {#if $cursorStore.text.length > 1}
        {$cursorStore.text}
    {/if}
</div>
<div class="circle-cursor circle-cursor--outer" style="transform:translate3d({$coordsOuter.x}px, {$coordsOuter.y}px, 0px)" class:hovered={$cursorStore.text.length > 1}></div>

<style>
    :root {
        --color-text: #F23939;
        --color-cursor: #F23939;
        --dot-size: 8px;
        --dot-size-hovered: 75px;
        --outer-size: 28px;
        --outer-size-hovered: 105px;
        --text-size: 12px;
        --opacity: 0.2;
        --opacity-hovered: 1;
    }
    .circle-cursor {
        position: absolute;
        left: 0;
        top: 0;
        pointer-events: none;
        border-radius: 100%;
        transform-origin: 50% center;
        font-size: var(--text-size);
    }
    .circle-cursor.circle-cursor--inner {
        width: var(--dot-size);
        height: var(--dot-size);
        left: calc((var(--dot-size)/2)*-1);
        top: calc((var(--dot-size)/2)*-1);
        z-index: 1000;
        background: var(--color-cursor);
        color: var(--color-text);
        display: flex;
        align-items: center;
        align-content: center;
        justify-content: center;
        text-align: center;
    }
    .circle-cursor.circle-cursor--inner.hovered {
        width: var(--dot-size-hovered);
        height: var(--dot-size-hovered);
        left: calc((var(--dot-size-hovered)/2)*-1);
        top: calc((var(--dot-size-hovered)/2)*-1);
        background: transparent;
    }
    .circle-cursor.circle-cursor--outer {
        width: var(--outer-size);
        height: var(--outer-size);
        left: calc((var(--outer-size) / 2) * -1);
        top: calc((var(--outer-size) / 2) * -1);
        border: 1px solid var(--color-cursor);
        z-index: 1200;
        opacity: 0.2;
    }
    .circle-cursor.circle-cursor--outer.hovered {
        width: var(--outer-size-hovered);
        height: var(--outer-size-hovered);
        left: calc((var(--outer-size-hovered)/2)*-1);
        top: calc((var(--outer-size-hovered)/2)*-1);
        background: transparent;
        opacity: 1;
    }
</style>
