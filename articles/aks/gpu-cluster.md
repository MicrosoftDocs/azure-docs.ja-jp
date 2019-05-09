---
title: Azure Kubernetes Service (AKS) での GPU の使用
description: Azure Kubernetes Service (AKS) で高パフォーマンス コンピューティングやグラフィックを集中的に使用するワークロードに GPU を使用する方法について説明します
services: container-service
author: zr-msft
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/28/2019
ms.author: zarhoads
ms.openlocfilehash: 150eaa6a4df558ed0c737d99cbcc8010baf63e96
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758242"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でコンピューティングを集中的に使用するワークロードに GPU を使用する

GPU (Graphical processing units) は、多くの場合に、グラフィックや視覚化ワークロードなど、コンピューティングを集中的に使用するワークロードに使用とされます。 AKS では、Kubernetes でこれらのコンピューティングを集中的に使用するワークロードを実行する GPU 対応ノード プールの作成をサポートしています。 使用可能な GPU 対応 VM の詳細については、[Azure での GPU 最適化済み VM サイズ][gpu-skus]に関する記事を参照してください。 AKS ノードには、最小サイズの *Standard_NC6* をお勧めします。

> [!NOTE]
> GPU 対応 VM には、より高い価格が適用され、利用可能なリージョンが限られる特殊なハードウェアが含まれます。 詳細については、[価格][azure-pricing]ツールと[利用可能なリージョン][azure-availability]を参照してください。

## <a name="before-you-begin"></a>開始する前に

