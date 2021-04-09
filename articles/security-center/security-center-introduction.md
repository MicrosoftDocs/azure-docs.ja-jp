---
title: Azure Security Center とは | Microsoft Docs
description: このページでは、Security Center の主な利点 (リソースがクラウドとオンプレミスのどちらにあるかを問わず、セキュリティの状態を検出し、改善する) について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: c4861192c7f2bbfb2a51e19b88daee45b501949b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634772"
---
# <a name="what-is-azure-security-center"></a>Azure Security Center とは

Azure Security Center は、データ センターのセキュリティ体制を強化する統合インフラストラクチャ セキュリティ管理システムです。Azure 内かどうかにかかわらずクラウド内とオンプレミス上のハイブリッド ワークロード全体を保護する高度な脅威防止機能があります。

リソースの安全を保つことは、お客様のクラウド プロバイダー、Azure、そしてお客様が共同で取り組む作業です。 クラウドに移行する場合、ワークロードを確実にセキュリティで保護する必要があります。それと同時に、IaaS (サービスとしてのインフラストラクチャ) に移行する場合は、PaaS (サービスとしてのプラットフォーム) と SaaS (サービスとしてのソフトウェア) だったときよりもお客様の責任が大きくなります。 Azure Security Center には、ネットワークを強化し、サービスをセキュリティで保護し、お客様がセキュリティ体制を把握するために必要なツールが用意されています。

Azure Security Center は、3 つの緊急性が高いセキュリティの課題を対処します。

-   **急速に変化するワークロード**: これはクラウドの強みであり、課題でもあります。 一方、エンド ユーザーはより多くの処理を実行できます。 さらに、使用および作成されている常に変化するサービスが、お客様のセキュリティ基準に準拠し、セキュリティのベスト プラクティスに従っていることを確認するにはどうすればよいでしょうか。

-   **ますます高度になる攻撃**: ワークロードをどこで実行する場合でも、攻撃はますます高度になっています。 パブリック クラウドのワークロードを保護する必要があります。これは実質的にインターネットに接続しているワークロードであり、セキュリティのベスト プラクティスに従わないと、さらに脆弱になる可能性があります。

-   **セキュリティ スキルの不足**: セキュリティ アラートとアラート システムの数は、環境が保護されているかどうかを確認するために必要な経歴と経験を持つ管理者の数を上回っています。 最近の攻撃の最新情報を把握し続けることは常に課題であり、セキュリティの世界が絶え間なく変化する最前線に立ち続けることは不可能です。

このような課題からお客様自身を保護できるように、Security Center には次のツールが用意されています。

-   **セキュリティ体制を強化する**:Security Center を使用すると、ご自分の環境を評価することができ、リソースの状態や、それらがセキュリティで保護されているかどうかを把握できます。

-   **脅威からの保護**:Security Center ではワークロードが評価され、脅威防止の推奨事項とセキュリティ アラートが生成されます。

-   **より迅速にセキュリティ保護**:Security Center では、すべてがクラウドの速度で行われます。 ネイティブに統合されているので、Security Center のデプロイは容易であり、Azure サービスを自動的にプロビジョニングしたり、保護できます。

> [!NOTE]
> このサービスは [Azure Lighthouse](../lighthouse/overview.md) をサポートしているため、サービス プロバイダーは各自のテナントにサインインして、顧客から委任されたサブスクリプションやリソース グループを管理することができます。 Azure Security Center のシナリオでは、個々のリソース グループではなく、サブスクリプションを委任する必要があります。

## <a name="architecture"></a>アーキテクチャ

Security Center はネイティブで Azure の一部なので、Service Fabric、SQL Database、SQL Managed Instance、ストレージ アカウントなどの Azure に含まれる PaaS サービスは、デプロイすることなく Security Center で監視および保護されます。

さらに、Security Center は、Log Analytics エージェントをインストールすることにより、Windows と Linux の両方のサーバーで、クラウドまたはオンプレミスにある Azure 以外のサーバーと仮想マシンを保護します。 Azure 仮想マシンは、Security Center 内に自動プロビジョニングされます。

エージェントと Azure から収集されたイベントは、セキュリティ分析エンジンで相互に関連付けられ、調整された推奨事項 (強化タスク) が提供されます。これに従うことで、ワークロードをセキュリティで保護できます。また、セキュリティ アラートも提供されます。 このようなアラートはできるだけ早く調査し、ワークロードに対して悪意のある攻撃が行われていないことを確認する必要があります。

