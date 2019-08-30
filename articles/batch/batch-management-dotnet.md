---
title: .NET 用クライアント ライブラリでのアカウント リソースの管理 - Azure Batch | Microsoft Docs
description: Batch Management .NET ライブラリを使用して Azure Batch アカウント リソースを作成、削除、変更します。
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/24/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: f7554993e2e3d8d2f6bce71db57a746a4392ce1a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095068"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>.NET 用の Batch 管理クライアント ライブラリを使用した Batch アカウントとクォータの管理

> [!div class="op_single_selector"]
> * [Azure Portal](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
> 
> 

[Batch Management .NET][api_mgmt_net] ライブラリを使用して Batch アカウントの作成、削除、キー管理、およびクォータ検出を自動化することにより、Azure Batch アプリケーションにおける保守の負担が減ります。

* **Batch アカウントを作成および削除します** 。 たとえば、独立系ソフトウェア ベンダー (ISV) が課金のためにクライアントごとに異なる Batch アカウントを割り当てる場合、顧客ポータルにアカウントの作成および削除機能を追加できます。
* **アカウント キーを取得および再生成します** 。 これは、アカウント キーの定期的なロールオーバーまたは失効を実施するセキュリティ ポリシーに準拠するうえで役に立ちます。 さまざまな Azure リージョンに複数の Batch アカウントがある場合、このロールオーバー プロセスを自動化するとソリューションの効率が向上します。
* **アカウントのクォータを確認し** 、Batch アカウントに対する制限の決定から試行錯誤の当て推量を排除します。 ジョブ開始前にアカウントのクォータをチェックしたり、プールを作成したり、コンピューティング ノードを追加したりすることで、いつどこでコンピューティング リソースが作成されるのかを事前に調整できます。 アカウントに追加リソースを割り当てる前に、クォータの増量が必要なアカウントを特定できます。
* Batch Management .NET、[Azure Active Directory][aad_about]、[Azure Resource Manager][resman_overview] を同じアプリケーションで使用することにより、完全な機能を備えた管理エクスペリエンスのために**他の Azure サービスの機能を組み合わせます**。 これらの機能とその API を使用して、摩擦のない認証エクスペリエンスや、リソース グループの作成と削除の機能、そして既に説明した機能によってエンド ツー エンドの管理ソリューションを実現できます。

> [!NOTE]
> この記事では、プログラムによる Batch アカウント、キー、およびクォータの管理に重点を置いて説明しますが、これらのアクティビティの多くは [Azure portal][azure_portal] を使用して実行できます。 詳細については、「[Azure Portal を使用して Azure Batch アカウントを作成する](batch-account-create-portal.md)」と「[Azure Batch サービスのクォータと制限](batch-quota-limit.md)」を参照してください。
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Batch アカウントを作成および削除します
前述のように、Batch Management API の主要な機能の 1 つは、Azure リージョン内の Batch アカウントの作成と削除です。 これらを行うには、[BatchManagementClient.Account.CreateAsync][net_create] および [DeleteAsync][net_delete]、またはこれらの同期版を使用します。

次のコード例では、アカウントを作成し、新しく作成されたアカウントを Batch サービスから取得して、削除します。 次のスニペットとこの記事の他のスニペットでは、`batchManagementClient` は [BatchManagementClient][net_mgmt_client] の完全に初期化されたインスタンスです。

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [!NOTE]
> Batch Management .NET ライブラリとその BatchManagementClient クラスを使用するアプリケーションでは、管理対象の Batch アカウントを所有するサブスクリプションに対する**サービス管理者**または**共同管理者**のアクセスが必要です。 詳細については、「Azure Active Directory」セクションおよび [AccountManagement][acct_mgmt_sample] コード サンプルを参照してください。
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>アカウント キーを取得および再生成します
お使いのサブスクリプション内の Batch アカウントからプライマリおよびセカンダリのアカウント キーを取得するには、[ListKeysAsync][net_list_keys] を使用します。 これらのキーを再生成するには、[RegenerateKeyAsync][net_regenerate_keys] を使用します。

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [!TIP]
> 管理アプリケーションのための効率化された接続ワークフローを作成できます。 まず、管理対象の Batch アカウントのアカウント キーを [ListKeysAsync][net_list_keys] で取得します。 次に、このキーを使用して、[BatchClient][net_batch_client] を初期化するときに使用される Batch .NET ライブラリの [BatchSharedKeyCredentials][net_sharedkeycred] クラスを初期化します。
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Azure サブスクリプションと Batch アカウントのクォータの確認
Azure サブスクリプションおよび個別の Azure サービス (Batch など) はすべて、それらの中にある特定のエンティティの数を制限する既定のクォータを持っています。 Azure サブスクリプションの既定のクォータについては、「 [Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」を参照してください。 Batch サービスの既定のクォータについては、「 [Azure Batch サービスのクォータと制限](batch-quota-limit.md)」を参照してください。 これらのクォータは、アプリケーション内から Batch Management .NET ライブラリを使用して確認できます。 アカウントやコンピューティング リソース (プールやコンピューティング ノードなど) を追加する前に、割り当ての意思決定を行うことができます。

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Azure サブスクリプションでの Batch アカウント クォータの確認
リージョンに Batch アカウントを作成する前に、Azure サブスクリプションを調べて、そのリージョンにアカウントを追加できるかどうかを確認できます。

次のコード スニペットでは、最初に [BatchManagementClient.Account.ListAsync][net_mgmt_listaccounts] を使用してサブスクリプション内のすべての Batch アカウントのコレクションを取得しています。 このコレクションを取得した後、対象リージョン内のアカウント数を調べています。 その後、[BatchManagementClient.Subscriptions][net_mgmt_subscriptions] を使用して Batch アカウントのクォータを取得し、そのリージョンに作成できるアカウントの数 (作成できる場合) を調べます。

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

上のスニペットで、`creds` は [TokenCloudCredentials][azure_tokencreds] のインスタンスです。 このオブジェクトを作成する例については、GitHub の [AccountManagement][acct_mgmt_sample] コード サンプルを参照してください。

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Batch アカウントでのコンピューティング リソース クォータの確認
Batch ソリューションでコンピューティング リソースを増やす前に、割り当てるつもりのリソースがアカウントのクォータを上回らないことを確認できます。 次のコード スニペットでは `mybatchaccount` という名前の Batch アカウントのクォータ情報を出力しています。 実際のアプリケーションでは、このような情報を使用して、作成する追加のリソースをアカウントが処理できるかどうかを判断できます。

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [!IMPORTANT]
> Azure サブスクリプションおよびサービスには既定のクォータがありますが、[Azure Portal][azure_portal] で要求を発行することによってこれらの制限の多くを増やすことができます。 たとえば、Batch アカウントのクォータを増やす方法については、「 [Azure Batch サービスのクォータと制限](batch-quota-limit.md) 」を参照してください。
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Batch Management .NET で Azure AD を使用する

Batch Management .NET ライブラリは Azure リソース プロバイダー クライアントであり、[Azure Resource Manager][resman_overview] と共に使用してアカウント リソースをプログラムで管理します。 Azure AD は、Batch Management .NET ライブラリなどの Azure リソース プロバイダー クライアントや、[Azure Resource Manager][resman_overview] を通じて行われた要求の認証に必要です。 Batch Management .NET ライブラリで Azure AD を使用する方法については、[Azure Active Directory を使用した Batch ソリューションの認証](batch-aad-auth.md)に関する記事をご覧ください。 

## <a name="sample-project-on-github"></a>GitHub のサンプル プロジェクト

Batch Management .NET の動作を確認するには、GitHub の [AccountManagment][acct_mgmt_sample] サンプル プロジェクトを確認してください。 AccountManagment サンプル アプリケーションでは、次の操作が示されています。

1. [ADAL][aad_adal] を使用して Azure AD からセキュリティ トークンを取得します。 ユーザーがまだサインインしていない場合は、Azure 資格情報の入力を求められます。
2. Azure AD から取得したセキュリティ トークンを使用して、[SubscriptionClient][resman_subclient] を作成し、アカウントに関連付けられているサブスクリプションの一覧を Azure に照会します。 一覧に複数のサブスクリプションが含まれている場合、ユーザーはサブスクリプションを選択できます。
3. 選択したサブスクリプションに関連付けられている資格情報を取得します。
4. 資格情報を使用して、[ResourceManagementClient][resman_client] オブジェクトを作成します。
5. [ResourceManagementClient][resman_client] オブジェクトを使用して、リソース グループを作成します。
6. [BatchManagementClient][net_mgmt_client] オブジェクトを使用して、いくつかの Batch アカウント操作を実行します。
   * 新しいリソース グループに Batch アカウントを作成します。
   * Batch サービスから新しく作成されたアカウントを取得します。
   * 新しいアカウントのアカウント キーを出力します。
   * アカウント用の新しいプライマリ キーを再生成します。
   * アカウントのクォータ情報を出力します。
   * サブスクリプションのクォータ情報を出力します。
   * サブスクリプション内のすべてのアカウントを出力します。
   * 新しく作成されたアカウントを削除します。
7. リソース グループを削除します。

新しく作成した Batch アカウントとリソース グループを削除する前に、[Azure Portal][azure_portal] でこれらを確認できます。

サンプル アプリケーションを正常に実行するには、Azure Portal で Azure AD テナントに登録し、Azure Resource Manager API にアクセス許可を付与しておく必要があります。 「[Batch 管理ソリューションの認証に Active Directory を使用する](batch-aad-auth-management.md)」に記載されている手順に従います。


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Azure Active Directory とは"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Azure AD の認証シナリオ"
[aad_integrate]:../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md "Azure Active Directory とアプリケーションの統合"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: https://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
