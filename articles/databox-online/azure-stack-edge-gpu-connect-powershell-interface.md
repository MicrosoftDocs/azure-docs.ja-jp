---
title: Microsoft Azure Stack Edge Pro GPU デバイスに Windows PowerShell インターフェイス経由で接続して管理する | Microsoft Docs
description: Azure Stack Edge Pro GPU に Windows PowerShell インターフェイス経由で接続して管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 580e5aab7b7ac1edcfee58345291afcb9eb0e977
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103562163"
---
# <a name="manage-an-azure-stack-edge-pro-gpu-device-via-windows-powershell"></a>Windows PowerShell を使用して Azure Stack Edge Pro GPU デバイスを管理する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro ソリューションにより、データを処理してネットワーク経由で Azure に送信できます。 この記事では、Azure Stack Edge Pro デバイスの構成と管理のタスクをいくつか説明します。 Azure portal、ローカル Web UI、または Windows PowerShell インターフェイスを使用してデバイスを管理できます。

この記事では、デバイスの PowerShell インターフェイスに接続する方法と、このインターフェイスを使用して実行できるタスクに重点を置いて説明します。 


## <a name="connect-to-the-powershell-interface"></a>PowerShell インターフェイスに接続する

[!INCLUDE [Connect to admin runspace](../../includes/azure-stack-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>サポート パッケージを作成する

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]


## <a name="view-device-information"></a>デバイス情報を表示する
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="view-gpu-driver-information"></a>GPU ドライバー情報を表示する

お使いのデバイスにコンピューティング ロールが構成されている場合は、PowerShell インターフェイス経由で GPU ドライバー情報を取得することもできます。 

1. [PowerShell インターフェイスに接続する](#connect-to-the-powershell-interface)。
2. `Get-HcsGpuNvidiaSmi` を使用して、お使いのデバイスの GPU ドライバー情報を取得します。

    このコマンドレットの使用例を次に示します。

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```
    このコマンドレットのサンプル出力からのドライバー情報をメモしておきます。

    ```powershell    
    +-----------------------------------------------------------------------------+    
    | NVIDIA-SMI 440.64.00    Driver Version: 440.64.00    CUDA Version: 10.2     |    
    |-------------------------------+----------------------+----------------------+    
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |    
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |    
    |===============================+======================+======================|    
    |   0  Tesla T4            On   | 000029CE:00:00.0 Off |                    0 |    
    | N/A   60C    P0    29W /  70W |   1539MiB / 15109MiB |      0%      Default |    
    +-------------------------------+----------------------+----------------------+    
    |   1  Tesla T4           On  | 0000AD50:00:00.0 Off |                    0 |
    | N/A   58C    P0    29W /  70W |    330MiB / 15109MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    ```

## <a name="enable-multi-process-service-mps"></a>マルチプロセス サービス (MPS) を有効にする

Nvidia GPU 上のマルチプロセス サービス (MPS) は、GPU を複数のジョブで共有できるメカニズムを提供します。ここで、各ジョブには、GPU のリソースのうち一定のパーセントが割り当てられます。 MPS は、Azure Stack Edge Pro GPU デバイスのプレビュー機能です。 デバイスで MPS を有効にするには、次の手順を実行します。

[!INCLUDE [Enable MPS](../../includes/azure-stack-edge-gateway-enable-mps.md)]


## <a name="reset-your-device"></a>デバイスをリセットする

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>コンピューティング ログを取得する

デバイスにコンピューティング ロールが構成されている場合は、PowerShell インターフェイス経由でコンピューティング ログを取得することもできます。

1. [PowerShell インターフェイスに接続する](#connect-to-the-powershell-interface)。
2. `Get-AzureDataBoxEdgeComputeRoleLogs` を使用してデバイスのコンピューティング ログを取得します。

    このコマンドレットの使用例を次に示します。

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection    
    ```

    このコマンドレットで使用されるパラメーターの説明を次に示します。
    - `Path`:コンピューティング ログ パッケージを作成する共有へのネットワーク パスを指定します。
    - `Credential`:ネットワーク共有のユーザー名を指定します。 このコマンドレットを実行するときには、共有のパスワードを指定する必要があります。
    - `FullLogCollection`:このパラメーターにより、ログ パッケージにすべてのコンピューティング ログが確実に含まれます。 既定では、ログ パッケージに含まれるものは、ログのサブセットのみです。


## <a name="change-kubernetes-pod-and-service-subnets"></a>Kubernetes ポッドとサービス サブネットの変更

既定では、Azure Stack Edge デバイス上の Kubernetes は、ポッドとサービスのサブネットとしてそれぞれ 172.27.0.0/16 と 172.28.0.0/16 を使用します。 これらのサブネットがネットワークで既に使用されている場合は、`Set-HcsKubeClusterNetworkInfo` コマンドレットを実行して変更できます。

この手順で Kubernetes クラスターが作成されて、Azure portal からコンピューティングを構成する前に、この構成を実行します。

1. [デバイスの PowerShell インターフェイスに接続します](#connect-to-the-powershell-interface)。
1. デバイスの PowerShell インターフェイスから、次のように実行します。

    `Set-HcsKubeClusterNetworkInfo -PodSubnet <subnet details> -ServiceSubnet <subnet details>`

    <subnet details> を、使用するサブネットの範囲に置き換えます。 

1. このコマンドの実行が完了したら、`Get-HcsKubeClusterNetworkInfo` コマンドを使用して、ポッドおよびサービスのサブネットが変更されたことを確認できます。

このコマンドの出力例を次に示します。

```powershell
[10.100.10.10]: PS>Set-HcsKubeClusterNetworkInfo -PodSubnet 10.96.0.1/16 -ServiceSubnet 10.97.0.1/16
[10.100.10.10]: PS>Get-HcsKubeClusterNetworkInfo

Id                                   PodSubnet    ServiceSubnet
--                                   ---------    -------------
6dbf23c3-f146-4d57-bdfc-76cad714cfd1 10.96.0.1/16 10.97.0.1/16
[10.100.10.10]: PS>
```

## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>IoT Edge に関連する Kubernetes の問題をデバッグする

開始する前に、以下が必要になります。

- コンピューティング ネットワークが構成済みである。 「[チュートリアル: GPU 搭載の Azure Stack Edge Pro 用のネットワークを構成する](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)」を参照してください。
- デバイスでコンピューティング ロールが構成済みである。
    
コンピューティング ロールが構成されている Azure Stack Edge Pro デバイスでは、2 つの異なるコマンド セットを利用してデバイスをトラブルシューティングしたり、監視したりできます。

- `iotedge` コマンドの使用。 これらのコマンドはデバイスの基本操作に利用できます。
- `kubectl` コマンドの使用。 これらのコマンドはデバイスのさまざまな操作に利用できます。

上記のコマンド セットのいずれかを実行するには、[PowerShell インターフェイスに接続する](#connect-to-the-powershell-interface)必要があります。

### <a name="use-iotedge-commands"></a>`iotedge` コマンドの使用

利用できるコマンドの一覧を表示するには、[PowerShell インターフェイスに接続し](#connect-to-the-powershell-interface)、`iotedge` 関数を使用します。

```powershell
[10.100.10.10]: PS>iotedge -?                                                                                                                           
Usage: iotedge COMMAND

Commands:
   list
   logs
   restart

[10.100.10.10]: PS>
```

次の表は、`iotedge` で利用できるコマンドの説明を簡単にまとめたものです。

|command  |説明 |
|---------|---------|
|`list`     | モジュールの一覧を表示する         |
|`logs`     | モジュールのログを取得する        |
|`restart`     | モジュールの停止と再開         |

#### <a name="list-all-iot-edge-modules"></a>すべての IoT Edge モジュールを一覧表示する

デバイスで実行されているすべてのモジュールを一覧表示するには、`iotedge list` コマンドを使用します。

このコマンドの出力例を次に示します。 このコマンドは、すべてのモジュール、関連付けられている構成、およびモジュールに関連付けられている外部 IP を一覧表示します。 たとえば、`https://10.128.44.244` で **Web サーバー** アプリにアクセスできます。 


```powershell
[10.100.10.10]: PS>iotedge list

NAME                   STATUS  DESCRIPTION CONFIG                                             EXTERNAL-IP
----                   ------  ----------- ------                                             -----
gettingstartedwithgpus Running Up 10 days  mcr.microsoft.com/intelligentedge/solutions:latest
iotedged               Running Up 10 days  azureiotedge/azureiotedge-iotedged:0.1.0-beta10    <none>
edgehub                Running Up 10 days  mcr.microsoft.com/azureiotedge-hub:1.0             10.128.44.243
edgeagent              Running Up 10 days  azureiotedge/azureiotedge-agent:0.1.0-beta10
webserverapp           Running Up 10 days  nginx:stable                                       10.128.44.244

[10.100.10.10]: PS>
```
#### <a name="restart-modules"></a>モジュールを再起動する

デバイス上で実行されているすべてのモジュールを一覧表示するには、`list` コマンドを使用します。 次に、再起動するモジュールの名前を特定し、それを `restart` コマンドで使用します。

モジュールを再起動する方法のサンプル出力を次に示します。 モジュールが実行されている期間の説明に基づいて、`cuda-sample1` が再起動されたことを確認できます。

```powershell
[10.100.10.10]: PS>iotedge list

NAME         STATUS  DESCRIPTION CONFIG                                          EXTERNAL-IP PORT(S)
----         ------  ----------- ------                                          ----------- -------
edgehub      Running Up 5 days   mcr.microsoft.com/azureiotedge-hub:1.0          10.57.48.62 443:31457/TCP,5671:308
                                                                                             81/TCP,8883:31753/TCP
iotedged     Running Up 7 days   azureiotedge/azureiotedge-iotedged:0.1.0-beta13 <none>      35000/TCP,35001/TCP
cuda-sample2 Running Up 1 days   nvidia/samples:nbody
edgeagent    Running Up 7 days   azureiotedge/azureiotedge-agent:0.1.0-beta13
cuda-sample1 Running Up 1 days   nvidia/samples:nbody

[10.100.10.10]: PS>iotedge restart cuda-sample1
[10.100.10.10]: PS>iotedge list

NAME         STATUS  DESCRIPTION  CONFIG                                          EXTERNAL-IP PORT(S)
----         ------  -----------  ------                                          ----------- -------
edgehub      Running Up 5 days    mcr.microsoft.com/azureiotedge-hub:1.0          10.57.48.62 443:31457/TCP,5671:30
                                                                                              881/TCP,8883:31753/TC
                                                                                              P
iotedged     Running Up 7 days    azureiotedge/azureiotedge-iotedged:0.1.0-beta13 <none>      35000/TCP,35001/TCP
cuda-sample2 Running Up 1 days    nvidia/samples:nbody
edgeagent    Running Up 7 days    azureiotedge/azureiotedge-agent:0.1.0-beta13
cuda-sample1 Running Up 4 minutes nvidia/samples:nbody

[10.100.10.10]: PS>

```

#### <a name="get-module-logs"></a>モジュールのログを取得する

デバイス上で実行されている IoT Edge モジュールに関するログを取得するには、`logs` コマンドを使用します。 

コンテナー イメージの作成中またはイメージのプル中にエラーが発生した場合、`logs edgeagent` を実行します。 `edgeagent` は、他のコンテナーのプロビジョニングを担う IoT Edge ランタイム コンテナーです。 `logs edgeagent` を実行するとすべてのログがダンプされるため、最近のエラーを表示する方法としては、オプション `--tail `0` の利用をお勧めします。 

出力例を次に示します。

```powershell
[10.100.10.10]: PS>iotedge logs cuda-sample2 --tail 10
[10.100.10.10]: PS>iotedge logs edgeagent --tail 10
<6> 2021-02-25 00:52:54.828 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:52:54.829 +00:00 [INF] - Plan execution ended for deployment 11
<6> 2021-02-25 00:53:00.191 +00:00 [INF] - Plan execution started for deployment 11
<6> 2021-02-25 00:53:00.191 +00:00 [INF] - Executing command: "Create an EdgeDeployment with modules: [cuda-sample2, edgeAgent, edgeHub, cuda-sample1]"
<6> 2021-02-25 00:53:00.212 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:53:00.212 +00:00 [INF] - Plan execution ended for deployment 11
<6> 2021-02-25 00:53:05.319 +00:00 [INF] - Plan execution started for deployment 11
<6> 2021-02-25 00:53:05.319 +00:00 [INF] - Executing command: "Create an EdgeDeployment with modules: [cuda-sample2, edgeAgent, edgeHub, cuda-sample1]"
<6> 2021-02-25 00:53:05.412 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:53:05.412 +00:00 [INF] - Plan execution ended for deployment 11
[10.100.10.10]: PS>
```

### <a name="use-kubectl-commands"></a>kubectl コマンドを使用する

コンピューティング ロールが構成されている Azure Stack Edge Pro デバイスでは、すべての `kubectl` コマンドをモジュールの監視やトラブルシューティングに利用できます。 使用可能なコマンドの一覧を表示するには、コマンド ウィンドウから `kubectl --help` を実行します。

```PowerShell
C:\Users\myuser>kubectl --help

kubectl controls the Kubernetes cluster manager.

Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
    create         Create a resource from a file or from stdin.
    expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
    run            Run a particular image on the cluster
    set            Set specific features on objects
    run-container  Run a particular image on the cluster. This command is deprecated, use "run" instead
==============CUT=============CUT============CUT========================

Usage:
    kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

C:\Users\myuser>
```

`kubectl` コマンドの広範な一覧については、[`kubectl` チートシート](https://kubernetes.io/docs/reference/kubectl/cheatsheet/) を参照してください。


#### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>Kubernetes クラスターの外部に公開されているサービスまたはモジュールの IP を取得するには

Kubernetes の外部に公開されている負荷分散のサービスまたはモジュールの IP を取得するには、次のコマンドを実行します。

`kubectl get svc -n iotedge`

Kubernetes クラスターの外部に公開されているすべてのサービスまたはモジュールの出力例を次に示します。 


```powershell
[10.100.10.10]: PS>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

[10.100.10.10]: PS>
```
External IP 列の IP アドレスは、サービスまたはモジュールの外部エンドポイントに対応します。 [Kubernetes ダッシュボードで外部 IP を取得する](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules)こともできます。


#### <a name="to-check-if-module-deployed-successfully"></a>モジュールが正常にデプロイされているかどうかを確認するには

コンピューティング モジュールは、ビジネス ロジックが実装されたコンテナーです。 Kubernetes ポッドでは、複数のコンテナーを実行できます。 

コンピューティング モジュールが正常にデプロイされたかどうかを確認するには、デバイスの PowerShell インターフェイスに接続します。
`get pods` コマンドを実行し、コンテナー (コンピューティング モジュールに対応) が実行されているかどうかを確認します。

特定の名前空間で実行されているすべてのポッドの一覧を取得するには、次のコマンドを実行します。

`get pods -n <namespace>`

IoT Edge 経由でデプロイされているモジュールを確認するには、次のコマンドを実行します。

`get pods -n iotedge`

`iotedge` 名前空間で実行されているすべてのポッドの出力例を次に示します。

```
[10.100.10.10]: PS>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

[10.100.10.10]: PS>
```

状態 **[Status]** は、名前空間のすべてのポッドが実行されていることを示し、 **[Ready]** はポッドにデプロイされたコンテナーの数を示します。 前の例では、すべてのポッドが実行されており、各ポッドにデプロイされているすべてのモジュールが実行されています。 

Azure Arc 経由でデプロイされているモジュールを確認するには、次のコマンドを実行します。

`get pods -n azure-arc`

または、[Kubernetes ダッシュボードに接続して IoT Edge または Azure Arc のデプロイを表示する](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#view-module-status)こともできます。


指定した名前空間の特定のポッドについてさらに詳細に出力するには、次のコマンドを実行します。

`kubectl describe pod <pod name> -n <namespace>` 

サンプル出力を次に示します。

```
[10.100.10.10]: PS>kubectl describe pod filemove-66c49984b7 -n iotedge
Name:           filemove-66c49984b7-h8lxc
Namespace:      iotedge
Priority:       0
Node:           k8s-1hwf613cl-1hwf613/10.139.218.12
Start Time:     Thu, 14 May 2020 12:46:28 -0700
Labels:         net.azure-devices.edge.deviceid=myasegpu-edge
                net.azure-devices.edge.hub=myasegpu2iothub.azure-devices.net
                net.azure-devices.edge.module=filemove
                pod-template-hash=66c49984b7
Annotations:    net.azure-devices.edge.original-moduleid: filemove
Status:         Running
IP:             172.17.75.81
IPs:            <none>
Controlled By:  ReplicaSet/filemove-66c49984b7
Containers:
    proxy:
    Container ID:   docker://fd7975ca78209a633a1f314631042a0892a833b7e942db2e7708b41f03e8daaf
    Image:          azureiotedge/azureiotedge-proxy:0.1.0-beta8
    Image ID:       docker://sha256:5efbf6238f13d24bab9a2b499e5e05bc0c33ab1587d6cf6f289cdbe7aa667563
    Port:           <none>
    Host Port:      <none>
    State:          Running
        Started:      Thu, 14 May 2020 12:46:30 -0700
    Ready:          True
    Restart Count:  0
    Environment:
        PROXY_LOG:  Debug
=============CUT===============================CUT===========================
Volumes:
    config-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-config
    Optional:  false
    trust-bundle-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-trust-bundle
    Optional:  false
    myasesmb1local:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1local
    ReadOnly:   false
    myasesmb1:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1
    ReadOnly:   false
    filemove-token-pzvw8:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  filemove-token-pzvw8
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                    node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


[10.100.10.10]: PS>
```

#### <a name="to-get-container-logs"></a>コンテナー ログを取得するには

モジュールのログを取得するには、デバイスの PowerShell インターフェイスから次のコマンドを実行します。

`kubectl logs <pod_name> -n <namespace> --all-containers` 

`all-containers` フラグはすべてのコンテナーのすべてのログをダンプするため、最近のエラーを表示する方法としては、オプション `--tail 10` の利用をお勧めします。

サンプル出力を次に示します。 

```
[10.100.10.10]: PS>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
DEBUG 2020-05-14T20:40:42Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:12Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
05/14/2020 19:46:44: Info: Opening module client connection.
05/14/2020 19:46:45: Info: Open done.
05/14/2020 19:46:45: Info: Initializing with input: /home/input, output: /home/output, protocol: Amqp.
05/14/2020 19:46:45: Info: IoT Hub module client initialized.

[10.100.10.10]: PS>
```

### <a name="change-memory-processor-limits-for-kubernetes-worker-node"></a>Kubernetes ワーカー ノードのメモリまたはプロセッサの制限を変更する

Kubernetes ワーカー ノードのメモリまたはプロセッサの制限を変更するには、次の手順を実行します。

1. [デバイスの PowerShell インターフェイスに接続します](#connect-to-the-powershell-interface)。
1. ワーカー ノードの現在のリソースとロール オプションを取得するには、次のコマンドを実行します。

    `Get-AzureDataBoxEdgeRole`

    出力例を次に示します。 `Resources` セクションの `Name` と `Compute` の値をメモします。 `MemoryInBytes` と `ProcessorCount` は、Kubernetes ワーカー ノードに現在割り当てられている値のメモリとプロセッサ数をそれぞれ示します。  

    ```powershell
    [10.100.10.10]: PS>Get-AzureDataBoxEdgeRole
    ImageDetail                : Name:mcr.microsoft.com/azureiotedge-agent
                                 Tag:1.0
                                 PlatformType:Linux
    EdgeDeviceConnectionString :
    IotDeviceConnectionString  :
    HubHostName                : ase-srp-007.azure-devices.net
    IotDeviceId                : srp-007-storagegateway
    EdgeDeviceId               : srp-007-edge
    Version                    :
    Id                         : 6ebeff9f-84c5-49a7-890c-f5e05520a506
    Name                       : IotRole
    Type                       : IOT
    Resources                  : Compute:
                                 MemoryInBytes:34359738368
                                 ProcessorCount:12
                                 VMProfile:
    
                                 Storage:
                                 EndpointMap:
                                 EndpointId:c0721210-23c2-4d16-bca6-c80e171a0781
                                 TargetPath:mysmbedgecloudshare1
                                 Name:mysmbedgecloudshare1
                                 Protocol:SMB
    
                                 EndpointId:6557c3b6-d3c5-4f94-aaa0-6b7313ab5c74
                                 TargetPath:mysmbedgelocalshare
                                 Name:mysmbedgelocalshare
                                 Protocol:SMB
                                 RootFileSystemStorageSizeInBytes:0
    
    HostPlatform               : KubernetesCluster
    State                      : Created
    PlatformType               : Linux
    HostPlatformInstanceId     : 994632cb-853e-41c5-a9cd-05b36ddbb190
    IsHostPlatformOwner        : True
    IsCreated                  : True    
    [10.100.10.10]: PS>
    ```
    
1. ワーカー ノードのメモリおよびプロセッサの値を変更するには、次のコマンドを実行します。

    Set-AzureDataBoxEdgeRoleCompute -Name <Name value from the output of Get-AzureDataBoxEdgeRole> -Memory <Value in Bytes> -ProcessorCount < コア数 >

    出力例を次に示します。 
    
    ```powershell
    [10.100.10.10]: PS>Set-AzureDataBoxEdgeRoleCompute -Name IotRole -MemoryInBytes 32GB -ProcessorCount 16
    
    ImageDetail                : Name:mcr.microsoft.com/azureiotedge-agent
                                 Tag:1.0
                                 PlatformType:Linux
    
    EdgeDeviceConnectionString :
    IotDeviceConnectionString  :
    HubHostName                : ase-srp-007.azure-devices.net
    IotDeviceId                : srp-007-storagegateway
    EdgeDeviceId               : srp-007-edge
    Version                    :
    Id                         : 6ebeff9f-84c5-49a7-890c-f5e05520a506
    Name                       : IotRole
    Type                       : IOT
    Resources                  : Compute:
                                 MemoryInBytes:34359738368
                                 ProcessorCount:16
                                 VMProfile:
    
                                 Storage:
                                 EndpointMap:
                                 EndpointId:c0721210-23c2-4d16-bca6-c80e171a0781
                                 TargetPath:mysmbedgecloudshare1
                                 Name:mysmbedgecloudshare1
                                 Protocol:SMB
    
                                 EndpointId:6557c3b6-d3c5-4f94-aaa0-6b7313ab5c74
                                 TargetPath:mysmbedgelocalshare
                                 Name:mysmbedgelocalshare
                                 Protocol:SMB
    
                                 RootFileSystemStorageSizeInBytes:0
    
    HostPlatform               : KubernetesCluster
    State                      : Created
    PlatformType               : Linux
    HostPlatformInstanceId     : 994632cb-853e-41c5-a9cd-05b36ddbb190
    IsHostPlatformOwner        : True
    IsCreated                  : True
    
    [10.100.10.10]: PS>    
    ```

メモリおよびプロセッサの使用率を変更する際は、次のガイドラインに従ってください。

- 既定のメモリは、デバイスの仕様の 25% です。
- 既定のプロセッサ数は、デバイスの仕様の 30% です。
- メモリとプロセッサ数の値を変更するときは、デバイスのメモリとプロセッサ数の 15% から 60% の間で値を変更することをお勧めします。 
- システム コンポーネントに十分なリソースがあるように、上限の 60% にすることをお勧めします。 

## <a name="connect-to-bmc"></a>BMC に接続する

ベースボード管理コントローラー (BMC) は、デバイスをリモートで監視および管理するために使用されます。 このセクションでは、BMC の構成を管理するために使用できるコマンドレットについて説明します。 これらのコマンドレットを実行する前に、[デバイスの PowerShell インターフェイスに接続します](#connect-to-the-powershell-interface)。

- `Get-HcsNetBmcInterface`:このコマンドレットを使用して、BMC のネットワーク構成プロパティ (`IPv4Address`、`IPv4Gateway`、`IPv4SubnetMask`、`DhcpEnabled` など) を取得します。 
    
    出力例を次に示します。
    
    ```powershell
    [10.100.10.10]: PS>Get-HcsNetBmcInterface
    IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
    -----------   ----------- -------------- -----------
    10.128.53.186 10.128.52.1 255.255.252.0        False
    [10.100.10.10]: PS>
    ```
- `Set-HcsNetBmcInterface`:このコマンドレットは、次の 2 つの方法で使用できます。

    - コマンドレットを使用して、`UseDhcp` パラメーターに適切な値を使用することで、BMC の DHCP 構成を有効または無効にします。 

        ```powershell
        Set-HcsNetBmcInterface -UseDhcp $true
        ```

        出力例を次に示します。 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -UseDhcp $true
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address IPv4Gateway IPv4SubnetMask DhcpEnabled
        ----------- ----------- -------------- -----------
        10.128.54.8 10.128.52.1 255.255.252.0         True
        [10.100.10.10]: PS>
        ```

    - このコマンドレットを使用して、BMC の静的構成を構成します。 `IPv4Address`、`IPv4Gateway`、および `IPv4SubnetMask` の値を指定できます。 
    
        ```powershell
        Set-HcsNetBmcInterface -IPv4Address "<IPv4 address of the device>" -IPv4Gateway "<IPv4 address of the gateway>" -IPv4SubnetMask "<IPv4 address for the subnet mask>"
        ```        
        
        出力例を次に示します。 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -IPv4Address 10.128.53.186 -IPv4Gateway 10.128.52.1 -IPv4SubnetMask 255.255.252.0
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
        -----------   ----------- -------------- -----------
        10.128.53.186 10.128.52.1 255.255.252.0        False
        [10.100.10.10]: PS>
        ```    

- `Set-HcsBmcPassword`:`EdgeUser` の BMC パスワードを変更するには、このコマンドレットを使用します。 ユーザー名 `EdgeUser` の大文字と小文字は区別されます。

    出力例を次に示します。 

    ```powershell
    [10.100.10.10]: PS> Set-HcsBmcPassword -NewPassword "Password1"
    [10.100.10.10]: PS>
    ```

## <a name="exit-the-remote-session"></a>リモート セッションを終了します。

リモート PowerShell セッションを終了するには、PowerShell ウィンドウを閉じます。

## <a name="next-steps"></a>次のステップ

- Azure portal に [Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md) をデプロイします。