---
title: Azure Monitor におけるログ データの表示と分析 | Microsoft Docs
description: この記事では、Azure portal で Log Analytics を使用して、Azure Monitor においてログ クエリを作成および編集する方法について説明します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: bwren
ms.openlocfilehash: 0e5b9b43e528b37fd994f9131f145abadb33c53b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798478"
---
# <a name="viewing-and-analyzing-log-data-in-azure-monitor"></a>Azure Monitor におけるログ データの表示と分析
Log Analytics は、Azure Monitor におけるログ データ操作とクエリ作成の主要なエクスペリエンスです。 **[Azure Monitor]** メニューの **[ログ]** から Log Analytics を開きます。 「[Azure portal で Log Analytics の使用を開始する](get-started-portal.md)」で、このポータルの概要を参照して、その機能を確認できます。

Log Analytics には、ログ クエリを操作するための次の機能が用意されています。

* 複数のタブ - 複数のクエリを操作する個別のタブを作成します。
* 豊富な視覚エフェクト - さまざまなグラフ作成オプション。
* 強化された IntelliSense と言語オート コンプリート。
* 構文の強調表示 – クエリの読みやすさが向上します。 
* クエリ エクスプローラー - 保存されたクエリと関数にアクセスします。
* スキーマ ビュー – クエリの記述を支援するために、データの構造を確認します。
* 共有 - クエリへのリンクを作成したり、共有されている Azure ダッシュボードにクエリをピン留めしたりします。
* スマート分析 - グラフのスパイクを識別し、その原因を迅速に分析します。
* 列の選択 - クエリ結果の列の並べ替えとグループ化を行います。

> [!NOTE]
> Log Analytics には、Azure portal に含まれていない外部ツールである高度な分析ポータルと同じ機能があります。 高度な分析ポータルは引き続き使用できますが、Azure portal にある高度な分析ポータルへのリンクや他の参照は、この新しいページに置き換えられています。

![Log Analytics](media/portals/log-analytics.png)

## <a name="resource-logs"></a>リソース ログ
Log Analytics は Virtual Machines などのさまざまな Azure リソースと統合されます。 つまり、Azure Monitor に切り替えることなく、また、リソース コンテキストを失うことなく、リソースの監視メニューから直接 Log Analytics を開くことができます。 **[ログ]** は一部の Azure リソースではまだ有効になっていませんが、さまざまな種類のリソース用のポータル メニューに表示されるようになります。

特定のリソースから Log Analytics を開くと、自動的にそのリソースのログ レコードのみにスコープが指定されます。   その他のレコードを含むクエリを記述する場合は、Azure Monitor メニューから開く必要があります。

次のオプションは、Log Analytics のリソース ビューからはまだ利用できません。

- 保存
- 警告の設定
- クエリ エクスプローラー
- 別のワークスペース/リソースへの切り替え (現時点では未計画)


## <a name="firewall-requirements"></a>ファイアウォールの要件
Log Analytics にアクセスするには、ブラウザーが次のアドレスにアクセスできる必要があります。  ブラウザーがファイアウォールを介して Azure Portal にアクセスしている場合は、これらのアドレスへのアクセスを有効にする必要があります。

| Uri | IP | Port |
|:---|:---|:---|
| portal.loganalytics.io | 動的 | 80,443 |
| api.loganalytics.io    | 動的 | 80,443 |
| docs.loganalytics.io   | 動的 | 80,443 |


## <a name="next-steps"></a>次の手順

- [Log Analytics の使用に関するチュートリアル](../../azure-monitor/log-query/get-started-portal.md)を参照します。
- [ログ検索の使用に関するチュートリアル](../../azure-monitor/learn/tutorial-viewdata.md)を参照します。

