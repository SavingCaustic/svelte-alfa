<script lang="ts">
    let { page = "", label = "" } = $props();
    //type-safe:
    //let { page, label } = $props() as { page: string; label: string };
</script>

<a class="linkButton" href={page}>{label}</a>

<style>
    .linkButton {
        display: inline-block;
        margin: 0.2rem;
        font-size: 1.5rem;
        font-family: Courier;
        text-transform: uppercase;
        padding: 0.5rem;
        padding-top: 0.8rem;
        background-color: #ccd;
        color: #c55;
    }
</style>
