---
title: Azure Resource Health を使用して SQL Database の正常性を監視する | Microsoft Docs
description: Azure Resource Health を使用して SQL Database の正常性を監視すると、Azure の問題がお客様のリソースに影響を及ぼしている場合に、診断するためとサポートを受けるために役立ちます。
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 02/26/2019
ms.openlocfilehash: 4757236d179e4d6ceb626f58f12cfe48799eed7a
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854363"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Resource Health を使用して、Azure SQL Database の接続をトラブルシューティングする

## <a name="overview"></a>概要

SQL Database 用の [Resource Health](../service-health/resource-health-overview.md#get-started) は、Azure の問題がお客様のリソースに影響を及ぼしている場合に、診断するためとサポートを受けるために役立ちます。 リソースの現在と過去の正常性に関する情報が表示され、問題を軽減するのに役立ちます。 Resource Health は、Azure のサービスの問題についてサポートが必要な場合にテクニカル サポートを提供します。

![概要](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>正常性のチェック

Resource Health では、リソースへのログインの成功と失敗を調べることで、SQL リソースの正常性を判断します。 現時点では、SQL DB リソース用の Resource Health では、システム エラーによるログインの失敗のみを調べ、ユーザー エラーによる失敗は調べません。 Resource Health の状態は、1 ～ 2 分ごとに更新されます。

## <a name="health-states"></a>正常性の状態

### <a name="available"></a>使用可能

**[使用可能]** という状態は、Resource Health によって、お使いの SQL リソースでシステム エラーによるログインの失敗が検出されていないことを意味します。

![使用可能](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>低下しています

**低下**という状態は、ログインの大部分が成功していること、ただし失敗もいくつか見つかっていることが、Resource Health によって検出されたことを意味します。 それらは、多くの場合、一時的なログイン エラーです。 一時的なログイン エラーによって発生する接続の問題の影響を減らすために、コード内に[再試行ロジック](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)を実装してください。

![低下しています](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>使用不可

**[使用不可]** という状態は、Resource Health によって、お使いの SQL リソースへのログインが一貫して失敗していることが検出されていることを意味します。 リソースが長期間この状態のままである場合は、サポートに問い合わせてください。

![使用不可](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Unknown

**不明**状態は、Resource Health がこのリソースに関する情報を 10 分以上受け取っていないことを示します。 この状態はリソースの状態を明確に示すものではありませんが、トラブルシューティング プロセスにおいて重要なデータ ポイントです。 リソースが想定したとおりに実行されている場合、リソースの状態は数分後に [使用可能] に変わります。 リソースで問題が発生している場合、[不明] 状態は、プラットフォーム内のイベントによってリソースが影響を受けていることを示唆している可能性があります。

![Unknown](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>履歴情報

Resource Health の [正常性の履歴] セクションで、最大 14 日間の正常性履歴にアクセスできます。 セクションには、Resource Health によって報告されたダウンタイムの発生理由 (入手可能な場合) も含まれます。 現時点では、Azure では、2 分の細分性で SQL Database リソースのダウンタイムが表示されます。 実際のダウンタイムは 1 分未満である可能性が高く、平均は 8 秒です。

### <a name="downtime-reasons"></a>ダウンタイムの理由

お使いの SQL Database でダウンタイムが発生すると、理由を判断するための分析が実行されます。 入手可能な場合、Resource Health の [正常性の履歴] セクションにダウンタイムの理由が報告されます。 ダウンタイムの理由は、通常、イベントの 30 分後に公開されます。

#### <a name="planned-maintenance"></a>Azure の計画メンテナンス

Azure インフラストラクチャでは、計画メンテナンス (データセンター内のハードウェアまたはソフトウェア コンポーネントのアップグレード) が定期的に実行されます。 データベースのメンテナンスが実行されている間、SQL によっていくつかの既存の接続が終了され、新しい接続が拒否される可能性があります。 計画メンテナンス中に発生するログイン エラーは、通常は一時的なものであり、[再試行ロジック](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)によってその影響を軽減することができます。 ログイン エラーの発生が続く場合は、サポートに問い合わせてください。

#### <a name="reconfiguration"></a>Reconfiguration

再構成は一時的な状態であると考えられ、ときどき発生することが想定されます。 これらのイベントは、負荷分散やソフトウェア/ハードウェア障害によってトリガーされる可能性があります。 クラウド データベースに接続するクライアントの運用アプリケーションでは、堅牢な接続[再試行ロジック](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)を実装する必要があります。それによって状況が緩和され、通常はエンド ユーザーにとってエラーが透過的になります。

## <a name="next-steps"></a>次の手順

- [一時的なエラーに対する再試行ロジック](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)の詳細を確認します。
- [SQL 接続エラーをトラブルシューティング、診断、および回避します。](./sql-database-connectivity-issues.md)
- [Resource Health のアラートの構成](../service-health/resource-health-alert-arm-template-guide.md)の詳細を確認します。
- [Resource Health](../service-health/resource-health-overview.md) の概要を確認します。
- [Resource Health の FAQ](../service-health/resource-health-faq.md)
