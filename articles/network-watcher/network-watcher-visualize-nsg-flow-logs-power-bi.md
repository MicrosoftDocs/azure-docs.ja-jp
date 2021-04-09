---
title: Azure NSG フロー ログの視覚化- Power BI
titleSuffix: Azure Network Watcher
description: Power BI を使用してネットワーク セキュリティ グループのフロー ログを視覚化し、Azure Network Watcher 内の IP トラフィックに関する情報を表示する方法について説明します。
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 50f08ffc304962cc66f6eed07adcb5b7e4aa6692
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98011154"
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>ネットワーク セキュリティ グループのフロー ログを Power BI で視覚化する

ネットワーク セキュリティ グループのイングレス IP トラフィックとエグレス IP トラフィックの情報は、ネットワーク セキュリティ グループのフロー ログで確認できます。 これらのフロー ログには、規則ごとの送信フローと受信フロー、フローの適用先の NIC、フローに関する 5 組の情報 (送信元/送信先 IP、送信元/送信先ポート、プロトコル) のほか、トラフィックが許可されたか拒否されたかどうかの情報が記録されます。

フローのログ データに関して、ログ ファイルを手動で検索することによって洞察を得るのは、あまり現実的ではありません。 この記事では、直近のフロー ログを視覚化してネットワークのトラフィックを把握する方法について取り上げます。

> [!Warning]  
> 次の手順は、フロー ログのバージョン 1 に使用できます。 詳細については、「[ネットワーク セキュリティ グループのフローのログ記録の概要](network-watcher-nsg-flow-logging-overview.md)」を参照してください。 次の手順は、変更しなければ、ログ ファイルのバージョン 2 で使用できません。

## <a name="scenario"></a>シナリオ

以下のシナリオでは、NSG フロー ログ データのシンクとして構成したストレージ アカウントに Power BI Desktop を接続します。 ストレージ アカウントとの接続が確立されると、Power BI は、ログをダウンロードして解析し、ネットワーク セキュリティ グループによって記録されたトラフィックを視覚的に表現します。

テンプレートに備わっている視覚的要素から、次の情報を把握することができます。

* トップ トーカー
* 時系列フロー データ (フロー方向ごと、規則に基づく判断ごと)
* ネットワーク インターフェイスの MAC アドレスごとのフロー
* NSG と規則ごとのフロー
* 送信先ポートごとのフロー

標準で用意されているテンプレートは編集することができます。新しいデータや視覚的要素を変更したり、必要に応じてクエリを編集したりすることが可能です。

## <a name="setup"></a>セットアップ

最初に、アカウント内の少なくとも 1 つのネットワーク セキュリティ グループで、そのフローのログ記録を有効にする必要があります。 ネットワーク セキュリティのフローのログ記録を有効にする手順については、「[Introduction to flow logging for Network Security Groups (ネットワーク セキュリティ グループのフローをログに記録する方法)](network-watcher-nsg-flow-logging-overview.md)」の記事をご覧ください。

また、ご利用のコンピューターに Power BI Desktop クライアントがインストールされていること、そしてストレージ アカウントに存在するログ データをダウンロードして読み込めるだけの空き領域がそのコンピューターにあることが必要です。

![Visio の図][1]

### <a name="steps"></a>手順

