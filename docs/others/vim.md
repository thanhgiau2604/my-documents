## Common keyboards in Vim

- use `h, j, k, l` instead of arrow keys to move the cursor
    ```txt
    h: move left
    l: move right
    j: move down
    k: move up
    ```

- use keys `w, b, and e` (also W, B, E in real Vim)
    ```txt
    w: start of next word
    e: move to the end of  word
    b: move to beginning of the word
    ge: move to the end of previous word
    ```

- can combine movement keys can combine with a number. For example, `3w` is the same as pressing w three times

- 3igo Esc → insert go 3 times

- To find and move to the next (or previous) occurrence of a character: `f and F`, e.g. `fo` finds next o.

- use `%` to jump match of `(, {, [`
- To reach the beginning of a line, press `0`
- For the end of a line, there’s `$`
- Find the `next occurrence` of the word under cursor with `*`, and the `previous with #`.

- `gg` → beginning of the file, G → end
- to `jump specific line`, give its line number along with `G`.


**Searching:**
   - use `/<text search>`, use `n` to find next word, `N` to find previous word.

- to `insert text` into a new line, use `o` (a line after) or `O` (a line before)
   after press o or O → editor is set to insert mode
 
- `x and X` to delete the character under the cursor and to the left of the cursor

- replace only one character under cursor, `without` changing to insert mode, use `r`.

- `d` is the delete command, able to combine with movement, (`dw` delete the first word on the `right side` of the cursor); (`d2e` delete on the right 2 word)

- `repeat` the previous command, just press `.`

- Vim has also visual mode, select text using movement keys before decide what to do with it → press `v`

- `:w` (save), `:q` (quit), `:q!` (quit without saving)
 - `u` (undo) or `ctrl + R` (redo), `:help`

**Copy, Cut and Paste**
- Press `v` to enter visual mode,  `y` to copy, `d` to cut and `P` (uppercase) to paste before your cursor.