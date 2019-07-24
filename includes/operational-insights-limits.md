---
title: インクルード ファイル
description: インクルード ファイル
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 05/16/2018
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 34f2ab8f7ccafb8b30e298cd71e09171ad8c87cb
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554143"
---
各サブスクリプションの Azure Log Analytics リソースには、次の制限が適用されます。

| リソース | 既定の制限 | 説明
| --- | --- | --- |
| サブスクリプションあたりの無料のワークスペースの数 | 10 | この制限を増やすことはできません。 |
| サブスクリプションあたりの有料のワークスペースの数 | 該当なし | リソース グループ内のリソースの数とサブスクリプションあたりのリソース グループの数によって制限されます。 | 

>[!NOTE]
>2018 年 4 月 2 日の時点では、新しいサブスクリプションの新しいワークスペースでは、"*GB あたり*" の料金プランが自動的に使用されます。 4 月 2 日より前に作成された既存のサブスクリプション、または既存の Enterprise Agreement 登録に関連付けられたサブスクリプションについては、新しいワークスペースに対して 3 つの価格レベルから引き続き選択することができます。 
>

Log Analytics の各ワークスペースには次の制限が適用されます。

|  | 無料 | Standard | プレミアム | スタンドアロン | OMS | GB あたり |
| --- | --- | --- | --- | --- | --- |--- |
| 1 日に収集されるデータ量 |500 MB<sup>1</sup> |なし |なし | なし | なし | なし
| データ保持期間 |7 日 |1 か月 |12 か月 | 1 か月<sup>2</sup> | 1 か月<sup>2</sup>| 1 か月<sup>2</sup>|

<sup>1</sup> 500 MB の 1 日のデータ転送の上限に達した場合、データの分析は停止し、翌日の初めに再開されます。 1 日は UTC に基づきます。

<sup>2</sup> スタンドアロン、OMS、および GB あたりの価格プランに対するデータ保有期間は、730 日間にまで増やすことができます。

| Category | 制限 | 説明
| --- | --- | --- |
| データ コレクター API | 1 回の投稿の最大サイズは 30 MB。<br>フィールド値の最大サイズは 32 KB。 | 大量の場合は複数の投稿に分割します。<br>32 KB を超えるフィールドは切り詰められます。 |
| Search API | 非集計データの場合に返されるレコードは 5,000 件。<br>集計データの場合に返されるのは 500,000 件。 | 集計データは、`summarize` コマンドを含む検索です。
 
