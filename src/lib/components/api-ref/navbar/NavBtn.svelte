<script lang="ts">
    export let label: string; // Unique ID for each knob or slider
</script>

<button class="navBtn">
{ label }
</button>
  
  <style>
    .navBtn {
      height:60px;
      margin:0px 20px;
      padding:0px 10px;
      background-color:#ccc;
      border-radius:10px;
      font-size:1.5rem;
    }
  </style>
  