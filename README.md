# Multiple_Accounts_SSH_Github
## How to setup multiple accounts using SSH keys on Github, also for multiple PCs

The following instructions show how to configure the SSH key pair for multiple Github users within a single PC.
The same instructions can be repeated on all PCs of yours from which you want to work on Github **with the same (multiple) credentials**: 
here, as an example, we call them *HOME_PC* and *WORK_PC*.

1. Go to your home directory and create (or verify it already exists) a hidden ".ssh" directory in it.

2. Go to that directory:
```console
    cd ~/.ssh 
```

3. From there, create an SSH private/public key pair for each Github account by doing:
```console
    ssh-keygen -t rsa -C "github_account_1@whatever.com"
    Generating public/private rsa key pair.
    Enter file in which to save the key (C:\Users\HOME_PC_user/.ssh/id_rsa):
    Enter a name for the key: HOME_PC_github_account_1
    Enter passphrase (empty for no passphrase): HOME_PC_whatever_password_you_want
    Enter same passphrase again: HOME_PC_whatever_password_you_want
    Your identification has been saved in HOME_PC_github_account_1.
    Your public key has been saved in HOME_PC_github_account_1.pub.
    The key fingerprint is:
    SHA256:rfcEezIMrxBMMtv28P4xKfnaPgtdcj5jYxJ2DkQu+70 github_account_1@whatever.com
    The key's randomart image is:
    +---[RSA 3072]----+
    |          .      |
    |         o       |
    |    o . . o      |
    |     B   =       |
    |    . * S B +    |
    |     . = X #     |
    |      . B # %    |
    |       o B.% =   |
    |        ++*oE    |
    +----[SHA256]-----+
```

4. Repeat all those steps for each of your Github accounts.

5. Create a "config" file (with no extension) in the ".ssh" directory: this will enable your local git client to use multiple Github accounts. Then edit it this way:
```console
    #account: github github_account_1
    Host github_account_1.github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/HOME_PC_github_account_1_id_rsa

    #account: github github_account_2
    Host github_account_2.github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/HOME_PC_github_account_2_id_rsa

    #account: github github_account_3
    Host github_account_3.github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/HOME_PC_github_account_3_id_rsa

    etc... (similarly for all your Gituhub accounts)
```

6. In case you want to use these accounts also from a different PC (e.g. WORK_PC), simply repeat all the previous steps
on that PC as well but use a different file name for the SSH key pair when asked (e.g. *WORK_PC_github_account_1* in place of *HOME_PC_github_account_1*,  etc...).
At this point you should have this situation on your PC (e.g. *HOME_PC*, and similarly on all other PCs of yours, e.g. on *WORK_PC*):
```console
    C:\Users\HOME_PC_user\.ssh>dir
    Volume in drive C has no label.
    Volume Serial Number is ****-****

    Directory of C:\Users\WORK_PC_user\.ssh

    19/07/2021  11:45    <DIR>          .
    19/07/2021  11:45    <DIR>          ..
    16/07/2021  14:57               277 config
    16/07/2021  12:32             2,602 HOME_PC_github_account_1_id_rsa
    16/07/2021  12:32               572 HOME_PC_github_account_1_id_rsa.pub
    16/07/2021  14:56             2,602 HOME_PC_github_account_2_id_rsa
    16/07/2021  14:56               572 HOME_PC_github_account_2_id_rsa.pub
    16/07/2021  14:59             2,602 HOME_PC_github_account_3_id_rsa
    16/07/2021  14:59               572 HOME_PC_github_account_3_id_rsa.pub
    ...etc                              (similarly for all the SSH key pairs you created)                    
    16/07/2021  12:58             1,410 known_hosts
```

7. The *known_hosts* file might be already there, or it will be created the first time you will try to do something with any remote repository (e.g. cloning, see later).

8. Now (on each PC) add one by one all the generated keys:
```console
    ssh-add ~/.ssh/HOME_PC_github_account_1_id_rsa
    ssh-add ~/.ssh/HOME_PC_github_account_2_id_rsa
    ssh-add ~/.ssh/HOME_PC_github_account_3_id_rsa
    ...etc
```

9. You can list all your added keys:
```console
    ssh-add -l
```

10. You can delete all added keys (then you have to add them as at point #8) in case you made a mistake:
```console
	ssh-add -D

    ...or just delete one key:
    ssh-add -d HOME_PC_github_account_1_id_rsa
```

## How to add the public keys on Github
So far you have generated all SSH key pairs for all Github accounts of yours, on all PC of yours: now you should login on Github and load the **public** key
on the corresponding account, for each PC.

e.g. you have 3 Github users and 2 PCs: therefore you have 3x2 = 6 SSH key pairs, 3 SSh key pairs per PC.
You have to login on each of your 3 accounts on https://github.com/ and then add the 2 public keys you generated (one on each of the 2 PCs you have) for that Github account.

In order to do so, follow these intructions: https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account

## How to clone your local git repositories
There is a **subtle thing**: when getting the SSH clone string from Github (by clicking on th "Code" button and selecting the "SSH" tab from the main page of the repository you
want to clone) it will propose you an URL like this: *git@github.com:github_account_1/your_repository.git*. You have to **manually add** the username *github_account_1* prepended to the *github.com* string, otherwise the local git client on your PC will not be able to understand which SSH key to provide for the remote authentication: e.g.
```console
    git clone git@github_account_1.github.com:github_account_1/your_repository.git
```

## How to configure the remote URL on your local repository
Similarly to what you had to do in order to clone the repository, when you want to configure a remote repository URL on your PC you have to prepend the username to the
*github.com* string:
```console
    git remote add origin git@github_account_1.github.com:github_account_1/your_repository.git
```

## How to commit & push
Before being able to commit and push, you first have to configure the *user.name* and *user.email* relative to your repository: this way your local git client will know "who you are" when working with a specific repository:
```console
    git config user.name "github_account_1"
    git config user.email "github_account_1@whatever.com"

    ...choose the correct user and email according to the repository you want to work on from that PC.
```

## Windows 10: “unable to start ssh-agent service, error :1058”
The *OpenSSH Authentication Agent* might be not running by default, check this:
https://stackoverflow.com/questions/52113738/starting-ssh-agent-on-windows-10-fails-unable-to-start-ssh-agent-service-erro