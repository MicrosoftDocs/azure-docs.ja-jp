---
title: kubectl を使用し、Azure Stack Edge Pro GPU デバイスで動的にプロビジョニングされた共有を介して Kubernetes ステートフル アプリをデプロイする | Microsoft Docs
description: kubectl を使用し、Microsoft Azure Stack Edge Pro GPU デバイスで動的にプロビジョニングされた共有を介して Kubernetes ステートフル アプリケーションのデプロイを作成および管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 160ba6014bbb2d5cb3ed4e8e4b28a61fe5e8d4cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520695"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-storageclass-on-your-azure-stack-edge-pro-gpu-device"></a>kubectl を使用し、Azure Stack Edge Pro GPU デバイスで StorageClass を使用して Kubernetes ステートフル アプリケーションを実行する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、StorageClass を使用して Kubernetes で単一インスタンスのステートフル アプリケーションをデプロイし、ストレージとデプロイを動的にプロビジョニングする方法について説明します。 このデプロイでは、既存の Kubernetes クラスターで `kubectl` コマンドを使用し、MySQL アプリケーションをデプロイします。 

この手順は、[Azure Stack Edge Pro デバイス上の Kubernetes ストレージ](azure-stack-edge-gpu-kubernetes-storage.md)に関する記事を確認し、[Kubernetes ストレージ](https://kubernetes.io/docs/concepts/storage/)の概念を理解しているユーザーを対象としています。


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
        - サポートされている Kubernetes バージョンで提供されているマッピングで、これら 2 つのバージョンの互換性を確認します<!-- insert link-->. 


Azure Stack Edge Pro デバイスにステートフル アプリケーションをデプロイする準備ができました。 


## <a name="deploy-mysql"></a>MySQL をデプロイする

Kubernetes デプロイを作成し、PersistentVolumeClaim (PVC) を使用して組み込みの StorageClass に接続することによって、ステートフル アプリケーションを実行します。 

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

1. 次の YAML ファイルを使用します。 `mysql-deployment.yml` ファイルには、MySQL を実行し、PVC を参照するデプロイが記述されています。 このファイルは、`/var/lib/mysql` のボリューム マウントを定義し、20 GB のボリュームを探す PVC を作成します。 動的な PV がプロビジョニングされ、PVC がこの PV にバインドされます。

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
              claimName: mysql-pv-claim-sc
    ```
    
2. 次の内容をコピーし、`mysql-deployment.yml` を保存したフォルダーに `mysql-pvc.yml` ファイルとして保存します。 Azure Stack Edge Pro デバイスに接続されたデータ ディスクにある組み込みの StorageClass を使用するには、PVC オブジェクトの `storageClassName` フィールドを `ase-node-local` に設定し、accessModes を `ReadWriteOnce` にする必要があります。 

    > [!NOTE] 
    > YAML ファイルが正しくインデントされていることを確認します。 [YAML lint](http://www.yamllint.com/) を使用すると、検証して保存することができます。
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim-sc
    spec:
      storageClassName: ase-node-local
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. `mysql-pvc.yaml` ファイルをデプロイします。

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    デプロイの出力例を次に示します。

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pvc.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim-sc created
    C:\Users\user>
    ```
   作成された PVC の名前を確認してください。この例では `mysql-pv-claim-sc` です。 これは後の手順で使用します。

4. `mysql-deployment.yml` ファイルの内容をデプロイします。

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    デプロイの出力例を次に示します。
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
    service/mysql created
    deployment.apps/mysql created
    C:\Users\user>
    ```
    
5. デプロイについての情報を表示します。

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Thu, 20 Aug 2020 11:14:25 -0700
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
        ClaimName:  mysql-pv-claim-sc
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-695c4d9dcd (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  24s   deployment-controller  Scaled up replica set mysql-695c4d9dcd to 1
    C:\Users\user>
    ```
    

6. デプロイによって作成されたポッドを一覧表示します。

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    出力例を次に示します。

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                     READY   STATUS    RESTARTS   AGE
    mysql-695c4d9dcd-rvzff   1/1     Running   0          40s
    C:\Users\user>
    ```
    
7. PersistentVolumeClaim を検査します。

    `kubectl describe pvc <your-pvc-name>`

    出力例を次に示します。

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim-sc -n userns1
    Name:          mysql-pv-claim-sc
    Namespace:     userns1
    StorageClass:  ase-node-local
    Status:        Bound
    Volume:        pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim-sc","namespace":"userns1"},"spec":{"...
                   pv.kubernetes.io/bind-completed: yes
                   pv.kubernetes.io/bound-by-controller: yes
                   volume.beta.kubernetes.io/storage-provisioner: rancher.io/local-path
                   volume.kubernetes.io/selected-node: k8s-3q7lhq2cl-3q7lhq2
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      20Gi
    Access Modes:  RWO
    VolumeMode:    Filesystem
    Mounted By:    mysql-695c4d9dcd-rvzff
    Events:
      Type    Reason                 Age                From                                                                                                Message
      ----    ------                 ----               ----                                                                                                -------
      Normal  WaitForFirstConsumer   71s (x2 over 77s)  persistentvolume-controller                                                                         waiting for first consumer to be created before binding
      Normal  ExternalProvisioning   62s                persistentvolume-controller                                                                         waiting for a volume to be created, either by external provisioner "rancher.io/local-path" or manually created by system administrator
      Normal  Provisioning           62s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  External provisioner is provisioning volume for claim "userns1/mysql-pv-claim-sc"
      Normal  ProvisioningSucceeded  60s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  Successfully provisioned volume pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>MySQL が実行中であることを確認する

アプリケーションが実行されていることを確認するには、次のように入力します。

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql -p`

求められたら、管理者パスワードを入力します。 パスワードは `mysql-deployment` ファイルにあります。

出力例を次に示します。

```powershell
C:\Users\user>kubectl exec mysql-695c4d9dcd-rvzff -i -t -n userns1 -- mysql -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
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
C:\Users\user>kubectl delete pvc mysql-pv-claim-sc -n userns1
persistentvolumeclaim "mysql-pv-claim-sc" deleted
C:\Users\user>
```                                                                                         


## <a name="next-steps"></a>次のステップ

Kubectl を使用してネットワークを構成する方法については、[Azure Stack Edge Pro デバイスへのステートレス アプリケーションのデプロイ](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)に関する記事を参照してください