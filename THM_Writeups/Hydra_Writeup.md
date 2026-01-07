# TryHackMe: Hydra Room Write-Up

**Room Link:** https://tryhackme.com/room/hydra  
**Difficulty:** Easy  
**Date Completed:** January 07, 2026  
**Machine IP:** 10.66.130.219

## Overview
This room introduces **Hydra**, a fast network logon cracker used for brute-forcing online services such as web forms and SSH. The objective is to obtain credentials for a web login and then for SSH on the target machine, retrieving two flags in the process.

## Task 1: Hydra Introduction
Read the provided information about Hydra, its capabilities, supported protocols, and the importance of strong passwords. No practical work required here.

## Task 2: Using Hydra

### Part 1: Brute-Forcing the Web Login (Flag 1)

The goal was to brute-force the username **molly** on a POST web form using the `rockyou.txt` wordlist.

My initial attempts used commands like:
```bash
hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.66.130.219 http-post-form "/:username=^USER^&password=^PASS^:Your username or password is incorrect."
```

Issues I encountered / Where I got stuck:

Hydra kept iterating through millions of passwords instead of stopping after roughly 30 attempts (as hinted in the room).
Progress updates only appeared every few minutes.
I tried multiple incorrect paths (/, /login.php, etc.).
Using the full failure message "Your username or password is incorrect." prevented Hydra from reliably detecting failed vs. successful logins.
Lowercase -v only showed sparse "ATTEMPT" lines with no real-time feedback.
Walkthrough videos appeared to finish instantly, making my slow progress very frustrating.

Critical changes that fixed it:

Correct POST path: /login
Failure condition: :F=incorrect (substring match – the word "incorrect" only appears on failed login attempts)
Uppercase -V: Displays every attempt in real time
-t 20: Increases parallel threads for faster execution

With these adjustments, Hydra quickly processed the first ~30 passwords and stopped immediately upon finding the correct one: sunshine.
Credentials obtained: molly : sunshine
Visited http://10.66.130.219/login, logged in, and retrieved Flag 1:


Next, brute-force SSH access using the same username molly and rockyou.txt.
I used an optimized command: hydra -l molly -P /usr/share/wordlists/rockyou.txt 10.66.130.219 ssh -t 20 -V


This completed quickly because the correct password (butterfly) appears very early in the wordlist.
Credentials obtained: molly : butterfly
Connected via SSH and located Flag 2 in the home directory.

Hydra's syntax for web forms is highly sensitive — the exact POST path and failure condition (:F=) are essential for accurate success detection.
Always use -V during debugging to monitor attempts in real time.
Increase threads with -t to accelerate the process (but avoid excessive values to prevent throttling).
Walkthrough videos often edit out waiting periods — even "quick" brute-force runs can take time if the command isn't perfectly tuned.
The room effectively demonstrates why common/default passwords are dangerous — both "sunshine" and "butterfly" rank early in rockyou.txt.


I Got pretty caught up in trying to figure this out and didn't take any screenshots this time around but I will try to be better about that in the future.
