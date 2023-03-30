# Svelte Piano Roll

- A simple, interactive piano roll interface
- Fully interactive for touch and mouse
- Will reliably scale to any size
- Note labelling
- Key highlighting
- Functions for key interactions for easy integration
- Svelte is the only dependency

## Usage

```
<script>
  import { PianoRoll }  from '@ashfrench-dev/svelte-piano-roll'

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
```

### `PianoRoll` parameters

- `starting_key`: `<int>`; The key the piano roll should start rendering keys. Range is from `1` (`A0`) to `88` (`C8`). Defaults to `35` (`G3`).
- `key_count`: `<int>`; The total number of keys the piano roll should render. Range is from `1` to `88`, but will not render keys before `A0` or beyond `C8`. Defaults to `18`.
- `height`: `<string>`; The height of the piano's container. A CSS-valid height value. Default is `'100%'`.
- `width`: `<string>`; The width of the piano's container. A CSS-valid width value. Default is `'100%'`.
- `show_note_labels`: `<boolean>`; Whether to show note labels or not. Default is `false`.
- `show_highlight_keys`: `<boolean>`; Whether to show highlighted notes or not. Default is `false`.
- `highlight_keys`: `<array>`; A list of keys (`<int>`) to highlight on the piano roll. Key range is from `1` (`A0`) to `88` (`C8`). Don't forget to set `highlight_keys` to true!
- `on_key_press`: `<function>`; A callback to execute after a key is pressed down. The callback will be passed a standard [`Event`](https://developer.mozilla.org/en-US/docs/web/api/event) as an argument.
- `on_key_unpress`: `<function>`; A callback to execute after a key is released. The callback will be passed a standard [`Event`](https://developer.mozilla.org/en-US/docs/web/api/event) as an argument.
