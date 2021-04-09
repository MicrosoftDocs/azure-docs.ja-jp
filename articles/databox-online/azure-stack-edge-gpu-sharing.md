---
title: Azure Stack Edge Pro GPU デバイスでの GPU 共有
description: Azure Stack Edge Pro GPU デバイスで GPU を共有する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 6683e39cfa3601b1ae1fbbe02e69e4dc0a54e8e7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564633"
---
# <a name="gpu-sharing-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイスでの GPU 共有

グラフィックス処理装置 (GPU) は、グラフィックス レンダリングを高速化するために設計された特殊なプロセッサです。 GPU によって多くのデータを同時に処理できるため、機械学習、ビデオ編集、およびゲーム アプリケーションに役立ちます。 汎用コンピューティング用の CPU に加えて、Azure Stack Edge Pro GPU デバイスには、ハードウェア アクセラレータによる推論などのコンピューティング集中型のワークロードに対して、1 つまたは 2 つの Nvidia Tesla T4 GPU を搭載できます。 詳細については、[Nvidia の Tesla T4 GPU](https://www.nvidia.com/data-center/tesla-t4/) に関するページを参照してください。


## <a name="about-gpu-sharing"></a>GPU 共有について

多くの機械学習やその他のコンピューティング ワークロードは、専用 GPU を必要としない場合があります。 GPU は共有できるため、コンテナー化またはVM ワークロード間で GPU を共有することで、GPU のパフォーマンス上の利点に大きな影響を与えることなく、GPU 使用率を高めることができます。  

## <a name="using-gpu-with-vms"></a>VM での GPU の使用

Azure Stack Edge Pro デバイスでは、VM ワークロードをデプロイするときに GPU を共有できません。 GPU は 1 つの VM にのみマップできます。 これは、1 つの GPU があるデバイスには 1 つの GPU VM、2 つの GPU が搭載されているデバイスには 2 つの VM のみ持つことができることを意味します。 コンテナー化されたワークロード用に Kubernetes が構成されたデバイスで GPU VM を使用する場合も、考慮する必要がある他の要因があります。 詳細については、「[GPU VM と Kubernetes](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#gpu-vms-and-kubernetes)」を参照してください。


## <a name="using-gpu-with-containers"></a>コンテナーでの GPU の使用

コンテナー化されたワークロードをデプロイする場合、ハードウェアとソフトウェア レイヤーで、複数の方法で GPU を共有できます。 Azure Stack Edge Pro デバイス上の Tesla T4 GPU では、ソフトウェアの共有に限定されます。 デバイスでは、GPU のソフトウェア共有に関して次の 2 つの方法が使用されます。 

- 最初の方法では、環境変数を使用して、時間を共有できる GPU の数を指定します。 この方法を使用する場合は、次の点に注意してください。

    - この方法では、GPU を 1 つまたは両方指定するか、まったく指定しないかのいずれかです。 部分的な使用を指定することはできません。
    - 複数のモジュールを 1 つの GPU にマップできますが、同じモジュールを複数の GPU にマップすることはできません。
    - Nvidia SMI 出力を使用すると、メモリ使用率を含む全体的な GPU 使用率を確認できます。
    
    詳細については、デバイス上に [GPU を使用する IoT Edge モジュールをデプロイする](azure-stack-edge-gpu-configure-gpu-modules.md)方法を参照してください。

- 2 番目の方法では、Nvidia GPU でマルチプロセス サービスを有効にする必要があります。 MPS は、CUDA を使用する複数のプロセスを 1 つの共有 GPU で同時に実行できるようにするランタイム サービスです。 MPS を使用すると、GPU 上のさまざまなプロセスからのカーネルと memcopy の操作を重複させることで、最大使用率を実現できます。 詳細については、「[マルチプロセス サービス](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf)」を参照してください。

    この方法を使用する場合は、次の点に注意してください。
    
    - MPS を使用すると、GPU デプロイでより多くのフラグを指定できます。
    - MPS による部分的な使用を指定することで、デバイスにデプロイされた各アプリケーションの使用量を制限できます。 `deployment.yaml` の `env` セクションに次のパラメーターを追加すると、各アプリに使用する GPU の割合を指定できます。 

    ```yml
    // Example: application wants to limit gpu percentage to 20%
    
        env:
              - name: CUDA_MPS_ACTIVE_THREAD_PERCENTAGE 
                value: "20"    
    ```

## <a name="gpu-utilization"></a>GPU 使用率
 
デバイスにデプロイされたコンテナー化されたワークロードで GPU を共有する場合、Nvidia システム管理インターフェイス (nvidia-smi) を使用できます。 Nvidia-smi は、Nvidia GPU デバイスの管理と監視に役立つコマンドライン ユーティリティです。 詳細については、「[Nvidia システム管理インターフェイス](https://developer.nvidia.com/nvidia-system-management-interface)」を参照してください。

GPU 使用率を表示するには、まずデバイスの PowerShell インターフェイスに接続します。 `Get-HcsNvidiaSmi` コマンドを実行し、Nvidia SMI 出力を表示します。 また、MPS を有効にしてからデバイスに複数のワークロードをデプロイすると、GPU 使用率がどのように変化するかを確認することもできます。 詳細については、[マルチプロセス サービスの有効化](azure-stack-edge-gpu-connect-powershell-interface.md#enable-multi-process-service-mps)に関する記事を参照してください。


## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge Pro での Kubernetes デプロイのための GPU 共有](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md)。
- [Azure Stack Edge Pro での IoT デプロイのための GPU 共有](azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing.md)。
