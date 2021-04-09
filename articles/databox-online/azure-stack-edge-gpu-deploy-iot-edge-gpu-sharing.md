---
title: Azure Stack Edge Pro GPU デバイスに GPU 共有を使用する IoT Edge ワークロードをデプロイする
description: Azure Stack Edge Pro GPU デバイスで IoT Edge を使用して GPU 共有ワークロードをデプロイする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: b52d1e834772a2a6e0e000b3df15d8aa0fa866a9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564723"
---
# <a name="deploy-an-iot-edge-workload-using-gpu-sharing-on-your-azure-stack-edge-pro"></a>Azure Stack Edge Pro で GPU 共有を使用する IoT Edge ワークロードをデプロイする

この記事では、コンテナー化されたワークロードで Azure Stack Edge Pro GPU デバイス上の GPU を共有する方法について説明します。 この手法では、マルチプロセス サービス (MPS) を有効にし、IoT Edge デプロイを使用して GPU ワークロードを指定する必要があります。 

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

1. [アクティブ化](azure-stack-edge-gpu-deploy-activate.md)され、[コンピューティングが構成されている](azure-stack-edge-gpu-deploy-configure-compute.md) Azure Stack Edge Pro GPU デバイスにアクセスできること。 [Kubernetes API エンドポイント](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)があり、デバイスにアクセスするクライアント上の `hosts` ファイルにこのエンドポイントを追加済みであること。

1. [サポートされているオペレーティング システム](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)でクライアント システムにアクセスしていること。 Windows クライアントを使用している場合、デバイスにアクセスするには、システムで PowerShell 5.0 以降が実行されている必要があります。

