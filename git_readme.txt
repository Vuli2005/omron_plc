git config user.name "VladimirV"
git config user.email "vladimir.vuleta@aspentech.com"

git config user.name "Vuli2005"
git config user.email "vuli2005@gmail.com"

Step 1: Create SSH keys for each GitHub account (on the Linux server)
ssh-keygen -t ed25519 -C "vuli2005@gmail.com" -f ~/.ssh/id_ed25519_personal
ssh-keygen -t ed25519 -C "vladimir.vuleta@aspentech.com" -f ~/.ssh/id_ed25519_work

Step 2: Add public keys to GitHub
id_ed25519_personal.pub
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIE3qXG9XySdU8YVqBEAaupAwBTQOxMwj++/LhY4zMEm+ vuli2005@gmail.com
id_ed25519_work.pub
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAINXMg/dqDoBRJis9zgvj9iCEezj8tlFyF1u5s5ieIWjy vladimir.vuleta@aspentech.com

Step 3: Configure ~/.ssh/config on the Linux serve

Host github-personal
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_personal

Host github-work
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_work

Manual Clone like this using identities
git@github-personal:vuli2005/repo_name.git
git@github-work:vladimirvuleta/repo_name.git

Confirm SSH works inside the container and host
ssh -T git@github-work
ssh -T git@github-personal

