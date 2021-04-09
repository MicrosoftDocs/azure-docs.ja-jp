---
title: お使いの環境でのデータ保有について - Azure Time Series Insight | Microsoft Docs
description: この記事では、Azure Time Series Insights 環境のデータ保有を制御する 2 つの設定について説明します。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 4f236679d0662df852581a6a8408ed6bc0d4e3fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91535691"
---
# <a name="understand-data-retention-in-azure-time-series-insights-gen1"></a>Azure Time Series Insights Gen1 のデータ保有の概要

> [!CAUTION]
> これは Gen1 の記事です。

この記事では、Azure Time Series Insights 環境のデータ保有に影響する 2 つの主要な設定について説明します。

## <a name="video"></a>ビデオ

### <a name="the-following-video-summarizes-azure-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>次の動画は、Azure Time Series Insights のデータ保有と、そのための計画方法をまとめたものです。</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

お使いの Azure Time Series Insights 環境にはそれぞれ、**データ保有期間** を制御する設定があります。 この値は 1 から 400 日間となっています。 環境の記憶域容量に達したとき、またはリテンション期間が終了したときのいずれか早い方でデータが削除されます。

また、Azure Time Series Insights 環境には、 **[ストレージ制限を超過したときの動作]** という設定があります。 この設定によって、環境の最大容量に到達したときのイングレスと消去の動作が制御されます。 この設定には、次の 2 つの動作の選択肢があります。

- **[Purge old data]\(古いデータを消去\)** (既定値)  
- **[Pause ingress]\(イングレスを一時停止\)**

> [!NOTE]
> 既定では、新しい環境を作成するとき、**古いデータを消去する** ようにリテンション期間が設定されます。 Azure portal で、必要に応じて、作成後にこの設定を切り替えることができます。Azure Time Series Insights 環境の **[構成]** ページを利用してください。
>
> - リテンション期間ポリシーを構成する方法については、「[Azure Time Series Insights のリテンション期間の構成](time-series-insights-how-to-configure-retention.md)」を参照してください。

両方のデータ保有ポリシーの詳細について、以下で詳しく説明します。

## <a name="purge-old-data"></a>古いデータを消去

- **[古いデータを消去]** は、Azure Time Series Insights 環境の既定の設定です。  
- ユーザーが使用する Azure Time Series Insights 環境に "*最も新しいデータ*" が常に表示されるようにする場合は、 **[古いデータを消去]** を選択します。
- **[古いデータを消去]** 設定では、環境の上限に到達したとき (保有期間、サイズ、カウントのいずれかが上限に達した時点で)、データが *消去* されます。 リテンション期間は既定で 30 日間に設定されます。
- 取り込まれた日が最も古いデータが先に消去されます ("先入れ先出し" 手法)。

### <a name="example-one"></a>例 1

**イングレスを続行し、古いデータを消去する** という保有動作を設定した環境を例として取り上げます。

**データ保有期間** を 400 日間に設定しています。 **容量** は S1 ユニットに設定されています。合計容量は 30 GB になります。 それでは、一日平均 500 MB まで受信データが蓄積されると想定しましょう。 この環境では、データの受信率を考慮すると、保有できるデータは 60 日分に限られます。60 日で最大容量に到達するためです。 受信データは一日あたり 500 MB x 60 日間 = 30 GB で累積されます。

61 日目に環境が最新のデータを表示しますが、60 日間を経過している古いデータは消去されます。 消去によって新しいデータを入れるための空き領域ができます。引き続き、新しいデータを調査できます。 データをより長く保存するには、ユニットを追加して環境のサイズを増やすか、プッシュするデータを減らします。  

### <a name="example-two"></a>例 2

次も、**イングレスを続行し、古いデータを消去する** という保有動作が設定された環境です。 この例では、**データ保有期間** に 180 日間という低い値を設定しています。 **容量** は S1 ユニットに設定されています。合計容量は 30 GB になります。 180 日間データを保存するには、一日あたりのイングレスを 0.166 GB (166 MB) 以下に抑える必要があります。  