1. ローカル システムに次のデプロイ `json` を保存すること。 このファイルの情報を使用して、IoT Edge デプロイを実行します。 このデプロイは、Nvidia で一般公開されている[単純な CUDA コンテナー](https://docs.nvidia.com/cuda/wsl-user-guide/index.html#running-simple-containers)に基づいています。 

    ```json
    {
        "modulesContent": {
            "$edgeAgent": {
                "properties.desired": {
                    "modules": {
                        "cuda-sample1": {
                            "settings": {
                                "image": "nvidia/samples:nbody",
                                "createOptions": "{\"Entrypoint\":[\"/bin/sh\"],\"Cmd\":[\"-c\",\"/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done\"],\"HostConfig\":{\"IpcMode\":\"host\",\"PidMode\":\"host\"}}"
                            },
                            "type": "docker",
                            "version": "1.0",
                            "env": {
                                "NVIDIA_VISIBLE_DEVICES": {
                                    "value": "0"
                                }
                            },
                            "status": "running",
                            "restartPolicy": "never"
                        },
                        "cuda-sample2": {
                            "settings": {
                                "image": "nvidia/samples:nbody",
                                "createOptions": "{\"Entrypoint\":[\"/bin/sh\"],\"Cmd\":[\"-c\",\"/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done\"],\"HostConfig\":{\"IpcMode\":\"host\",\"PidMode\":\"host\"}}"
                            },
                            "type": "docker",
                            "version": "1.0",
                            "env": {
                                "NVIDIA_VISIBLE_DEVICES": {
                                    "value": "0"
                                }
                            },
                            "status": "running",
                            "restartPolicy": "never"
                        }
                    },
                    "runtime": {
                        "settings": {
                            "minDockerVersion": "v1.25"
                        },
                        "type": "docker"
                    },
                    "schemaVersion": "1.1",
                    "systemModules": {
                        "edgeAgent": {
                            "settings": {
                                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                                "createOptions": ""
                            },
                            "type": "docker"
                        },
                        "edgeHub": {
                            "settings": {
                                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                            },
                            "type": "docker",
                            "status": "running",
                            "restartPolicy": "always"
                        }
                    }
                }
            },
            "$edgeHub": {
                "properties.desired": {
                    "routes": {
                        "route": "FROM /messages/* INTO $upstream"
                    },
                    "schemaVersion": "1.1",
                    "storeAndForwardConfiguration": {
                        "timeToLiveSecs": 7200
                    }
                }
            },
            "cuda-sample1": {
                "properties.desired": {}
            },
            "cuda-sample2": {
                "properties.desired": {}
            }
        }
    }
    ```

## <a name="verify-gpu-driver-cuda-version"></a>GPU ドライバーと CUDA のバージョンを確認する

最初の手順では、デバイスで必要な GPU ドライバーと CUDA のバージョンが実行されていることを確認します。

1. [デバイスの PowerShell インターフェイスに接続します](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。

1. 次のコマンドを実行します。

    `Get-HcsGpuNvidiaSmi`

1. Nvidia smi 出力から、デバイス上の GPU バージョンと CUDA バージョンをメモしておきます。 Azure Stack Edge 2102 ソフトウェアを実行している場合、このバージョンは次のドライバー バージョンに対応します。

    - GPU ドライバー バージョン: 460.32.03
    - CUDA バージョン: 11.2
    
    出力例を次に示します。

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Tue Feb 23 10:34:01 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 0000041F:00:00.0 Off |                    0 |
    | N/A   40C    P8    15W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>  
    ```

1. このセッションは開いたままにしておいてください。記事全体を通してこれを使用して Nvidia smi 出力を表示します。


## <a name="deploy-without-context-sharing"></a>コンテキスト共有なしでデプロイする

ここで、マルチプロセス サービスが実行されておらず、コンテキスト共有がない場合に、アプリケーションをデバイスにデプロイできます。 Azure portal を使用して、ご利用のデバイスに存在する `iotedge` 名前空間にデプロイします。

### <a name="create-user-in-iot-edge-namespace"></a>IoT Edge 名前空間にユーザーを作成する

まず、`iotedge` 名前空間に接続するユーザーを作成します。 IoT Edge モジュールは、iotedge 名前空間にデプロイされます。 詳細については、[デバイス上の Kubernetes 名前空間](azure-stack-edge-gpu-kubernetes-rbac.md#namespaces-types)に関する記事を参照してください。

ユーザーを作成し、`iotedge` 名前空間へのアクセス権をユーザーに付与するには、次の手順に従います。 

1. [デバイスの PowerShell インターフェイスに接続します](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。

1. `iotedge` 名前空間に新しいユーザーを作成します。 次のコマンドを実行します。

    `New-HcsKubernetesUser -UserName <user name>`

    出力例を次に示します。

    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName iotedgeuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: 
    ===========================//snipped //======================// snipped //=============================
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: iotedgeuser
      name: iotedgeuser@kubernetes
    current-context: iotedgeuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: iotedgeuser
      user:
        client-certificate-data: 
    ===========================//snipped //======================// snipped //=============================
        client-key-data: 
    ===========================//snipped //======================// snipped ============================
    PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=
    ```

1. プレーン テキストで表示される出力をコピーします。 その出力を、ローカル コンピューター上のユーザー プロファイルの `.kube` フォルダー (`C:\Users\<username>\.kube` など) に *config* ファイル (拡張子なし) として保存します。 

1. 作成したユーザーに `iotedge` 名前空間へのアクセス権を付与します。 次のコマンドを実行します。

    `Grant-HcsKubernetesNamespaceAccess -Namespace iotedge -UserName <user name>`    

    出力例を次に示します。

    ```python
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace iotedge -UserName iotedgeuser
    [10.100.10.10]: PS>    
    ```
詳細な手順については、「[Azure Stack Edge Pro GPU デバイスで kubectl を使用して Kubernetes クラスターに接続して管理する](azure-stack-edge-gpu-create-kubernetes-cluster.md#configure-cluster-access-via-kubernetes-rbac)」を参照してください。

### <a name="deploy-modules-via-portal"></a>ポータルを使用してモジュールをデプロイする

Azure portal を使用して IoT Edge モジュールをデプロイします。 N 体シミュレーションを実行する、一般公開されている Nvidia CUDA サンプル モジュールをデプロイします。 詳細については、「[N 体シミュレーション](https://physics.princeton.edu//~fpretori/Nbody/intro.htm)」を参照してください。

1. デバイスで IoT Edge サービスが実行されていることを確認します。

    ![実行されている IoT Edge サービス。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-1.png)

1. 右ペインで [IoT Edge] タイルを選択します。 **[IoT Edge] > [プロパティ]** に移動します。 右ペインで、デバイスに関連付けられている IoT Hub リソースを選択します。

    ![プロパティの表示](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-2.png)

1. IoT Hub リソース内で、 **[デバイスの自動管理] > [IoT Edge]** に移動します。 右ペインで、ご利用のデバイスに関連付けられている IoT Edge デバイスを選択します。

    ![IoT Edge にアクセスします。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-3.png)

1. **[Set modules]\(モジュールの設定\)** を選びます。

    ![[モジュールの設定] に移動します。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-4.png)

1. **[+ 追加] > [+ IoT Edge モジュール]** を選択します。

    ![Add IoT Edge module コマンドを入力して実行します。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-5.png)

1. **[モジュールの設定]** タブで、 **[IoT Edge モジュール名]** と **[イメージのURI]** を指定します。 **[Image Pull Policy]\(イメージ プル ポリシー\)** を **[On create]\(作成時\)** に設定します。

    ![モジュールの設定。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-6.png)
1. **[環境変数]** タブで、**NVIDIA_VISIBLE_DEVICES** を **0** として指定します。

    ![環境変数。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-7.png)

1. **[コンテナーの作成オプション]** タブで、次のオプションを指定します。

    ```json
    {
        "Entrypoint": [
            "/bin/sh"
        ],
        "Cmd": [
            "-c",
            "/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done"
        ],
        "HostConfig": {
            "IpcMode": "host",
            "PidMode": "host"
        }
    }    
    ```
    オプションは次のように表示されます。

    ![コンテナーの作成オプション。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-8.png)

    **[追加]** を選択します。

1. 追加したモジュールは **[実行中]** と表示されます。 

    ![デプロイを確認して作成します。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-9.png)


1. 最初のモジュールの追加時に行ったすべての手順を繰り返して、モジュールを追加します。 この例では、モジュールの名前を `cuda-sample2` と指定します。 

    ![2 つ目のモジュールに対するモジュールの設定。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-12.png)

    両方のモジュールが同じ GPU を共有するので、同じ環境変数を使用します。

    ![2 つ目のモジュールの環境変数。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-13.png)

    最初のモジュールに対して指定したものと同じコンテナーの作成オプションを使用し、 **[追加]** を選択します。

    ![2 つ目のモジュールに対するコンテナーの作成オプション。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-14.png)

1. **[モジュールの設定]** ページで **[確認および作成]** を選択し、 **[作成]** を選択します。 

    ![2 つ目のデプロイを確認して作成します。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-15.png)

1. これで、両方のモジュールの **[ランタイムの状態]** が **[実行中]** と表示されます。  

    ![2 つ目のデプロイの状態。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-16.png)


### <a name="monitor-workload-deployment"></a>ワークロードのデプロイを監視する

1. 新しい PowerShell セッションを開きます。

1. `iotedge` 名前空間で実行されているポッドの一覧を表示します。 次のコマンドを実行します。

    `kubectl get pods -n iotedge`   

    出力例を次に示します。

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n iotedge --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1
    NAME                            READY   STATUS    RESTARTS   AGE
    cuda-sample1-869989578c-ssng8   2/2     Running   0          5s
    cuda-sample2-6db6d98689-d74kb   2/2     Running   0          4s
    edgeagent-79f988968b-7p2tv      2/2     Running   0          6d21h
    edgehub-d6c764847-l8v4m         2/2     Running   0          24h
    iotedged-55fdb7b5c6-l9zn8       1/1     Running   1          6d21h
    PS C:\WINDOWS\system32>   
    ```
    2 つのポッド (`cuda-sample1-97c494d7f-lnmns` と `cuda-sample2-d9f6c4688-2rld9`) がデバイスで実行されています。

1. 両方のコンテナーで N 体シミュレーションが実行されている間に、Nvidia smi 出力の GPU 使用率を確認します。 デバイスの PowerShell インターフェイスにアクセスし、`Get-HcsGpuNvidiaSmi` を実行します。

    両方のコンテナーで N 体シミュレーションが実行されているときの出力例を次に示します。

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Fri Mar  5 13:31:16 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   52C    P0    69W /  70W |    221MiB / 15109MiB |    100%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    188342      C   /tmp/nbody                        109MiB |
    |    0   N/A  N/A    188413      C   /tmp/nbody                        109MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```
    ご覧のように、GPU 0 で 2 つのコンテナーが N 体シミュレーションを行っている状態で実行されています。 また、対応するメモリ使用量を表示することもできます。

1. シミュレーションが完了すると、Nvidia smi 出力に、デバイスで実行されているプロセスがないことが示されます。

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Fri Mar  5 13:54:48 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   34C    P8     9W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```
 
1. N 体シミュレーションが完了したら、ログを表示して、デプロイの詳細と、シミュレーションの完了にかかった時間を把握します。 

    最初のコンテナーからの出力例を次に示します。

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample1-869989578c-ssng8 cuda-sample1
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170171.531 ms
    = 98.590 billion interactions per second
    = 1971.801 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>
    ```
    2 つ目のコンテナーからの出力例を次に示します。

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample2-6db6d98689-d74kb cuda-sample2
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170054.969 ms
    = 98.658 billion interactions per second
    = 1973.152 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>
    ```

1. モジュールのデプロイを停止します。 デバイスの IoT Hub リソースで次のようにします。
    1. **[Automatic Device Deployment]\(デバイスの自動デプロイ\) > [IoT Edge]** に移動します。 デバイスに対応する IoT Edge デバイスを選択します。

    1. **[モジュールの設定]** に移動し、モジュールを選択します。 

        ![モジュールの設定を選択します。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-1.png)

    1. **[モジュール]** タブで、モジュールを選択します。
    
        ![モジュールを選択します。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-2.png)

    1.  **[モジュールの設定]** タブで、 **[必要な状態]** を [停止] に設定します。 **[Update]\(更新\)** を選択します。

        ![モジュール設定を更新します。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-3.png)

    1. 手順を繰り返して、デバイスにデプロイされている 2 つ目のモジュールを停止します。 **[確認および作成]** を選択し、次に **[作成]** を選択します。 これにより、デプロイが更新されます。

        ![更新されたデプロイを確認して作成します。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-6.png)
 
    1. **[モジュールの設定]** ページを複数回更新します。 モジュールの **[ランタイムの状態]** が **[停止]** と表示されるまで行います。

        ![デプロイ状態を確認します。](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-8.png) 
    

## <a name="deploy-with-context-sharing"></a>コンテキスト共有ありでデプロイする

ここで、デバイスで MPS が実行されている場合に、2 つの CUDA コンテナーに N 体シミュレーションをデプロイできます。 まず、デバイスで MPS を有効にします。


1. [デバイスの PowerShell インターフェイスに接続します](azure-stack-edge-gpu-connect-powershell-interface.md)。

1. デバイスで MPS を有効にするには、`Start-HcsGpuMPS` コマンドを実行します。

    ```powershell
    [10.100.10.10]: PS>Start-HcsGpuMPS
    K8S-1HXQG13CL-1HXQG13:
    Set compute mode to EXCLUSIVE_PROCESS for GPU 0000191E:00:00.0.
    All done.
    Created nvidia-mps.service
    [10.100.10.10]: PS>    
    ```
1. デバイスの PowerShell インターフェイスから Nvidia smi 出力を取得します。 デバイスで `nvidia-cuda-mps-server` プロセス (MPS サービス) が実行されていることが確認できます。

    出力例を次に示します。

    ```yml
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Thu Mar  4 12:37:39 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   36C    P8     9W /  70W |     28MiB / 15109MiB |      0%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    122792      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    ```

1. 以前に停止したモジュールをデプロイします。 **[モジュールの設定]** を使用して、 **[必要な状態]** を [実行中] に設定します。

    出力例を次に示します。

    ```yml
    PS C:\WINDOWS\system32> kubectl get pods -n iotedge --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1
    NAME                            READY   STATUS    RESTARTS   AGE
    cuda-sample1-869989578c-2zxh6   2/2     Running   0          44s
    cuda-sample2-6db6d98689-fn7mx   2/2     Running   0          44s
    edgeagent-79f988968b-7p2tv      2/2     Running   0          5d20h
    edgehub-d6c764847-l8v4m         2/2     Running   0          27m
    iotedged-55fdb7b5c6-l9zn8       1/1     Running   1          5d20h
    PS C:\WINDOWS\system32>
    ```
    デバイスにモジュールがデプロイされ、実行されていることが確認できます。

1. モジュールがデプロイされると、両方のコンテナーで N 体シミュレーションの実行も開始されます。 最初のコンテナーでシミュレーションが完了したときの出力例を次に示します。

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge logs cuda-sample1-869989578c-2zxh6 cuda-sample1
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 155256.062 ms
    = 108.062 billion interactions per second
    = 2161.232 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32> 
    ```
    2 つ目のコンテナーでシミュレーションが完了したときの出力例を次に示します。

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample2-6db6d98689-fn7mx cuda-sample2
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 155366.359 ms
    = 107.985 billion interactions per second
    = 2159.697 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>    
    ```      

1. 両方のコンテナーで N 体シミュレーションが実行されているときに、デバイスの PowerShell インターフェイスから Nvidia smi 出力を取得します。 出力例を次に示します。 3 つのプロセスがあり、`nvidia-cuda-mps-server` プロセス (種類 C) は MPS サービスに対応し、`/tmp/nbody` プロセス (種類 M + C) は各モジュールによってデプロイされた N 体ワークロードに対応します。 

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Thu Mar  4 12:59:44 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   54C    P0    69W /  70W |    242MiB / 15109MiB |    100%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A     56832    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A     56900    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A    122792      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    ```
    
## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge Pro で共有 GPU Kubernetes ワークロードをデプロイする](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md)。
