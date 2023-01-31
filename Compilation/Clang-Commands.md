---
title: Clang Commands
description: 
published: true
date: 2023-01-31T15:22:55.092Z
tags: 
editor: markdown
dateCreated: 2023-01-31T15:22:55.092Z
---

- Clang
  - Compiler `clang -cc1`, may failed to locate system header files or other config parameters
    - `clang -cc1 –analyze –analyzer-checker=<package>`
  - Compiler Driver `clang`
    - `clang --analyze -Xanalyzer -analyzer-checker=<package>`
  - scan-build