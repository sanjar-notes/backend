# 1. User Authentication
Created Tuesday 12 April 2022

# Why
User data (usually protected and private) is loaded and served when the log in, or have an active session.

# How
The most basic way to log in is by providing username and a password.
* But it is risky to save the password directly as text, so a hash of the password is saved. While logging in, the password provided by the user is hashed to verify it matches.
* User authentication has(really?) many other vulnerabilities, so it's better to use an authentication library like passport.js

# What