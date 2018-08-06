# CPU-POOL Test
These tests are to evaluate cpu-pool feature in different environments especially for NUMA and HT mode.

## Hardware condition
| Role | System | CPU | Memory | Storage |
| ------ | ------- | ------- | ------| -- |
| Master | Ubuntu16.04LTS,64 bit | i7-6700K CPU @ 4.00GHz*1 | DDR4 2133 MHz 8G * 4 | 1T |
| Worker1 | Fedora 4.13.16-100.fc25.x86_64 | Intel(R) Xeon(R) Platinum 8167M CPU @ 2.00GHz * 2 | DDR4 8G 2400MHz* 2 * 12 - 4G(Swap area) | 186G

## Software
First to tested is cpu workload type: [speccpu2006.img](http://10.239.173.96/download/skx-issue/speccpu2006.img.xz)

## Test for NUMA
- Prerequirements
1. Disable HT. To avoid HT effect and evaluate it only for NUMA
2. Set up kubernetes cluster. We will test in three modes: [Original K8s mode](https://v1-10.docs.kubernetes.io/docs/setup/independent/create-cluster-kubeadm/), [First-touch mode and Interleave mode](https://github.com/chuan9/kubernetes/blob/devel/cpu-pool/v1.10.3/CPU-POOLS.md).  
3. Intro for three modes:  
    - K8S original mode: mode run with the original k8s binary and default scheduler  
    - First-touch mode: Run pod on cores which belongs to one NUMA mode, assign task to cores using cpu-pool feature.  
    As is shown in the following table, pod runs on cpu which is green.  
    *Instance=26*  
    ![First-touch](https://github.com/chuan9/kubernetes/blob/cpu-pool-test/test/kubelet/pool-tool/spec/firsttouch26.png)  
    *Instance=14*    
    ![First-touch14](https://github.com/chuan9/kubernetes/blob/cpu-pool-test/test/kubelet/pool-tool/spec/firsttouch14.png)  
    *Instance=2*    
    ![First-touch2](https://github.com/chuan9/kubernetes/blob/cpu-pool-test/test/kubelet/pool-tool/spec/firsttouch2.png)  
    - Interleave mode: Run pod on cores half on local NUMA mode and half on remote NUMA-node, assign task to cores using cpu-pool feature.  
    As is shown in the following table, pod runs on cpu which is green.  
    *Instance=26*  
    ![Interleave](https://github.com/chuan9/kubernetes/blob/cpu-pool-test/test/kubelet/pool-tool/spec/interleave26.png)  
    *Instance=14*  
    ![Interleave14](https://github.com/chuan9/kubernetes/blob/cpu-pool-test/test/kubelet/pool-tool/spec/interleave14.png)  
    *Instance=2*  
    ![Interleave2](https://github.com/chuan9/kubernetes/blob/cpu-pool-test/test/kubelet/pool-tool/spec/interleave2.png)  

- Test table  

    | Test Mode | type | Instance | Execution time(second) |
    |-----------|------|----------|------------------------|
    | Original K8S | -- | -- | -- |
    | First touch  | -- | -- | -- |
    | Interleave   | -- | -- | -- |

- Notes  
    - Instance should be same for three modes:26
    - Number of cores used under three modes should be same:26
    - As CPU#0 is reserved for system process, test first touch mode on NUMA node 1

## Test for HT
- Prerequirements
1. Disable NUMA. To avoid NUMA effect and evaluate it only for HT
2. Set up kubernetes cluster. We will test in three modes: [Original K8s mode](https://v1-10.docs.kubernetes.io/docs/setup/independent/create-cluster-kubeadm/), [One core mode and two core mode](https://github.com/chuan9/kubernetes/blob/devel/cpu-pool/v1.10.3/CPU-POOLS.md).
3. Intro for three modes:  
    - K8S original mode: mode run with the original k8s binary and default scheduler  
    - One thread mode: Run pod on cores which belongs to one thread, assign task to cores using cpu-pool feature  
    - Cross-thread mode: Run pod on cores which belongs to two thread, which means, every core has two threads, both of them run pods, assign task to cores using cpu-pool feature

- Test table

    | Test Mode | Instance | Execution time |
    |-----------|----------|----------------|
    | Original K8S | TBD | pending |
    | One thread mode | TBD | pending |
    | Cross-thread mode | TBD | pending |

- Notes  
    - Instance should be same for three modes
    - Number of cores used under three modes should be same

## Future test
We plan to test Different workload type: Memory type, Storage Type and Network type.

