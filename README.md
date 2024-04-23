# Extending kubectl

1. Install and configure kubectl plugin manager [Krew](https://krew.sigs.k8s.io/plugins/):

```sh
(
  set -x; cd "$(mktemp -d)" &&
  OS="$(uname | tr '[:upper:]' '[:lower:]')" &&
  ARCH="$(uname -m | sed -e 's/x86_64/amd64/' -e 's/\(arm\)\(64\)\?.*/\1\2/' -e 's/aarch64$/arm64/')" &&
  KREW="krew-${OS}_${ARCH}" &&
  curl -fsSLO "https://github.com/kubernetes-sigs/krew/releases/latest/download/${KREW}.tar.gz" &&
  tar zxvf "${KREW}.tar.gz" &&
  ./"${KREW}" install krew
)
```

2. Add the $HOME/.krew/bin directory to your PATH environment variable. Using command: `nano ~/.bashrc`, after restart your shell.

```sh
export PATH="${KREW_ROOT:-$HOME/.krew}/bin:$PATH"
```

3. Run kubectl krew to check the installation.

```sh
$ k krew      
krew is the kubectl plugin manager.
You can invoke krew through kubectl: "kubectl krew [command]..."

Usage:
  kubectl krew [command]

Available Commands:
  help        Help about any command
  index       Manage custom plugin indexes
  info        Show information about an available plugin
  install     Install kubectl plugins
  list        List installed kubectl plugins
  search      Discover kubectl plugins
  uninstall   Uninstall plugins
  update      Update the local copy of the plugin index
  upgrade     Upgrade installed plugins to newer versions
  version     Show krew version and diagnostics

Flags:
  -h, --help      help for krew
  -v, --v Level   number for the log level verbosity

Use "kubectl krew [command] --help" for more information about a command.
```

4. Use the file from repository `kubeplugin` and grant execution rights:

```sh
$ chmod 755 kubeplugin
$ ll kubeplugin
-rwxr-xr-x 1 unweijoozd unweijoozd 684 кві 23 16:00 kubeplugin
```

5. Copy kubeplugin to /user/local/bin/ directory

```sh
sudo cp ./kubeplugin /usr/local/bin/kubectl-kubeplugin
```

6. Check the list of existing plugins

```sh
$ kubectl plugin list

/home/unweijoozd/.krew/bin/kubectl-krew
/usr/local/bin/kubectl-kubeplugin
```

7. Plugin usage:

```sh
$ k kubeplugin pod kube-system
Resource, Namespace, Name, CPU, Memory
pod, kube-system, coredns-6799fbcd5-h6dv5, 1m, 55Mi
pod, kube-system, local-path-provisioner-6c86858495-mpdlf, 1m, 8Mi
pod, kube-system, metrics-server-54fd9b65b-9j5xr, 4m, 24Mi
pod, kube-system, svclb-traefik-b90a5bdd-pxvw8, 0m, 2Mi
pod, kube-system, traefik-f4564c4f4-g5zmw, 1m, 118Mi

$ k kubeplugin node kube-system
Resource, Namespace, Name, CPU, Memory
node, kube-system, k3d-mycluster-server-0, 42m, 0%
```