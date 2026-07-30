# Bash Scripting — Detailed Study Notes for Absolute Beginners

This guide assumes you have never written a script before. Every concept is explained in plain language first, then shown with an example, then explained again so you understand *why* it works.

---

## 1. What is Bash? (And Why Learn It?)

Think of your computer's operating system (like Linux) as a big, complex machine. A **shell** is simply a way for you to "talk" to that machine using typed commands instead of clicking buttons.

**Bash** (short for "Bourne Again SHell") is one specific type of shell — the most popular one on Linux. It's also available on:
- **macOS** (built in)
- **Windows** (through something called WSL — Windows Subsystem for Linux)

### Why should you bother learning it?
- It's been around since the early days of Linux and isn't going away.
- Many other tools (like Ansible) work *alongside* Bash, not instead of it.
- Small bash scripts can save you huge amounts of time by automating boring, repetitive tasks (like copying files, checking logs, or renaming things in bulk).

### Why might you NOT use only Bash?
- Bash doesn't support **object-oriented programming** — a more advanced programming style.
- Its syntax (the way you write it) is harder to read than something like Python.
- For big, complex automation across many computers, tools like **Python** or **Ansible** are often better.

**Bottom line:** Learn Bash for small-to-medium tasks and quick automation. Learn Python if you need more advanced logic.

---

## 2. Getting Comfortable in the Terminal

Before writing scripts, you need to know a few basic commands. Think of these as the "words" you'll use to build "sentences" (scripts) later.

