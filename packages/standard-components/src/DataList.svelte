<script>
  import { onMount } from "svelte"

  export let _bb
  export let model
  export let layout = "list"

  let headers = []
  let store = _bb.store

  async function fetchData() {
    if (!model || !model.length) return

    const FETCH_RECORDS_URL = `/api/views/all_${model}`
    const response = await _bb.api.get(FETCH_RECORDS_URL)
    if (response.status === 200) {
      const json = await response.json()

      store.update(state => {
        state[model] = json
        return state
      })
    } else {
      throw new Error("Failed to fetch records.", response)
    }
  }

  $: data = $store[model] || []
  $: if (model) fetchData()

  onMount(async () => {
    await fetchData()
  })
</script>

<section class:grid={layout === 'grid'} class:list={layout === 'list'}>
  {#each data as data}
    <div class="data-card">
      <ul>
        {#each Object.keys(data) as key}
          <li>
            <span class="data-key">{key}:</span>
            <span class="data-value">{data[key]}</span>
          </li>
        {/each}
      </ul>
    </div>
  {/each}
</section>

<style>
  .list {
    display: flex;
    flex-direction: column;
    font-family: Inter;
  }

  .grid {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
  }

  ul {
    list-style-type: none;
  }

  li {
    margin: 5px 0 5px 0;
  }

  .data-card {
    border: 1px solid #ccc;
    border-radius: 5px;
    padding: 10px;
  }

  .data-key {
    font-weight: bold;
    font-size: 20px;
    text-transform: capitalize;
  }
</style>
