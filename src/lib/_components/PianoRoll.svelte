<script>
  // starting_key: int (min 1 max 88)
  // - default will override breaking values
  // key_count: int (min 1 max 88)
  // - default will override breaking values
  // options: object
  // - height: string (any valid CSS height value)
  // - width: string (any valid CSS width value)
  // - show_note_labels: bool
  // - show_highlight_keys: bool
  // - highlight_keys: array
  // - on_key_press: function
  // - on_key_unpress: function
  export let starting_key, key_count, options;

  // set some defaults
  let keys = { black: [], white: [] }
  let defaultStartingKey = 35
  let defaultKeyCount = 18
  let lowestPossibleKey = 1
  let highestPossibleKey = 88
  let hasLeadingWhiteHalfKey = false
  let hasLeadingBlackHalfKey = false
  let hasTrailingWhiteHalfKey = false
  let hasTrailingBlackHalfKey = false
  let isLeadingBlackHalfKeyInvisible = false
  let isTrailingBlackHalfKeyInvisible = false

  // start loading parameters
  let startingKey = starting_key
  if (isNaN(startingKey) || startingKey < 1 || startingKey > highestPossibleKey) {
    startingKey = defaultStartingKey
  }
  
  let keyCount = key_count
  if (isNaN(keyCount) || keyCount < lowestPossibleKey || keyCount > highestPossibleKey) {
    keyCount = defaultKeyCount
  }

  if (startingKey + keyCount > highestPossibleKey) {
    keyCount = highestPossibleKey - startingKey
  }
  
  let endingKeyNumber = startingKey + keyCount - 1

  // options parameters
  let height = '100%'
  let width = '100%'
  let showNoteLabels = false
  let showHighlightNotes = false
  let highlightNotes = []
  let onKeyPress = (e) => {}
  let onKeyUnpress = (e) => {}

  if (typeof options === 'object') {
    try {
      if (Object.hasOwn(options, 'height') && typeof options.height === 'string') {
        height = options.height
      }

      if (Object.hasOwn(options, 'width') && typeof options.width === 'string') {
        width = options.width
      }

      if (Object.hasOwn(options, 'show_note_labels') && typeof options.show_note_labels === 'boolean') {
        showNoteLabels = options.show_note_labels
      }

      if (Object.hasOwn(options, 'show_highlight_keys') && typeof options.show_highlight_keys === 'boolean') {
        showHighlightNotes = options.show_highlight_keys
      }

      if (Object.hasOwn(options, 'highlight_keys') && Array.isArray(options.highlight_keys)) {
        highlightNotes = options.highlight_keys
      }

      if (Object.hasOwn(options, 'on_key_press') && typeof options.on_key_press === 'function') {
        onKeyPress = options.on_key_press
      }

      if (Object.hasOwn(options, 'on_key_unpress') && typeof options.on_key_unpress === 'function') {
        onKeyUnpress = options.on_key_unpress
      }
    } catch (exception) {
      // we tried and failed
      console.log('PianoRoll.svelte: error loading options:\n', exception)
    }
  }

  function isBlackKey(param_key_number) {
    let blackKeyNumbers = [2, 5, 7, 10, 12, 14, 17, 19, 22, 24, 26, 29, 31, 34, 36, 38, 41, 43, 46, 48, 50, 53, 55, 58, 60, 62, 65, 67, 70, 72, 74, 77, 79, 82, 84, 86]
    
    return blackKeyNumbers.includes(param_key_number)
  }

  function isWhiteKey(param_key_number) {
    let whiteKeyNumbers = [1, 3, 4, 6, 8, 9, 11, 13, 15, 16, 18, 20, 21, 23, 25, 27, 28, 30, 32, 33, 35, 37, 39, 40, 42, 44, 45, 47, 49, 51, 52, 54, 56, 57, 59, 61, 63, 64, 66, 68, 69, 71, 73, 75, 76, 78, 80, 81, 83, 85, 87, 88]
    
    return whiteKeyNumbers.includes(param_key_number)
  }

  function needsSpacerKey(param_key_number) {
    let spacerKeyNumbers = [3, 8, 15, 20, 27, 32, 39, 44, 51, 56, 63, 68, 75, 80, 87]

    return spacerKeyNumbers.includes(param_key_number)
  }

  function orderKeys(param_starting_key, param_key_count) {
    console.log('endingKeyNumber was', endingKeyNumber)
    let currentKey = param_starting_key
    let runs = 0
    let result = {
      black: [],
      white: []
    }

    while (runs < param_key_count) {
      if (isBlackKey(currentKey)) {
        result.black.push(currentKey)
      } else if (isWhiteKey(currentKey)) {
        result.black.push(false)
        
        result.white.push(currentKey)
        
        if (needsSpacerKey(currentKey)) {
          result.black.push(false)
        }
      }

      runs++

      if (runs >= param_key_count || currentKey == highestPossibleKey) {
        endingKeyNumber = currentKey
        console.log('endingKeyNumber is', endingKeyNumber)

        if (needsSpacerKey(endingKeyNumber)) {
          result.black.pop()
        }
        
        break
      }

      currentKey++
    }

    return result
  }

  function getNoteAtValue(param_value, param_get_flat=false) {
    let notes = { "A0": 1, "A#0": 2, "Bb0": 2, "B0": 3, "C1": 4, "C#1": 5, "Db1": 5, "D1": 6, "D#1": 7, "Eb1": 7, "E1": 8, "F1": 9, "F#1": 10, "Gb1": 10, "G1": 11, "G#1": 12, "Ab1": 12, "A1": 13, "A#1": 14, "Bb1": 14, "B1": 15, "C2": 16, "C#2": 17, "Db2": 17, "D2": 18, "D#2": 19, "Eb2": 19, "E2": 20, "F2": 21, "F#2": 22, "Gb2": 22, "G2": 23, "G#2": 24, "Ab2": 24, "A2": 25, "A#2": 26, "Bb2": 26, "B2": 27, "C3": 28, "C#3": 29, "Db3": 29, "D3": 30, "D#3": 31, "Eb3": 31, "E3": 32, "F3": 33, "F#3": 34, "Gb3": 34, "G3": 35, "G#3": 36, "Ab3": 36, "A3": 37, "A#3": 38, "Bb3": 38, "B3": 39, "C4": 40, "C#4": 41, "Db4": 41, "D4": 42, "D#4": 43, "Eb4": 43, "E4": 44, "F4": 45, "F#4": 46, "Gb4": 46, "G4": 47, "G#4": 48, "Ab4": 48, "A4": 49, "A#4": 50, "Bb4": 50, "B4": 51, "C5": 52, "C#5": 53, "Db5": 53, "D5": 54, "D#5": 55, "Eb5": 55, "E5": 56, "F5": 57, "F#5": 58, "Gb5": 58, "G5": 59, "G#5": 60, "Ab5": 60, "A5": 61, "A#5": 62, "Bb5": 62, "B5": 63, "C6": 64, "C#6": 65, "Db6": 65, "D6": 66, "D#6": 67, "Eb6": 67, "E6": 68, "F6": 69, "F#6": 70, "Gb6": 70, "G6": 71, "G#6": 72, "Ab6": 72, "A6": 73, "A#6": 74, "Bb6": 74, "B6": 75, "C7": 76, "C#7": 77, "Db7": 77, "D7": 78, "D#7": 79, "Eb7": 79, "E7": 80, "F7": 81, "F#7": 82, "Gb7": 82, "G7": 83, "G#7": 84, "Ab7": 84, "A7": 85, "A#7": 86, "Bb7": 86, "B7": 87, "C8": 88 }
    let isSharpSkipped = false

    for (let note in notes) {
      if (notes[note] === param_value) {
        if (param_get_flat && !isSharpSkipped) {
          isSharpSkipped = true
          continue
        }

        return note
      }
    }

    return null
  }

  function keyPressed(param_event) {
    let target = param_event.target
    
    target.setAttribute("data-pressed", "true")

    onKeyPress(param_event)

    return false
  }

  function keyUnpressed(param_event) {
    let target = param_event.target

    target.setAttribute("data-pressed", "false")

    onKeyUnpress(param_event)

    return false
  }

  keys = orderKeys(startingKey, keyCount)

  hasLeadingWhiteHalfKey = isBlackKey(startingKey)
  hasLeadingBlackHalfKey = isWhiteKey(startingKey)
  hasTrailingWhiteHalfKey = isBlackKey(endingKeyNumber)
  hasTrailingBlackHalfKey = isWhiteKey(endingKeyNumber)
  isLeadingBlackHalfKeyInvisible = needsSpacerKey(startingKey - 1) || startingKey == lowestPossibleKey
  isTrailingBlackHalfKeyInvisible = needsSpacerKey(endingKeyNumber) || endingKeyNumber == highestPossibleKey

  console.log('here we go!')
  console.log(startingKey)
  console.log(keyCount)
  console.log(options)
