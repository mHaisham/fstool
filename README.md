### fstool

install using pip

```python
python3 -m pip install fstool
```

Command line tool
```bash
python3 -m fstool -h

usage: __main__.py [-h] [-p PATH] [-c CONFIG] [-v] [-m] [-o] [-s START] [-e END]
{restructure}

format file directory files

positional arguments:
  {restructure}

optional arguments:
  -h, --help            show this help message and exit
  -p PATH, --path PATH  home path
  -c CONFIG, --config CONFIG
                        config file location. default is config.json
  -v, --verbose         increase output verbosity
  -m, --move            moves files instead of default copy
  -o, --overwrite       ovewrites the file if it exists
  -s START, --start START
                        start depth of file search
  -e END, --end END     end depth of file search

```

#### Documentation

Exposes three functions to the public
* restructure
```python
restructure(
    home: str,
    files: list,
    config: dict,
    *,
    move: bool = True,
    overwrite: bool = False,
    verbose: bool = False,
):
```

`config` is a dictionary of instructions on how to restructure the `files`, an example of which would be
```json
{ 
   ".+\\.txt":{ 
      "dir":"aiml",
      "file":".+\\.txt"
   },
   "(.+)\\.properties":{ 
      "dir":"config",
      "file":"\\1.txt"
   }
}
```

This is inefficient to write so `fstool` comes with a built in parser (`parse_config`) so that the above json can be written as
```json
{
    "aiml": [
        ".+\\.txt"
    ],
    "config": [{
        "old": "(.+)\\.properties",
        "new": "\\1.txt"
    }],
}
```

where the `key` is the directory and maps to a list of regex patterns or if you want to rename the file an object with keys `new` and `old`

`old` - file to move

`new` - file to be moved to

* crawl
```python
crawl(
        path: str,
        start_depth: int = 0,
        end_depth: int = -1,
        *,
        skip_permission_error: bool = True) -> tuple:
```

```python
    iterates through directores starting with path

    :return: (files, dirs) from start_depth(inclusive) to end_depth(exclusive)

    default start_depth is 0
    default end_depth is -1 (till all paths exhausted)

    :param skip_permission_error: when true skips through all directores that requires elevated privilages

    *note: if start_depth equals end_depth lists will be empty
```

To default parameters left to it would iterate through all the subdirectories

###### depth 0
`start_depth` - 0

`start_depth` - 1

###### depth 1
`start_depth` - 1

`start_depth` - 2

`etc`

* parse_config

```python
parse_config(config: dict) -> dict:
```

Compiles input json to a more efficient version which can be used by the `restructure` function
