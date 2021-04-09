---
title: コマーシャル マーケットプレースの分析データにアクセスするための API
description: これらの API を使用して、パートナー センターの分析データにプログラムからアクセスします。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: ed6d658155267ab21fd4cdd28dd50fcbb258ee90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583649"
---
# <a name="apis-for-accessing-commercial-marketplace-analytics-data"></a>コマーシャル マーケットプレースの分析データにアクセスするための API

次に、コマーシャル マーケットプレースの分析データとそれに関連付けられている機能にアクセスするための API の一覧を示します。

- [データセット プル API](#dataset-pull-apis)
- [クエリ管理 API](#query-management-apis)
- [レポート管理 API](#report-management-apis)
- [レポート実行プル API](#report-execution-pull-apis)

## <a name="dataset-pull-apis"></a>データセット プル API

***表 1: データセット プル API***

| **API** | **機能** |
| --- | --- |
| [すべてのデータセットを取得する](analytics-api-get-all-datasets.md) | 使用可能なすべてのデータセットを取得します。 データセットには、テーブル、列、メトリック、および時間範囲が一覧表示されます。 |
|||

## <a name="query-management-apis"></a>クエリ管理 API

***表 2: クエリ管理 API***

| **API** | **機能** |
| --- | --- |
| [レポート クエリの作成](analytics-programmatic-access.md#create-report-query-api) | 列およびメトリックのエクスポート元となるデータセットを定義するカスタム クエリを作成します。 |
| [レポート クエリの取得](analytics-api-get-report-queries.md) | レポートで使用できるすべてのクエリを取得します。 既定では、すべてのシステムおよびユーザー定義クエリを取得します。 |
| [レポート クエリの削除](analytics-api-delete-report-queries.md) | ユーザー定義クエリを削除します。 |
|||

## <a name="report-management-apis"></a>レポート管理 API

***表 3: レポート管理 API***

| **API** | **機能** |
| --- | --- |
| [レポートの作成](analytics-programmatic-access.md#create-report-api) | 一定の間隔で実行されるクエリをスケジュールします。 |
| [レポート クエリの試行](analytics-api-try-report-queries.md) | レポート クエリ ステートメントを実行します。 データが想定どおりであるかどうかを確認するためにパートナーが使用できるレコードを 10 件だけ返します。 |
| [レポートの取得](analytics-api-get-report.md) | スケジュールされているすべてのレポートを取得します。 |
| [レポートの更新](analytics-api-update-report.md) | レポート パラメーターを変更します。 |
| [レポートの削除](analytics-api-delete-report.md) | すべてのレポートおよびレポート実行レコードを削除します。 |
| [レポート実行の一時停止](analytics-api-pause-report-executions.md) | レポートのスケジュール実行を一時停止します。 |
| [レポート実行の再開](analytics-api-resume-report-executions.md) | 一時停止されたレポートのスケジュール実行を再開します。 |
|||

## <a name="report-execution-pull-apis"></a>レポート実行プル API

***表 4: レポート実行プル API***

| **API** | **機能** |
| --- | --- |
| [レポート実行の取得](analytics-programmatic-access.md#get-report-executions-api) | 特定のレポートで発生したすべての実行を取得します。 |
|||

## <a name="next-steps"></a>次のステップ

- [Swagger API URL](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html)を使用して API を試すことができます。
