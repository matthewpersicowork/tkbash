# tkbash
Build fancy graphical user interfaces with simple bash commands!
## Bash wrapper for Tcl/Tk gui

![tkbash-gui](https://i.imgur.com/J8XUtSB.png)
```
#!/bin/bash
tkbash 1 window --theme clam -w 290 -h 250 --title "I am butiful" --alwaysontop 1 --icon "kitten.png"
tkbash 1 label label1 --relx 0.4 -y 10 -w 130 -h 20 -t "I like bananas."
tkbash 1 select select1 --relx 0.4 -y 30 -w 130 -h 20 -t "Black|White|Green|Blue"
tkbash 1 button button1 -x 165 -y 75 -w 120 -h 30 -t "Delete text" --command "
    tkbash 1 text1 -t \" \"
    notify-send \"You selected color \$(tkbash 1 get select1)!\""
tkbash 1 text text1 -x 165 -y 105 -w 120 -h 120 -t "Yorem Lipsum"
tkbash 1 image image1 -x 10 -y 60 -w 125 -h 120 --image "kitten.png"
```

- Pure bash code (internally translates into respective [Tcl/Tk](https://www.tcl.tk/) code, sent to `wish` background process)
- Same syntax for adding and editing elements
- Gui runs in background, accessible from everywhere and everytime using `tkbash <gui-id> dosomething...`
- Pass callback code directly as `--command <command>`
- Print element contents with `get <variable>`
- Currently supports: text (input), combobox (select), checkbutton (checkbox), radiobutton groups, button, label, image
- Every element gets placed with absolute or window-relative coordinates. No geometry managers supported.
- Entire functionality of Tk available if desired via `--tkcommand`
- Configure multiple GUIs that access each other

Snapshot of `tkbash --help`:

```
>>> TkBash. Basic Bash superset for Tcl/Tk gui. Just a layer, NO reimplementation of Tk. Place elements using absolute coordinates x/y/w/h.

USAGE:
	tkbash <gui_id> [<element>] <variable> [-options...]
		Add / edit element.
	tkbash <gui_id> get <variable>
		Print the value of the associated element.
	tkbash <gui_id> window [-options...]
		Set window properties.
	tkbash <gui_id> [<variable>] --tkcommand <command>
		Execute custom Tcl/Tk code. For advanced element configuration not provied by tkbash itself. "--tkcommand" can also be "--tk". Variable can be ommitted for full access. Example: "tkbash mygui mybutton --tkcommand 'configure -background green'" or "tkbash mygui --tkcommand 'wm maxsize . 200 200'".


		gui_id
			Unique identifier for the GUI. To be reused with subsequent tkbash calls.
		element
			Any of the elements listed below. Required when adding a new one.
		variable
			Variable to hold the value associated with the element.

		When adding an element for the first time, positional options are required:
		-x <x-position>
			
		-X, --relx <x-position>
			
		-y <y-position>
			
		-Y, --rely <y-position>
			
		-w, --width <width>
			blub
		-h, --height <height>
			bla
Window
Set options for the entire interface / window as described above.
	--theme <themename>
		Set the look and feel used by all ttk-controls. Available themes usually include clam, alt, default and classic.
	--title <title>
		Set the window title.
	--alwaysontop, --topmost <switch>
		Set the window's always on top behaviour. Activate with 0, deactivate with 1.
	--alpha, transparency <alpha>
		Set the transparency of the entire window. Floating value between 0 and 1.
	--icon <iconpath>
		Set the window icon.
	--resizable <switch>
		Set the window's resizable behaviour. Activate with 0, deactivate with 1.
	--close, --exit
		Destroy, close, exit, die, kill, waste the window.

ELEMENTS
	button / submit
		Options:
			-t, --text, --content <text>
				The text that will be displayed on the button.
			-c, --command <command>
				Bash code to be executed when the element is clicked.
		Get:
			You cannot retrieve any value from a button.
	text / input / edit / textfield / textarea
		A text element is a multiline input area for text.
		Options:
			-t, --text, --content <text>
				Set the contents of the text field.
		Get:
			Prints the contents of the text field.
	label
		A label element displays plain text somewhere on the gui.
		Options:
			-t, --text, --content <text>
				Set the contents of the label.
		Get:
			You cannot retrieve any value from a label.
	image / picture / img / bmp / bitmap / p
		Display an image.
		Options:
			--image <path>
				Path of the image to be shown.
		Get:
			You cannot retrieve any value from an image.
		Note: Internally, this does the same like 'label', meaning these two element names are interchangeable.
	checkbutton / checkbox / check / tick / toggle
		For boolean values.
		Options:
			-t, --text, --content <text>
				Set the text to be displayed next to the tick.
			-c, --command <command>
				Bash code to be executed when the element is clicked.
		Get:
			Prints 1 when checked, 0 when unchecked, nothing when pristine.
	radiobutton / radio
		Multiple radiobuttons form a group. Only one radiobutton in a group can be active at the same time.
		Options:
			[required] --group <group-id>
				Assign this radiobutton to a group. Can be any string. See 'get' below. In contrary to most options, this one can not be changed afterwards, meaning if you pass it any other time that on element creation, it gets ignored.
			-t, --text, --content <text>
				Set the text to be displayed next to the radiobutton.
			-c, --command <command>
				Bash code to be executed when the element is clicked.
		Get:
			Every radiobutton gets internally assigned a number. The first radiobutton of a group has the number 0, the second 1, third 2 and so on. When you call the 'get' method for any of the radios contained in this group, it will print out the number of the selected radio (or nothing when pristine) in its group: 0 or 1 or 2 or whatever. The name of the group does not matter for retrieving the value. The group is only needed when a radiobutton.
	combobox / select / dropdown / dropdownmenu
		Select one of many values. Behaves like a classical HTML-select (the Tk-state 'readonly' is active).
		Options:
			-t, --text, --content <text>
				A pipe-delimited list (e.g. "option1|option2|option3") of options for this select which the user will be able to choose from.
		Get:
			Prints the selected value, e.g. "option1" when the first value is chosen.
	--disabled
		Dissables (greys out) the command. Can be used with any element, will however take no action  on labels.

Note on the -c, --command option: The command passed will be executed asynchronously from within a subshell. Thus, session variables cannot be accessed.

```
