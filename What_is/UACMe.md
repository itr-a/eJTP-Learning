# What is UACMe?

**UACMe** is a tool that tricks Windows into letting a program run with full Admin rights **without showing that scary “Do you want to allow this?” popup**.

### How does it work
Instead of fighting Windows, it uses Windows against itself — by:
- Finding trusted apps that already run with Admin power
- Sneaking their own code into those apps
- And letting Windows elevate it without asking, because it trusts the app