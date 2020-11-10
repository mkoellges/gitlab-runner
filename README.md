# The gitlab-runner

## Architecture
The gitlab source code management is able to run CI/CD pipelines. But for that, nodes that execute the tasks defined in the pipeline are needed because the Source Code Management (SCM) system is not executing this. 

For this, we have to install special nodes that execute our jobs inside a defined pipeline. These nodes are called "Runners" because the tasks that are defined in our pipeline are executed here.

## Install the gitlab-runner

Here the installation of the gitlab-runner for Ubuntu is described.

To install the gitlab-runner, we need to add the system repository that contains the gitlab runner software. Add the repository with

```sh
curl -L https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh | sudo bash
```

Now we have prepared the system for installing the software using the packager of ubuntu.

```sh
export GITLAB_RUNNER_DISABLE_SKEL=true
sudo -E apt-get install gitlab-runner
```

The installation of the gitlab-runner enables and starts the runner as a service. So you can be sure that it is up and running after next boot too.

## Configure the gitlab-runner

The configuration of the gitlab-runner ist store in ```/etc//gitlab-runner/config.toml```. This file contains the configuration of the software itself and of all registered endpoints where the runner can work with. 

An example configfile after installing the software shoul looks like this:

```toml
concurrent = 4
check_interval = 0

[session_server]
  session_timeout = 1800
```

With this settings, you can run 4 parallel builds on this runner. If you want to increase/decrease the amount of parallel build processes, change the number of "concurrent".

## Register the gitlab-runner

To activate the gitlab-runner in gitlab itself, you must run a registration command on the gitlab-runner node and add the credentials of your gitlab repository or gitlab group. Keep in mind, when adding a gitlab-runner to a group, all repositories of the group can work with the configured gitlab-runner.

```sh
sudo gitlab-runner register
```
Now input the asked credentials. To find the correct credentials that are asked in the registration process, checkout in ```gitlab -> Settings -> CI/CD -> Runners```. Here expand the ```Runners``` and on the page that will be opened you find the gitlab URL and the associated token. Both information is needed in register process. An Example of registering:

```sh
gitlab-runner register \
  --non-interactive \
  --url "https://git.metrosystems.net/" \
  --registration-token "[REGSTER_TOKEN]" \
  --description "Gitlab-runner-machine-1" \
  --executor "shell" \
  --tag-list "manni"
```
You can add more than one tag using a comma separated list.

This register information is written in the config file. 

You find the whole documentation of this here: https://docs.gitlab.com/runner/register/

## Reload the gitlab-runner

After a reconfiguration, the configfile must be reread and activated with a restart of the gitlab-runner. 

```sh
sudo gitlab-runner restart
```