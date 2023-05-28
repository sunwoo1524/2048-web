<script>
    // export let anim;
    export let num;

    const colors = {
        2: "#bae0ff",
        4: "#6fbcfc",
        8: "#0366b7",
        16: "#252fea",
        32: "#1d24aa",
        64: "#151975",
        128: "#5120bc",
        256: "#3d198c",
        512: "#2b0877",
        1024: "#180344",
        2048: "#b168c9",
    }
</script>

{#if num == 0}
<div class="tile empty"></div>
{:else}
<div class="tile" style="background-color: {colors[num]}; font-size: {num < 128 ? 45 : num < 1024 ? 30 : 25}px">{num}</div>
{/if}

<style>
    @import url('https://fonts.googleapis.com/css2?family=Poppins&display=swap');

    :root {
        --tile-size: 80px;
        --tile-padding: 5px;
    }

    .tile {
        box-sizing: border-box;
        width: calc(var(--tile-size) - var(--tile-padding));
        height: calc(var(--tile-size) - var(--tile-padding));
        
        margin: var(--tile-padding);
    }

    .tile:not(.empty) {
        display: flex;
        align-items: center;
        justify-content: center;
        color: white;
        font-family: 'Poppins', sans-serif;
        border-radius: 5px;
    }
</style>