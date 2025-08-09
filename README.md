# `git-futz`

Fuzz Git commit timestamps & timezones

## Usage

Inside your repo before pushing your unpublished commits, run the
script like this:

```shell
$ git-futz [--no-tz] [<last-public-commit>]
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

Note that `<last-public-commit>` is optional.  If it is not provided,
the following heuristic will be applied to find the last public
commit.

- identify remote associated to the current branch: `my_remote`
- find the remote head it is configured to merge with: `refs/heads/branch`
- find the last public commit using: `my_remote/branch..HEAD`;
  note the 2 dots, it means in the case of diverging histories, the
  last common ancestor will be picked

  1. straight line history: `B`, `C`, `D` will be rewritten
     ```
	 public   private -->
	    A ----- B ----- C ----- D
	 ```
  2. diverging history:
	 ```
	 A ----- B ----- C ----- D ----- E (public)
	  \
	   \
	    F ----- G ----- H
	  private -->
	 ```

# FAQ

- *Q:* Can I install the script as a git hook?
- *A:* Unfortunately currently I have not found a way to do this,
  since the script rewrites history, and the `pre-push` hook (see:
  `.git/hooks/pre-push.sample`) runs _after_ determining the commits
  that should be pushed.
