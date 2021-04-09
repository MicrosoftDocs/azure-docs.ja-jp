---
title: 'ML Studio (classic): Azure AI Gallery データを管理する - Azure'
description: インターフェイスまたは AI Gallery Catalog API を使用して、Azure AI Gallery から製品内ユーザー データをエクスポートおよび削除することができます。 この記事では、その方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: mldocs
ms.openlocfilehash: 3461b8026079d089bacca9f12aa94e13f5cb2874
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519237"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Azure AI Gallery からの製品内ユーザー データの表示と削除



インターフェイスまたは AI Gallery Catalog API を使用して、Azure AI Gallery から製品内ユーザー データを表示および削除することができます。 この記事では、その方法について説明します。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>UI を使用して AI Gallery のデータを表示する

Azure AI Gallery の Web サイト UI を使用して公開した項目を表示することができます。 ユーザーは、次のようにして、ソリューション、プロジェクト、実験、およびその他の公開された項目のうち、パブリック項目と一覧にない項目の両方を表示することができます。

1.    [Azure AI Gallery](https://gallery.azure.ai/) にサインインします。
2.    右上隅にあるプロファイル画像をクリックします。次に、アカウント名をクリックして、プロファイル ページを読み込みます。
3.    プロファイル ページには、一覧にないエントリを含め、ギャラリーに公開されたすべての項目が表示されます。

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>AI Gallery Catalog API を使用してデータを表示する

[https://catalog.cortanaanalytics.com/entities](https://catalog.cortanaanalytics.com/entities ) からアクセスでき、AI Gallery Catalog API を使用して収集されたデータをプログラムで表示することができます。 データを表示するには、作成者 ID が必要です。 Catalog API を使用して一覧にないエンティティを表示するには、アクセス トークンが必要です。

Catalog の応答は JSON 形式で返されます。

### <a name="get-an-author-id"></a>作成者 ID を取得する
作成者 ID は、Azure AI Gallery への公開時に使用されたメール アドレスに基づきます。 変更されることはありません。

1. [Azure AI Gallery](https://gallery.azure.ai/) にサインインします。
2. 右上隅にあるプロファイル画像をクリックします。次に、アカウント名をクリックして、プロファイル ページを読み込みます。
3. アドレス バーの URL には、`authorId=` の後に英数字の ID が表示されます。 たとえば、次のような URL があるとします。\
    `https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

    作成者 ID は次のようになります。\
    `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>アクセス トークンを取得する

Catalog API を使用して一覧にないエンティティを表示するには、アクセス トークンが必要です。 アクセス トークンがなくても、ユーザーは引き続きパブリック エンティティとその他のユーザー情報を表示することができます。

アクセス トークンを取得するには、ログイン中にブラウザーによる Catalog API への HTTP 要求の `DataLabAccessToken` ヘッダーを検査する必要があります。

1.    [Azure AI Gallery](https://gallery.azure.ai/) にサインインします。
2.    右上隅にあるプロファイル画像をクリックします。次に、アカウント名をクリックして、プロファイル ページを読み込みます。
3.    F12 キーを押して、ブラウザーの開発者ツール ウィンドウを開き、[ネットワーク] タブを選択してページを更新します。 
4. フィルター テキスト ボックスに *catalog* という文字列を入力して、要求をフィルター処理します。
5.    URL `https://catalog.cortanaanalytics.com/entities` への要求で、GET 要求を見つけて、 *[ヘッダー]* タブを選択します。 *[要求ヘッダー]* セクションまで下にスクロールします。
6.    ヘッダー `DataLabAccessToken` の下に英数字トークンがあります。 データの安全を維持するため、このトークンは共有しないでください。

### <a name="view-user-information"></a>ユーザー情報を表示する
前の手順で取得した作成者 ID を使用し、次の URL の `[AuthorId]` を置き換えて、ユーザーのプロファイルの情報を表示します。

`https://catalog.cortanaanalytics.com/users/[AuthorID]`

たとえば、以下のような URL 要求があるとします。

`https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

この場合、次のような応答が返されます。

```json
{"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}
```

### <a name="view-public-entities"></a>パブリック エンティティを表示する

Catalog API には、[AI Gallery Web サイト](https://gallery.azure.ai/)で直接表示することもできる、Azure AI Gallery に公開されたエンティティに関する情報が格納されます。 

公開されたエンティティを表示するには、`[AuthorId]` を、上記の「[作成者 ID を取得する](#get-an-author-id)」で取得した作成者 ID に置き換えて、以下の URL にアクセスします。

`https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'`

次に例を示します。

`https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'`

### <a name="view-unlisted-and-public-entities"></a>一覧にないエンティティとパブリック エンティティを表示する

このクエリでは、パブリック エンティティのみが表示されます。 一覧にないものを含む、すべてのエンティティを表示するには、前のセクションで取得したアクセス トークンを指定します。

1.    [Postman](https://www.getpostman.com) などのツールを使用し、「[アクセス トークンを取得する](#get-your-access-token)」の説明に従って、カタログ URL への HTTP GET 要求を作成します。
2.    アクセス トークンに設定された値を使用して、`DataLabAccessToken` という HTTP 要求ヘッダーを作成します。
3.    HTTP 要求を送信します。

> [!TIP]
> 一覧にないエンティティが Catalog API からの応答に表示されない場合は、ユーザーのアクセス トークンが無効であるか有効期限が切れている可能性があります。 Azure AI Gallery からサインアウトし、「[アクセス トークンを取得する](#get-your-access-token)」の手順を繰り返してトークンを更新してください。 
