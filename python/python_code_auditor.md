# Python Code Auditor

## About
A directory-wide code-auditor that finds and displays errors in your Python files.

## Overview
The **hygiene.py** script below uses a session-manager to crawl the relevant directory recursively to find and examine all of its Python files for errors. It avoids the traditional bottleneck of stopping on the first error by using a context-manager to intercept the errors, gather all of them into a list, inject that list into an **ExceptionGroup**, raise them concurrently from there, handle them without crashing, and display the result in your terminal window. Two modes of operation are provided, with SAFE MODE as the default and ACTIVE MODE being available for power-users.

## Features
* Detailed mode notifications that identify the current mode.
* Paths appended to sub-exceptions to smooth out traceback noise and help with navigation when fixing errors.
* The context manager binds the traversal life-cycles to automated cleanup and error-raising.
* Unhandled groups terminate the interpreter with a non-zero exit-status.

## ⚠️ CRITICAL operational modes and best practice
Before running the script, it's vital to understand its two distinct modes of operation so you can decide how to use it.

* **Modes:**
  * **SAFE MODE**
    * This **reads** top-level code.
    * It uses Python's native AST compilation to read code as text without executing it. It's safe to run on any unexamined, untrusted, or potentially-malicious code-base.
    * This is the **default mode** for the script. It will use this mode when you execute the script from within an IDE or on the command-line without any command-line options.
  * **ACTIVE MODE**:
    * This **reads and executes** top-level code.
    * This actively executes top-level code to catch runtime errors (like `NameError`).
    * It can only be run on the command-line with the `--run` or -`r` option.
* **Best Practice:**
  * Never run this script with the `--run` or `-r` option on unexamined pull requests, untrusted third-party repositories, or unknown code. Doing so can trigger arbitrary code execution on your local machine. **Always review the files statically first!**

## The script
Save the following script as **hygiene.py** to run it on your system:
```python
#!/usr/bin/env python3
import importlib.machinery
import importlib.util
import os
import sys

"""
⚠️ IMPORTANT: While it's completely safe to run a script like this one
on your personal scripts, this script engine is capable of actively
executing code at the module level when the code is imported. If you run
it inside a directory that contains a script with a naked, unwrapped
command, like os.remove("data.db") or a live network request, that code
will fire off during the audit if this script is in ACTIVE MODE. As a
safety precaution, this script has been written to be careful by default
by staying in the super-safe read-only SAFE MODE, but allowing you the
ability to pass the --run or -r command-line option to it to make it
less careful and more informative in ACTIVE MODE if you like.

USAGE IN SAFE MODE:
# Run it on the current directory: hygiene.py
# Run it on a subdirectory off of the current directory: hygiene.py ./foo
# Run it on the specified directory: hygiene.py /home/username/foo

USAGE IN ACTIVE MODE:
# Run it on the current directory: hygiene.py -r
# Run it on the current directory: hygiene.py --run
# Run it on a subdirectory off of the current directory: hygiene.py -r ./foo
# Run it on a subdirectory off of the current directory: hygiene.py --run ./foo
# Run it on the specified directory: hygiene.py -r /home/username/foo
# Run it on the specified directory: hygiene.py --run /home/username/foo
"""

class AuditSession:
    """A context-manager that intercepts crashes and bundles them 
    into an ExceptionGroup at the end.
    """
    def __init__(self, session_name="Repository Audit"):
        self.session_name = session_name
        self.errors = []

    def __enter__(self):
        # Return the manager object, itself, into the 'as' variable:
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        # If a crash happens inside the block, Python sends it here:
        if exc_val is not None:
            self.errors.append(exc_val)
            # Returning True tells Python not to crash, but to keep going:
            return True 
        
        # When the block ends naturally, check if any errors were collected:
        if self.errors:
            raise ExceptionGroup(f"❌ {self.session_name} failed with sub-exceptions:", self.errors)

    def log_failure(self, exception_instance):
        """Allows manually adding an error without needing a literal code crash."""
        self.errors.append(exception_instance)

# Fire up a custom audit-session context-manager:
def audit_directory(target_dir=".", execute_mode=False):
    """Scans the specified directory for all .py files, toggling between
    SAFE MODE and ACTIVE MODE based on whether or not arguments are
    provided on the command-line.
    """
    mode_label = "ACTIVE MODE" if execute_mode else "SAFE MODE"
    print(f"📁 Initializing directory-wide [{mode_label}] sweep for: {os.path.abspath(target_dir)}")
    
    # Get the name of this script:
    current_script_name = os.path.basename(sys.argv[0])

    # Run the audit-session:
    with AuditSession( f"Global Directory { mode_label} Audit") as session:
        for root, dirs, files in os.walk(target_dir):
            for file in files:
                if file.endswith('.py') and file != current_script_name:
                    file_path = os.path.join(root, file)
                    print(f"🔍 Auditing: {os.path.relpath(file_path, target_dir)}")

                    # Test for exceptions:                
                    try:
                        if execute_mode:
                            # ACTIVE MODE: Read AND execute top-level statements:
                            loader = importlib.machinery.SourceFileLoader(file, file_path)
                            spec = importlib.util.spec_from_loader(file, loader)
                            module = importlib.util.module_from_spec(spec)
                            loader.exec_module(module)
                        else:
                            # SAFE MODE: Only read top-level statements:
                            with open(file_path, 'r', encoding='utf-8') as f:
                                code_content = f.read()
                            compile(code_content, filename=file_path, mode='exec')

                    # Handle the exceptions:                        
                    except Exception as e:
                        # Note the location of each error at the bottom of each sub-exception:
                        e.add_note(f"📍 Location of error: {os.path.relpath(file_path, target_dir)}")
                        session.log_failure(e)
                    except BaseException as e:
                        session.log_failure(e)

    # Announce success:
    print(f"\n✨ All files successfully verified in [{mode_label}] mode with zero faults!")

# Run this script from the terminal:
if __name__ == "__main__":
    import sys
    
    # Parse the arguments:
    args = sys.argv[1:]
    execute_code = False
    target_path = "."
    
    # Check if the user passed the execution toggle:
    if "--run" in args or "-r" in args:
        execute_code = True
        # Filter out the option so it isn't accidentally considered to be a directory path:
        args = [a for a in args if a not in ("--run", "-r")]
        
    # Check for command-line options:
    if args:
        target_path = args[0]
        
    # Print a safety manifesto to the terminal based on the chosen mode:
    if execute_code:
        print("⚠️  [SAFETY NOTICE]: Operating in ACTIVE MODE (--run or -r enabled).")
        print("   Code blocks and module-level scripts will be fully executed.")
        print("   Note: Function bodies will only throw faults if they're actively called.\n")
    else:
        print("🛡️  [SAFETY NOTICE]: Operating in SAFE MODE (read-only).")
        print("   Files will only be checked for structural syntax and indentation faults.")
        print("   Note: Runtime errors (like undefined variables or bad division) are")
        print("   deliberately ignored in this mode by design to prevent accidental execution.\n")
        print("   👉 To enable ACTIVE MODE testing, in which code blocks and module-level")
        print("   scripts will be fully executed, run: python3 hygiene.py <path> --run\n")
        print("-" * 80)

    # Call the engine with safety-switch passed along:
    audit_directory(target_path, execute_mode=execute_code)
```
