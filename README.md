There is an issue in Ubuntu 16.04, that results in a lot of hassle running ```keychain``` with ```gpg-agent``` at least on WSL.

The reason is, that Ubuntu 16.04 installs ```gpg-agent``` version 2.1.x and ```gpg``` client version 1.4.x. As the net points out, both versions are not really compatible. Distros provide ```gpg2``` packages for version 2.x. ```Keychain``` isn't aware of ```gpg2``` and Ubuntu 16.04 mix both versions. Buhh...

### Initial error

```
 * Adding N gpg key(s): ID ...
 * Error: Problem adding (is pinentry installed?); giving up
```

To overcome the situation we need ```gpg2``` support in ```keychain```. There is an issue open for that. The workarounds are:

* Downgrade Ubuntu 16.04 to ```gpg-agent``` version 1.4.x
* Switch using ```gpg2```

To make the second work, here is a quick hack:

### Install

```bash
sudo apt install gpg2
sudo apt install make
mkdir -m 0700 ~/.keychain
git clone https://github.com/iaean/keychain ~/.keychain/keychain
cd ~/.keychain/keychain
make
```

### Run

```bash
alias gpg=gpg2 # optional
gpgconf --launch gpg-agent # recommended
if [ -x ~/.keychain/keychain/keychain ]; then
  ~/.keychain/keychain/keychain --agents gpg,ssh ID ...
  source ~/.keychain/${NODENAME}-sh
  source ~/.keychain/${NODENAME}-sh-gpg
fi
```

Set an optional ```alias``` to switch to ```gpg2``` without typing ```gpg2```.   
Starting ```gpg-agent``` before ```keychain``` is recommended because ```keychain``` dosn't set ```GPG_AGENT_INFO``` for version 1 compatibility on agent startup. But it does on inheritance...



Introduction to Keychain
========================

**Official documentation for Keychain can be found on [the official Keychain
wiki page](http://www.funtoo.org/Keychain).**

`Keychain` helps you to manage ssh and GPG keys in a convenient and secure
manner. It acts as a frontend to `ssh-agent` and `ssh-add`, but allows you
to easily have one long running `ssh-agent` process per system, rather than
the norm of one `ssh-agent` per login session. 

This dramatically reduces the number of times you need to enter your
passphrase. With `keychain`, you only need to enter a passphrase once every
time your local machine is rebooted. `Keychain` also makes it easy for remote
cron jobs to securely "hook in" to a long running `ssh-agent` process,
allowing your scripts to take advantage of key-based logins.

