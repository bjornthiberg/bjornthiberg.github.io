---
layout: post
title:  "skald1: an encrypted journal in Rust"
date:   2024-09-30 21:00:00 +0200
category: skald
---

I have completed the [rustlings](https://github.com/rust-lang/rustlings) exercises, and I am moving on to a more specific project. I'm going to create an encrypted local file-based journal. In the longer term, to make it a bit more Rust-y and to learn some systems programming, each entry will be timestamped, encrypted and immutable once published.

Keeping with the theme, I will call this small project **Skald**, after the norse poets.

## tools I will use
- Rust for the backend
- first CLI, and then Tauri for the frontend. I want to stay in the Rust ecosystem
- deploy releases on github

I won't make this as ambitious or clearly structured as my last project, but I want to at least be mindful of the order in which I do things and get a sense of progress. I will use repo issues for this purpose. I won't spend as much time on project planning or architecture, as I'm primarily doing this to learn how to *write* Rust.

## project plan
**Chunk 1:** project structure, basic functionality
1. diary.rs module
2. DiaryEntry struct
3. file saving and loading
4. CLI interface for adding/viewing entries.

**Chunk 2:** improve basics
1. add timestamps to entries.
2. improve file I/O error handling
3. make the basic functionality robust
4. add JSON serialization/deserialization

**Chunk 3:** encryption
- TBD

**Chunk 4:** immutability, hash chaining
- TBD
- I want tamper detection

**Chunk 5:** frontend GUI
- TBD
- will use Tauri, and perhaps Svelte
