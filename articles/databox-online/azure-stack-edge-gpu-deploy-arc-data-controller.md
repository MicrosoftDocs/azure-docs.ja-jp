---
title: Azure Arc データ コントローラーを Azure Stack Edge Pro GPU デバイスにデプロイする | Microsoft Docs
description: Azure Arc データ コントローラーと Azure Data Services を Azure Stack Edge Pro GPU デバイスにデプロイする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 57633df8c6482a9b0645813519991282bdbf22c1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102633514"
---
# <a name="deploy-azure-data-services-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Data Services を Azure Stack Edge Pro GPU デバイスにデプロイする

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Arc データ コントローラーを作成した後、Azure Data Services を Azure Stack Edge Pro GPU デバイスにデプロイするプロセスについて説明します。 

Azure Arc データ コントローラーは、カスタマー マネージド環境で Azure Data Services を使用できるようにするローカル コントロール プレーンです。 Azure Stack Edge Pro デバイス上で実行されている Kubernetes クラスターで Azure Arc Data Controller を作成すると、そのデータ コントローラーに SQL Managed Instance (プレビュー) などの Azure Data Services をデプロイできます。

データ コントローラーを作成して SQL Managed Instance をデプロイする手順には、PowerShell と、デバイス上の Kubernetes クラスターにコマンド ラインでアクセスできるネイティブ ツールである `kubectl` を使用します。


## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

1. [Activate Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md) に関する記事で説明されているように、Azure Stack Edge Pro デバイスにアクセスでき、デバイスをアクティブにしてあること。

1. デバイスでコンピューティング ロールが有効になっていること。 Kubernetes クラスターは、[Azure Stack Edge Pro デバイスでのコンピューティングの構成](azure-stack-edge-gpu-deploy-configure-compute.md)に関するページの手順に従ってデバイスでコンピューティングを構成したときにもそのデバイスに作成されています。

1. ローカル Web UI の **[デバイス]** ページで Kubernetes API エンドポイントを取得していること。 詳細については、[Kubernetes API エンドポイントの取得](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)に関するセクションの手順を参照してください。

1. デバイスに接続するクライアントにアクセスできること。 
    1. この記事では、PowerShell 5.0 以降が実行されている Windows クライアント システムを使用して、デバイスにアクセスします。 [サポートされるオペレーティング システム](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)を備えた他のクライアントを使用することもできます。 
    1. クライアントに `kubectl` をインストールします。 クライアント バージョンの場合:
        1. デバイスにインストールされている Kubernetes サーバーのバージョンを確認します。 デバイスのローカル UI で、 **[ソフトウェア更新プログラム]** ページに移動します。 このページで、**Kubernetes サーバーのバージョン** を記録します。
        1. マスターとのマイナー バージョンの差が 1 未満のクライアントをダウンロードしてください。 ただし、クライアントのバージョンは、マスターよりも最大 1 マイナー バージョン上位であってもかまいません。 たとえば、v1.3 のマスターは v1.1、v1.2、v1.3 のノードで動作し、v1.2、v1.3、v1.4 のクライアントで動作します。 Kubernetes クライアント バージョンの詳細については、「[Kubernetes バージョンとバージョン スキュー サポート ポリシー](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew)」を参照してください。
    
