---
title: Batch Management .NET ライブラリを使用し、アカウント リソースを管理する
description: Batch Management .NET ライブラリを使用して Azure Batch アカウント リソースを作成、削除、変更します。
ms.topic: how-to
ms.date: 01/26/2021
ms.custom: seodec18, has-adal-ref, devx-track-csharp
ms.openlocfilehash: f6acf23742b8d4c5c31a5ea952aba5c76b64cae0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896733"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>.NET 用の Batch 管理クライアント ライブラリを使用した Batch アカウントとクォータの管理

[Batch Management .NET](/dotnet/api/overview/azure/batch) ライブラリを使用して Batch アカウントの作成、削除、キー管理、およびクォータ検出を自動化することにより、Azure Batch アプリケーションにおける保守の負担が減ります。

- **Batch アカウントを作成および削除します** 。 たとえば、独立系ソフトウェア ベンダー (ISV) が課金のためにクライアントごとに異なる Batch アカウントを割り当てる場合、顧客ポータルにアカウントの作成および削除機能を追加できます。
- **アカウント キーを取得および再生成します** 。 これは、アカウント キーの定期的なロールオーバーまたは失効を実施するセキュリティ ポリシーに準拠するうえで役に立ちます。 さまざまな Azure リージョンに複数の Batch アカウントがある場合、このロールオーバー プロセスを自動化するとソリューションの効率が向上します。
- **アカウントのクォータを確認し** 、Batch アカウントに対する制限の決定から試行錯誤の当て推量を排除します。 ジョブ開始前にアカウントのクォータをチェックしたり、プールを作成したり、コンピューティング ノードを追加したりすることで、いつどこでコンピューティング リソースが作成されるのかを事前に調整できます。 アカウントに追加リソースを割り当てる前に、クォータの増量が必要なアカウントを特定できます。
- Batch Management .NET、[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)、[Azure Resource Manager](../azure-resource-manager/management/overview.md) を同じアプリケーションで使用することにより、完全な機能を備えた管理エクスペリエンスのために **他の Azure サービスの機能を組み合わせます**。 これらの機能とその API を使用して、摩擦のない認証エクスペリエンスや、リソース グループの作成と削除の機能、そして既に説明した機能によってエンド ツー エンドの管理ソリューションを実現できます。

> [!NOTE]
> この記事では、プログラムによる Batch アカウント、キー、およびクォータの管理に重点を置いて説明しますが、これらのアクティビティの多くは [Azure portal を使用](batch-account-create-portal.md)して実行することもできます。

## <a name="create-and-delete-batch-accounts"></a>Batch アカウントを作成および削除します

Batch Management API の主要な機能の 1 つは、Azure リージョンでの [Batch アカウント](accounts.md)の作成と削除です。 これらを行うには、[BatchManagementClient.Account.CreateAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.createasync) および [DeleteAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.deleteasync)、またはこれらの同期版を使用します。

次のコード例では、アカウントを作成し、新しく作成されたアカウントを Batch サービスから取得して、削除します。 次のスニペットとこの記事の他のスニペットでは、`batchManagementClient` は [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient) の完全に初期化されたインスタンスです。

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
> Batch Management .NET ライブラリとその BatchManagementClient クラスを使用するアプリケーションでは、管理対象の Batch アカウントを所有するサブスクリプションに対するサービス管理者または共同管理者のアクセスが必要です。 詳細については、「Azure Active Directory」セクションおよび [AccountManagement](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement) コード サンプルを参照してください。

## <a name="retrieve-and-regenerate-account-keys"></a>アカウント キーを取得および再生成します

お使いのサブスクリプション内の Batch アカウントからプライマリおよびセカンダリのアカウント キーを取得するには、[GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync) を使用します。 これらのキーを再生成するには、[RegenerateKeyAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.regeneratekeyasync) を使用します。

