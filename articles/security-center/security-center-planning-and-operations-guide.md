---
title: Security Center 計画および運用ガイド
description: このドキュメントを利用して、Azure Security Center と日常的な運用に関する考慮事項の採用前に計画を立てることができます。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: a8b285ca0b8d0dae88bfe8dd19ac7e1bc8372e32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099201"
---
# <a name="planning-and-operations-guide"></a>計画と運用のガイド
このガイドは、Azure Security Center の使用を計画している情報技術 (IT) プロフェッショナル、IT アーキテクト、情報セキュリティ アナリスト、クラウド管理者を対象としています。


## <a name="planning-guide"></a>計画ガイド
このガイドでは、組織のセキュリティ要件とクラウド管理モデルに応じて Security Center の利用を最適化するために実行できるタスクについて説明します。 Security Center を最大限に活用するには、安全な開発と運用、監視、ガバナンス、インシデント対応のニーズを満たすために、組織内のさまざまな個人やチームがこのサービスをどのように使用するのかを把握することが重要です。 Security Center の使用を計画するうえで考慮が必要となる主な領域は次のとおりです。

* セキュリティ ロールとアクセス制御
* セキュリティ ポリシーと推奨事項
* データの収集と保存
* Azure 以外のリソースのオンボード
* 継続的なセキュリティの監視
* インシデント対応

次のセクションでは、これらの領域のそれぞれについて計画し、自社の要件に合わせて推奨事項を適用する方法について説明します。


> [!NOTE]
> 「 [Azure Security Center のよく寄せられる質問 (FAQ)](faq-general.md) 」に、設計と計画のフェーズにも役立つ一般的な質問の一覧が記載されています。

## <a name="security-roles-and-access-controls"></a>セキュリティ ロールとアクセス制御
組織の規模と構造によっては、複数の個人やチームが Security Center を使用して、セキュリティ関連のさまざまなタスクを実行する場合があります。 次の図には、架空の人物のほか、それぞれの役割とセキュリティ責任が例として挙げられています。

![ロール](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-new.png)

Security Center を使用すると、上記のようなさまざまな責任を果たすことができます。 次に例を示します。

**Jeff (ワークロード所有者)**

* クラウド ワークロードとその関連リソースを管理する
* 会社のセキュリティ ポリシーに従って保護を実装、管理する責任を負う

**Ellen (CISO/CIO)**

* 会社のセキュリティに関して全面的な責任を負う
* クラウド ワークロード全般について会社のセキュリティ体制を把握したいと考えている
* 主な攻撃とリスクを把握している必要がある

**David (IT セキュリティ担当者)**

* 適切な保護が実施されるように、会社のセキュリティ ポリシーを設定する
* ポリシーを使用してコンプライアンスを監視する
* リーダーまたは監査担当者向けのレポートを作成する

**Judy (セキュリティ運用担当者)**

* 24 時間 365 日監視を行い、セキュリティ アラートに対応する
* クラウド ワークロード所有者または IT セキュリティ アナリストに報告を行う

**Sam (セキュリティ アナリスト)**

* 攻撃を調査する
* クラウド ワークロード所有者と連携して修復を実施する

Security Center では、[Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) が使用されており、これによって提供される[組み込みのロール](../role-based-access-control/built-in-roles.md)は、Azure でユーザー、グループ、サービスに割り当てることができます。 ユーザーが Security Center を開くと、アクセス権のあるリソースに関する情報のみが表示されます。 これは、サブスクリプションまたはリソースが属するリソース グループについて、所有者、共同作業者、閲覧者のいずれかのロールがユーザーに割り当てられていることを意味します。 これらのロールに加え、Security Center には、次の 2 つの固有のロールがあります。

- **セキュリティ閲覧者**: このロールに属しているユーザーは、Security Center の構成 (推奨事項、アラート、ポリシー、正常性を含む) のみを閲覧できますが、変更を加えることはできません。
- **セキュリティ管理者**: セキュリティ閲覧者と同じですが、セキュリティ ポリシーの更新と、推奨事項とアラートの解除を実行することもできます。

上記で説明した Security Center のロールには、ストレージ、Web とモバイル、モノのインターネットなどの Azure の他のサービス領域へのアクセス権はありません。

前の図で説明した人物の例では、次の Azure RBAC が必要になります。

**Jeff (ワークロード所有者)**

* リソース グループの所有者/共同作成者

**Ellen (CISO/CIO)**

* サブスクリプションの所有者/共同作成者またはセキュリティ管理者

**David (IT セキュリティ担当者)**

* サブスクリプションの所有者/共同作成者またはセキュリティ管理者

**Judy (セキュリティ運用担当者)**

* サブスクリプションの閲覧者またはセキュリティ閲覧者 (アラートを表示する場合)
* サブスクリプションの所有者/共同作成者またはセキュリティ管理者 (アラートを解除する場合は必須)

**Sam (セキュリティ アナリスト)**

* サブスクリプションの閲覧者 (アラートを表示する場合)
* サブスクリプションの所有者/共同作成者 (アラートを解除する場合は必須)
* ワークスペースへのアクセスが必要な場合がある

上記に加えて、次の点を考慮する必要があります。

* セキュリティ ポリシーを編集できるのは、サブスクリプションの所有者/共同作成者とセキュリティ管理者のみです。
* セキュリティに関する推奨事項をリソースに適用できるのは、サブスクリプションとリソース グループの所有者と共同作成者のみです。

Security Center の Azure RBAC を使用してアクセス制御を計画する際は、組織内のどのユーザーが Security Center を使用するのかを必ず把握してください。 また、実行されるタスクの種類を把握し、それに応じて Azure RBAC を構成してください。

> [!NOTE]
> タスクを実行するために必要となる最小限の権限ロールをユーザーに割り当てることをお勧めします。 たとえば、リソースのセキュリティ状態に関する情報の表示のみが必要で、推奨事項の適用やポリシーの編集などの操作を行う必要がないユーザーには、閲覧者ロールを割り当てます。
>
>

## <a name="security-policies-and-recommendations"></a>セキュリティ ポリシーと推奨事項
セキュリティ ポリシーは、ワークロードの必要な構成を定義し、会社や規制のセキュリティ要件に確実に準拠できるようにします。 Security Center では、Azure サブスクリプションのポリシーを定義できます。これらのポリシーは、ワークロードの種類やデータの機密性に合わせて調整できます。

Security Center のポリシーには、次のコンポーネントが含まれています。
- [データ収集](security-center-enable-data-collection.md): エージェントのプロビジョニングとデータ収集の設定。
- [セキュリティ ポリシー](tutorial-security-policy.md): Security Center で監視および推奨されるコントロールを決定する [Azure Policy](../governance/policy/overview.md)。または、Azure Policy を使用して、新しい定義の作成、追加ポリシーの定義、管理グループ間でのポリシーの割り当てを行います。
- [電子メール通知](security-center-provide-security-contact-details.md): セキュリティ連絡先と通知設定。
- [価格レベル](security-center-pricing.md): Azure Defender の有無にかかわらず、(API を使用してサブスクリプション、ワークスペース、またはリソース グループに指定できる) スコープ内のリソースに使用できる Security Center の機能が決まります。

> [!NOTE]
> セキュリティ コントラクトを指定すると、セキュリティ インシデントが発生した場合に Azure から組織内の適切な人物に連絡が届くようになります。 この推奨事項を有効にする方法の詳細については、「 [Azure Security Center でセキュリティ連絡先の詳細情報を指定する](security-center-provide-security-contact-details.md) 」を参照してください。

### <a name="security-policies-definitions-and-recommendations"></a>セキュリティ ポリシーの定義と推奨事項
Security Center では、Azure サブスクリプションごとに自動で既定のセキュリティ ポリシーが作成されます。 そのポリシーを Security Center で編集するか、Azure Policy を使って新しい定義を作成したり、追加のポリシーを定義したり、管理グループ (組織全体のこともあれば、その中の一部署などのこともあります) にポリシーを割り当てたりすることによって、複数のスコープにわたってポリシーに対するコンプライアンスを監視することができます。

セキュリティ ポリシーを構成する前に、 [セキュリティに関する推奨事項](security-center-recommendations.md)をそれぞれ確認し、対象の各種サブスクリプションとリソース グループに対してこれらのポリシーが適切かどうかを判断します。 セキュリティに関する推奨事項に対処するためにどのような処置を実行する必要があるか、および組織のだれが新しい推奨事項を監視し、必要な手順に行うかを理解しておくことも重要です。

## <a name="data-collection-and-storage"></a>データの収集と保存
Azure Security Center では、Log Analytics エージェントを使用して、仮想マシンからセキュリティ データを収集します。これは、Azure Monitor サービスで使用されるのと同じエージェントです。 このエージェントから[収集されたデータ](security-center-enable-data-collection.md)は、Log Analytics ワークスペースに格納されます。

### <a name="agent"></a>エージェント

