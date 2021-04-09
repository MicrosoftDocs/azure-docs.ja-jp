---
title: Kubernetes のデプロイのために Azure Stack Edge デバイス上でコンピューティング アクセラレーション GPU または VPU を使用する| Microsoft Docs
description: Kubernetes のデプロイのために、Azure Stack Edge Pro GPU、Azure Stack Edge Pro R、または Azure Stack Edge Mini Ri 上でコンピューティング アクセラレーション GPU または VPU を使用する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 3eb648af60a7be62d08f6b172347778d2358643c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102440124"
---
# <a name="use-compute-acceleration-on-azure-stack-edge-pro-gpu-for-kubernetes-deployment"></a>Kubernetes のデプロイのために Azure Stack Edge Pro GPU でコンピューティング アクセラレーションを使用する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Kubernetes のデプロイを使用するときに Azure Stack Edge デバイス上でコンピューティング アクセラレーションを使用する方法について説明します。 この記事は、Azure Stack Edge Pro GPU、Azure Stack Edge Pro R、および Azure Stack Edge Mini R デバイスに適用されます。


## <a name="about-compute-acceleration"></a>コンピューティング アクセラレーションについて 

中央処理装置 (CPU) は、コンピューター上で実行するほとんどのプロセスに対する既定の汎用コンピューティングです。 多くの場合、一部の機能については、CPU のソフトウェアで実行するよりも、特殊なコンピューター ハードウェアを使用して実行した方が効率が良くなります。 たとえば、グラフィックス処理装置 (GPU) を使用して、ピクセル データの処理を高速化することができます。  

コンピューティング アクセラレーションとは、グラフィカル処理ユニット (GPU)、Vision Processing Unit (VPU)、または Field Programmable Gate Array (FPGA) がハードウェアの高速化に使用される Azure Stack Edge デバイスの場合に特に使用される用語です。 Azure Stack Edge デバイス上にデプロイされるほとんどのワークロードでは、タイミングが重要で、カメラのストリームが複数あり、またはフレーム レートが大きいため、これらすべてで固有のハードウェア アクセラレータが必要になります。

この記事では、次のデバイスで GPU または VPU を使用したコンピューティング アクセラレーションのみについて説明します。

- **Azure Stack Edge Pro GPU** - これらのデバイスには、1 つまたは 2 つの Nvidia T4 Tensor Core GPU を搭載できます。 詳細については、「[NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/)」を参照してください。
- **Azure Stack Edge Pro R** - これらのデバイスには、1 つの Nvidia T4 Tensor Core GPU が搭載されています。 詳細については、「[NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/)」を参照してください。
- **Azure Stack Edge Mini R** - これらのデバイスには、1 つの Intel Movidius Myriad X VPU が搭載されています。 詳細については、「[Intel Movidius Myriad X VPU](https://www.movidius.com/MyriadX)」を参照してください。


## <a name="use-gpu-for-kubernetes-deployment"></a>Kubernetes のデプロイのために GPU を使用する

次の `yaml` の例は、Azure Stack Edge Pro GPU、または GPU を搭載した Azure Stack Edge Pro R デバイスで使用できます。

<!--In a production scenario, Pods are not used directly and these are wrapped around higher level constructs like Deployment, ReplicaSet which maintain the desired state in case of pod restarts, failures.-->

```yml
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  containers:
    - name: cuda-container
      image: nvidia/cuda:9.0-devel
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
    - name: digits-container
      image: nvidia/digits:6.0
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
```


## <a name="use-vpu-for-kubernetes-deployment"></a>Kubernetes のデプロイのために VPU を使用する

次の `yaml` の例は、VPU を搭載した Azure Stack Edge Mini R デバイスで使用できます。

```yml
apiVersion: batch/v1
kind: Job
metadata:
 name: intelvpu-demo-job
 labels:
   jobgroup: intelvpu-demo
spec:
 template:
   metadata:
     labels:
       jobgroup: intelvpu-demo
   spec:
     restartPolicy: Never
     containers:
       -
         name: intelvpu-demo-job-1
         image: ubuntu-demo-openvino:devel
         imagePullPolicy: IfNotPresent
         command: [ "/do_classification.sh" ]
         resources:
           limits:
             vpu.intel.com/hddl: 1
```


## <a name="next-steps"></a>次の手順

[kubectl を使用し、Azure Stack Edge Pro GPU デバイスで PersistentVolume を使って Kubernetes ステートフル アプリケーションを実行する](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md)方法について説明します。
