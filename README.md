# ssh-private-key-buildpack - gitlab default

This is an altered version of [https://github.com/debitoor/ssh-private-key-buildpack](https://github.com/debitoor/ssh-private-key-buildpack), which did not correctly pull hosts from the environment. This version is set to use both git@github.com and git@gitlab.com as hosts, so it'll correctly grab all necessary dependencies.

Set this in your app's buildpacks like so:

```bash
heroku buildpacks:set -i 1 https://github.com/circles-learning-labs/ssh-private-key-buildpack --app APP_NAME_HERE
```



> (Original repo notes)

A Heroku buildpack for setting the ssh private key as part of the application build. It's meant to be used as part of a setup [using multiple buildpacks](https://devcenter.heroku.com/articles/using-multiple-buildpacks-for-an-app), so other buildpacks can authenticate with hosts using ssh keys, for instance to install dependencies from private git repositories.

# Example usage

## Configure Multiple Buildpacks
### _Option 1:_ Heroku CLI or Dashboard
Add the buildpack to your Heroku app either using the CLI or the Heroku dashboard. The `ssh-private-key-buildpack` needs to run before any buildpack trying to get ssh access. In the following example, it runs before the `heroku/go` buildpack.

    $ heroku buildpacks:set --index 1 https://gitlab.com/darrenklein/ssh-key-private-buildpack.git
    $ heroku buildpacks:add heroku/go

### _Option 2:_ Use `heroku-buildpack-multi`
Instead of setting the buildpacks directly with Heroku they can also be configured using a `.buildpacks` in combination with [`heroku-buildpack-multi`]( https://github.com/heroku/heroku-buildpack-multi).

    $ heroku buildpacks:set https://github.com/heroku/heroku-buildpack-multi.git  
The same example given for the CLI use would have the following `.buildpacks` file.

    $ cat .buildpacks
    https://gitlab.com/darrenklein/ssh-key-private-buildpack.git
    https://github.com/heroku/heroku-buildpack-go

## Configure SSH Key

Set the private key environment variable `SSH_KEY` of your Heroku app (note that the key needs to be base64 encoded).

    $ heroku config:set SSH_KEY=$(cat path/to/your/keys/id_rsa | base64)

> (The following note is from the original repo, and describes the broken feature. This version defaults to git@gitlab.com and will not accept multiple hosts.)

By default the buildback adds Github to `known_hosts`. However you can configure your app to allow custom hosts, too. All that's needed is the set `SSH_HOSTS` for you app to a comma-separated list of hosts, e.g. `git@github.com,example.com`

    $ heroku config:set SSH_HOSTS="git@github.com,example.com"
