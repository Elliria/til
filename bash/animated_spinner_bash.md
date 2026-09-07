# Animated Spinner In Bash
You can create a spinning indicator (▖, ▘, ▝, ▗) in the terminal to indicate that a long-running background command is actively processing. Most such spinners use a pipe, an underscore, a forward-slash and a back-slash, but this one has a very unique and interesting look to it with its seemingly-flying blocks.

Below are two example scripts with notes beneath each that contain explanations and instructions for their use. The first script is a quick one-liner that you can use with any simple task. The second script is more robust and is intended for heavier programs or commands or scripts that are running in the background.

## One-liner plug-and-play script
* Script:
  ```bash
  tput civis; (set +m; (while true; do for x in ▖ ▘ ▝ ▗; do printf "\r%s" "$x"; sleep 0.1; done; done) & sleep 3; kill $! 2>/dev/null); tput cnorm; printf "\r\033[K"`
  ```
* Explanation:
  * `tput civis` temporarily hides the terminal's cursor so it doesn't blink or create a white-box artifact inside the animation.
  * `set +m` disables "monitor mode" inside the bracketed subshell to mute the `+ Terminated job` notifications that Bash usually prints when background tasks are killed.
  * `sleep 0.1` is the delay used to give the animation a natural, fast spinning-speed.
  * `sleep 3` is the example task used to represent your main Bash task.
  * `kill $! 2>/dev/null` terminates the background loop when your main task finishes and routes any stray errors silently into the void. 
  * `tput cnorm`: restores the cursor when the script is finished running.
* Use:
  * To use this with your command, replace `sleep 3` with any command or script you want the animation to run with.
  * To adjust the spinning-speed delay, replace `sleep 0.1` with your custom delay, keeping in mind that a delay of **0.1** or **0.2** seconds is recommended.

## Robust script that handles initialization lag
* Script:
  ```bash
  #!/bin/bash

  sleep 3 &
  PID=$!

  tput civis

  spinner=( '▖' '▘' '▝' '▗' )
  i=1
  sleep 0.1

  while kill -0 $PID 2>/dev/null; do
      printf "\r%s\033[K" "${spinner[i++ % ${#spinner[@]}]}"
      sleep 0.2
  done

  printf "\r\033[K"
  tput cnorm
  printf "Done!\n"
  ```

* Explanation:
  * `PID=$!` captures and stores the unique Process ID of the background job (`&`) so that the script can track its progress.
  * `i=1` and `sleep 0.1` prepares the index and executes a brief, silent delay before the loop begins. This is used to trick terminal engines, like **Konsole**, into processing background subshell allocations behind the scenes to eliminate the visual frame-stutter that's known as "initialization lag".
  * `sleep 0.2` controls the rotation-speed of the animation.
  * `kill -0 $PID` polls the running process to see if it's still alive without interrupting its execution.
  * `i++ % ${#spinner[@]}` uses continuous modulo math to cycle the block positions without nested loops so that it can maintain an entirely uniform and fluid frame-rate.
  * `\033[K` is a hard localized ANSI wipe that removes any trailing pixel-remnants to the right of the cursor on each frame-tick.
* Use:
  * To use this wrapper, replace `sleep 3` on line 3 with your heavy-duty background script or command.
  * To adjust the spinning-speed, change the `sleep 0.2` duration inside the **while** loop to your preferred timing.
  * To adjust the spinning-speed delay, replace `sleep 0.2` inside the **while** loop with your custom delay, keeping in mind that a delay of **0.1** or **0.2** seconds is recommended.

-----
**Tags:** tag-bash tag-bashscripting tag-commandline