セキュリティ ポリシーで自動プロビジョニングを有効にすると、サポートされているすべての Azure VM と作成される新しい VM に Log Analytics エージェント ([Windows](../azure-monitor/agents/agent-windows.md) または [Linux](../azure-monitor/vm/quick-collect-linux-computer.md) の場合) がインストールされます。 VM またはコンピューターに Log Analytics エージェントが既にインストールされている場合、Azure Security Center は、現在インストールされているエージェントを活用します。 エージェントのプロセスは、他への影響が少なく設計されているため、VM のパフォーマンスに対する影響もごくわずかです。

Windows 用 Log Analytics エージェントでは、TCP ポート 443 を使用する必要があります。 詳細については、[トラブルシューティングに関する記事](security-center-troubleshooting-guide.md)を参照してください。

ある時点で、データ収集を無効にする必要が生じた場合は、セキュリティ ポリシーで無効にすることができます。 ただし、Log Analytics エージェントは他の Azure 管理サービスや監視サービスで使用されている場合があるため、Security Center でデータ収集をオフにしても、このエージェントが自動的にアンインストールされることはありません。 必要な場合は、手動でエージェントをアンインストールできます。

> [!NOTE]
> サポート対象の VM の一覧については、「[Azure Security Center のよく寄せられる質問 (FAQ)](faq-vms.md)」を参照してください。

### <a name="workspace"></a>ワークスペース

ワークスペースとは、データのコンテナーとして機能する Azure リソースです。 組織のメンバーは、複数のワークスペースを使用して、IT インフラストラクチャの一部またはすべてから収集されるデータのさまざまなセットを管理する場合があります。

(Azure Security Center に代わって) Log Analytics エージェントから収集されたデータは、VM の位置情報を考慮して、Azure サブスクリプションに関連付けられている既存の Log Analytics ワークスペースまたは新規のワークスペースのいずれかに格納されます。

Azure ポータルで、Log Analytics ワークスペースの一覧を参照して表示できます。一覧には、Azure Security Center によって作成されたワークスペースも含まれます。 新しいワークスペースに対して、関連するリソース グループが作成されます。 それらは、次の名前付け規則に従います。

* ワークスペース:*DefaultWorkspace-[subscription-ID]-[geo]*
* リソース グループ:*DefaultResourceGroup-[geo]*

Azure Security Center によって作成されたワークスペースでは、データは 30 日間保持されます。 既存のワークスペースでは、リテンション期間は、ワークスペースの価格レベルに基づきます。 必要に応じて、既存のワークスペースを使用することもできます。

> [!NOTE]
> Microsoft は、このデータのプライバシーとセキュリティの保護に積極的に取り組んでいます。 Microsoft ではコーディングからサービスの運用まで、厳密なコンプライアンスとセキュリティのガイドラインに準拠しています。 データの取い扱いとプライバシーに関する詳細については、「[Azure Security Center のデータ セキュリティ](security-center-data-security.md)」を参照してください。
>

## <a name="onboard-non-azure-resources"></a>Azure 以外のリソースのオンボード

Security Center では、Azure 以外のコンピューターのセキュリティの状況を監視できますが、これらのリソースをオンボードしておく必要があります。 Azure 以外のリソースをオンボードする方法の詳細については、[Azure 以外のコンピューターのオンボード](quickstart-onboard-machines.md)に関するページを参照してください。

## <a name="ongoing-security-monitoring"></a>継続的なセキュリティの監視
Security Center の初期構成と推奨事項の適用を完了したら、次の手順は Security Center の運用プロセスの検討です。

Security Center の [概要] では、Azure リソースと接続済みの Azure 以外のリソースすべてのセキュリティが統合された 1 つのビューが表示されます。 以下の例は、対処すべき多くの問題を含む環境を示しています。

![dashboard](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig11.png)

> [!NOTE]
> Security Center は、通常の運用上の手順には干渉しません。デプロイを受動的に監視し、有効にされたセキュリティ ポリシーに基づいて推奨事項を提供します。

現在の Azure 環境に対して Security Center を使用することを初めて選択する場合は、**推奨事項** のページで、すべての推奨事項を必ずご確認ください。

毎日のセキュリティ操作の一環として、脅威インテリジェンス オプションにアクセスすることを計画してください。 そこでは、特定のコンピューターがボットネットの一部であるかどうかを特定するなど、環境に対するセキュリティの脅威を特定することができます。

### <a name="monitoring-for-new-or-changed-resources"></a>新しいリリースや変更されたリソースの監視

ほとんどの Azure 環境は、リソースが定期的に作成、スピンアップまたはダウン、再構成、変更され動的です。 Security Center を使用すると、これらの新しいリソースのセキュリティ状態を可視化できるようになります。