1. 必要に応じて、[Azure Arc 対応データ サービスをデプロイおよび管理するためのクライアント ツールをインストールします](../azure-arc/data/install-client-tools.md)。 これらのツールは必須ではありませんが、推奨されます。  
1. データ コントローラーと 1 つの SQL マネージド インスタンスをプロビジョニングするのに十分なリソースがデバイスにあることを確認します。 データ コントローラーと 1 つの SQL マネージド インスタンスには、少なくとも 16 GB の RAM と 4 つの CPU コアが必要です。 詳細なガイダンスについては、[Azure Arc 対応データ サービスのデプロイの最小要件](../azure-arc/data/sizing-guidance.md#minimum-deployment-requirements)に関するページを参照してください。


## <a name="configure-kubernetes-external-service-ips"></a>Kubernetes 外部サービスの IP を構成する

1. デバイスのローカル Web UI に移動した後、 **[Compute]** に移動します。
1. コンピューティングが有効になっているネットワークを選択します。 

    ![ローカル UI の [コンピューティング] ページ 2](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-1.png)

1. 3 つの Kubernetes 外部サービス IP を追加します (他の外部サービスまたはコンテナー用に既に構成してある IP に加えて)。 データ コントローラーによって 2 つのサービス IP が使用され、3 番目の IP は SQL Managed Instance を作成するときに使用します。 デプロイするデータ サービスを 1 つ追加するごとに、1 つの IP が必要になります。 

    ![ローカル UI の [コンピューティング] ページ 3](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-2.png)

1. 設定を適用すると、これらの新しい IP は既存の Kubernetes クラスターですぐに有効になります。 


## <a name="deploy-azure-arc-data-controller"></a>Azure Arc データ コントローラーをデプロイする

データ コントローラーをデプロイする前に、名前空間を作成する必要があります。

### <a name="create-namespace"></a>[名前空間の作成] 

新しい専用の名前空間を作成し、そこにデータ コントローラーをデプロイします。 また、ユーザーを作成し、作成した名前空間へのアクセス権をユーザーに付与します。 

> [!NOTE]
> 名前空間とユーザー名の両方に、[DNS サブドメインの命名規則](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names)が適用されます。

1. [PowerShell インターフェイスに接続する](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。
1. 名前空間を作成する。 型:

    `New-HcsKubernetesNamespace -Namespace <Name of namespace>`

1. ユーザーを作成します。 型: 

    `New-HcsKubernetesUser -UserName <User name>`

1. 構成ファイルはプレーンテキストで表示されます。 このファイルをコピーし、*config* ファイルとして保存します。 

    > [!IMPORTANT]
    > 構成ファイルは *.txt* ファイルとして保存せず、ファイル拡張子なしで保存してください。

1. この構成ファイルは、ローカル コンピューター上のユーザー プロファイルの `.kube` フォルダーに存在する必要があります。 このファイルをユーザー プロファイルのそのフォルダーにコピーします。

    ![クライアント上の構成ファイルの場所](media/azure-stack-edge-gpu-create-kubernetes-cluster/location-config-file.png)
1. 作成した名前空間へのアクセス権をユーザーに付与します。 型: 

    `Grant-HcsKubernetesNamespaceAccess -Namespace <Name of namespace> -UserName <User name>`

    上記のコマンドの出力例を次に示します。 この例では、`myadstest` 名前空間と `myadsuser` ユーザーを作成し、その名前空間へのアクセス権をユーザーに付与します。
    
    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesNamespace -Namespace myadstest
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName myadsuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBD=======//snipped//=======VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: myadsuser
      name: myadsuser@kubernetes
    current-context: myadsuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: myadsuser
      user:
        client-certificate-data: LS0tLS1CRUdJTiBDRV=========//snipped//=====EE9PQotLS0kFURSBLRVktLS0tLQo=
    
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myadstest -UserName myadsuser
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS>
    ```
1. システム上の hosts ファイルに DNS エントリを追加します。 

    1. メモ帳を管理者として実行し、`C:\windows\system32\drivers\etc\hosts` にある `hosts` ファイルを開きます。 
    2. ローカル UI (前提条件) の **[デバイス]** ページから保存した情報を使用して、hosts ファイルにエントリを作成します。 

        たとえば、このエンドポイント `https://compute.myasegpudev.microsoftdatabox.com/[10.100.10.10]` をコピーして、デバイスの IP アドレスと DNS ドメインを含む次のエントリを作成します。 

        `10.100.10.10 compute.myasegpudev.microsoftdatabox.com`

1. Kubernetes ポッドに接続できることを確認するには、cmd プロンプトまたは PowerShell セッションを開始します。 型:
    
    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
    No resources found.
    PS C:\WINDOWS\system32>
    ```
これで、データ コントローラーとデータ サービス アプリケーションを名前空間にデプロイし、アプリケーションとそのログを表示できます。

### <a name="create-data-controller"></a>データ コントローラーの作成

データ コントローラーは、API、コントローラー サービス、ブートストラップ、監視データベースおよびダッシュボードを提供するために Kubernetes クラスターにデプロイされるポッドのコレクションです。 次の手順のようにして、前に作成した名前空間の Azure Stack Edge デバイスに存在する Kubernetes クラスターにデータ コントローラーを作成します。   

1. データ コントローラーを作成するために必要な次の情報を収集します。

    
    |列 1  |列 2  |
    |---------|---------|
    |データ コントローラー名     |データ コントローラーのわかりやすい名前。 たとえば、「 `arctestdatacontroller` 」のように入力します。         |
    |データ コントローラーのユーザー名     |データ コントローラー管理者ユーザーの任意のユーザー名。 データ コントローラーのユーザー名とパスワードは、管理機能を実行するためにデータ コントローラー API に対する認証を行うために使用されます。          |
    |データ コントローラーのパスワード     |データ コントローラー管理者ユーザーのパスワード。 安全なパスワードを選択し、クラスター管理者の特権を必要としているユーザーとのみ共有します。         |
    |Kubernetes 名前空間の名前     |データ コントローラーを作成する Kubernetes 名前空間の名前。         |
    |Azure サブスクリプション ID     |Azure 内のデータ コントローラー リソースを作成する場所の Azure サブスクリプション GUID。         |
    |Azure リソース グループ名     |Azure 内のデータ コントローラー リソースを作成するリソース グループの名前。         |
    |Azure の場所     |Azure でデータ コントローラー リソース メタデータが格納される Azure の場所。 利用可能なリージョンの一覧については、「Azure グローバル インフラストラクチャ/リージョン別の製品」を参照してください。|


1. PowerShell インターフェイスに接続する。 データ コントローラーを作成するには、次のように入力します。 

    ```powershell
    Set-HcsKubernetesAzureArcDataController -SubscriptionId <Subscription ID> -ResourceGroupName <Resource group name> -Location <Location without spaces> -UserName <User you created> -Password <Password to authenticate to Data Controller> -DataControllerName <Data Controller Name> -Namespace <Namespace you created>    
    ```
    上記のコマンドの出力例を次に示します。

    ```powershell
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS> 
    ```
    
    デプロイが完了するまでに 5 分くらいかかることがあります。

    > [!NOTE]
    > Azure Stack Edge Pro デバイス上の Kubernetes クラスターに作成されたデータ コントローラーは、現在のリリースでは切断モードでのみ動作します。

### <a name="monitor-data-creation-status"></a>データ作成の状態を監視する

1. 別の PowerShell ウィンドウを開きます。
1. データ コントローラーの作成状態を監視するには、次の `kubectl` コマンドを使用します。 

    ```powershell
    kubectl get datacontroller/<Data controller name> --namespace <Name of your namespace>
    ```
    コントローラーが作成されると、状態は `Ready` になります。
    上記のコマンドの出力例を次に示します。

    ```powershell
    PS C:\WINDOWS\system32> kubectl get datacontroller/arctestcontroller --namespace myadstest
    NAME                STATE
    arctestcontroller   Ready
    PS C:\WINDOWS\system32>
    ```
1. データ コントローラーで実行されている外部サービスに割り当てられている IP を確認するには、`kubectl get svc -n <namespace>` コマンドを使用します。 出力例を次に示します。

    ```powershell
    PS C:\WINDOWS\system32> kubectl get svc -n myadstest
    NAME                      TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                       AGE
    controldb-svc             ClusterIP      172.28.157.130   <none>        1433/TCP,8311/TCP,8411/TCP                    3d21h
    controller-svc            ClusterIP      172.28.123.251   <none>        443/TCP,8311/TCP,8301/TCP,8411/TCP,8401/TCP   3d21h
    controller-svc-external   LoadBalancer   172.28.154.30    10.57.48.63   30080:31090/TCP                               3d21h
    logsdb-svc                ClusterIP      172.28.52.196    <none>        9200/TCP,8300/TCP,8400/TCP                    3d20h
    logsui-svc                ClusterIP      172.28.85.97     <none>        5601/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdb-svc             ClusterIP      172.28.255.103   <none>        8086/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdc-svc             ClusterIP      172.28.208.191   <none>        8300/TCP,8400/TCP                             3d20h
    metricsui-svc             ClusterIP      172.28.158.163   <none>        3000/TCP,8300/TCP,8400/TCP                    3d20h
    mgmtproxy-svc             ClusterIP      172.28.228.229   <none>        443/TCP,8300/TCP,8311/TCP,8400/TCP,8411/TCP   3d20h
    mgmtproxy-svc-external    LoadBalancer   172.28.166.214   10.57.48.64   30777:30621/TCP                               3d20h
    sqlex-svc                 ClusterIP      None             <none>        1433/TCP                                      3d20h
    PS C:\WINDOWS\system32>
    ```

## <a name="deploy-sql-managed-instance"></a>SQL マネージド インスタンスをデプロイする

データ コントローラーが正常に作成されたら、テンプレートを使用してデータ コントローラーに SQL Managed Instance をデプロイできます。

### <a name="deployment-template"></a>デプロイ テンプレート

次のデプロイ テンプレートを使用して、デバイスのデータ コントローラーに SQL Managed Instance をデプロイします。

```yml
apiVersion: v1
data:
    password: UGFzc3dvcmQx
    username: bXlhZHN1c2Vy
kind: Secret
metadata:
    name: sqlex-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
    name: sqlex
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
    service:
    type: LoadBalancer
    storage:
    backups:
        className: ase-node-local
        size: 5Gi
    data:
        className: ase-node-local
        size: 5Gi
    datalogs:
        className: ase-node-local
        size: 5Gi
    logs:
        className: ase-node-local
        size: 1Gi
```


#### <a name="metadata-name"></a>メタデータ名
    
メタデータ名は、SQL Managed Instance の名前です。 前の `deployment.yaml` で関連付けられたポッドの名前は `sqlex-n` になります (`n` は、アプリケーションに関連付けられているポッドの数です)。 
    
#### <a name="password-and-username-data"></a>パスワードとユーザー名のデータ

データ コントローラーのユーザー名とパスワードは、管理機能を実行するためにデータ コントローラー API に対する認証を行うために使用されます。 デプロイ テンプレートに含まれるデータ コントローラーのユーザー名とパスワードの Kubernetes シークレットは、base64 でエンコードされた文字列です。 

オンライン ツールを使用して、希望するユーザー名とパスワードを base64 でエンコードできます。また、プラットフォームに応じて組み込みの CLI ツールを使用することもできます。 オンラインの Base64 エンコード ツールを使用するときは、ツールでユーザー名とパスワードの文字列 (データ コントローラーの作成時に入力したもの) を入力すると、Base64 でエンコードされた対応する文字列がツールによって生成されます。
    
#### <a name="service-type"></a>サービスの種類

サービスの種類は `LoadBalancer` に設定する必要があります。
    
#### <a name="storage-class-name"></a>ストレージ クラス名

デプロイによってデータ、バックアップ、データ ログ、ログに使用される Azure Stack Edge デバイス上のストレージ クラスを確認できます。 デバイスにデプロイされているストレージ クラスを取得するには、`kubectl get storageclass` コマンドを使用します。

```powershell
PS C:\WINDOWS\system32> kubectl get storageclass
NAME             PROVISIONER      RECLAIMPOLICY  VOLUMEBINDINGMODE     ALLOWVOLUMEEXPANSION   AGE
ase-node-local   rancher.io/local-path   Delete  WaitForFirstConsumer  false                  5d23h
PS C:\WINDOWS\system32>
```
前の出力例では、デバイスのストレージ クラス `ase-node-local` がテンプレートで指定されています。
 
#### <a name="spec"></a>仕様

Azure Stack Edge デバイスに SQL マネージド インスタンスを作成するには、`deployment.yaml` の spec セクションでメモリと CPU の要件を指定できます。 次の例に示すように、SQL マネージド インスタンスごとに少なくとも 2 GB のメモリと 1 つの CPU コアを要求する必要があります。 

```yml
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
```  

データ コントローラーと 1 つの SQL マネージド インスタンスのサイズに関する詳細なガイダンスについては、「[SQL Managed Instance のサイズ設定の詳細](../azure-arc/data/sizing-guidance.md#sql-managed-instance-sizing-details)」を参照してください。

### <a name="run-deployment-template"></a>デプロイ テンプレートを実行する

次のコマンドを使用して、`deployment.yaml` を実行します。

```powershell
kubectl create -n <Name of namespace that you created> -f <Path to the deployment yaml> 
```

次のコマンドの出力例を以下に示します。

```powershell
PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
No resources found.
PS C:\WINDOWS\system32> 
PS C:\WINDOWS\system32> kubectl create -n myadstest -f C:\azure-arc-data-services\sqlex.yaml  secret/sqlex-login-secret created
sqlmanagedinstance.sql.arcdata.microsoft.com/sqlex created
PS C:\WINDOWS\system32> kubectl get pods --namespace myadstest
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-mv2cd   1/1     Running   0          83m
control-w9s9l        2/2     Running   0          78m
controldb-0          2/2     Running   0          78m
controlwd-4bmc5      1/1     Running   0          64m
logsdb-0             1/1     Running   0          64m
logsui-wpmw2         1/1     Running   0          64m
metricsdb-0          1/1     Running   0          64m
metricsdc-fb5r5      1/1     Running   0          64m
metricsui-56qzs      1/1     Running   0          64m
mgmtproxy-2ckl7      2/2     Running   0          64m
sqlex-0              3/3     Running   0          13m
PS C:\WINDOWS\system32>
```

出力例の `sqlex-0` ポッドは、SQL マネージド インスタンスの状態を示します。

## <a name="remove-data-controller"></a>データ コントローラーを削除する

データ コントローラーを削除するには、それをデプロイした専用の名前空間を削除します。


```powershell
kubectl delete ns <Name of your namespace>
```


## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge Pro にステートレス アプリケーションをデプロイする](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md)。
