# ContainderD and Cri commands


## Containerd remove all resources
```

#!/bin/bash

set -o errexit
set -o xtrace

main() {
        local namespaces=$(list_namespaces)

        for namespace in $namespaces; do
                local tasks=$(list_tasks $namespace)
                for task in $tasks; do
                        delete_task $namespace $task
                done

                local containers=$(list_containers_in_namespace $namespace)
                for container in $containers; do
                        delete_container $namespace $container
                done

                local images=$(list_images $namespace)
                for image in $images; do
                        delete_image $namespace $image
                done

                delete_namespace $namespace
        done
}

list_namespaces() {
        ctr --namespace=$namespace namespace list -q
}

list_containers_in_namespace() {
        local namespace=$1
        ctr --namespace=$namespace container list -q
}

list_tasks() {
        local namespace=$1
        ctr -n $namespace task list -q
}

list_images() {
        local namespace=$1
        ctr -n $namespace image list -q
}

delete_image() {
        local namespace=$1
        local image=$2
        ctr -n $namespace image rm $image
}

delete_task() {
        local namespace=$1
        local task=$2
        ctr -n $namespace task delete --force $task
}

delete_container() {
        local namespace=$1
        local container=$2
        ctr --namespace=$namespace container delete $container
}

delete_namespace() {
        local namespace=$1
        ctr namespace remove $namespace
}

main "$@"
```
