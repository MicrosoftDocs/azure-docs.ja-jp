---
title: Azure Cost Management と Billing の概要
description: Azure Cost Management と Billing の機能を使用して、請求管理タスクを実施し、コストへの請求アクセスを管理します。 また、Azure の支出を監視および管理し、Azure のリソースの使用を最適化する機能も使用します。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/03/2021
ms.topic: overview
ms.service: cost-management-billing
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: 9fe658a1755ce3731f220ec656845da1f861fa9b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050539"
---
# <a name="what-is-azure-cost-management--billing"></a>Azure Cost Management と Billing とは

Microsoft クラウドを使用することにより、ビジネス ワークロードの技術的なパフォーマンスを大幅に向上させることができます。 また、コストを削減し、組織の資産を管理するために必要なオーバーヘッドを軽減することもできます。 ただし、このビジネス チャンスはクラウドのデプロイに無駄や非効率性をもたらす可能性があるため、リスクが生じます。 Azure Cost Management と Billing は、ワークロードのコストの分析、管理、最適化に役立つ、Microsoft が提供するツールのスイートです。 このスイートを使用すると、組織がクラウドによってもたらされるベネフィットを確実に活用できます。

Azure のワークロードは、家庭の照明のように考えることができます。 一日中家を空けるとき、あなたは電気を点けたままにしますか。 毎月のエネルギー代を削減するのに役立つ、効率の良い電球を使用しませんか。 1 つの部屋に必要以上の照明がありますか。 Azure Cost Management と Billing を使用すると、組織で使用するワークロードにも同様の思考プロセスを適用できます。

Azure の製品とサービスでは、使用した分に応じて料金が発生します。 Azure リソースを作成して使用すると、そのリソースに対して課金されます。 新しいリソースのデプロイは簡単であるため、ワークロードのコストは、適切な分析や監視を行わないと急速に跳ね上がることがあります。 Azure Cost Management と Billing の機能は、次の目的で使用します。

- 請求書の支払いなどの課金管理タスクを実施する
- コストへの請求アクセスを管理する
- 月次請求書の生成に使用されたコストと使用状況データをダウンロードする
- コストにデータ分析を事前に適用する
- 支出しきい値を設定する
- 支出を最適化できるワークロードの変更の機会を特定する

組織としてコスト管理にアプローチする方法の詳細については、「[Azure Cost Management のベスト プラクティス](./costs/cost-mgt-best-practices.md)」記事を参照してください。

![Cost Management + Billing の最適化プロセスの図。](./media/cost-management-optimization-process.png)

## <a name="understand-azure-billing"></a>Azure Billing の概念

Azure Billing 機能は、請求されたコストを確認し、請求情報へのアクセスを管理するために使用します。 大規模な組織では、通常、請求タスクを実行するのは、調達チームや財務チームです。

Azure を使用するためにサインアップすると、課金アカウントが作成されます。 課金アカウントを使用して請求書と支払いを管理し、コストを追跡します。 複数の請求先アカウントにアクセスできます。 たとえば、個人的なプロジェクトのために Azure にサインアップしたとします。 そのため、課金アカウントを持つ個別の Azure サブスクリプションがある可能性があります。 この場合、組織の Enterprise Agreement または Microsoft Customer Agreement を通じてアクセスすることもできます。 シナリオごとに、個別の課金アカウントを持つことになります。

### <a name="billing-accounts"></a>課金アカウント

現在、Azure portal では、次の種類の課金アカウントがサポートされています。

- **Microsoft Online Services Program**: Azure Web サイトから Azure にサインアップすると、Microsoft Online Services Program の個別の課金アカウントが作成されます。 たとえば、[Azure 無料アカウント](./manage/create-free-services.md)または従量課金制料金のアカウントにサインアップした場合や、Visual Studio サブスクライバーとしてサインアップした場合です。

- **マイクロソフト エンタープライズ契約**:組織が Azure を使用するために Enterprise Agreement (EA) を締結すると、Enterprise Agreement の課金アカウントが作成されます。

- **Microsoft Customer Agreement**: 組織が Microsoft 担当者と連携して Microsoft Customer Agreement を締結すると、Microsoft Customer Agreement の課金アカウントが作成されます。 一部のリージョンでは、Azure Web サイトから従量課金制料金のアカウントにサインアップしたお客様や、[Azure 無料アカウント](./manage/create-free-services.md)をアップグレードしたお客様にも、Microsoft Customer Agreement の課金アカウントが作成される場合があります。

## <a name="understand-azure-cost-management"></a>Azure Cost Management の概念

課金は、コスト管理と関連してはいますが、異なるものです。 課金は、顧客に商品やサービスを請求し、取引関係を管理するプロセスです。

