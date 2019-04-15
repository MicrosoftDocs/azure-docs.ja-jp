---
title: 公開リージョンとエンドポイント
titleSuffix: Azure Cognitive Services
description: 3 つのオーサリング リージョンとそのポータルが多数の公開リージョンをすべてサポートしています。 LUIS アプリを公開するリージョンは、Azure LUIS エンドポイント キーを作成するときに Azure portal で指定するリージョンまたは場所に対応します。 アプリを公開するとき、キーに関連付けられているリージョンに対して、エンドポイント URL が LUIS によって自動的に生成されます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 20ea2eb632a6d685351178691cc3d0f58a567902
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891414"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>作成と公開のリージョンと関連付けられているキー

3 つのオーサリング リージョンは、対応する LUIS ポータルによってサポートされます。 LUIS アプリを複数のリージョンに公開するには、リージョンごとに少なくとも 1 つのキーが必要です。 

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS オーサリング リージョン
リージョンに基づいて、3 つの LUIS オーサリング ポータルがあります。 作成と公開は同じリージョンで行う必要があります。 

|LUIS|オーサリング リージョン|Azure リージョン名|
|--|--|--|
|[www.luis.ai][www.luis.ai]|米国<br>ヨーロッパ以外<br>オーストラリア以外| `westus`|
|[au.luis.ai][au.luis.ai]|オーストラリア| `australiaeast`|
|[eu.luis.ai][eu.luis.ai]|ヨーロッパ|`westeurope`|

オーサリング リージョンには、[ペア フェールオーバー リージョン](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)があります。 

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>公開リージョンと Azure リソース
アプリは、LUIS ポータルで追加された LUIS リソースと関連付けられているすべてのリージョンに発行されます。 たとえば、[www.luis.ai][www.luis.ai] で作成されたアプリの場合は、**westus** で LUIS または Cognitive Service リソースを作成し、[それをリソースとしてアプリに追加する](luis-how-to-azure-subscription.md)と、アプリはそのリージョンで公開されます。 

## <a name="public-apps"></a>パブリック アプリ
パブリック アプリはすべてのリージョンで公開されるので、リージョン ベースの LUIS リソース キーを持つユーザーは、どのリージョンがそれらのリソース キーと関連付けられているかにかかわらず、アプリにアクセスできます。

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>公開リージョンはオーサリング リージョンに関連付けられています。

オーサリング リージョン アプリは、対応する公開リージョンにのみ公開できます。 お使いのアプリが間違ったオーサリング リージョンにある場合は、アプリをエクスポートし、ご自身の公開リージョンの正しいオーサリング リージョンにインポートしてください。 

https://www.luis.ai で作成された LUIS アプリは、[ヨーロッパ](#publishing-to-europe) リージョンと[オーストラリア](#publishing-to-australia) リージョンを除くすべてのエンドポイントに公開できます。 

## <a name="publishing-to-europe"></a>ヨーロッパへの公開

ヨーロッパ リージョンに公開するには、 https://eu.luis.ai でのみ LUIS を作成します。 それ以外の場所で、ヨーロッパ リージョンのキーを使って公開しようとすると、LUIS によって警告メッセージが表示されます。 代わりに https://eu.luis.aiを使用してください。 [https://eu.luis.ai][eu.luis.ai] で作成された LUIS アプリは、他のリージョンに自動的には移行されません。 移行するには、その LUIS アプリをエクスポートしてから、インポートします。

## <a name="europe-publishing-regions"></a>ヨーロッパの公開リージョン

 グローバル リージョン | オーサリング API リージョンとオーサリング Web サイト| 公開およびクエリ実行リージョン<br>`API region name`   |  エンドポイント URL 形式   |
