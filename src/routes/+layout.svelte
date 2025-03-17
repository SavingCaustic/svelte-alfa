<script lang="ts">
  import { onMount, onDestroy } from "svelte";
  import { controlStore } from "$lib/stores/controls";
  import { sendMessage } from "$lib/websocket";
  import { connectWebSocket, disconnectWebSocket } from "$lib/websocket";
  import { browser } from "$app/environment"; // Import browser check

  import NavBar from "$lib/components/api-ref/navbar/NavBar.svelte";

  //Svelte5
  let { children } = $props();

  let rootDiv: HTMLElement;

  type TouchType = "key" | "knob";

  type TouchData = {
    element: HTMLElement; // The element being interacted with
    type: TouchType;
    startX?: number; // Optional for dragging
    startY?: number;
    orgCC?: number;
    swypeAxis?: "X" | "Y" | ""; // Add swypeAxis
  };

  let activeTouches: Map<number, TouchData> = new Map();

  onMount(() => {
    rootDiv = document.getElementById("app-root") as HTMLElement;
    if (rootDiv && browser) {
      rootDiv.addEventListener("pointerdown", handlePointerDown);
      rootDiv.addEventListener("pointermove", handlePointerMove);
      rootDiv.addEventListener("pointerup", handlePointerUp);
      rootDiv.addEventListener("pointercancel", handlePointerUp);
      //document.getElementById("keyboard").addEventListener("pointerleave", handlePointerLeave);
      window.addEventListener("contextmenu", (e) => e.preventDefault()); // Prevent unwanted context menu popups
    }
    //um.. We need some variable to set ip and port - resolved curr in component..
    connectWebSocket(); //("ws://localhost:18080/ws");
  });

  onDestroy(() => {
    //rootDiv = document.getElementById("app-root");
    if (rootDiv) {
      rootDiv.removeEventListener("pointerdown", handlePointerDown);
      rootDiv.removeEventListener("pointermove", handlePointerMove);
      rootDiv.removeEventListener("pointerup", handlePointerUp);
      rootDiv.removeEventListener("pointercancel", handlePointerUp);
      rootDiv.removeEventListener("contextmenu", (e) => e.preventDefault());
      //
      disconnectWebSocket();
    }
  });

  function handlePointerDown(event: PointerEvent): void {
    event.preventDefault();
    let target = event.target as HTMLElement;
    let key = target.closest("#keyboard .t_key") as HTMLElement | null;
    let knob = target.closest(".t_knob") as HTMLElement | null;
    console.log("handling pointer down");
    if (key) {
      console.log("here");
      activeTouches.set(event.pointerId, { element: key, type: "key" });
      const keyId = key.id; // Use the ID as the key for the store
      playNote(getNoteNumber(keyId)); // Extract note number
    } else if (knob) {
      const knobId = knob.id; // Use the ID as the key for the store
      let knobValue = $controlStore[knobId] || 0; // Get value from store, default to 0

      activeTouches.set(event.pointerId, {
        element: knob,
        type: "knob",
        startX: event.clientX,
        startY: event.clientY,
        orgCC: knobValue,
      });
      console.log("knob value:" + knobValue); //correct
    }
  }

  function handlePointerMove(event: PointerEvent): void {
    let touchData = activeTouches.get(event.pointerId);
    if (!touchData) return;
    if (touchData.type === "key") {
      let key = document
        .elementFromPoint(event.clientX, event.clientY)
        ?.closest("#keyboard .t_key") as HTMLElement | null;
      if (key && key !== touchData.element) {
        stopNote(getNoteNumber(touchData.element.id));
        touchData.element = key;
        playNote(getNoteNumber(key.id));
      }
    } else if (
      touchData.type === "knob" &&
      touchData.startX !== undefined &&
      touchData.startY !== undefined
    ) {
      let deltaX = event.clientX - touchData.startX;
      let deltaY = event.clientY - touchData.startY;
      // Determine axis movement
      if (!touchData.swypeAxis) {
        if (Math.abs(deltaY) > 3 && Math.abs(deltaX) < 3) {
          touchData.swypeAxis = "Y";
        } else if (Math.abs(deltaX) > 3 && Math.abs(deltaY) < 3) {
          touchData.swypeAxis = "X";
        }
      }

      let newCC = touchData?.orgCC ?? 0;
      let tmpCC = newCC;

      if (touchData.swypeAxis === "Y") {
        newCC = Math.round(newCC - deltaY / 2);
      } else if (touchData.swypeAxis === "X") {
        newCC = Math.round(newCC + deltaX / 10);
      }

      newCC = Math.max(0, Math.min(127, newCC)); // Clamp value between 0-127
      console.log("new CC:" + newCC);
      if (newCC != tmpCC) {
        sendCC(touchData.element.id, newCC);
        //Working: sendMessage("0xB01000");
      }
      //updateKnob(touchData.element, deltaX, deltaY);
    }
  }

  function sendCC(ccName: string, ccVal: number) {
    //really send this to web-socket server and let that server set the data anytime,
    //but fake now.
    //wsRequest('yada-yada'..)
    //well really only send if there's a new value..
    let ccNumber = ccName.replace("cc_", "");
    let ccValue = Number(ccVal).toString(16).padStart(2, "0"); // Convert value to hex
    let midi = "0xB0" + ccNumber + ccValue;
    sendMessage(midi);
  }

  function handlePointerUp(event: PointerEvent): void {
    let touchData = activeTouches.get(event.pointerId);
    if (!touchData) return;

    if (touchData.type === "key") {
      stopNote(getNoteNumber(touchData.element.id));
    }

    activeTouches.delete(event.pointerId);
  }

  function getNoteNumber(keyId: string): string {
    return keyId.substring(4);
  }

  function playNote(noteNo: string): void {
    console.log("Playing note " + noteNo);
    const midi = "0x90" + noteNo + "80";
    sendMessage(midi);
  }

  function stopNote(noteNo: string): void {
    console.log("Stopping note " + noteNo);
    const midi = "0x80" + noteNo + "00";
    sendMessage(midi);
  }
</script>

<div id="mainFrame">
  <div id="slotContainer">
    {@render children()}
  </div>
  <div id="navContainer">
    <NavBar />  
  </div>
</div>

<style>
  #mainFrame {
    display: flex;
    flex-direction: column;
    height: 100vh; /* Full screen height */
  }

  #slotContainer {
    flex: 1; /* Takes the remaining space */
    overflow: auto; /* Allows scrolling if content overflows */
  }

  #navContainer {
/*    height: 80px;
    flex-shrink: 0; */
  }
</style>
