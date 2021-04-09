---
title: 公開リージョンとエンドポイント - LUIS
description: Azure portal で指定するリージョンは、LUIS アプリを発行する場所と同じであり、この同じリージョンに対してエンドポイント URL が生成されます。
ms.service: cognitive-services
ms.subservice: language-understanding
author: aahill
ms.author: aahi
ms.topic: reference
ms.date: 01/21/2021
ms.custom: references_regions
ms.openlocfilehash: 8b43fc472f3247a93414a0b18d9098c6dfb94917
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98681609"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>作成と公開のリージョンと関連付けられているキー

LUIS オーサリング リージョンは、LUIS ポータルによってサポートされます。 LUIS アプリを複数のリージョンに公開するには、リージョンごとに少なくとも 1 つのキーが必要です。

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS オーサリング リージョン

[!INCLUDE [portal consolidation](includes/portal-consolidation.md)]

LUIS には、リージョンに関係なく使用できる 1 つのポータル [www.luis.ai](https://www.luis.ai) があります。 それでも、作成と公開は同じリージョンで行う必要があります。

オーサリング リージョンには、[ペアになったフェールオーバー リージョン](../../best-practices-availability-paired-regions.md)があります

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>公開リージョンと Azure リソース

アプリは、LUIS ポータルで追加された LUIS リソースと関連付けられているすべてのリージョンに発行されます。 たとえば、[www.luis.ai][www.luis.ai] で作成されたアプリの場合は、**westus** で LUIS または Cognitive Service リソースを作成し、[それをリソースとしてアプリに追加する](luis-how-to-azure-subscription.md)と、アプリはそのリージョンで公開されます。

## <a name="public-apps"></a>パブリック アプリ
パブリック アプリはすべてのリージョンで公開されるので、リージョン ベースの LUIS リソース キーを持つユーザーは、どのリージョンがそれらのリソース キーと関連付けられているかにかかわらず、アプリにアクセスできます。

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>公開リージョンはオーサリング リージョンに関連付けられています。

オーサリング リージョン アプリは、対応する公開リージョンにのみ公開できます。 お使いのアプリが間違ったオーサリング リージョンにある場合は、アプリをエクスポートし、ご自身の公開リージョンの正しいオーサリング リージョンにインポートしてください。

> [!NOTE]
> https://www.luis.ai で作成した LUIS アプリは、[ヨーロッパ](#publishing-to-europe)および[オーストラリア](#publishing-to-australia) リージョンを含むすべてのエンドポイントに発行できるようになりました。

## <a name="publishing-to-europe"></a>ヨーロッパへの公開

 グローバル リージョン | オーサリング API リージョン | 公開およびクエリ実行リージョン<br>`API region name`   |  エンドポイント URL 形式   |
|-----|------|------|------|
| ヨーロッパ | `westeurope`| フランス中部<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| ヨーロッパ | `westeurope`| 北ヨーロッパ<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| ヨーロッパ | `westeurope`| 西ヨーロッパ<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| ヨーロッパ | `westeurope`| 英国南部<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>オーストラリアへの公開

 グローバル リージョン | オーサリング API リージョン | 公開およびクエリ実行リージョン<br>`API region name`   |  エンドポイント URL 形式   |
|-----|------|------|------|
| オーストラリア | `australiaeast` | オーストラリア東部<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="other-publishing-regions"></a>その他の公開リージョン

 グローバル リージョン | オーサリング API リージョン | 公開およびクエリ実行リージョン<br>`API region name`   |  エンドポイント URL 形式   |
|-----|------|------|------|
| アフリカ | `westus`<br>[www.luis.ai][www.luis.ai]| 南アフリカ北部<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| アジア | `westus`<br>[www.luis.ai][www.luis.ai]| インド中部<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| アジア | `westus`<br>[www.luis.ai][www.luis.ai]| 東アジア<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| アジア | `westus`<br>[www.luis.ai][www.luis.ai]| 東日本<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| アジア | `westus`<br>[www.luis.ai][www.luis.ai]| 西日本<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| アジア | `westus`<br>[www.luis.ai][www.luis.ai]| 韓国中部<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| アジア | `westus`<br>[www.luis.ai][www.luis.ai]| 東南アジア<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| アジア | `westus`<br>[www.luis.ai][www.luis.ai]| アラブ首長国連邦北部<br>`northuae`     |   `https://northuae.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北米 |`westus`<br>[www.luis.ai][www.luis.ai] | カナダ中部<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北米 |`westus`<br>[www.luis.ai][www.luis.ai] | 米国中部<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北米 |`westus`<br>[www.luis.ai][www.luis.ai] | 米国東部<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北米 | `westus`<br>[www.luis.ai][www.luis.ai] | 米国東部 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北米 | `westus`<br>[www.luis.ai][www.luis.ai] | 米国中北部<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北米 | `westus`<br>[www.luis.ai][www.luis.ai] | 米国中南部<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北米 |`westus`<br>[www.luis.ai][www.luis.ai] | 米国中西部<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北米 | `westus`<br>[www.luis.ai][www.luis.ai] | 米国西部<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北米 |`westus`<br>[www.luis.ai][www.luis.ai] | 米国西部 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 南アメリカ | `westus`<br>[www.luis.ai][www.luis.ai] | ブラジル南部<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |

## <a name="endpoints"></a>エンドポイント

[オーサリングおよび予測エンドポイント](developer-reference-resource.md)の詳細について説明します。

## <a name="failover-regions"></a>フェールオーバー リージョン

各リージョンには、フェールオーバー先のセカンダリ リージョンがあります。 ヨーロッパではヨーロッパ内でフェールオーバーし、オーストラリアではオーストラリア内でフェールオーバーします。

オーサリング リージョンには、[ペア フェールオーバー リージョン](../../best-practices-availability-paired-regions.md)があります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [事前構築済みのエンティティのリファレンス](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai