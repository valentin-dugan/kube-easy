# Kube-Easy


## 0. Prerequisites:
  - install fzf
  - install kubectl
  - for every kubernetes cluster keep kubeconfig as separate files with descriptive name in `~/.kube` directory



## 1. Add script to shell
- To find out what shell you are using run command: `echo $SHELL`
- Depending on your shell add to `~/.bashrc` or `~/.zshrc` the following code:

```bash
_k () {

  DIR=~/.kube/
  FILE=$(ls $DIR | fzf)

  export KUBECONFIG="${DIR}${FILE}"

  KUBECONFIG_FILE=$FILE
  KUBE_NAMESPACE=$( grep 'namespace:' ${DIR}${FILE} | awk '{print $2}' )

  alias kk='unset KUBECONFIG KUBECONFIG_FILE KUBE_NAMESPACE ; alias k=_k '
  alias k="kubectl"

}

kns () {

   if [[ -n $1 ]]; then
     kubectl config set-context --current --namespace $1  &>/dev/null
     KUBE_NAMESPACE=$( grep "namespace:" "${DIR}${FILE}" | awk '{print $2}' )

   else
     kubectl config set-context --current --namespace='' &>/dev/null
     KUBE_NAMESPACE=''

   fi

 }

alias k='_k'

unset DIR
unset FILE
```


## 3. (Optional) Modify your shell prompt to have a feedback of what kubernetes cluster you control

For bash shell add to `~/.bashrc` the following line:
```bash
PS1+='$( [[ -n $KUBECONFIG_FILE ]] && echo " $KUBECONFIG_FILE ") $( [[ -n $KUBE_NAMESPACE ]] && echo " $KUBE_NAMESPACE ") '
```

For zshell add to `~/.zshrc` the following line
```bash
PROMPT='$( [[ -n $KUBECONFIG_FILE ]] && echo " $KUBECONFIG_FILE ") $( [[ -n $KUBE_NAMESPACE ]] && echo " $KUBE_NAMESPACE ") '
```


### How to use

```
k - prompt user to choose a kubeconfig file, after that it will behave as kubectl command
kns <namespace> - change namespace
kk - exit. Unset KUBECONFIG varible
```
