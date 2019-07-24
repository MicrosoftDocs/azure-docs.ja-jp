---
title: Power BI アプリ オファーを発行する - Azure Marketplace | Microsoft Docs
description: Microsoft AppSource マーケットプレースに Power BI アプリ オファーを発行します。
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 0e82041dab1932ed7c129b598740b2441aa59315
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822754"
---
# <a name="publish-a-power-bi-app-offer"></a>Power BI アプリ オファーを発行する

Cloud パートナー ポータルでオファーを定義し、関連する技術資産を作成した後の最後のステップでは、オファーを発行するために送信します。 このプロセスを開始するには、**[新しいオファー]** ウィンドウの左側のウィンドウで、**[発行]** を選択します。 詳しくは、「[Azure Marketplace および AppSource のオファーを発行する](../manage-offers/cpp-publish-offer.md)」をご覧ください。


## <a name="publishing-steps"></a>発行手順

発行プロセスの主な手順を次に示します。

![Power BI アプリ オファーの発行プロセスの手順](./media/publishing-process-steps.png)

次の表は、各手順についての説明と、推定完了時間を示しています。

|   発行ステップ            |   Time     |   説明                                                                  |
| --------------------         |------------| ----------------                                                               |
| 前提条件の検証       | 約 15 分     | プラン情報とプラン設定が有効化されます。                            |
| 認定                | 1 ～ 7 日間   | Power BI の認定チームがオファーを分析します。 チームで、提供されたインストール URL を使用してアプリをインストールすることにより、Power BI アプリで手動検証テストを実行します。 アプリ認定プロセスの一環として、主な検証が行われます (このドキュメントでのちほど説明します)。         |
| 梱包                    | 1 時間以内  | このオファーの技術資産が、顧客用にパッケージ化されます。                        |
| リード生成の登録 | 1 時間以内  | リード システムを構成して展開します。                                      |
| 発行元のサインオフ            | \-         | オファーが稼働状態になる前に、最終的なレビューと確認を行います。 また、オファリングのプレビューへのリンクも表示されます。 プレビューの表示方法に満足したら、**[状態]** タブで **[起動]** を選択します。これにより、AppSource へのアプリの掲載要求がオンボード チームに送信されます。    |
| ライブ                         | \< 3 時間 | オファーは AppSource 上のリストに公開され ("ライブ")、顧客は Power BI のサブスクリプションでアプリを表示して展開できるようになりました。 確認の電子メールも届きます。 **[すべてのオファー]** の右側の列で、すべてのオファーの状態を確認できます。 **[状態]** タブをクリックすると、オファーの発行フローの状態を詳しく確認できます。 |
|   |   |

このプロセスの完了には、最大で 8 日かかります。 以上の発行手順が済むと、Power BI アプリ オファーは [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) の Power BI アプリ セクションに掲載されます。


### <a name="app-certification-process"></a>アプリ認定プロセス

Microsoft のオンボード チームは、このプロセスを使用して、Power BI アプリ オファーの提出を検証します。

1. 法的ドキュメントおよびヘルプ リンクを確認します。
2. サポートの連絡先情報を確認します。
3. インストーラー URL を使用して、適切なインストールを確認します。
4. アプリをスキャンし、マルウェアや他の悪意のあるコンテンツがないかどうかを確認します。
5. 表示されているコンテンツとアプリの説明が一致することを確認します。
6. アプリに関連する操作が Power BI で想定どおりに機能することを確認します。 チームは、サンプル データを使ってレポートとダッシュボードを開き、カスタム データ ソースに接続し、データを最新の情報に更新したりします。

何らかの問題が見つかった場合、認定チームはフィードバックを提供します。  Power BI アプリの要件の詳細については、[Power BI アプリのドキュメント](https://go.microsoft.com/fwlink/?linkid=2028636)をご覧ください。


## <a name="next-steps"></a>次の手順

[AppSource マーケットプレース](https://appsource.microsoft.com) でアプリを定期的に監視することをお勧めします。  自分のマーケットプレースの顧客とアプリの利用状況について分析情報を取得するには、[Cloud パートナー ポータル](https://cloudpartner.azure.com/#insights)の [Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md) の機能も使用する必要があります。 最後に、[オファーを更新](./cpp-update-existing-offer.md)できます。
