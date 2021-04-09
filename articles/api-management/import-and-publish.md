---
title: チュートリアル - Azure API Management での最初の API のインポートと発行
description: このチュートリアルでは、OpenAPI 仕様の API を Azure API Management にインポートして、その API を Azure portal でテストする方法について説明します。
author: mikebudzynski
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: apimpm
ms.openlocfilehash: 9ff64f57e61002101b4e2c560bdcd91863cc461e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91626967"
---
# <a name="tutorial-import-and-publish-your-first-api"></a>チュートリアル:最初の API のインポートと発行

このチュートリアルでは、OpenAPI 仕様のバックエンド API を JSON 形式で Azure API Management にインポートする方法について説明します。 この例で使用されるバックエンド API は Microsoft から提供されており、Azure ([https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json)) でホストされています。

バックエンド API を API Management にインポートした後は、API Management API がバックエンド API のファサードになります。 ファサードは、API Management でニーズに応じてカスタマイズすることができ、そのためにバックエンド API に触れる必要はありません。 詳しくは、「[API を変換および保護する](transform-api.md)」をご覧ください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * API Management に API をインポートする
> * Azure Portal での API のテスト

インポート後は、Azure portal で API を管理できます。

:::image type="content" source="media/import-and-publish/created-api.png" alt-text="API Management の新しい API":::

## <a name="prerequisites"></a>前提条件

- [Azure API Management の用語](api-management-terminology.md)を理解する。
- [Azure API Management インスタンスを作成する](get-started-create-service-instance.md)。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a>バックエンド API のインポートと発行

このセクションでは、OpenAPI 仕様のバックエンド API をインポートおよび発行する方法を示します。

1. API Management インスタンスの左側のナビゲーションで **[API]** を選択します。
1. **[OpenAPI]** タイルを選択します。
1. **[OpenAPI 仕様から作成する]** ウィンドウで **[Full]\(すべて\)** を選択します。
1. 以下の表の値を入力します。 次に、 **[作成]** を選択して API を作成します。

   API の値は、作成時に、または後で **[設定]** タブに移動して設定できます。

   :::image type="content" source="media/import-and-publish/create-api.png" alt-text="API の作成":::


   |設定|[値]|説明|
   |-------|-----|-----------|
   |**OpenAPI の仕様**|*https:\//conferenceapi.azurewebsites.net?format=json*|API を実装するサービス。 要求は、API Management によってこのアドレスに転送されます。|
   |**表示名**|前述のサービス URL を入力すると、JSON に基づく値が API Management によってこのフィールドに入力されます。|[開発者ポータル](api-management-howto-developer-portal.md)に表示される名前。|
   |**名前**|前述のサービス URL を入力すると、JSON に基づく値が API Management によってこのフィールドに入力されます。|API の一意の名前。|
   |**説明**|前述のサービス URL を入力すると、JSON に基づく値が API Management によってこのフィールドに入力されます。|API の説明 (省略可)。|
   |**URL スキーム**|**HTTPS**|API にアクセスできるプロトコル。|
   |**API URL サフィックス**|*conference*|API Management サービスのベース URL に付加されるサフィックス。 API Management では API がサフィックスによって識別されるため、サフィックスは、特定の発行者のすべての API で一意である必要があります。|
   |**タグ**| |検索、グループ化、フィルター処理を目的に API を整理するためのタグ。|
   |**成果物**|**無制限**|1 つまたは複数の API の関連付け。 すべての API Management インスタンスは、次の 2 つのサンプル成果物を備えています: **スターター** と **無制限**。 API の発行は、API に成果物 (この例では "**無制限**") を関連付けることで行います。<br/><br/> 1 つの成果物に複数の API を追加し、開発者ポータルを通じて開発者に提供できます。 この API を別の成果物に追加するには、成果物名を入力または選択します。 複数の成果物に API を追加するには、この手順を繰り返します。 後で **[設定]** ページから成果物に API を追加することもできます。<br/><br/>  成果物の詳細については、[成果物の作成と発行](api-management-howto-add-products.md)に関する記事を参照してください。|
   |**ゲートウェイ**|**マネージド**|API を公開する API ゲートウェイ。 このフィールドは、**Developer** レベルおよび **Premium** レベルのサービスでのみ使用できます。<br/><br/>**マネージド** とは、API Management サービスに組み込まれ、Microsoft によって Azure でホストされるゲートウェイを意味します。 [セルフホステッド ゲートウェイ](self-hosted-gateway-overview.md)は、Premium および Developer サービス レベルでのみ使用できます。 これらはオンプレミスまたは他のクラウドにデプロイできます。<br/><br/> ゲートウェイが選択されていない場合、API は使用できず、API 要求は成功しません。|
   |**この API をバージョン管理しますか?**|選択または選択解除|詳細については、[複数のバージョンの API の公開](api-management-get-started-publish-versions.md)に関する記事をご覧ください。|

   > [!NOTE]
   > API を API コンシューマーに発行するには、API を成果物に関連付ける必要があります。

2. **［作成］** を選択します

API 定義のインポートで問題が発生した場合は、[既知の問題と制約事項の一覧](api-management-api-import-restrictions.md)を参照してください。

## <a name="test-the-new-api-in-the-azure-portal"></a>Azure portal での新しい API のテスト

Azure portal には、API の操作を表示およびテストするための便利な環境が用意されており、操作を直接呼び出すことができます。

1. API Management インスタンスの左側のナビゲーションで **[API]**  >  **[Demo Conference API]\(デモ会議 API)** を選択します。
1. **[テスト]** タブを選択し、 **[GetSpeakers]** を選択します。 クエリ パラメーターとヘッダーがあれば、このページの **[クエリ パラメーター]** と **[ヘッダー]** に表示されます。 **Ocp-Apim-Subscription-Key** には、この API に関連付けられているサブスクリプション キーの値が自動的に入力されます。
1. **[送信]** を選択します。

   :::image type="content" source="media/import-and-publish/01-import-first-api-01.png" alt-text="Azure portal で API をテストする":::

   バックエンドは **200 OK** といくつかのデータで応答します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * 最初の API のインポート
> * Azure Portal での API のテスト

次のチュートリアルに進み、成果物を作成して発行する方法を学習してください。

> [!div class="nextstepaction"]
> [成果物を作成して発行する](api-management-howto-add-products.md)
