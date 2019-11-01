# go-cli-test


## Build

Build the app with `go build`. Run `build.sh` instead if you want to build cross-compiled binaries.

### Generate a GPG key

Store the keys and passphrase in your doppler config

```
gpg --full-generate-key
gpg --list-secret-keys  # copy the key's 40-character ID
gpg --output secret.key --armor --export-secret-key KEY_ID
gpg --output public.key --armor --export KEY_ID
gpg --keyserver pgp.mit.edu --send-key LAST_8_DIGITS_OF_KEY_ID
```

## Run

#### Local development

`./doppler-run -environment=$(doppler config:get environment) -pipeline=$(doppler config:get pipeline) -key=$(doppler config:get key)  -- YOUR COMMAND HERE`

#### Docker (alpine):

`docker run --rm -it -v "$(pwd)/bin":/mnt/bin -e key=$(doppler config:get key) -e pipeline=$(doppler config:get pipeline) -e environment=$(doppler config:get environment) alpine:latest`


## Test

Test building for all targets:

`goreleaser release --snapshot --skip-publish --rm-dist`


## Release

```
vi version.go  # bump the version
git add version.go && git commit -m "Bump version to vX.Y.Z" && git push
git status # confirm clean workspace
git tag vX.Y.Z -a -m "The release message"
git push --tags
GITHUB_TOKEN=$(doppler secrets get GITHUB_TOKEN --plain goreleaser release --rm-dist
```

Note: The release will automatically fail if the tag and HEAD have diverged
`   ⨯ release failed after 0.13s error=git tag v0.0.2 was not made against commit c9c6950d18790c17db11fedae331a226f8f12c6b`

## Help

Issue: `gpg: signing failed: Inappropriate ioctl for device`
Fix: `export GPG_TTY=$(tty)`
