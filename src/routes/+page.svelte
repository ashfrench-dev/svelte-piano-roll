<script>
  import PianoRoll from "../lib/_components/PianoRoll.svelte";

  let startingKey = 35
  let keyCount = 18
  let height = '100%'
  let width = '100%'
  let showNoteLabels = false
  let showHighlightKeys = false
  let highlightKeys = []
  let onKeyPress = (e) => {}
  let onKeyUnpress = (e) => {}
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
  <PianoRoll 
    starting_key={startingKey}
    key_count={keyCount}
    height={height}
    width={width}
    show_note_labels={showNoteLabels}
    show_highlight_keys={showHighlightKeys}
    highlight_keys={highlightKeys}
    on_key_press={onKeyPress}
    on_key_unpress={onKeyUnpress}/>
</div>

<p>Check out the <a href="https://github.com/ashfrench-dev/svelte-piano-roll">Github</a> for tips on usage</p>