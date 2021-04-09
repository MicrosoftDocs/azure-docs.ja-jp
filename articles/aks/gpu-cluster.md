---
title: Azure Kubernetes Service (AKS) での GPU の使用
description: Azure Kubernetes Service (AKS) で高パフォーマンス コンピューティングやグラフィックを集中的に使用するワークロードに GPU を使用する方法について説明します
services: container-service
ms.topic: article
ms.date: 08/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: d7e312f049acc0b74aa0a253864bfce6100044bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96929142"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でコンピューティングを集中的に使用するワークロードに GPU を使用する

GPU (Graphical processing units) は、多くの場合に、グラフィックや視覚化ワークロードなど、コンピューティングを集中的に使用するワークロードに使用とされます。 AKS では、Kubernetes でこれらのコンピューティングを集中的に使用するワークロードを実行する GPU 対応ノード プールの作成をサポートしています。 使用可能な GPU 対応 VM の詳細については、[Azure での GPU 最適化済み VM サイズ][gpu-skus]に関する記事を参照してください。 AKS ノードには、最小サイズの *Standard_NC6* をお勧めします。

> [!NOTE]
> GPU 対応 VM には、より高い価格が適用され、利用可能なリージョンが限られる特殊なハードウェアが含まれます。 詳細については、[価格][azure-pricing]ツールと[利用可能なリージョン][azure-availability]を参照してください。

現在、GPU 対応ノード プールの使用は Linux ノード プールでのみ使用できます。

## <a name="before-you-begin"></a>開始する前に

