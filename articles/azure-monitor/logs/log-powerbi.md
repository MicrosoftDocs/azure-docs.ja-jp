---
title: Log Analytics の Power BI と Excel との統合
description: Log Analytics から Power BI に結果を送信する方法
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: 894b87bfa17a59db20b424199a4021ad75b182fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041061"
---
# <a name="log-analytics-integration-with-power-bi"></a>Log Analytics の Power BI との統合

この記事では、Log Analytics から Microsoft Power BI にデータをフィードして、より視覚に訴えるレポートとダッシュボードを作成する方法について説明します。 

## <a name="background"></a>バックグラウンド 

Azure Monitor ログは、ログの取り込みのためのエンドツーエンドのソリューションを提供するプラットフォームです。 [Azure Monitor Log Analytics](../platform/data-platform.md#) は、これらのログを照会するためのインターフェイスです。 Log Analytics を含む Azure Monitor データプラットフォーム全体の詳細については、「[Azure Monitor データ プラットフォーム](../data-platform.md)」を参照してください。 

Microsoft Power BI は、Microsoft のデータ視覚化プラットフォームです。 開始方法の詳細については、[Power BI のホームページ](https://powerbi.microsoft.com/)を参照してください。 


一般的には、無料の Power BI 機能を使用して、視覚に訴えるレポートとダッシュボードを統合し、作成することができます。

より高度な機能を利用するには、Power BI Pro または Premium アカウントの購入が必要になることがあります。 次のような機能が含まれています。 
 - 作業の共有 
 - スケジュールされた更新
 - Power BI アプリ 
 - データフローと増分更新 

詳細については、[Power BI の価格と機能の詳細](https://powerbi.microsoft.com/pricing/)に関するページを参照してください。 

## <a name="integrating-queries"></a>クエリの統合  

Power BI で使用される主なクエリ言語は、[M クエリ言語](/powerquery-m/power-query-m-language-specification/)です。 

Log Analytics クエリを M にエクスポートし、Power BI で直接使用することができます。 クエリが正常に実行されたら、Log Analytics UI の上部の操作バーにある **[エクスポート]** ボタンから **[Export to Power BI (M query)]\(Power BI にエクスポート (M クエリ)\)** を選択します。


:::image type="content" source="media/log-powerbi/export-query2.png" alt-text="エクスポート オプションのメニュー プルダウンを示す Log Analytics クエリ" border="true":::

Log Analytics によって、Power BI で直接使用できる M コードを含む .txt ファイルが作成されます。

## <a name="connecting-your-logs-to-a-dataset"></a>ログのデータセットへの接続 

Power BI データセットは、レポートと視覚化に使用できるデータのソースです。 Log Analytics クエリをデータセットに接続するには、Log Analytics からエクスポートされた M コードを Power BI の空のクエリにコピーします。 

詳細については、[Power BI データセットの概要](/power-bi/service-datasets-understand/)に関する記事を参照してください。 

## <a name="collect-data-with-power-bi-dataflows"></a>Power BI データフローを使用してデータを収集する 

Power BI データフローを使用すると、データを収集して格納することもできます。 詳細については、[Power BI データフロー](/power-bi/service-dataflows-overview)に関する記事を参照してください。

データフローは、データの収集と準備を支援するために設計された "クラウド ETL" の一種です。 データセットは、さまざまなエンティティを接続し、ニーズに合わせてモデル化できるように設計された "モデル" です。

## <a name="incremental-refresh"></a>増分更新 

Power BI データセットと Power BI データフローの両方に、増分更新オプションがあります。 この機能は Power BI データフローと Power BI データセットでサポートされていますが、使用するには Power BI Premium が必要です。  


増分更新を使用すると、クエリの実行時にすべてのデータを何度も取り込む代わりに、小さなクエリを実行し、実行ごとに少量のデータを更新することができます。 大量のデータを保存することもできますが、クエリを実行するたびに新しい増分データを追加することもできます。 この動作は、実行時間の長いレポートに最適です。

Power BI の増分更新は、結果セットに *datetime* フィールドが存在することが前提となります。 増分更新を構成する前に、Log Analytics クエリの結果セットに少なくとも 1 つの *datetime* フィールドが含まれていることを確認してください。 

増分更新の詳細と構成方法については、[Power BI データセットと増分更新](/power-bi/service-premium-incremental-refresh)に関する記事と、[Power BI データフローと増分更新](/power-bi/service-dataflows-incremental-refresh)に関する記事を参照してください。

## <a name="reports-and-dashboards"></a>レポートとダッシュボード

データが Power BI に送信されたら、引き続き Power BI を使用してレポートとダッシュボードを作成できます。

詳細については、[最初の Power BI モデルとレポートの作成方法に関するこのガイド](/learn/modules/build-your-first-power-bi-report/)を参照してください。  

## <a name="excel-integration"></a>Excel との連携

Power BI で使用されるのと同じ M 統合を使用して、Excel スプレッドシートと統合できます。 詳細については、この [Excel との統合方法に関するガイド](https://support.microsoft.com/office/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a)を参照して、Log Analytics からエクスポートした M クエリを貼り付けます。

その他の情報については、「[Log Analytics と Excel の統合](log-excel.md)」に記載されています。

## <a name="next-steps"></a>次のステップ

[Log Analytics クエリ](./log-query-overview.md)の使用を開始します。