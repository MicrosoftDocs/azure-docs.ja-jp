---
title: Azure リソースのマネージド ID - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Event Hubs で Azure リソースのマネージド ID を使用する方法について説明します
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 784d8c9280aeff7224f90ecee0b16c9c30381aeb
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53087730"
---
# <a name="managed-identities-for-azure-resources-with-event-hubs"></a>Event Hubs での Azure リソースのマネージド ID

[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) は、デプロイに関連付けられ、その下でアプリケーション コードが実行されるセキュリティ保護された ID を作成できる Azure 間機能です。 この ID は、アプリケーションに必要な特定の Azure リソースにアクセスするためのカスタム アクセス許可を付与するアクセス制御ロールに関連付けることができます。 

マネージド ID では、Azure プラットフォームがこのランタイム ID を管理します。 ID 自体やアクセスする必要のあるリソース用に、アクセス キーをアプリケーション コードや構成に保存して保護する必要はありません。 Azure リソースのマネージド ID のサポートが有効になっている Azure App Service アプリケーション内または仮想マシン内で実行されている Event Hubs クライアント アプリでは、SAS のルールとキーまたは他のアクセス トークンを処理する必要はありません。 クライアント アプリに必要なのは、Event Hubs 名前空間のエンドポイント アドレスだけです。 アプリが接続すると、Event Hubs は操作でクライアントにマネージド ID のコンテキストをバインドします。これについては後で例を示します。

マネージド ID に関連付けられた Event Hubs クライアントは、承認されているすべての操作を行うことができます。 マネージド ID を Event Hubs のロールに関連付けることによって承認が付与されます。 

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs のロールとアクセス許可

Event Hubs 名前空間の "所有者" ロールまたは "共同作成者" ロールに対してのみマネージド ID を追加でき、これによって、この名前空間のすべてのエンティティに対するフル コントロールが ID に付与されます。 ただし、名前空間トポロジを変更する管理操作は、最初は Azure Resource Manager を使う場合にのみサポートされます。 ネイティブの Event Hubs REST 管理インターフェイスを使う場合はサポートされません。 このサポートは、.NET Framework クライアントの [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) オブジェクトをマネージド ID 内で使用できないことも意味します。 
 
## <a name="use-event-hubs-with-managed-identities-for-azure-resources"></a>Azure リソースのマネージド ID で Event Hubs を使用する

次のセクションでは、以下の手順について説明します。

1. マネージド ID の下で実行されるサンプル アプリケーションを作成してデプロイします。
2. その ID を Event Hubs 名前空間へのアクセスに付与します。
3. アプリケーションがその ID を使用してイベント ハブと対話する方法。

この概要では、[Azure App Service](https://azure.microsoft.com/services/app-service/) でホストされる Web アプリケーションについて説明します。 VM でホストされるアプリケーションに必要な手順もほぼ同じです。

### <a name="create-an-app-service-web-application"></a>App Service Webアプリケーションを作成する

まず、App Service ASP.NET アプリケーションを作成します。 Azure でこれを行う方法がわからない場合は、[こちらのチュートリアル](../app-service/app-service-web-get-started-dotnet-framework.md)に従ってください。 ただし、チュートリアルで示す MVC アプリケーションを作成する代わりに、Web フォーム アプリケーションを作成してください。

### <a name="set-up-the-managed-identity"></a>マネージド ID を設定する

アプリケーションを作成したら、Azure Portal で新しく作成された Web アプリに移動し (チュートリアルにも示されています)、**[管理対象サービス ID]** ページに移動してこの機能を有効にします。 

![マネージド サービス ID ページ](./media/event-hubs-managed-service-identity/msi1.png)
 
機能を有効にすると、Azure Active Directory に新しいサービス ID が作成され、App Service ホストに構成されます。

### <a name="create-a-new-event-hubs-namespace"></a>新しい Event Hubs 名前空間を作成する

次に、Azure リソースのマネージド ID のプレビューをサポートしている Azure リージョン (**米国東部**、**米国東部 2**、**西ヨーロッパ**) のいずれかで、[Event Hubs 名前空間を作成](event-hubs-create.md)します。 

ポータルで名前空間の **[アクセス制御 (IAM)]** ページに移動し、**[ロールの割り当ての追加]** をクリックして、マネージド ID を**所有者**ロールに追加します。 そのためには、**[アクセス許可の追加]** パネルの **[選択]** フィールドで Web アプリケーションの名前を検索し、エントリをクリックします。 その後、 **[保存]** をクリックします。 これで、Web アプリケーションのマネージド ID は、Event Hubs 名前空間と以前に作成したイベント ハブにアクセスできるようになりました。 

### <a name="run-the-app"></a>アプリの実行

次に、作成した ASP.NET アプリケーションの既定のページを変更します。 [こちらの GitHub リポジトリ](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp)の Web アプリケーション コードを使用することもできます。 

アプリを開始した後、ブラウザーで EventHubsMSIDemo.aspx を参照します。 スタート ページとして設定することもできます。 コードは EventHubsMSIDemo.aspx.cs ファイルにあります。 いくつかの入力フィールドと、Event Hubs に接続してイベントを送受信するための **send** ボタンおよび **receive** ボタンを備えた最小限の Web アプリケーションが作成されます。 

[MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) オブジェクトを初期化する方法に注意してください。 共有アクセス トークン (SAS) トークン プロバイダーを使用するのではなく、コードで `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)` を呼び出してマネージド ID のトークン プロバイダーを作成します。 そのため、保存および使用するシークレットはありません。 マネージド ID のコンテキストから Event Hubs へのフローと承認ハンドシェイクは、トークン プロバイダーによって自動的に処理されます。これは SAS の使用よりも単純なモデルです。

これらの変更を行ったら、アプリケーションを発行して実行します。 適切な発行データを取得するには、Visual Studio で発行プロファイルをダウンロードしてインポートします。

![発行プロファイルのインポート](./media/event-hubs-managed-service-identity/msi3.png)
 
メッセージを送受信するには、名前空間の名前と作成したエンティティの名前を入力し、**[send]** または **[receive]** をクリックします。 
 
マネージド ID は、Azure 環境内と、その ID を構成した App Service デプロイでのみ機能します。 現時点では、マネージド ID は App Service デプロイ スロットでは機能しません。

## <a name="next-steps"></a>次の手順

Event Hubs の詳細については、次のリンクを参照してください。

* [Event Hubs のチュートリアル](event-hubs-dotnet-standard-getstarted-send.md)を開始する
* [Event Hubs の FAQ](event-hubs-faq.md)
* [Event Hubs の価格詳細](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Event Hubs を使用するサンプル アプリケーション](https://github.com/Azure/azure-event-hubs/tree/master/samples)
