# `git-futz`

Fuzz Git commit timestamps & timezones

## Usage

Inside your repo before pushing your unpublished commits, run the
script like this:

```shell
$ git-futz [--no-tz] <last-public-commit>
```

`git-futz` will rewrite the timestamps such that they are evenly
distributed between the timestamp of the `<last-public-commit>` and
_now_.

Optionally, it also assigns a timezone randomly, i.e. if you run it
today it might choose London, and on a subsequent call tomorrow it
could pick Tokyo.  This however needs some Linux specific commands
(`timedatectl` & `shuf`).  If the dependencies are not available, this
feature is disabled.

If you want to disable the feature explicitly, you can pass the
optional flag `--no-tz`.

# TODOs

Preserve committer name & email, currently it is assumed the original
committer was the person running the script.  Since this is a branch
with unpublished commits, it seems like a reasonable assumption, but
strictly speaking it may not be the case.
