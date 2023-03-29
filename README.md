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
  import { PianoRoll }  from '@ashfrench-dev/svelte-pianoroll'

  let startingKey = 35
  let keyCount = 18
  let options = {
    height: '100%',
    width: '100%',
    show_note_labels: true,
    show_highlight_keys: false,
    highlight_keys: [],
    on_key_press: (e) => {},
    on_key_unpress: (e) => {}
  }
</script>

<PianoRoll starting_key={startingKey} key_count={keyCount} options={options}/>
```

### `PianoRoll` parameters

- `starting_key`: `<int>`; The key the piano roll should start rendering keys. Range is from `1` (`A0`) to `88` (`C8`). Defaults to `35` (`G3`).
- `key_count`: `<int>`; The total number of keys the piano roll should render. Range is from `1` to `88`, but will not render keys before `A0` or beyond `C8`. Defaults to `18`.
- `options`: `<object>`; An object with the following properties:
  - `height`: `<string>`; The height of the piano's container. A CSS-valid height value. Default is `'100%'`.
  - `width`: `<string>`; The width of the piano's container. A CSS-valid width value. Default is `'100%'`.
  - `show_note_labels`: `<boolean>`; Whether to show note labels or not. Default is `false`.
  - `show_highlight_keys`: `<boolean>`; Whether to show highlighted notes or not. Default is `false`.
  - `highlight_keys`: `<array>`; A list of keys (`<int>`) to highlight on the piano roll. Key range is from `1` (`A0`) to `88` (`C8`). Don't forget to set `highlight_keys` to true!
  - `on_key_press`: `<function>`; A callback to execute after a key is pressed down. The callback will be passed a standard [`Event`](https://developer.mozilla.org/en-US/docs/web/api/event) as an argument.
  - `on_key_unpress`: `<function>`; A callback to execute after a key is released. The callback will be passed a standard [`Event`](https://developer.mozilla.org/en-US/docs/web/api/event) as an argument.

