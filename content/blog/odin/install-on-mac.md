---
title: "Odin Language - Installing on macOS Ventura"
date: 2024-01-18T23:50:07+05:30
---

I just followed the official documentation :) But setting `LLVM_CONFIG` was not documented

```bash

xcode-select --install

brew install llvm@14

echo 'export PATH="/usr/local/opt/llvm/bin:$PATH"' >> ~/.zshrc_profile

export LDFLAGS="-L/opt/homebrew/opt/llvm@14/lib"
export CPPFLAGS="-I/opt/homebrew/opt/llvm@14/include"

export LLVM_CONFIG="/opt/homebrew/opt/llvm@14/bin/llvm-config
```