Cost Management は、高度な分析によって組織のコストや使用パターンを示します。 Cost Management のレポートには、Azure サービスとサード パーティの Marketplace オファリングによって消費される使用量ベースのコストが表示されます。 コストは、交渉済みの価格、予約要素、Azure ハイブリッド特典割引に基づきます。 このレポートは、使用量の内部コストおよび外部コストや、Azure Marketplace の料金をすべて表示します。 予約の購入、サポート、税金など、その他の料金はまだレポートに表示されません。 レポートは、支出やリソースの使用量を把握し、異常な支出を見つける際に役立ちます。 予測分析も使用できます。 Cost Management は、Azure 管理グループ、予算、および推奨事項を使用して、支出の編成方法やコストの削減方法を明確に示します。

Azure portal や自動エクスポート用のさまざまな API を使用して、コスト データを外部システムや外部プロセスに統合することもできます。 課金データの自動エクスポートや、スケジュール化されたレポートも利用できます。

Azure Cost Management で Azure のコストを削減する方法の概要については、Azure Cost Management の概要ビデオをご覧ください。 他の動画を視聴するには、[Cost Management の YouTube チャンネル](https://www.youtube.com/c/AzureCostManagement)にアクセスしてください。

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

### <a name="plan-and-control-expenses"></a>支出の計画と管理

Cost Management は、コスト分析、予算、推奨事項、コスト管理データのエクスポートなどをとおして、コストの計画や管理に役立てることができます。

コスト分析を使用すると、組織のコストを調査および分析できます。 組織ごとに集計コストを表示することで、コストがどこで発生しているかを把握したり、消費傾向を識別したりすることができます。 また、一定期間の累積コストを表示することで、予算に対する月単位、四半期ごと、場合によっては年単位のコスト傾向を見積もることができます。

予算は、組織の財務報告に対する責任を計画して満たすために役立ちます。 コストがしきい値や上限を超えることを防ぎたい場合にも便利です。 予算は、支出について他のユーザーに通知し、先を見越してコストを管理する際にも役立ちます。 また、支出が時間の経過と共にどのように進行したのかを確認できます。

推奨事項は、活動休止状態のリソースや十分に活用されていないリソースを特定することで効率性を最適化し、改善する方法を提示してくれます。 または、より安価なリソースのオプションを教えてくれます。 推奨事項に対処する場合は、リソースの使用方法を変更してコストを削減します。 対処する際には、最初にコストの最適化に関する推奨事項を表示して、非効率な可能性がある使用方法を確認します。 次に、推奨事項に対処して、コスト効率の良いオプションを使うように Azure リソースの使用方法を変更します。 その後、アクションを検証し、行った変更が成功したことを確認します。

コスト管理データのアクセスや確認のために外部システムを使用する場合は、データを Azure から簡単にエクスポートできます。 日次で CSV 形式でエクスポートするスケジュールを設定し、Azure ストレージにデータ ファイルを保存することもできます。 そうすると、外部システムからデータにアクセスすることができます。

### <a name="cloudyn-deprecation"></a>Cloudyn の非推奨化

Cloudyn は Cost Management に関連した Azure サービスで、2020 年末までに非推奨化されます。 既にある Cloudyn の機能は、可能な限り直接 Azure portal に統合されています。 現在、新しいユーザーはオンボードされていませんが、完全に非推奨化されるまで製品サポートは継続されます。
 
### <a name="additional-azure-tools"></a>その他の Azure ツール

Azure には、Azure Cost Management と Billing の機能セットに含まれていない、その他のツールもあります。 しかし、それらはコスト管理のプロセスにおいて重要な役割を果たしています。 これらのツールの詳細については、次のリンクを参照してください。

- [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/) - このツールを使用して初期クラウド コストを概算できます。
- [Azure Migrate](../migrate/migrate-services-overview.md) - Azure による置換ソリューションから現在のデータ センター ワークロードを評価し、必要な分析情報を取得します。
- [Azure Advisor](../advisor/advisor-overview.md) - 使用されていない VM を識別し、Azure 予約インスタンス購入に関する推奨事項を受け取ります。
- [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/) - 現在のオンプレミスの Windows Server または SQL Server のライセンスを Azure の VM 用に使用し、コストを節約します。

## <a name="next-steps"></a>次のステップ

Cost Management と Billing について理解したら、次のステップはサービスの使用開始です。

- Azure Cost Management の使用を開始し、[コストを分析](./costs/quick-acm-cost-analysis.md)します。
- [Azure Cost Management のベスト プラクティス](./costs/cost-mgt-best-practices.md)もお読みください。