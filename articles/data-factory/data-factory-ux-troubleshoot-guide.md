---
title: Azure Data Factory UX のトラブルシューティング
description: Azure Data Factory UX の問題をトラブルシューティングする方法について説明します。
services: data-factory
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: 9a96c385c25eea1c5e217665f162de402a73e558
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567840"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Azure Data Factory UX の問題のトラブルシューティング
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory UX の一般的なトラブルシューティング方法について説明します。

## <a name="adf-ux-not-loading"></a>ADF UX が読み込まれない

> [!NOTE]
> Azure Data Factory UX では、Microsoft Edge と Google Chrome が正式にサポートされています。 他の Web ブラウザーを使用すると、予期しない、またはドキュメントに記載されていない動作が発生する可能性があります。

### <a name="third-party-cookies-blocked"></a>サードパーティの Cookie がブロックされた

ADF UX では、ブラウザーの Cookie を使用してユーザー セッションを維持し、対話型の開発と監視のエクスペリエンスを実現します。 シークレット セッションを使用しているか、広告ブロック機能が有効になっていると、ブラウザーでサードパーティの Cookie がブロックされる可能性があります。 サードパーティの Cookie をブロックすると、空白のページ (https://adf.azure.com/accesstoken.html ) へリダイレクトされる、またはサードパーティの Cookie がブロックされていることを示す警告メッセージが表示されるなど、ポータルの読み込み時に問題が発生する可能性があります。 この問題を解決するには、次の手順に従って、ブラウザーでサードパーティの Cookie オプションを有効にします。

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>すべての Cookie を許可する

1. ブラウザーで **chrome://settings/cookies** にアクセスします。
1. **[Cookie をすべて受け入れる]** オプションを選択します ![Chrome-Allow-All-Cookies](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. ADF UX を最新の状態に更新して、もう一度やり直してください。

#### <a name="only-allow-adf-ux-to-use-cookies"></a>ADF UX のみに Cookie の使用を許可する
すべての Cookie を許可しない場合は、必要に応じて ADF UX のみを許可することもできます。
1. **chrome://settings/cookies** にアクセスします。
1. **[常に Cookie を使用できるサイト]** オプションの下にある **[追加]** をクリックします ![ADF UX を許可されたサイトに追加する](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. **adf.azure.com** サイトを追加し、 **[すべての Cookie]** オプションをオンにして保存します。 ![ADF UX サイトからのすべての Cookie を許可する](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. ADF UX を最新の状態に更新して、もう一度やり直してください。


### <a name="microsoft-edge"></a>Microsoft Edge

1. ブラウザーで **edge://settings/content/cookies** にアクセスします。
1. **[Cookie データの保存と読み取りをサイトに許可する]** が有効になっていて、 **[サードパーティの Cookie をブロックする]** オプションが無効になっていることを確認します ![Edge ですべての Cookie を許可する](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. ADF UX を最新の状態に更新して、もう一度やり直してください。

#### <a name="only-allow-adf-ux-to-use-cookies"></a>ADF UX のみに Cookie の使用を許可する

すべての Cookie を許可しない場合は、必要に応じて ADF UX のみを許可することもできます。

1. **edge://settings/content/cookies** にアクセスします。
1. **[許可]** セクションの **[追加]** をクリックして、**adf.azure.com** サイトを追加します。 ![ADF UX を許可されたサイトに追加する](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. ADF UX を最新の状態に更新して、もう一度やり直してください。

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

* [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
* [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
* [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/)
* [Microsoft Q&A 質問ページ](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
