

# 🐍 Python Debugger Walkthrough

This `readme` demonstrates how to use the Python debugger in a beginner-friendly IDE environment. The goal is to help you understand how breakpoints, watch expressions, and step execution work in a live debugging session.

##  Script Overview

The script you're debugging is `Debugger.py`, which contains:

```python
name = "John"
print("Hello " + name + ".")
age = 40
print(name + " is " + str(age) + " years old.")
```

##  Step-by-Step Debugging Instructions

###  Open the Debugger Panel
- On the right-hand side of the IDE, locate and click the **Debugger** tab to open the debugging interface.

###  Set Breakpoints
- Click the **gutter** (the space to the left of the line numbers) beside:
  - Line 1: `name = "John"`
  - Line 4: `print(name + " is " + str(age) + " years old.")`
- This sets breakpoints that will pause execution at these lines.

###  Add Watch Expressions
- In the **Debugger window**, locate the **Watch Expressions** section.
- Add two expressions to monitor:
  - `name`
  - `age`
- These will display the current values of the variables as the program runs.

###  Run the Program
- Click the **Run** button to execute the script normally.
- This opens a **Runner tab** and prints the output:
  ```
  Hello John.
  John is 40 years old.
  ```

###  Run in Debug Mode
- On the **Runner tab**, click **Run in Debug Mode** to start a debug session.

###  Observe Breakpoint Behavior
- The program will pause at the first breakpoint (line 1).
- You’ll see the debugger highlight the line and show the current state of variables.

###  Step Over Execution
- Click the **Step Over** icon (usually a curved arrow) at the top of the Debugger window.
- This executes line 1 and updates the `name` variable in the **Local Variables** section.

###  Resume to Next Breakpoint
- Click the **blue arrow** (Continue/Resume) to proceed.
- The debugger will pause again at line 4, where the second breakpoint is set.
- The `age` variable will now be visible and updated.

### Final Resume
- Click the **blue arrow** again to finish execution.
- The final output will be printed, and the process will exit with code `0`, indicating success.

##  Debugging Outcome

By following these steps, you’ll gain hands-on experience with:
- Setting and using breakpoints
- Watching variable values in real time
- Controlling execution flow with step and resume actions

---

Let me know if you'd like thi
