<script lang="ts">
  import qrcode, { type QRCodeSegment, type QRCodeErrorCorrectionLevel } from 'qrcode'

  let data: QRCodeSegment[] = [{ data: '', mode: 'alphanumeric' }]
  let dataStr = ''
  let errorCorrectionLevel: QRCodeErrorCorrectionLevel ='L'
  let isManualMode = false
  let dataURL = ''

  function append() {
    data.push({ data: '', mode: 'alphanumeric' })
    data = data
  }

  function remove(i: number) {
    data.splice(i, 1)
    data = data
  }

  $: {
    if (isManualMode) {
      qrcode.toDataURL(data, { errorCorrectionLevel }, (err, url) => {
        if (err) {
          dataURL = ''
        } else {
          dataURL = url
        }
      })
    } else {
      qrcode.toDataURL(dataStr, { errorCorrectionLevel }, (err, url) => {
        if (err) {
          dataURL = ''
        } else {
          dataURL = url
        }
      })
    }
  }
</script>

<main>
  <h1 class="text-center font-bold text-2xl py-4">Minimal QRCode Generator</h1>
  <div class="p-4 container mx-auto">
    <ul class="list-disc list-inside mb-4"><li><a href="https://github.com/soldair/node-qrcode" target="_blank" class="underline">node-qrcode</a></li></ul>
    <h2 class="text-xl font-bold mb-4">Data</h2>
    <div class="mb-4">
      <div class="mb-4">
        <label><input type="checkbox" bind:checked={isManualMode} class="checkbox" /> Manual Mode</label>
      </div>
      {#if isManualMode}
        <table class="table">
          <tbody>
            {#each data as d, i}
              <tr>
                <th>{i+1}</th>
                <td>
                  <input bind:value={d.data} class="input" />
                </td>
                <td>
                  <select bind:value={d.mode} class="select">
                    <option>alphanumeric</option>
                    <option>numeric</option>
                    <option>byte</option>
                  </select>
                </td>
                <td><button onclick={() => remove(i)} class="btn btn-soft btn-error">x</button></td>
              </tr>
            {/each}
          </tbody>
        </table>
        <button onclick={append} class="btn btn-soft btn-primary">append</button>
      {:else}
        <div>
          input: <input bind:value={dataStr} class="input" />
        </div>
      {/if}
    </div>
    <h2 class="text-xl font-bold mb-4">Option</h2>
    <div class="mb-4">
      Error correction level:
      <select bind:value={errorCorrectionLevel} class="select">
        <option>L</option>
        <option>M</option>
        <option>Q</option>
        <option>H</option>
      </select>
    </div>
    <div class="mb-4">
      <div class="text-sm text-base-content/50">{isManualMode ? JSON.stringify(data) : dataStr}, {'{'} "errorCorrectionLevel": "{errorCorrectionLevel}" {'}'}</div>
    </div>
    <h2 class="text-xl font-bold mb-4">Result</h2>
    <div class="mb-4">
      {#if dataURL}
        <img src={dataURL} alt="generated" />
      {/if}
    </div>
  </div>
</main>
