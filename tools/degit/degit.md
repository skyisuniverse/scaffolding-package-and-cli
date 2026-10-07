# **[degit](https://github.com/Rich-Harris/degit)** 

is a command-line tool created by Rich Harris for fast project [scaffolding](https://gdevops.frama.io/opsindev/tuto-project/architecture/physical_architecture/javascript/degit/definition/definition.html) that downloads Git repository tarballs without keeping full git histories or .git folders.


## Key Features

- **Fast Copies**: Downloads a compressed tar archive of the latest commit instead of running a full `git clone`.
- **No `.git` Folder**: Removes version control files so you can start a fresh project repository.
- **Subdirectories & Branches**: Supports downloading specific branches, tags, commits, or subfolders (e.g., `user/repo#tag` or `user/repo/subdir`).
- **Custom Actions**: Allows automated file removal or post-clone steps via a `degit.json` configuration file.