```csharp
// Get and print the primary and secondary keys
BatchAccountGetKeyResult accountKeys =
    await batchManagementClient.Account.GetKeysAsync(
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
> 管理アプリケーションのための効率化された接続ワークフローを作成できます。 まず、管理対象の Batch アカウントのアカウント キーを [GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync) で取得します。 次に、このキーを使用して、[BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) を初期化するときに使用される Batch .NET ライブラリの [BatchSharedKeyCredentials](/dotnet/api/microsoft.azure.batch.auth.batchsharedkeycredentials) クラスを初期化します。

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Azure サブスクリプションと Batch アカウントのクォータの確認

Azure サブスクリプションおよび個別の Azure サービス (Batch など) はすべて、それらの中にある特定のエンティティの数を制限する既定のクォータを持っています。 Azure サブスクリプションの既定のクォータについては、「 [Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md)」を参照してください。 Batch サービスの既定のクォータについては、「 [Azure Batch サービスのクォータと制限](batch-quota-limit.md)」を参照してください。 これらのクォータは、アプリケーション内から Batch Management .NET ライブラリを使用して確認できます。 アカウントやコンピューティング リソース (プールやコンピューティング ノードなど) を追加する前に、割り当ての意思決定を行うことができます。

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Azure サブスクリプションでの Batch アカウント クォータの確認

リージョンに Batch アカウントを作成する前に、Azure サブスクリプションを調べて、そのリージョンにアカウントを追加できるかどうかを確認できます。

次のコード スニペットでは、最初に **ListAsync** を使用してサブスクリプション内のすべての Batch アカウントのコレクションを取得しています。 このコレクションを取得した後、対象リージョン内のアカウント数を調べています。 その後、**GetQuotasAsync** を使用して Batch アカウントのクォータを取得し、そのリージョンに作成できるアカウントの数 (作成できる場合) を調べます。

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.BatchAccount.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Location.GetQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

上記のスニペットで、`creds` は **TokenCredentials** のインスタンスです。 このオブジェクトを作成する例については、GitHub の [AccountManagement](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement) コード サンプルを参照してください。

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
> Azure サブスクリプションおよびサービスには既定のクォータがありますが、これらの制限の多くは、[Azure portal でクォータの引き上げを依頼する](batch-quota-limit.md#increase-a-quota)ことによって引き上げることができます。

## <a name="use-azure-ad-with-batch-management-net"></a>Batch Management .NET で Azure AD を使用する

Batch Management .NET ライブラリは Azure リソース プロバイダー クライアントであり、[Azure Resource Manager](../azure-resource-manager/management/overview.md) と共に使用してアカウント リソースをプログラムで管理します。 Azure AD は、Batch Management .NET ライブラリを含むすべての Azure リソース プロバイダー クライアントや、Azure Resource Manager を通じて行われた要求の認証に必要です。 Batch Management .NET ライブラリで Azure AD を使用する方法については、[Azure Active Directory を使用した Batch ソリューションの認証](batch-aad-auth.md)に関する記事をご覧ください。

## <a name="sample-project-on-github"></a>GitHub のサンプル プロジェクト

Batch Management .NET の動作を確認するには、GitHub の [AccountManagment](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement) サンプル プロジェクトを確認してください。 AccountManagment サンプル アプリケーションでは、次の操作が示されています。

1. [ADAL](../active-directory/azuread-dev/active-directory-authentication-libraries.md) を使用して Azure AD からセキュリティ トークンを取得します。 ユーザーがまだサインインしていない場合は、Azure 資格情報の入力を求められます。
2. Azure AD から取得したセキュリティ トークンを使用して、[SubscriptionClient](/dotnet/api/microsoft.azure.management.resourcemanager.subscriptionclient) を作成し、アカウントに関連付けられているサブスクリプションの一覧を Azure に照会します。 一覧に複数のサブスクリプションが含まれている場合、ユーザーはサブスクリプションを選択できます。
3. 選択したサブスクリプションに関連付けられている資格情報を取得します。
4. 資格情報を使用して、[ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) オブジェクトを作成します。
5. [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) オブジェクトを使用して、リソース グループを作成します。
6. [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient) オブジェクトを使用して、いくつかの Batch アカウント操作を実行します。
   - 新しいリソース グループに Batch アカウントを作成します。
   - Batch サービスから新しく作成されたアカウントを取得します。
   - 新しいアカウントのアカウント キーを出力します。
   - アカウント用の新しいプライマリ キーを再生成します。
   - アカウントのクォータ情報を出力します。
   - サブスクリプションのクォータ情報を出力します。
   - サブスクリプション内のすべてのアカウントを出力します。
   - 新しく作成したアカウントを削除します。
7. リソース グループを削除します。

サンプル アプリケーションを正常に実行するには、Azure Portal で Azure AD テナントに登録し、Azure Resource Manager API にアクセス許可を付与しておく必要があります。 「[Batch 管理ソリューションの認証に Active Directory を使用する](batch-aad-auth-management.md)」に記載されている手順に従います。

## <a name="next-steps"></a>次のステップ

- [Batch サービスのワークフローと主要なリソース](batch-service-workflow-features.md) (プール、ノード、ジョブ、タスクなど) について学習します。
- [Batch .NET クライアント ライブラリ](quick-run-dotnet.md)または [Python](quick-run-python.md) を使用した Batch 対応アプリケーションの開発に関する基本事項を確認してください。 これらのクイック スタートでは、Batch サービスを使用して複数のコンピューティング ノードでワークロードを実行するサンプル アプリケーションについて説明します。ここで、ワークロード ファイルのステージングと取得には、Azure Storage を使用します。
