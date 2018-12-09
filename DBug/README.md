# The Debugging with Xcode and LLDB

## The Debugging settings

You can configure the behavior settings when pausing in the debugger.

See Preferences / Befaviors / Pauses, for more details.

# DBug

[DBag](https://github.com/shoheiyokoyama/LLDBDebugging/tree/master/DBug/DBug) is an iOS project with examples of debugs.

## Evaluating variable with LLDB

With the following command,  you can change the behavior at runtime.

### `expression` or `expr`

Evaluate an expression on the current thread. Displays any returned value with LLDB's default formatting.

Syntax: `expression <cmd-options> -- <expr>`

```
(lldb) po variable
true
(lldb) expr variable = false
(lldb) po variable
false
```

### `print`

`print` command takes no arguments, unlike the `expression` command.
This is an abbreviation for `expression --`.

### `po`

`po` command is an abbreviation for `expression -O  --`.
`expression -O  --` ( --object-description ): Display using a language-specific description API, if possible.

### Change animation behavior

The [DBag](https://github.com/shoheiyokoyama/LLDBDebugging/tree/master/DBug/DBug) project is an app that button moves according to the values ​​of `currentDirection` and `currentPoint`.

Before running the animation, you can change animation behavior by changing the value at runtime.

It's possible to change the value with the following command:

```
(lldb) expr currentPoint = .leftBottom
(lldb) expr currentDirection = .random
```

### Update UIKit

It's possible to update the UI state at runtime.
For example, background color, text, constraints.

```
(lldb) expr label.text = "foo"
```

In suspended state, the frame isn't updated. To update, run the following command:

```
(lldb) expr CATransaction.flush()
```

[Flush](https://developer.apple.com/documentation/quartzcore/catransaction/1448270-flush) is typically called automatically at the end of the current runloop, regardless of the runloop mode.


## Process skip

Moving a breakpoint will skip the process.

This magic poses a risk of crashing. so be careful about handling uninitialized instances etc.



## Breakpoint

Click `Edit Breakpoint ...`  or double-click a breakpoint to set breakpoints in detail.

<p align="center">
  <img src="https://github.com/shoheiyokoyama/LLDBDebugging/blob/master/DBug/Assets/edit-breakpoint.png" width="500">
</p>


### Condition



### Ignore

You can ignore the breakpoint by the `ignore` .

For example, if you add 2, the breakpoint is ignored twice and the breakpoint is reached the third time.

### Action

When the breakpoint is reached, processing is executed.

e.g.

```
expr variable = false
```

### Option

If `automatically continue after evaluating actions` is checked, processing will not stop at the breakpoint.

## Symbolic Breakpoint

Using the symbolic breakpoints. you can set a breakpoint based on a symbol, like a method or function name, regardless of where that name might appear in the code.

You can set up a symbolic breakpoint for `-[UILabel setText:]` and it will be triggered any time that method is called. 

e.g.

- `-[UILabel setText:]`
- `-[UIApplication main]`
- `-[UIViewController viewDidLoad]`

To create a symbolic breakpoint in Xcode, click the + button in the lower-left toolbar within the Breakpoint Navigator and choose “Add Symbolic Breakpoint…”

Next, Add `-[UILabel setText:]` in Symbol or the the following command in LLDB:

```
(lldb) breakpoint set --name "-[UILabel setText:]"
```

<p align="center">
  <img src="https://github.com/shoheiyokoyama/LLDBDebugging/blob/master/DBug/Assets/label-symbolic-breakpoint.png" width="700">
</p>


In this state, you can see the arguments passed to the function.

```
(lldb) po $arg1
<UILabel: 0x7fb1ae405cd0; frame = (0 0; 275 50); text = 'Direction: Clockwise 🕐'; opaque = NO; autoresize = RM+BM; userInteractionEnabled = NO; layer = <_UILabelLayer: 0x600000292ad0>>

(lldb) po (SEL)$arg2
"setText:"

(lldb) po $arg3
Direction: Clockwise 🕐
```

## Requirements

- Xcode 10.1
- Swift 4.2