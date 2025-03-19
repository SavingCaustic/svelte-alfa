<script lang="ts">
    import { websockLast } from "$lib/websocket";
    import RpcButton from "$lib/components/api-ref/RpcButton.svelte";
    import Panel from "$lib/components/api-ref/Panel.svelte";
    import Knob from "$lib/components/api-ref/Knob.svelte";
    import WsButton from "$lib/components/api-ref/WsButton.svelte";
    import Keyboard from "$lib/components/api-ref/Keyboard.svelte";
    
</script>

<h1>api ref</h1>
<Panel label="Server">
    <RpcButton label="Shutdown" uri="method=server.quit" />
</Panel>
<Panel label="Project">
    <RpcButton label="Load demo project" uri="method=project.load&value=demo" />
    <RpcButton label="Save demo project" uri="method=project.save&value=demo" />
    <RpcButton label="88 bpm" uri="method=project.set&key=bpm&value=88" />
    <RpcButton label="120 bpm" uri="method=project.set&key=bpm&value=120" />
    <RpcButton label="432 Hz" uri="method=project.set&key=master_tune&value=432"/>
</Panel>
<Panel label="Rack (synth) & effects">
    <RpcButton label="Mount Subreal to zero" uri="method=rack.mount&key=synth&value=Subreal&rack_id=0"/>
    <RpcButton label="Mount Delay to effect1" uri="method=rack.mount&key=effect1&value=Delay&rack_id=0"/>
    <RpcButton label="Mount Chorus to effect2" uri="method=rack.mount&key=effect2&value=Chorus&rack_id=0"/>
    <RpcButton label="Un-mount effect1" uri="method=rack.mount&key=effect1&value=&rack_id=0"/>
</Panel>
<Panel label="Rack (eventors)">
    <RpcButton label="Mount Third to eventor1" uri="method=rack.mount&key=eventor1&value=Third&rack_id=0"/>
    <RpcButton label="Mount Fifth to eventor2" uri="method=rack.mount&key=eventor2&value=Fifth&rack_id=0"/>
    <RpcButton label="Mount Arpeggio to eventor1" uri="method=rack.mount&key=eventor1&value=Arpeggiator&rack_id=0"/>
    <RpcButton label="Un-mount eventor1" uri="method=rack.mount&key=eventor1&value=&rack_id=0"/>
</Panel>
<Panel label="Unit">
    <RpcButton label="Setup LUT0" uri="method=unit.set&unit=synth&key=lut1_overtones&value=0.4,0.3,0.2&rack_id=0"/>
    <RpcButton label="Setup LUT1" uri="method=unit.set&unit=synth&key=lut2_overtones&value=0.4,0,0,0,0,0.5&rack_id=0"/>
    <RpcButton label="Load patch Submarino" uri="method=unit.patchLoad&unit=synth&value=Submarino&rack_id=0"/>
    <RpcButton label="Save patch Submarino2" uri="method=unit.patchSave&unit=synth&value=Submarino2&rack_id=0"/>
</Panel>
<Panel label="Deprecated">
    <RpcButton label="Set osc_mix - rack 0" uri="method=unit.param&rack_id=0&unit=synth&key=osc_mix&value=111"/>
    <RpcButton label="Set effect1 param0 (not midi aware), rack 0" uri="method=unit.param&rack_id=0&unit=effect1&key=0&value=111"/>
</Panel>
<Panel label="Websockets">
    <WsButton data="0x904060" label="Note on (E4)" />
    <WsButton data="0x904000" label="Note off (E4)" />
    <Knob id="cc_10"/>
    <Knob id="cc_12"/>
    <Knob id="cc_13" steps="7"/>
    <Knob id="cc_15"/>
    <p>Last sent: {$websockLast.lastSent}</p>
    <p>Last recieved: {$websockLast.lastReceived}</p>
    <Keyboard />
</Panel>