Azure 環境に新しいリソース (VM、SQL DB) を追加すると、Security Center によって自動的にこれらのリソースが検出され、セキュリティの監視が開始されます。 これには、PaaS の Web ロールと worker ロールも含まれます。 データ収集が [セキュリティ ポリシー](tutorial-security-policy.md)で有効になっている場合は、仮想マシンに対して追加の監視機能が自動的に有効になります。

また、セキュリティ上のリスクが生じる可能性がある構成の変更、推奨されるベースラインからの逸脱、およびセキュリティ アラートがないか、既存のリソースを定期的に監視する必要があります。 

### <a name="hardening-access-and-applications"></a>アクセスとアプリケーションのセキュリティ強化

セキュリティ操作の一環として、VM へのアクセスを制限する予防措置を採用したり、VM 上で実行されているアプリケーションを制御したりする必要もあります。 Azure VM への受信トラフィックをロックダウンすることで、攻撃にさらされることを減らすと同時に、必要に応じて簡単に VM に接続できるようになります。 VM へのアクセスのセキュリティを強化するには、[Just-In-Time VM アクセス](security-center-just-in-time.md)機能を使用します。

[適応型アプリケーション制御](security-center-adaptive-application.md)を使用して、Azure に配置された VM で実行できるアプリケーションを制限できます。 いくつかの利点のうち、これは特にマルウェアから VM を強化することに役立ちます。 機械学習を使用すると、一覧を表示できる規則の作成を支援するため、Security Center によって VM で実行されているプロセスが分析されます。


## <a name="incident-response"></a>インシデント対応
脅威が発生すると、Security Center によって検出され、アラートが生成されます。 組織は新しいセキュリティ アラートの有無を監視し、必要に応じて、詳しい調査や攻撃の修復のための対策を講じる必要があります。 Security Center の脅威保護のしくみの詳細については、[Azure Security Center の脅威の検出と応答の方法](security-center-alerts-overview.md#detect-threats)に関する記事を参照してください。

この記事は実際のインシデント対応プランの作成支援を目的としていないため、インシデント対応の段階のベースとして、クラウド ライフサイクルにおける Microsoft Azure のセキュリティ レスポンスを使用します。 段階は次の図のとおりです。

![クラウド ライフサイクルにおけるインシデント対応の段階](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> 計画の作成については、米国国立標準技術研究所 (NIST) の『 [Computer Security Incident Handling Guide (コンピューター セキュリティ インシデント対応ガイド)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) 』が参考資料としてご利用いただけます。
>

次の段階で、Security Center の警告を使用できます。

* **検出**: 1 つまたは複数のリソースで、疑わしいアクティビティを識別します。
* **評価**: 最初の評価を実行して、疑わしいアクティビティに関する詳細情報を入手します。
* **診断**: 修復手順を使用して技術的な処置を施し、問題に対処します。

各セキュリティ アラートで提供される情報は、攻撃の性質に関する理解を深め、緩和策を提案するために活用できます。 詳細情報へのリンクか Azure 内の他の情報源へのリンクがアラートに記載されている場合もあります。 提供される情報を使用して、さらに検索したり、緩和策を開始したりできます。ワークスペースに格納されているセキュリティ関連のデータを検索することもできます。

次の例は、疑わしい RDP アクティビティの発生を示しています。

![Suspicious activity](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

このページには、攻撃が発生した時刻、ソース ホスト名、標的となった VM に関する詳細情報のほか、推奨される手順も表示されます。 状況によっては、攻撃元の情報が空白になっていることもあります。 このような動作の詳細については、「 [Missing Source Information in Azure Security Center Alerts (Azure Security Center アラートに表示されないソース情報)](/archive/blogs/azuresecurity/missing-source-information-in-azure-security-center-alerts) 」を参照してください。

侵害されたシステムを特定したら、以前に作成した[ワークフローの自動化](workflow-automation.md)を実行できます。 これらは、アラートによってトリガーされた場合に Security Center から実行できるプロシージャのコレクションです。

ビデオ「[Azure Security Center と Microsoft Operations Management Suite をインシデント対応に活用する方法](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703)」では、上記の各段階における Security Center の使用方法を示すデモをいくつか確認できます。

> [!NOTE]
> インシデント対応プロセス中に役立つ Security Center 機能の使用方法の詳細については、[Azure Security Center でのセキュリティ アラートの管理と対応](security-center-managing-and-responding-alerts.md)に関するページを参照してください。
>
>

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Security Center の導入を計画する方法について説明しました。 セキュリティ センターの詳細については、次を参照してください。

* [Azure セキュリティ センターでのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)
* [Azure Security Center を使用したパートナー ソリューションの監視](./security-center-partner-integration.md) 」 -- パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](faq-general.md) 」 -- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](/archive/blogs/azuresecurity/) -- Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。