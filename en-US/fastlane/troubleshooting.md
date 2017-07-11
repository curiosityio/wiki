---
name: Troubleshooting
---

# `error: 'stdio.h' file not found` while running fastlane command

This happens when running a `fastlane commandHere` command. The way to fix this is to instead run `bundle exec fastlane commandHere`. [This GitHub issue](https://github.com/fastlane/fastlane/issues/8431#issuecomment-287845604) is where I found the issue and the solution. 
