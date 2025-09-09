---
title: "Combining Macros With Conditional Statements in Vim"
date: "2025-09-09"
tags: ["plain text files", "vim text editor"]
draft: false
---

In this post I'll explain how I use [Vim][] to insert data fields in a `json`
file with data gathered from a `csv` file.

Suppose we have a `json` file with hundreds of entries like this:

```json
"01234567891": {
    "fname": "john smith",
    "level": 2,
    "email": "john.smith@server.net",
    "award": "none"
}
```

Now, suppose that a third party gives us a `csv` file with the awards for some
of the people in our `csv`, possibly not all of them. The thing though, is that
this `csv` file contains not enough data for us to identify each person in it in
a unique fashion and due to its size (may be a few hundred lines) we certainly
do not want to check them by hand, one after another.

In order to keep things a little less abstract, let's imagine that the columns
of our `csv` file have the following meaning:

```csv
name       ; level ; award
jane doe   ; 3     ; o
john smith ; 2     ; b
```

Not a while ago I would write a `Python` script that would read both the `csv`
and the `json` files and would then produce a third file with the updated data.
These days I find it a lot more joyful to use only [Vim][] to this end and see
the data being manipulated right in front of these old eyes.

1. Copy the contents of the `csv` file to the end of the `json` file.
1. We record a macro at register `q` to:
    - find the first line that starts with a simple word (remember, this is json
      file) and then deletes it (well, as a matter of fact, we delete it only
      after updating the values)
    - after capturing all data in that row, find a registry matching both the
      person's name and its level
    - update the award for that person

    ```text
    /^[a-z]\+mmt;€ý5be"ayf;€ý5w"byiwf;€ý5w"cyiw/"fname": "a",$\n^\s\+"level": b,4jEwci"c`mdd
    ```

1. Now we create a loop that will trigger the macro at `q` for each occurrence
   of the regexp pattern contained in the beginning of the macro.

    ```text
    :while search('^[a-z]\+') > 0 | exec 'norm @q' | endwhile
    ```

And just like that, the work is done. There are a few quirks with this method. I
would recommend using it with data files you know well. Additionally, one should
perform simple data checks before putting such macros into work as it's possible
to fall into infinite loops (in that case, `ctrl-c` is your friend). See you in
the next one. Cheers!

## TODO

Find the ascii equivalents of the strange characters above.

[Vim]: https://www.vim.org/
