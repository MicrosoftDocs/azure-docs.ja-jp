---
title: クイック スタート - Azure API Management インスタンスの作成
description: Azure portal を使用して新しい Azure API Management サービス インスタンスを作成します。
author: vladvino
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/08/2020
ms.author: apimpm
ms.openlocfilehash: 66d0874536b358b720b43780d6ecce879ac866ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90708208"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-portal"></a>クイック スタート」を参照してください。Azure portal を使用して新しい Azure API Management サービス インスタンスを作成する

API Management (APIM) が組織にもたらす利点は、外部、パートナー、社内の開発者に API を公開することによって、社内のデータやサービスの可能性を広げられることです。 API Management は、開発者の取り組み、ビジネス インサイト、分析、セキュリティ、保護を通じて API プログラムの価値を高め、企業にコア コンピテンシーをもたらします。 APIM を使用すると、任意の場所でホストされている既存のバックエンド サービスの最新の API ゲートウェイを作成し、管理できます。 詳細については、[概要](api-management-key-concepts.md)に関するページを参照してください。

このクイック スタートでは、Azure Portal を使用して新しい API Management インスタンスを作成する手順を説明します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

API Management インスタンス

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-a-new-service"></a>新しいサービスの作成

1. Azure portal メニューから **[リソースの作成]** を選択します。 Azure の **[ホーム]** ページから **[リソースの作成]** を選択することもできます。 
   
   :::image type="content" source="media/get-started-create-service-instance/00-CreateResource-01.png" alt-text="[リソースの作成]"::: を選択します

   
1. **[新規]** ページで、 **[統合]**  >  **[API Management]** を選択します。

   新しい Azure API Management インスタンス
   
1. **[API Management サービス]** ページで設定を入力します。

   :::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-create-new.png" alt-text="新しいインスタンス":::
   
   | 設定                 | 説明   |                                                                     
   |-------------------------|-----------------------------------------------|
   | 名前                | API Management サービスの一意の名前。 この名前を後から変更することはできません。 サービス名は、サービスおよび対応する Azure リソースの両方を参照します。 <br/> サービスの名前は、 *\<name\>.azure-api.net* の形式で既定のドメイン名を生成するために使用されます。 カスタム ドメイン名を使用する場合は、[カスタム ドメインの構成](configure-custom-domain.md)に関するページをご覧ください。 |
   | **[サブスクリプション]**: 。          | この新しいサービス インスタンスが作成されるサブスクリプション。   |
   | **リソース グループ**      |  新規または既存のリソース グループを選択します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 |
   | **地域**          | 使用可能な API Management サービスの場所から近くにある地理的リージョンを選択します。 | 
   | **組織名**   | 組織の名前。 この名前は、開発者ポータルのタイトルや通知用電子メールの送信者など、さまざまな場所に使用されます。 |                                                         
   | **管理者のメール アドレス** | **API Management** からのすべての通知が送信されるメール アドレス。   |  
   | **価格レベル**        | サービスを評価するために **[Developer]** レベルを選択します。 このレベルは運用目的では使用できません。 API Management レベルのスケーリングの詳細については、[アップグレードとスケーリング](upgrade-and-scale.md)に関するをページをご覧ください。 |

3. **［作成］** を選択します

    > [!TIP]
    > このサービス レベルに API Management サービスを作成してアクティブにするには、30 分から 40 分かかります。 **[ダッシュボードにピン留めする]** を選択すると、新しく作成したサービスの検索が簡単になります。

[!INCLUDE [api-management-navigate-to-instance](../../includes/api-management-navigate-to-instance.md)]

**[概要]** ページで、サービスのプロパティを確認します。

   API Management インスタンス

API Management サービス インスタンスがオンラインになったら、使用する準備ができています。 [最初の API をインポートして発行する](import-and-publish.md)チュートリアルを開始します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になった場合は、次の手順に従って、リソース グループと、関連するすべてのリソースを削除できます。

1. Azure portal で、「**リソース グループ**」を検索して選択します。 **[ホーム]** ページで **[リソース グループ]** を選択することもできます。 

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-01.png" alt-text="リソース グループのナビゲーション":::

1. **[リソース グループ]** ページで、リソース グループを選択します。

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-02.png" alt-text="リソース グループの選択":::

1. [リソース グループ] ページで、**[リソース グループの削除]** を選択します。 
   
1. リソース グループの名前を入力し、**[削除]** を選択します。

   リソース グループの削除

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [最初の API をインポートして発行する](import-and-publish.md)
