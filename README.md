# Composer copy file

Composer script copying your files after install. Supports copying of entire directories, individual files and complex nested directories.

For example copy fonts:

```
{
    "require":{
        "twbs/bootstrap": "~3.3",
        "slowprog/composer-copy-file": "~0.2"
    },
    "scripts": {
        "post-install-cmd": [
            "SlowProg\\CopyFile\\ScriptHandler::copy"
        ],
        "post-update-cmd": [
            "SlowProg\\CopyFile\\ScriptHandler::copy"
        ]
    },
    "extra": {
        "copy-file": {
            "vendor/twbs/bootstrap/fonts/": "web/fonts/"
        }
    }
}
```

## Using for deployment or other composer commands
This version (forked from https://github.com/slowprog/CopyFile) also supports getting the file sources from another configuration file and executing the command via setting another composer command.

Potential use cases for this include using it for configuring the local dev environment, where the config file is git ignored.

If a config file is not defined or the files to be copied is defined for the said command, copyFile will default back to the common defined file list in the composer.json.

An example building upon the above
```
{
    "require":{
        "twbs/bootstrap": "~3.3",
        "slowprog/composer-copy-file": "~0.2"
    },
    "scripts": {
        "post-install-cmd": [
            "SlowProg\\CopyFile\\ScriptHandler::copy"
        ],
        "post-update-cmd": [
            "SlowProg\\CopyFile\\ScriptHandler::copy"
        ],
        "deploy": [
            "SlowProg\\CopyFile\\ScriptHandler::copy"
        ]
    },
    "extra": {
        "copy-file": {
            "vendor/twbs/bootstrap/fonts/": "web/fonts/"
        },

        "copy-config": "config/external-copy.php"
    }
}

```

### Copy Config file format

The file needs to define the list of command names along with the files to be copied in the following format, in a php array.

```
$copyFiles=array(
    'deploy'=>array('blog/'=>'/var/wwwroot/blog', 'source/'=>'/var/wwwroot/test'),
    'config-update'=>array('wp-config.php' => 'blog/wp-config.php')
  );
```


## Use cases

You need to be careful when using a last slash. The file-destination is different from the directory-destination with the slash.

Source directory hierarchy:

```
dir/
    subdir/
        file1.txt
        file2.txt
```

1. Dir-to-dir:

    ```
    {
        "extra": {
            "copy-file": {
                "dir/subdir/": "web/other/"
            }
        }
    }
    ```

    Result:

    ```
    web/
        other/
            file1.txt
            file2.txt
    ```

2. File-to-dir:

    ```
    {
        "extra": {
            "copy-file": {
                "dir/subdir/file1.txt": "web/other/"
                "dir/subdir/file2.txt": "web/other/file2.txt/"
            }
        }
    }
    ```

    Result:

    ```
    web/
        other/
            file1.txt
            file2.txt/
                file2.txt
    ```

3. File-to-file:

    ```
    {
        "extra": {
            "copy-file": {
                "dir/subdir/file1.txt": "web/other/file1.txt"
                "dir/subdir/file2.txt": "web/other/file_rename.txt"
            }
        }
    }
    ```

    Result:

    ```
    web/
        other/
            file1.txt
            file_rename.txt
    ```
