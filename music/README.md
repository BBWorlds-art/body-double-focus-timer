# Focus music

Put your local audio files in this folder. The timer reads them from the
`focusTracks` list near the top of the `<script>` in `index.html`.

Expected file names (rename your files to match, or edit the list in `index.html`):

- `brown-noise.mp3`  → "Brown Noise"
- `rain-focus.mp3`   → "Rain Focus"
- `ocean-focus.mp3`  → "Ocean Focus"
- `cafe-focus.mp3`   → "Cafe Focus"
- `deep-focus.mp3`   → "Deep Focus"
- `break-reset.mp3`  → "Break Reset"

Notes:
- `.mp3` is the safest cross-browser format (iPhone/Safari included).
- Music starts only after you tap a button (browser autoplay rules).
- Music is independent of the timer chime.
- You can have fewer or more tracks — just keep the `focusTracks` list and
  these files in sync.
