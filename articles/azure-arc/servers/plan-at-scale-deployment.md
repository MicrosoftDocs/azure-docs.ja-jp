---
title: Azure Arc 対応サーバーの大規模デプロイを計画する方法
description: 多数のマシンを Azure Arc 対応サーバーに対して有効にして、Azure で、重要なセキュリティ、管理、監視の機能の構成を簡略化する方法について説明します。
ms.date: 02/23/2021
ms.topic: conceptual
ms.openlocfilehash: 0e77fc00f94f2f46c60bb2c5dcecc10a4e2e3bc5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032230"
---
# <a name="planing-for-an-at-scale-deployment-of-azure-arc-enabled-servers"></a>Azure Arc 対応サーバーの大規模デプロイの計画

IT インフラストラクチャ サービスまたはビジネス アプリケーションのデプロイは、どの企業にとっても難題です。 適切に実行し、うれしくない驚きや計画外のコストを回避するには、徹底的に計画を立てて可能な限り準備しておく必要があります。 Azure Arc 対応サーバーの大規模なデプロイを計画するには、大規模なデプロイをサポートするタスクを適切に完了するために満たす必要がある、設計とデプロイの条件を網羅する必要があります。

デプロイを円滑に進めるには、計画により、以下のことが明確に理解されるようにする必要があります。

* ロールと責任。
* 物理サーバーまたは仮想マシンがネットワークとシステムの要件を満たしていることを確認するための、それらのインベントリ。
* デプロイと継続的な管理の成功を実現するために必要なスキル セットとトレーニング。
* 受け入れの基準と、その成功を追跡する方法。
* デプロイを自動化するために使用するツールまたは方法。
* 遅延や中断などを避けるために特定済みのリスクと軽減策。
* デプロイ中に中断を回避する方法。
* 重要な問題が発生したときにどのようなエスカレーション パスがあるか。

この記事の目的は、環境内の複数の運用物理サーバーまたは仮想マシンの全体にわたって Azure Arc 対応サーバーのデプロイを成功させるための準備を、確実に行うことです。

## <a name="prerequisites"></a>前提条件

