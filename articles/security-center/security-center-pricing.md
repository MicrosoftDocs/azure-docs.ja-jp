---
title: Azure Security Center (無料) と有効化された Azure Defender
description: Azure Security Center でクラウド ワークロード保護のために Azure Defender を有効にする利点について説明します
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/08/2021
ms.openlocfilehash: d45dae8b0b3725555bd83a05032339671a9595be
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454366"
---
# <a name="azure-security-center-free-vs-azure-defender-enabled"></a>Azure Security Center (無料) と有効化された Azure Defender
Azure Defender は、最初の 30 日間は無料で利用できます。 30 日経過した時点で、サービスの利用を継続することを選択した場合、使用量に応じた課金が自動的に開始されます。

「[クイックスタート: Azure Defender を有効にする](enable-azure-defender.md)」で説明されているように、**価格と設定** に関するページからアップグレードすることができます。 選択した通貨でのお住まいのリージョンに応じた価格の詳細については、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」を参照してください。

## <a name="what-are-the-benefits-of-enabling-azure-defender"></a>Azure Defender を有効にする利点

Security Center は、次の 2 つのモードで提供されます。

- **Azure Defender 無効** (Free) - Azure Defender を使用しない Security Center は、Azure portal 内の Azure Security Center ダッシュボードに初めてアクセスしたとき、または API を介してプログラムで有効にした場合に、すべての Azure サブスクリプションで無料で有効になります。 この無料モードを使用すると、セキュリティ ポリシー、継続的なセキュリティ評価、Azure リソースの保護に役立つ実践的なセキュリティの推奨事項が提供されます。

- **Azure Defender 有効** - 無料モードの機能を、プライベートおよび他のパブリック クラウドで実行されているワークロードまで拡張し、ハイブリッド クラウド ワークロード全体に統合されたセキュリティ管理と脅威防止機能を提供します。 Azure Defender の主な機能の一部を次に示します。

    - **Microsoft Defender for Endpoint** - Azure Defender for servers には、包括的なエンドポイントの検出と応答 (EDR) のために、[Microsoft Defender for Endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender) が含まれています。 Microsoft Defender for Endpoint を Azure Defender と共に使用する利点の詳細については、[Security Center の統合された EDR ソリューションの使用](security-center-wdatp.md)に関するページを参照してください。
    - **仮想マシンとコンテナー レジストリの脆弱性スキャン** - 業界で最も高度な脆弱性管理ソリューションを提供するスキャナーを、すべての仮想マシンに簡単にデプロイできます。 結果の表示、調査、修復を Security Center 内で直接実行できます。 
    - **ハイブリッド セキュリティ** - オンプレミスとクラウドのすべてのワークロードのセキュリティを、統合された 1 つのビューで確認できます。 セキュリティ ポリシーを適用し、ハイブリッド クラウドのワークロードのセキュリティを継続的に評価することで、セキュリティ標準に確実に準拠できます。 ファイアウォールやその他のパートナー ソリューションなどのさまざまなソースから、セキュリティ データを収集、検索、分析します。
    - **脅威防止アラート** - 高度な行動分析と Microsoft インテリジェント セキュリティ グラフを使用して、巧妙化するサイバー攻撃に対応します。 組み込みの行動分析と機械学習により、各種攻撃やゼロデイ攻撃を特定することができます。 ネットワーク、マシン、クラウド サービスに対する攻撃や侵害後のアクティビティを監視します。 対話型のツールと状況に応じた脅威インテリジェンスにより、調査を効率化します。
    - **アクセスとアプリケーションの制御** (AAC) - 特定のワークロードに適応する、機械学習を活用した推奨事項を適用して、許可および拒否リストを作成することで、マルウェアや他の望ましくないアプリケーションをブロックします。 Azure VM の管理ポートに対するジャスト イン タイムの制御されたアクセスで、ネットワーク攻撃対象領域を減らします。 ブルート フォースなどのネットワーク攻撃に対する露出が、AAC によって劇的に減少します。
    - **コンテナーのセキュリティ機能** - コンテナー化された環境で、脆弱性管理とリアルタイムの脅威の防止が利用できます。 **コンテナー レジストリ用 Azure Defender** を有効にした場合、すべての機能が有効になるまで最大 12 時間かかることがあります。 料金は、接続されたレジストリにプッシュされた一意のコンテナー イメージの数に基づいています。 イメージのスキャンが 1 回完了すると、変更されてもう 1 度プッシュされない限り、再度課金されることはありません。
    - **Azure 環境に接続されているリソースに対する広範な脅威の防止** - Azure Defender には、すべてのリソースに共通の Azure サービス (Azure Resource Manager、Azure DNS、Azure ネットワーク レイヤー、Azure Key Vault) に対する Azure ネイティブの広範な脅威の防止機能が含まれています。 Azure Defender は、Azure 管理レイヤーと Azure DNS レイヤーの独自の可視性を備えているため、これらのレイヤーに接続されているクラウド リソースを保護できます。


