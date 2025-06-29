ssh-keygen -t ed25519 -C "your_email@example.com"


Generating public/private ed25519 key pair.
Enter file in which to save the key (/Users/you/.ssh/id_ed25519):


eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519


eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519


cat ~/.ssh/id_ed25519.pub


