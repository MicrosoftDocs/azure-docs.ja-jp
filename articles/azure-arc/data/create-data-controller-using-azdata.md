---
title: Azure Data CLI (azdata) を使用してデータ コントローラーを作成する
description: Azure Data CLI (azdata) を使用して、既に作成されている一般的なマルチノード Kubernetes クラスターに Azure Arc データ コントローラーを作成します。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: facb7db73bf7a709b9ed07e460d8653d79f1ed2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687595"
---
# <a name="create-azure-arc-data-controller-using-the-azure-data-cli-azdata"></a>[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] を使用した Azure Arc データ コントローラーの作成

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>前提条件

概要情報については、「[Azure Arc データ コントローラーを作成する](create-data-controller.md)」のトピックを確認してください。

[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] を使用して Azure Arc データ コントローラーを作成するには、[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] をインストールしておく必要があります。

   [[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] をインストールする](install-client-tools.md)

選択するターゲット プラットフォームに関係なく、データ コントローラー管理者ユーザーの作成の前に、次の環境変数を設定する必要があります。 これらの資格情報は、必要に応じて、データ コントローラーへの管理者アクセス権を持つ必要がある他のユーザーに提供できます。

**AZDATA_USERNAME** - データ コントローラー管理者ユーザーの任意のユーザー名。 例: `arcadmin`

**AZDATA_PASSWORD** - データ コントローラー管理者ユーザーの任意のパスワード。 パスワードの長さは少なくとも 8 文字で、英大文字、英小文字、数字、記号の 4 種類のうち 3 種類を含んでいる必要があります。

### <a name="linux-or-macos"></a>Linux または macOS

```console
export AZDATA_USERNAME="<your username of choice>"
export AZDATA_PASSWORD="<your password of choice>"
```

### <a name="windows-powershell"></a>Windows PowerShell

```console
$ENV:AZDATA_USERNAME="<your username of choice>"
$ENV:AZDATA_PASSWORD="<your password of choice>"
```

Azure Arc データ コントローラーの作成を開始する前に、Kubernetes クラスターに接続して認証し、既存の Kubernetes コンテキストを選択しておく必要があります。 Kubernetes クラスターまたはサービスへの接続方法はさまざまです。 Kubernetes API サーバーへの接続方法については、使用している Kubernetes ディストリビューションまたはサービスのドキュメントを参照してください。

現在の Kubernetes 接続があること、および現在のコンテキストは、次のコマンドを使用して確認できます。

```console
kubectl get namespace
kubectl config current-context
```

### <a name="connectivity-modes"></a>接続モード

「[接続モードと要件](./connectivity.md)」で説明されているように、Azure Arc データ コントローラーは `direct` または `indirect` の接続モードのいずれかを使用してデプロイできます。 `direct` 接続モードを使用すると、使用状況データは自動的にかつ継続的に Azure に送信されます。 この記事の例では、次のように `direct` 接続モードを指定します。

   ```console
   --connectivity-mode direct
   ```

   `indirect` 接続モードでコントローラーを作成するには、次に示すように例のスクリプトを更新します。

   ```console
   --connectivity-mode indirect
   ```

#### <a name="create-service-principal"></a>サービス プリンシパルの作成

`direct` 接続モードで Azure Arc データ コントローラーをデプロイする場合、Azure 接続にはサービス プリンシパルの資格情報が必要です。 使用状況とメトリックのデータをアップロードするには、サービス プリンシパルが使用されます。 

次のコマンドを実行して、メトリック アップロードのサービス プリンシパルを作成します。

> [!NOTE]
> サービス プリンシパルを作成するには、[Azure で一定のアクセス許可](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)を持っている必要があります。

サービス プリンシパルを作成するには、次の例を更新します。 `<ServicePrincipalName>` をサービス プリンシパルの名前に置き換えて、コマンドを実行します。

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName>
``` 

以前にサービス プリンシパルを作成しており、単に最新の資格情報の取得が必要な場合は、次のコマンドを実行して資格情報をリセットします。

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

たとえば、`azure-arc-metrics` という名前のサービス プリンシパルを作成するには、次のコマンドを実行します。

```console
az ad sp create-for-rbac --name azure-arc-metrics
```

出力例:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

後で使用するために、`appId`、`password`、`tenant` の値を環境変数に保存します。 

#### <a name="save-environment-variables-in-windows"></a>Windows で環境変数を保存する

```console
SET SPN_CLIENT_ID=<appId>
SET SPN_CLIENT_SECRET=<password>
SET SPN_TENANT_ID=<tenant>
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

#### <a name="save-environment-variables-in-linux-or-macos"></a>Linux または macOS で環境変数を保存する

