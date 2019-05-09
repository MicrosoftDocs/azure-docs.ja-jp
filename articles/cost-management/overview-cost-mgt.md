---
title: Azure Cost Management の概要 | Microsoft Docs
description: Azure Cost Management とは、Azure の支出を監視および管理してリソース使用量を最適化するのに役立つコスト管理ソリューションです。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/14/2019
ms.topic: overview
ms.service: cost-management
manager: benshy
ms.custom: ''
ms.openlocfilehash: 69f91949347eadcffb3c0d3ff833a40b5e483e24
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58003180"
---
# <a name="what-is-azure-cost-management"></a>Azure Cost Management とは

コスト管理とは、ビジネスに関連するコストを効率的に計画して制御するプロセスです。 通常、コストの管理タスクは、財務、管理、およびアプリのチームが実行します。 Azure Cost Management は、企業がコストを考慮して計画を立てるときに役立ちます。 また、コストを効果的に分析して、クラウド支出を最適化する措置を取ることもできます。 組織としてコスト管理にアプローチする方法の詳細については、「[Azure Cost Management のベスト プラクティス](cost-mgt-best-practices.md)」記事を参照してください。

課金は、コスト管理と関連してはいますが、異なるものです。 課金は、顧客に商品やサービスを請求し、取引関係を管理するプロセスです。  通常、課金タスクを実行するのは、調達チームや財務チームです。

Cost Management は、高度な分析によって組織のコストや使用パターンを示します。 Cost Management のレポートには、Azure サービスとサード パーティの Marketplace オファリングによって消費される使用量ベースのコストが表示されます。 コストは、交渉済みの価格、予約要素、Azure ハイブリッド特典割引に基づきます。 このレポートは、使用量の内部コストおよび外部コストや、Azure Marketplace の料金をすべて表示します。 予約の購入、サポート、税金など、その他の料金はまだレポートに表示されません。 レポートは、支出やリソースの使用量を把握し、異常な支出を見つける際に役立ちます。 予測分析も使用できます。 Cost Management は、Azure 管理グループ、予算、および推奨事項を使用して、支出の編成方法やコストの削減方法を明確に示します。

Azure portal や自動エクスポート用のさまざまな API を使用して、コスト データを外部システムや外部プロセスに統合することもできます。 課金データの自動エクスポートや、スケジュール化されたレポートも利用できます。

## <a name="plan-and-control-expenses"></a>支出の計画と管理

Cost Management は、コスト分析、予算、推奨事項、コスト管理データのエクスポートなどを通して、コストの計画や管理に役立てることができます。

コスト分析を使用すると、組織のコストを調査および分析できます。 組織ごとに集計コストを表示することで、コストがどこで発生しているかを把握したり、消費傾向を識別したりすることができます。 また、一定期間の累積コストを表示することで、予算に対する月単位、四半期ごと、場合によっては年単位のコスト傾向を見積もることができます。

予算は、組織の財務報告に対する責任を計画して満たすために役立ちます。 コストがしきい値や上限を超えることを防ぎたい場合にも便利です。 予算は、支出について他のユーザーに通知し、先を見越してコストを管理する際にも役立ちます。 また、支出が時間の経過と共にどのように進行したのかを確認できます。

推奨事項は、活動休止状態のリソースや十分に活用されていないリソースを特定することで効率性を最適化し、改善する方法を提示してくれます。 または、より安価なリソースのオプションを教えてくれます。 推奨事項に対処する場合は、リソースの使用方法を変更してコストを削減します。 対処する際には、最初にコストの最適化に関する推奨事項を表示して、非効率な可能性がある使用方法を確認します。 次に、推奨事項に対処して、コスト効率の良いオプションを使うように Azure リソースの使用方法を変更します。 その後、アクションを検証し、行った変更が成功したことを確認します。

コスト管理データのアクセスや確認のために外部システムを使用する場合は、データを Azure から簡単にエクスポートできます。 日次で CSV 形式でエクスポートするスケジュールを設定し、Azure ストレージにデータ ファイルを保存することもできます。 そうすると、外部システムからデータにアクセスすることができます。

## <a name="consider-cloudyn"></a>Cloudyn の検討

[Cloudyn](overview.md) は、Cost Management に関連する Azure サービスです。 Cloudyn を使うと、クラウドの使用状況や Azure リソースの支出を追跡できます。 AWS や Google などの他のクラウド プロバイダーもサポートしています。 わかりやすいダッシュボードのレポートは、コストの割り当てとショーバック/チャージバックに役立ちます。 現在の Cost Management は、ショーバック/チャージ バックや他のクラウド サービス プロバイダーをサポートしていません。 しかし、Cloudyn はそれらをサポート _している_ 選択肢の 1 つです。 現時点では、Microsoft クラウド サービス プロバイダー (CSP) アカウントは Cost Management ではサポートされていませんが、Cloudyn ではサポートされています。 CSP アカウントがある場合、またはショーバック/チャージバックを使用する場合は、Cloudyn を使用してコストを管理できます。

## <a name="additional-azure-tools"></a>その他の Azure ツール

Azure には、Azure Cost Management 機能セットに含まれていないその他のツールもあります。 しかし、それらはコスト管理のプロセスにおいて重要な役割を果たしています。 これらのツールの詳細については、次のリンクを参照してください。

- [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/) - このツールを使用して初期クラウド コストを概算できます。
- [Azure Migrate](../migrate/migrate-overview.md) - Azure による置換ソリューションから現在のデータ センター ワークロードを評価し、必要な分析情報を取得します。
- [Azure Advisor](../advisor/advisor-overview.md) - 使用されていない VM を識別し、Azure 予約インスタンス購入に関する推奨事項を受け取ります。
- [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/) - 現在のオンプレミスの Windows Server または SQL Server のライセンスを Azure の VM 用に使用し、コストを節約します。


## <a name="next-steps"></a>次の手順

Cost Management について理解したら、次の手順はサービスの使用開始です。

- Azure Cost Management の使用を開始し、[コストを分析](quick-acm-cost-analysis.md)します。
- [Azure Cost Management のベスト プラクティス](cost-mgt-best-practices.md)もお読みください。
