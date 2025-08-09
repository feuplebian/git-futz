# `git-futz`

Fuzz Git commit timestamps & timezones

## Installation

No installation is necessary, however if you put the script in your
`PATH`, you could run the script as a custom Git command; e.g.

```shell
$ cp git-futz ~/bin/
$ git futz -h
```

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
	   F ----- G ----- H
     private -->
	 ```

# Change log
- POC: works after some `sed` magic :tada:
  - depends on Python for date arithmetic :slightly_frowning_face:
- Reimplement date arithmetic using integer maths in Bash :wink:
- Add timezone randomisation
- Make timezone randomisation optional; improves portability :sparkles:
- Bug fix: was reading commit timestamp with `git-show` instead of `git-log` :no_mouth:
- Preserve committer name & email :1st_place_medal:
- Document various install options
- Add automatic upstream detection heuristic :gift:


# FAQ

- **Q:** How can I run `git-futz` automatically before a `git-push`?
- **A:** You could write a git alias that runs `git-futz` before doing
  a push.
  ```config
  [alias]
          futzy-push  =  !git futz && git push
  ```

- **Q:** Can I install the script as a git hook?
- **A:** Unfortunately currently I have not found a way to do this,
  since the script rewrites history, and the `pre-push` hook (see:
  `.git/hooks/pre-push.sample`) runs _after_ determining the commits
  that should be pushed.


- **Q:** Can I schedule running `git-futz` and `git-push` to also not
  reveal when I ran the command?
- **A:** This is a bit tricky, I'm not certain yet what would be the
  best practice.  Note that `git-futz` only alters the timestamp in
  the commit history.  When you `git-push`, your remote forge (GitHub,
  GitLab, etc) can record the time.

  On GitHub, this is sometime shown as a notification on the
  repository page:

  > @username pushed branch `branch_name` XYZ minutes ago

  I don't think this can be avoided.  So to "poison" this information,
  you could schedule a cron job at your desired time.  Something like
  this should work:
  ```shell
  cd /path/to/repo && git futz && git push
  ```
