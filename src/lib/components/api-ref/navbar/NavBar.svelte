<script>
    import NavBtn from "./NavBtn.svelte";
    import NavDisplay from "./NavDisplay.svelte";

</script>
<div class="navBarBg">
  <NavBtn page="rackSelector" label="POPUP RACK SELECTOR"/>
  <NavBtn page="settings" label="POPUP SETTINGS"/>
  <NavDisplay />
  <NavBtn page="metronome" label="POPUP METRONOME"/>
  <NavBtn label="TEST KBD (OVER POPUP)"/>
  <NavBtn label="TEST ALERT (ALW. ON TOP)"/>
</div>
  
  <style>
    .navBarBg {
      background-color:#555;
      height:80px;
      display:flex;
      flex-direction: row;
      align-items: center;
    }
  </style>
  