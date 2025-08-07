# `git-futz`

Fuzz Git commit timestamps & timezones

## Usage

Inside your repo before pushing your unpublished commits, run the
script like this:

```shell
$ git-futz <last-public-commit>
```

`git-futz` will rewrite the timestamps such that they are evenly
distributed between the timestamp of the `<last-public-commit>` and
now.  And it will assign a timezone randomly.

# TODOs

Preserve committer name & email, currently it is assumed it is the
same as the person running the script.  Since this is a branch with
unpublished commits, it seems like a reasonable assumption, but
strictly speaking it may not be the case.
