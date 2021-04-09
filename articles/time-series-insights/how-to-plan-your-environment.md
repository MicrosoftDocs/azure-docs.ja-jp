---
title: Gen2 の環境を計画する - Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights Gen2 環境を構成、管理、計画、およびデプロイするためのベスト プラクティスです。
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: c8d96d7b5952c020493af278ee1ea8ad5ff46716
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95016787"
---
# <a name="plan-your-azure-time-series-insights-gen2-environment"></a>Azure Time Series Insights Gen2 環境の計画

この記事では、Azure Time Series Insights Gen2 を使用して、迅速に計画し、開始するためのベスト プラクティスについて説明します。

## <a name="best-practices-for-planning-and-preparation"></a>計画と準備のベスト プラクティス

環境の計画と準備に関するベストプラクティスについては、次の記事で詳しく説明しています。

* [Azure Time Series Insights Gen2 環境をプロビジョニングする](#the-gen2-environment)ときに取得するもの。
* [タイム シリーズ ID と Timestamp プロパティ](#configure-time-series-ids-and-timestamp-properties)。
* [新しいタイム シリーズ モデルと独自のモデルの構築方法](#understand-the-time-series-model)。
* [JSON で効率的にイベントを送信する](#shape-your-events)方法。
* Azure Time Series Insights の[ビジネス ディザスター リカバリーのオプション](#business-disaster-recovery)。

Azure Time Series Insights では、従量課金制のビジネス モデルが採用されています。 料金と容量の詳細については、「[Azure Time Series Insights の価格](https://azure.microsoft.com/pricing/details/time-series-insights/)」を参照してください。

## <a name="the-gen2-environment"></a>Gen2 環境

Azure Time Series Insights Gen2 環境をプロビジョニングするときは、次の 2 つの Azure リソースを作成します。

* Azure Time Series Insights Gen2 環境
* Azure ストレージ アカウント

プロビジョニング プロセスの一環として、ウォーム ストアを有効にするかどうかを指定します。 ウォーム ストアは、階層化クエリ エクスペリエンスを提供します。 有効にする場合は、7 日から 30 日の保持期間を指定する必要があります。 ウォーム ストアの保持期間内に実行されるクエリは、一般に応答時間が短縮されます。 クエリがウォーム ストアの保持期間を超える場合は、コールド ストアから提供されます。

ウォーム ストアのクエリは無料ですが、コールド ストアのクエリにはコストが発生します。 クエリ パターンを理解し、それに従ってウォーム ストア構成を計画することが重要です。 最新データに対する対話型分析はウォーム ストアに配置し、パターン分析と長期的傾向はコールド ストアに配置することをお勧めします。

> [!NOTE]
> ウォーム データに対してクエリを実行する方法の詳細については、[API リファレンス](/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters)に関するページを参照してください。

始めに、3 つの追加項目が必要です。

* [タイム シリーズ モデル](./concepts-model-overview.md)
* [Time Series Insights に接続されているイベント ソース](./concepts-streaming-ingestion-event-sources.md)
* どちらもモデルにマップされ、有効な JSON 形式である[イベント ソースに送られるイベント](./time-series-insights-send-events.md)

## <a name="review-azure-time-series-insights-gen2-limits"></a>Azure Time Series Insights Gen2 の制限を確認する

[!INCLUDE [Review Azure Time Series Insights Gen2 limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>タイム シリーズ ID と Timestamp プロパティの構成

新しい Azure Time Series Insights 環境を作成するには、時系列 ID を選択します。 そうすることで、データの論理パーティションとして機能します。 前述のように、タイム シリーズ ID の準備ができていることを確認します。

> [!IMPORTANT]
> タイム シリーズ ID *は、後で変更できません*。 最終的な選択と初めて使用する前に、それぞれを確認してください。

リソースを一意に区別するために、最大 3 つのキーを選択できます。 詳細については、「[タイム シリーズ ID の選択に関するベスト プラクティス](./how-to-select-tsid.md)」および「[取り込み規則](concepts-json-flattening-escaping-rules.md)」を参照してください。

**Timestamp** プロパティも重要です。 イベント ソースを追加する際に、このプロパティを指定できます。 各イベント ソースには、時間の経過と共にイベント ソースを追跡するために使用されるオプションの Timestamp プロパティがあります。 Timestamp の値は大文字と小文字が区別され、各イベント ソースの個々の仕様に書式設定される必要があります。

空白のままにすると、イベントが IoT ハブまたはイベント ハブにエンキューされた時刻が、イベントのタイムスタンプとして使用されます。 通常、ユーザーは、タイムスタンプ プロパティをカスタマイズし、ハブ エンキュー時刻ではなく、センサーまたはタグによって読み取りが生成された時刻を使用する必要があります。 詳細およびタイム ゾーン オフセットの読み取りについては、「[イベント ソースのタイムスタンプ](./concepts-streaming-ingestion-event-sources.md#event-source-timestamp)」を参照してください。

## <a name="understand-the-time-series-model"></a>タイム シリーズ モデルについて

Azure Time Series Insights 環境の時系列モデルを構成できるようになりました。 新しいモデルにより、IoT データの検索と分析が簡単になります。 時系列データのキュレーション、メンテナンス、およびエンリッチメントが可能になり、コンシューマー対応データ セットの準備に役立ちます。 モデルでは、タイム シリーズ ID が使われ、これは、型と呼ばれる変数と階層に一意のリソースを関連付けるインスタンスにマップされます。 詳細については、[時系列モデル](./concepts-model-overview.md)の概要に関する記事を参照してください。

モデルは動的であるため、いつでも構築できます。 すばやく開始するには、データを Azure Time Series Insights にプッシュする前に、モデルを構築してアップロードします。 モデルを構築するには、[タイム シリーズ モデルの使用](./concepts-model-overview.md)に関するページを参照してください。

多くのお客様で、タイム シリーズ モデルは既存の資産モデルや既に設置されている ERP システムにマップされます。 既存のモデルがない場合は、迅速に開始できるように、事前構築済みのユーザー エクスペリエンスが[提供されて](https://github.com/Microsoft/tsiclient)います。 モデルがどのように役立つかを考えるため、[サンプル デモ環境](https://insights.timeseries.azure.com/preview/demo)を参照してください。

## <a name="shape-your-events"></a>イベントの調整

Azure Time Series Insights にイベントを送信する方法を確認できます。 理想的には、イベントは適切かつ効率的に非正規化されます。

お勧めの方法:

* タイム シリーズ モデルにメタデータを格納します。
* タイム シリーズ モード、インスタンス フィールド、およびイベントに、必要な情報 (タイム シリーズ ID や Timestamp プロパティなど) のみが含まれるようにします。

詳細およびイベントがフラット化および格納される方法については、「[JSON のフラット化とエスケープの規則](./concepts-json-flattening-escaping-rules.md)」を参照してください。

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>次のステップ

* [Azure Advisor](../advisor/advisor-overview.md) を確認して、ビジネス復旧の構成オプションを計画します。
* [Azure Advisor](../advisor/advisor-overview.md) を確認して、ビジネス復旧の構成オプションを計画します。
* Azure Time Series Insights Gen2 での[データ インジェスト](./concepts-ingestion-overview.md)についてさらに確認します。
* Azure Time Series Insights Gen2 での[データ ストレージ](./concepts-storage.md)についての記事を確認します。
* Azure Time Series Insights Gen2 での[データ モデリング](./concepts-model-overview.md)について確認します。