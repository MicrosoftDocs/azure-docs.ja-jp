---
title: Azure Database for PostgreSQL - Single Serverでのクエリ ストアのベスト プラクティス
description: この記事では、Azure Database for PostgreSQL - Single Server でのクエリ ストア関連のベスト プラクティスについて説明します。
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: dd39b7ecd51902f5035b4cd17d59dea964d0c962
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91708834"
---
# <a name="best-practices-for-query-store"></a>クエリ ストア関連のベスト プラクティス

**適用対象:** Azure Database for PostgreSQL - 単一サーバー バージョン 9.6、10、11

この記事では、Azure Database for PostgreSQL でクエリ ストアを使用するさいのベスト プラクティスについて説明します。

## <a name="set-the-optimal-query-capture-mode"></a>最適なクエリ キャプチャ モードを設定します。
重要なデータをクエリ ストアをキャプチャさせます。 

|**pg_qs.query_capture_mode** | **シナリオ**|
|---|---|
|_すべて_  |すべてのクエリとその実行頻度や他の統計情報の観点から徹底的にワークロードを分析します。 ワークロード中の新しいクエリを特定します｡ アドホック クエリを使用してユーザーまたは自動化によるパラメーター化が特定されているかどうかを検出します。 _すべて_ はリソース消費の増加というコストを伴います｡ |
|_上位_  |上位のクエリ (クライアントによって発行されたクエリ) に注目します｡
|_なし_ |調査したいクエリ セットと時間枠をすでにキャプチャしているため､他のクエリによって生まれる邪魔なものを排除したい考えているとします｡ テストおよびベンチマーク環境では､_なし_ が適切です｡ 重要な新しいクエリを追跡､最適化する機会を逃す可能性があるため､_なし_ を使用するときは注意してください｡ 過去の時間枠のデータを回復することはできません。 |

クエリ ストアには、待機統計用のストアもあります｡ 待機統計を制御する追加のキャプチャ モード クエリがあります｡ **pgms_wait_sampling.query_capture_mode** は _なし_ または _すべて_ に設定できます｡ 

> [!NOTE] 
> **pg_qs.query_capture_mode** は **pgms_wait_sampling.query_capture_mode** に優先します。 Pg_qs.query_capture_mode が _なし_ の場合、pgms_wait_sampling.query_capture_mode の設定は何の働きもしません｡ 


## <a name="keep-the-data-you-need"></a>必要なデータを保持します。
**Pg_qs.retention_period_in_days** パラメーターには､クエリ ストアのデータ保持期間を日数で指定します。 この期間より古いクエリと統計データは削除されます。 既定では、クエリ ストアは 7 日の間データを保持するように構成されます｡ 使用する予定がない履歴データを残さないでください。 もっと長くデータを保持する必要がある場合は、値を大きくします｡


## <a name="set-the-frequency-of-wait-stats-sampling"></a>待機統計のサンプリング頻度を設定します。 
**Pgms_wait_sampling.history_period** パラメーターには､待機イベントをサンプリングする頻度 (ミリ秒単位) を指定します。 値が小さいほど、サンプリング頻度は増します。 より詳しい情報を得られますが、リソースの消費量が大きくなるというコストが伴います｡ サーバーの負荷が大きい場合、あるいはきめの細かい情報が必要ない場合は､値を大きくしてください｡


## <a name="get-quick-insights-into-query-store"></a>クエリ ストアに対するクイック インサイトを取得します。
Azure portal にある [Query Performance Insight](concepts-query-performance-insight.md) を利用して､クエリ ストア内のデータに対するクイック インサイトを取得することができます｡ 視覚化することによって､長期にわたって最も長い期間実行されているクエリと最も長い待機イベントが明らかになります｡

## <a name="next-steps"></a>次のステップ
- [Azure portal](howto-configure-server-parameters-using-portal.md) または [Azure CLI](howto-configure-server-parameters-using-cli.md) を使用してパラメーターを取得または設定する方法を学びます｡