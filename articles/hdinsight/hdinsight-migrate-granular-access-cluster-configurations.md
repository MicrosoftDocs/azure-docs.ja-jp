---
title: 詳細なロール ベースのアクセスの Azure HDInsight クラスター構成
description: HDInsight クラスター構成の詳細なロールベースのアクセスを移行の一環として求める変更について説明します。
author: tylerfox
ms.author: tyfox
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: 47569309f35848e82488abd549751f6f1e5a1baa
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954872"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>クラスター構成できめ細かなロールベースのアクセスに移行する

機密情報を取得するための、よりきめ細かいロールベースのアクセスのサポートに対して、いくつかの重要な変更が導入されています。 [影響を受けるエンティティまたはシナリオ](#am-i-affected-by-these-changes)のいずれかを使用している場合は、これらの変更の一環として、**2019 年 9 月 3 日までに** 何らかのアクションが必要になる場合があります。

## <a name="what-is-changing"></a>何が変わるのですか?

以前は、所有者、共同作成者、または閲覧者の [Azure ロール](../role-based-access-control/rbac-and-directory-admin-roles.md)を保有するクラスター ユーザーが HDInsight API を介してシークレットを取得できました。これは、`*/read` アクセス許可を持っていればだれでも利用できたためです。 シークレットは、ユーザーのロールよりもさらに高度なアクセス権を取得するために使用できる値として定義されます。 これらには、クラスター ゲートウェイ HTTP 資格情報、ストレージ アカウント キー、およびデータベースの資格情報などの値が含まれます。

2019 年 9 月 3 日以降、これらのシークレットにアクセスするには `Microsoft.HDInsight/clusters/configurations/action` アクセス許可が必要になります。つまり、閲覧者ロールを持つユーザーからはアクセスできなくなります。 このアクセス許可を持つロールは、共同作成者、所有者、新しい HDInsight クラスター オペレーター ロールです (以下で詳しく説明します)。

また、共同作成者または所有者の管理アクセス許可を付与されなくても、シークレットを取得することができる新しい [HDInsight クラスター オペレーター](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) ロールも導入されます。 まとめると次のようになります。

| Role                                  | 以前                                                                                       | 今後の予定       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Reader                                | - シークレットを含む読み取りアクセス。                                                                   | - シークレットを **除く** 読み取りアクセス | 
| HDInsight クラスター オペレーター<br>(新しいロール) | 該当なし                                                                                              | - シークレットを含む読み取り/書き込みアクセス         | 
| Contributor                           | - シークレットを含む読み取り/書き込みアクセス。<br>- すべての種類の Azure リソースを作成および管理します。<br>- スクリプト アクションの実行。     | 変更なし |
| 所有者                                 | - シークレットを含む読み取り/書き込みアクセス。<br>- すべてのリソースへのフル アクセス<br>- アクセスを他のユーザーに委任する。<br>- スクリプト アクションの実行。 | 変更なし |

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

- [**GET /configurations/{configurationName}**](/rest/api/hdinsight/hdinsight-cluster#get-configuration) (機密情報が削除される)
    - 以前は、個々の構成の種類 (機密情報を含む) を取得するために使用されていました。
    - 2019 年 9 月 3 日以降、この API 呼び出しからは、シークレットを省略した状態で個々の構成の種類が返されるようになります。 シークレットを含むすべての構成を取得するには、新しい POST/configurations 呼び出しを使用します。 ゲートウェイ設定だけを取得するには、新しい POST/getGatewaySettings 呼び出しを使用します。
- [**GET /configurations**](/rest/api/hdinsight/hdinsight-cluster#get-configuration) (非推奨)
    - 以前は、すべての構成 (機密情報を含む) を取得するために使用されていました。
    - 2019 年 9 月 3 日以降、この API 呼び出しは非推奨となり、サポートされなくなります。 今後すべての構成を取得するには、新しい POST /configurations 呼び出しを使用します。 機密性の高いパラメーターを省略した状態で構成を取得するには、GET /configurations/{configurationName} 呼び出しを使用します。
- [**POST /configurations/{configurationName}**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (非推奨)
    - 以前はゲートウェイの資格情報を更新するために使用されていました。
    - 2019 年 9 月 3 日以降、この API 呼び出しは非推奨となり、サポートされなくなります。 代わりに、新しい POST/updateGatewaySettings を使用してください。

次の代わりとなる API が追加されました。</span>

- [**POST /configurations**](/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - この API は機密情報を含むすべての構成を取得する場合に使用します。
- [**POST /getGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - この API はゲートウェイ設定を取得する場合に取得します。
- [**POST /updateGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - この API はゲートウェイ設定 (ユーザー名やパスワード) を更新する場合に使用します。

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tools for Visual Studio Code

1\.1.1 またはそれよりも古いバージョンを使用している場合は、中断を回避するために[最新バージョンの Azure HDInsight Tools for Visual Studio Code に更新](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false)してください。

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

3\.20.0 またはそれよりも古いバージョンを使用している場合は、中断を回避するために[最新バージョンの Azure Toolkit for IntelliJ プラグインに更新](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij)してください。

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Azure Data Lake and Stream Analytics Tools for Visual Studio

中断を回避するために、[Azure Data Lake and Stream Analytics Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) のバージョン 2.3.9000.1 以降に更新してください。  更新については、Microsoft のドキュメント「[Data Lake Tools for Visual Studio の更新](./hadoop/apache-hadoop-visual-studio-tools-get-started.md#update-data-lake-tools-for-visual-studio)」を参照してください。

### <a name="azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse

3\.15.0 以前のバージョンを使用している場合は、中断を回避するために[最新バージョンの Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) に更新してください。

### <a name="sdk-for-net"></a>.NET 用 SDK

#### <a name="versions-1x-and-2x"></a>バージョン 1.x および 2.x

[バージョン 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) の HDInsight SDK for .NET に更新してください。 以下の変更に影響されるメソッドを使用している場合、最小限のコード変更が必要になる可能性があります。

- `ClusterOperationsExtensions.GetClusterConfigurations` では、ストレージ キー (コア サイト) または HTTP 資格情報 (ゲートウェイ) などの **機密性の高いパラメーターが返されなくなります**。
    - 機密性の高いパラメーターを含むすべての構成を取得するには、今後は `ClusterOperationsExtensions.ListConfigurations` を使います。  "閲覧者" ロールを持つユーザーはこのメソッドを使用できないことに注意してください。 これにより、クラスターの機密情報にアクセスできるユーザーをきめ細かく制御できます。
    - HTTP ゲートウェイ資格情報だけを取得するには、`ClusterOperationsExtensions.GetGatewaySettings` を使います。

- `ClusterOperationsExtensions.GetConnectivitySettings` は非推奨になり、`ClusterOperationsExtensions.GetGatewaySettings` に置き換えられています。

- `ClusterOperationsExtensions.ConfigureHttpSettings` は非推奨になり、`ClusterOperationsExtensions.UpdateGatewaySettings` に置き換えられています。

- `ConfigurationsOperationsExtensions.EnableHttp` と `DisableHttp` は非推奨になりました。 HTTP は常に有効にされるようになったので、これらのメソッドはもう必要ありません。

#### <a name="versions-3x-and-up"></a>バージョン 3.x 以降

[バージョン 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) の HDInsight SDK for .NET に更新してください。 以下の変更に影響されるメソッドを使用している場合、最小限のコード変更が必要になる可能性があります。

- [`ConfigurationOperationsExtensions.Get`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get) では、ストレージ キー (コア サイト) や HTTP 資格情報 (ゲートウェイ) などの **機密性の高いパラメーターが返されなくなります**。
    - 機密性の高いパラメーターを含むすべての構成を取得するには、今後は [`ConfigurationOperationsExtensions.List`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list) を使用します。  "閲覧者" ロールを持つユーザーはこのメソッドを使用できないことに注意してください。 これにより、クラスターの機密情報にアクセスできるユーザーをきめ細かく制御できます。 
    - HTTP ゲートウェイ資格情報だけを取得するには、[`ClusterOperationsExtensions.GetGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings) を使用します。 
- [`ConfigurationsOperationsExtensions.Update`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update) は非推奨になり、[`ClusterOperationsExtensions.UpdateGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings) に置き換えられています。 
- [`ConfigurationsOperationsExtensions.EnableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp) と [`DisableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp) は非推奨となりました。 HTTP は常に有効にされるようになったので、これらのメソッドはもう必要ありません。

### <a name="sdk-for-python"></a>Python 用 SDK

[バージョン 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) の HDInsight SDK for Python に更新してください。 以下の変更に影響されるメソッドを使用している場合、最小限のコード変更が必要になる可能性があります。

- [`ConfigurationsOperations.get`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) では、ストレージ キー (コア サイト) や HTTP 資格情報 (ゲートウェイ) などの **機密性の高いパラメーターが返されなくなります**。
    - 機密性の高いパラメーターを含むすべての構成を取得するには、今後は [`ConfigurationsOperations.list`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) を使用します。  "閲覧者" ロールを持つユーザーはこのメソッドを使用できないことに注意してください。 これにより、クラスターの機密情報にアクセスできるユーザーをきめ細かく制御できます。 
    - HTTP ゲートウェイ資格情報だけを取得するには、[`ClusterOperations.get_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) を使用します。
- [`ConfigurationsOperations.update`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) は非推奨になり、[`ClusterOperations.update_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) に置き換えられています。

### <a name="sdk-for-java"></a>Java 用 SDK

[バージョン 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) の HDInsight SDK for Java に更新してください。 以下の変更に影響されるメソッドを使用している場合、最小限のコード変更が必要になる可能性があります。

- `ConfigurationsInner.get` では、ストレージ キー (コア サイト) または HTTP 資格情報 (ゲートウェイ) などの **機密性の高いパラメーターが返されなくなります**。
- `ConfigurationsInner.update` は非推奨となりました。

### <a name="sdk-for-go"></a>Go 用 SDK

[バージョン 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight) の HDInsight SDK for Go に更新してください。 以下の変更に影響されるメソッドを使用している場合、最小限のコード変更が必要になる可能性があります。

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Get) では、ストレージ キー (コア サイト) や HTTP 資格情報 (ゲートウェイ) などの **機密性の高いパラメーターが返されなくなります**。
    - 機密性の高いパラメーターを含むすべての構成を取得するには、今後は [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.List) を使用します。  "閲覧者" ロールを持つユーザーはこのメソッドを使用できないことに注意してください。 これにより、クラスターの機密情報にアクセスできるユーザーをきめ細かく制御できます。 
    - HTTP ゲートウェイ資格情報だけを取得するには、[`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.GetGatewaySettings) を使用します。
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Update) は非推奨になり、[`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings) に置き換えられています。

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
中断を回避するために [Az PowerShell バージョン 2.0.0](https://www.powershellgallery.com/packages/Az) 以降に更新してください。  以下の変更に影響されるメソッドを使用している場合、最小限のコード変更が必要になる可能性があります。
- `Grant-AzHDInsightHttpServicesAccess` は非推奨になり、新しい `Set-AzHDInsightGatewayCredential` コマンドレットに置き換えられています。
- ストレージ キーに対するきめ細かいロールベースのアクセスをサポートするために、`Get-AzHDInsightJobOutput` は更新されました。
    - HDInsight クラスターのオペレーター、共同作成者、または所有者のロールを持つユーザーには影響を及ぼしません。
    - 閲覧者のロールのみを持つユーザーは、`DefaultStorageAccountKey` パラメーターを明示的に指定する必要があります。
- `Revoke-AzHDInsightHttpServicesAccess` は非推奨となりました。 HTTP は常に有効になったので、このコマンドレットはもう必要ありません。
 詳細については、[az.HDInsight の移行ガイド](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight)を参照してください。

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>HDInsight クラスター オペレーター ロールの割り当てをユーザーに追加する

[所有者](../role-based-access-control/built-in-roles.md#owner)ロールを保有するユーザーは、機密の HDInsight クラスター構成の値 (クラスター ゲートウェイ資格情報やストレージ アカウント キーなど) への読み取り/書き込みアクセスを求めるユーザーに [HDInsight クラスター オペレーター](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) ロールを付与することができます。

### <a name="using-the-azure-cli"></a>Azure CLI の使用

このロール割り当てを追加するには、Azure CLI 内で `az role assignment create` コマンドを使用するのが最も簡単な方法です。

> [!NOTE]
> このコマンドは、所有者ロールを保有するユーザーによって、それらのアクセス許可が付与される場合にのみ、実行される必要があります。 `--assignee` は、サービス プリンシパルの名前、または HDInsight クラスター オペレーター ロールの割り当て先とするユーザーの電子メール アドレスです。 アクセス許可が不十分であることを示すエラーが表示された場合は、以下の「FAQ」を参照してください。

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

Azure portal を使用して、HDInsight クラスター オペレーター ロールの割り当てをユーザーに追加することもできます。 「[Azure portal を使用して Azure ロールを割り当てる](../role-based-access-control/role-assignments-portal.md)」のドキュメントを参照してください。

## <a name="faq"></a>よく寄せられる質問

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>API 要求やツールを更新した後、403 (禁止) の応答が表示されるのはなぜですか?

クラスター構成は、細かく設定したロールベースのアクセス制御の背後にあり、これらにアクセスするには `Microsoft.HDInsight/clusters/configurations/*` アクセス許可が必要です。 このアクセス許可を取得するには、構成にアクセスしようとしているユーザーまたはサービス プリンシパルに、HDInsight クラスター オペレーター、共同作成者、または所有者ロールを割り当てます。

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Azure CLI コマンドを実行して HDInsight クラスター オペレーター ロールを別のユーザーまたはサービス プリンシパルに割り当てると、"操作を完了するための十分な特権がありません" と表示されるのはなぜですか?

所有者ロールだけでなく、コマンドを実行しているユーザーまたはサービス プリンシパルは、担当者のオブジェクト ID を検索するために、十分な Azure AD アクセス許可を持っている必要があります。 このメッセージは、Azure AD のアクセス許可が不十分であることを示します。 `-–assignee` 引数を `–assignee-object-id` に置き換えて、名前の代わりにパラメーターとして担当者のオブジェクト ID (またはマネージド ID の場合は、プリンシパル ID) を指定します。 詳細については、[az role assignment create ドキュメント](/cli/azure/role/assignment#az-role-assignment-create)の「省略可能なパラメーター」セクションを参照してください。

それでもうまくいかない場合は、Azure AD 管理者に連絡して、適切なアクセス許可を取得してください。

### <a name="what-will-happen-if-i-take-no-action"></a>操作を行わない場合、どうなりますか?

2019 年 9 月 3 日以降、`GET /configurations` と `POST /configurations/gateway` の呼び出しによって、情報が返されなくなります。また、`GET /configurations/{configurationName}` 呼び出しでは、ストレージ アカウント キーやクラスター パスワードなどの機密性の高いパラメーターは返されなくなります。 これは、対応する SDK メソッドと PowerShell コマンドレットにも当てはまります。

上記の Visual Studio、VSCode、IntelliJ または Eclipse 用のいずれかのツールで以前のバージョンを使用している場合は、更新するまで機能しなくなります。

詳細については、自分のシナリオに対応するこのドキュメントのセクションを参照してください。
