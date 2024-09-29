# tmux-sessionizer

tmux-sessionizer is a script that was taken from [Primeagen](https://github.com/ThePrimeagen/.dotfiles/blob/master/bin/.local/scripts/tmux-sessionizer) and adjusted to be more configurable.

tmux-sessionizer searches for subdirectories within a path based off a user provided search term. If the search term is found and matches exactly 1 directory,  it opens a tmux session in that directory. Otherwise, it will open the search results in a fuzzy finder, and allow the user to select which directory they'd like to open in tmux. If a session already exists for the directory, it attaches to the existing session instead.

## Usage
```
Usage: ./tmux-sessionizer [-c|--config] [-d|--directory] [-s|--sessions] [SEARCH_TERM]
Search [-d|--directory] for SEARCH_TERM. If found, open a tmux session those directories.
If search term is not specified, open the entire directories in a fuzzy finder. Once a directory
is selected in the fuzzy finder, a tmux session is started in the directory. If a session already
exists for SEARCH_TERM or the selected directory, open that session instead of creating a new one.

Options can be specified through a configuration file, or command line flags. Additionally, the
path to the config file through an environment variable. Arugments are parsed in following
order of least to most weighted:
    Configuration file < Environment variable < Command line flags.

Configuration file:
    directory=${HOME}/other,2,3
    directory=${HOME}/projects,3,3

Environment variables:
    TMS_CONFIG="${HOME}/.config/tmux-sessionizer/config"

Command line flags:
    -c|--config         Specify a configuration file to read settings from.
                        Format is in key=value pairs. [Default: ${HOME}/.config/tmux-sessionizer/config]
    -d|--directory      Specify the directories to search within, and the depth to search.
                        Can be specified multiple times. Must be a CSV in the format of:
                            PATH,MINDEPTH (default 1),MAXDEPTH (default 1)
                        For example, if you run the command with the flag:
                            -d "${HOME}/repositories,3,3" -d "${HOME}/workspaces,2,3"
                        It will search ${HOME}/repositories with a mindepth and maxdepth of 3.
                        ${HOME}/workspaces will be searched with a mindepth of 2 and maxdepth of
                        3.
    -h|--help           Print this menu and exit.
    -s|--sessions       Search for existing tmux sessions in a fuzzy finder.
```

### -d/--directory
You specify the paths to parse using this flag. If you are using the configuration file, it will be in the format of directory=PATH,MINDEPTH,MAXDEPTH within the file:

At least one directory must be specified, either through the CLI or through the configuration file if calling without --sessions.

#### CLI
In the CLI, you can specify multiple directories using multiple flags:
```
tmux-sessionizer -d ${HOME}/repositories,3,3 -d ${HOME}/workspaces,1,1
```
#### Config file
Environment variables can be used in the config file.
Example:
```
directory=${HOME}/workspaces,1,1
directory=${HOME}/repositories,3,3
```

### -c/--config
Specifies path to the config file.

Defaults to ${HOME}/.config/tmux-sessionizer/config

#### CLI
```
tmux-sessionizer -c ${HOME}/.config/tmux-sessionizer/config
```
#### Environment Variable
```
TMS_CONFIG=${HOME}/.config/tmux-sessionizer/config tmux-sessionizer
```

### -s/--sessions
Searches through active tmux sessions in a fuzzy finder.

#### CLI
```
tmus-sessionizer -s
```
