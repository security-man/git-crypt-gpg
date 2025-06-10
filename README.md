# git-crypt-gpg
This repository contains simple documentation for configuring git-crypt in gpg mode. The documentation is divided into two sections:
- [Generating a GPG keypair](#gpg-key-generation)
- [Managing a repository with git-crypt in GPG mode](#gpg-key-generation)

# GPG Key Generation
```
gpg --full-generate-key
```

```
gpg --armor --export USER_ID > USER_ID.asc
```

# git-crypt GPG configuration
## git-crypt GPG setup

## git-crypt GPG: Adding new users
```
gpg --import USER_ID.asc
```
```
gpg --edit-key USER_ID
```
```
git-crypt add-gpg-user USER_ID
```