この記事は、GPU をサポートするノードを含む AKS クラスターが既に存在していることを前提としています。 AKS クラスターで Kubernetes 1.10 以降を実行している必要があります。 これらの要件を満たす AKS クラスターが必要な場合は、この記事の最初のセクションを参照して、[AKS クラスターを作成](#create-an-aks-cluster)してください。

また、Azure CLI バージョン 2.0.59 以降がインストールされ、構成されている必要もあります。 バージョンを確認するには、 `az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「 [Azure CLI のインストール][install-azure-cli]」を参照してください。

## <a name="create-an-aks-cluster"></a>AKS クラスターの作成

最小要件 (GPU 対応ノードと Kubernetes バージョン 1.10 以降) を満たしている AKS クラスターが必要な場合は、次の手順を実行します。 これらの要件を満たしている AKS クラスターが既にある場合は、[次のセクションに進んでください](#confirm-that-gpus-are-schedulable)。

まず、[az group create][az-group-create] コマンドを使用して、クラスターのリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* リージョンに作成します。

```azurecli
az group create --name myResourceGroup --location eastus
```

ここで、[az aks create][az-aks-create] コマンドを使用して、AKS クラスターを作成します。 次の例では、サイズ `Standard_NC6` の単一のノードを含むクラスターを作成し、Kubernetes バージョン 1.11.7 を実行します。

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1 \
    --kubernetes-version 1.11.8
```

[az aks get-credentials][az-aks-get-credentials] コマンドを使用して、AKS クラスターの資格情報を取得します。

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="confirm-that-gpus-are-schedulable"></a>GPU がスケジュール可能であることを確認する

AKS クラスターが作成されたら、Kubernetes で GPU がスケジュール可能であることを確認します。 まず、[kubectl get nodes][ kubectl-get] コマンドを使用して、クラスター内のノードを一覧表示します。

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   6m    v1.11.7
```

ここで、[kubectl describe node][kubectl-describe] コマンドを使用して、GPU がスケジュール可能であることを確認します。 *Capacity* セクションで、GPU は `nvidia.com/gpu:  1` と表示されているはずです。 GPU が表示されていない場合は、「[GPU の可用性のトラブルシューティング](#troubleshoot-gpu-availability)」を参照してください。

次の抜粋された例では、*aks nodepool1 18821093 0* という名前のノードで GPU が使用できることを示しています。

```
$ kubectl describe node aks-nodepool1-28993262-0

Name:               aks-nodepool1-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 cpu:                6
 ephemeral-storage:  30428648Ki
 hugepages-1Gi:      0
 hugepages-2Mi:      0
 memory:             57713780Ki
 nvidia.com/gpu:     1
 pods:               110
Allocatable:
 cpu:                5916m
 ephemeral-storage:  28043041951
 hugepages-1Gi:      0
 hugepages-2Mi:      0
 memory:             52368500Ki
 nvidia.com/gpu:     1
 pods:               110
System Info:
 Machine ID:                 9148b74152374d049a68436ac59ee7c7
 System UUID:                D599728C-96F3-B941-BC79-E0B70453609C
 Boot ID:                    a2a6dbc3-6090-4f54-a2b7-7b4a209dffaf
 Kernel Version:             4.15.0-1037-azure
 OS Image:                   Ubuntu 16.04.5 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.11.7
 Kube-Proxy Version:         v1.11.7
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-28993262-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                     CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                                     ------------  ----------  ---------------  -------------  ---
  gpu-resources              nvidia-device-plugin-97zfc               0 (0%)        0 (0%)      0 (0%)           0 (0%)         2m4s

[...]
```

## <a name="run-a-gpu-enabled-workload"></a>GPU 対応ワークロードの実行

GPU が機能していることを確認するには、適切なリソース要求を指定して GPU 対応ワークロードをスケジュールします。 この例では、[MNIST データセット](http://yann.lecun.com/exdb/mnist/)に対して [Tensorflow](https://www.tensorflow.org/versions/r1.1/get_started/mnist/beginners) ジョブを実行します。

*samples-tf-mnist-demo.yaml* という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。 次のジョブ マニフェストには `nvidia.com/gpu: 1` のリソース制限が含まれています。

> [!NOTE]
> ドライバーを呼び出すときに、CUDA ドライバーのバージョンが CUDA ランタイムのバージョンに対して不十分であるなどのバージョン不一致エラーが発生した場合は、nVidia ドライバーのマトリックス互換性チャート ([https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)) を確認してください。

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
        image: microsoft/samples-tf-mnist-demo:gpu
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

`--watch` 引数を指定して [kubectl get jobs][kubectl-get] コマンドを使用し、ジョブの進行状況を監視します。 イメージを最初にプルし、データセットを処理するまで数分かかる可能性があります。 *COMPLETIONS* 列に *1/1* と表示されている場合、ジョブが正常に完了しています。

```
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

GPU 対応ワークロードの出力を確認するには、まず [kubectl get pods][kubectl-get] コマンドでポッドの名前を取得します。

```
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-smnr6   0/1     Completed   0          3m
```

ここで、[kubectl logs][kubectl-logs] コマンドを使用して、ポッド ログを表示します。 次のポッド ログの例では、適切な GPU デバイスが検出されたことを確認します (`Tesla K80`)。 独自のポッドの名前を指定します。

```
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-02-28 23:47:34.749013: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-02-28 23:47:34.879877: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 3130:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2019-02-28 23:47:34.879915: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 3130:00:00.0, compute capability: 3.7)
2019-02-28 23:47:39.492532: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
Accuracy at step 40: 0.8685
Accuracy at step 50: 0.8864
Accuracy at step 60: 0.901
Accuracy at step 70: 0.905
Accuracy at step 80: 0.9103
Accuracy at step 90: 0.9126
Adding run metadata for 99
Accuracy at step 100: 0.9176
Accuracy at step 110: 0.9149
Accuracy at step 120: 0.9187
Accuracy at step 130: 0.9253
Accuracy at step 140: 0.9252
Accuracy at step 150: 0.9266
Accuracy at step 160: 0.9255
Accuracy at step 170: 0.9267
Accuracy at step 180: 0.9257
Accuracy at step 190: 0.9309
Adding run metadata for 199
Accuracy at step 200: 0.9272
Accuracy at step 210: 0.9321
Accuracy at step 220: 0.9343
Accuracy at step 230: 0.9388
Accuracy at step 240: 0.9408
Accuracy at step 250: 0.9394
Accuracy at step 260: 0.9412
Accuracy at step 270: 0.9422
Accuracy at step 280: 0.9436
Accuracy at step 290: 0.9411
Adding run metadata for 299
Accuracy at step 300: 0.9426
Accuracy at step 310: 0.9466
Accuracy at step 320: 0.9458
Accuracy at step 330: 0.9407
Accuracy at step 340: 0.9445
Accuracy at step 350: 0.9486
Accuracy at step 360: 0.9475
Accuracy at step 370: 0.948
Accuracy at step 380: 0.9516
Accuracy at step 390: 0.9534
Adding run metadata for 399
Accuracy at step 400: 0.9501
Accuracy at step 410: 0.9552
Accuracy at step 420: 0.9535
Accuracy at step 430: 0.9545
Accuracy at step 440: 0.9533
Accuracy at step 450: 0.9526
Accuracy at step 460: 0.9566
Accuracy at step 470: 0.9547
Accuracy at step 480: 0.9548
Accuracy at step 490: 0.9545
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この記事で作成され、関連付けられている Kubernetes オブジェクトを削除するには、次のように [kubectl delete job][kubectl delete] コマンドを使用します。

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="troubleshoot-gpu-availability"></a>GPU の可用性のトラブルシューティング

ノードで使用可能な GPU が表示されない場合は、nVidia デバイス プラグイン用の DaemonSet をデプロイする必要がある場合があります。 この DaemonSet により、各ノードでポッドが実行され、GPU に必要なドライバーが提供されます。

まず、[kubectl create namespace][kubectl-create] コマンドを使用して、名前空間 (*gpu-resources* など) を作成します。

```console
kubectl create namespace gpu-resources
```

*nvidia-device-plugin-ds.yaml* という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。 マニフェストの中間にある `image: nvidia/k8s-device-plugin:1.11` を、使用している Kubernetes バージョンと一致するように更新します。 たとえば、ご自分の AKS クラスター内で、Kubernetes バージョン 1.12 を実行している場合、タグを `image: nvidia/k8s-device-plugin:1.12` に更新します。

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  labels:
    kubernetes.io/cluster-service: "true"
  name: nvidia-device-plugin
  namespace: gpu-resources
spec:
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
      containers:
      - image: nvidia/k8s-device-plugin:1.11 # Update this tag to match your Kubernetes version
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
      nodeSelector:
        beta.kubernetes.io/os: linux
        accelerator: nvidia
```

ここで [kubectl apply][kubectl-apply] コマンドを使用して DaemonSet を作成します。

```
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

[kubectl describe node][kubectl-describe] コマンドを再度実行して、ノードで GPU が使用できるようになったことを確認します。

## <a name="next-steps"></a>次の手順

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

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/linux/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
