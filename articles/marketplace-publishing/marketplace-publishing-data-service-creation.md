---
title: "Marketplace 用データ サービスの作成ガイド | Microsoft Docs"
description: "Azure Marketplace で購入できるデータ サービスを作成、認定、デプロイする方法について詳しく説明します。"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 96194198-6991-43b4-918e-ee337e250339
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: c0c9362f1c2e15c947aaaf7187f3383ad243140f


---
# <a name="data-service-publishing-guide-for-the-azure-marketplace"></a>Azure Marketplace のデータ サービスの発行ガイド
> [!IMPORTANT]
> **現時点では、新しいデータ サービスの発行元はオンボードされなくなりました。一覧への新しいデータ サービスの追加は承認されません。** SaaS ビジネス アプリケーションを AppSource で発行する場合、詳細については[こちら](https://appsource.microsoft.com/partners)をご覧ください。 IaaS アプリケーションまたは開発者サービスを Azure Marketplace で発行する場合、詳細については[こちら](https://azure.microsoft.com/marketplace/programs/certified/)をご覧ください。
> 
> 

手順 1 の「[アカウントの作成および登録](marketplace-publishing-accounts-creation-registration.md)」では、Azure Marketplace のデータ サービス オファーの[技術以外の一般的な要件](marketplace-publishing-pre-requisites.md)と[技術的な要件](marketplace-publishing-data-service-creation-prerequisites.md)について説明しました。 ここでは、Azure Marketplace 向けの[発行ポータル][link-pubportal]で、データ サービス プランを作成する手順について説明します。

## <a name="1----login-to-the-publishing-portal"></a>1.  発行ポータルにログインします。
[https://publish.windowsazure.com](https://publish.windowsazure.com.)

**初めて発行ポータルにログインする場合は、デベロッパー センターに会社の販売者プロファイルファイルを登録したときに使用したものと同じアカウントを使用します。**  発行ポータルでは、他の従業員を共同管理者として後で追加できます。

初めて発行ポータルにログインする場合は、 **[Data Services の発行]** タイルをクリックします。

## <a name="2----choose-data-services-in-the-navigation-menu-on-the-left-side"></a>2.  左側のナビゲーション メニューの **[Data Services]** を選択します。
  ![図](media/marketplace-publishing-data-service-creation/pubportal-main-nav.png)

## <a name="3----create-a-new-data-service"></a>手順 3.  新しいデータ サービスを作成します。
新しいデータ サービス オファーのタイトルを入力し、右側の [+] をクリックします。

  ![図](media/marketplace-publishing-data-service-creation/step-3.png)

## <a name="4----review-the-sub-menu-under-the-newly-created-data-service-in-the-navigation-menu"></a>4.  ナビゲーション メニューで新しく作成したデータ サービスのサブメニューを確認します。
**[チュートリアル]** タブをクリックし、Azure Marketplace に適切にデータ サービスを発行するために必要なすべての手順を確認します。

> [!TIP]
> [チュートリアル] ページのリンクと、左側のデータ サービス オファーのサブメニューのタブはいつでも使用できます。
> 
> 

## <a name="5----create-a-new-plan"></a>5.  新しいオファーを作成します。
### <a name="offers-plans-transactions"></a>オファー、プラン、トランザクション。
各オファーには複数のプランを含めることができます。また、少なくとも 1 つのプランを含める必要があります。 エンドユーザーがオファーにサブスクライブする場合、オファーのプランのいずれかにサブスクライブします。 各プランには、エンドユーザーがサービスを使用できる方法が定義されています。

現在、Azure Marketplace は、Data Services の月単位のサブスクリプション トランザクション ベース モデルのみをサポートしています。そのため、エンドユーザーは、サブスクライブしたプランの料金に従って月額料金を支払います。また、プランで定義されている月数のトランザクションを使用できます。

各トランザクションは、通常、サービスに送信されるクエリに基づいて、データ サービスが返すレコード数と定義されています。 既定値は 100 です。 各クエリから返されるトランザクション数は、レコード数を 100 で割り、最も近い整数に切り上げられた数になります。

各クエリで使用されるトランザクション数を監視 (測定) するのは、Azure Marketplace サービス レイヤーの役割です。

> [!IMPORTANT]
> 月内にトランザクション上限に達したエンドユーザーは、毎月のサブスクリプション サイクルが終了するまで、サービスを継続して使用できなくなります。
> 
> 指定したプラン (プランの 1 つ) に数が無制限のトランザクションを含めることができます (必須ではありません)。
> 
> 

### <a name="create-a-plan"></a>プランを作成します。
1. [新しいプランの追加] の横にある **[+]** をクリックします。
2. このプランの使用量として **[無制限]** または **[制限あり]** を選択します。  [制限あり] の場合、プランで 1 か月に使用できるトランザクション数を指定します。
   
    ![図](media/marketplace-publishing-data-service-creation/step-5.1.png)  
   
    発行ポータルからは、"プラン ID" も提案されます。プラン ID は、UI でエンドユーザーにプラン名を通知するために使用されます。また、Marketplace サービスがプランを識別するためにも使用されます。 必要に応じて、"プラン ID" を変更できます。
   
   > [!NOTE]
   > "プラン ID" は、各オファーの範囲内で一意である必要があります。 発行ポータルで使用される他の多くの ID と同様に、プランが初めて発行され、運用が開始された後はロックされ、ID を変更できなくなります。
   > 
   > 
3. クリックして選択を確定します。
4. 新しく作成したプランに関する質問がいくつか表示されます。
   
    ![図](media/marketplace-publishing-data-service-creation/step-5.2.png)

| 質問 | 重要性 |
| --- | --- |
| **このプランは無料で世界中で使用できますか。** |完全に無料のプランを作成できます。 このオファーに 1 つのプランしかない場合、Marketplace に "無料のオファー" を発行することを意味します。 (複数あるうちの) 1 つのプランのみについての場合、エンドユーザーに、毎月のトランザクション数が比較的少ないサービスに関する情報を提供することができます。  これに該当する場合、追加の質問は表示されません。 |

> [!NOTE]
> エンドユーザーは、有料プランにいつでもアップグレードできます。
> 
> 

| 質問 | 重要性 |
| --- | --- |
| **無料試用版はありますか。** |まったく "試用版なし" を選択するか、"1 か月間" のプランを使用するオプションを提供することができます。 一般的に、発行元は、1 か月間無料のオプションを選択して、エンドユーザーにオファーの利点を理解してもらうことを好みます。 |

> [!IMPORTANT]
> 支払い方法 (クレジット カード、エンタープライズ契約など) の設定が完了している場合にのみ、エンドユーザーは無料試用版を購入できます。
> 
> 1 か月間の無料試用版の終了後、ユーザーがサブスクリプションのキャンセルを行わなかった場合、Azure Marketplace のサブスクリプション日時点の料金の課金が開始されます。 特別な通知はエンドユーザーに送信されません。
> 
> 

| 質問 | 重要性 |
| --- | --- |
| **このプランの購入にプロモーション コードは必要ですか。** |発行元は、"プロモーション コード" という特殊なコードを特定のユーザーに提供することで、サービス プランへのアクセスを制限することができます。 このプロモーション コードを持っているエンドユーザーのみが、プランにサブスクライブできるようになります。 [いいえ] を選択すると、オファーを使用できるリージョンの全ユーザー (詳細については、 [Marketplace マーケティング コンテンツガイド](marketplace-publishing-push-to-staging.md) を参照してください) が、このプランにサブスクライブできるようになります。 追加の質問は表示されません。 |
| **有効なプロモーション コードを持っていないユーザーにこのプランを表示しないことはできますか。** |前の質問に対する回答が「はい」の場合、発行元には、Marketplace の UI にこのプランが完全に表示されないようにするオプションがあります。 つまり、オファーの詳細ページにこのプランが表示されなくなります。 プロモーション コードを受け取って購入したエンドユーザーは、そのプロモーション コードを使用してサブスクライブできます。 |

## <a name="6----create-your-marketplace-marketing-content"></a>6.  Marketplace のマーケティング コンテンツを作成します。
**[マーケティング]、[価格]、[サポート]、[カテゴリ]** の各タブに必要な情報を提供する方法については、「[Marketplace マーケティング コンテンツ ガイド](marketplace-publishing-push-to-staging.md)」を参照してください。このガイドは、Azure Marketplace で発行されているすべてのアーティファクトに共通しています。  

## <a name="7----connect-your-offer-to-your-service-sql-azure-based-or-web-service-based"></a>7.  サービス (SQL Azure ベースまたは Web サービス ベース) にオファーを接続します。
**[Data Services]** サブメニューをクリックします。

ページの上部で、オファーの **[名前空間]**を指定するように求められます。  

  ![図](media/marketplace-publishing-data-service-creation/step-7.png)

以下の質問で、発行元が新しく作成したオファーを Azure Marketplace に表示する方法を定義します (詳細については、[Data Services の技術的な前提条件に関するガイド](marketplace-publishing-data-service-creation-prerequisites.md)を参照してください)。

  ![図](media/marketplace-publishing-data-service-creation/step-7.2.png)

**データベース ベースのサービスの発行**

**[データベース]**をクリックします。 次のページが表示されます。

  ![図](media/marketplace-publishing-data-service-creation/step-7.3.png)

SQL Azure DB に基づいてデータセットの CSDL マッピングを作成するには:

  ![図](media/marketplace-publishing-data-service-creation/step-7.4.png)

次に各テーブルについて設定します。

  ![図](media/marketplace-publishing-data-service-creation/step-7.5.png)

  ![図](media/marketplace-publishing-data-service-creation/step-7.6.png)

Web サービスの場合

  ![図](media/marketplace-publishing-data-service-creation/step-7.7.png)

> [!IMPORTANT]
> CSDL Web サービスの作成の詳細な手順と例については、 [既存の Web サービスを CSDL 経由で OData にマッピングする方法](marketplace-publishing-data-service-creation-odata-mapping.md) についてのページを参照してください。
> 
> 

## <a name="next-steps"></a>次のステップ
ここでは、データ サービス オファーを作成しました。「[Marketplace マーケティング コンテンツ ガイド](marketplace-publishing-push-to-staging.md)」の手順を完了してから、[ステージング環境でのデータ サービスのテスト](marketplace-publishing-data-service-test-in-staging.md)に関するページに進んでください。

## <a name="see-also"></a>関連項目
* [Getting Started: How to publish an offer to the Azure Marketplace (概要: Azure Marketplace へのプランの発行方法)](marketplace-publishing-getting-started.md)
* 全体的な OData マッピングのプロセスと目的を理解したい場合は、こちらの [データ サービスの OData マッピング](marketplace-publishing-data-service-creation-odata-mapping.md) に関する記事を読んで、定義、構造、手順を確認してください。
* 特定のノードとそのパラメーターについて知りたい場合は、定義と説明、例、ユース ケースのコンテキストなどが記載された、この [データ サービスの OData マッピング ノード](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) に関する記事を参照してください。
* 例に関心がある場合は、 [データ サービス OData マッピング例](marketplace-publishing-data-service-creation-odata-mapping-examples.md) に関するページでサンプル コードを参照し、コード構文とコンテキストを学習してください。

[link-pubportal]:https://publish.windowsazure.com



<!--HONumber=Dec16_HO2-->


