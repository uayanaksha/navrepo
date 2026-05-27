# 02 — Navigation

> The repo is a graph. Learning to traverse it efficiently is the most
> portable skill in software.

This section is about moving through code at speed. Different questions
demand different tools — the meta-skill is picking the right one.

## Contents

| File | What you'll learn |
|---|---|
| [search-tools.md](search-tools.md) | rg, ast-grep, comby, GitHub & Sourcegraph search |
| [lsp-navigation.md](lsp-navigation.md) | Go-to-def, find-references, workspace symbols |
| [entry-points.md](entry-points.md) | Finding `main`, tests, build, request handlers |
| [git-archaeology.md](git-archaeology.md) | blame, log -S, log -L, bisect, pickaxe |
| [following-data-flow.md](following-data-flow.md) | Tracing requests end-to-end |
| [dependency-traversal.md](dependency-traversal.md) | Reading vendored / third-party code |

## The Mental Model

```
What you want                       Use
---------------------------         ----------------
A symbol you can name               LSP
A string you've seen                ripgrep
A pattern, any names                ast-grep / comby
A line that no longer exists        git log -S
A function's history                git log -L
Who wrote this, when, why           git blame → git show
Cross-repo: usage patterns          Sourcegraph / GitHub code search
```

Stop when one tool gives you the answer. Don't escalate to a heavier tool
out of habit.

## The One-Sentence Summary

> **Use the highest-level tool that answers your question; learn the
> next one up when you run out.**

## See Also

- [../11-tooling/](../11-tooling/) — installing and configuring these tools
- [../03-reading-code/](../03-reading-code/) — what to do *after* you've found the code
