---
title: Event Grid ポータルによる Media Services イベントの監視
description: この記事では、Azure Media Services イベントを監視するために Event Grid をサブスクライブする方法を説明します。
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: Azure Media Services, ストリーム, ブロードキャスト, ライブ, オフライン
ms.service: media-services
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 3828d5288c730e8fc0595a0deb6f1cb3267e885e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608788"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-portal"></a>Azure portal を使用した Event Grid による Media Services イベントの作成と監視

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

Azure Event Grid は、クラウドのイベント処理サービスです。 このサービスは、[イベント サブスクリプション](../../../event-grid/concepts.md#event-subscriptions)を使って、イベント メッセージをサブスクライバーにルーティングします。 Media Services イベントには、データの変更に対応するために必要なすべての情報が含まれます。 Media Services イベントは、eventType プロパティが "Microsoft.Media" で始まっていることで識別できます。 詳細については、「[Media Services イベントのスキーマ](media-services-event-schemas.md)」を参照してください。

この記事では、Azure portal を使用して、Azure Media Services アカウントのイベントをサブスクライブします。 次に、イベントをトリガーして結果を表示します。 通常は、イベント データを処理し、アクションを実行するエンドポイントにイベントを送信します。 この記事では、メッセージを収集して表示する Web アプリにイベントを送信します。

最後に、イベント データが Web アプリに送信されたことを確認します。

## <a name="prerequisites"></a>前提条件 

* 有効な Azure サブスクリプションを持っている。
* [このクイックスタート](../create-account-howto.md)の説明に従って、新しい Azure Media Services アカウントを作成します。

## <a name="create-a-message-endpoint"></a>メッセージ エンドポイントの作成

Media Services アカウントのイベントをサブスクライブする前に、イベント メッセージのエンドポイントを作成しましょう。 通常、エンドポイントは、イベント データに基づくアクションを実行します。 この記事では、イベント メッセージを表示する[構築済みの Web アプリ](https://github.com/Azure-Samples/azure-event-grid-viewer)をデプロしします。 デプロイされたソリューションには、App Service プラン、App Service Web アプリ、および GitHub からのソース コードが含まれています。

1. **[Deploy to Azure]\(Azure にデプロイ\)** を選択して、ソリューションをサブスクリプションにデプロイします。 Azure portal で、パラメーターの値を指定します。

   [![[Deploy to Azure]\(Azure にデプロイ\) というラベルが付けられたボタンが表示されている画像。](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)

1. デプロイが完了するまでに数分かかる場合があります。 デプロイが成功した後で、Web アプリを表示して、実行されていることを確認します。 Web ブラウザーで `https://<your-site-name>.azurewebsites.net` にアクセスします

"Azure Event Grid ビューアー" サイトに切り替えると、まだイベントがないことがわかります。
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-media-services-events"></a>Media Services イベントのサブスクライブ

どのイベントを追跡し、どこにイベントを送信するかは、トピックを購読することによって Event Grid に伝えます。

1. ポータルで Media Services アカウントを選択し、 **[イベント]** を選択します。
1. イベントをビューアー アプリに送信するには、エンドポイントの Web hook を使用します。 

   ![[webhook] を選択する](../media/monitor-events-portal/select-web-hook.png)

1. イベント サブスクリプションには、Media Services アカウントの値があらかじめ入力されています。 
1. **[エンドポイントのタイプ]** には "webhook" を選択します。
1. このトピックでは、 **[すべてのイベントの種類を購読します]** チェック ボックスをオンにしておきます。 ただし、オフにして、特定のイベントの種類をフィルター処理することもできます。 
1. **[エンドポイントの選択]** リンクをクリックします。

    Web hook エンドポイントに対して、Web アプリの URL を入力し、ホーム ページの URL に `api/updates` を追加します。 

1. **[選択内容の確認]** をクリックします。
1. **[作成]** をクリックします。
1. サブスクリプションに名前を付けます。

   ![ログの選択](../media/monitor-events-portal/create-subscription.png)

1. Web アプリをもう一度表示し、その Web アプリにサブスクリプションの検証イベントが送信されたことに注目します。 

    Event Grid は検証イベントを送信するので、エンドポイントはイベント データを受信することを確認できます。 エンドポイントは、`validationResponse` から `validationCode` を設定する必要があります。 詳細については、「[Event Grid security and authentication](../../../event-grid/security-authentication.md)」(Event Grid のセキュリティと認証) を参照してください。 Web アプリ コードを表示して、サブスクリプションがどのように検証されるかを確認できます。

では、イベントをトリガーして、Event Grid がメッセージをエンドポイントに配信するようすを見てみましょう。

## <a name="send-an-event-to-your-endpoint"></a>エンドポイントへのイベントの送信

エンコード ジョブを実行して、Media Services アカウントのイベントをトリガーできます。 [このクイック スタート](../stream-files-dotnet-quickstart.md)に従って、ファイルをエンコードし、イベントの送信を開始できます。 すべてのイベントをサブスクライブしている場合は、次のような画面が表示されます。

> [!TIP]
> 目のアイコンを選択してイベント データを展開します。 すべてのイベントを表示したい場合は、ページを最新の情報に更新しないでください。

![サブスクリプション イベントの表示](../media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>次のステップ

[アップロード、エンコード、およびストリーミング](../stream-files-tutorial-with-api.md)
