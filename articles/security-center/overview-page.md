---
title: Azure Security Center のメイン ダッシュボードまたは [概要] ページ
description: Security Center の概要ページの機能について学習します
author: memildin
ms.author: memildin
ms.date: 03/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 768d69b626a870910d6734e1a1ddc29871f96afb
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099779"
---
# <a name="azure-security-centers-overview-page"></a>Azure Security Center の概要ページ

Azure Security Center を開いたときに、最初に表示されるページが概要ページです。 

この対話型ダッシュボードでは、ハイブリッド クラウド ワークロードのセキュリティ体制に関する統合ビューが提供されます。 また、セキュリティ アラート、カバレッジ情報なども表示されます。

ページ上の任意の要素を選択すると、より詳細な情報を取得できます。

:::image type="content" source="media/overview-page/overview.png" alt-text="Security Center の概要ページ":::

## <a name="features-of-the-overview-page"></a>概要ページの機能

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="Security Center の概要ページの上部バー":::

**上部のメニュー バー** では、次のものが提供されます。
- **サブスクリプション** - このボタンを選択して、サブスクリプションの一覧を表示およびフィルター処理できます。 選択されたサブスクリプションのセキュリティの状態を反映するように、Security Center の表示が調整されます。
- **新機能** - [リリース ノート](release-notes.md)を開いて、新機能、バグ修正、非推奨の機能を最新の状態に保つことができます。
- 接続されたクラウド アカウントにおいて、以下のメイン タイルに情報のコンテキストを表示するための **高レベルの数値**。 評価されたリソース、アクティブな推奨事項、セキュリティ アラートの数も示されます。 評価されたリソース数を選択すると、[資産インベントリ](asset-inventory.md)にアクセスできます。 [AWS アカウント](quickstart-onboard-aws.md)と [GCP プロジェクト](quickstart-onboard-gcp.md)の接続の詳細について詳しく確認します。


ページの中央には **4 つの中央タイル** があります。詳細については、専用ダッシュボードへの各リンクを参照してください。
- **セキュア スコア** - Security Center によって、セキュリティの問題について、リソース、サブスクリプション、組織を継続的に評価されます。 その後、すべての結果を 1 つのスコアに集約して、現在のセキュリティの状況を一目で確認できるようにします。スコアが高くなるほど、識別されたリスク レベルが低下します。 [詳細については、こちらを参照してください](secure-score-security-controls.md)。
- **規制コンプライアンス** - お客様の Azure 環境の継続的評価に基づいて、お客様のコンプライアンス体制に関する分析情報が Security Center から提供されます。 Security Center を使うと、セキュリティのベスト プラクティスに従って、ハイブリッド クラウド環境のリスク要因を分析できます。 それらの評価は、サポートされている一連の標準のコンプライアンス制御に対応付けられます。 [詳細については、こちらを参照してください](security-center-compliance-dashboard.md)。
- **Azure Defender** - これは Azure およびハイブリッド ワークロードを高度かつインテリジェントに保護するために Security Center 内に統合された、クラウド ワークロード保護プラットフォーム (CWPP) です。 タイルには、接続されているリソース (現在選択されているサブスクリプション用) と最近のアラート (重要度別にカラー コード) のカバレッジが表示されます。 [詳細については、こちらを参照してください](azure-defender.md)。
- **Firewall Manager** - このタイルには、[Azure Firewall Manager](../firewall-manager/overview.md) のハブとネットワークの状態が表示されます。 


**[分析情報]** ウィンドウでは、ご利用の環境用にカスタマイズされた次のような項目が提供されます。
- 最も攻撃を受けたリソース
- セキュア スコアを上げる可能性が最も高いセキュリティ制御
- 影響を受けたリソースが最も多いアクティブな推奨事項
- Azure Security Center の専門家による最近のブログ記事

## <a name="next-steps"></a>次の手順

このページでは、Security Center の概要ページを紹介しました。 関連情報については、以下をご覧ください。

- [資産インベントリ ツールと資産管理ツールを使用してリソースの調査と管理を行う](asset-inventory.md)
- [Azure Security Center 内のセキュリティ スコア](secure-score-security-controls.md)