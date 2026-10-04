# Nano Trailing White-space Indicators
If you like having little green boxes that indicate spaces or tabs at the end of any line in **nano**:
1. Create the `.nanorc` file in your home directory if it doesn't already exist:
   ```bash
   touch ~/.nanorc
   ```
2. Open the file in a text editor.
3. Add these contents to the bottom of the file:
   ```text
   syntax "default"
   color ,green "[[:space:]]+$"
   ```
4. Save the change.
5. Close the file.

### Note
* You can use a different color other than green. According to the [nanorc man page](https://nano-editor.org/dist/latest/nanorc.5.html), valid base color names are **black**, **blue**, **cyan**, **green**, **magenta**, **red**, **white**, and **yellow**.
* In modern terminal emulators that support at least 256 colors, you can also use these colors: **beet**, **brick**, **brown**, **crimson**, **lagoon**, **latte**, **lime**, **mauve**, **mint**, **normal** , **ocher**, **orange**, **peach**, **pink**, **plum**, **purple**, **rosy**, **sage**, **sand**, **sea**, **sky**, **slate**, **tawny**, and **teal**  (with **normal** being the default color).
* Modern versions of **nano** running in 256-color terminals also accept a three-digit hexadecimal code prefixed with a hash-mark (for example: `,#f00`) that will be mapped to the closest matching color.

---
**Tags:** tag-indicators tag-kubuntu tag-software tag-ubuntu tag-white-space
