# Bash Scripting Study Notes (Beginner Friendly)

## 1. What is Bash?

- **Bash** = "Bourne Again Shell". It's a program that lets you talk to your computer using text commands.
- It's the most popular shell on Linux, and also works on macOS and Windows (through WSL).
- **Good for:** automating simple, repetitive tasks.
- **Not great for:** big, complex programs (no object-oriented programming). For that, use Python.

---

## 2. Basic Terminal Commands

| Command | What it does |
|---|---|
| `echo hello` | Prints "hello" to the screen |
| `cat file.txt` | Prints the contents of a file |
| `ls` | Lists files in the current folder |
| `ls -l` | Lists files with extra details (permissions, size, etc.) |
| `pwd` | Shows which folder you're currently in |

**Vim basics (text editor used in this course):**
- `i` → enter Insert mode (start typing)
- `Esc` → exit Insert mode
- `:w` → save
- `:q` → quit
- `:wq` → save AND quit
- `:q!` → quit WITHOUT saving

---

## 3. Writing Your First Script

1. Create a file: `vim myscript.sh`
2. Add this line at the very top — it's called a **shebang**, and tells the system which interpreter to use:
   ```bash
   #!/bin/bash
   ```
3. Write your commands below it, e.g. `echo Hello World`
4. Save and quit (`:wq`)
5. Give it permission to run:
   ```bash
   chmod u+x myscript.sh
   ```
6. Run it:
   ```bash
   ./myscript.sh
   ```

💡 **Why the shebang?** Just because you're *in* a bash shell doesn't mean the system assumes your script is bash. Always specify it.

---

## 4. Variables

Variables store values you can reuse.

```bash
first_name="Herbert"
echo "Hello $first_name"
```

**Getting input from the user:**
```bash
echo "What is your name?"
read name
echo "Hello $name"
```

---

## 5. Positional Arguments

These are values you pass to a script when you run it.

```bash
#!/bin/bash
echo "Hello $1 $2"
```
Run it like this:
```bash
./script.sh Herbert Lindemans
```
Output: `Hello Herbert Lindemans`

📌 `$1` = first argument, `$2` = second argument, and so on.

---

## 6. Redirecting Input & Output

| Symbol | Meaning |
|---|---|
| `command1 \| command2` | **Pipe** — sends output of command1 into command2 |
| `command > file` | Sends output to a file (⚠️ overwrites file!) |
| `command >> file` | Appends output to a file (keeps old content) |
| `command < file` | Feeds a file's content INTO a command |
| `command << WORD` | Feeds multiple lines of text into a command |
| `command <<< "text"` | Feeds a single string into a command |

**Examples:**
```bash
echo "Hello World" > hello.txt      # overwrite
echo "Good day" >> hello.txt        # append
ls -l /usr/bin | grep bash          # filter output with pipe
```

---

## 7. Test Operators (Comparing Values)

Used to check if something is true or false.

```bash
[ "hello" = "hello" ]     # comparing text
echo $?                   # 0 = true/success, 1 = false/error

[ 1 -eq 1 ]               # comparing numbers (-eq = equal)
```

**Common number operators:** `-eq` (equal), `-ne` (not equal), `-gt` (greater than), `-lt` (less than)

---

## 8. If / Elif / Else

```bash
#!/bin/bash
if [ "${1,,}" = "admin" ]; then
    echo "You're the boss here. Welcome."
elif [ "${1,,}" = "help" ]; then
    echo "Enter your username."
else
    echo "I don't know who you are."
fi
```

📌 `${1,,}` converts the input to lowercase, so "Admin" and "admin" both match.
📌 Every `if` must end with `fi`.

---

## 9. Case Statements

Cleaner way to check multiple options instead of many `elif`s.

```bash
#!/bin/bash
case "${1,,}" in
    username|admin)
        echo "You're the boss here. Welcome."
        ;;
    help)
        echo "Enter your username."
        ;;
    *)
        echo "Hello there, please enter a valid username."
        ;;
esac
```

📌 `*` = catch-all (like "else"). Every `case` ends with `esac`.

---

## 10. Arrays

Arrays let you store multiple values in one variable.

```bash
my_list=(1 2 3 4 5)
echo "${my_list[@]}"     # prints ALL items
echo "${my_list[0]}"     # prints the FIRST item (index starts at 0!)
```

---

## 11. For Loops

Used to repeat an action for each item in a list.

```bash
for item in "${my_list[@]}"; do
    echo -n "$item" | wc -c
done
```

📌 Structure: `for VARIABLE in LIST; do ... done`

---

## 12. Functions

Functions = mini-programs inside your script, so you don't repeat code.

```bash
#!/bin/bash
show_uptime() {
    up=$(uptime -p)
    since=$(uptime -s)
    echo "Machine up for: $up, since: $since"
}

show_uptime   # calling the function
```

**Important - use `local` variables inside functions:**
```bash
show_uptime() {
    local up=$(uptime -p)
    local since=$(uptime -s)
}
```
Without `local`, variables inside a function can accidentally overwrite variables outside it!

**Passing arguments to functions:**
```bash
greet() {
    echo "Hello $1"
}
greet "Herbert"
```

**Exit codes in functions:**
```bash
if [ "${1,,}" = "herbert" ]; then
    return 0    # success
else
    return 1    # failure
fi
```

---

## 13. AWK (Filtering Text)

Used to pick out specific parts (columns) of text.

```bash
awk '{print $1}' file.txt          # prints the 1st word of each line
awk '{print $2}' file.txt          # prints the 2nd word

# Using a custom separator (like commas in CSV files):
awk -F ',' '{print $1}' file.csv
```

---

## 14. SED (Find & Replace)

Used to replace text inside files.

```bash
sed 's/fly/grasshopper/g' file.txt
```
- `s` = substitute
- `fly` = word to find
- `grasshopper` = word to replace it with
- `g` = do it globally (every occurrence, not just the first)

**Keep a backup of the original file:**
```bash
sed -i.original 's/fly/grasshopper/g' file.txt
```

---

## Quick Cheat Sheet Summary

| Concept | Symbol/Command |
|---|---|
| Comment/interpreter line | `#!/bin/bash` |
| Variable | `name=value` |
| Use variable | `$name` |
| User input | `read var` |
| Positional argument | `$1`, `$2` |
| Pipe | `\|` |
| Overwrite to file | `>` |
| Append to file | `>>` |
| Feed file as input | `<` |
| If statement | `if [ ]; then ... fi` |
| Case statement | `case ... in ... esac` |
| Array | `arr=(a b c)` |
| Loop | `for x in list; do ... done` |
| Function | `name() { ... }` |
| Filter columns | `awk` |
| Find & replace | `sed` |