## <a name="faq---pricing-and-billing"></a>FAQ - 価格と課金 

- [Security Center で組織内のどのユーザーが Azure Defender の変更を有効にしたかは、どのようにして追跡できますか?](#how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center)
- [Security Center ではどのようなプランが提供されていますか?](#what-are-the-plans-offered-by-security-center)
- [サブスクリプションで Azure Defender を有効にするにはどうすればよいですか?](#how-do-i-enable-azure-defender-for-my-subscription)
- [サブスクリプションのサーバーのサブセットで、サーバーに対して Azure Defender を有効にすることはできますか?](#can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription)
- [Microsoft Defender for Endpoint のライセンスが既にある場合、Azure Defender の割引を受けることができますか?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [私のサブスクリプションでは Azure Defender for servers が有効になっています。実行していないサーバーは課金されますか?](#my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers)
- [Log Analytics エージェントがインストールされていないマシンは課金されますか?](#will-i-be-charged-for-machines-without-the-log-analytics-agent-installed)
- [Log Analytics エージェントが複数のワークスペースにレポートする場合、二重に課金されるのですか?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice)
- [Log Analytics エージェントが複数のワークスペースにレポートする場合、それらすべてで 500 MB 無料のデータ インジェストが利用できるのですか?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them)
- [500 MB 無料のデータ インジェストの計算対象は、ワークスペース全体ですか、それとも厳密にマシン単位ですか?](#is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine)

### <a name="how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center"></a>Security Center で組織内のどのユーザーが Azure Defender の変更を有効にしたかは、どのようにして追跡できますか?
Azure サブスクリプションには、価格設定を変更する許可が与えられた管理者が複数設定されていることがあります。 変更を行ったユーザーを見つけるには、Azure アクティビティ ログを使用します。

:::image type="content" source="media/security-center-pricing/logged-change-to-pricing.png" alt-text="価格変更イベントを示す Azure アクティビティ ログ":::

**[イベント開始者]** 列の一覧にユーザーの情報が表示されない場合は、イベントの JSON から適切な情報を探してください。

:::image type="content" source="media/security-center-pricing/tracking-pricing-changes-in-activity-log.png" alt-text="Azure アクティビティ ログの JSON エクスプローラー":::


### <a name="what-are-the-plans-offered-by-security-center"></a>Security Center ではどのようなプランが提供されていますか? 
Security Center には 2 つのプランがあります。 

- Azure Security Center Free 
- Azure Defender  

### <a name="how-do-i-enable-azure-defender-for-my-subscription"></a>サブスクリプションで Azure Defender を有効にするにはどうすればよいですか? 
以下に示すどの方法でも、ご利用のサブスクリプションに対して Azure Defender を有効にすることができます。 

| メソッド                                          | Instructions                                                                                                                                       |
|-------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure portal の Azure Security Center ページ | [Azure Defender を有効にする](enable-azure-defender.md)                                                                                                  |
| REST API                                        | [Pricings API](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI                                       | [az security pricing](/cli/azure/security/pricing)                                                                                                 |
| PowerShell                                      | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy                                    | [バンドルの価格](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|                                                 |                                                                                                                                                    |

### <a name="can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>サブスクリプションのサーバーのサブセットで、サーバーに対して Azure Defender を有効にすることはできますか?
いいえ。 サブスクリプションで[サーバー用 Azure Defender](defender-for-servers-introduction.md) を有効にすると、サブスクリプション内のすべてのサーバーが Azure Defender によって保護されます。 

また、Log Analytics ワークスペース レベルでサーバー用 Azure Defender を有効にする方法もあります。 この場合、そのワークスペースにレポートするサーバーだけが保護され、課金されるようになります。 ただし、いくつかの機能が利用できなくなります。 それらの例としては、Just-in-Time VM アクセス、ネットワーク検出、規制コンプライアンス、アダプティブ ネットワークのセキュリティ強化機能、適応型アプリケーション制御などが挙げられます。 

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Microsoft Defender for Endpoint のライセンスが既にある場合、Azure Defender の割引を受けることができますか?
Microsoft Defender for Endpoint のライセンスを既に取得している場合は、Azure Defender ライセンスのその部分について料金を支払う必要はありません。

割引を確認するには、Security Center のサポート チームに連絡し、関連するライセンスごとに、関連するワークスペース ID、リージョン、ライセンス情報を提示してください。

### <a name="my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>私のサブスクリプションでは Azure Defender for servers が有効になっています。実行していないサーバーは課金されますか? 
いいえ。 サブスクリプションで [Azure Defender for servers](defender-for-servers-introduction.md) を有効にしても、割り当て解除済みの電源状態のマシンは課金されません。 マシンは電源の状態に基づいて課金されます。次の表を参照してください。

| State        | 説明                                                                                                                                      | 課金されたインスタンスの使用 |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| 開始中     | VM を起動中です。                                                                                                                               | 課金されません            |
| 実行中      | VM の通常の動作状態です                                                                                                                    | 課金されます                |
| 停止中     | これは過渡的な状態です。 完了すると、Stopped と表示されます。                                                                           | 課金されます                |
| 停止済み      | ゲスト OS 内から、または PowerOff API によって、VM がシャットダウンされています。 ハードウェアは引き続き VM に割り当てられ、VM はホスト上に残ります。 | 課金されます                |
| 割り当て解除中 | 過渡的な状態です。 完了すると、VM は割り当て解除済みと表示されます。                                                                             | 課金されません            |
| 割り当て解除済み  | VM は正常に停止され、ホストから削除されました。                                                                                  | 課金されません            |

:::image type="content" source="media/security-center-pricing/deallocated-virtual-machines.png" alt-text="割り当て解除状態のマシンを示す Azure Virtual Machines":::

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>Log Analytics エージェントがインストールされていないマシンは課金されますか?
はい。 サブスクリプションで[サーバー用 Azure Defender](defender-for-servers-introduction.md) を有効にすると、そのサブスクリプション内のマシンには、Log Analytics エージェントがインストールされていなくても一連の保護が適用されます。

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>Log Analytics エージェントが複数のワークスペースにレポートする場合、二重に課金されるのですか? 
はい。 2 つ以上の異なる Log Analytics ワークスペース (マルチホーム) にデータを送信するように Log Analytics エージェントを構成した場合、"セキュリティ" または "マルウェア対策" ソリューションがインストールされている各ワークスペースが課金の対象となります。 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>Log Analytics エージェントが複数のワークスペースにレポートする場合、それらすべてで 500 MB 無料のデータ インジェストが利用できるのですか?
はい。 2 つ以上の異なる Log Analytics ワークスペース (マルチホーム) にデータを送信するように Log Analytics エージェントを構成した場合、500 MB 分のデータ インジェストが無料になります。 これはノード単位、レポート先ワークスペース単位、日単位で計算され、"セキュリティ" または "マルウェア対策" ソリューションがインストールされているどのワークスペースでも利用できます。 500 MB を超えて取り込まれたデータについては課金の対象となります。

### <a name="is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine"></a>500 MB 無料のデータ インジェストの計算対象は、ワークスペース全体ですか、それとも厳密にマシン単位ですか?
ワークスペースに接続されている各マシンについて、1 日あたり 500 MB のデータ インジェストが無料で提供されます。 具体的には、Azure Security Center によって直接収集されたセキュリティ データのタイプが対象となります。

このデータは、1 日あたりのレートを全ノードにわたって平均したものです。 したがって 100 MB を送信するマシンの他に 800 MB を送信するマシンがあったとしても、その合計が、無料の上限である **[マシンの台数] x 500 MB** を超えなければ、余分に課金されることはありません。

## <a name="next-steps"></a>次のステップ
この記事では、Security Center の価格オプションについて説明しました。 関連資料については、以下を参照してください。

- [Azure ワークロードのコストを最適化する方法](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [ご利用の通貨とリージョンにおける価格の詳細](https://azure.microsoft.com/pricing/details/security-center/)
- ソリューションを特定のエージェント セットに制限することで、コストを管理し、ソリューション用に収集されるデータの量を制限することもできます。 [ソリューションのターゲット設定](../azure-monitor/insights/solution-targeting.md)を使用すると、ソリューションにスコープを適用し、ワークスペース内のコンピューターのサブセットをターゲットにすることができます。 ソリューションのターゲット設定を使用している場合、Security Center ではソリューションがないものとしてワークスペースを表示します。