|-----|------|------|------|
| [ヨーロッパ](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| フランス中部<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [ヨーロッパ](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 北ヨーロッパ<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [ヨーロッパ](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 西ヨーロッパ<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [ヨーロッパ](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 英国南部<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>オーストラリアへの公開

オーストラリア リージョンに公開するには、 https://au.luis.ai でのみ LUIS を作成します。 それ以外の場所で、オーストラリア リージョンのキーを使って公開しようとすると、LUIS によって警告メッセージが表示されます。 代わりに https://au.luis.aiを使用してください。 [https://au.luis.ai][au.luis.ai] で作成された LUIS アプリは、他のリージョンに自動的には移行されません。 移行するには、その LUIS アプリをエクスポートしてから、インポートします。

## <a name="australia-publishing-regions"></a>オーストラリアの公開リージョン

 グローバル リージョン | オーサリング API リージョンとオーサリング Web サイト| 公開およびクエリ実行リージョン<br>`API region name`   |  エンドポイント URL 形式   |
|-----|------|------|------|
| [オーストラリア](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| オーストラリア東部<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>他のリージョンへの公開

他のリージョンに公開するには、[https://www.luis.ai](https://www.luis.ai) でのみ LUIS アプリを作成します。 

## <a name="other-publishing-regions"></a>その他の公開リージョン

 グローバル リージョン | オーサリング API リージョンとオーサリング Web サイト| 公開およびクエリ実行リージョン<br>`API region name`   |  エンドポイント URL 形式   |
|-----|------|------|------|
| アジア | `westus`<br>[www.luis.ai][www.luis.ai]| インド中部<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| アジア | `westus`<br>[www.luis.ai][www.luis.ai]| 東アジア<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| アジア | `westus`<br>[www.luis.ai][www.luis.ai]| 東日本<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| アジア | `westus`<br>[www.luis.ai][www.luis.ai]| 西日本<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| アジア | `westus`<br>[www.luis.ai][www.luis.ai]| 韓国中部<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| アジア | `westus`<br>[www.luis.ai][www.luis.ai]| 東南アジア<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北米 |`westus`<br>[www.luis.ai][www.luis.ai] | カナダ中部<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北米 |`westus`<br>[www.luis.ai][www.luis.ai] | 米国中央部<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北米 |`westus`<br>[www.luis.ai][www.luis.ai] | 米国東部<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北米 | `westus`<br>[www.luis.ai][www.luis.ai] | 米国東部 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北米 | `westus`<br>[www.luis.ai][www.luis.ai] | 米国中北部<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| 北米 | `westus`<br>[www.luis.ai][www.luis.ai] | 米国中南部<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| 北米 |`westus`<br>[www.luis.ai][www.luis.ai] | 米国中西部<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 北米 | `westus`<br>[www.luis.ai][www.luis.ai] | 米国西部<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| 北米 |`westus`<br>[www.luis.ai][www.luis.ai] | 米国西部 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| 南アメリカ | `westus`<br>[www.luis.ai][www.luis.ai] | ブラジル南部<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="endpoints"></a>エンドポイント

LUIS には現在、オーサリング用に 1 つと、クエリ予測分析用に 1 つの 2 つのエンドポイントがあります。

|目的|URL|
|--|--|
|Authoring|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|テキスト解析 (クエリ予測)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

次の表は、前の表で中かっこ `{}` で囲まれているパラメーターの説明です。

|パラメーター|目的|
|--|--|
|region|Azure リージョン - 作成と公開には異なるリージョンが使用されます。|
|appID|URL ルートで使用され、アプリ ダッシュボードに表示される LUIS アプリ ID|
|q|チャット ボットなどのクライアント アプリケーションから送信される発話テキスト|

## <a name="failover-regions"></a>フェールオーバー リージョン

各リージョンには、フェールオーバー先のセカンダリ リージョンがあります。 ヨーロッパではヨーロッパ内でフェールオーバーし、オーストラリアではオーストラリア内でフェールオーバーします。

オーサリング リージョンには、[ペア フェールオーバー リージョン](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)があります。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [事前構築済みエンティティのリファレンス](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
