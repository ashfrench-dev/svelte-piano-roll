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
  <li>Svelte is the only dependency</li>
  <li>Fully interactive for touch and mouse usage</li>
  <li>Will reliably scale to any size.</li>
</ul>

<div style="height: 420px; width: 100%; border: solid 5px gray; box-sizing: border-box;">
  <PianoRoll starting_key={startingKey} key_count={keyCount} options={options}/>
</div>

<h2>Usage</h2>
<pre style="background: #333; color: ghostwhite; padding: 12px; border-radius: 6px;">
&lt;script&gt;
  import &#123; PianoRoll &#125;  from '@ashfrench-dev/svelte-pianoroll'

  let startingKey = 40
  let keyCount = 16
  let options = &#123;
    height: '100%',
    width: '100%',
    show_note_labels: true,
    show_highlight_keys: false,
    highlight_keys: [],
    on_key_press: (e) => &#123;&#125,
    on_key_unpress: (e) => &#123;&#125
  &#125
&lt;/script&gt;

&lt;PianoRoll starting_key=&#123;startingKey&#125 key_count=&#123;keyCount&#125 options=&#123;options&#125/&gt;
</pre>