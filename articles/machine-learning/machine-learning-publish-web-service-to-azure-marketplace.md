---
title: "(非推奨) Azure Marketplace に Machine Learning Web サービスを発行する | Microsoft Docs"
description: "(非推奨) Azure Marketplace に Azure Machine Learning Web サービスを発行する方法"
services: machine-learning
documentationcenter: 
author: BharathS
manager: jhubbard
editor: cgronlun
ms.assetid: 68e908be-3a99-4cd7-9517-e2b5f2f341b8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: deprecated
ms.date: 01/06/2017
ms.author: bharaths
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 85a58e09e05fdb50984055ab25c4c2fe520dab4d
ms.openlocfilehash: 2d62966f130f6778c9561393cc7fc338f8903f1e


---
# <a name="deprecated-publish-azure-machine-learning-web-service-to-the-azure-marketplace"></a>(非推奨) Azure Marketplace への Azure Machine Learning Web サービスの発行

> [!NOTE]
> DataMarket および Data Services は間もなく提供終了となる予定です。既存のサブスクリプションは、2017 年 3 月 31 日付けで提供終了となり、取り消されます。 その結果、この記事は非推奨となる予定です。 
> 
> 代わりに、Machine Learning 実験を [Cortana Intelligence Gallery ギャラリー](https://gallery.cortanaintelligence.com/)に発行して、データ サイエンス コミュニティのために役立てることができます。 詳しくは、「[Cortana Intelligence ギャラリーでリソースを共有および発見する](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-gallery-how-to-use-contribute-publish)」をご覧ください。

Azure Marketplace は、外部の顧客が使用する有料または無料のサービスとして Azure Machine Learning Web サービスを発行する機能を提供します。 この記事では、プロセスの概要と使用開始のためのガイドラインへのリンクを示します。 このプロセスを使用することで、他の開発者が Web サービスをアプリケーション内で使用できるようになります。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="overview-of-the-publishing-process"></a>発行プロセスの概要
Azure Machine Learning Web サービスを Azure Marketplace に発行するための手順を次に示します。

1. Machine Learning Request-Response サービス (RRS) を作成して発行する
2. サービスを運用環境にデプロイし、API キーと OData のエンドポイント情報を取得する
3. 発行済みの Web サービスの URL を使用して、 [Azure Marketplace (データ マーケット)](https://publish.windowsazure.com/workspace/) 
4. 送信すると、プランの内容が確認され、顧客が購入するにはこのサービスが承認される必要があります。 発行プロセスには数営業日かかります。 

## <a name="walk-through"></a>チュートリアル
### <a name="step-1-create-and-publish-a-machine-learning-request-response-service-rrs"></a>手順 1: Machine Learning Request-Response サービス (RRS) を作成して発行する
 まだ完了していない場合は、こちらの [チュートリアル](machine-learning-walkthrough-5-publish-web-service.md)をご覧ください。

### <a name="step-2-deploy-the-service-to-production-and-obtain-the-api-key-and-odata-endpoint-information"></a>手順 2: サービスを運用環境にデプロイし、API キーと OData のエンドポイント情報を取得する
1. [[Azure クラシック ポータル]](http://manage.windowsazure.com)で、左側のナビゲーション バーから **[MACHINE LEARNING]** オプションを選択し、ワークスペースを選択します。 
2. **[WEB サービス]** タブをクリックし、マーケットプレースに発行する Web サービスを選択します。
   
    ![Azure Marketplace][workspace]
3. マーケットプレースで使用するエンドポイントを選択します。 追加のエンドポイントを作成しない場合は、 **[既定]** のエンドポイントを選択できます。
4. エンドポイントをクリックすると、 **[API キー]**が表示されます。 この情報は後の手順 3 で必要になりますので、コピーしておきます。
   
    ![Azure Marketplace][apikey]
5. **[要求/応答]** メソッドをクリックします。この時点で、マーケットプレースへのバッチ実行サービスの公開はサポートされていません。 [要求/応答] メソッドの API ヘルプ ページが表示されます。
6. **[OData エンドポイント アドレス]** をコピーします。この情報は後の手順 3 で必要になります。
   
    ![Azure Marketplace][odata]

サービスを運用環境にデプロイします。

### <a name="step-3-use-the-url-of-the-published-web-service-to-publish-to-azure-marketplace-datamarket"></a>手順 3: 発行済みの Web サービスの URL を使用して、Azure Marketplace (データ マーケット) に発行する
1. [Azure Marketplace (データ マーケット)](http://datamarket.azure.com/home) 
2. ページの上部にある **[発行]** リンクをクリックします。 [[Microsoft Azure 発行ポータル]](https://publish.windowsazure.com)
3. **[発行者]** セクションをクリックし、発行者として登録します。
4. 新しいプランを作成するには、**[Data Services]** を選択し、**[新しいデータ サービスの作成]** をクリックします。 
   
   ![Azure Marketplace][image1]
   
   <br />
5. **[プラン]** で、料金プランなどのサービスに関する情報を入力します。 無料サービスまたは有料サービスのいずれを提供するかを決定します。 有料にするには、銀行や税などの支払い情報を入力します。
6. **[マーケティング]** で、プランのタイトルや説明などプランに関する情報を入力します。
7. **[料金]** で、国固有のプランの価格を設定するか、システムによるサービスの "自動料金設定" を使用します。
8. **[データ サービス]** タブをクリックし、**[データ ソース]** として **[Web サービス]** をクリックします。
   
    ![Azure Marketplace][image2]
9. 上記の手順 2 で説明したように、Azure クラシック ポータルから、Web サービスの URL と API キーを取得します。
10. [マーケットプレース データ サービスのセットアップ] ダイアログで、OData エンドポイント アドレスを **[サービス URL]** テキスト ボックスに貼り付けます。
11. **認証**するには、**[認証スキーム]** として **[ヘッダー]** を選択します。
    
    * **[ヘッダー名]**に「Authorization」と入力します。
    * **[ヘッダー値]** に「Bearer」と入力し、**スペース** キーを押した後、API キーを貼り付けます。
    * **[このサービスは OData]** チェック ボックスをオンにします。
    * **[接続テスト]** をクリックして接続をテストします。
12. **[カテゴリ]** で、**[Machine Learning]** が選択されていることを確認します。
13. プランに関するメタデータをすべて入力したら、**[発行]** をクリックし、**[Push to Staging]** をクリックします。 この時点で、修正する必要のある問題が残っている場合は通知されます。
14. すべての問題が解決されたことを確認したら、 **[Request approval to push to Production]**をクリックします。 発行プロセスには数営業日かかります。 

[image1]:./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
[image2]:./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
[workspace]:./media/machine-learning-publish-web-service-to-azure-marketplace/selectworkspace.png
[apikey]:./media/machine-learning-publish-web-service-to-azure-marketplace/apikey.png
[odata]:./media/machine-learning-publish-web-service-to-azure-marketplace/odata.png




<!--HONumber=Jan17_HO2-->


