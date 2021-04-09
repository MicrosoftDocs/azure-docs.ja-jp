---
title: Translator リソースを作成する
titleSuffix: Azure Cognitive Services
description: この記事では、Azure Cognitive Services Translator リソースを作成し、サブスクリプション キーとエンドポイント URL を取得する方法について説明します。
services: cognitive-services
author: laujan
ms.author: lajanuar
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: a0d8532d19aff41bc5e7defb3b58462e81018749
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101712931"
---
# <a name="create-a-translator-resource"></a>Translator リソースを作成する

この記事では、Azure portal で Translator リソースを作成する方法について説明します。 [Azure Translator](translator-info-overview.md) は、クラウドベースの機械翻訳サービスであり、REST API の [Azure Cognitive Services](../what-are-cognitive-services.md) ファミリに含まれます。 Azure リソースは、作成するサービスのインスタンスです。 Azure サービスに対するすべての API 要求には、アクセスを認証するために、**エンドポイント** URL と読み取り専用の **サブスクリプション キー** が必要です。

## <a name="prerequisites"></a>前提条件

開始するには、アクティブな [**Azure アカウント**](https://azure.microsoft.com/free/cognitive-services/)が必要です。  お持ちでない場合は、[**無料の 12 か月のサブスクリプションを作成**](https://azure.microsoft.com/free/)できます。

## <a name="translator-resource-types"></a>Translator リソースの種類

Translator サービスには、2 つの異なるリソースの種類を使用してアクセスできます。

* **単一サービス** リソースの種類を使用すると、1 つのサービス API キーとエンドポイントにアクセスできます。  

* **マルチサービス** リソースの種類を使用すると、1 つの API キーとエンドポイントを使用して複数の Cognitive Services にアクセスできます。 現在、Cognitive Services リソースは次のサービスで使用できます。
  * Language ([Translator](../translator/translator-info-overview.md)、[Language Understanding (LUIS)](../luis/what-is-luis.md)、[Text Analytics](../text-analytics/overview.md))  
  * Vision ([Computer Vision](../computer-vision/overview.md))、([Face](../face/overview.md))  
  * Decision ([Content Moderator](../content-moderator/overview.md))  

## <a name="create-your-resource"></a>リソースを作成する

* Azure portal の [ **[Create Translator]\(Translator の作成\)**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) ページに直接移動し、プロジェクトの詳細を入力します。

* Azure portal の [ **[Create Cognitive Services]\(Cognitive Services の作成\)**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) ページに直接移動し、プロジェクトの詳細を入力します。

>[!TIP]
>必要に応じて、Azure portal のホーム ページから、次のように **作成** プロセスを開始できます。
>
> 1. [**Azure portal**](https://ms.portal.azure.com/#home) のホーム ページに移動します。
> 1. Azure サービス メニューから ➕ **[リソースの作成]** を選択します。
>1. **[Marketplace を検索]** 検索ボックスに、「**Translator**」(単一サービス リソース) または「**Cognitive Services**」(マルチサービス リソース) と入力し、選択します。  上記の [リソースの種類の選択](#create-your-resource)に関する記述を "*参照*" してください。
> 1. **[作成]** を選択すると、プロジェクトの詳細ページが表示されます。
><br/><br/>

## <a name="complete-your-project-and-instance-details"></a>プロジェクトとインスタンスの詳細を入力する

1. **[サブスクリプション]**。 使用できる Azure サブスクリプションのいずれかを選択します。

1. **[リソース グループ]**。 選択した Azure リソース グループは、新しいリソースの仮想コンテナーとして機能します。 新しいリソース グループを作成するか、同じライフサイクル、アクセス許可、ポリシーを共有する既存のリソース グループにリソースを追加することができます。

1. **[リソース リージョン]** . ご自身のビジネスまたはアプリケーションが特定のリージョンを必要としない限り、 **[グローバル]** を選択します。 Translator は、非リージョン サービスです。特定の Azure リージョンンに依存することはありません。 「[Azure のリージョンと Availability Zones](../../availability-zones/az-overview.md)」を "*参照*" してください。

1. **[名前]**。 自分のリソースに対して選択した名前を入力します。 選択した名前は Azure 内で一意である必要があります。

> [!NOTE]
> カスタム ドメイン エンドポイントを必要とする Translator 機能を使用する場合は、[名前] フィールドに入力する値が、エンドポイントのカスタム ドメイン名パラメーターになります。

5. **価格レベル**。 自分のニーズに合った[価格レベル](https://azure.microsoft.com/pricing/details/cognitive-services/translator)を選択します。

   * 各サブスクリプションに Free レベルがあります。
   * Free レベルは有料プランと同じ機能を備えており、有効期限がありません。
   * アカウントごとに 1 つだけ無料サブスクリプションが許可されます。</li></ul>

1. マルチサービス リソースを作成した場合は、チェック ボックスを使用して追加の使用状況の詳細を確認する必要があります。

1. **[確認および作成]** を選択します。

1. サービス条件を確認し、 **[作成]** を選択してリソースをデプロイします。

1. リソースが正常にデプロイされた後、 **[リソースに移動]** を選択します。

### <a name="authentication-keys-and-endpoint-url"></a>認証キーとエンドポイント URL

すべての Cognitive Services API 要求には、認証のためにエンドポイント URL と読み取り専用キーが必要です。

* **認証キー**。 自分のキーは、Translation サービスへの要求ごとに渡される一意の文字列です。 自分のキーは、クエリ文字列パラメーターを使用するか、HTTP 要求ヘッダーで指定して渡すことができます。

* **エンドポイント URL**。 特定の Azure リージョンが必要な場合を除き、API 要求ではグローバル エンドポイントを使用します。 「[ベース URL](reference/v3-0-reference.md#base-urls)」を "*参照*" してください。 グローバル エンドポイント URL は `api.cognitive.microsofttranslator.com` です。

## <a name="get-your-authentication-keys-and-endpoint"></a>認証キーとエンドポイントを取得する

1. 新しいリソースがデプロイされた後、 **[リソースに移動]** を選択するか、自分のリソース ページに直接移動します。
1. 左側のレールの *[リソース管理]* で、 **[キーとエンドポイント]** を選択します。
1. 自分のサブスクリプション キーとエンドポイント URL をコピーして、*Microsoft Notepad* などのアクセスしやすい場所に貼り付けます。

:::image type="content" source="../media/cognitive-services-apis-create-account/get-cog-serv-keys.png" alt-text="キーとエンドポイントを取得します。":::

## <a name="how-to-delete-a--resource-or-resource-group"></a>リソースまたはリソース グループを削除する方法

> [!Warning]
> リソース グループを削除すると、そのグループに含まれるすべてのリソースも削除されます。

Cognitive Services または Translator リソースを削除するには、**リソースを削除** するか、**リソース グループを削除** します。

リソースを削除するには:

1. Azure portal で、リソース グループに移動します。
1. 隣接するチェック ボックスをオンにして、削除するリソースを選択します。
1. 右端近くの上部メニューから **[削除]** を選択します。
1. **[Deleted Resources]\(削除対象のリソース\)** ダイアログ ボックスに "*はい*" と入力します。
1. **[削除]** を選択します。

リソース グループを削除するには:

1. Azure portal で、リソース グループに移動します。
1. 左端近くの上部メニュー バーから、 **[リソース グループの削除]** を選択します。
1. リソース グループ名を入力し、 **[削除]** を選択して、削除要求を確定します。

## <a name="how-to-get-started-with-translator"></a>Translator の使用を開始する方法

このクイックスタートでは、REST API で Translator サービスを使用する方法について説明します。

> [!div class="nextstepaction"]
> [Translator を使ってみる](quickstart-translator.md)

## <a name="more-resources"></a>その他のリソース

* [Microsoft Translator のコード サンプル](https://github.com/MicrosoftTranslator).  GitHub で、多言語の Translator のコード サンプルを入手できます。
* [Microsoft Translator サポート フォーラム](https://www.aka.ms/TranslatorForum)
* [Azure を使ってみる (3 分間のビデオ)](https://azure.microsoft.com/get-started/?b=16.24)