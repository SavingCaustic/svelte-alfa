<script lang="ts">
    import { rpcPostUri } from "$lib/rpc";

    let {uri = "", label = ""} = $props();
</script>

<button class="rpcButton" onclick={() => rpcPostUri(uri)}>
    {label}
</button>

<style>
    .rpcButton {
        margin: 0.2rem;
        font-size: 1.2rem;
        font-family: Courier;
        text-transform: uppercase;
        padding:0.5rem;
        padding-top:0.8rem;
        background-color:#ccd;
        color:#555;
    }
</style>
