---
title: Kubernetes ツールを使用したデータ コントローラーの作成
description: Kubernetes ツールを使用したデータ コントローラーの作成
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 500587dc6564aa55eb430365eb67bb958bbd2482
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519981"
---
# <a name="create-azure-arc-data-controller-using-kubernetes-tools"></a>Kubernetes ツールを使用した Azure Arc データ コントローラーの作成

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>前提条件

概要情報については、「[Azure Arc データ コントローラーを作成する](create-data-controller.md)」のトピックを確認してください。

Kubernetes ツールを使用して Azure Arc データ コントローラーを作成するには、Kubernetes ツールがインストールされている必要があります。  この記事の例では `kubectl` を使用しますが、Kubernetes ダッシュボード、`oc`、`helm` などの Kubernetes の他のツールや Kubernetes yaml/json を使い慣れている場合は、それらのツールでも同様のアプローチを使用できます。

[kubectl ツールをインストールする](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

> [!NOTE]
> 以下に示す Azure Arc データ コントローラーを作成する手順の一部では、Kubernetes クラスター管理者のアクセス許可が必要です。  Kubernetes クラスター管理者でない場合、代わりにそれらのステップを実行するには、Kubernetes クラスター管理者の権限が必要です。

### <a name="cleanup-from-past-installations"></a>以前のインストールからのクリーンアップ

以前に Azure Arc データ コントローラーをインストールし、同じクラスターで `azdata arc dc delete` コマンドを使用して Azure Arc データ コントローラーを削除した場合は、まだ削除が必要なクラスター レベルのオブジェクトが存在する可能性があります。 次のコマンドを実行して、Azure Arc データ コントローラーのクラスター レベルのオブジェクトを削除します。

```console
# Cleanup azure arc data service artifacts
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete crd postgresql-11s.arcdata.microsoft.com 
kubectl delete crd postgresql-12s.arcdata.microsoft.com
```

## <a name="overview"></a>概要

Azure Arc データ コントローラーを作成するステップの概要は、次のとおりです。
1. Arc データ コントローラー、Azure SQL マネージド インスタンス、PostgreSQL Hyperscale のカスタム リソース定義を作成します。 **[Kubernetes クラスター管理者のアクセス許可が必要]**
2. データ コントローラーが作成される名前空間を作成します。 **[Kubernetes クラスター管理者のアクセス許可が必要]**
3. レプリカ セット、サービス アカウント、ロール、ロール バインディングを含め、ブートストラップ サービスを作成します。
4. データ コントローラー管理者のユーザー名とパスワードのシークレットを作成します。
5. データ コントローラーを作成します。
   
## <a name="create-the-custom-resource-definitions"></a>カスタム リソース定義を作成する

次のコマンドを実行して、カスタム リソース定義を作成します。  **[Kubernetes クラスター管理者のアクセス許可が必要]**

```console
kubectl create -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/custom-resource-definitions.yaml
```

## <a name="create-a-namespace-in-which-the-data-controller-will-be-created"></a>データ コントローラーが作成される名前空間を作成する

次のようなコマンドを実行して、データ コントローラーが作成される新しい専用の名前空間を作成します。  この例とこの記事の残りの例では、`arc` の名前空間名が使用されます。 別の名前を使用する場合は、全体で同じ名前を使用してください。

```console
kubectl create namespace arc
```

クラスター管理者でない他のユーザーがこの名前空間を使用する場合は、名前空間管理者ロールを作成し、ロール バインディングを通じてそのロールをそれらのユーザーに付与することをお勧めします。  名前空間管理者は、名前空間に対する完全なアクセス許可を持っている必要があります。  より詳細なロール ベースのアクセスをユーザーに提供する方法については、後で詳しく説明します。

## <a name="create-the-bootstrapper-service"></a>ブートストラップ サービスを作成する

ブートストラップ サービスでは、データ コントローラー、SQL マネージド インスタンス、PostgreSQL Hyperscale サーバー グループなどのカスタム リソースの作成、編集、削除を行う受信要求が処理されます。

次のコマンドを実行して、ブートストラップ サービス、そのブートストラップ サービス用のサービス アカウント、そのブートストラップ サービス アカウントのロールとロールのバインドを作成します。

```console
kubectl create --namespace arc -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/bootstrapper.yaml
```

次のコマンドを使用して、ブートストラップ ポッドが実行されていることを確認します。  状態が `Running` に変わるまで、数回実行することが必要になる場合があります。

```console
kubectl get pod --namespace arc
```

ブートストラップの yaml テンプレート ファイルにより、既定で Microsoft Container Registry (MCR) からブートストラップのコンテナー イメージがプルされます。  お使いの環境で Microsoft Container Registry に直接アクセスできない場合は、次の操作を実行できます。
- 次のステップに従って、[Microsoft Container Registry からコンテナーイメージをプルし、それらをプライベート コンテナー レジストリにプッシュ](offline-deployment.md)します。
- プライベート コンテナー レジストリ用に[イメージのプル シークレットを作成](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-lin)します。
- イメージのプル シークレットをブートストラップ コンテナーに追加します。 以下の例を参照してください。
- ブートストラップ イメージのイメージの場所を変更します。 以下の例を参照してください。

次の例は、Kubernetes ドキュメントに記載されているように、イメージのプル シークレット名 `regcred` を作成したことを前提としています。

```yaml
#just showing only the relevant part of the bootstrapper.yaml template file here
containers:
      - env:
        - name: ACCEPT_EULA
          value: "Y"
        #image: mcr.microsoft.com/arcdata/arc-bootstrapper:public-preview-dec-2020  <-- template value to change
        image: <your registry DNS name or IP address>/<your repo>/arc-bootstrapper:<your tag>
        imagePullPolicy: IfNotPresent
        name: bootstrapper
        resources: {}
        securityContext:
          runAsUser: 21006
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      imagePullSecrets:
      - name: regcred
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      serviceAccount: sa-mssql-controller
      serviceAccountName: sa-mssql-controller
      terminationGracePeriodSeconds: 30

```

## <a name="create-a-secret-for-the-data-controller-administrator"></a>データ コントローラー管理者のシークレットを作成する

データ コントローラー管理者のユーザー名とパスワードは、データ コントローラー API に対して認証し、管理機能を実行するために使用されます。  安全なパスワードを選択し、クラスター管理者の特権を必要としているユーザーとのみ共有します。

Kubernetes シークレットは base64 でエンコードされた文字列として格納されます。1 つはユーザー名、1 つはパスワード用です。

オンライン ツールを使用して、希望するユーザー名とパスワードを base64 でエンコードできます。また、プラットフォームに応じて組み込みの CLI ツールを使用することもできます。

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

ユーザー名とパスワードをエンコードした後に、[テンプレート ファイル](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/controller-login-secret.yaml)に基づいてファイルを作成し、ユーザー名とパスワードの値を独自の値に置き換えることができます。

それから、次のコマンドを実行してシークレットを作成します。

```console
kubectl create --namespace arc -f <path to your data controller secret file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\controller-login-secret.yaml
```

## <a name="create-the-data-controller"></a>データ コントローラーを作成する

これで、データ コントローラー自体を作成する準備が整いました。

まず、[テンプレート ファイル](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/data-controller.yaml)のコピーをコンピューターのローカルに作成し、設定の一部を変更できるようにします。

必要に応じて、次の値を編集します。

**必須**
- **location**:これを、データ コントローラーに関する "_メタデータ_" が格納される Azure の場所に変更します。  使用できる Azure の場所の一覧は、[データ コントローラーの作成の概要](create-data-controller.md)に関する記事で確認できます。
- **resourceGroup**: Azure Resource Manager でデータ コントローラーの Azure リソースを作成する Azure リソース グループ。  通常、このリソース グループは既に存在しているはずですが、データを Azure にアップロードするときまでは必要ありません。
- **subscription**: Azure リソースを作成するサブスクリプションの Azure サブスクリプション GUID。

**見直してできれば既定値を変更することを推奨**
- **storage..className**: データ コントローラーのデータ ファイルとログ ファイルに使用するストレージ クラス。  Kubernetes クラスターで使用できるストレージ クラスがわからない場合は、次のコマンドを実行します。`kubectl get storageclass`  既定値は `default` で、既定のストレージ クラスではなく、`default` という名前の既存のストレージ クラスがあることを前提としています。  注:目的のストレージ クラスに設定される className 設定は 2 つあり、1 つはデータ用で、もう 1 つはログ用です。
- **serviceType**:LoadBalancer を使用していない場合は、サービス タイプを `NodePort` に変更します。  注:2 つの serviceType 設定を変更する必要があります。
- Azure Red Hat OpenShift または Red Hat OpenShift コンテナー プラットフォームでは、データ コントローラーを作成する前にセキュリティ コンテキスト制約を適用する必要があります。 「[OpenShift で Azure Arc 対応データ サービスのセキュリティ コンテキスト制約を適用する](how-to-apply-security-context-constraint.md)」の手順に従います。
- **Security**: Azure Red Hat OpenShift または Red Hat OpenShift コンテナー プラットフォームの場合は、データ コントローラーの yaml ファイルで、`security:` 設定を下記の値に置き換えます。 

```yml
  security:
    allowDumps: true
    allowNodeMetricsCollection: false
    allowPodMetricsCollection: false
    allowRunAsRoot: false
```

**省略可能**
- **name**:データ コントローラーの既定の名前は `arc` ですが、必要に応じて変更できます。
- **displayName**:ファイルの先頭にある name 属性と同じ値に設定します。
- **registry**:既定では Microsoft Container Registry です。  Microsoft Container Registry からイメージを取得して[プライベート コンテナー レジストリにプッシュ](offline-deployment.md)している場合は、ここでレジストリの IP アドレスまたは DNS 名を入力します。
- **dockerRegistry**:必要に応じて、プライベート コンテナー レジストリからイメージをプルするために使用するイメージのプル シークレット。
- **repository**:Microsoft Container Registry 上の既定のリポジトリは `arcdata` です。  プライベート コンテナー レジストリを使用している場合は、Azure Arc 対応データ サービスのコンテナーイメージが格納されているフォルダーまたはリポジトリのパスを入力します。
- **imageTag**: テンプレート内には最新バージョンのタグが既定で設定されていますが、古いバージョンを使用する必要がある場合は変更できます。

次の例は、完成したデータ コントローラーの yaml ファイルを示しています。 お客様の要件と、上記の情報に基づいて、実際の環境に合わせて例を更新します。

```yaml
apiVersion: arcdata.microsoft.com/v1alpha1
kind: datacontroller
metadata:
  generation: 1
  name: arc
spec:
  credentials:
    controllerAdmin: controller-login-secret
    #dockerRegistry: arc-private-registry - optional if you are using a private container registry that requires authentication using an image pull secret
    serviceAccount: sa-mssql-controller
  docker:
    imagePullPolicy: Always
    imageTag: public-preview-dec-2020 
    registry: mcr.microsoft.com
    repository: arcdata
  security:
    allowDumps: true
    allowNodeMetricsCollection: true
    allowPodMetricsCollection: true
    allowRunAsRoot: false
  services:
  - name: controller
    port: 30080
    serviceType: LoadBalancer
  - name: serviceProxy
    port: 30777
    serviceType: LoadBalancer
  settings:
    ElasticSearch:
      vm.max_map_count: "-1"
    azure:
      connectionMode: Indirect
      location: eastus
      resourceGroup: myresourcegroup
      subscription: c82c901a-129a-435d-86e4-cc6b294590ae
    controller:
      displayName: arc
      enableBilling: "True"
      logs.rotation.days: "7"
      logs.rotation.size: "5000"
  storage:
    data:
      accessMode: ReadWriteOnce
      className: default
      size: 15Gi
    logs:
      accessMode: ReadWriteOnce
      className: default
      size: 10Gi
```

編集したファイルをローカル コンピューターに保存し、次のコマンドを実行してデータ コントローラーを作成します。

```console
kubectl create --namespace arc -f <path to your data controller file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\data-controller.yaml
```

## <a name="monitoring-the-creation-status"></a>作成状態の監視

コントローラーの作成が完了するまでに数分かかります。 次のコマンドを使用して、別のターミナル ウィンドウで進行状況を監視できます。

> [!NOTE]
>  次のコマンド例では、`arc` という名前のデータ コントローラーと Kubernetes 名前空間が作成されていることを前提としています。  異なる名前の名前空間/データ コントローラーを使用している場合は、`arc` をその名前で置き換えてください。

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

次のようなコマンドを実行して、特定のポッドの作成状態を確認することもできます。  これは特に、何らの問題をトラブルシューティングするのに役立ちます。

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

Azure Data Studio 用の Azure Arc 拡張機能には、Azure Arc 対応 Kubernetes をセットアップし、SQL Managed Instance のサンプル yaml ファイルを含む git リポジトリを監視するように構成する方法を順を追って説明するノートブックが用意されています。 すべてが接続されると、新しい SQL Managed Instance が Kubernetes クラスターにデプロイされます。

Azure Data Studio 用の Azure Arc 拡張機能にある「**Azure Arc 対応 Kubernetes と Flux を使用した SQL Managed Instance のデプロイ**」のノートブックを参照してください。

## <a name="troubleshooting-creation-problems"></a>作成の問題のトラブルシューティング

作成で問題が発生した場合は、「[トラブルシューティング ガイド](troubleshoot-guide.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- [Kubernetes ネイティブ ツールを使用して SQL マネージド インスタンスを作成する](./create-sql-managed-instance-using-kubernetes-native-tools.md)
- [Kubernetes ネイティブ ツールを使用して PostgreSQL Hyperscale サーバー グループを作成する](./create-postgresql-hyperscale-server-group-kubernetes-native-tools.md)