この記事は、GPU をサポートするノードを含む AKS クラスターが既に存在していることを前提としています。 AKS クラスターで Kubernetes 1.10 以降を実行している必要があります。 これらの要件を満たす AKS クラスターが必要な場合は、この記事の最初のセクションを参照して、[AKS クラスターを作成](#create-an-aks-cluster)してください。

また、Azure CLI バージョン 2.0.64 以降がインストールされ、構成されている必要もあります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

## <a name="create-an-aks-cluster"></a>AKS クラスターを作成する

最小要件 (GPU 対応ノードと Kubernetes バージョン 1.10 以降) を満たしている AKS クラスターが必要な場合は、次の手順を実行します。 これらの要件を満たしている AKS クラスターが既にある場合は、[次のセクションに進んでください](#confirm-that-gpus-are-schedulable)。

まず、[az group create][az-group-create] コマンドを使用して、クラスターのリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* リージョンに作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

ここで、[az aks create][az-aks-create] コマンドを使用して、AKS クラスターを作成します。 次の例では、サイズ `Standard_NC6` の 1 つのノードを含むクラスターを作成します。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1
```

[az aks get-credentials][az-aks-get-credentials] コマンドを使用して、AKS クラスターの資格情報を取得します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="install-nvidia-device-plugin"></a>NVIDIA デバイス プラグインをインストールする

ノード内の GPU を使用するには、NVIDIA デバイス プラグイン用の DaemonSet をデプロイしておく必要があります。 この DaemonSet により、各ノードでポッドが実行され、GPU に必要なドライバーが提供されます。

まず、[kubectl create namespace][kubectl-create] コマンドを使用して、名前空間 (*gpu-resources* など) を作成します。

```console
kubectl create namespace gpu-resources
```

*nvidia-device-plugin-ds.yaml* という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。 このマニフェストは、[Kubernetes プロジェクト用の NVIDIA デバイス プラグイン][nvidia-github]の一部として提供されます。

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nvidia-device-plugin-daemonset
  namespace: gpu-resources
spec:
  selector:
    matchLabels:
      name: nvidia-device-plugin-ds
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      # Mark this pod as a critical add-on; when enabled, the critical add-on scheduler
      # reserves resources for critical add-on pods so that they can be rescheduled after
      # a failure.  This annotation works in tandem with the toleration below.
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        name: nvidia-device-plugin-ds
    spec:
      tolerations:
      # Allow this pod to be rescheduled while the node is in "critical add-ons only" mode.
      # This, along with the annotation above marks this pod as a critical add-on.
      - key: CriticalAddonsOnly
        operator: Exists
      - key: nvidia.com/gpu
        operator: Exists
        effect: NoSchedule
      containers:
      - image: mcr.microsoft.com/oss/nvidia/k8s-device-plugin:1.11
        name: nvidia-device-plugin-ctr
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
        volumeMounts:
          - name: device-plugin
            mountPath: /var/lib/kubelet/device-plugins
      volumes:
        - name: device-plugin
          hostPath:
            path: /var/lib/kubelet/device-plugins
```

ここで、次の出力例に示すように、[kubectl apply][kubectl-apply] コマンドを使用して DaemonSet を作成し、NVIDIA デバイス プラグインが正常に作成されていることを確認します。

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="use-the-aks-specialized-gpu-image-preview"></a>AKS 専用 GPU イメージ (プレビュー) を使用する

これらの手順の代わりとして、AKS からは、[Kubernetes 向け NVIDIA デバイス プラグイン][nvidia-github]が既に含まれる、完全構成済みの AKS イメージが提供されます。

> [!WARNING]
> 新しい AKS 専用 GPU イメージを使用し、クラスター向け NVIDIA デバイス プラグイン デーモン セットを手動インストールしないでください。


`GPUDedicatedVHDPreview` 機能を登録します。

```azurecli
az feature register --name GPUDedicatedVHDPreview --namespace Microsoft.ContainerService
```

状態が "**登録済み**" と表示されるまでに数分かかることがあります。 [az feature list](/cli/azure/feature#az-feature-list) コマンドを使用して登録状態を確認できます。

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/GPUDedicatedVHDPreview')].{Name:name,State:properties.state}"
```

状態が登録済みと表示されたら、[az provider register](/cli/azure/provider#az-provider-register) コマンドを使用して、`Microsoft.ContainerService` リソース プロバイダーの登録を更新します。

```azurecli
az provider register --namespace Microsoft.ContainerService
```

aks-preview CLI 拡張機能をインストールするには、次の Azure CLI コマンドを使用します。

```azurecli
az extension add --name aks-preview
```

aks-preview CLI 拡張機能を更新するには、次の Azure CLI コマンドを使用します。

```azurecli
az extension update --name aks-preview
```

### <a name="use-the-aks-specialized-gpu-image-on-new-clusters-preview"></a>新しいクラスターで AKS 専用 GPU イメージ (プレビュー) を使用する    

クラスターの作成時に、AKS 専用 GPU イメージを使用するようにクラスターを構成します。 AKS 専用 GPU イメージを使用するため、新しいクラスターで GPU エージェント ノードの `--aks-custom-headers` フラグを使用します。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

通常の AKS イメージを使用してクラスターを作成する場合、カスタムの `--aks-custom-headers` タグを省略すると、そうすることができます。 下のように、もっと特化した GPU ノード プールの追加を選択することもできます。


### <a name="use-the-aks-specialized-gpu-image-on-existing-clusters-preview"></a>既存のクラスターで AKS 専用 GPU イメージ (プレビュー) を使用する

AKS 専用 GPU イメージを使用するように新しいノード プールを構成します。 AKS 専用 GPU イメージを使用するため、新しいノード プールで GPU エージェント ノードの `--aks-custom-headers` フラグを使用します。

```azurecli
az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

通常の AKS イメージを使用してノード プールを作成する場合、カスタムの `--aks-custom-headers` タグを省略すると、そうすることができます。 

> [!NOTE]
> GPU SKU に第 2 世代仮想マシンが必要な場合、次のように作成できます。
> ```azurecli
> az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6s_v2 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true
> ```

## <a name="confirm-that-gpus-are-schedulable"></a>GPU がスケジュール可能であることを確認する

AKS クラスターが作成されたら、Kubernetes で GPU がスケジュール可能であることを確認します。 まず、[kubectl get nodes][kubectl-get] コマンドを使用して、クラスター内のノードを一覧表示します。

```console
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   13m   v1.12.7
```

ここで、[kubectl describe node][kubectl-describe] コマンドを使用して、GPU がスケジュール可能であることを確認します。 *Capacity* セクションで、GPU は `nvidia.com/gpu:  1` と表示されているはずです。

次の抜粋された例では、*aks nodepool1 18821093 0* という名前のノードで GPU が使用できることを示しています。

```console
$ kubectl describe node aks-nodepool1-28993262-0

Name:               aks-nodepool1-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 attachable-volumes-azure-disk:  24
 cpu:                            6
 ephemeral-storage:              101584140Ki
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         57713784Ki
 nvidia.com/gpu:                 1
 pods:                           110
Allocatable:
 attachable-volumes-azure-disk:  24
 cpu:                            5916m
 ephemeral-storage:              93619943269
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         51702904Ki
 nvidia.com/gpu:                 1
 pods:                           110
System Info:
 Machine ID:                 b0cd6fb49ffe4900b56ac8df2eaa0376
 System UUID:                486A1C08-C459-6F43-AD6B-E9CD0F8AEC17
 Boot ID:                    f134525f-385d-4b4e-89b8-989f3abb490b
 Kernel Version:             4.15.0-1040-azure
 OS Image:                   Ubuntu 16.04.6 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.12.7
 Kube-Proxy Version:         v1.12.7
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-28993262-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                     CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                                     ------------  ----------  ---------------  -------------  ---
  kube-system                nvidia-device-plugin-daemonset-bbjlq     0 (0%)        0 (0%)      0 (0%)           0 (0%)         2m39s

[...]
```

## <a name="run-a-gpu-enabled-workload"></a>GPU 対応ワークロードの実行

GPU が機能していることを確認するには、適切なリソース要求を指定して GPU 対応ワークロードをスケジュールします。 この例では、[MNIST データセット](http://yann.lecun.com/exdb/mnist/)に対して [Tensorflow](https://www.tensorflow.org/) ジョブを実行します。

*samples-tf-mnist-demo.yaml* という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。 次のジョブ マニフェストには `nvidia.com/gpu: 1` のリソース制限が含まれています。

> [!NOTE]
> ドライバーを呼び出すときに、CUDA ドライバーのバージョンが CUDA ランタイムのバージョンに対して不十分であるなどのバージョン不一致エラーが発生した場合は、NVIDIA ドライバーのマトリックス互換性チャート ([https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)) を確認してください。

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    app: samples-tf-mnist-demo
  name: samples-tf-mnist-demo
spec:
  template:
    metadata:
      labels:
        app: samples-tf-mnist-demo
    spec:
      containers:
      - name: samples-tf-mnist-demo
        image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
```

[kubectl apply][kubectl-apply] コマンドを使ってジョブを実行します。 このコマンドは、マニフェスト ファイルを解析し、定義されている Kubernetes オブジェクトを作成します。

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>GPU 対応ワークロードの状態と出力の表示

`--watch` 引数を指定して [kubectl get jobs][kubectl-get] コマンドを使用し、ジョブの進行状況を監視します。 イメージを最初にプルし、データセットを処理するまで数分かかる可能性があります。 *COMPLETIONS* 列に *1/1* と表示されている場合、ジョブは正常に完了しました。 *Ctrl-C* を使用して `kubetctl --watch` コマンドを終了します。

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

GPU 対応ワークロードの出力を確認するには、まず [kubectl get pods][kubectl-get] コマンドでポッドの名前を取得します。

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

ここで、[kubectl logs][kubectl-logs] コマンドを使用して、ポッド ログを表示します。 次のポッド ログの例では、適切な GPU デバイスが検出されたことを確認します (`Tesla K80`)。 独自のポッドの名前を指定します。

```console
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-05-16 16:08:31.258328: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-05-16 16:08:31.396846: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties: 
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 2fd7:00:00.0
totalMemory: 11.17GiB freeMemory: 11.10GiB
2019-05-16 16:08:31.396886: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 2fd7:00:00.0, compute capability: 3.7)
2019-05-16 16:08:36.076962: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.1081
Accuracy at step 10: 0.7457
Accuracy at step 20: 0.8233
Accuracy at step 30: 0.8644
Accuracy at step 40: 0.8848
Accuracy at step 50: 0.8889
Accuracy at step 60: 0.8898
Accuracy at step 70: 0.8979
Accuracy at step 80: 0.9087
Accuracy at step 90: 0.9099
Adding run metadata for 99
Accuracy at step 100: 0.9125
Accuracy at step 110: 0.9184
Accuracy at step 120: 0.922
Accuracy at step 130: 0.9161
Accuracy at step 140: 0.9219
Accuracy at step 150: 0.9151
Accuracy at step 160: 0.9199
Accuracy at step 170: 0.9305
Accuracy at step 180: 0.9251
Accuracy at step 190: 0.9258
Adding run metadata for 199
Accuracy at step 200: 0.9315
Accuracy at step 210: 0.9361
Accuracy at step 220: 0.9357
Accuracy at step 230: 0.9392
Accuracy at step 240: 0.9387
Accuracy at step 250: 0.9401
Accuracy at step 260: 0.9398
Accuracy at step 270: 0.9407
Accuracy at step 280: 0.9434
Accuracy at step 290: 0.9447
Adding run metadata for 299
Accuracy at step 300: 0.9463
Accuracy at step 310: 0.943
Accuracy at step 320: 0.9439
Accuracy at step 330: 0.943
Accuracy at step 340: 0.9457
Accuracy at step 350: 0.9497
Accuracy at step 360: 0.9481
Accuracy at step 370: 0.9466
Accuracy at step 380: 0.9514
Accuracy at step 390: 0.948
Adding run metadata for 399
Accuracy at step 400: 0.9469
Accuracy at step 410: 0.9489
Accuracy at step 420: 0.9529
Accuracy at step 430: 0.9507
Accuracy at step 440: 0.9504
Accuracy at step 450: 0.951
Accuracy at step 460: 0.9512
Accuracy at step 470: 0.9539
Accuracy at step 480: 0.9533
Accuracy at step 490: 0.9494
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事で作成され、関連付けられている Kubernetes オブジェクトを削除するには、次のように [kubectl delete job][kubectl delete] コマンドを使用します。

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="next-steps"></a>次のステップ

Apache Spark ジョブを実行するには、[AKS での Apache Spark ジョブの実行][aks-spark]に関する記事を参照してください。

Kubernetes での機械学習 (ML) ワークロードの実行に関する詳細については、[Kubeflow ラボ][kubeflow-labs]を参照してください。

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[azure-pricing]: https://azure.microsoft.com/pricing/
[azure-availability]: https://azure.microsoft.com/global-infrastructure/services/
[nvidia-github]: https://github.com/NVIDIA/k8s-device-plugin

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
