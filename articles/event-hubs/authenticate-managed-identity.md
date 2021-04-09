---
title: Azure Active Directory を使用したマネージド ID の認証
description: この記事では、Azure Active Directory を使用して Azure Event Hubs リソースにアクセスするためのマネージド ID を認証する方法について説明します
ms.topic: conceptual
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 2070cfd94b39a08afb86ffd3579f1116faac72d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98805284"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Azure Active Directory を使用して Event Hubs リソースにアクセスするためのマネージド ID を認証する
Azure Event Hubs では、[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を使用した Azure Active Directory (Azure AD) 認証がサポートされています。 Azure リソースのマネージド ID では、Azure Virtual Machines (VMs)、Function Apps、Virtual Machine Scale Sets などのサービスで実行されているアプリケーションから Event Hubs リソースへのアクセスを、Azure AD 資格情報を使用して承認することができます。 Azure リソースのマネージド ID を Azure AD 認証と一緒に使用することで、クラウドで動作するアプリケーションに資格情報を保存することを避けることができます。

この記事では、Azure VM からイベント ハブへのアクセスを、マネージド ID を使用して承認する方法について示します。

## <a name="enable-managed-identities-on-a-vm"></a>VM 上のマネージド ID を有効にする
Azure リソースのマネージド ID を使用してご利用の VM から Event Hubs リソースへのアクセスを承認するには、最初に VM 上で Azure リソースのマネージド ID を有効にする必要があります。 Azure リソースのマネージド ID を有効にする方法については、次の記事のいずれかを参照してください。

- [Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager テンプレート](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager クライアント ライブラリ](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Azure AD のマネージド ID にアクセス許可を付与する
ご利用のアプリケーション内のマネージド ID から Event Hubs サービスへの要求を認可するには、最初にそのマネージド ID に対して Azure ロールベースのアクセス制御 (Azure RBAC) の設定を構成します。 Azure Event Hubs で、Event Hubs との間で送受信を行うためのアクセス許可を含む Azure ロールを定義します。 Azure ロールがマネージド ID に割り当てられると、適切なスコープでの Event Hubs データへのアクセスが、そのマネージド ID に付与されます。

Azure ロールの割り当ての詳細については、[Azure Active Directory を使用して認証し、Event Hubs リソースにアクセスする](authorize-access-azure-active-directory.md)ことに関するページを参照してください。

## <a name="use-event-hubs-with-managed-identities"></a>マネージド ID で Event Hubs を使用する
マネージド ID で Event Hubs を使用するには、ロールと適切なスコープを ID に割り当てる必要があります。 このセクションの手順では、マネージド ID で実行され Event Hubs リソースにアクセスするシンプルなアプリケーションを使用します。

ここでは、[Azure App Service](https://azure.microsoft.com/services/app-service/) でホストされているサンプル Web アプリケーションを使用します。 Web アプリケーションを作成するための詳細な手順については、[Azure に ASP.NET Core Web アプリを作成する](../app-service/quickstart-dotnetcore.md)に関する記事を参照してください

アプリケーションが作成されたら、次の手順を行います。 

1. **[設定]** に移動し、 **[ID]** を選択します。 
1. **[状態]** を選択して **[オン]** にします。 
1. **[保存]** を選択して設定を保存します。 

    :::image type="content" source="./media/authenticate-managed-identity/identity-web-app.png" alt-text="Web アプリのマネージド ID":::
4. 情報メッセージで **[はい]** を選択します。 

    この設定を有効にすると、ご利用の Azure Active Directory (Azure AD) に新しいサービス ID が作成され、App Service ホストに構成されます。

    次に、このサービス ID をご利用の Event Hubs リソースの必要なスコープ内のロールに割り当てます。

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Azure portal を使用して Azure ロールを割り当てるには
Event Hubs リソースにロールを割り当てるには、Azure portal でそのリソースに移動します。 リソースの [アクセス制御 (IAM)] 設定を表示し、次の手順に従ってロールの割り当てを管理します。

> [!NOTE]
> 次の手順では、Event Hubs 名前空間にサービス ID のロールを割り当てます。 同じ手順に従って、任意の Event Hubs リソースにスコープ指定されたロールを割り当てることができます。 

1. Azure portal でご利用の Event Hubs 名前空間に移動し、その名前空間の **[概要]** を表示します。 
1. 左側のメニューの **[アクセス制御 (IAM)]** を選択して、イベント ハブのアクセス制御設定を表示します。
1.  **[ロールの割り当て]** タブを選択して、ロールの割り当ての一覧を表示します。
3.  **[追加]** を選択し、**[ロールの割り当ての追加]*** を選択します。
4.  **[ロールの割り当ての追加]** ページで、これらの手順に従います。
    1. **[ロール]** で、割り当てる Event Hubs ロールを選択します。 この例では、 **[Azure Event Hubs データ所有者]** です。
    1. **[アクセスの割り当て先]** フィールドで、 **[システム割り当てマネージド ID]** の **[App Service]** を選択します。 
    1. Web アプリのマネージド ID が作成された **サブスクリプション** を選択します。
    1. 作成した Web アプリの **マネージド ID** を選択します。 ID の既定の名前は、Web アプリの名前と同じです。 
    1. 次に、 **[保存]** を選択します。 
    
        ![[ロールの割り当ての追加] ページ](./media/authenticate-managed-identity/add-role-assignment-page.png)

    ロールを割り当てると、Web アプリケーションには、定義されたスコープの下で Event Hubs リソースへのアクセス権が付与されます。 

    > [!NOTE]
    > マネージド ID をサポートするサービスの一覧については、「[Azure リソースのマネージド ID をサポートするサービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)」を参照してください。

### <a name="test-the-web-application"></a>Web アプリケーションをテストする
1. Event Hubs 名前空間とイベント ハブを作成します。 
2. Web アプリを Azure にデプロイする。 GitHub 上の Web アプリへのリンクは、次のタブ付きセクションを参照してください。 
3. SendReceive.aspxがWeb アプリのデフォルトドキュメントとして設定されていることを確認します。 
3. Web アプリの **ID** を有効にします。 
4. このIDを名前空間レベルまたはイベント ハブ レベルで **Event Hubs データ所有者** の役割に割り当てます。 
5. Webアプリケーションを実行し、名前空間名とイベント ハブ名、およびメッセージを入力し、**送信** を選択します。 イベントを受信するには、**受信** を選択します。 

#### <a name="azuremessagingeventhubs-latest"></a>[ Azure.Messaging.EventHubs (最新)](#tab/latest)
これで Web アプリケーションを起動し、ブラウザーで aspx のサンプル ページを参照できるようになりました。 [GitHub リポジトリ](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)には、Event Hubs リソースとの間でデータの送受信を行うサンプル Web アプリケーションがあります。

[NuGet](https://www.nuget.org/packages/Azure.Messaging.EventHubs/)から最新のパッケージをインストールし、**EventHubProducerClient** を使用してEvent Hubs にイベントの送信を開始し、**EventHubConsumerClient** を使用してイベントの受信を開始します。 

> [!NOTE]
> マネージド ID を使用してイベント ハブにイベントを発行する Java サンプルについては、[GitHub 上での Azure ID サンプルを使ったイベントの発行](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)に関するページを参照してください。

```csharp
protected async void btnSend_Click(object sender, EventArgs e)
{
    await using (EventHubProducerClient producerClient = new EventHubProducerClient(txtNamespace.Text, txtEventHub.Text, new DefaultAzureCredential()))
    {
        // create a batch
        using (EventDataBatch eventBatch = await producerClient.CreateBatchAsync())
        {

            // add events to the batch. only one in this case. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes(txtData.Text)));

            // send the batch to the event hub
            await producerClient.SendAsync(eventBatch);
        }

        txtOutput.Text = $"{DateTime.Now} - SENT{Environment.NewLine}{txtOutput.Text}";
    }
}
protected async void btnReceive_Click(object sender, EventArgs e)
{
    await using (var consumerClient = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, $"{txtNamespace.Text}.servicebus.windows.net", txtEventHub.Text, new DefaultAzureCredential()))
    {
        int eventsRead = 0;
        try
        {
            using CancellationTokenSource cancellationSource = new CancellationTokenSource();
            cancellationSource.CancelAfter(TimeSpan.FromSeconds(5));

            await foreach (PartitionEvent partitionEvent in consumerClient.ReadEventsAsync(cancellationSource.Token))
            {
                txtOutput.Text = $"Event Read: { Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray()) }{ Environment.NewLine}" + txtOutput.Text;
                eventsRead++;
            }
        }
        catch (TaskCanceledException ex)
        {
            txtOutput.Text = $"Number of events read: {eventsRead}{ Environment.NewLine}" + txtOutput.Text;
        }
    }
}
```

#### <a name="microsoftazureeventhubs-legacy"></a>[Microsoft.Azure.EventHubs (レガシー)](#tab/old)
これで Web アプリケーションを起動し、ブラウザーで aspx のサンプル ページを参照できるようになりました。 [GitHub リポジトリ](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp)には、Event Hubs リソースとの間でデータの送受信を行うサンプル Web アプリケーションがあります。

[NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)から最新のパッケージをインストールし、次のコードに示すようにEventHubClientを使用してEvent Hubsとの間でデータの送受信を開始します。 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>Kafka 用の Event Hubs
Apache Kafka アプリケーションを使用すると、マネージド ID OAuth を使用して Azure Event Hubs との間でメッセージを送受信することができます。 GitHub の次のサンプルを参照してください: [Kafka 用の Event Hubs - マネージド ID OAuth を使用したメッセージの送受信](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)。

## <a name="samples"></a>サンプル
- **Azure.Messaging.EventHubs** サンプル
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Microsoft.Azure.EventHubs サンプル](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)。 
    
    これらのサンプルでは、古い **Microsoft.Azure.EventHubs** ライブラリが使用されていますが、最新の **Azure.Messaging.EventHubs** ライブラリを使用するように簡単に更新できます。 古いライブラリから新しいライブラリを使用するようにサンプルを移行する方法については、[Microsoft.Azure.EventHubs から Azure.Messaging.EventHubs への移行のガイド](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)に関する記事を参照してください。
    このサンプルは、最新の **Azure.Messaging.EventHubs** ライブラリを使用するように更新されています。
- [Kafka 用の Event Hubs - マネージド ID OAuth を使用したメッセージの送受信](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>次のステップ
- Azure リソースのマネージド ID の詳細については、次の記事を参照してください。[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)
- 次の関連記事を参照してください。
    - [Azure Active Directory を使用してアプリケーションから Azure Event Hubs への要求を認証する](authenticate-application.md)
    - [Shared Access Signature を使用して Azure Event Hubs に対する要求を認証する](authenticate-shared-access-signature.md)
    - [Azure Active Directory を使用して Event Hubs リソースへのアクセスを承認する](authorize-access-azure-active-directory.md)
    - [Shared Access Signature を使用して Event Hubs リソースへのアクセスを承認する](authorize-access-shared-access-signature.md)
