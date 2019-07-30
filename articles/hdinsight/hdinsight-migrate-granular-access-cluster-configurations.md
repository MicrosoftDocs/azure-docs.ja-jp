---
title: クラスター構成できめ細かなロールベースのアクセスに移行する - Azure HDInsight
description: HDInsight クラスター構成の詳細なロールベースのアクセスを移行の一環として求める変更について説明します。
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ebb1723a9a2b2d069a1766d4f78151f2b684c5b9
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464676"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>クラスター構成できめ細かなロールベースのアクセスに移行する

機密情報を取得するための、よりきめ細かいロールベースのアクセスのサポートに対して、いくつかの重要な変更が導入されています。 影響を受ける**エンティティまたはシナリオ**の中のいずれかを使用している場合は、これらの変更の一環として、何らかの[アクションが必要になる場合があります](#am-i-affected-by-these-changes)。

## <a name="what-is-changing"></a>何が変わるのですか?

以前は、所有者、共同作成者、または閲覧者の [RBAC ロール](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)を保有するクラスター ユーザーが HDInsight API を介してシークレットを取得できました。これは、`*/read` アクセス許可を持っていればだれでも利用できたためです。
今後、これらのシークレットにアクセスするには `Microsoft.HDInsight/clusters/configurations/*` アクセス許可が必要になります。つまり、閲覧者ロールを持つユーザーからはアクセスできなくなります。 シークレットは、ユーザーのロールよりもさらに高度なアクセス権を取得するために使用できる値として定義されます。 これらには、クラスター ゲートウェイ HTTP 資格情報、ストレージ アカウント キー、およびデータベースの資格情報などの値が含まれます。

また、共同作成者または所有者の管理アクセス許可を付与されなくても、シークレットを取得することができる新しい [HDInsight クラスター オペレーター](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) ロールも導入されます。 まとめると次のようになります。

| Role                                  | 以前                                                                                       | 今後の予定       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Reader                                | - シークレットを含む読み取りアクセス                                                                   | - シークレットを**除く**読み取りアクセス |           |   |   |
| HDInsight クラスター オペレーター<br>(新しいロール) | 該当なし                                                                                              | - シークレットを含む読み取り/書き込みアクセス         |   |   |
| Contributor                           | - シークレットを含む読み取り/書き込みアクセス<br>- すべての種類の Azure リソースを作成および管理します。     | 変更なし |
| Owner                                 | - シークレットを含む読み取り/書き込みアクセス<br>- すべてのリソースへのフル アクセス<br>- アクセスを他のユーザーに委任する | 変更なし |

HDInsight クラスター オペレーター ロールの割り当てを特定のユーザーに追加することで、そのユーザーにクラスター シークレットへの読み取り/書き込みアクセスを付与する方法の詳細については、後のセクション「[HDInsight クラスター オペレーター ロールの割り当てをユーザーに追加する](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)」を参照してください。

## <a name="am-i-affected-by-these-changes"></a>私はこれらの変更の影響を受けますか?

次のエンティティとシナリオには影響があります。

- [API](#api):`/configurations` エンドポイントまたは `/configurations/{configurationName}` エンドポイントを使用しているユーザー。
- [Azure HDInsight Tools for Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) バージョン 1.1.1 以下。
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) バージョン 3.20.0 以下。
- バージョン 2.3.9000.1 より前の [Azure Data Lake and Stream Analytics Tools for Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio)。
- [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse) バージョン 3.15.0 以前。
- [.NET 用 SDK](#sdk-for-net)
    - [バージョン 1.x または 2.x](#versions-1x-and-2x): ConfigurationsOperationsExtensions クラスから `GetClusterConfigurations`、`GetConnectivitySettings`、`ConfigureHttpSettings`、`EnableHttp`、または `DisableHttp` メソッドを使用しているユーザー。
    - [バージョン 3.x 以降](#versions-3x-and-up): `ConfigurationsOperationsExtensions` クラスから `Get`、`Update`、`EnableHttp`、または `DisableHttp` メソッドを使用しているユーザー。
- [Python 用 SDK](#sdk-for-python): `ConfigurationsOperations` クラスから `get` または `update` のメソッドを使用しているユーザー
- [Java 用 SDK](#sdk-for-java): `ConfigurationsInner` クラスから `update` または `get` のメソッドを使用しているユーザー
- [Go 用 SDK](#sdk-for-go): `ConfigurationsClient` 構造体から `Get` または `Update` のメソッドを使用しているユーザー
- バージョン 2.0.0 より前の [Az.HDInsight PowerShell](#azhdinsight-powershell)。
ご利用のシナリオの移行手順を確認するには、以下のセクションを参照 (または上記のリンクを使用) してください。

### <a name="api"></a>API

次の API は変更されるか非推奨となります。

- [**GET /configurations/{configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (機密情報が削除される)
    - 以前は、個々の構成の種類 (機密情報を含む) を取得するために使用されていました。
    - この API 呼び出しからは、シークレットを省略した状態で個々の構成の種類が返されるようになります。 シークレットを含むすべての構成を取得するには、新しい POST/configurations 呼び出しを使用します。 ゲートウェイ設定だけを取得するには、新しい POST/getGatewaySettings 呼び出しを使用します。
- [**GET /configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (非推奨)
    - 以前は、すべての構成 (機密情報を含む) を取得するために使用されていました。
    - この API 呼び出しはサポートされなくなります。 今後すべての構成を取得するには、新しい POST /configurations 呼び出しを使用します。 機密性の高いパラメーターを省略した状態で構成を取得するには、GET /configurations/{configurationName} 呼び出しを使用します。
- [**POST /configurations/{configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (非推奨)
    - 以前はゲートウェイの資格情報を更新するために使用されていました。
    - この API 呼び出しは非推奨となり、サポートされなくなります。 代わりに、新しい POST/updateGatewaySettings を使用してください。

次の代わりとなる API が追加されました。</span>

- [**POST /configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - この API は機密情報を含むすべての構成を取得する場合に使用します。
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - この API はゲートウェイ設定を取得する場合に取得します。
- [**POST /updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - この API はゲートウェイ設定 (ユーザー名やパスワード) を更新する場合に使用します。

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tools for Visual Studio Code

1\.1.1 またはそれよりも古いバージョンを使用している場合は、中断を回避するために[最新バージョンの Azure HDInsight Tools for Visual Studio Code に更新](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false)してください。

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

3\.20.0 またはそれよりも古いバージョンを使用している場合は、中断を回避するために[最新バージョンの Azure Toolkit for IntelliJ プラグインに更新](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij)してください。

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Azure Data Lake and Stream Analytics Tools for Visual Studio

中断を回避するために、[Azure Data Lake and Stream Analytics Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) のバージョン 2.3.9000.1 以降に更新してください。  更新については、Microsoft のドキュメント「[Data Lake Tools for Visual Studio の更新](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio)」を参照してください。

### <a name="azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse

3\.15.0 以前のバージョンを使用している場合は、中断を回避するために[最新バージョンの Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) に更新してください。

### <a name="sdk-for-net"></a>.NET 用 SDK

#### <a name="versions-1x-and-2x"></a>バージョン 1.x および 2.x

[バージョン 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) の HDInsight SDK for .NET に更新してください。 以下の変更に影響されるメソッドを使用している場合、最小限のコード変更が必要になる可能性があります。

- `ClusterOperationsExtensions.GetClusterConfigurations` では、ストレージ キー (コア サイト) または HTTP 資格情報 (ゲートウェイ) などの**機密性の高いパラメーターが返されなくなります**。
    - 機密性の高いパラメーターを含むすべての構成を取得するには、今後は `ClusterOperationsExtensions.ListConfigurations` を使います。  "閲覧者" ロールを持つユーザーはこのメソッドを使用できないことに注意してください。 これにより、クラスターの機密情報にアクセスできるユーザーをきめ細かく制御できます。
    - HTTP ゲートウェイ資格情報だけを取得するには、`ClusterOperationsExtensions.GetGatewaySettings` を使います。

- `ClusterOperationsExtensions.GetConnectivitySettings` は非推奨になり、`ClusterOperationsExtensions.GetGatewaySettings` に置き換えられています。

- `ClusterOperationsExtensions.ConfigureHttpSettings` は非推奨になり、`ClusterOperationsExtensions.UpdateGatewaySettings` に置き換えられています。

- `ConfigurationsOperationsExtensions.EnableHttp` と `DisableHttp` は非推奨になりました。 HTTP は常に有効にされるようになったので、これらのメソッドはもう必要ありません。

#### <a name="versions-3x-and-up"></a>バージョン 3.x 以降

[バージョン 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) の HDInsight SDK for .NET に更新してください。 以下の変更に影響されるメソッドを使用している場合、最小限のコード変更が必要になる可能性があります。

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) では、ストレージ キー (コア サイト) や HTTP 資格情報 (ゲートウェイ) などの**機密性の高いパラメーターが返されなくなります**。
    - 機密性の高いパラメーターを含むすべての構成を取得するには、今後は [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) を使用します。  "閲覧者" ロールを持つユーザーはこのメソッドを使用できないことに注意してください。 これにより、クラスターの機密情報にアクセスできるユーザーをきめ細かく制御できます。 
    - HTTP ゲートウェイ資格情報だけを取得するには、[`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet) を使用します。 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) は非推奨になり、[`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet) に置き換えられています。 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) と [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) は非推奨となりました。 HTTP は常に有効にされるようになったので、これらのメソッドはもう必要ありません。

### <a name="sdk-for-python"></a>Python 用 SDK

[バージョン 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) の HDInsight SDK for Python に更新してください。 以下の変更に影響されるメソッドを使用している場合、最小限のコード変更が必要になる可能性があります。

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) では、ストレージ キー (コア サイト) や HTTP 資格情報 (ゲートウェイ) などの**機密性の高いパラメーターが返されなくなります**。
    - 機密性の高いパラメーターを含むすべての構成を取得するには、今後は [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) を使用します。  "閲覧者" ロールを持つユーザーはこのメソッドを使用できないことに注意してください。 これにより、クラスターの機密情報にアクセスできるユーザーをきめ細かく制御できます。 
    - HTTP ゲートウェイ資格情報だけを取得するには、[`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) を使用します。
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) は非推奨になり、[`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) に置き換えられています。

### <a name="sdk-for-java"></a>Java 用 SDK

[バージョン 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) の HDInsight SDK for Java に更新してください。 以下の変更に影響されるメソッドを使用している場合、最小限のコード変更が必要になる可能性があります。

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) では、ストレージ キー (コア サイト) や HTTP 資格情報 (ゲートウェイ) などの**機密性の高いパラメーターが返されなくなります**。
    - 機密性の高いパラメーターを含むすべての構成を取得するには、今後は [`ConfigurationsInner.list`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.configurationsinner.list?view=azure-java-stable) を使用します。  "閲覧者" ロールを持つユーザーはこのメソッドを使用できないことに注意してください。 これにより、クラスターの機密情報にアクセスできるユーザーをきめ細かく制御できます。 
    - HTTP ゲートウェイ資格情報だけを取得するには、[`ClustersInner.getGatewaySettings`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.getgatewaysettings?view=azure-java-stable) を使用します。
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) は非推奨になり、[`ClustersInner.updateGatewaySettings`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.updategatewaysettings?view=azure-java-stable) に置き換えられています。

### <a name="sdk-for-go"></a>Go 用 SDK

[バージョン 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) の HDInsight SDK for Go に更新してください。 以下の変更に影響されるメソッドを使用している場合、最小限のコード変更が必要になる可能性があります。

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) では、ストレージ キー (コア サイト) や HTTP 資格情報 (ゲートウェイ) などの**機密性の高いパラメーターが返されなくなります**。
    - 機密性の高いパラメーターを含むすべての構成を取得するには、今後は [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) を使用します。  "閲覧者" ロールを持つユーザーはこのメソッドを使用できないことに注意してください。 これにより、クラスターの機密情報にアクセスできるユーザーをきめ細かく制御できます。 
    - HTTP ゲートウェイ資格情報だけを取得するには、[`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings) を使用します。
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) は非推奨になり、[`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings) に置き換えられています。

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
中断を回避するために [Az PowerShell バージョン 2.0.0](https://www.powershellgallery.com/packages/Az) 以降に更新してください。  以下の変更に影響されるメソッドを使用している場合、最小限のコード変更が必要になる可能性があります。
- `Grant-AzHDInsightHttpServicesAccess` は非推奨になり、新しい `Set-AzHDInsightGatewayCredential` コマンドレットに置き換えられています。
- ストレージ キーに対するきめ細かいロールベースのアクセスをサポートするために、`Get-AzHDInsightJobOutput` は更新されました。
    - HDInsight クラスターのオペレーター、共同作成者、または所有者のロールを持つユーザーには影響を及ぼしません。
    - 閲覧者のロールのみを持つユーザーは、`DefaultStorageAccountKey` パラメーターを明示的に指定する必要があります。
- `Revoke-AzHDInsightHttpServicesAccess` は非推奨となりました。 HTTP は常に有効になったので、このコマンドレットはもう必要ありません。
 詳細については、[az.HDInsight の移行ガイド](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight)を参照してください。

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>HDInsight クラスター オペレーター ロールの割り当てをユーザーに追加する

[共同作成者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor)ロールまたは[所有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)ロールを保有するユーザーは、機密 HDInsight クラスター構成 (クラスター ゲートウェイ資格情報やストレージ アカウント キーなど) への読み取り/書き込みアクセスを求めるユーザーに[ HDInsight Cluster Operator](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) ロールを付与することができます。

### <a name="using-the-azure-cli"></a>Azure CLI の使用

このロール割り当てを追加するには、Azure CLI 内で `az role assignment create` コマンドを使用するのが最も簡単な方法です。

> [!NOTE]
> このコマンドは、共同作成者ロールまたは所有者ロールを保有するユーザーによって、それらのアクセス許可が付与される場合にのみ、実行される必要があります。 `--assignee` は HDInsight クラスター オペレーター ロールの割り当て先とするユーザーの電子メール アドレスです。

#### <a name="grant-role-at-the-resource-cluster-level"></a>リソース (クラスター) レベルでロールを付与します。

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>リソース グループ レベルでロールを付与します。

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>サブスクリプション レベルでロールを付与します。

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Azure ポータルの使用

Azure portal を使用して、HDInsight クラスター オペレーター ロールの割り当てをユーザーに追加することもできます。 詳細については、[RBAC と Azure portal を使用して Azure リソースへのアクセスを管理する - ロール割り当ての追加](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)に関するページを参照してください。
