# systemd.unit files

This repository contains systemd unit files for common services configured to
run in docker. It is meant to be used in conjunction with `docker`,
`docker-machine`, and the per-user systemd instance.

Start by exporting this repository into your home directory.

```bash
mkdir -p ~/.config/systemd
git clone git@github.com:b0d0nne11/systemd.units.git ~/.config/systemd/user
```

The following aliases make the per-user systemd instance a little easier to use.

```bash
alias start='systemctl --user start'
alias stop='systemctl --user stop'
alias status='systemctl --user status'
alias reload='systemctl --user reload'
alias restart='systemctl --user restart'
```

In order to use systemd with a remote docker daemon it is neccesary to export
a few environment variables into the systemd instance. In addition, a few
services need to know the IP address of the docker host and they use the
`DOCKER_IP` environment variable.

The following snippit in `~/.bashrc` wraps `docker-machine active MACHINE` and
exports the required variables into the shell environment and systemd instance.

```bash
function docker-machine-wrapper {
  command docker-machine $@
  if [ $# -eq 2 ]; then
    case "$1" in
    'active')
      eval "$(docker-machine env)"
      export DOCKER_IP="$(docker-machine ip)"
      systemctl --user import-environment DOCKER_HOST
      systemctl --user import-environment DOCKER_TLS_VERIFY
      systemctl --user import-environment DOCKER_CERT_PATH
      systemctl --user import-environment DOCKER_IP
      ;;
    esac
  fi
}

alias docker-machine='docker-machine-wrapper'
```