</script>

<style>
  .pianoroll, .pianoroll * {
    box-sizing: border-box;
  }

  .key {
    outline-style: solid;
    outline-width: 1px;
    outline-color: #111827;
    height: 100%;
    transition-property: all;
    transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
    transition-duration: 150ms;
  }

  .black-key {
    background-color: rgb(31 41 55);
    border-style: solid;
    border-width: 3px;
    border-bottom-width: 6px;
    border-top-width: 0px;
    border-color: rgb(17 24 39);
    border-left-color: rgb(55 65 81);
    border-bottom-right-radius: 8px;
    border-bottom-left-radius: 8px;
  }
  
  .black-key[data-pressed=true] {
    background-color: rgb(17 24 39);
    color: rgb(107 114 128);
    border-color: transparent;
  }

  .black-key.highlighted {
    border-color: rgb(30 64 175);
    border-left-color: rgb(96 165 250);
  }

  .black-key .note-label {
    font-size: 0.9vw;
    color: rgb(255 255 255);
  }

  .white-key {
    background-color: rgb(255 255 255);
    border-bottom-style: solid;
    border-bottom-width: 6px;
    border-bottom-color: rgb(209 213 219);
    border-bottom-right-radius: 8px;
    border-bottom-left-radius: 8px;
  }

  .white-key[data-pressed=true] {
    background-color: rgb(229 231 235);
    border-color: transparent;
  }

  .white-key.highlighted {
    border-bottom-color: rgb(29 78 216);
  }

  .white-key .note-label {
    font-size: 1vw;
    color: rgb(0 0 0);
  }

  .highlighted {
    background-color: rgb(59 130 246);
  }

  .highlighted[data-pressed=true] {
    background-color: rgb(37 99 235);
    border-color: transparent;
  }

  .highlighted .note-label {
    color: rgb(255 255 255);
  }

  .note-label {
    font-family: sans-serif;
  }

  .invisible {
    visibility: hidden;
    pointer-events: none;
  }

  .leading-half-key {
    border-left-width: 0px;
    border-bottom-right-radius: 0px;
    border-bottom-left-radius: 0px;
    border-bottom-right-radius: 6px;
  }

  .trailing-half-key {
    border-right-width: 0px;
    border-bottom-right-radius: 0px;
    border-bottom-left-radius: 0px;
    border-bottom-left-radius: 6px;
  }
