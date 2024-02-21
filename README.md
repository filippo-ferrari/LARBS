# F's version of Luke's Auto-Rice Bootstrapping Scripts (LARBS)

## ⚠️  Current workarond for this [issue](https://github.com/filippo-ferrari/LARBS/issues/2) ⚠️
As of right now the script fails to install due to an error when trying to install the AUR helper.
The way fix this error is: 

- Step 1: Run the script: sh larbs.sh and let the script fail.
- Step 2: Added the newly created user you added from the script to the wheel group: usermod -aG wheel username
- Step 3: Install vi: pacman -S vi
- Step 4: Edit the sudoers file visudo /etc/sudoers
- Step 5: Uncomment this line: # %whell ALL=(ALL:ALL) ALL
- Step 6: Switch from the root user to the new user. su username
- Step 7: Change home directories: cd
- Step 8: Goto the directory yay is installed: cd .local/src/yay
- Step 9: Install yay: makepkg -si
- Step 10: Type: exit
- Step 11: Now as the root user again, run: sh larbs.sh

Workaround provided by: [this user's comment](https://github.com/LukeSmithxyz/LARBS/issues/538#issuecomment-1808489841)

## Installation:

clone the repository:

```
git clone https://github.com/filippo-ferrari/LARBS.git
```
then launch the script:
```
sh larbs.sh
```

# Luke's Auto-Rice Bootstrapping Scripts (LARBS)

## Installation:

On an Arch-based distribution as root, run the following:

```
curl -LO larbs.xyz/larbs.sh
sh larbs.sh
```

That's it.

## What is LARBS?

LARBS is a script that autoinstalls and autoconfigures a fully-functioning
and minimal terminal-and-vim-based Arch Linux environment.

LARBS can be run on a fresh install of Arch or Artix Linux, and provides you
with a fully configured diving-board for work or more customization.

## Customization

By default, LARBS uses the programs [here in progs.csv](static/progs.csv) and installs
[my dotfiles repo (voidrice) here](https://github.com/filippo-ferrari/voidrice),
but you can easily change this by either modifying the default variables at the
beginning of the script or giving the script one of these options:

- `-r`: custom dotfiles repository (URL)
- `-p`: custom programs list/dependencies (local file or URL)
- `-a`: a custom AUR helper (must be able to install with `-S` unless you
  change the relevant line in the script

### The `progs.csv` list

LARBS will parse the given programs list and install all given programs. Note
that the programs file must be a three column `.csv`.

The first column is a "tag" that determines how the program is installed, ""
(blank) for the main repository, `A` for via the AUR or `G` if the program is a
git repository that is meant to be `make && sudo make install`ed.

The second column is the name of the program in the repository, or the link to
the git repository, and the third column is a description (should be a verb
phrase) that describes the program. During installation, LARBS will print out
this information in a grammatical sentence. It also doubles as documentation
for people who read the CSV and want to install my dotfiles manually.

Depending on your own build, you may want to tactically order the programs in
your programs file. LARBS will install from the top to the bottom.

If you include commas in your program descriptions, be sure to include double
quotes around the whole description to ensure correct parsing.

### The script itself

The script is extensively divided into functions for easier readability and
trouble-shooting. Most everything should be self-explanatory.

The main work is done by the `installationloop` function, which iterates
through the programs file and determines based on the tag of each program,
which commands to run to install it. You can easily add new methods of
installations and tags as well.

Note that programs from the AUR can only be built by a non-root user. What
LARBS does to bypass this by default is to temporarily allow the newly created
user to use `sudo` without a password (so the user won't be prompted for a
password multiple times in installation). This is done ad-hocly, but
effectively with the `newperms` function. At the end of installation,
`newperms` removes those settings, giving the user the ability to run only
several basic sudo commands without a password (`shutdown`, `reboot`,
`pacman -Syu`).
