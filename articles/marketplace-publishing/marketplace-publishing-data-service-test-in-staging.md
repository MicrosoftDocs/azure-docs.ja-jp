---
title: "Marketplace のデータ サービス プランのテスト | Microsoft Docs"
description: "Azure Marketplace のデータ サービス プランをテストする方法を理解します。"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: e861bd11-f74d-4d77-b4b5-23fb463644ad
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: daf2017bfbab4250e3f1481f592e0e858c77f121


---
# <a name="testing-your-data-service-offer-in-staging"></a>ステージング環境でのデータ サービス プランのテスト
> [!IMPORTANT]
> **現時点では、新しいデータ サービスの発行元はオンボードされなくなりました。一覧への新しいデータ サービスの追加は承認されません。** SaaS ビジネス アプリケーションを AppSource で発行する場合、詳細については[こちら](https://appsource.microsoft.com/partners)をご覧ください。 IaaS アプリケーションまたは開発者サービスを Azure Marketplace で発行する場合、詳細については[こちら](https://azure.microsoft.com/marketplace/programs/certified/)をご覧ください。
> 
> 

最初の 2 つの手順 ([Microsoft 開発者アカウントの作成](marketplace-publishing-accounts-creation-registration.md)と[公開ポータルでのデータ サービス プランの作成](marketplace-publishing-data-service-creation.md)) が完了したら、Azure Marketplace でオファーを使用可能にする準備が整います。 このトピックでは「ステージング」と呼ばれる第一の中間的なステップについて説明します。

ステージングとは、プランをプライベートの「サンドボックス」にデプロイすることです。そこでは、本番にプッシュする前に、機能をテストおよび検証できます。 ステージングでは、プランはそれをデプロイしたお客様に表示されるのと同じように表示されます。

## <a name="step-1-pushing-your-offer-to-staging"></a>手順 1. ステージングへのプランのプッシュ
ステージングにプランをプッシュすることで、のちにサブスクライバーがプランを使用可能になる前にプランをテストできます。  自分のデータのサブスクライバーに対して、プランがどのように表示されどのように機能するかを確認できます。  

  ![図](media/marketplace-publishing-data-service-test-in-staging/step-1.1.png)

1.  [発行ポータル](https://publish.windowsazure.com)
2. 左側のナビゲーション ウィンドウで [ **Data Services** ] を選択します。
3. ステージング環境にプッシュするプランを選択します。 上記の画面が表示されます。
4. [ **ステージングにプッシュ** ] ボタンをクリックします。  
5. プランにおいて、ステージングにプッシュする前に完了すべき問題がある場合には、一覧が表示されます。  一覧の各項目をクリックしてそれらの項目を修正してください。 すべての修正を終えたら、[ **ステージングにプッシュ** ] ボタンを再度クリックします。

プランに問題がない場合は、以下のようなポップアップ ウィンドウが表示されます。  

プランを Azure ポータルに上げる計画がない / 承認が下りていない場合 (現在は容量に制限がある)、ポップアップ ウィンドウはそのまま閉じてください。

(DataMarket ポータルに加えて) Azure ポータルの Data Sevice をテストするには、テストで使用する Azure サブスクリプション ID が必要になります。  このサブスクリプション ID は、プランのテストに使用できるアカウントを識別します。  

サブスクリプション ID を貼り付け、チェックマークをクリックして続行します。

  ![図](media/marketplace-publishing-data-service-test-in-staging/step-1.2.png)

> [!NOTE]
> これらの Azure サブスクリプション ID は、 [Azure 管理ポータル](https://manage.windowsazure.com)でのテストとステージングにのみ必要です。 Azure Marketplace でのテストには必要ではありません。
> 
> 

次に現れる画面では、下に黄色く強調表示された "In progress" アイコンによって発行が進行中であることがわかります。 ステージングへのプッシュは 10 ～ 15 分かかります。  それより時間がかかる場合、最初にブラウザーを更新してください (IE で F5 を押下)。  まれではありますが、プランがステージングへのプッシュを 1 時間過ぎてもまだ行っていたら、その場合はお問い合わせのリンクをクリックして問題報告を行ってください。

  ![図](media/marketplace-publishing-data-service-test-in-staging/step-1.3.png)

ステージングへのプッシュが完了すると、"In progress" アイコンの動きが止まり、状態が "Staged" に更新されます。  これでプランのテストの準備ができました。  

## <a name="step-2-test-your-staged-offer-in-datamarket"></a>手順 2. ステージされたプランを DataMarket でテストする
[ **サービス プランを表示** ] というテキストに続くリンクをクリックして、プランが本番稼働したときにサブスクライバーが参照し、DataMarket に表示される画面を表示してください。

  ![図](media/marketplace-publishing-data-service-test-in-staging/step-2.2.png)

上でマークされた 12 項目をそれぞれテストまたは検証し、すべてのロゴ、価格 / トランザクション、テキスト、画像、ドキュメント、リンクが正しく、適切に動作していることを確認してください。  これは、プランの作成時に入力したテスト値が実際の値に置き換えられていることを確認するのに良いタイミングです。

1. プラン ロゴ
2. プラン名
3. 発行者名 / 会社の Web サイトへのリンク
4. プランの検索カテゴリ
5. サブスクライバーを支援するためのプランのサポート リンク
6. プランのコンテキストの説明
7. 課金の詳細を示すオファー プラン
8. 実装コードへのリンク
9. プラン データの使用法を図解するサンプル イメージ
10. プラン内の各サービスに対するメタデータの入力 / 出力
11. プランの利用規約
12. プラン データのプレビュー

最後に、リンク "EXPLORE THIS DATASET" をクリックすることで、Datamarket でサービスが動作することを確認します。  "Service Explorer" と呼ばれるツールで新しいウィンドウが開き、プランに関してクエリの結果をプレビューできます。  このウィンドウでは、必要なパラメーターを入力でき、サービスに関するクエリ結果の表示も可能です。   また、クエリに関する URL も表示されます。  

> [!NOTE]
> 必ず先頭に表示されるサービスの説明テキストを確認してください。  プランが複数のサービス呼び出しで構成されている場合、一番下のタブをクリックして、次のサービスに移動してレビューおよびテストできます。
> 
> 

## <a name="next-step"></a>次のステップ
問題が発生して解決にヘルプが必要な場合は、 [Azure パブリッシャー サポート](http://go.microsoft.com/fwlink/?LinkId=272975)にご連絡ください。

プランの発行準備が整ったら、「 [本番にプッシュするための承認の要求](marketplace-publishing-push-to-production.md) 」ドキュメントを参照してください。

## <a name="see-also"></a>関連項目
* [Getting Started: How to publish an offer to the Azure Marketplace (概要: Azure Marketplace へのプランの発行方法)](marketplace-publishing-getting-started.md)




<!--HONumber=Nov16_HO3-->


