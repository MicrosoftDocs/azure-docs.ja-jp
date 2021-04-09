---
title: Kubernetes で Azure SQL Edge コンテナーをデプロイする - Azure SQL Edge
description: Kubernetes での Azure SQL Edge コンテナーのデプロイについて説明します
keywords: SQL Edge, コンテナー, Kubernetes
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 31a454c93ad5192f387306a8ec557c4e4d3ae991
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395293"
---
# <a name="deploy-an-azure-sql-edge-container-in-kubernetes"></a>Kubernetes で Azure SQL Edge コンテナーをデプロイする

Azure SQL Edge は、Kubernetes で実行される Azure IoT Edge の IoT Edge モジュールまたはスタンドアロン コンテナー ポッドの両方として、Kubernetes クラスターにデプロイできます。 この記事の残りの部分では、Kubernetes クラスター上のスタンドアロン コンテナーのデプロイに焦点を当てます。 Kubernetes での Azure IoT Edge のデプロイの詳細については、「[Azure IoT Edge on Kubernetes (プレビュー)](https://microsoft.github.io/iotedge-k8s-doc/introduction.html)」を参照してください。

このチュートリアルでは、Kubernetes クラスター上のコンテナーに高可用性 Azure SQL Edge インスタンスを構成する方法について説明します。 

> [!div class="checklist"]
> * SA パスワードを作成する
> * ストレージを作成する
> * 配置を作成する
> * SQL Server Management Studio (SSMS) と接続する
> * 障害と復旧を検証する

Kubernetes 1.6 以降では、[ストレージ クラス](https://kubernetes.io/docs/concepts/storage/storage-classes/)、[永続ボリューム要求](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims)、および [Azure ディスク ボリューム タイプ](https://github.com/kubernetes/examples/tree/master/staging/volumes/azure_disk)がサポートされています。 Kubernetes でネイティブに Azure SQL Edge インスタンスを作成して管理できます。 この記事の例では、共有ディスクのフェールオーバー クラスター インスタンスと同様の高可用性構成を実現するために、[配置](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)を作成する方法を示します。 この構成では、Kubernetes はクラスター オーケストレーターの役割を果たします。 コンテナー内の Azure SQL Edge インスタンスで障害が発生すると、オーケストレーターにより、同じ永続ストレージに接続されるコンテナーの別のインスタンスがブートストラップされます。

![Kubernetes クラスター内の Azure SQL Edge](media/deploy-kubernetes/kubernetes-sql-edge.png)

上の図で、`azure-sql-edge` は[ポッド](https://kubernetes.io/docs/concepts/workloads/pods/pod/)内のコンテナーです。 Kubernetes により、クラスター内のリソースが調整されます。 [レプリカ セット](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)によって、ノード障害が発生した後でポッドが自動的に復旧されます。 アプリケーションがサービスに接続します。 このケースでは、サービスは、`azure-sql-edge` の障害後も変化しない IP アドレスがホストされているロード バランサーを表します。

次の図では、`azure-sql-edge` コンテナーで障害が発生しています。 オーケストレーターとしての Kubernetes により、レプリカ セット内に適切な数の正常なインスタンスが存在することが保証され、構成に従って新しいコンテナーが開始されます。 オーケストレーターによって同じノード上で新しいポッドを開始され、`azure-sql-edge` によって同じ永続ストレージに再接続されます。 サービスは、再作成された `azure-sql-edge` に接続されます。

![ポッドで障害が発生した後の Kubernetes クラスター内の Azure SQL Edge](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

次の図では、`azure-sql-edge` コンテナーがホストされているノードで障害が発生しています。 オーケストレーターによって異なるノード上で新しいポッドを開始され、`azure-sql-edge` によって同じ永続ストレージに再接続されます。 サービスは、再作成された `azure-sql-edge` に接続されます。

![ノードで障害が発生した後の Kubernetes クラスター内の Azure SQL Edge](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png)

## <a name="prerequisites"></a>前提条件

* **Kubernetes クラスター**
   - このチュートリアルでは、Kubernetes クラスターが必要です。 この手順では、[kubectl](https://kubernetes.io/docs/user-guide/kubectl/) を使用してクラスターを管理します。 

   - このチュートリアルでは、Azure Kubernetes Service を使用して Azure SQL Edge をデプロイします。 `kubectl` を使って AKS に単一ノード Kubernetes クラスターを作成して接続する方法については、[Azure Kubernetes Service (AKS) クラスターのデプロイ](../aks/tutorial-kubernetes-deploy-cluster.md)に関する記事をご覧ください。 

   >[!NOTE]
   >ノード障害から保護するため、Kubernetes クラスターには複数のノードが必要です。

* **Azure CLI**
   - このチュートリアルの手順は、Azure CLI 2.10.1 に対して検証されています。

## <a name="create-a-kubernetes-namespace-for-sql-edge-deployment"></a>SQL Edge をデプロイするための Kubernetes 名前空間を作成する

Kubernetes クラスターに新しい名前空間を作成します。 この名前空間を使用して、SQL Edge と、必要なすべての成果物がデプロイされます。 Kubernetes 名前空間の詳細については、「[名前空間](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)」を参照してください。

   ```azurecli
   kubectl create namespace <namespace name>
   ```  

## <a name="create-an-sa-password"></a>SA パスワードを作成する

Kubernetes クラスターで SA パスワードを作成します。 Kubernetes では、パスワードなどの機密構成情報を[シークレット](https://kubernetes.io/docs/concepts/configuration/secret/)として管理できます。

次のコマンドでは、SA アカウントのパスワードが作成されます。

   ```azurecli
   kubectl create secret generic mssql --from-literal=SA_PASSWORD="MyC0m9l&xP@ssw0rd" -n <namespace name>
   ```  

   `MyC0m9l&xP@ssw0rd` は複雑なパスワードに置き換えます。

## <a name="create-storage"></a>ストレージを作成する

Kubernetes クラスターで、[永続ボリューム](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)と[永続ボリューム要求](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistent-volume-claim-protection)を構成します。 次の手順を実行します。 

1. マニフェストを作成して、ストレージ クラスと永続ボリューム要求を定義します。  マニフェストでは、ストレージ プロビジョナー、パラメーター、[再要求ポリシー](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#reclaiming)を指定します。 Kubernetes クラスターでは、このマニフェストを使って、永続ストレージが作成されます。 

   次の yaml の例では、ストレージ クラスと永続ボリューム要求が定義されています。 この Kubernetes クラスターは Azure 内にあるため、ストレージ クラス プロビジョナーは `azure-disk` です。 ストレージ アカウントの種類は `Standard_LRS` です。 永続ボリューム要求は `mssql-data` という名前です。 永続ボリューム要求のメタデータには、それをストレージ クラスに接続するための注釈が含まれています。 

   ```yaml
   kind: StorageClass
   apiVersion: storage.k8s.io/v1
   metadata:
        name: azure-disk
   provisioner: kubernetes.io/azure-disk
   parameters:
     storageaccounttype: Standard_LRS
     kind: managed
   ---
   kind: PersistentVolumeClaim
   apiVersion: v1
   metadata:
     name: mssql-data
     annotations:
       volume.beta.kubernetes.io/storage-class: azure-disk
   spec:
     accessModes:
     - ReadWriteOnce
     resources:
       requests:
         storage: 8Gi
   ```

   ファイルを保存します (例: **pvc.yaml**)。

2. Kubernetes で永続ボリューム要求を作成します。

   ```azurecli
   kubectl apply -f <Path to pvc.yaml file> -n <namespace name>
   ```

   `<Path to pvc.yaml file>` は、ファイルを保存した場所です。

   永続ボリュームが Azure ストレージ アカウントとして自動的に作成され、永続ボリューム要求にバインドされます。 

    ![永続ボリューム要求コマンドのスクリーンショット](media/deploy-kubernetes/pvc-cmd.png)

3. 永続ボリューム要求を確認します。

   ```azurecli
   kubectl describe pvc <PersistentVolumeClaim>  -n <name of the namespace>
   ```

   `<PersistentVolumeClaim>` は、永続ボリューム要求の名前です。

   前のステップでは、永続ボリューム要求に `mssql-data` という名前が指定されています。 永続ボリューム要求に関するメタデータを表示するには、次のコマンドを実行します。

   ```azurecli
   kubectl describe pvc mssql-data  -n <namespace name>
   ```

   返されるメタデータには、`Volume` という名前の値が含まれます。 この値は、BLOB の名前にマップされます。

   ![Volume を含む、返されたメタデータのスクリーンショット](media/deploy-kubernetes/describe-volume.png)

4. 永続ボリュームを確認します。

   ```azurecli
   kubectl describe pv -n <namespace name>
   ```

   `kubectl` では、自動的に作成されて永続ボリューム要求にバインドされた永続ボリュームに関するメタデータが返されます。 

## <a name="create-the-deployment"></a>配置を作成する

この例では、Azure SQL Edge インスタンスをホストするコンテナーを、Kubernetes デプロイ オブジェクトとして記述しています。 配置ではレプリカ セットが作成されます。 レプリカ セットによってポッドが作成されます。 

この手順では、Azure SQL Edge Docker イメージに基づいてコンテナーを記述するマニフェストを作成します。 マニフェストでは、`mssql-data` 永続ボリューム要求と、Kubernetes クラスターに既に適用されている `mssql` シークレットが参照されます。 また、マニフェストでは[サービス](https://kubernetes.io/docs/concepts/services-networking/service/)についても記述されています。 このサービスはロード バランサーです。 ロード バランサーにより、Azure SQL Edge インスタンスの復旧後も IP アドレスが維持されることが保証されます。 

1. 配置を記述するマニフェスト (YAML ファイル) を作成します。 次の例では、Azure SQL Edge コンテナー イメージに基づくコンテナーを含むデプロイが記述されています。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sqledge-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sqledge
  template:
    metadata:
      labels:
        app: sqledge
    spec:
      volumes:
        - name: sqldata
          persistentVolumeClaim:
            claimName: mssql-data
      containers:
        - name: azuresqledge
          image: mcr.microsoft.com/azure-sql-edge:latest
          ports:
            - containerPort: 1433
          volumeMounts:
            - name: sqldata
              mountPath: /var/opt/mssql
          env:
            - name: MSSQL_PID
              value: "Developer"
            - name: ACCEPT_EULA
              value: "Y"
            - name: SA_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mssql
                  key: SA_PASSWORD
            - name: MSSQL_AGENT_ENABLED
              value: "TRUE"
            - name: MSSQL_COLLATION
              value: "SQL_Latin1_General_CP1_CI_AS"
            - name: MSSQL_LCID
              value: "1033"
      terminationGracePeriodSeconds: 30
      securityContext:
        fsGroup: 10001
---
apiVersion: v1
kind: Service
metadata:
  name: sqledge-deployment
spec:
  selector:
    app: sqledge
  ports:
    - protocol: TCP
      port: 1433
      targetPort: 1433
      name: sql
  type: LoadBalancer
```

   前のコードを、`sqldeployment.yaml` という名前の新しいファイルにコピーします。 次の値を更新します。 

   * MSSQL_PID `value: "Developer"`: Azure SQL Edge Developer Edition を実行するようにコンテナーを設定します。 Developer Edition には、運用データ用のライセンスは付与されません。 デプロイを運用環境で使用する場合は、エディションを `Premium` に設定します。 

      >[!NOTE]
      >詳細については、[Azure SQL Edge のライセンス方法](https://azure.microsoft.com/pricing/details/sql-edge/)に関する記事を参照してください。

   * `persistentVolumeClaim`:この値には、永続ボリューム要求に使用される名前にマップされる `claimName:` のエントリが必要です。 このチュートリアルでは `mssql-data` を使用します。 

   * `name: SA_PASSWORD`:このセクションで定義されているように、コンテナー イメージを構成して SA パスワードを設定します。

     ```yaml
     valueFrom:
       secretKeyRef:
         name: mssql
         key: SA_PASSWORD 
     ```

     Kubernetes では、コンテナーを配置するときに、`mssql` という名前のシークレットを参照してパスワードの値が取得されます。 

   >[!NOTE]
   >`LoadBalancer` サービスの種類を使うことにより、Azure SQL Edge インスタンスにポート 1433 で (インターネットを経由して) リモート アクセスできるようになります。

   ファイルを保存します (例: **sqledgedeploy.yaml**)。

2. 配置を作成します。

   ```azurecli
   kubectl apply -f <Path to sqledgedeploy.yaml file> -n <namespace name>
   ```

   `<Path to sqldeployment.yaml file>` は、ファイルを保存した場所です。

   ![配置コマンドのスクリーンショット](media/deploy-kubernetes/deploy-sql-cmd.png)

   配置とサービスが作成されます。 Azure SQL Edge インスタンスはコンテナー内にあり、永続ストレージに接続されています。

   ポッドの状態を表示するには、「`kubectl get pod -n <namespace name>`」と入力します。

   ![get pod コマンドのスクリーンショット](media/deploy-kubernetes/get-sql-pod-cmd.png)

   上の図では、ポッドの状態は `Running` です。 この状態は、コンテナーの準備ができていることを示します。 この処理には数分かかることがあります。

   >[!NOTE]
   >配置が作成された後、ポッドが表示されるまでに数分かかることがあります。 遅延は、クラスターで Docker ハブから Azure SQL Edge コンテナー イメージがプルされるためです。 イメージが初めてプルされた後の配置は、既にイメージがキャッシュされているノードに対するものの場合は、高速になる可能性があります。 

3. サービスが実行されていることを確認します。 次のコマンドを実行します。

   ```azurecli
   kubectl get services -n <namespace name>
   ```

   このコマンドでは、実行されているサービスと共に、サービスの内部および外部の IP アドレスも返されます。 `mssql-deployment` サービスの外部 IP アドレスを記録しておきます。 この IP アドレスを使って Azure SQL Edge に接続します。 

   ![get service コマンドのスクリーンショット](media/deploy-kubernetes/get-service-cmd.png)

   Kubernetes クラスター内のオブジェクトの状態に関する詳細情報を取得するには、以下を実行します。

   ```azurecli
   az aks browse --resource-group <MyResourceGroup> --name <MyKubernetesClustername>
   ```  

## <a name="connect-to-the-azure-sql-edge-instance"></a>Azure SQL Edge インスタンスに接続する

説明に従ってコンテナーを構成した場合は、Azure 仮想ネットワークの外部からアプリケーションに接続できます。 `sa` アカウントと、サービスの外部 IP アドレスを使います。 Kubernetes シークレットとして構成したパスワードを使います。 Azure SQL Edge インスタンスへの接続の詳細については、[Azure SQL Edgeへの接続](connect.md)に関する記事を参照してください。

## <a name="verify-failure-and-recovery"></a>障害と復旧を検証する

障害と復旧を確認するには、ポッドを削除します。 手順は次のとおりです。

1. Azure SQL Edge を実行しているポッドを一覧表示します。

   ```azurecli
   kubectl get pods -n <namespace name>
   ```

   Azure SQL Edge が実行されているポッドの名前をメモします。

2. ポッドを削除します。

   ```azurecli
   kubectl delete pod sqledge-deployment-7df66c9999-rc9xl
   ```
   `sqledge-deployment-7df66c9999-rc9xl` は、前のステップでポッド名に対して返された値です。 

Kubernetes によって、Azure SQL Edge インスタンスを復旧するためのポッドが自動的に再作成され、永続ストレージに接続されます。 新しいポッドが配置されたことを確認するには、`kubectl get pods` を使います。 新しいコンテナーの IP アドレスが同じであることを確認するには、`kubectl get services` を使います。 

## <a name="summary"></a>まとめ

このチュートリアルでは、高可用性を実現するために Azure SQL Edge コンテナーを Kubernetes クラスターに配置する方法について説明しました。 

> [!div class="checklist"]
> * SA パスワードを作成する
> * ストレージを作成する
> * 配置を作成する
> * Azure SQL Edge Management Studio (SSMS) と接続する
> * 障害と復旧を検証する

## <a name="next-steps"></a>次のステップ

- [Kubernetes の概要](../aks/intro-kubernetes.md)
- [SQL Edge での ONNX を使用した機械学習と人工知能](onnx-overview.md)。
- [IoT Edge を使用して SQL Edge でエンド ツー エンドの IoT ソリューションを構築する](tutorial-deploy-azure-resources.md)。
- [Azure SQL Edge でのデータ ストリーミング](stream-data.md)