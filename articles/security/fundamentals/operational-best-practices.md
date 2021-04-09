---
title: Azure 資産のセキュリティに関するベスト プラクティス
titleSuffix: Azure security
description: この記事では、Azure 内のデータ、アプリケーション、その他の資産を保護するための運用可能な一連のベスト プラクティスについて説明します。
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 86874a60d48ddcbdaca5ae779ad554ee58cc233b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96498848"
---
# <a name="azure-operational-security-best-practices"></a>Azure で運用可能なセキュリティに関するベスト プラクティス
この記事では、Azure 内のデータ、アプリケーション、その他の資産を保護するための運用可能な一連のベスト プラクティスについて説明します。

これらのベスト プラクティスは、集約された意見に基づくものであり、Azure プラットフォームの最新の機能に対応しています。 人の考えやテクノロジは時間の経過と共に変化するため、この記事は、それらの変化が反映されるように定期的に更新されます。

## <a name="define-and-deploy-strong-operational-security-practices"></a>強力で運用可能なセキュリティに関するプラクティスの定義とデプロイ
Azure で運用可能なセキュリティとは、ユーザーが Azure 内のデータ、アプリケーション、その他の資産を保護するために使用できる、サービス、コントロール、機能を指します。 Azure で運用可能なセキュリティは、[セキュリティ開発ライフサイクル (SDL)](https://www.microsoft.com/sdl)、[Microsoft セキュリティ レスポンス センター](https://www.microsoft.com/msrc?rtc=1) プログラム、サイバー セキュリティの脅威状況に対する深い認識など、Microsoft に固有の機能から得られた知識が組み込まれたフレームワークを基盤としています。

## <a name="manage-and-monitor-user-passwords"></a>ユーザー パスワードの管理と監視
次の表に、ユーザー パスワードの管理に関連するベスト プラクティスをいくつか示します。

**ベスト プラクティス**: クラウドで適切なレベルのパスワード保護があることを確認します。   
**詳細**: Microsoft ID プラットフォーム (Azure Active Directory、Active Directory、Microsoft アカウント) のユーザーを対象とした [Microsoft のパスワードのガイダンス](https://www.microsoft.com/research/publication/password-guidance/)にあるガイダンスに従います。

**ベスト プラクティス**: ユーザー アカウントに関連する疑わしい動作を監視します。   
**詳細**: [危険な状態のユーザー](../../active-directory/identity-protection/overview-identity-protection.md)と [危険なサインイン](../../active-directory/identity-protection/overview-identity-protection.md)を、Azure AD セキュリティ レポートを使用して監視します。

**ベスト プラクティス**: 危険度の高いパスワードを自動的に検出して修復します。   
**詳細**: [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) は Azure AD Premium P2 エディションの機能です。この機能を使用すると、以下を行うことができます。

- 組織の ID に影響する潜在的な脆弱性を検出する
- 組織の ID に関連する検出された疑わしいアクションに対する自動応答を構成する
- 疑わしいインシデントを調査し、適切なアクションを実行して解決する

## <a name="receive-incident-notifications-from-microsoft"></a>Microsoft からインシデント通知を受け取る
セキュリティ運用チームが Microsoft からの Azure インシデント通知を必ず受け取るようにしてください。 セキュリティ チームはインシデント通知により侵害された Azure リソースの存在を知ることができるため、潜在的なセキュリティ リスクに素早く対応して修復することができます。

Azure の登録ポータルで、管理者の連絡先情報にセキュリティ操作を通知する詳細を確実に含めることができます。 連絡先情報は、電子メール アドレスと電話番号です。

## <a name="organize-azure-subscriptions-into-management-groups"></a>Azure サブスクリプションを管理グループに整理する
組織に多数のサブスクリプションがある場合は、これらのサブスクリプションのアクセス、ポリシー、およびコンプライアンスを効率的に管理する方法が必要になることがあります。 [Azure 管理グループ](../../governance/management-groups/create-management-group-portal.md)では、サブスクリプションを上回る範囲のレベルが提供されます。 管理グループと呼ばれるコンテナーにサブスクリプションを整理して、管理グループに管理条件を適用できます。 管理グループ内のすべてのサブスクリプションは、管理グループに適用された条件を自動的に継承します。

管理グループとサブスクリプションの柔軟な構造をディレクトリに構築することができます。 各ディレクトリには、ルート管理グループと呼ばれる 1 つの最上位管理グループがあります。 このルート管理グループは階層に組み込まれており、すべての管理グループとサブスクリプションはルート管理グループにまとめられます。 ルート管理グループを使用すると、グローバル ポリシーと Azure ロールの割り当てをディレクトリ レベルで適用できます。

管理グループの使用に関するベスト プラクティスを次にいくつか示します。

**ベスト プラクティス**: ポリシーやアクセス許可などのガバナンス要素が追加されたときに、新しいサブスクリプションが確実に適用されるようにします。   
**詳細**: ルート管理グループを使用して、すべての Azure 資産に適用される企業全体のセキュリティ要素を割り当てます。 要素の例としては、ポリシーやアクセス許可があります。

**ベスト プラクティス**: 各セグメント内における制御とポリシーの一貫性に関するポイントを提供するため、最上位レベルの管理グループをセグメント化戦略と合わせます。   
**詳細**: ルート管理グループの下のセグメントごとに 1 つの管理グループを作成します。 ルートの下に他の管理グループは作成しないでください。

**ベスト プラクティス**: 運用とセキュリティの両方を妨げる混乱を回避するため、管理グループの深さを制限します。   
**詳細**: 階層は、ルートを含めて 3 レベルまでに制限します。

**ベスト プラクティス**: ルート管理グループを使用して企業全体に適用する項目を慎重に選択します。   
**詳細**: ルート管理グループ要素がすべてのリソースにわたって適用される明確な必要性があること、そして影響度が低いことを確実にします。

適切な候補は次のとおりです。

- 明確な事業影響のある規制要件 (たとえば、データ主権に関連する制限)
- 慎重にレビューされた audit 効果のあるポリシーや Azure RBAC アクセス許可割り当てなど、運用に関する潜在的なマイナスの影響がほぼない要件

**ベスト プラクティス**: ルート管理グループに対する企業全体の変更すべてを、適用する前に慎重に計画してテストします (ポリシー、Azure RBAC モデルなど)。   
**詳細**: ルート管理グループ内の変更は、Azure 上のすべてのリソースに影響する可能性があります。 企業全体にわたる一貫性を確保する強力な方法が提供される一方で、エラーや不適切な使用によって実稼働運用にマイナスの影響が発生する可能性があります。 テスト ラボまたは運用パイロットで、ルート管理グループに対するすべての変更をテストします。

## <a name="streamline-environment-creation-with-blueprints"></a>ブループリントの使用による環境の作成の簡素化
[Azure Blueprints](../../governance/blueprints/overview.md) サービスによってクラウド アーキテクトや中央の情報技術グループは、組織の標準、パターン、要件を実装および順守した反復可能な一連の Azure リソースを定義できます。 Azure Blueprint を使用すると、開発チームは一連の組み込みコンポーネントを使用し、組織のコンプライアンスに従ってこれらの環境を作成しているという自信を持って、新しい環境を迅速に構築して立ち上げることができます。

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>予期しない動作変化の記憶域サービスを監視します。
クラウド環境でホストされる分散型アプリケーションの問題の診断およびトラブルシューティングは、従来の環境よりも複雑になる可能性があります。 アプリケーションは、PaaS や IaaS インフラストラクチャ、オンプレミス、モバイル デバイス、これらの環境を組み合わせたものにデプロイできます。 アプリケーションのネットワーク トラフィックがパブリックとプライベート ネットワークを走査可能性があり、アプリケーションが複数の記憶域テクノロジを使用して可能性があります。

アプリケーションが動作 (低速の応答時間) などの予期しない変更を使用するストレージ サービスを継続的に監視する必要があります。 詳細なデータを収集して、問題を徹底的に分析するログを使用します。 監視とログの両方から得られた診断情報を基に、アプリケーションで発生した問題の根本原因を特定できます。 そして、問題をトラブルシューティングし、問題を修復する適切な手順を決定できます。

[Azure Storage Analytics](../../storage/common/storage-analytics.md) では、ログが記録され、Azure ストレージ アカウントのメトリック データを得ることができます。 このデータを使用して、要求のトレース、使用傾向の分析、ストレージ アカウントの問題の診断をすることをお勧めします。

## <a name="prevent-detect-and-respond-to-threats"></a>脅威の防止、検出、および対応
[Azure Security Center](../../security-center/security-center-introduction.md) を使用すると、Azure リソースのセキュリティを高度に視覚化 (および制御) しながら、脅威を回避し、検出し、それらに対応することができます。 これにより、Azure サブスクリプション全体に統合セキュリティの監視とポリシーの管理を提供し、気付かない可能性がある脅威を検出し、さまざまなセキュリティ ソリューションと連動します。

Security Center の Free レベルでは、Azure リソースに対してのみ限定的なセキュリティを提供します。 Standard レベルでは、オンプレミスおよび他のクラウドにこれらの機能を拡張します。 Security Center Standard は、セキュリティの脆弱性の検出と修正、悪意のあるアクティビティをブロックするためのアクセス制御とアプリケーション制御の適用、分析とインテリジェンスを使用して、脅威の検出、攻撃を受けたときのすばやい対応を支援します。 Security Center Standard は最初の 60 日間、無料でお試しいただけます。 [Azure サブスクリプションの Security Center Standard へのアップグレード](../../security-center/security-center-get-started.md)をお勧めします。

Security Center では、すべての Azure リソースのセキュリティの状態を一元的に把握できます。 セキュリティの制御が適切かつ正しく構成されているかを一目で確認し、注意が必要なリソースを素早く特定できます。

Security Center は、包括的な Endpoint Detection and Response (EDR) 機能を提供する [Microsoft Defender Advanced Threat Protection (ATP)](../../security-center/security-center-wdatp.md) とも統合されます。 Microsoft Defender ATP の統合により、異常を見つけることができます。 また、Security Center によって監視されているサーバー エンドポイントでの高度な攻撃を検出して対応することもできます。

ほぼすべてのエンタープライズ組織には、多様なシグナル収集デバイスからログ情報を統合することで新たな脅威を識別することに役立つ、セキュリティ情報およびイベント管理 (SIEM) システムがあります。 ログはその後データ分析システムにより分析され、すべてのログ収集および分析ソリューションで避けることのできないノイズから、"興味を引く" ものが何であるかを識別するのに役立ちます。

[Azure Sentinel](../../sentinel/overview.md) は、スケーラブルでクラウドネイティブ型のセキュリティ情報およびイベント管理 (SIEM) およびセキュリティ オーケストレーション自動応答 (SOAR) ソリューションです。 Azure Sentinel では、アラートの検出、脅威の可視性、予防的な捜索、および脅威への自動対応により、高度なセキュリティ分析と脅威インテリジェンスが提供されます。

脅威に対する保護、検出、および対応に関するベスト プラクティスを次にいくつか示します。

**ベスト プラクティス**: クラウドベースの SIEM を使用して SIEM ソリューションの速度とスケーラビリティを向上させます。   
**詳細**: [Azure Sentinel](../../sentinel/overview.md) の特徴と機能を調査して、現在オンプレミスで使用しているものの機能と比較します。 Azure Sentinel が組織の SIEM の要件を満たす場合は、採用を検討します。

**ベスト プラクティス**: 調査に優先順位を付けることができるよう、最も重大なセキュリティの脆弱性を見つけます。   
**詳細**: [Azure のセキュリティ スコア](../../security-center/secure-score-security-controls.md)をレビューして、Azure Security Center に組み込まれた Azure ポリシーとイニシアティブから導き出される推奨事項を確認します。 これらの推奨事項は、セキュリティ更新プログラム、エンドポイント保護、暗号化、セキュリティ構成、WAF の欠落、インターネットに接続された VM など、最大の危険への対応に役立ちます。

Center for Internet Security (CIS) コントロールを基にしたセキュリティ スコアにより、組織の Azure セキュリティを外部ソースに対してベンチマークすることができます。 外部検証は、チームのセキュリティ戦略を検証し強化することに役立ちます。

**ベスト プラクティス**: マシン、ネットワーク、ストレージおよびデータ サービス、アプリケーションのセキュリティ体制を監視して、潜在的なセキュリティの問題を検出し優先順位を付けます。  
**詳細**: 最も優先順位が高い項目から、Security Center の [セキュリティに関する推奨事項](../../security-center/security-center-recommendations.md)に従います。

**ベスト プラクティス**: Security Center のアラートを、セキュリティ情報およびイベント管理 (SIEM) ソリューションに統合します。   
**詳細**: SIEM を使用している組織のほどんとは、アナリストの応答を必要とするセキュリティ アラートに関する中央情報センターとして SIEM を使用しています。 Security Center によって生成されて処理されたイベントは、Azure Monitor で利用可能なログの 1 つである Azure アクティビティ ログに発行されます。 Azure Monitor では、任意の監視データを SIEM ツールにルーティングするための統合パイプラインが提供されています。 手順については、「[SIEM、SOAR、または IT Service Management ソリューションへのアラートのストリーミング](../../security-center/export-to-siem.md)」を参照してください。 Azure Sentinel を使用している場合は、[Azure Security Center への接続](../../sentinel/connect-azure-security-center.md)に関する記事を参照してください。

**ベスト プラクティス**: Azure ログを SIEM に統合します。   
**詳細**: [データの収集とエクスポートに Azure Monitor](../../azure-monitor/overview.md#integrate-and-export-data) を使用します。 この方法はセキュリティ インシデントの調査を可能にするために重要であり、オンライン ログのリテンションは制限されます。 Azure Sentinel を使用している場合、「[データ ソースの接続](../../sentinel/connect-data-sources.md)」を参照してください。

**ベスト プラクティス**: Endpoint Detection and Response (EDR) 機能を攻撃の調査に統合することで、調査と検出プロセスを高速化し、誤検出を減らします。   
**詳細**: Security Center のセキュリティ ポリシーを使用して、[エンドポイント統合用に Microsoft Defender を有効](../../security-center/security-center-wdatp.md#enabling-the-microsoft-defender-for-endpoint-integration)にします。 脅威の検出とインシデント対応には、Azure Sentinel の使用を検討してください。

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>エンド ツー エンドのシナリオ ベースのネットワーク監視
お客様は、仮想ネットワーク、ExpressRoute、Application Gateway、ロード バランサーなどのネットワーク リソースを組み合わせて Azure にエンド ツー エンド ネットワークを構築します。 各ネットワーク リソースは監視することができます。

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) は地域サービスです。 この診断ツールおよび視覚化ツールを使用して、ネットワーク シナリオ レベルで Azure 内と Azure 間の状態を監視して診断します。

ネットワークの監視と使用可能なツールのベスト プラクティスを次に示します。

**ベスト プラクティス**: パケット キャプチャを使ってリモート ネットワーク監視を自動化する。  
**詳細**: Network Watcher を使用して、VM にログインすることなくネットワークの問題を監視して診断します。 アラートの設定により[パケット キャプチャ](../../network-watcher/network-watcher-alert-triggered-packet-capture.md)をトリガーし、パフォーマンスに関する情報をパケット レベルでリアルタイムに確認可能です。 問題が起きた場合には、詳細に調査してより適切に診断できます。

**ベスト プラクティス**: フロー ログを使用して、ネットワーク トラフィックに関する分析情報を得る。  
**詳細**: [ネットワーク セキュリティ グループのフロー ログ](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)を使うと、ネットワーク トラフィックのパターンをより深く把握できます。 フロー ログの情報をもとに、コンプライアンスに関するデータを収集し、ネットワークのセキュリティ プロファイルを監査、監視できます。

**ベスト プラクティス**: VPN 接続の問題を診断する。  
**詳細**: Network Watcher を使用して、[VPN Gateway と接続に関する最も一般的な問題を診断します](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md)。 問題を識別するだけでなく、詳細なログを使用して、詳しい調査を行うこともできます。

## <a name="secure-deployment-by-using-proven-devops-tools"></a>実証済みの DevOps ツールによるセキュリティ保護されたデプロイ
次の DevOps のベスト プラクティスを使用して、エンタープライズとチームが、生産性と効率性があることを確認します。

**ベスト プラクティス**: サービスのビルドとデプロイを自動化する。  
**詳細**: [コードとしてのインフラストラクチャ](/azure/devops/learn/what-is-infrastructure-as-code)は、モジュール式インフラストラクチャの構築と管理に関連する IT 担当者の日常作業の負担を軽減する、一連の技法とプラクティスです。 これにより、ソフトウェア開発者がアプリケーション コードを構築し保守するように、IT 担当者は最新のサーバー環境を構築、維持できます。

[Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) を使用して、宣言型のテンプレートを使ってアプリケーションをプロビジョニングすることができます。 1 つのテンプレートで、複数のサービスをその依存関係と共にデプロイできます。 アプリケーション ライフサイクルの各ステージで、同じテンプレートを使用してアプリケーションを繰り返しデプロイします。

**ベスト プラクティス**: Azure Web アプリまたはクラウド サービスに自動的にビルドし、デプロイする。  
**詳細**: Azure DevOps Projects を Azure Web アプリまたはクラウド サービスに [自動的にビルドしてデプロイ](/azure/devops/pipelines/index)するように構成できます。 コードをチェックインするたびに、Azure DevOps により、Azure へビルドが実行され、バイナリが自動的にデプロイされます。 パッケージのビルド プロセスは、Visual Studio の Package コマンドに相当し、発行手順は Visual Studio の Publish コマンドに相当します。

**ベスト プラクティス**: リリース管理を自動化します。  
**詳細**: [Azure Pipelines](/azure/devops/pipelines/index) は、複数の段階に分かれたデプロイを自動化し、リリース プロセスを管理するためのソリューションです。 継続的で管理されたデプロイメント パイプラインを作成し、すばやく、簡単に、頻繁にリリースできます。 Azure Pipelines を使用すると、リリース プロセスを自動化し、承認ワークフローを事前定義できます。 オンプレミスまたはクラウドに展開し、必要に応じて拡張、カスタマイズできます。

**ベスト プラクティス**: アプリを起動する前または更新プログラムを運用環境にデプロイする前に、パフォーマンスを確認する。  
**詳細**: 次の目的で、クラウドベースの [ロード テスト](/azure/devops/test/load-test/overview#alternatives)を実行します。

- アプリのパフォーマンスの問題を見つけます。
- デプロイの品質を向上します。
- アプリが常に使用可能であることを確認します。
- アプリが、次の立ち上げ、またはマーケティング キャンペーン時のトラフィックに対応できることを確認します。

[Apache JMeter](https://jmeter.apache.org/) はコミュニティの強力な支持を受けている、無料で人気のオープン ソース ツールです。

**ベスト プラクティス**: アプリケーションのパフォーマンスを監視する。  
**詳細**: [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) は、複数のプラットフォームで使用できる Web 開発者向けの拡張可能なアプリケーション パフォーマンス管理 (APM) サービスです。 Application Insights を使用して、実行中の Web アプリケーションを監視します。 パフォーマンスに異常があると、自動的に検出されます。 組み込まれている分析ツールを使えば、問題を診断し、ユーザーがアプリを使用して実行している操作を把握できます。 Application Insights は、パフォーマンスやユーザビリティを継続的に向上させるうえで役立つように設計されています。

## <a name="mitigate-and-protect-against-ddos"></a>DDoS に対する被害軽減と保護
分散型サービス拒否 (DDoS) は、アプリケーションのリソースを枯渇させようとする攻撃の種類です。 目的は、正当な要求を処理するアプリケーションの可用性と能力に影響を与えることです。 これらの攻撃は、いっそう高度なものになり、規模と影響も大きくなっています。 この攻撃は、インターネット経由で一般に到達可能なすべてのエンドポイントで実行できます。

DDoS に対する回復性を設計しビルドするときは、さまざまな障害モードに対応した計画と設計が必要です。 Azure の DDoS 回復性のサービスをビルドするためのベスト プラクティスを次に示します。

**ベスト プラクティス**: 設計と実装から、デプロイと運用まで、アプリケーションのライフ サイクル全体を通して、セキュリティを優先する必要があります。 アプリケーションには、比較的少量の要求によって大量のリソースを使用し、結果としてサービスを停止させるような、バグが含まれる可能性があります。  
**詳細**: Microsoft Azure で実行されるサービスを保護するには、お客様は、自分のアプリケーションのアーキテクチャをよく理解し、[ソフトウェア品質の 5 つの重要な要素](/azure/architecture/guide/pillars)に注目する必要があります。 お客様は、標準的なトラフィックの量、アプリケーションと他のアプリケーションの間の接続モデル、パブリック インターネットに公開されるサービス エンドポイントについて知っておく必要があります。

アプリケーション自体を対象とするサービス拒否攻撃に対処するのに十分な回復力をアプリケーションに持たせることが、最も重要です。 Azure プラットフォームには、[セキュリティ開発ライフサイクル (Security Development Lifecycle: SDL)](https://www.microsoft.com/sdl) からセキュリティとプライバシーが組み込まれるようになっています。 SDL は、すべての開発段階でセキュリティに対処し、Azure がいっそう安全になるように継続的に更新されることを保証します。

**ベスト プラクティス**: [水平方向にスケーリングする](/azure/architecture/guide/design-principles/scale-out)ようにアプリケーションを設計し、増加した負荷の需要を満たす (特に、DDoS 攻撃の場合)。 アプリケーションがサービスの 1 つのインスタンスに依存する場合は、単一障害点が発生します。 複数のインスタンスをプロビジョニングすると、システムの回復力と拡張性が高まります。  
**詳細**: [Azure App Service](../../app-service/overview.md) の場合は、複数のインスタンスを提供する [App Service プラン](../../app-service/overview-hosting-plans.md)を選択してください。

Azure Cloud Services の場合は、[複数インスタンス](../../cloud-services/cloud-services-choose-me.md)を使用するように各ロールを構成してください。

[Azure Virtual Machines](../../virtual-machines/windows/overview.md) の場合は、VM アーキテクチャに 1 つ以上の VM が含まれていることと、[可用性セット](../../virtual-machines/windows/tutorial-availability-sets.md)に各 VM が含まれていることを確認してください。 自動スケーリング機能には仮想マシン スケール セットを使うことをお勧めします。

**ベスト プラクティス**: アプリケーションのセキュリティ防御を多層化すると、攻撃が成功する可能性が減少します。 Azure プラットフォームの組み込み機能を使って、アプリケーションのセキュリティ保護設計を実装します。  
**詳細**: 攻撃のリスクは、アプリケーションの規模 (攻撃対象領域) と共に大きくなります。 公開されている IP アドレス空間を承認リストを使用して閉鎖し、ロード バランサー ([Azure Load Balancer](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) と [Azure Application Gateway](../../application-gateway/application-gateway-create-probe-portal.md)) を用いて不要にリスニングしているポートを閉じることで、攻撃対象領域を減らすことができます。

[ネットワーク セキュリティ グループ](../../virtual-network/network-security-groups-overview.md)も、攻撃対象領域を軽減する 1 つの手段です。 [サービス タグ](../../virtual-network/network-security-groups-overview.md#service-tags)および[アプリケーション セキュリティ グループ](../../virtual-network/network-security-groups-overview.md#application-security-groups)を使用して、セキュリティ規則作成の複雑さを軽減し、アプリケーションの構造の自然な延長としてネットワーク セキュリティを構成することができます。

お客様は、可能な限り、[仮想ネットワーク](../../virtual-network/virtual-networks-overview.md)に Azure サービスをデプロイする必要があります。 このプラクティスを使用すると、サービス リソースはプライベート IP アドレスを通して通信できます。 仮想ネットワークからの Azure サービス トラフィックは、パブリック IP アドレスを発信元 IP アドレスとして既定で使用します。

[サービス エンドポイント](../../virtual-network/virtual-network-service-endpoints-overview.md)を使用すると、サービス トラフィックは、仮想ネットワークから Azure サービスにアクセスするときに、仮想ネットワークのプライベート アドレスを発信元 IP アドレスとして使用するように切り替わります。

Azure 内のリソースと供に、お客様のオンプレミスのリソースが攻撃されることがよくあります。 オンプレミスの環境を Azure に接続している場合は、オンプレミスのリソースのパブリック インターネットへの露出を最小限にします。

Azure には、ネットワーク攻撃からの保護を提供する 2 つの DDoS [サービス オファリング](../../ddos-protection/ddos-protection-overview.md)があります。

- 基本保護は、追加コストなしに、既定で Azure に統合されます。 グローバルにデプロイされる Azure ネットワークのスケールと容量が、常時有効なトラフィック監視とリアルタイムのリスク軽減によって、一般的なネットワーク層攻撃からの保護を提供します。 基本保護では、ユーザーによる構成やアプリケーションの変更は必要なく、Azure DNS などの PaaS サービスを含むすべての Azure サービスを保護できます。
- 標準保護は、ネットワーク攻撃に対する高度な DDoS 軽減機能を提供します。 この機能は、お客様固有の Azure リソースを保護するために自動的に調整されます。 保護は、仮想ネットワークの作成時に簡単に有効にできます。 作成の後で行うこともでき、アプリケーションまたはリソースを変更する必要はありません。

## <a name="enable-azure-policy"></a>Azure Policy の有効化
[Azure Policy](../../governance/policy/overview.md) は、ポリシーの作成、割り当て、管理に使用する Azure のサービスです。 これらのポリシーにより、リソースにルールと効果が適用されて、それらのリソースが会社の標準とサービス レベル アグリーメントに準拠した状態に保たれます。 Azure Policy では、割り当て済みのポリシーでリソースの非準拠を評価することによって、このニーズが満たされます。

Azure Policy を有効にして、組織の明文化されたポリシーを監視し実施します。 これにより、ハイブリッド クラウド ワークロードのセキュリティ ポリシーを一元的に管理することで、会社や規制のセキュリティ要件に確実に準拠できます。 [コンプライアンスを強制するポリシーの作成と管理](../../governance/policy/tutorials/create-and-manage.md)方法について確認してください。 ポリシーの要素の概要については、「[Azure Policy の定義の構造](../../governance/policy/concepts/definition-structure.md)」を参照してください。

Azure Policy の採用後に従うセキュリティのベストプラクティスを次にいくつか示します。

**ベスト プラクティス**: ポリシーにより、いくつかの種類の効果をサポートします。 これらについては、「[Azure Policy の定義の構造](../../governance/policy/concepts/definition-structure.md#policy-rule)」で確認できます。 **deny** 効果と **remediate** 効果によってビジネス業務にマイナスの影響がある可能性があるため、**audit** 効果から開始して、ポリシーからのマイナスの影響のリスクを抑えます。   
**詳細**: [audit モードでポリシーのデプロイを開始](../../governance/policy/concepts/definition-structure.md#policy-rule)し、その後 **deny** または **remediate** に進みます。 **deny** または **remediate** に移る前に、audit 効果の結果をテストしレビューします。

詳細については、「[コンプライアンスを強制するポリシーの作成と管理](../../governance/policy/tutorials/create-and-manage.md)」を参照してください。

**ベスト プラクティス**: ポリシー違反の監視を担当するロールを特定し、適切な修復アクションが確実に迅速に行われるようにします。   
**詳細**: 割り当てられたロールに、[Azure portal](../../governance/policy/how-to/get-compliance-data.md#portal) または [コマンド ライン](../../governance/policy/how-to/get-compliance-data.md#command-line)を使用してコンプライアンスを監視させます。

**ベスト プラクティス**: Azure Policy は、組織の明文化されたポリシーを技術的に表したものです。 混乱を減らし、一貫性を高めるため、すべての Azure Policy 定義を組織のポリシーにマッピングします。   
**詳細**: [ポリシーの定義](../../governance/policy/concepts/definition-structure.md#display-name-and-description)内または [イニシアティブ定義](../../governance/policy/concepts/initiative-definition-structure.md#metadata)の説明内に組織のポリシーへの参照を追加することにより、組織のドキュメント内または Azure Policy の定義自体にマッピングをドキュメント化します。

## <a name="monitor-azure-ad-risk-reports"></a>Azure AD のリスク レポートの監視
ほとんどのセキュリティ侵害は、攻撃者がユーザーの ID を盗むことにより環境にアクセスできるようになると発生します。 侵害された ID を検出するのは簡単な作業ではありません。 Azure AD では、アダプティブ機械学習アルゴリズムとヒューリスティックを使用して、ユーザー アカウントに関連する疑わしいアクションを検出します。 検出された疑わしいアクションはそれぞれ、[リスク検出](../../active-directory/identity-protection/overview-identity-protection.md)と呼ばれるレコードに格納されます。 リスク検出は、Azure AD のセキュリティ レポートに記録されます。 詳細については、[危険な状態のユーザー セキュリティ レポート](../../active-directory/identity-protection/overview-identity-protection.md)に関する記事、および[リスクの高いサインイン セキュリティ レポート](../../active-directory/identity-protection/overview-identity-protection.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ
Azure を使用してクラウド ソリューションを設計、デプロイ、管理するときに使用するセキュリティのベスト プラクティスの詳細については、「[Azure セキュリティのベスト プラクティスとパターン](best-practices-and-patterns.md)」を参照してください。

Azure のセキュリティとそれに関連する Microsoft サービスの一般情報については、以下のリソースを参照してください。
* [Azure セキュリティ チーム ブログ](/archive/blogs/azuresecurity/) – Azure のセキュリティに関する最新情報を提供しています。
* [Microsoft セキュリティ レスポンス センター](https://technet.microsoft.com/library/dn440717.aspx) - このサイトでは、Azure に関する問題を含め、マイクロソフトのセキュリティの脆弱性を報告できます。メールの場合は、secure@microsoft.com 宛に報告してください。