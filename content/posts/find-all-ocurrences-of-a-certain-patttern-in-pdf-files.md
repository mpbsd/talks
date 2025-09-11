---
title: "Find All Ocurrences of a Certain Patttern in PDF Files"
date: "2025-09-11"
tags: ["plain text files", "vim text editor", "grep", "vimgrep", "ghostscript"]
draft: false
---

Today a very good friend of mine and I were looking for a certain pattern of
text through 10 or so PDF documents. We were both hoping to find what we were
looking for as soon as possible so that we could move along with our working
day. Suddenly we faced ourselves with the question of whether or not we had
found all occurrences of what we were looking for. [Vim][] to the rescue, I say!

The first thing to do was to convert the PDFs into pure text so that we could
read them in [Vim][]. I have [Ghostscript][] installed in my machine as a
[LaTeX][] dependency. I used it to convert our PDFs into `txt` files with a
single line command like this:

```shell
for i in $(seq 1 10); do I=$(printf '%02d' "$i"); gs -sDEVICE=txtwrite -o"${I}.txt" "${I}.pdf"; done
```

Earler today I had used [Vim][] (of course) to name these files like `01.pdf`,
`02.pdf`, and so on, so that the above command would work. Next, I opened the
newly created files all at once with a wild card:

```shell
vim *.txt
```

For the final step, I searched through these files with the following command:

```shell
:vimgrep /<your pattern goes in here>/ ##
```

That `##` in there means the list of buffers loaded at [Vim][]'s runtime.
`Vimgrep` is in my case [Vim][]'s internal `grep`. Notice that it can be very
slow with large code bases. In the present case, it was instantaneous. Then,
[Vim][] creates a list it calls `Quickfix list` that contains every occurrence
of the searched pattern, that you can then use to very easily jump to the next
one (even if it means jumping to a different file) with a few key strokes (for
example, `]q` if you follow [unimpaired][]'s convention, by Tim Pope). That's
it guys, see you in the next one. Cheers!

[Vim]: https://www.vim.org/
[Ghostscript]: https://www.ghostscript.com/
[LaTeX]: https://latex-projects.org/
[unimpaired]: https://github.com/tpope/vim-unimpaired
