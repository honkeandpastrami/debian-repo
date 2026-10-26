# debian-repo

An apt repository for honkeandpastrami's packages, served at
<https://honkeandpastrami.github.io/debian-repo>. It holds the `.deb` files attached to
the last 3 releases of every repository in [`sources`](sources).

## Use it

```sh
sudo curl -fsSLo /etc/apt/keyrings/honkeandpastrami.pgp https://honkeandpastrami.github.io/debian-repo/honkeandpastrami.pgp
printf 'Types: deb\nURIs: https://honkeandpastrami.github.io/debian-repo\nSuites: stable\nComponents: main\nSigned-By: /etc/apt/keyrings/honkeandpastrami.pgp\n' \
  | sudo tee /etc/apt/sources.list.d/honkeandpastrami.sources
sudo apt update && sudo apt install sleep-manager exchequer-cli
```

## How it works

[`publish`](.github/workflows/publish.yml) rebuilds the whole repository from GitHub
Releases, signs it with `SIGNING_KEY`, and deploys it to Pages. Nothing is committed. It
runs on every push here, by hand, and when a producer's release workflow notifies it:

```sh
gh api repos/honkeandpastrami/debian-repo/dispatches -f event_type=release
```

That call needs a fine-grained token with *Contents: read & write* on this repository,
kept as the producer's `DEBIAN_REPO_TOKEN` secret. To add a package, attach its `.deb` to
a release, add the repository to `sources`, and make that release notify this one.
