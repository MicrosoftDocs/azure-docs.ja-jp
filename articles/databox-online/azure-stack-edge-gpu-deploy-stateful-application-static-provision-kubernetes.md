---
title: kubectl を使用し、Azure Stack Edge Pro デバイスで静的にプロビジョニングされた共有を介して Kubernetes ステートフル アプリをデプロイする | Microsoft Docs
description: kubectl を使用し、Azure Stack Edge Pro GPU デバイスで静的にプロビジョニングされた共有を介して Kubernetes ステートフル アプリケーションのデプロイを作成および管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/09/2021
ms.author: alkohli
ms.openlocfilehash: 01ba8e1f22deb376fd461be24d3f66f0a7f5e1ae
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102610486"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-a-persistentvolume-on-your-azure-stack-edge-pro-device"></a>kubectl を使用し、Azure Stack Edge Pro デバイスで PersistentVolume を使って Kubernetes ステートフル アプリケーションを実行する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、PersistentVolume (PV) とデプロイを使用して、Kubernetes で単一インスタンスのステートフル アプリケーションをデプロイする方法について説明します。 このデプロイでは、既存の Kubernetes クラスターで `kubectl` コマンドを使用し、MySQL アプリケーションをデプロイします。 

この手順は、[Azure Stack Edge Pro デバイス上の Kubernetes ストレージ](azure-stack-edge-gpu-kubernetes-storage.md)に関する記事を確認し、[Kubernetes ストレージ](https://kubernetes.io/docs/concepts/storage/)の概念を理解しているユーザーを対象としています。 

Azure Stack Edge Pro では、Azure SQL Edge コンテナーの実行もサポートされており、ここで説明する MySQL の場合と同様の方法でデプロイできます。 詳細については、[Azure SQL Edge](../azure-sql-edge/overview.md) に関する記事をご覧ください。


## <a name="prerequisites"></a>前提条件

ステートフル アプリケーションをデプロイする前に、デバイスとデバイスにアクセスするために使用するクライアントで、次の前提条件を完了してください。

### <a name="for-device"></a>デバイスでは

- 1 ノードの Azure Stack Edge Pro デバイスに対するサインイン資格情報がある。
    - デバイスがアクティブ化されている。 [デバイスのアクティブ化](azure-stack-edge-gpu-deploy-activate.md)に関する記事を参照してください。
    - デバイスに、Azure portal を使用して構成されたコンピューティング ロールがあり、Kubernetes クラスターがある。 [コンピューティングの構成](azure-stack-edge-gpu-deploy-configure-compute.md)に関する記事を参照してください。

### <a name="for-client-accessing-the-device"></a>デバイスにアクセスするクライアントでは

- Azure Stack Edge Pro デバイスへのアクセスに使用される Windows クライアント システムがある。
    - クライアントでは、Windows PowerShell 5.0 以降が実行されている。 Windows PowerShell の最新バージョンをダウンロードするには、「[Windows PowerShell のインストール](/powershell/scripting/install/installing-windows-powershell)」を参照してください。
    
    - [サポートされているオペレーティング システム](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)が搭載されている他のクライアントを使用することもできます。 この記事では、Windows クライアントを使用する場合の手順について説明します。 
    
    - [Azure Stack Edge Pro デバイス上の Kubernetes クラスターへのアクセス](azure-stack-edge-gpu-create-kubernetes-cluster.md)に関する記事で説明されている手順を完了している。 完了した内容:
      - `New-HcsKubernetesNamespace` コマンドを使用して `userns1` 名前空間を作成した。 
      - `New-HcsKubernetesUser` コマンドを使用してユーザー `user1` を作成した。 
      - `Grant-HcsKubernetesNamespaceAccess` コマンドを使用して、`user1` に `userns1` への アクセス権を許可した。       
      - クライアントに `kubectl` をインストールし、ユーザー構成を含む `kubeconfig` ファイルを C:\\Users\\&lt;username&gt;\\.kube に保存した。 
    
    - `kubectl` クライアントのバージョンと、Azure Stack Edge Pro デバイスで実行されている Kubernetes マスターのバージョンの差が 1 未満であることを確認する。 
        - クライアントで実行されている kubectl のバージョンを確認するには、`kubectl version` を使用します。 完全なバージョン番号をメモしておきます。
        - お使いの Azure Stack Edge Pro デバイスのローカル UI で、 **[概要]** に移動し、Kubernetes ソフトウェアの番号をメモします。 
        - サポートされている Kubernetes バージョンで提供されているマッピングで、これら 2 つのバージョンの互換性を確認します。<!-- insert link--> 


Azure Stack Edge Pro デバイスにステートフル アプリケーションをデプロイする準備ができました。 

## <a name="provision-a-static-pv"></a>静的な PV をプロビジョニングする

PV を静的にプロビジョニングするには、デバイスに共有を作成する必要があります。 SMB 共有に対して PV をプロビジョニングするには、次の手順に従います。 

> [!NOTE]
> - この操作方法の記事で使用されている具体的な例は、NFS 共有では機能しません。 NFS 共有は通常、Azure Stack Edge デバイスでデータベース以外のアプリケーションを使用してプロビジョニングできます。
> - ストレージ ボリュームを使用するステートフル アプリケーションをデプロイして永続ストレージを提供するには、`StatefulSet` を使用することをお勧めします。 この例では、`Deployment` と 1 つのレプリカのみを使用しています。これは、開発とテストに適しています。 

1. Edge 共有と Edge ローカル共有のどちらを作成するかを選択します。 「[共有の追加](azure-stack-edge-manage-shares.md#add-a-share)」の手順に従って、共有を作成してください。 **[Edge コンピューティングで共有を使用する]** チェック ボックスを必ずオンにします。

    ![PV の Edge ローカル共有](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/edge-local-share-static-provision-1.png)

    1. 新しい共有を作成するのではなく、既存の共有を使用する場合は、共有をマウントする必要があります。
    
        Azure portal で Azure Stack Edge リソースの **[共有]** にアクセスします。 既存の共有の一覧で、使用する共有を選択してクリックします。

        ![PV の既存のローカル共有を選択する](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-1.png)

    1. **[マウント]** を選択し、メッセージが表示されたらマウントを確定します。  

        ![PV の既存のローカル共有をマウントする](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-2.png)

1. 共有名をメモしておきます。 この共有が作成されるときに、作成された SMB 共有に対応する Kubernetes クラスターに、永続ボリューム オブジェクトが自動的に作成されます。 

## <a name="deploy-mysql"></a>MySQL をデプロイする

Kubernetes のデプロイを作成し、PersistentVolumeClaim (PVC) を使用して、前の手順で作成した PV に接続することによって、ステートフル アプリケーションを実行します。 

ステートフル アプリケーションのデプロイを作成および管理するために使用するすべての `kubectl` コマンドでは、構成に関連付けられている名前空間を指定する必要があります。 kubectl コマンドで名前空間を指定するには、`kubectl <command> -n <your-namespace>` を使用します。

1. 名前空間の Kubernetes クラスターで実行されているポッドの一覧を取得します。 ポッドは、Kubernetes クラスターで実行されるアプリケーション コンテナー (つまり、プロセス) です。

   ```powershell
   kubectl get pods -n <your-namespace>
   ```
    
   コマンドの使用例を次に示します。
    
   ```powershell
    C:\Users\user>kubectl get pods -n "userns1"
    No resources found in userns1 namespace.    
    C:\Users\user>
   ```
    
   クラスターで実行されているアプリケーションが存在しないため、出力にはリソース (ポッド) が見つからないことが示されるはずです。

1. 次の YAML ファイルを使用します。 `mysql-deployment.yml` ファイルには、MySQL を実行し、PVC を参照するデプロイが記述されています。 このファイルは、`/var/lib/mysql` のボリューム マウントを定義し、20 GB のボリュームを探す PVC を作成します。 

    この要求は、前の手順で共有を作成したときに静的にプロビジョニングされた既存の PV によって満たされます。 デバイスで、共有ごとに 32 TB の大きな PV が作成されます。 PV は PVC によって規定された要件を満たしており、PVC をこの PV にバインドする必要があります。

    次の `mysql-deployment.yml` ファイルをコピーし、Azure Stack Edge Pro デバイスへのアクセスに使用する Windows クライアント上のフォルダーに保存します。
    
    ```yml
    apiVersion: v1
    kind: Service
    metadata:
      name: mysql
    spec:
      ports:
      - port: 3306
      selector:
        app: mysql
      clusterIP: None
    ---
    apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
    kind: Deployment
    metadata:
      name: mysql
    spec:
      selector:
        matchLabels:
          app: mysql
      strategy:
        type: Recreate
      template:
        metadata:
          labels:
            app: mysql
        spec:
          containers:
          - image: mysql:5.6
            name: mysql
            env:
              # Use secret in real usage
            - name: MYSQL_ROOT_PASSWORD
              value: password
            ports:
            - containerPort: 3306
              name: mysql
            volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
          volumes:
          - name: mysql-persistent-storage
            persistentVolumeClaim:
              claimName: mysql-pv-claim
    ```
    
2. 次の内容をコピーし、`mysql-deployment.yml` を保存したフォルダーに `mysql-pv.yml` ファイルとして保存します。 以前に `kubectl` で作成した SMB 共有を使用するには、PVC オブジェクトの `volumeName` フィールドをその共有の名前に設定します。 

    > [!NOTE] 
    > YAML ファイルが正しくインデントされていることを確認します。 [YAML lint](http://www.yamllint.com/) を使用すると、検証して保存することができます。
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim
    spec:
      volumeName: <smb-share-name-here>
      storageClassName: ""
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. `mysql-pv.yaml` ファイルをデプロイします。

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    デプロイの出力例を次に示します。

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pv.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim created
    
    C:\Users\user>
    ```
   作成された PVC の名前をメモします。 これは後の手順で使用します。 

4. `mysql-deployment.yml` ファイルの内容をデプロイします。

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    デプロイの出力例を次に示します。
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
        service/mysql created
        deployment.apps/mysql created
    ```
    
5. デプロイについての情報を表示します。

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Tue, 18 Aug 2020 09:44:58 -0700
    Labels:             <none>
    Annotations:        deployment.kubernetes.io/revision: 1
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"mysql","namespace":"userns1"},"spec":{"selector":{"matchL...
    Selector:           app=mysql
    Replicas:           1 desired | 1 updated | 1 total | 1 available | 0 unavailable
    StrategyType:       Recreate
    MinReadySeconds:    0
    Pod Template:
      Labels:  app=mysql
      Containers:
       mysql:
        Image:      mysql:5.6
        Port:       3306/TCP
        Host Port:  0/TCP
        Environment:
          MYSQL_ROOT_PASSWORD:  password
        Mounts:
          /var/lib/mysql from mysql-persistent-storage (rw)
      Volumes:
       mysql-persistent-storage:
        Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
        ClaimName:  mysql-pv-claim
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Progressing    True    NewReplicaSetAvailable
      Available      True    MinimumReplicasAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-c85f7f79c (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  10m   deployment-controller  Scaled up replica set mysql-c85f7f79c to 1
    
    C:\Users\user>
    ```
    

6. デプロイによって作成されたポッドを一覧表示します。

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    出力例を次に示します。

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                    READY   STATUS    RESTARTS   AGE
    mysql-c85f7f79c-vzz7j   1/1     Running   1          14m
    
    C:\Users\user>
    ```
    
7. PersistentVolumeClaim を検査します。

    `kubectl describe pvc <your-pvc-name>`

    出力例を次に示します。

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim -n userns1
    Name:          mysql-pv-claim
    Namespace:     userns1
    StorageClass:
    Status:        Bound
    Volume:        mylocalsmbshare1
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim","namespace":"userns1"},"spec":{"acc...
                   pv.kubernetes.io/bind-completed: yes
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      32Ti
    Access Modes:  RWO,RWX
    VolumeMode:    Filesystem
    Mounted By:    mysql-c85f7f79c-vzz7j
    Events:        <none>
    
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>MySQL が実行中であることを確認する


MySQL を実行しているポッド内のコンテナーに対してコマンドを実行するには、次のように入力します。

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql`

出力例を次に示します。

```powershell
C:\Users\user>kubectl exec mysql-c85f7f79c-vzz7j -i -t -n userns1 -- mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.6.49 MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

## <a name="delete-a-deployment"></a>デプロイの削除

デプロイを削除するには、デプロイされたオブジェクトの名前を指定して削除します。 これらのオブジェクトには、デプロイ、サービス、および PVC が含まれます。
 
```powershell
kubectl delete deployment <deployment-name>,svc <service-name> -n <your-namespace>
kubectl delete pvc <your-pvc-name> -n <your-namespace>
```

デプロイとサービスを削除した場合の出力例を次に示します。

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```
PVC を削除した場合の出力例を次に示します。

```powershell
C:\Users\user>kubectl delete pvc mysql-pv-claim -n userns1
persistentvolumeclaim "mysql-pv-claim" deleted
C:\Users\user>
```

PVC が削除されると、PV が PVC にバインドされなくなります。 共有の作成時に PV がプロビジョニングされたため、共有を削除する必要があります。 次の手順に従います。

1. 共有のマウントを解除します。 Azure portal で、 **[Azure Stack Edge リソース] > [共有]** に移動し、マウントを解除する共有を選択してクリックします。 **[マウント解除]** を選択し、操作を確定します。 共有がマウント解除されるまで待ちます。 マウント解除によって、Kubernetes クラスターから共有 (および関連付けられている PersistentVolume) が解放されます。 

    ![PV のローカル共有のマウント解除](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/unmount-edge-local-share-1.png)

1. これで、 **[削除]** を選択して削除を確定し、共有を削除することができます。 これにより、共有および対応する PV も削除されます。

    ![PV のローカル共有の削除](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/delete-edge-local-share-1.png)


## <a name="next-steps"></a>次のステップ

ストレージを動的にプロビジョニングする方法については、[Azure Stack Edge Pro デバイスでの動的プロビジョニングを使用したステートフル アプリケーションのデプロイ](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md)に関する記事を参照してください