```console
export SPN_CLIENT_ID='<appId>'
export SPN_CLIENT_SECRET='<password>'
export SPN_TENANT_ID='<tenant>'
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

#### <a name="save-environment-variables-in-powershell"></a>PowerShell で環境変数を保存する

```console
$Env:SPN_CLIENT_ID="<appId>"
$Env:SPN_CLIENT_SECRET="<password>"
$Env:SPN_TENANT_ID="<tenant>"
$Env:SPN_AUTHORITY="https://login.microsoftonline.com"
```

サービス プリンシパルを作成した後、サービス プリンシパルを適切なロールに割り当てます。 

### <a name="assign-roles-to-the-service-principal"></a>サービス プリンシパルにロールを割り当てる

こちらのコマンドを実行して、サービス プリンシパルを、データベース インスタンスのリソースが配置されているサブスクリプションの `Monitoring Metrics Publisher` ロールに割り当てます。

#### <a name="run-the-command-on-windows"></a>Windows でコマンドを実行する

> [!NOTE]
> Windows 環境から実行する場合は、ロール名に二重引用符を使用する必要があります。

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role "Contributor" --scope subscriptions/<Subscription ID>
```

#### <a name="run-the-command-on-linux-or-macos"></a>Linux または macOS でコマンドを実行する

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

#### <a name="run-the-command-in-powershell"></a>PowerShell でコマンドを実行する

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

```output
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

サービス プリンシパルが適切なロールに割り当てられ、環境変数が設定されたら、データ コントローラーの作成に進むことができます 

## <a name="create-the-azure-arc-data-controller"></a>Azure Arc データ コントローラーを作成する

> [!NOTE]
> 以下の例では azdata arc dc create コマンドの `--namespace` パラメーターに別の値を使用できますが、必ず、以下で示す他のすべてのコマンドで `--namespace parameter` にその名前空間名を使用してください。

- [Azure Kubernetes Service (AKS) に作成する](#create-on-azure-kubernetes-service-aks)
- [Azure Stack Hub の AKS エンジンに作成する](#create-on-aks-engine-on-azure-stack-hub)
- [Azure Stack HCI の AKS に作成する](#create-on-aks-on-azure-stack-hci)
- [Azure Red Hat OpenShift (ARO) に作成する](#create-on-azure-red-hat-openshift-aro)
- [Red Hat OpenShift Container Platform (OCP) に作成する](#create-on-red-hat-openshift-container-platform-ocp)
- [オープンソースのアップストリーム Kubernetes (kubeadm) に作成する](#create-on-open-source-upstream-kubernetes-kubeadm)
- [AWS Elastic Kubernetes Service (EKS) に作成する](#create-on-aws-elastic-kubernetes-service-eks)
- [Google Cloud Kubernetes Engine Service (GKE) に作成する](#create-on-google-cloud-kubernetes-engine-service-gke)

### <a name="create-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) に作成する

既定では、AKS デプロイ プロファイルでは `managed-premium` ストレージ クラスが使用されます。 `managed-premium` ストレージ クラスは、Premium ディスクを含む VM イメージを使用してデプロイされた VM がある場合にのみ機能します。

ストレージ クラスとして `managed-premium` を使用する場合は、次のコマンドを実行してデータ コントローラーをデプロイできます。 コマンドのプレースホルダーを、リソース グループ名、サブスクリプション ID、Azure の場所に置き換えます。

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

どのストレージ クラスを使用するかわからない場合は、使用している VM の種類に関係なくサポートされる `default` ストレージ クラスを使用してください。 最速のパフォーマンスが得られなくなるだけです。

`default` ストレージ クラスを使用する場合は、次のコマンドを実行できます。

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

コマンドを実行したら、「[作成状態の監視](#monitoring-the-creation-status)」に進みます。

### <a name="create-on-aks-engine-on-azure-stack-hub"></a>Azure Stack Hub の AKS エンジンに作成する

既定では、デプロイ プロファイルでは `managed-premium` ストレージ クラスが使用されます。 `managed-premium` ストレージ クラスは、Premium ディスクを含む VM イメージを使用して Azure Stack Hub にデプロイされたワーカー VM がある場合にのみ機能します。

次のコマンドを実行すると、managed-premium ストレージ クラスを使用してデータ コントローラーを作成できます。

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

どのストレージ クラスを使用するかわからない場合は、使用している VM の種類に関係なくサポートされる `default` ストレージ クラスを使用してください。 Azure Stack Hub では、Premium ディスクと Standard ディスクは同じストレージ インフラストラクチャによって支えられています。 したがって、それらは全般的には同等のパフォーマンスを提供することが期待されますが、IOPS の制限が異なっています。

`default` ストレージ クラスを使用する場合は、次のコマンドを実行できます。

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

コマンドを実行したら、「[作成状態の監視](#monitoring-the-creation-status)」に進みます。

### <a name="create-on-aks-on-azure-stack-hci"></a>Azure Stack HCI の AKS に作成する

既定では、デプロイ プロファイルでは `default` という名前のストレージ クラスとサービス タイプ `LoadBalancer` が使用されます。

次のコマンドを実行すると、`default` ストレージ クラスとサービス タイプ `LoadBalancer` を使用してデータ コントローラーを作成できます。

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

コマンドを実行したら、「[作成状態の監視](#monitoring-the-creation-status)」に進みます。


### <a name="create-on-azure-red-hat-openshift-aro"></a>Azure Red Hat OpenShift (ARO) に作成する

Azure Red Hat OpenShift には、セキュリティ コンテキスト制約が必要です。

#### <a name="apply-the-security-context"></a>セキュリティ コンテキストを適用する

Azure Red Hat OpenShift でデータ コントローラーを作成する前に、特定のセキュリティ コンテキスト制約 (SCC) を適用する必要があります。 プレビュー リリースでは、これらによってセキュリティ制約が緩和されます。 今後のリリースで、更新された SCC が提供されます。

[!INCLUDE [apply-security-context-constraint](includes/apply-security-context-constraint.md)]

#### <a name="create-custom-deployment-profile"></a>カスタム デプロイ プロファイルを作成する

Azure RedHat Open Shift には、プロファイル `azure-arc-azure-openshift` を使用します。

```console
azdata arc dc config init --source azure-arc-azure-openshift --path ./custom
```

#### <a name="create-data-controller"></a>データ コントローラーの作成

次のコマンドを実行して、データ コントローラーを作成します。

> [!NOTE]
> ここで使用する名前空間は、前の `oc adm policy add-scc-to-user` コマンドで使用したものと同じです。 たとえば `arc` とします。

```console
azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example
#azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