* お使いのマシンで、Connected Machine エージェントで[サポートされているオペレーティング システム](agent-overview.md#supported-operating-systems)が実行されていること。
* お使いのマシンが、オンプレミスのネットワークまたはその他のクラウド環境から、直接またはプロキシ サーバーを介して Azure 内のリソースに接続できること。
* Arc 対応サーバーの Connected Machine エージェントをインストールして構成するための、マシンでの昇格された (つまり、管理者またはルートとしての) 特権を持つアカウント。
* マシンをオンボードするには、**Azure Connected Machine のオンボード** ロールのメンバーである必要があります。
* マシンの読み取り、変更、および削除を行うには、**Azure Connected Machine のリソース管理者** ロールのメンバーである必要があります。

## <a name="pilot"></a>パイロット

すべての運用マシンへのデプロイを行う前に、このデプロイ プロセスを評価することから開始し、その後、環境内でそのプロセスを広く採用します。 パイロットの場合は、ビジネスを遂行する会社の機能にとって重要でないマシンの代表的なサンプリングを特定します。 パイロットを実行し、その影響を評価するのに十分な時間を確保することをお勧めします。Microsoft では、少なくとも 30 日をお勧めします。

パイロットの範囲と詳細を説明する正式な計画を立てます。 作業の開始に役立つように、次のサンプルに、計画に何を含める必要があるかを示します。

* 目標 - パイロットが必要であるという決定に至った、ビジネス上、技術上の決定要因について説明します。
* 選択条件 - パイロットによって、ソリューションのどのような側面を実証するかを選択するために使用する条件を指定します。
* 範囲 - パイロットの範囲について説明します。これには、ソリューションのコンポーネント、予想スケジュール、パイロットの期間、対象とするマシンの数などが含まれます。
* 成功条件とメトリック - パイロットの成功条件と、成功のレベルを判断するために使用される具体的な基準を定義します。
* トレーニング計画 - パイロット中に、Azure とそのサービスの使用経験がないシステム エンジニアや管理者などをトレーニングするための計画について説明します。
* 移行計画 - パイロットから運用への移行をガイドするために使用される戦略と条件について説明します。
* ロールバック - パイロットをデプロイ前の状態にロールバックする手順について説明します。
* リスク - パイロットの実施と、運用のデプロイに関連する、特定されたすべてのリスクを一覧で示します。

## <a name="phase-1-build-a-foundation"></a>フェーズ 1: 基盤を構築する

このフェーズでは、システム エンジニアまたは管理者が、組織の Azure サブスクリプションでコア機能を有効にして、お使いのマシンで Arc 対応サーバーおよびその他の Azure サービスによる管理を有効にする前に、基盤の構築を開始します。

|タスク |Detail |Duration |
|-----|-------|---------|
| [リソース グループの作成](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Arc 対応サーバーだけを含み、これらのリソースの管理と監視を一元化する専用リソース グループ。 | 1 時間 |
| マシンの整理に役立つ[タグ](../../azure-resource-manager/management/tag-resources.md)を適用する。 | Arc 対応サーバーの管理の複雑さを軽減し、管理上の意思決定を簡略化するのに役立つ、IT 面で調整された[タグ付け戦略](/cloud-adoption-framework/decision-guides/resource-tagging/)の評価と作成を行います。 | 1 日 |
| [Azure Monitor ログ](../../azure-monitor/logs/data-platform-logs.md)を設計してデプロイする | [設計とデプロイに関する考慮事項](../../azure-monitor/logs/design-logs-deployment.md)を評価して、ハイブリッド サーバーとマシンから収集したログ データを格納するために、組織が既存の Log Analytics ワークスペースを使用するか、別の Log Analytics ワークスペースを実装するかを決定します。<sup>1</sup> | 1 日 |
| [Azure Policy](../../governance/policy/overview.md) ガバナンス計画を作成する | Azure Policy を使用して、サブスクリプションまたはリソース グループのスコープでハイブリッド サーバーとマシンのガバナンスを実装する方法を決定します。 | 1 日 |
| [ロールベースのアクセス制御](../../role-based-access-control/overview.md) (RBAC) を構成する | Arc 対応サーバーを管理するためのアクセスと、他の Azure サービスやソリューションからのデータを表示する機能を、誰が持つかを制御するアクセス計画を作成します。 | 1 日 |
| Log Analytics エージェントが既にインストールされているマシンを特定する | 既存の Log Analytics エージェントのデプロイから、拡張機能で管理されるエージェントへの変換をサポートするため、[Log Analytics](../../azure-monitor/logs/log-analytics-overview.md) で次のログクエリを実行します。<br> Heartbeat <br> &#124; where TimeGenerated > ago(30d) <br> &#124; where ResourceType == "machines" and (ComputerEnvironment == "Non-Azure") <br> &#124; summarize by Computer, ResourceProvider, ResourceType, ComputerEnvironment | 1 時間 |

<sup>1</sup> Log Analytics ワークスペースの設計評価の一部としての重要な考慮事項は、Update Management、変更履歴とインベントリ機能のほか、Azure Security Center と Azure Sentinel をサポートする Azure Automation との統合です。 組織に既に Automation アカウントがあり、Log Analytics ワークスペースにリンクされる管理機能が有効になっている場合は、重複するアカウントやワークスペースなどを作成する代わりにそれらの既存のリソースを使用することによって、管理操作を一元化して合理化できるかどうかを評価し、コストを最小限に抑えることができます。

## <a name="phase-2-deploy-arc-enabled-servers"></a>フェーズ 2: Arc 対応サーバーをデプロイする

次に、デプロイを準備し、エージェントのインストールを実行して、フェーズ 1 で構築した基盤への追加を行います。

|タスク |Detail |Duration |
|-----|-------|---------|
| 定義済みのインストール スクリプトをダウンロードする | 自動デプロイの要件をサポートするために、Connected Machine エージェントの大規模なデプロイ用に事前に定義されたインストール スクリプトを見直してカスタマイズします。<br><br> 大規模なオンボード リソースのサンプル:<br><br> <ul><li> [大規模な基本的デプロイのスクリプト](onboard-service-principal.md)</ul></li> <ul><li>[VMware vSphere の Windows Server VM の大規模なオンボード](https://github.com/microsoft/azure_arc/blob/master/azure_arc_servers_jumpstart/docs/vmware_scaled_powercli_win.md)</ul></li> <ul><li>[VMware vSphere の Linux VM の大規模なオンボード](https://github.com/microsoft/azure_arc/blob/master/azure_arc_servers_jumpstart/docs/vmware_scaled_powercli_linux.md)</ul></li> <ul><li>[Ansible を使用した、AWS EC2 インスタンスの大規模なオンボード](https://github.com/microsoft/azure_arc/blob/master/azure_arc_servers_jumpstart/docs/aws_scale_ansible.md)</ul></li> <ul><li>[PowerShell リモート処理を使用した大規模なデプロイ](https://docs.microsoft.com/azure/azure-arc/servers/onboard-powershell) (Windows のみ)</ul></li>| 要件、組織のプロセス (変更およびリリース管理など)、使用されたオートメーションの方法に応じて 1 日以上。 |
| [サービス プリンシパルを作成する](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) |サービス プリンシパルを作成し、Azure PowerShell を使用して、またはポータルから、非対話形式でコンピューターを接続します。| 1 時間 |
| ターゲットのサーバーとマシンに Connected Machine エージェントをデプロイする |オートメーション ツールを使用してスクリプトをサーバーにデプロイし、サーバーを Azure に接続します。| リリース計画と、段階的ロールアウトを行うかどうかに応じて 1 日以上。 |

## <a name="phase-3-manage-and-operate"></a>フェーズ 3: 管理して運用する

フェーズ 3 では、管理者またはシステム エンジニアが、Connected Machine エージェントとマシンをそれらのライフサイクル中に管理し、運用するための手動タスクのオートメーションを有効にします。

|タスク |Detail |Duration |
|-----|-------|---------|
|Resource Health アラートを作成する |サーバーが Azure へのハートビートの送信を 15 分より長く停止する場合は、それがオフラインであるか、ネットワーク接続がブロックされているか、エージェントが実行されていないことを意味する可能性があります。 これらのインシデントに対応して調査し、[Resource Health アラート](../..//service-health/resource-health-alert-monitor-guide.md)を使用してその開始時に通知を受け取る方法の計画を作成します。<br><br> アラートの構成時には以下のように指定します。<br> **リソースの種類** = **Azure Arc 対応サーバー**<br> **現在のリソースの状態** = **使用不可**<br> **以前のリソースの状態** = **使用可能** | 1 時間 |
|Azure Advisor アラートを作成する | 優れたエクスペリエンスと最新のセキュリティおよびバグの修正プログラムについては、Azure Arc 対応サーバー エージェントを最新の状態に保つことをお勧めします。 古くなったエージェントは、[Azure Advisor アラート](../../advisor/advisor-alerts-portal.md)で識別されます。<br><br> アラートの構成時には以下のように指定します。<br> **推奨の種類** = **最新バージョンの Azure Connected Machine Agent にアップグレードする** | 1 時間 |
|サブスクリプションまたはリソース グループのスコープに [Azure ポリシーを割り当てる](../../governance/policy/assign-policy-portal.md) |**Azure Monitor for VMs の有効化** [ポリシー](../../azure-monitor/vm/vminsights-enable-policy.md) (およびニーズに合ったその他のポリシー) をサブスクリプションまたはリソース グループ スコープに割り当てます。 Azure Policy により、お使いの環境全体で Azure Monitor for VMs に必要なエージェントをインストールするポリシーの定義を割り当てることができます。| 場合により異なる |
|[Arc 対応サーバーに対して Update Management を有効にする](../../automation/update-management/enable-from-automation-account.md) |Azure Automation で Update Management を構成して、Arc 対応サーバーに登録されている Windows と Linux の仮想マシンのオペレーティング システム更新プログラムを管理します。 | 約 15 分 |

## <a name="next-steps"></a>次のステップ

* トラブルシューティング情報は、[Connected Machine エージェントの問題解決ガイド](troubleshoot-agent-onboard.md)を参照してください。

* Azure Automation の [State Configuration](../../automation/automation-dsc-overview.md) や、その他にサポートされている [Azure VM 拡張機能](manage-vm-extensions.md)のような他の Azure サービスを使用してデプロイを簡略化する方法について説明します。