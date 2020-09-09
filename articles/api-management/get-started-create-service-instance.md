---
title: Azure API Management インスタンスの作成 | Microsoft Docs
description: 新しい Azure API Management インスタンスを作成するには、このチュートリアルの手順に従います。
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 6c71b88f43570a65edb5d0bea24f623c861f8111
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206284"
---
# <a name="create-a-new-azure-api-management-service-instance"></a>Azure API Management サービスの新しいインスタンスの作成

API Management (APIM) が組織にもたらす利点は、外部、パートナー、社内の開発者に API を公開することによって、社内のデータやサービスの可能性を広げられることです。 API Management は、開発者の取り組み、ビジネス インサイト、分析、セキュリティ、保護を通じて API プログラムの価値を高め、企業にコア コンピテンシーをもたらします。 APIM を使用すると、任意の場所でホストされている既存のバックエンド サービスの最新の API ゲートウェイを作成し、管理できます。 詳細については、[概要](api-management-key-concepts.md)に関するトピックを参照してください。

このクイック スタートでは、Azure Portal を使用して新しい API Management インスタンスを作成する手順を説明します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

![API Management インスタンス](./media/get-started-create-service-instance/get-started-create-service-instance-created.png)

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-a-new-service"></a>新しいサービスの作成

1. Azure portal メニューから **[リソースの作成]** を選択します。 Azure の **[ホーム]** ページから **[リソースの作成]** を選択することもできます。 
   
   ![[リソースの作成] を選択します](./media/get-started-create-service-instance/00-CreateResource-01.png)
   
1. **[新規]** 画面で、 **[統合]** 、 **[API Management]** の順に選択します。
   
   ![新しい Azure API Management インスタンス](./media/get-started-create-service-instance/00-CreateResource-02.png)
   
1. **[API Management サービス]** 画面で設定を入力します。
   
   ![新しいインスタンス](./media/get-started-create-service-instance/get-started-create-service-instance-create-new.png)
   
   | 設定                 | 推奨値                               | 説明                                                                                                                                                                                                                                                                                                                         |
|-------------------------|-----------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名前**                | API Management サービスの一意の名前 | この名前を後から変更することはできません。 サービスの名前は、 *{name}.azure-api.net* の形式で既定のドメイン名を生成するために使用されます。 カスタム ドメイン名を使用する場合は、[カスタム ドメインの構成](configure-custom-domain.md)に関するページをご覧ください。 <br/> サービス名は、サービスおよび対応する Azure リソースへの参照に使用されます。 |
| **サブスクリプション**        | 該当するサブスクリプション                             | この新しいサービス インスタンスが作成されるサブスクリプション。 アクセスできる各種の Azure サブスクリプションの中から、サブスクリプションを 1 つ選択できます。                                                                                                                                                            |
| **リソース グループ**      | *apimResourceGroup*                           | 新規または既存のリソースを選択できます。 リソース グループとは、ライフサイクル、アクセス許可、ポリシーを共有するリソースの集まりです。 [こちら](../azure-resource-manager/management/overview.md#resource-groups)をご覧ください。                                                                                                  |
| **場所**            | "*米国西部*"                                    | 近くの地理的リージョンを選択します。 ドロップダウン リストのボックスには、API Management サービスを利用できるリージョンのみが表示されます。                                                                                                                                                                                                          |
| **組織名**   | 組織の名前                 | この名前は、開発者ポータルのタイトルや通知用電子メールの送信者など、さまざまな場所に使用されます。                                                                                                                                                                                                             |
| **管理者のメール アドレス** | *admin\@org.com*                               | **API Management** からのすべての通知が送信されるメール アドレスを設定します。                                                                                                                                                                                                                                              |
| **価格レベル**        | *開発者*                                   | サービスを評価するために **[開発者]** レベルを設定します。 このレベルは運用目的では使用できません。 API Management レベルのスケーリングの詳細については、[アップグレードとスケーリング](upgrade-and-scale.md)に関するをページをご覧ください。                                                                                                                                    |

3. **[作成]** を選択します。

    > [!TIP]
    > API Management サービスの作成には、通常 20 分から 30 分かかります。 **[ダッシュボードにピン留めする]** を選択すると、新しく作成したサービスの検索が簡単になります。

[!INCLUDE [api-management-navigate-to-instance](../../includes/api-management-navigate-to-instance.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になった場合は、次の手順に従って、リソース グループと、関連するすべてのリソースを削除できます。

1. Azure portal で、「**リソース グループ**」を検索して選択します。 **[ホーム]** ページで **[リソース グループ]** を選択することもできます。 

   ![リソース グループのナビゲーション](./media/get-started-create-service-instance/00-DeleteResource-01.png)

1. **[リソース グループ]** ページで、リソース グループを選択します。

   ![リソース グループのナビゲーション](./media/get-started-create-service-instance/00-DeleteResource-02.png)

1. [リソース グループ] ページで、 **[リソース グループの削除]** を選択します。 
   
1. リソース グループの名前を入力し、 **[削除]** を選択します。

   ![リソース グループの削除](./media/get-started-create-service-instance/00-DeleteResource-03.png)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [最初の API をインポートして発行する](import-and-publish.md)