---
title: Azure Stack Edge Pro GPU デバイスで実行するように FPGA デバイスの IoT Edge モジュールを変更する
description: 既存の FPGA デバイス上の既存の IoT Edge モジュールを Azure Stack Edge Pro GPU デバイスで実行するために必要な変更について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 660fbf7cc4dd28c800d8f49fd5d990c99f97c4c8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102442997"
---
# <a name="run-existing-iot-edge-modules-from-azure-stack-edge-pro-fpga-devices-on-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro FPGA デバイスの既存の IoT Edge モジュールを Azure Stack Edge Pro GPU デバイスで実行する

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

この記事では、Azure Stack Edge Pro FPGA で実行されている Docker ベースの IoT Edge モジュールを、Azure Stack Edge Pro GPU デバイス上の Kubernetes ベースの IoT Edge プラットフォームで実行できるようにするために必要な変更について、詳しく説明します。 

## <a name="about-iot-edge-implementation"></a>IoT Edge の実装について 

IoT Edge の実装は、Azure Stack Edge Pro FPGA デバイス上と Azure Stack Edge Pro GPU デバイス上では異なります。 GPU デバイスの場合、IoT Edge のホスティング プラットフォームとして Kubernetes が使用されます。 FPGA デバイス上の IoT Edge には、Docker ベースのプラットフォームが使用されます。 IoT Edge Docker ベースのアプリケーション モデルは、Kubernetes ネイティブのアプリケーション モデルに自動的に変換されます。 ただし、サポートされているのは Kubernetes アプリケーション モデルの小さなサブセットのみであるため、いくつかの変更がやはり必要になる場合があります。

ワークロードを FPGA デバイスから GPU デバイスに移行する場合、Kubernetes プラットフォームで既存の IoT Edge モジュールを正常に動作させるには、それらに対して変更を行う必要があります。 ストレージ、ネットワーク、リソース使用、Web プロキシについて、異なる要件を指定することが必要になる場合があります。 

## <a name="storage"></a>ストレージ

IoT Edge モジュールのストレージを指定する場合は、次の情報を考慮します。

- Kubernetes 上のコンテナーのストレージは、ボリューム マウントを使用して指定します。
- Kubernetes へのデプロイで、永続的ストレージまたはホスト パスを関連付けるためのバインドを指定することはできません。
    - 永続的ストレージの場合は、種類が `volume` の `Mounts` を使用します。
    - ホスト パスの場合は、種類が `bind` の `Mounts` を使用します。
- Kubernetes 上の IoT Edge の場合、`Mounts` によるバインドはディレクトリに対してのみ機能し、ファイルには機能しません。

#### <a name="example---storage-via-volume-mounts"></a>例 - ボリューム マウントによるストレージ 

Docker 上の IoT Edge の場合、デバイス上の共有をコンテナー内のパスにマップするには、ホスト パス バインドが使用されます。 次に示すのは、FPGA デバイスで使用されるコンテナー作成オプションです。

```
{
  "HostConfig": 
  {
   "Binds": 
    [
     "<Host storage path for Edge local share>:<Module storage path>"
    ]
   }
}
```
<!-- is this how it will look on GPU device?-->
次に示すのは、Kubernetes 上の IoT Edge へのホスト パスで、種類 `bind` の `Mounts` を使用する例です。
```
{
    "HostConfig": {
        "Mounts": [
            {
                "Target": "<Module storage path>",
                "Source": "<Host storage path>",
                "Type": "bind"
            }
        ]
    }
}
```


<!--following example is for persistent storage where we use mounts w/ type volume-->

Kubernetes 上で IoT Edge を実行している GPU デバイスの場合、ストレージを指定するにはボリューム マウントを使用します。 共有を使用してストレージをプロビジョニングする場合、`Mounts.Source` の値は、GPU デバイスにプロビジョニングされた SMB または NFS 共有の名前になります。 `/home/input` は、コンテナー内でボリュームにアクセスできるパスです。 次に示すのは、GPU デバイスで使用されるコンテナー作成オプションです。

```
{
    "HostConfig": {
        "Mounts": [
        {
            "Target": "/home/input",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        },
        {
            "Target": "/home/output",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        }]
    }
}
```



## <a name="network"></a>ネットワーク

IoT Edge モジュールのネットワークを指定する場合は、次の情報を考慮します。 

- クラスターの内部と外部の両方でサービスを公開するには、`HostPort` を指定する必要があります。
    - サービスの公開をクラスターのみに限定するための K8sExperimental オプション。
- モジュール間通信には、`HostPort` の指定と、マップされたポートを使用した (そして、コンテナーで公開されたポートを使用しない) 接続が必要です。
- ホスト ネットワークは `dnsPolicy = ClusterFirstWithHostNet` で動作するので、すべてのコンテナー (特に `edgeHub`) もホスト ネットワーク上に存在する必要はありません。 <!--Need further clarifications on this one-->
- TCP へのポート マッピングを追加すると、同じ要求で UDP は機能しません。

#### <a name="example---external-access-to-modules"></a>例 - モジュールへの外部アクセス 

ポート バインドを指定する IoT Edge モジュールの場合、デバイスのローカル UI で指定された Kubernetes の外部サービス IP 範囲を使用して、IP アドレスが割り当てられます。 次の例で示すように、Docker 上の IoT Edge と Kubernetes 上の IoT Edge の間で、コンテナー作成オプションに変更はありません。  

```json 
{
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
        }
    }
}
```

ただし、モジュールに割り当てられた IP アドレスを照会するには、「[サービスまたはモジュールの IP アドレスを取得する](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules)」で説明されているように、Kubernetes ダッシュボードを使用できます。 

