# git-crypt-gpg
This repository contains simple documentation for configuring git-crypt in gpg mode. The documentation is divided into two sections:
- [Generating a GPG keypair](#gpg-key-generation)
- [Managing a repository with git-crypt in GPG mode](#gpg-key-generation)

# GPG Key Generation
To generate the necessary public/private RSA keypair you will need to have installed [Gnu Privacy Guard (GnuPG)](https://www.gnupg.org/). For Mac users this can be installed via `brew install gnupg`.

Once installed, a new RSA key can be configured using a single command:
```
gpg --full-generate-key
```
This command will then lead to a series of terminal prompts to specify the exact specification of the key. When prompted, use the following configuration options:
- Type of key: 1 (RSA)
- Keysize: 4096
- Validity: 0
- Is this correct?: y
- Real name: Any unique name
- Email address: Any unique email
- Comment: Optional
- Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit?: O
- Passphrase: Memorable passphrase of length > 8 characters (you will be prompted for this later when you come to unlock the repository using `git-crypt unlock`)

If successful, you should be greeted with the following message, containing RSA key metadata:
```
pub   rsa4096 20XX-XX-XX [SC]
      UNIQUE_ID...............................
uid                      UNIQUE_NAME <UNIQUE_EMAIL>
sub   rsa4096 20XX-XX-XX [E]
```
This keypair has been generated and stored on your local machine. The private key will remain on your local device to perform decryption. The public key will be shared with repository contributors, to allow you to decrypt any files encrypted using `git-crypt`.

To share your public key you will need to export it as a file which can be sent to a repository contributor:
```
gpg --armor --export USER_ID > USER_ID.asc
```
Here, the public key associated with `USER_ID` is being exported to the file `USER_ID.asc`. 'USER_ID' may be replaced with the appropriate name / identifier.

Finally, simply send the `USER_ID.asc` file to the repository contributor. As it is a *public* key you can use whatever channel you desire (e.g., email, instance message)

# git-crypt GPG configuration
## git-crypt GPG setup
To setup git-crypt in GPG mode, you **MUST** have already [generated an RSA key using GPG](#gpg-key-generation). Once you have a working RSA key, initialise git-crypt in your repository using the standard command:
```
git-crypt init
```
Next, create a `.gitattributes` file within your repository which includes a pattern of the following formats:
```
secretfile filter=git-crypt diff=git-crypt
*.key filter=git-crypt diff=git-crypt
secretdir/** filter=git-crypt diff=git-crypt
```
Finally, you need to add **YOURSELF** as the first user:
```
git-crypt add-gpg-user USER_ID
```
Where `USER_ID` is the email address / username that you specified when you generated your RSA key using GPG. 
## git-crypt GPG: Adding new users
To start, the user in question *must* first have sent you their keyfile. This will be of the format `keyfile.asc` as described in the [key generation section](#gpg-key-generation). Once this file has been downloaded to your local machine, you can proceed.

First, import the key from the downloaded keyfile:
```
gpg --import ~/path/to/the/keyfile.asc
```
Next, you need to modify the imported key so that it can be trusted by your local machine. This can be done using a single command:
```
gpg --edit-key USER_ID
```
The above command will give you many options for editing the key. You should enter the following sub-commands / options to trust the key:
- trust
- 5
- y
- quit
Next, you can add the newly-trusted key as a user via git-crypt: 
```
git-crypt add-gpg-user USER_ID
```
Where `USER_ID` is the username / email address specified when creating the key.

Finally, the whole repository updates need to be committed to Github.