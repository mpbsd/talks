---
title: "Edit Multiple Lines at Once With Vim"
date: "2025-09-02"
tags: ["plain text files", "multiple lines editing", "vim text editor"]
draft: false
---

In this post I want to explain how to sort the lines of a `csv` file while
keeping related lines close to one another.

My friends organize and promote a mathematics competition called [OMEG][]. Every
year I help them to keep the list of its participants up to date. It's a `csv`
file whose fields have the following meaning: [inep code][], full name and exam
level (it's an ordinal number, either 1, 2, or 3). In general, we want the lines
to be sorted like this: inep codes must be in non decreasing order. Finally,
lines sharing both the same inep code and exam level must be sorted
alphabetically, according to the person's name. Additionally, I like to add a
blank line between pairs of lines beginning with non identical inep codes just
to make things easier on the eyes. This is indeed a very simple task, thanks to
[Vim][].

Two important things before we continue:

- Inep codes are composed of 8 consecutive digits;
- This year, exceptionally, we've an extra field. It's there to indicate the
   kind of username for that person, either a CPF or an e-mail. It would be even
   easier, as in fewer key strokes, without these fields and a slight
   modification of the commands below would work just fine.

Sort the lines:

```shell
:sort
```

Create an empty line between pairs of consecutive lines starting with non
identical inep codes:

```shell
:g/^\([0-9]\{8\}\).*$\n^\%(\1\)\@!/s/$/\r/
```

Sort every **paragraph** according to the exam level, as it will automatically
take the person's name into account for us:

```shell
:g/./:/\%(^$\n^\)\@<=\%(cpf\|e-mail\)/;/^\%(cpf\|e-mail\).*$\n^$/sort /[123]$/ r
```

And just like that, the job is done. As you see, [Vim][] is a very powerful tool. Of
course I could use a large language model for this task, but there would be no
fun in it. See you on the next one. Cheers!

[OMEG]: https://omeg.ime.ufg.br/
[Vim]: https://www.vim.org/
[inep code]: https://www.gov.br/inep/pt-br/acesso-a-informacao/dados-abertos/inep-data/catalogo-de-escolas/
