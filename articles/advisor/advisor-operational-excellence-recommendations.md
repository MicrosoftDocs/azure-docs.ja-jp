---
title: Advisor を使用してオペレーショナル エクセレンスを向上させる
description: Azure Advisor を使用して、Azure サブスクリプションのオペレーショナル エクセレンスを最適化し、成熟させます。
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 0b938a0c7a42182bb8d2a50b48d65a0844d952a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579957"
---
# <a name="achieve-operational-excellence-by-using-azure-advisor"></a>Azure Advisor を使用してオペレーショナル エクセレンスを実現する

Azure Advisor でのオペレーショナル エクセレンスの推奨事項は、次の点で役立ちます。 
- プロセスとワークフローの効率。
- リソースの管理容易性。
- デプロイのベスト プラクティス。 

これらの推奨事項については、Advisor ダッシュボードの **[オペレーショナル エクセレンス]** タブを見るとわかります。

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Azure の問題による影響を受けるときに通知される Azure Service Health アラートを作成する

Azure サービスの問題による影響を受けるときに通知されるよう、Azure Service Health アラートを設定することをお勧めします。 [Azure Service Health](https://azure.microsoft.com/features/service-health/) は、Azure サービスの問題による影響を受ける場合に、カスタマイズされたガイダンスとサポートを提供する無料サービスです。 Advisor はアラートが構成されていないサブスクリプションを識別し、構成するように推奨します。


## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>サブスクリプションの上限に到達しないようにストレージ アカウントを設計する

Azure リージョンでは、サブスクリプションごとに最大 250 個のストレージ アカウントをサポートできます。 この制限に達したら、そのリージョンとサブスクリプションの組み合わせでストレージ アカウントを作成することができなくなります。 Advisor では、サブスクリプションが確認されて、リージョンとサブスクリプションが上限に近づいている場合はストレージ アカウント数を減らして設計するように勧告されます。

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>必要な時に Azure クラウドの専門家を利用できるようにする

ビジネスに不可欠なワークロードを実行しているとき、必要時にはテクニカル サポートを受けることができるようにしておくことが重要です。 Advisor は、サポート プランにサポート チームが含まれていない Bus Critical なサブスクリプションを特定します。 サポート チームを含むオプションにアップグレードすることが推奨されます。

## <a name="delete-and-re-create-your-pool-to-remove-a-deprecated-internal-component"></a>プールを削除して再作成し、非推奨の内部コンポーネントを削除する

プールで非推奨の内部コンポーネントが使用されている場合は、安定性とパフォーマンスを向上させるためにプールを削除して再作成します。

## <a name="repair-invalid-log-alert-rules"></a>無効なログ アラート ルールを修復する

Azure Advisor は、条件セクションに無効なクエリが指定された警告ルールを検出します。 Azure Monitor でログ警告ルールを作成し、それらを使用して、指定された間隔で分析クエリを実行できます。 クエリの結果によって、アラートをトリガーする必要があるかどうかが決定されます。 分析クエリは、参照されるリソース、テーブル、またはコマンドの変更のために、時間の経過と共に無効になることがあります。 Advisor は、警告ルールが自動的に無効になることを防止し、Azure でのリソースの監視範囲を保証するために、警告ルール内のクエリを修正することを推奨します。 [アラート ルールのトラブルシューティング](../azure-monitor/alerts/alerts-troubleshoot-log.md)の詳細を確認してください。

## <a name="use-azure-policy-recommendations"></a>Azure Policy の推奨事項を使用する

Azure Policy は、ポリシーの作成、割り当て、管理に使用できる Azure のサービスです。 これらのポリシーでは、リソースに対してルールが適用され、それらが影響を受けます。 次の Azure Policy の推奨事項は、オペレーショナル エクセレンスの実現に役立ちます。 

**タグを管理する。** このポリシーは、任意のリソースが作成または更新されたときに、指定されたタグと値を追加または置換します。 修復タスクをトリガーすることで、既存のリソースを修復できます。 このポリシーによってリソース グループのタグが変更されることはありません。

**geo コンプライアンス要件を強制する。** このポリシーでは、リソースをデプロイするときに組織が指定できる場所を制限できます。 

**デプロイに対して許可される仮想マシンの SKU を指定する。** このポリシーを使用して、組織でデプロイできる仮想マシン SKU のセットを指定できます。

"***Managed Disks を使用していない VM の監査*" を強制する。**

**"*リソース グループからのタグの継承*" を有効にする。** このポリシーは、任意のリソースが作成または更新されるときに、親リソース グループのタグと値を追加または置換します。 修復タスクをトリガーすることで、既存のリソースを修復できます。

Advisor は、お客様がベスト プラクティスを採用してオペレーショナル エクセレンスを実現するのに役立つ、いくつかの個々の Azure ポリシーを推奨しています。 推奨されたポリシーをお客様が割り当てる場合、推奨は行われません。 お客様が後でポリシーを削除する場合、Advisor は、削除を次を示す強力なシグナルであると解釈するため、推奨は行われないままとなります。

1.  お客様は、Advisor の推奨にもかかわらず、お客様固有のユース ケースには適用されないという理由で、ポリシーを削除しました。 
2.  お客様は、ポリシーの割り当てと削除を行った後に、ポリシーを認識して理解します。後でそれがユース ケースに関連するようになった場合は、ガイダンスを利用することなく、必要に応じて、再度割り当てるか削除することができます。 お客様は、同じポリシーを再度割り当てることに大きな関心を持った場合は、Advisor の推奨を必要とすることなく、Azure Policy で割り当てを行うことができます。 このロジックは、オペレーショナル エクセレンス カテゴリのポリシー推奨事項に特に適用されるものであることにご注意ください。 これらのルールは、セキュリティに関する推奨事項には適用されません。  


## <a name="no-validation-environment-enabled"></a>検証環境が有効になっていません
現在のサブスクリプションで検証環境が有効になっていないことが、Azure Advisor で確認されました。 お使いのホスト プールの作成時に、プロパティ タブの \"[Validation environment]\"\(検証環境\) で \"[いいえ]\" が選択されています。検証環境が有効になっているホスト プールが少なくとも 1 つあれば、潜在的な問題が早期に検出される Windows Virtual Desktop サービスのデプロイを通じて、ビジネス継続性が確保されます。 [詳細情報](../virtual-desktop/create-validation-host-pool.md)

## <a name="ensure-production-non-validation-environment-to-benefit-from-stable-functionality"></a>確実に、運用 (非検証) 環境で安定した機能を利用できるようにする
お使いのホスト プールで検証環境が有効になっている数が多すぎることが Azure Advisor で確認されました。 検証環境をその目的に最適化するには、お使いのホスト プールの少なくとも 1 つには検証環境を設定しますが、半分より多くには設定しないようにする必要があります。 使用しているホスト プールで検証環境の有効または無効のバランスを適正にすると、特定の更新プログラムで提供される Windows Virtual Desktop のマルチステージ デプロイの利点を最適に利用できるようになります。 この問題を解決するには、お使いのホスト プールのプロパティを開き、\"[Validation Environment]\"\(検証環境\) の設定の横にある \"[いいえ]\" を選択します。

## <a name="enable-traffic-analytics-to-view-insights-into-traffic-patterns-across-azure-resources"></a>Traffic Analytics を有効にして、Azure リソース間のトラフィック パターンに関する分析情報を表示する
Traffic Analytics は、Azure でのユーザーとアプリケーションのアクティビティを可視化するクラウドベースのソリューションです。 Traffic Analytics により、Network Watcher ネットワーク セキュリティ グループ (NSG) フロー ログが分析され、トラフィック フローに関する分析情報が提供されます。 Traffic Analytics を使用すると、Azure と Azure 以外のデプロイ全体のトップ トーカーを表示し、環境内の開いているポート、プロトコル、および悪意のあるフローを調査し、ネットワークのデプロイを最適化してパフォーマンスを向上させることができます。 10 分と 60 分の処理間隔でフロー ログを処理できるため、トラフィックの分析を高速化できます。 お使いの Azure リソースに対して Traffic Analytics を有効にすることをお勧めします。 


## <a name="next-steps"></a>次のステップ

Advisor の推奨事項の詳細については、以下を参照してください。
* [Advisor 入門](advisor-overview.md)
* [開始するには](advisor-get-started.md)
* [Advisor のコストに関する推奨事項](advisor-cost-recommendations.md)
* [Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)
* [Advisor の信頼性に関する推奨事項](advisor-high-availability-recommendations.md)
* [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
