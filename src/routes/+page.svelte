<script>
  import PianoRoll from "../lib/_components/PianoRoll.svelte";

  let startingKey = 35
  let keyCount = 18
  let options = {
    height: '100%',
    width: '100%',
    show_note_labels: true,
    show_highlight_keys: true,
    highlight_keys: [35, 40, 43],
    on_key_press: (e) => {},
    on_key_unpress: (e) => {}
  }
</script>

<svelte:head>
  <title>Svelte Piano Roll</title>
</svelte:head>

<h1>Svelte Piano Roll</h1>

<ul>
  <li>A simple, interactive piano roll interface</li>
  <li>Fully interactive for touch and mouse</li>
  <li>Will reliably scale to any size</li>
  <li>Note labelling</li>
  <li>Key highlighting</li>
  <li>Functions for key interactions for easy integration</li>
  <li>Svelte is the only dependency</li>
</ul>


<div style="height: 420px; width: 100%; border: solid 5px gray; box-sizing: border-box;">
  <PianoRoll starting_key={startingKey} key_count={keyCount} options={options}/>
</div>

<p>Check out the [Github](https://github.com/ashfrench-dev/svelte-piano-roll) for tips on usage</p>