Security Center を有効にすると、Security Center に組み込まれたセキュリティ ポリシーが、Security Center カテゴリのビルトインのイニシアティブとして Azure Policy に反映されます。 組み込みのイニシアティブは、Security Center のすべての登録済みサブスクリプションに対して (Azure Defender が有効になっているかどうかに関係なく) 自動的に割り当てられます。 組み込みイニシアティブに含まれるのは監査ポリシーだけです。 Azure Policy における Security Center のポリシーの詳細については、「[セキュリティ ポリシーの操作](tutorial-security-policy.md)」を参照してください。

## <a name="strengthen-security-posture"></a>セキュリティ体制の強化

Azure Security Center を使用すると、セキュリティ体制を強化できます。 つまり、セキュリティのベスト プラクティスとして推奨される強化タスクを特定して実行し、マシン、データ サービス、およびアプリ全体に実装することができます。 これには、セキュリティ ポリシーの管理と実施、および Azure 仮想マシン、Azure 以外のサーバー、Azure PaaS サービスが準拠していることの確認が含まれています。 Security Center には、ネットワークのセキュリティ資産を明確に可視化して、ワークロード全体を把握するために必要なツールが用意されています。 

### <a name="manage-organization-security-policy-and-compliance"></a>組織のセキュリティ ポリシーとコンプライアンスの管理

これは、ワークロードがセキュリティで保護されていることを把握し、確実にするための基本的なセキュリティです。まず、セキュリティ ポリシーを適切に調整して配置することから始めます。 Security Center のすべてのポリシーは Azure Policy 制御を基礎にして構築されているため、**世界レベルのポリシー ソリューション** のすべての範囲と柔軟性を利用できます。 Security Center では、管理グループ、サブスクリプション全体、さらにはテナント全体に対して実行するようにポリシーを設定できます。

:::image type="content" source="./media/security-center-intro/sc-dashboard.png" alt-text="ポリシー管理ページ":::

Security Center を使用すると、**シャドウ IT のサブスクリプションを特定する** ことができます。 ダッシュボードで **未カバー** のラベルが付いたサブスクリプションを見ると、新しくサブスクリプションが作成されたときにすぐに把握できます。また、ポリシーによって保護され、Azure Security Center によって保護されていることを確認できます。

:::image type="content" source="./media/security-center-intro/sc-policy-dashboard.png" alt-text="Security Center のポリシー ダッシュボード":::

### <a name="continuous-assessments"></a>継続的な評価

Security Center では、ワークロード全体に展開されている新しいリソースが継続的に検出され、セキュリティのベスト プラクティスに従って構成されているかどうかが評価され、そうでない場合はフラグが立てられます。また、マシンを保護するために必要な修正点について、優先順位が付けられた推奨事項一覧が表示されます。 この推奨事項一覧を可能にし、サポートしているのは、[Azure セキュリティ ベンチマーク](../security/benchmarks/introduction.md)という、一般的なコンプライアンス フレームワークに基づくセキュリティとコンプライアンスのベスト プラクティスに関する Microsoft 製の Azure 固有の一連のガイドラインです。 この広く遵守されているベンチマークは、クラウド中心のセキュリティに重点を置いた [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) と [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) の統制に基づいています。

Security Center では、全体的なセキュリティ体制に対する各推奨事項の重要度が理解しやすいよう、推奨事項がセキュリティ コントロールにグループ化され、各コントロールには **セキュア スコア** 値が追加されます。 これは、**セキュリティ作業の優先度付け** を可能にするうえで重要です。

:::image type="content" source="./media/security-center-intro/sc-secure-score.png" alt-text="Security Center のセキュア スコア":::

### <a name="network-map"></a>ネットワーク マップ

ネットワークのセキュリティ状態を継続的に監視するために Security Center に用意されている最も強力なツールの 1 つとして、**ネットワーク マップ** があります。 このマップを使用すると、ワークロードのトポロジを確認できるため、各ノードが正しく構成されているかどうかを確認できます。 ノードがどのように接続されているかを確認できます。これにより、攻撃者がネットワークに侵入しやすくなる可能性がある望ましくない接続をブロックすることができます。

:::image type="content" source="./media/security-center-intro/sc-net-map.png" alt-text="Security Center のネットワーク マップ":::


### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>推奨されるコントロールを構成してセキュリティを最適化し、強化する

Azure Security Center が持つ価値の中心は、その推奨事項にあります。 推奨事項は、ワークロード上で見つかった特定のセキュリティ上の懸案事項に合わせて調整されています。Security Center では、脆弱性を検出するだけでなく、その排除のための具体的な手順を提供することで、セキュリティ管理者の作業が自動実行されます。

このように、Security Center では、セキュリティ ポリシーを設定するだけでなく、セキュリティで保護された構成基準をリソース全体に適用することができます。

