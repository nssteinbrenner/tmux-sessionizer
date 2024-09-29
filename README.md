
# tmux-sessionizer

**tmux-sessionizer** is a configurable script that searches for subdirectories based on a user-provided search term and opens a tmux session in the matching directory. If the term matches exactly one directory, it opens a session immediately. Otherwise, it presents search results in a fuzzy finder for the user to select the desired directory. If a session already exists for the directory, it attaches to the existing session.

Adapted from [Primeagen's script](https://github.com/ThePrimeagen/.dotfiles/blob/master/bin/.local/scripts/tmux-sessionizer), **tmux-sessionizer** offers more configurability.

## Usage

```bash
./tmux-sessionizer [-c|--config] [-d|--directory] [-s|--sessions] [-v|--verbose] [-n|--new] [SEARCH_TERM]
```

- Searches `[-d|--directory]` for `SEARCH_TERM`. If found, opens a tmux session for those directories.
- If no search term is provided, it opens all directories in a fuzzy finder, allowing the user to select a directory to start a session.
- If a session for `SEARCH_TERM` or the selected directory already exists, it opens the existing session instead of creating a new one.
- With `-n|--new`, it creates the target directory and opens a tmux session in it.

### Argument Precedence

Arguments are parsed in the following order of priority:
1. **Configuration file**
2. **Environment variable**
3. **Command line flags**

## Options

- `-c|--config`: Specify a config file to read settings from. Format: key=value pairs. Defaults to `${HOME}/.config/tmux-sessionizer/config`.
- `-d|--directory`: Specify directories to search in, along with search depth. Multiple directories can be specified as a comma-separated value (CSV):
  `PATH,MINDEPTH,MAXDEPTH`.
  PATH is required. MINDEPTH and MAXDEPTH both default to 1.
  For example:
  ```bash
  -d "${HOME}/repositories,3,3" -d "${HOME}/workspaces,2,3"
  ```
  This will search:

  - `${HOME}/repositories` with mindepth and maxdepth of 3
  - `${HOME}/workspaces` with mindepth of 2 and maxdepth of 3

- `-n|--new`: Create a new directory and open a tmux session inside it.
- `-s|--sessions`: Search for active tmux sessions using a fuzzy finder. Incompatible with `-d|--directory` and `-n|--new`.
- `-v|--verbose`: Enable debug logging.
- `-h|--help`: Display this help message and exit.

## Configuration File Format

Options can be specified through a configuration file located at `${HOME}/.config/tmux-sessionizer/config` or a custom path provided with the `-c` flag or an environment variable.

Example configuration file:
```bash
directory=${HOME}/projects,2,3
directory=${HOME}/other,1,2
```

## Environment Variables

You can specify the config file path through the `TMS_CONFIG` environment variable:
```bash
export TMS_CONFIG="${HOME}/.config/tmux-sessionizer/config"
tmux-sessionizer
```

## Examples

- Search for a folder under a specific directory:
  ```bash
  tmux-sessionizer -d /home/my-user/repositories,1,1 my-repo
  ```

- Search across multiple directories:
  ```bash
  tmux-sessionizer -d /home/my-user/workspaces,1,1 -d /home/my-user/repositories,1,1
  ```

- Create a new directory and open a session in it:
  ```bash
  tmux-sessionizer -n /home/my-user/repositories/my-repo
  ```

- Search for existing tmux sessions:
  ```bash
  tmux-sessionizer -s
  ```

- Run with a custom config file:
  ```bash
  tmux-sessionizer -c /home/my-user/tmux-sessionizer.conf
  ```

## Flags in Detail

### `-d|--directory`

Specifies the directories to search in. At least one directory must be provided either through the CLI or a configuration file if not using the `--sessions` flag.

**Format:**
```
--directory PATH,MINDEPTH,MAXDEPTH
```
Both `MINDEPTH` and `MAXDEPTH` default to 1.

#### Multiple Directories (CLI)

Use multiple `-d` flags to specify multiple directories:
```bash
tmux-sessionizer -d ${HOME}/repositories,3,3 -d ${HOME}/workspaces,1,1
```

#### Configuration File

In the config file, use the following format:
```
directory=PATH,MINDEPTH,MAXDEPTH
```
Example:
```bash
directory=${HOME}/workspaces,1,1
directory=${HOME}/repositories,3,3
```

### `-c|--config`

Specifies the path to a configuration file.

**Default**: `${HOME}/.config/tmux-sessionizer/config`

#### CLI Example
```bash
tmux-sessionizer -c ${HOME}/.config/tmux-sessionizer/config
```

#### Environment Variable Example
```bash
TMS_CONFIG=${HOME}/.config/tmux-sessionizer/config tmux-sessionizer
```

### `-s|--sessions`

Searches active tmux sessions via fuzzy finder.
Incompatible with `-d|--directory` and `-n|--new`.

#### CLI Example
```bash
tmux-sessionizer -s
```

### `-n|--new`

Creates a new target directory and opens a tmux session inside it.

Incompatible with `-d|--directory` and `-s|--sessions`. A search term must not be specified.

#### CLI Example
```bash
tmux-sessionizer --new /home/my-user/repositories/my-repo
```