</style>

<div class="pianoroll" style="width: {width}; height: {height}; position: relative; overflow: hidden;">
  <div style="z-index: 10; width: 100%; height: 60%; position: absolute; top: 0; display: flex; flex-direction: row; pointer-events: none;">
    {#if hasLeadingBlackHalfKey}
      {#if isLeadingBlackHalfKeyInvisible}
        <div class="key black-key leading-half-key invisible" style="flex: 1;"></div>
      {:else}
        <div 
          class="key black-key leading-half-key {highlightNotes.includes(startingKey - 1) && showHighlightNotes ? 'highlighted' : ''}"
          style="flex: 1; pointer-events: auto;"
          data-pressed="false"
          data-key-number="{startingKey - 1}"
          data-note="{getNoteAtValue(startingKey - 1)}"
          on:mousedown|preventDefault|stopPropagation|capture={(e) => {keyPressed(e)}}
          on:touchstart|preventDefault|stopPropagation|capture={(e) => {keyPressed(e)}}
          on:mouseup|preventDefault|stopPropagation|capture={(e) => {keyUnpressed(e)}}
          on:touchend|preventDefault|stopPropagation|capture={(e) => {keyUnpressed(e)}}></div>
      {/if}
    {/if}
    {#each keys.black as blackKey}
      {#if blackKey === false}
        <div class="key black-key invisible" style="flex: 2;"></div>
      {:else}
        <div 
          class="key black-key {highlightNotes.includes(blackKey) && showHighlightNotes ? 'highlighted' : ''}"
          style="flex: 2; position: relative; pointer-events: auto;"
          data-pressed="false"
          data-key-number="{blackKey}"
          data-note="{getNoteAtValue(blackKey)}"
          on:mousedown|preventDefault|stopPropagation|capture={(e) => {keyPressed(e)}}
          on:touchstart|preventDefault|stopPropagation|capture={(e) => {keyPressed(e)}}
          on:mouseup|preventDefault|stopPropagation|capture={(e) => {keyUnpressed(e)}}
          on:touchend|preventDefault|stopPropagation|capture={(e) => {keyUnpressed(e)}}>
        {#if showNoteLabels}
          <div style="position: absolute; bottom: 0; width: 100%; text-align: center; user-select: none; pointer-events: none;">
            <span class="note-label">
              {getNoteAtValue(blackKey)}
              <br/>
              {getNoteAtValue(blackKey, true)}
            </span>
          </div>
        {/if}
        </div>
      {/if}
    {/each}
    {#if hasTrailingBlackHalfKey}
      {#if isTrailingBlackHalfKeyInvisible}
        <div class="key black-key trailing-half-key invisible" style="flex: 1;"></div>
      {:else}
        <div 
          class="key black-key trailing-half-key {highlightNotes.includes(endingKeyNumber + 1) && showHighlightNotes ? 'highlighted' : ''}"
          style="flex: 1; pointer-events: auto;"
          data-pressed="false"
          data-key-number="{endingKeyNumber + 1}"
          data-note="{getNoteAtValue(endingKeyNumber + 1)}"
          on:mousedown|preventDefault|stopPropagation|capture={(e) => {keyPressed(e)}}
          on:touchstart|preventDefault|stopPropagation|capture={(e) => {keyPressed(e)}}
          on:mouseup|preventDefault|stopPropagation|capture={(e) => {keyUnpressed(e)}}
          on:touchend|preventDefault|stopPropagation|capture={(e) => {keyUnpressed(e)}}></div>
      {/if}
    {/if}
  </div>
  <div style="width: 100%; height: 100%; position: absolute; top: 0; display: flex; flex-direction: row;">
    {#if hasLeadingWhiteHalfKey}
      <div 
        class="key white-key leading-half-key {highlightNotes.includes(startingKey - 1) && showHighlightNotes ? 'highlighted' : ''}"
        style="flex: 1;"
        data-pressed="false"
        data-key-number="{startingKey - 1}"
        data-note="{getNoteAtValue(startingKey - 1)}"
        on:mousedown|preventDefault|stopPropagation|capture={(e) => {keyPressed(e)}}
        on:touchstart|preventDefault|stopPropagation|capture={(e) => {keyPressed(e)}}
        on:mouseup|preventDefault|stopPropagation|capture={(e) => {keyUnpressed(e)}}
        on:touchend|preventDefault|stopPropagation|capture={(e) => {keyUnpressed(e)}}></div>
    {/if}
    {#each keys.white as whiteKey}
      <div 
        class="key white-key {highlightNotes.includes(whiteKey) && showHighlightNotes ? 'highlighted' : ''}"
        style="flex: 4; text-align: center; position: relative; pointer-events: auto;"
        data-pressed="false"
        data-key-number="{whiteKey}"
        data-note="{getNoteAtValue(whiteKey)}"
        on:mousedown|preventDefault|stopPropagation|capture={(e) => {keyPressed(e)}}
        on:touchstart|preventDefault|stopPropagation|capture={(e) => {keyPressed(e)}}
        on:mouseup|preventDefault|stopPropagation|capture={(e) => {keyUnpressed(e)}}
        on:touchend|preventDefault|stopPropagation|capture={(e) => {keyUnpressed(e)}}>
        {#if showNoteLabels}
          <div style="position: absolute; bottom: 0; width: 100%; text-align: center; user-select: none; pointer-events: none;">
            <span class="note-label" style="{whiteKey === 40 ? 'font-weight: 700;' : ''}">
              {getNoteAtValue(whiteKey)}
            </span>
          </div>
        {/if}
      </div>
    {/each}
    {#if hasTrailingWhiteHalfKey}
      <div 
        class="key white-key trailing-half-key {highlightNotes.includes(endingKeyNumber + 1) && showHighlightNotes ? 'highlighted' : ''}"
        style="flex: 1; pointer-events: auto;"
        data-pressed="false"
        data-key-number="{endingKeyNumber + 1}"
        data-note="{getNoteAtValue(endingKeyNumber + 1)}"
        on:mousedown|preventDefault|stopPropagation|capture={(e) => {keyPressed(e)}}
        on:touchstart|preventDefault|stopPropagation|capture={(e) => {keyPressed(e)}}
        on:mouseup|preventDefault|stopPropagation|capture={(e) => {keyUnpressed(e)}}
        on:touchend|preventDefault|stopPropagation|capture={(e) => {keyUnpressed(e)}}></div>
    {/if}
  </div>
</div>

<!--here for svelte's questionable purging of CSS-->
<div class="black-key white-key highlighted" data-pressed="true" style="display: none;"></div>
<div class="black-key white-key highlighted" data-pressed="false" style="display: none;"></div>