推奨事項を利用すると、各リソースの攻撃対象領域を減らすことができます。 これには、Azure 仮想マシン、Azure 以外のサーバー、SQL や Storage アカウントなどの Azure PaaS サービスなどが含まれます。リソースの種類によって評価方法は異なり、固有の基準があります。

:::image type="content" source="./media/security-center-intro/sc-recommendation-example.png" alt-text="Security Center の推奨事項例":::

## <a name="protect-against-threats"></a>脅威からの保護

Security Center の脅威の防止機能により、Azure 内のサービスとしてのプラットフォーム (PaaS) だけでなく、サービスとしてのインフラストラクチャ (IaaS)、Azure 以外のサーバーの脅威も検出して防止することができます。

Security Center の脅威の防止機能には、サイバー キルチェーン分析に基づいて環境内のアラートを自動的に相関させるフュージョン キルチェーン分析が含まれており、攻撃キャンペーンの詳細 (開始点、リソースに対する影響の種類) を詳細に把握することができます。

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure Security Center のセキュリティ アラートの一覧":::

### <a name="integration-with-microsoft-defender-for-endpoint"></a>Microsoft Defender for Endpoint との統合

Azure Defender for servers には、Microsoft Defender for Endpoint との、自動的でネイティブな統合機能が備わっています。 詳細については、「[Security Center の統合 EDR ソリューションを使用してエンドポイントを保護する: Microsoft Defender for Endpoint](security-center-wdatp.md)」を参照してください。


### <a name="protect-paas"></a>PaaS の保護

Security Center は、Azure PaaS サービス全体の脅威を検出するために役立ちます。 Azure App Service、Azure SQL、Azure ストレージ アカウント、その他のデータ サービスを含む Azure サービスを対象とした脅威を検出できます。 また、Microsoft Cloud App Security のユーザーおよびエンティティ行動分析 (UEBA) とのネイティブ統合を利用して、Azure アクティビティ ログに対して異常検出を実行することもできます。

### <a name="block-brute-force-attacks"></a>ブルート フォース攻撃のブロック

Security Center は、ブルートフォース攻撃への露出を制限するために役立ちます。 Just-In-Time VM アクセスを使用して仮想マシン ポートへのアクセスを減らすことにより、不要なアクセスを防止してネットワークを強化することができます。 許可されたユーザー、許可されたソース IP アドレスの範囲または IP アドレス、および制限された期間にのみ、選択したポートに対するセキュリティで保護されたアクセス ポリシーを設定することができます。

### <a name="protect-data-services"></a>データ サービスの保護

Security Center には、Azure SQL 内のデータの自動分類を実行するために役立つ機能が含まれています。 また、Azure SQL と Storage サービス全体の潜在的な脆弱性の評価と、それらを緩和する方法の推奨事項を取得することもできます。

## <a name="get-secure-faster"></a>より迅速にセキュリティ保護

ネイティブの Azure 統合 (Azure Policy、Azure Monitor ログなど) が、他の Microsoft セキュリティ ソリューション (Microsoft Cloud App Security、Microsoft Defender for Endpoint など) とのシームレスな統合と組み合わされているため、包括的なセキュリティ ソリューションを実現できるだけでなく、オンボードと展開も簡単です。

さらに、Azure 以外にも、他のクラウド上やオンプレミスのデータ センター内で実行されているワークロードにソリューション全体を拡張することもできます。

### <a name="automatically-discover-and-onboard-azure-resources"></a>Azure リソースの自動検出とオンボード

Security Center で、Azure および Azure リソースとのシームレスでネイティブな統合が実現します。 つまり、すべての Azure リソース全体から Azure Policy と組み込みの Security Center ポリシーに関係する詳細なセキュリティ ストーリーを集めて、Azure でのリソースの作成時に、新たに検出されたリソースに自動的に適用することができます。

広範なログ収集: Windows および Linux のログはすべてセキュリティ分析エンジンで活用され、推奨事項とアラートの作成に使用されます。

## <a name="next-steps"></a>次のステップ

- Security Center を使用するには、Microsoft Azure のサブスクリプションが必要です。 サブスクリプションがない場合は、 [無料試用版](https://azure.microsoft.com/free/)にサインアップできます。

- Azure portal の Azure Security Center ダッシュボードに初めてアクセスしたとき、または API を介してプログラムで有効にした場合は、現在のすべての Azure サブスクリプションで Security Center の Free の価格レベルが有効になります。 高度なセキュリティ管理と脅威検出の機能を利用するには、Azure Defender を有効にする必要があります。 Azure Defender は、30 日間無料で試すことができます。 詳しくは、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」ページをご覧ください。

- 今すぐ Azure Defender を有効にする準備ができたら、「[クイック スタート: Azure Security Center を設定する](security-center-get-started.md)」で手順を参照してください。