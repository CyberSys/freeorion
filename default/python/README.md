# python directory

Python scripts, while modifiable, may require specific functions which return
an appropriate response to FreeOrion.
For example, turn_events/turn_events.py is a required file and should define
an `execute_turn_events()` function returning a boolean (successful completion).

## Contents

* `AI/` Python code which controls the computer players.  This is a
sub-module of the resource directory and can be changed with the --ai-path flag.
* `auth/` Python code which manages authentication information stored either in a file
or in a database.
* `chat/` Python code which manages chat history stored either in a file or in
a database.
* `common/` Common files for code utilized by both the AI and the server.
* `handlers/` debug and development features, see [handlers/README.md](handlers/README.md)
* `turn_events/` Python scripts that run at the beginning of every turn, and
can trigger events that would be impossible to do purely in FOCS.
* `universe_generation/` Python scripts that get run at the beginning of the
game and create the galaxy. You can customise the galaxy generation by
editing options.py and universe_tables.py, both of which have more information
in comments over there. The latter, specifically, controls which star types,
planet types, planet sizes, and also other content get placed.

# Development

## Setup environment
### Run game with AI files from the repository folder
This part is required if you use download builds. 
If you compile the code, it works with the repo folder.
- Run game
- Open Options
- Scroll left to Others
- Click generate persistent config
- Check `Config and log files path` value
- Open directory with config
- Edit `persistent_config.xml`
- ```xml
  <?xml version="1.0"?>
  <XMLDoc>
    <resource>
      <path>...repo_path\default</path>
      <stringtable>
        <path>...repo_path\default\stringtables\en.txt</path>
      </stringtable>
    </resource>
  </XMLDoc>
  ```
### Configure IDE
Add `default\python\` as content root.

### Install dependencies for code check tools
```sh
pip install -r default\python\requirements-dev.txt
```


## Developers routine

### It's recommended to install pre-commit hooks, see: [CONTRIBUTING.md](CONTRIBUTING.md)


### Manual code style check
You don't need to run this script manually, pre-commit hooks and CI will do it fo you.

We use [ruff](https://github.com/charliermarsh/ruff) for code style checks and formatting.
Settings for ruff are located in the `pyproject.toml`.

You could run ruff in any directory, and it will check the files in that directory only.
It's recommended to run in from the root.


Code checks:
```sh
ruff check .
```

Formatting
```sh
ruff format .
```

## Running test
We use [pytest](https://docs.pytest.org) as testing framework.
There is a various options to run tests.
I'd recommend running tests from your IDE, all modern has support of pytest.

Some useful command for console:
Run all tests recursively.
```shell
pytest
```

Run test from file/folder.
You could use relative or absolute paths.
```shell
pytest tests\AI\test_assertion_fails.py
```

Run specific test using test name.
```shell
tests/AI/test_assertion_fails.py::test_does_fail
```

## Debug via chat
To inspect current AI state in the game 
It's possible to start interactive console with AI.
It's the same REPL (Read-Print-Evaluate-Loop) as you have when 
starting Python from the console. With all AI variables in it.
You could print any AI variables, you could change them,
you could even reload some python files with `reload()`

Result is printed both to the chat and logs.

- Go to "logs and config" directory
- Open AI/default/config.ini
- Add `allow_debug_chat=1` to main section
  ```ini
  [main]
  allow_debug_chat=1
  ```
- Start application
- Start game
- Send `help` to chat
- Follow instructions

## Generating skeletons
To aid development we create skeleton files. 
This files looks like Python files with empty classes/function.

The only goal of these files is to provide autocomplete and basic static checks for code.

You could to regenerate files each time C++ API has changed. 
But since this is cosmetic only changes, this operation could be postponed.  

Generation is a bit tricky, we run script inside AI that try to extract data from the C++ API.
This process is not very precise and may produce slightly different result than real API.

To generate skeletons you need to run some hooks on AI, 
in order to do it  
- Run game with AI config 
  ```sh
  freeorion --ai-config freeorion\default\python\handlers\inspect_interface_config.ini 
  ```
  `freeorion` is a path to the game binary (freeorion.exe on Windows).
  `freeorion\default\python\handlers\inspect_interface_config.ini` path to file in this repository. 
  Note: that relative paths may not work, better specify an absolute path.
- Start a new game and wait until it exits to main menu with an error.
  Generated files will be saved to AI folder:
  - freeorion\default\python\freeorion.pyi
  - freeorion\default\python\AI\freeOrionAIInterface.pyi
  You can check full paths in `AI_1.log` and `freeoriond.log`
- If you run AI from the repo, generated files will be in place,
otherwise copy them to repository with replace and commit.
