# template-snap

An attempt to build a pi completely from gh

## workflows

### `main.yaml`

Used to build local:

    - `snap/`
    - `src/`

into the final `snapcraft`.

#### Obtain value for `secrets.SNAPCRAFT_TOKEN`

> Reference: [Snapcraft CMD Login](https://snapcraft.io/docs/snapcraft-authentication)

##### On a local machine

This action workflow will log you in to the Snap Store. For this to work, you need an Ubuntu One account.

###### Install `snapcraft`

`snap install snapcraft --classic`

You will also need a Snap Store login token. To obtain one, run the following command on your machine:

```shell
snapcraft export-login <some-credentials-filename>
cat <some-credentials-filename>
```
Copy the output from above code-snippet (`jwt`-like token) and add this to the repository's secrets variable `SNAPCRAFT_TOKEN`.

### `seek_remote_dispatch.yaml`

#### Obtain value for `secrets.PAT`

> **NOTE**: this is **ONLY** required if this repository is needing to obtain content from a third-party repository.
>
> > Goto: [Create New Personal Access Token](https://github.com/settings/personal-access-tokens/new) and set the below requirements to create a PAT.

##### Repository Access

- `Only select repositories`
  - select the specific repository you are working with.

##### Permissions

- `Repository permissions`:
  - [Contents](https://docs.github.com/rest/overview/permissions-required-for-fine-grained-personal-access-tokens#repository-permissions-for-contents): `r+w`
    - **yields**: `mandatory`: Read access to metadata

Set the following `Repository Variables`:
> **Execute** in terminal for fast-link:
```shell
echo "${$(git config --get remote.origin.url)//'.git'/}/settings/variables/actions/new"
```
- **Name**: `${{ vars.* }}` current repo variable name
  - **Value**: description of value for said variable name

- `GH_REMOTE_OWNER_ORG`
  - the owner or organization the remote repo you'd like to watch
- `GH_REMOTE_REPOSITORY`
  - the name of the remote repo you'd like to watch
<!-- TODO: add this example and fix workflow -->
- `USE_GH_RELEASE`, **default** = `false`
  - informs the dispatcher and cacher steps to use `github's` assets/release files instead of another defined source.

Example
```shell
https://github.com/jasonkolodziej/template-snap.git

GH_REMOTE_OWNER_ORG=jasonkolodziej
GH_REMOTE_REPOSITORY=template-snap

#? the following URL is templated as
"https://github.com/${GH_REMOTE_OWNER_ORG}/${GH_REMOTE_REPOSITORY}.git"
```

- 

```yaml
# <github actions manifest ...>
          OWNER: ${{ vars.GH_REMOTE_OWNER_ORG }}
          REPOSITORY: ${{ vars.GH_REMOTE_REPOSITORY }}
          USE_GH_RELEASE: ${{ vars.USE_REMOTE_GH_RELEASE_FILES }} #? default=false
# <github actions manifest ...>
```