1. Power BI テンプレート ([Network Watcher PowerBI フロー ログ テンプレート](https://aka.ms/networkwatcherpowerbiflowlogstemplate)) をダウンロードして Power BI Desktop アプリケーションで開きます。
1. 必要なクエリ パラメーターを入力します。
   1. **StorageAccountName**: 読み込んで視覚化する NSG フロー ログの保存先となっているストレージ アカウントの名前を指定します。
   1. **NumberOfLogFiles**: Power BI にダウンロードして視覚化するログ ファイルの数を指定します。 たとえば「50」と入力した場合、直近 50 件のログ ファイルがダウンロードされます。 2 つの NSG を有効にして、このアカウントに NSG フロー ログを送信するように構成した場合、過去 25 時間分のログを表示することができます。

      ![Power BI メイン][2]

1. ストレージ アカウントのアクセス キーを入力します。 有効なアクセス キーは、Azure Portal で目的のストレージ アカウントに移動し、[設定] メニューから **[アクセス キー]** を選択すると確認できます。 **[接続]** をクリックして変更を適用します。

    ![アクセス キー][3]

    ![アクセス キー 2][4]

4. ログがダウンロードされて解析され、あらかじめ作成しておいた視覚的要素が利用できる状態になります。

## <a name="understanding-the-visuals"></a>視覚的要素について

テンプレートには、NSG のフロー ログ データを人間にわかりやすく表示するさまざまな視覚的要素が用意されています。 以下の画像は、データが投入されたときにダッシュボードがどのように表示されるのかを示した例です。 以降、それぞれの視覚的要素について詳しく見ていきます。 

![PowerBI][5]
 
トップ トーカーの視覚的要素には、所定の期間に最も多く接続を開始した IP アドレスが表示されます。 ボックスのサイズは、相対的な接続数に対応します。 

![トップ トーカー][6]

以下の時系列グラフは、所定の期間におけるフローの数を示しています。 上のグラフはフロー方向で色分けされ、下のグラフは許可/拒否の判断ごとに色分けされています。 この視覚的要素を手掛かりにして、時間の経過に伴うトラフィックの傾向を調べたり、トラフィック (または特定の色で示されたトラフィック) に瞬間的に生じる異常な増加や減少を特定したりすることができます。

![所定の期間におけるフロー][7]

以下のグラフは、ネットワーク インターフェイスごとのフローを示したものです。上のグラフはフロー方向で色分けされ、下のグラフは許可/拒否の判断ごとに色分けされています。 この情報をもとに、相対的にどの VM の通信量が多いかや、特定の VM へのトラフィックが許可されているか拒否されているか、という点についての洞察を得ることができます。

![NIC ごとのフロー][8]

以下のドーナツ グラフは、送信先ポート別フローの構成比を示しています。 この情報から、指定した期間に送信先として最もよく使用されたポートを把握することができます。

![ドーナツ][9]

以下の棒グラフは、NSG と規則ごとのフローを示しています。 この情報からは、最も多くのトラフィックを処理した NSG と、各 NSG における規則ごとのトラフィック内訳を確認できます。

![棒グラフ][10]
 
以下のグラフには、ログに存在する NSG や、所定の期間にキャプチャされたフロー数、最も早い時期にキャプチャされたログの日付についての情報が表示されています。 ログの対象になっている NSG やフローの日付範囲は、この情報から把握することができます。

![情報グラフ 1][11]

![情報グラフ 2][12]

このテンプレートには、関心のあるデータだけを表示する手段として以下のスライサーが備わっています。 リソース グループや NSG、各種規則に対してフィルターを適用することが可能です。 さらに、送信元 IP、送信先 IP、送信元ポート、送信先ポート、プロトコル、許可/拒否の判断、ログが記録された日時を条件としてフィルター処理することもできます。

![スライサー][13]

## <a name="conclusion"></a>まとめ

このシナリオでは、Network Watcher から得られるネットワーク セキュリティ グループのフロー ログと Power BI を使い、トラフィックを視覚化して把握する方法を紹介しました。 Power BI は、標準で備わっているテンプレートを使用して、ストレージから直接ログをダウンロードし、ローカルで処理します。 テンプレートの読み込みにかかる時間は、リクエストされたファイルの数やダウンロードされたファイルの合計サイズによって変動します。

皆さんの要件に合わせて、このテンプレートを自由にカスタマイズしてご利用ください。 Power BI とネットワーク セキュリティ グループのフロー ログには、さまざまな活用方法があります。 

## <a name="notes"></a>メモ

* 既定では、`https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/` にログが保存されます。

    * 別のディレクトリにもデータが存在する場合、データをプルして処理するクエリに変更を加える必要があります。

* 1 GB を超えるサイズのログには、標準で備わっているテンプレートはお勧めできません。

* 大量のログが存在する場合は、別のデータ ストア (Data Lake、SQL Server など) を使った解決方法について調べるようお勧めします。

## <a name="next-steps"></a>次の手順

Elastick Stack で NSG フロー ログを視覚化する方法については、「[オープン ソース ツールを使用した Azure Network Watcher NSG フロー ログの視覚化](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)」を参照してください

[1]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure7.png
[8]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure8.png
[9]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure9.png
[10]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure10.png
[11]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure11.png
[12]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure12.png
[13]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure13.png
