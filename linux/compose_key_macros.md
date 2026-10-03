# Compose Key Macros

## Use a compose key to type automatically in Linux
Sometimes, you have special characters, like vulgar fractions or foreign-language characters that you need to type. Other times, you have snippets of text that you often need to type out, but can't remember or that are long and bothersome to type out. The **compose key** in Linux can automate that typing for you. Once you've set a **compose key**, you can press it followed by pressing a default or user-defined combination of other keys (a macro) and it will automatically insert your content for you no matter where you are in the operating system.

## Enable a compose key
* **Enable a compose key in Kubuntu:**
  1. Open **Settings → System Settings → Regional & Language → Keyboard Layout → Enable keyboard layouts → Advanced**.
  2. Choose a **Compose key position** <mark>← I chose the right **Ctrl** key</mark>
  3. Click the **Apply** button.
  4. Close the window.
  5. Log out and back in again (because the compose key is handled by the X server (X11) or Wayland display server and not the desktop environment directly).
* **Enable a compose key in Ubuntu:**
  1. Open **Settings → Keyboard → Special Character Entry → Compose Key**.
  2. Disable the **Use layout default** toggle.
  3. Choose a **Compose key position** <mark>← I chose **Right Ctrl**</mark>
  4. Close the **Compose Key** window.
  5. Close the **Settings** window
  6. Log out and back in again to make the changes take effect.

## Use a compose key with macros
Unlike traditional key-combinations where the keys are held down together, with a compose key, you **press each key independently, one after the other**. And when working with lower-case or upper-case letters in macros, they're done the same way you do when typing.

## See your system's master-list of default macros
Your operating system contains a master-list of all of the available default macros in the  `/usr/share/X11/locale/en_US.UTF-8/Compose` file. You can also create your own custom macros.

## Some example macros
These examples use the right **Ctrl** key as the **compose key**:
* ° = **Ctrl** → **o** → **o**
* © = **Ctrl** → **o** → **c**
* ® = **Ctrl** → **o** → **r**
* ™ = **Ctrl** → **t** → **m**
* ½ = **Ctrl** → **1** → **2**
* ⅓ = **Ctrl** → **1** → **3**
* ⅔ = **Ctrl** → **2** → **3**
* ¼ = **Ctrl** → **1** → **4**
* ¾ = **Ctrl** → **3** → **4**
* ⅕ = **Ctrl** → **1** → **5**
* ⅖ = **Ctrl** → **2** → **5**
* ⅗ = **Ctrl** → **3** → **5**
* ⅘ = **Ctrl** → **4** → **5**
* ⅙ = **Ctrl** → **1** → **6**
* ⅚ = **Ctrl** → **5** → **6**
* ⅐ = **Ctrl** → **1** → **7**
* ⅛ = **Ctrl** → **1** → **8**
* ⅜ = **Ctrl** → **3** → **8**
* ⅝ = **Ctrl** → **5** → **8**
* ⅞ = **Ctrl** → **7** → **8**
* ä = **Ctrl** → **"** → **a**
* Ä = **Ctrl** → **"** → **A**
* ö  = **Ctrl** → **"** → **o**
* Ö = **Ctrl** → **"** → **O**
* ü = **Ctrl** → **"** → **u**
* Ü = **Ctrl** → **"** → **U**
* ß = **Ctrl** → **s** → **s**
* → = **Ctrl** → **-** → **>**

## Create custom macros
You can create a file that contains custom macros for auto-typing text and/or emojis:
1. Your system automatically looks for a hidden file named `~/.XCompose` in your user's home directory. If it exists, the macros listed in that file are loaded instead of or in addition to the default ones. Open the file in a text editor if it exists or create it with `touch ~/.XCompose` and open it in a text editor.
  2. Explore the possibilities by pasting the following example template into the file:
     ```
     # Load the system's master list of default key-combinations:
     include "%L"

     # Load my custom macros:
     <Multi_key> <s> <h> <r> <u> <g> : "¯\\_(ツ)_/¯"
     <Multi_key> <e> <m> : "my.email@domain.com"
     <Multi_key> <r> <c> : "🔴"
     <Multi_key> <y> <c> : "🟡"
     <Multi_key> <g> <c> : "🟢"
     <Multi_key> <p> <n> : "📌"
     <Multi_key> <s> <p> : "✨"
     <Multi_key> <t> <o> <d> <o> : "☐"
     <Multi_key> <d> <o> <n> <e> : "☑"
     <Multi_key> <minus> <minus> <greater> : "→"
     ```
     As you can see, the example template tells your system to load the standard library first and then adds your custom macros at the bottom.
  3. Replace the example macros above with your own custom ones using this format:
     ```text
     # Example: <Multi_key> <key1> <key2> : "your character or string here"
     ```
  4. Save the file.
  5. You may need to reboot the computer for the changes to take effect.

## Troubleshooting
* System-Level collisions can happen when a macro that you'd like to use conflicts with a key-combination that's already in use by the OS, sometimes even if just the first character of it is in use. So, for example, if you create a `t ➔ o ➔ d ➔ o` macro, the OS will stop at the `t` or the `to` and wait for something that it expects and, when it doesn't get it, gobble up the do part of your trigger.
* You can check what's in use by the OS by searching the master-list. For example, if you wanted to set up `Ctrl → * → *` as a shortcut for the sparkle emoji, you would do a search for **asterisk** in the master-list to see if it's already in use:
  ```bash
  grep "<asterisk>" /usr/share/X11/locale/en_US.UTF-8/Compose
  ```
* If all else fails, you can comment out the **include** line near the top of the `~/.XCompose` file to disable the default key-combinations and then specify all of your key-combinations manually.
  * Note that this all works under **X11** and **Wayland**.

---
**Tags:** tag-computer tag-desktop tag-hardware tag-software tag-system tag-tweaks