コマンドを実行したら、「[作成状態の監視](#monitoring-the-creation-status)」に進みます。

### <a name="create-on-red-hat-openshift-container-platform-ocp"></a>Red Hat OpenShift Container Platform (OCP) に作成する

> [!NOTE]
> Azure で Red Hat OpenShift Container Platform を使用している場合は、利用できる中で最新のバージョンを使用することをお勧めします。

Red Hat OCP でデータ コントローラーを作成する前に、特定のセキュリティ コンテキスト制約を適用する必要があります。 

#### <a name="apply-the-security-context-constraint"></a>セキュリティ コンテキスト制約を適用する

[!INCLUDE [apply-security-context-constraint](includes/apply-security-context-constraint.md)]

#### <a name="determine-storage-class"></a>ストレージ クラスを定義する

次のコマンドを実行して、使用するストレージ クラスを決定する必要もあります。

```console
kubectl get storageclass
```

#### <a name="create-custom-deployment-profile"></a>カスタム デプロイ プロファイルを作成する

次のコマンドを実行して、`azure-arc-openshift` デプロイ プロファイルに基づいて新しいカスタムのデプロイ プロファイル ファイルを作成します。 このコマンドでは、`custom` というディレクトリが現在の作業ディレクトリに作成され、カスタムのデプロイ プロファイル ファイル `control.json` がそのディレクトリに作成されます。

OpenShift Container Platform には、プロファイル `azure-arc-openshift` を使用します。

```console
azdata arc dc config init --source azure-arc-openshift --path ./custom
```

#### <a name="set-storage-class"></a>ストレージ クラスを設定する 

次に、下記のコマンドの `<storageclassname>` を、(上記の `kubectl get storageclass` コマンドを実行して決定された) 使用するストレージ クラスの名前に置き換えることによって、目的のストレージ クラスを設定します。

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

#### <a name="set-loadbalancer-optional"></a>LoadBalancer を設定する (省略可能)

既定では、`azure-arc-openshift` デプロイ プロファイルでは `NodePort` がサービス タイプとして使用されます。 ロード バランサーと統合された OpenShift クラスターを使用している場合は、次のコマンドを使用して、`LoadBalancer` サービス タイプを使用するように構成を変更できます。

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

#### <a name="verify-security-policies"></a>セキュリティ ポリシーを確認する

OpenShift を使用するときは、OpenShift の既定のセキュリティ ポリシーを使用して実行するか、通常よりも全般的に環境をロックダウンすることが必要な場合があります。 デプロイ時および実行時に必要なアクセス許可を最小限にするために、必要に応じて、次のコマンドを実行して一部の機能を無効にすることができます。

このコマンドでは、ポッドに関するメトリックの収集を無効にします。 この機能を無効にすると、ポッドに関するメトリックを Grafana のダッシュボードに表示できなくなります。 既定値は true です。

```console
azdata arc dc config replace -p ./custom/control.json --json-values spec.security.allowPodMetricsCollection=false
```

このコマンドでは、ノードに関するメトリックの収集を無効にします。 この機能を無効にすると、ノードに関するメトリックを Grafana のダッシュボードに表示できなくなります。 既定値は true です。

```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowNodeMetricsCollection=false
```

このコマンドでは、トラブルシューティングの目的でメモリ ダンプを取得する機能を無効にします。
```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowDumps=false
```

#### <a name="create-data-controller"></a>データ コントローラーの作成

これで、次のコマンドを使用してデータ コントローラーを作成する準備ができました。

> [!NOTE]
>   ここで使用する名前空間は、前の `oc adm policy add-scc-to-user` コマンドで使用したものと同じです。 たとえば `arc` とします。

> [!NOTE]
>   `--path` パラメーターは、control.json ファイル自体ではなく、control.json ファイルを含む _ディレクトリ_ を指している必要があります。


```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

コマンドを実行したら、「[作成状態の監視](#monitoring-the-creation-status)」に進みます。

### <a name="create-on-open-source-upstream-kubernetes-kubeadm"></a>オープンソースのアップストリーム Kubernetes (kubeadm) に作成する

既定では、kubeadm デプロイ プロファイルでは、`local-storage` という名前のストレージ クラスとサービス タイプ `NodePort` が使用されます。 これで問題ない場合は、目的のストレージ クラスとサービス タイプを設定する次の手順をスキップして、後述する `azdata arc dc create` コマンドをすぐに実行できます。

デプロイ プロファイルをカスタマイズして特定のストレージ クラスやサービス タイプを指定する場合は、まず次のコマンドを実行して、kubeadm デプロイ プロファイルに基づいて新しいカスタムのデプロイ プロファイル ファイルを作成します。 このコマンドでは、`custom` というディレクトリが現在の作業ディレクトリに作成され、カスタムのデプロイ プロファイル ファイル `control.json` がそのディレクトリに作成されます。

```console
azdata arc dc config init --source azure-arc-kubeadm --path ./custom
```

次のコマンドを実行して、使用可能なストレージ クラスを検索できます。

```console
kubectl get storageclass
```

次に、下記のコマンドの `<storageclassname>` を、(上記の `kubectl get storageclass` コマンドを実行して決定された) 使用するストレージ クラスの名前に置き換えることによって、目的のストレージ クラスを設定します。

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

既定では、kubeadm デプロイ プロファイルでは `NodePort` がサービス タイプとして使用されます。 ロードバランサーと統合された Kubernetes クラスターを使用している場合は、次のコマンドを使用して構成を変更できます。

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

これで、次のコマンドを使用してデータ コントローラーを作成する準備ができました。

```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

コマンドを実行したら、「[作成状態の監視](#monitoring-the-creation-status)」に進みます。

### <a name="create-on-aws-elastic-kubernetes-service-eks"></a>AWS Elastic Kubernetes Service (EKS) に作成する

既定では、EKS のストレージ クラスは `gp2` で、サービス タイプは `LoadBalancer` です。

次のコマンドを実行し、提供されている EKS デプロイ プロファイルを使用してデータ コントローラーを作成します。

```console
azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

コマンドを実行したら、「[作成状態の監視](#monitoring-the-creation-status)」に進みます。

### <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>Google Cloud Kubernetes Engine Service (GKE) に作成する

既定では、GKE のストレージ クラスは `standard` で、サービス タイプは `LoadBalancer` です。

次のコマンドを実行し、提供されている GKE デプロイ プロファイルを使用してデータ コントローラーを作成します。

```console
azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

コマンドを実行したら、「[作成状態の監視](#monitoring-the-creation-status)」に進みます。

## <a name="monitoring-the-creation-status"></a>作成状態の監視

コントローラーの作成が完了するまでに数分かかります。 次のコマンドを使用して、別のターミナル ウィンドウで進行状況を監視できます。

> [!NOTE]
>  次のコマンド例では、`arc` という名前のデータ コントローラーと Kubernetes 名前空間が作成されていることを前提としています。 異なる名前の名前空間/データ コントローラーを使用している場合は、`arc` をその名前で置き換えてください。

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

次のようなコマンドを実行して、特定のポッドの作成状態を確認することもできます。 これは特に、何らの問題をトラブルシューティングするのに役立ちます。

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>作成の問題のトラブルシューティング

作成で問題が発生した場合は、「[トラブルシューティング ガイド](troubleshoot-guide.md)」を参照してください。