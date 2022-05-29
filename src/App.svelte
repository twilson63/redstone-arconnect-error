<script>
  import Arweave from "arweave";
  import * as SW from "redstone-smartweave";
  import { Jumper } from "svelte-loading-spinners";

  const CONTRACT_SRC = "Hljxh8rYyXCb45BYULHb6KhUDnRkxc4ZUaUDCUkOP_w";

  let output = "";
  let submitting = false;

  const arweave = Arweave.init({
    host: "arweave.net",
    protocol: "https",
    port: 443,
  });

  const smartweave = SW.SmartWeaveWebFactory.memCachedBased(arweave).build();

  async function createSC(e) {
    e.target.disabled = true;
    submitting = true;
    try {
      const result = await smartweave.createContract.deployFromSourceTx(
        {
          initState: JSON.stringify({ addresses: [] }),
          srcTxId: CONTRACT_SRC,
          wallet: "use_wallet",
        },
        true
      );

      output = JSON.stringify(result);
    } catch (e) {
      console.log(e);
      output = `
      Error Message: ${e.message}
      Stack Trace: ${e.stack}
      `;
    }
    e.target.disabled = false;
    submitting = false;
  }
</script>

<div class="flex flex-col">
  <h1 class="text-6xl mb-8">Hello SmartWeave</h1>
  <button class="btn" on:click={createSC}>Create SmartContract!</button>
</div>
<hr />
{#if submitting}
  <div class="flex items-center justify-center">
    <Jumper size="60" color="#FF3E00" unit="px" duration="1s" />
  </div>
{/if}
<pre><code>{output}</code></pre>
