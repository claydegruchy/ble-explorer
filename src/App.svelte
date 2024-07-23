<script>
  import {
    BleClient,
    numbersToDataView,
    dataViewToNumbers,
    dataViewToText,
  } from "@capacitor-community/bluetooth-le";

  const service = "0000180f-0000-1000-8000-00805f9b34fb";
  const characteristic = "00002a19-0000-1000-8000-00805f9b34fb";

  let device = {};

  async function connect() {
    await BleClient.initialize({
      androidNeverForLocation: true,
    });
    device = await BleClient.requestDevice({
      services: [],
      optionalServices: [service],
    });

    await BleClient.disconnect(device.deviceId);
    await BleClient.connect(device.deviceId);
  }

  let lastBatteryReading;
  async function read() {
    let value = await BleClient.read(device.deviceId, service, characteristic);
    console.log(value);
    lastBatteryReading = dataViewToNumbers(value);
  }
</script>

<main>
  <div>This is a BLE test tool</div>
  {#if device.deviceId}
    Connected to: {device.name}

    <button on:click={read}>Read battery level</button>

    {#if lastBatteryReading}
      Last battery reading: {lastBatteryReading}
    {/if}
  {:else}
    <button on:click={connect}>Connect</button>
  {/if}
</main>

<style>
</style>