### `echo` — print text to the screen
```bash
echo Hello
```
**Output:**
```
Hello
```
Here, `echo` is the **command**, and `Hello` is the **argument** (the piece of information you're giving the command to work with).

### `cat` — show the contents of a file
First you need a file to look at. Let's make one using a text editor called **Vim**.

```bash
vim text_file.txt
```

This opens Vim. Vim has different "modes":
- Press **`i`** → enters **Insert mode**, where you can type text.
- Type your text, e.g. `Hello World`
- Press **`Esc`** → exits Insert mode, back to **Command mode**.
- Type **`:w`** → saves (writes) the file.
- Type **`:q`** → quits Vim.
- Or combine both: **`:wq`** → saves AND quits in one step.
- If you made a mistake and don't want to save: **`:q!`** → quits WITHOUT saving.

Now that the file exists, view its contents:
```bash
cat text_file.txt
```
**Output:**
```
Hello World
```

### `ls` — list files in your current folder
```bash
ls
```
This shows you all files/folders where you currently are.

Add `-l` (a **flag** — an extra option you give a command) for more detail:
```bash
ls -l
```
This shows things like file permissions, size, and date modified.

### `pwd` — show your current location
```bash
pwd
```
**Output (example):**
```
/home/yourname
```
`pwd` stands for "print working directory" — it tells you exactly which folder you're currently working in.

---

## 3. Writing Your First Bash Script

A **script** is just a text file full of commands that run one after another, automatically.

### Step 1: Create the file
```bash
vim shell_test.sh
```

### Step 2: Add the "shebang" line
At the very top of every script, add this special line:
```bash
#!/bin/bash
```
**What does this do?** It tells the computer: "Use the Bash program to run this file." Without it, the computer won't know what kind of script it is.

💡 To find the exact path to bash on your system, run:
```bash
echo $shell
```
This might print something like `/bin/bash`. Use whatever it shows you.

### Step 3: Write a command
Below the shebang line, add:
```bash
echo Hello World
```

So your full file looks like:
```bash
#!/bin/bash
echo Hello World
```
Save and quit with `:wq`.

### Step 4: Give the file permission to run
By default, Linux won't let you run a script — it needs permission first.
```bash
chmod u+x shell_test.sh
```
**Breaking this down:**
- `chmod` = "change mode" (change permissions)
- `u` = only for the **user** (you, the file owner) — not everyone on the system
- `+x` = add **execute** permission (permission to run it)

You can check permissions with `ls -l` — before this command, you'd see no execute permission; after, you will.

### Step 5: Run the script
```bash
./shell_test.sh
```
**Output:**
```
Hello World
```

📌 The `./` means "look for this file in the current folder."

---

## 4. Variables

A **variable** is like a labeled box where you store a piece of information so you can reuse it later, instead of typing it over and over.

### Creating a variable
```bash
first_name=Yash
```
⚠️ No spaces around the `=` sign! `first_name = Yash` will cause an error.

### Using a variable
To use the value stored in a variable, put a `$` in front of its name:
```bash
echo Hello $first_name
```
**Output:**
```
Hello Yash
```

### Example script with variables
```bash
#!/bin/bash
first_name=Yash
last_name=Rawat
echo Hello $first_name $last_name
```
**Output:**
```
Hello Yash Rawat
```

### Getting input from the user
Instead of hardcoding the name, you can ask the user to type it in:
```bash
#!/bin/bash
echo "What is your first name?"
read first_name
echo "What is your last name?"
read last_name
echo "Hello $first_name $last_name"
```
When you run this script, it will pause and wait for you to type an answer each time it hits a `read` line.

---

## 5. Positional Arguments

Instead of asking the user to type answers while the script runs, you can also give the script information **immediately when you run it**. These are called **positional arguments**.

### Example
```bash
#!/bin/bash
echo Hello $1 $2
```
Save this as `pos_arg.sh`, make it executable, then run:
```bash
./pos_arg.sh Yash Rawat
```
**Output:**
```
Hello Yash Rawat
```

**How this works:**
- `$1` = the first word typed after the script name (`Yash`)
- `$2` = the second word (`Rawat`)
- Position `0` is reserved for the script's own name, so your arguments start counting from 1.

Think of it like handing arguments to the script in a specific order, separated by spaces.

---

## 6. Piping and Redirection (Sending Output Places)

### Piping with `|`
Piping lets you take the output of one command and feed it directly into another command.

```bash
ls -l /usr/bin | grep bash
```
**What's happening:**
1. `ls -l /usr/bin` lists everything in the `/usr/bin` folder (there could be hundreds of files).
2. The `|` symbol grabs that entire output and "pipes" it into the next command.
3. `grep bash` filters that output, only showing lines that contain the word "bash".

Think of `|` like a physical pipe connecting the output of one machine to the input of another.

### Sending output to a file with `>` and `>>`

**`>` — overwrite**
```bash
echo Hello World > hello.txt
```
This creates (or overwrites!) `hello.txt` with the text "Hello World".

Check it:
```bash
cat hello.txt
```
**Output:** `Hello World`

Now watch what happens if you do it again with different text:
```bash
echo Good day to you > hello.txt
cat hello.txt
```
**Output:** `Good day to you`

⚠️ Notice "Hello World" is GONE. The `>` symbol **erases** whatever was in the file before writing the new content.

**`>>` — append (add without erasing)**
```bash
rm hello.txt                          # delete the file first
echo Hello World >> hello.txt
echo Good day to you >> hello.txt
cat hello.txt
```
**Output:**
```
Hello World
Good day to you
```
Now both lines are there, because `>>` **adds** to the file instead of replacing it. This is very useful for things like log files, where you want to keep adding new entries over time.

### Feeding input INTO a command
These are the reverse — instead of sending output out, you're feeding input in.

**`<` — feed a file's content into a command**
```bash
wc -w < hello.txt
```
`wc -w` counts words. Using `<` feeds the file directly as input, so you only get the word count — not the filename attached to it (which is what happens if you type `wc -w hello.txt` instead).

**`<<` — feed multiple lines of text**
```bash
cat << EOF
This is line one
This is line two
EOF
```
This lets you type multiple lines directly, and it stops collecting input once it sees `EOF` again (you can use any word instead of `EOF`).

**`<<<` — feed a single string**
```bash
wc -w <<< "Hello there word count"
```
This directly hands that quoted string as input to `wc -w`, without needing a file at all.

---

## 7. Test Operators (Checking If Something Is True)

Bash has a built-in way to check whether something is true or false, using square brackets `[ ]`.

### Comparing text (strings)
```bash
[ "Hello" = "Hello" ]
echo $?
```
**Output:** `0`

**What is `$?`?** It shows the **exit code** of the last command — a special number that tells you whether it succeeded or failed.
- `0` = success / true
- Any other number (usually `1`) = failure / false

⚠️ You MUST have spaces around the brackets and the `=` sign: `[ "Hello" = "Hello" ]` — not `["Hello"="Hello"]`.

### Comparing numbers
```bash
[ 1 = 0 ]
echo $?
```
**Output:** `1` (false — they're not equal)

For numbers specifically, it's better to use `-eq` (equal), which properly checks numeric values:
```bash
[ 1 -eq 1 ]
echo $?
```
**Output:** `0` (true)

**Common numeric test operators:**
| Operator | Meaning |
|---|---|
| `-eq` | equal to |
| `-ne` | not equal to |
| `-gt` | greater than |
| `-lt` | less than |
| `-ge` | greater than or equal to |
| `-le` | less than or equal to |

---

## 8. If / Elif / Else Statements

This lets your script make decisions and do different things depending on a condition.

### Example: a simple "login check" script
```bash
#!/bin/bash
if [ "${1,,}" = "admin" ]; then
    echo "You're the boss here. Welcome."
elif [ "${1,,}" = "help" ]; then
    echo "Just enter your username."
else
    echo "I don't know who you are, but you're not the boss of me."
fi
```

**Breaking this down line by line:**
- `if [ condition ]; then` — starts the check. If the condition is true, run the next lines.
- `${1,,}` — this takes the first positional argument (`$1`) and converts it to lowercase. The double comma `,,` is what triggers the lowercase conversion. This means "Admin", "ADMIN", and "admin" will all match.
- `elif [ condition ]; then` — short for "else if". If the FIRST condition was false, check THIS condition instead.
- `else` — if none of the above conditions were true, do this instead.
- `fi` — this is REQUIRED to close every `if` statement (it's "if" spelled backwards).

**Running it:**
```bash
./login.sh Admin
```
**Output:**
```
You're the boss here. Welcome.
```

---

## 9. Case Statements

When you have MANY possible options to check (rather than just 2-3), `case` statements are cleaner than writing lots of `elif` lines.

### Example
```bash
#!/bin/bash
case "${1,,}" in
    username|admin)
        echo "You're the boss here. Welcome."
        ;;
    help)
        echo "Just enter your username."
        ;;
    *)
        echo "Hello there, but you're not the boss of me. Please enter a valid username."
        ;;
esac
```

**Breaking this down:**
- `case VALUE in` — starts checking `VALUE` against a list of options.
- `username|admin)` — the `|` here means "OR". This line matches if the input is EITHER "username" OR "admin".
- `;;` — marks the end of that particular option's action (like a "break").
- `*)` — this is the **catch-all** option (like `else`). If nothing else matched, this runs.
- `esac` — closes the `case` statement (it's "case" spelled backwards).

**Running it:**
```bash
./login.sh help
```
**Output:**
```
Just enter your username.
```

---

## 10. Arrays

An **array** is a variable that can hold MULTIPLE values at once, instead of just one.

### Creating an array
```bash
my_first_list=(1 2 3 4 5)
```
Notice: values are separated by **spaces** (not commas), and wrapped in parentheses `()`.

### Printing the array
If you try:
```bash
echo $my_first_list
```
**Output:**
```
1
```
This only shows the FIRST item — not what most people expect!

To print the WHOLE array, you need special syntax:
```bash
echo ${my_first_list[@]}
```
**Output:**
```
1 2 3 4 5
```

### Printing one specific item
Arrays are numbered starting at **0** (not 1!). So:
```bash
echo ${my_first_list[0]}   # prints: 1  (the FIRST item)
echo ${my_first_list[2]}   # prints: 3  (the THIRD item)
```

---

## 11. For Loops

A **for loop** lets you repeat an action once for EVERY item in a list (like an array), instead of writing the same code over and over.

### Example: counting letters in each word of an array
```bash
#!/bin/bash
my_first_list=(apple banana cherry)

for item in ${my_first_list[@]}; do
    echo -n $item | wc -c
done
```

**Breaking this down:**
- `for item in ${my_first_list[@]};` — for each value in the array, temporarily call it `item`.
- `do` — marks the start of what to do for each item.
- `echo -n $item | wc -c` — print the item (without adding a newline, thanks to `-n`), then pipe it into `wc -c` which counts the characters.
- `done` — marks the end of the loop.

Every time the loop runs, `item` becomes the next value in the array — first "apple", then "banana", then "cherry" — until the list runs out.

---

## 12. Functions

A **function** is a mini-script INSIDE your script — a reusable block of commands you can run just by calling its name, instead of retyping the same lines everywhere.

### Basic example
```bash
#!/bin/bash

show_uptime() {
    up=$(uptime -p)
    since=$(uptime -s)
    cat << EOF
--------------------------------
This machine has been up for ${up}
It has been running since ${since}
--------------------------------
EOF
}

show_uptime
```

**Breaking this down:**
- `show_uptime() { ... }` — this DEFINES the function. Everything between the curly braces `{ }` is what the function does.
- `up=$(uptime -p)` — runs the command `uptime -p` and stores its output inside the variable `up`. The `$( )` syntax means "run this command and capture its output."
- The function is only ever RUN when you actually type its name — in this case, `show_uptime` on the last line calls it.

📌 A function must be **defined before** it is called in the script (Bash reads top to bottom).

### Local variables — an important gotcha!

By default, variables created INSIDE a function are actually **global** — meaning they can accidentally overwrite variables with the same name OUTSIDE the function. This can cause confusing bugs in bigger scripts.

**Problem example:**
```bash
#!/bin/bash
up="Before function"
since="Before function"
echo $up
echo $since

show_uptime() {
    up=$(uptime -p)
    since=$(uptime -s)
}

show_uptime
echo $up      # this has now CHANGED! Not what we want.
echo $since
```

**The fix — use `local`:**
```bash
show_uptime() {
    local up=$(uptime -p)
    local since=$(uptime -s)
}
```
Adding `local` means these variables only exist INSIDE the function, and won't touch anything outside it.

### Passing arguments to functions
Just like scripts can take positional arguments (`$1`, `$2`), so can functions!

```bash
#!/bin/bash

greet() {
    echo Hello $1
}

greet Yash
```
**Output:**
```
Hello Yash
```

### Using exit codes in functions
Functions can also return a success/failure code using `return`:

```bash
#!/bin/bash

check_user() {
    if [ "${1,,}" = "Yash" ]; then
        return 0     # 0 = success
    else
        return 1     # 1 = failure
    fi
}

check_user "$1"
if [ $? = 1 ]; then
    echo "Someone unknown called the function."
fi
```
- `return 0` inside a function means "success" — checked later with `$?`.
- `return 1` (or any non-zero number) means "failure."

---

## 13. AWK (Filtering and Extracting Text)

`awk` is a tool for pulling out specific pieces (columns) from text — super useful when working with structured data like logs or CSV files.

### Example with a plain text file
Create a file `test.txt` containing:
```
one two three
```

```bash
awk '{print $1}' test.txt
```
**Output:** `one`

```bash
awk '{print $2}' test.txt
```
**Output:** `two`

**How this works:** By default, `awk` treats each SPACE as a separator between "columns." `$1` = first column, `$2` = second column, and so on (note: this is different from bash's `$1` positional arguments — inside `awk`, `$1` means "first field of the current line").

### Example with a CSV file (comma-separated)
Create `test.csv` containing:
```
one,two,three
```

```bash
awk -F ',' '{print $1}' test.csv
```
**Output:** `one`

**The `-F ','` flag** tells `awk`: "Instead of using spaces as the separator, use commas."

### Piping command output into awk
```bash
echo "just get this word: hello" | awk '{print $5}'
```
**Output:** `hello`

(Because "hello" is the 5th word, separated by spaces, in that sentence.)

You could also split by a different character:
```bash
echo "just get this word: hello" | awk -F ':' '{print $2}'
```
This splits at the colon `:` instead of spaces.

---

## 14. SED (Find and Replace in Files)

`sed` lets you search for a word/pattern in a file and replace it — like "Find & Replace" in a word processor, but from the command line.

### Basic example
Create `set_test.txt` containing some text that includes the word "fly".

```bash
sed 's/fly/grasshopper/g' set_test.txt
```

**Breaking this down:**
- `s` = **substitute** mode (find and replace)
- `fly` = the word to find
- `grasshopper` = the word to replace it with
- `g` = do this **globally** — replace EVERY occurrence in the file, not just the first one found

### Keeping a backup of the original
If you want to actually change the file (not just preview the change) while keeping a safety copy:
```bash
sed -i.original 's/fly/grasshopper/g' set_test.txt
```
- `-i` = edit the file "in place" (actually save the changes)
- `.original` = the suffix used to name the backup file (so your original content is saved as `set_test.txt.original`)

Without `-i`, `sed` just PREVIEWS the change in your terminal without actually modifying the file.

---

## Quick Reference Cheat Sheet

| Concept | Syntax | Example |
|---|---|---|
| Shebang (top of every script) | `#!/bin/bash` | — |
| Create a variable | `name=value` | `age=25` |
| Use a variable | `$name` | `echo $age` |
| Get user input | `read var` | `read age` |
| Positional argument | `$1`, `$2`, ... | `echo $1` |
| Pipe output to another command | `cmd1 \| cmd2` | `ls \| grep txt` |
| Overwrite file with output | `cmd > file` | `echo hi > file.txt` |
| Append output to file | `cmd >> file` | `echo hi >> file.txt` |
| Feed file as input | `cmd < file` | `wc -w < file.txt` |
| Feed multiple lines as input | `cmd << WORD` | `cat << EOF` |
| Feed a single string as input | `cmd <<< "text"` | `wc -w <<< "hi"` |
| Test equality (string) | `[ a = b ]` | `[ "$x" = "y" ]` |
| Test equality (number) | `[ a -eq b ]` | `[ 1 -eq 1 ]` |
| If statement | `if [ ]; then ... fi` | see section 8 |
| Case statement | `case $x in ... esac` | see section 9 |
| Create an array | `arr=(a b c)` | `arr=(1 2 3)` |
| Print whole array | `${arr[@]}` | `echo ${arr[@]}` |
| Print one array item (starts at 0) | `${arr[0]}` | `echo ${arr[0]}` |
| For loop | `for x in list; do ... done` | see section 11 |
| Define a function | `name() { ... }` | see section 12 |
| Local variable (inside function) | `local name=value` | `local up=5` |
| Function's exit result | `return 0` (success) / `return 1` (fail) | — |
| Check last exit code | `$?` | `echo $?` |
| Filter/extract text columns | `awk '{print $1}' file` | see section 13 |
| Find and replace in file | `sed 's/old/new/g' file` | see section 14 |

---

### Final Tip
Don't try to memorize all of this at once. Open a terminal, try each example yourself, and change small parts of it to see what happens. Bash scripting is learned by doing, not just reading.
