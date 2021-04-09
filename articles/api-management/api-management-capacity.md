---
title: Azure API Management インスタンスの容量 | Microsoft Docs
description: この記事では、容量メトリックの概要と、Azure API Management インスタンスをスケーリングするかどうかについて情報に基づいて判断する方法について説明します。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.custom: fasttrack-edit
ms.openlocfilehash: c7c002b57f2220ac0a9fba43a8081b2a4ed800e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93081061"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Azure API Management インスタンスの容量

**容量** は、より多くの負荷に対応できるように API Management インスタンスをスケーリングするかどうかについて情報に基づいて判断する上で、最も重要な [Azure Monitor のメトリック](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis)です。 その構造は複雑であり、特定の動作が課せられます。

この記事では、**容量** の概要とその動作について説明します。 Azure portal の **容量** メトリックにアクセスする方法を説明し、API Management インスタンスのスケーリングまたはアップグレードを検討する時期を提案します。

> [!IMPORTANT]
> この記事では、Azure API Management インスタンスを監視し、その容量メトリックに基づいてスケーリングする方法について説明します。 ただし、個々の API Management インスタンスが実際にその容量に "*達した*" ときにどうなるかを理解することも重要です。 インスタンスの物理的な過負荷を防ぐためのサービスレベルのスロットリングが Azure API Management によって適用されることはありません。 インスタンスは、その物理的な容量に達したとき、過剰な負荷がかかって受信要求を処理できない Web サーバーと同じように振る舞います。待ち時間が増えたり、接続がドロップされたり、タイムアウト エラーが発生したりします。つまり、他のあらゆる外部サービスと同様、こうしたリスクに対処できるよう API クライアント側で備える必要があります (再試行ポリシーを適用するなど)。

## <a name="prerequisites"></a>前提条件

この記事の手順を実行するには、以下が必要です。

+ 有効な Azure サブスクリプション

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM インスタンス。 詳細については、[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関する記事を参照してください。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>容量の概要

![容量メトリックについて説明する図。](./media/api-management-capacity/capacity-ingredients.png)

**容量** は、API Management インスタンスの負荷のインジケーターです。 リソース使用量 (CPU、メモリ) とネットワーク キューの長さを反映します。 CPU とメモリの使用量によって、以下によるリソース使用量がわかります。

+ 要求処理など API Management のデータ プレーン サービス (要求の転送やポリシーの実行が含まれます)。
+ API Management 管理プレーン サービス (Azure portal または ARM 経由で適用された管理アクション、[開発者ポータル](api-management-howto-developer-portal.md)から来る負荷など)。
+ 選択したオペレーティング システム プロセス (新しい接続での TLS ハンドシェイクのコストを伴うプロセスが含まれます)。

合計 **容量** は、API Management インスタンスの各ユニットの値の平均です。

**容量メトリック** は API Management インスタンスの問題を明らかにするように設計されていますが、**容量メトリック** の変更に問題が反映されない場合もあります。

## <a name="capacity-metric-behavior"></a>容量メトリックの動作

実際の **容量** は、その構成があるため、多くの変数の影響を受ける可能性があります。次に例を示します。

+ 接続パターン (要求に対する新しい接続と既存の接続の再利用)
+ 要求と応答のサイズ
+ 要求を送信する各 API または複数のクライアントで構成されたポリシー。

要求に対する操作が複雑になるほど、**容量** の使用量は多くなります。 たとえば、複雑な変換ポリシーの場合、単純な要求の転送よりもはるかに多くの CPU が使用されます。 バックエンド サービスの応答が遅い場合も、使用量が多くなります。

> [!IMPORTANT]
> **容量** は、処理されている要求の数を直接測定したものではありません。

![容量メトリックのスパイク](./media/api-management-capacity/capacity-spikes.png)

**容量** は、要求が処理中でない場合でも、断続的にスパイクする場合や、ゼロより大きくなる場合があります。 これは、システム固有またはプラットフォーム固有のアクションが原因です。インスタンスをスケーリングするかどうかを判断する際には考慮しないでください。

低い **容量メトリック** が必ずしも API Management インスタンスに問題が発生していることを意味するものではありません。
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Azure portal を使用して容量を調べる
  
![容量メトリック](./media/api-management-capacity/capacity-metric.png)  

1. [Azure ポータル](https://portal.azure.com/)で、APIM インスタンスに移動します。
2. **[メトリック]** を選びます。
3. 紫色のセクションから、使用できるメトリックから **[容量]** メトリックを選択し、既定の **[平均]** 集計のままにします。

    > [!TIP]
    > 間違った解釈を回避するために、常に場所ごとに **[容量]** メトリックの内訳を調べるようにしてください。

4. 緑色のセクションから、メトリックをディメンションで分割する **[場所]** を選択します。
5. セクションの上部のバーから目的の期間を選択します。

    メトリック アラートを設定して、予想外の出来事が発生したときに通知されるようにすることができます。 たとえば、APIM インスタンスが予想ピーク容量を 20 分を超えている場合に通知を受けます。

    >[!TIP]
    > サービスの容量が少なくなったときに通知するようにアラートを設定するか、Azure Monitor の自動スケーリング機能を使用して Azure API Management ユニットを自動的に追加することができます。 スケーリング操作には約 30 分かかるので、それに従ってルールを計画してください。  
    > マスターの場所のスケーリングのみを実行できます。

## <a name="use-capacity-for-scaling-decisions"></a>スケーリングの判断に容量を使用する

**容量** は、より多くの負荷に対応できるように API Management インスタンスをスケーリングするかどうかを判断するためのメトリックです。 以下を検討してください。

+ 長期的な傾向と平均を確認する。
+ 負荷の増加に関係しない可能性が高い突発的なスパイクを無視する (説明については、「容量メトリックの動作」を参照してください)。
+ **容量** の値が長い期間 (たとえば 30 分) に 60% または 70% を超える場合に、インスタンスをアップグレードまたはスケーリングする。 サービスやシナリオによって、異なる値が適している場合があります。

>[!TIP]  
> あらかじめトラフィックを見積もることができる場合は、予想するワークロードで APIM インスタンスをテストしてください。 テナントに対する要求の負荷を徐々に増やし、ピーク負荷に対応する容量メトリックの値を監視することができます。 前のセクションの手順に従って、Azure portal を使用して常に容量の使用量を把握してください。

## <a name="next-steps"></a>次のステップ

- [API Management インスタンスのアップグレードとスケーリングを行う](upgrade-and-scale.md)
- [クラウドの支出を最適化して節約する](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)