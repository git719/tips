---
type: howto
---
## Internet Archive

A login to [archive.org](https://archive.org/) is worth less than it looks, so know what it buys before you set one up. Search and Wayback Machine snapshots need no login at all. A book marked "Borrow" opens only in the browser after you borrow it, and neither your keys nor any command will get you its text. Some lending items cannot be borrowed at all. What the keys do unlock is uploading, Save Page Now, and downloads of files your account is entitled to, such as your own uploads. If that is what you need, here is a login that the `ia` command line and a coding-agent session can both reuse.

### 1. Create the account

Sign up at [archive.org/signup](https://archive.org/signup) and confirm the email.

### 2. Install the command line

```bash
brew install internetarchive   # macOS
pipx install internetarchive   # elsewhere
```

Both provide the `ia` command.

### 3. Log in once

Run this in a real terminal, not from a script:

```bash
ia configure
```

It asks for the account email, then for the password with hidden input. It fetches the account's S3-style keys from archive.org and writes them with mode 600 to `$XDG_CONFIG_HOME/internetarchive/ia.ini`, which is `~/.config/internetarchive/ia.ini` by default. It still reads the older `~/.config/ia.ini` and `~/.ia`. A wrong password fails with a clear error.

For scripts there is `ia configure -u EMAIL -p PASSWORD`, which leaves the password in your shell history. If you keep a `.netrc`, use `ia configure --netrc` instead.

### 4. Confirm the login

```bash
ia configure --check    # exits 0 when the keys are valid
ia configure --whoami   # prints the account the keys belong to
ia configure --show     # prints the config with the secret and login cookie redacted
ls -l ~/.config/internetarchive/ia.ini   # expect -rw-------
```

### Manual key file

You can also copy the keys from [archive.org/account/s3.php](https://archive.org/account/s3.php) into the file yourself:

```ini
[s3]
access = <ACCESS-KEY>
secret = <SECRET-KEY>
```

### 5. Use it

```bash
ia search 'subject:"market street" collection:prelinger' -f title -p rows:3
ia metadata IDENTIFIER
ia list IDENTIFIER
ia download IDENTIFIER
```

Search prints identifiers only, unless `-f FIELD` adds fields such as `title`. Use `-p page:1 -p rows:20` to limit results, and `-i` for bare identifiers to feed a batch download.

### Agent sessions

Any tool that shells out picks the login up from the ini file, with no environment setup. Give the session allow rules for the read-only `ia` subcommands, such as `search`, `metadata`, and `list`, and a deny rule on reading the config folder. That way it can use the keys but never print them. Point its web fetches elsewhere, since a fetch never carries the login. If the secret ever leaks, regenerate the keys at the s3.php page.

### No-login fallback

The public JSON endpoint answers searches with no account at all:

```text
https://archive.org/advancedsearch.php?q=QUERY&fl[]=identifier&fl[]=title&rows=10&output=json
```

The official docs live at [archive.org/developers/internetarchive](https://archive.org/developers/internetarchive/).
