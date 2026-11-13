\# Git Guide for the PACE Team



\## Every time you start



1\. Open Git Bash and type: cd PACE

&#x20;  (If the line before the $ shows only \~, you are not inside the project yet.)

2\. git checkout main

3\. git pull



\## Doing your work



1\. Make your own branch: git checkout -b feature/your-work

2\. Work only in your own folder (ml, k8s, traffic, monitoring, app).

3\. Save your work:

&#x20;  git add .

&#x20;  git commit -m "short message"

4\. Send it to GitHub: git push -u origin feature/your-work

5\. Open a pull request on GitHub. Bindhu will check and merge it.



\## Common problems



\- "not a git repository": you are not inside the PACE folder. Type cd PACE.

\- "command not found" with strange characters: the paste added hidden characters. Type the command by hand.

\- "Nothing specified, nothing added": you forgot the dot. Type git add . (with a space and a dot).

\- "nothing to commit": you forgot git add . before git commit.

\- Push is rejected: type git pull first, then push again.

\- Sign-in problem: use "Sign in with your browser" when Git asks. Do not use your password.



\## Safety rules



\- Never work directly on main.

\- Never upload passwords, keys, or tokens.

\- Never share a screenshot that shows a token or password.

\- Ask Bindhu if you get stuck.

