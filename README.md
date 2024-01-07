# Setup for the first time

- Install Hugo (version v0.114.0-9df2ec7988e5a217a14901cc76c0b7e76b2e9f02+extende (extended)) from github!
- Project root run:
```bash
git submodule init
git submodule update
cd themes/nighfall
checkout v0.2.0
cd ../../
hugo server -D
```
- Create new post
```bash
hugo new content blog/java/completable-futures/index.md
```
- Upload site
```bash
hugo
```