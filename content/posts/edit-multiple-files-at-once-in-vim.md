---
title: "Edit Multiple Files at Once With Vim"
date: "2025-07-25"
tags: ["plain text files", "multiple files editing", "Vim Text Editor"]
draft: false
---

Just the other day I engaged in an interesting project with my family. We grabbed the data about this TV Show from IMDb in order to create:

1. A [PostgreSQL][] database
2. A presentation in [LaTeX][] using the [Beamer][] document class
3. A statically generated web site with [Hugo][]

Once the database was ready everything else was relatively easy thanks to [Vim][]. For instance, to create the presentation we dumped a `csv` file from our database like this:

```postgresql
(dbname)=> \copy (select * from episode) to main.tex csv delimiter ';';
```

Since the fields had the following meaning:

```csv
episode id;title;synopsis;release date;rate;votes
```

we felt that an indeed very long substitute command will do the trick:

```vim
:%s/^\([^;]\+\);\([^;]\+\);\([^;]\+\);\([^;]\+\);\([^;]\+\);\([^;]\+\)$/\\begin{frame}{\2}\r\\begin{table}\r\\centering\r\\begin{tabular}{llll}\r\\textbf{Episode} \& \\texttt{\1} \& \\textbf{Votes} \& \\texttt{\6} \\\\\r\\textbf{Release} \& \\texttt{\4} \& \\textbf{Rate} \& \\texttt{\5}\r\\end{tabular}\r\\end{table}\r\\begin{synopsys}\r\3\r\\end{synopsys}\r\\end{frame}\r/
```

And just like that the frames for all 217 episodes were created. We then added a minimal preamble to the document containing that custom environment we called `synopsis`. Next we formatted the [LaTeX][] source code with the normal mode command `gg=G`, saved and compiled it in order to produce a `PDF` file.

Having accomplished the second of our tasks it was now time to create that statically generated website. With [Hugo][] it was as easy as running the following command:

```shell
$ hugo new site mywebsite
```
Then we installed a [theme for Hugo][]. The next thing to do was to create the content for our website. Remember that `csv` file that we dumped from our database in the last step? Well, we saved a duplicate of it in our downloads folder. We then used a [Python][] script to create one blog post for each row in the `csv` file. This step was necessary because we wanted corresponding blog posts and episodes to have matching published and released dates. The internals of such a script doesn't matter right now, suffices it to say that it saves each post to a file on disk that is named after the primary key of the corresponding episode in our database, as this will play an important role in the concluding part of our approach.

It was only when we ran the command `hugo server -D` that we had a glimpse of how things were going so far: the posts were listed in reverse chronological order. Makes sense, right? To sort our posts from the most recent ones to the older ones, except that in our case we wanted the posts to be listed in chronological order so that it matches our thinking process. After reading the manuals we found that [Hugo][] sorts posts by something it calls `weight`. It must be an integer, with lower values taking precedence over higher ones. If it doesn't find a weight to a post it then sorts it by the date in which it was published, finally falling back to the filename on disk (order not guaranteed). The ordinal of an episode would be a perfect fit for this, don't you think? Here's the thing though: how do we add a weight entry to the metadata on every post with the least possible effort? Do you remember dealing with `argc` and `argv` from your `C` programming classes? Well, the files you open at runtime in [Vim][] populate a list called argv and argc is simply the cardinality of such list. So, we opened all posts using a wildcard:

```shell
$ vim content/posts/*.md
```

Then we ran the ex command `echo argc()` in order to check whether we had correctly loaded all files in memory or not and, as expected, it greeted us with the number 217. Now, to insert a weight entry right after the title one in the metadata we did the following:

```vim
:argdo g/^title:/s/$/\rweight: 0/
```

As you might have guessed that argdo command means we are iterating through the argv list, one buffer after another. The only missing part is the weight itself, which we updated with the following command:

```vim
:argdo g/^weight:/s/0$/\=bufnr()/ | update
```

That `\=` in the substitution pattern means that what follows next is an expression. Roughly speaking, we can do math with it in our substitution patterns. In this case, we simply inserted the ordinal number for the episode into the metadata. Go ahead and ask me: But this weight of yours, doesn't it correspond to the order in which the buffer appears in Vim's argv list? I'm glad you asked. YES! You see, that's why I name my episodes like `S01E01`, meaning the first episode of the first season, `S01E02` meaning the second episode of the first season, and so on.

I hope you see a pattern going on here: the unix philosophy, plain text files, REGEXP's and a good text editor.

[Beamer]: https://ctan.org/pkg/beamer
[Hugo]: https://gohubo.io
[LaTeX]: https://latex-project.org
[PostgreSQL]: https://postgresql.org
[Python]: https://python.org
[Vim]: https://www.vim.org
[theme for Hugo]: https://themes.gohugo.io