この環境の一日あたりのイングレス量が 0.166 GB を超過すると、一部のデータが消去されるため、データを 180 日間保存できなくなります。 忙しい期間中、この同じ環境はどうなるでしょうか。 環境のイングレス量が一日あたり平均 0.189 GB に増えると想定します。 この忙しい概算時間中は、158 日分のデータが保持されます (30 GB/0.189 = 158.73 日分のリテンション期間)。 この期間は、目的のデータ保有期間より短くなります。

## <a name="pause-ingress"></a>イングレスを一時停止

- **[イングレスを一時停止]** 設定では、保有期間より前にサイズやカウントの上限に到達してもデータが消去されません。  
- **[イングレスを一時停止]** では、リテンション期間到達に起因してデータが消去される前に環境の容量を増やす時間がユーザーに与えられます。
- これはデータ損失防止に役立ちますが、イングレスの一時停止がイベント ソースのリテンション期間より長引いた場合、最も新しいデータが失われる可能性があります。
- ただし、環境の最大容量に到達すると、次のような追加のアクションが行われるまでデータ イングレスが一時停止となります。

  - 「[Azure Time Series Insights 環境をスケーリングする方法](time-series-insights-how-to-scale-your-environment.md)」に説明されているように環境の最大容量を増やしてスケール ユニットを追加する。
  - データ保有期間に到達し、データが消去され、環境が最大容量より下に引き下げられる。

### <a name="example-three"></a>例 3

保有動作を **イングレスを一時停止する** ように構成した環境について考察します。 この例では、**データ保有期間** が 60 日間に設定されています。 **容量** は 3 ユニットの S1 に設定されています。 この環境のイングレスは一日あたり 2 GB であると想定します。 この環境では、最大容量に到達したとき、イングレスが一時停止します。

一時停止すると、イングレスが再開するか、**イングレスの続行** が有効になる (古いデータが消去され、新しいデータのための空き領域が作られる) まで環境により同じデータセットが表示されます。

イングレス再開時の動作:

- イベント ソースが受信した順序でデータが流れる。
- イベント ソースのリテンション期間ポリシーを超過した場合を除き、タイムスタンプに基づいてイベントに索引が付けられる。 イベント ソースのリテンション期間の構成については、「[Event Hubs のよく寄せられる質問](../event-hubs/event-hubs-faq.md)」を参照してください。

> [!IMPORTANT]
> イングレスの一時停止を回避するために、通知を出すアラートを設定してください。 Azure イベント ソースの場合、既定のリテンション期間は 1 日のため、データが失われることがあります。 イングレスが一時停止すると、追加の措置を取らない限り、最も新しいデータが失われる可能性があります。 データ損失を回避するには、容量を増やすか、**古いデータを消去する** 動作に切り替える必要があります。

関連する Event Hubs で **メッセージのリテンション期間** プロパティを調整することで、Azure Time Series Insights でイングレスの一時停止が発生したときのデータ損失を最小限に抑えることができます。

[![イベント ハブのメッセージのリテンション期間。](media/time-series-insights-concepts-retention/event-hub-retention.png)](media/time-series-insights-concepts-retention/event-hub-retention.png#lightbox)

Azure Time Series Insights では、イベント ソースでプロパティが構成されていない場合 (`timeStampPropertyName`)、イベント ハブに到着したときのタイムスタンプに X 軸が設定されます。 `timeStampPropertyName` が何か別のものに設定されている場合、イベントの解析時に、データ パケットで構成済みの `timeStampPropertyName` が環境により検索されます。

環境を拡張して、容量を増やすか、リテンション期間を延ばす必要がある場合は、「[Azure Time Series Insights 環境をスケーリングする方法](time-series-insights-how-to-scale-your-environment.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- データ保有の設定を構成または変更する方法については、[Azure Time Series Insights のリテンション期間の構成](time-series-insights-how-to-configure-retention.md)に関するページを参照してください。

- [Azure Time Series Insights 内での待機時間の軽減](time-series-insights-environment-mitigate-latency.md)について確認します。