または、[デバイスの PowerShell インターフェイスに接続](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)し、`iotedge` リスト コマンドを使用して、デバイスで実行されているすべてのモジュールの一覧を表示することもできます。 [コマンドの出力](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge)では、モジュールに関連付けられている外部 IP も示されます。


## <a name="resource-usage"></a>リソースの使用状況 

GPU デバイス上の Kubernetes ベースの IoT Edge のセットアップでは、ハードウェアの高速化、メモリ、CPU の要件などのリソースの指定が、FPGA デバイスのものとは異なります。 

#### <a name="compute-acceleration-usage"></a>コンピューティング アクセラレーションの使用

FPGA にモジュールをデプロイするには、コンテナー作成オプションを使用します。 <!--with Device Bindings--> 次のように構成します。 <!--not sure where are device bindings in this config--> 

```json
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "microsoft.com/fpga_catapult": 2
        },
        "requests": {
        "microsoft.com/fpga_catapult": 2
        }
    }
    },
    "Env": [
    "WIRESERVER_ADDRESS=10.139.218.1"
    ]
}
``` 

    
<!--Note: The IP address assigned to your FPGA module's service can be used to send inferencing requests from outside the cluster OR your ML module can be used along with DBE Simple Module Flow by passing files to the module using an input share.-->
    
GPU の場合は、次の最小構成で示すように、デバイスのバインドではなくリソース要求の指定を使用します。 catapult の代わりに nvidia リソースを要求し、`wireserver` を指定する必要はありません。 

```json     
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "nvidia.com/gpu": 2
        }    
    }
}
```

#### <a name="memory-and-cpu-usage"></a>メモリと CPU の使用
 
メモリと CPU の使用を設定するには、`k8s-experimental` セクションのモジュールに対するプロセッサの制限を使用します。 <!--can we verify if this is how we set limits of memory and CPU-->

```json
    "k8s-experimental": {
    "resources": {
        "limits": {
            "memory": "128Mi",
            "cpu": "500m",
            "nvidia.com/gpu": 2
        },
        "requests": {
            "nvidia.com/gpu": 2
        }
}
```
メモリと CPU の指定は必須ではありませんが、一般的には推奨されます。 `requests` を指定しないと、limits で設定されている値が最低限必要な値として使用されます。 

モジュールでの共有メモリの使用にも、別の方法が必要です。 たとえば、「[Live Video Analytics を Azure Stack Edge にデプロイする](../media-services/live-video-analytics-edge/deploy-azure-stack-edge-how-to.md#deploy-live-video-analytics-edge-module-using-azure-portal)」で説明されているように、Live Video Analytics と推論ソリューションの間の共有メモリ アクセスには、ホスト IPC モードを使用できます。


## <a name="web-proxy"></a>Web プロキシ 

Web プロキシを構成するときは、次の情報を考慮します。

ネットワークに Web プロキシが構成されている場合は、FPGA デバイス上の Docker ベースの IoT Edge のセットアップで、`edgeHub` のデプロイ用に次の環境変数を構成します。

- `https_proxy : <proxy URL>`
- `UpstreamProtocol : AmqpWs` (Web プロキシで `Amqp` トラフィックが許可されていない場合)

GPU デバイス上の Kubernetes ベースの IoT Edge のセットアップでは、デプロイの間に次の変数を追加で構成する必要があります。

- `no_proxy`: localhost

- Kubernetes プラットフォーム上の IoT Edge プロキシには、ポート 35000 と 35001 が使用されます。 モジュールがこれらのポートで実行されていないことをご確認ください。そうしないと、ポートの競合が発生するおそれがあります。 

## <a name="other-differences"></a>その他の相違点

- **デプロイ戦略**: 場合によっては、モジュールに対する更新プログラムのデプロイ動作を変更する必要があります。 IoT Edge モジュールの既定の動作はローリング アップデートです。 この動作により、モジュールでハードウェア アクセラレータやネットワーク ポートなどのリソースが使用されている場合、更新されたモジュールが再起動しなくなります。 この動作により、GPU デバイス用の Kubernetes プラットフォームで永続ボリュームを処理するときに、予期しない影響が発生するおそれがあります。 この既定の動作をオーバーライドするには、モジュールの `k8s-experimental` セクションで `Recreate` を指定します。

    ```    
    {
      "k8s-experimental": {
        "strategy": {
          "type": "Recreate"
        }
      }
    }
    ```

- **モジュール名**: モジュール名は、Kubernetes の名前付け規則に従う必要があります。 Docker を使用して IoT Edge で実行されているモジュールを、Kubernetes を使用する IoT Edge に移動するときは、モジュール名の変更が必要になる場合があります。 名前付けの詳細については、[Kubernetes の名前付け規則](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/)に関するページを参照してください。
- **その他のオプション**: 
    - FPGA デバイスで動作する特定の Docker 作成オプションは、GPU デバイスの Kubernetes 環境では機能しません。 例: , like – EntryPoint。<!--can we confirm what exactly is required here-->
    - `:` などの環境変数は、`__` で置き換える必要があります。
    - Kubernetes ポッドの **コンテナー作成中** 状態は、IoT Hub リソースのモジュールでは **バックオフ** 状態になります。 ポッドがこの状態になる理由は多々ありますが、一般的な理由は、大きいコンテナー イメージが低ネットワーク帯域幅の接続を通してプルされている場合です。 ポッドがこの状態になると、モジュールは起動中ですが、IoT Hub ではモジュールの状態が **バックオフ** と表示されます。


## <a name="next-steps"></a>次のステップ

- [モジュールを使用するために GPU を構成する](azure-stack-edge-j-series-configure-gpu-modules.md)方法を確認します。
