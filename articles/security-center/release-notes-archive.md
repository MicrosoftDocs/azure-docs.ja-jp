---
title: Azure Security Center の最新情報のアーカイブ
description: 6 か月前以前の、Azure Security Center の新機能と変更点の説明。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2020
ms.author: memildin
ms.openlocfilehash: d7ac2777ffd500082bd0b8a9700d53bffd9d6931
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2020
ms.locfileid: "89177314"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Azure Security Center の最新情報のアーカイブ

「[What's new in Azure Security Center? (Azure Security Center の最新情報)](release-notes.md)」という主要リリース ノート ページには、過去 6 か月間の更新情報が含まれていますが、このページにはそれ以前の項目が含まれています。

このページでは、以下に関する情報を提供します。

- 新機能
- バグの修正
- 非推奨の機能


## <a name="february-2020"></a>2020 年 2 月

### <a name="fileless-attack-detection-for-linux-preview"></a>Linux 向けのファイルレス攻撃検出 (プレビュー)

検出を回避するためにステルス性の高い方法を利用する攻撃者が増えているので、Azure Security Center では、Windows 向けに加えて Linux 向けのファイルレス攻撃検出を拡張しています。 ファイルレス攻撃では、ソフトウェアの脆弱性が悪用されて、悪意のあるペイロードが正常なシステム プロセスに挿入され、メモリ内に隠されます。 これらの手法には以下のようなものがあります。

- ディスク上のマルウェアの痕跡を最小化または排除する
- ディスクベースのマルウェア スキャン ソリューションによる検出の可能性を大幅に下げる

この脅威に対処するために、Azure Security Center では 2018 年 10 月にファイルレス攻撃検出をリリースしました。そしてこのたび、ファイルレス攻撃検出は Linux にも拡張されました。 



## <a name="january-2020"></a>2020 年 1 月

### <a name="enhanced-secure-score-preview"></a>強化されたセキュア スコア (プレビュー)

Azure Security Center のセキュア スコア機能の強化バージョンが、プレビュー版で利用できるようになりました。 このバージョンでは、複数の推奨事項が、脆弱な攻撃対象領域をより正確に反映するセキュリティ コントロールにグループ化されています (例: 管理ポートへのアクセスの制限)。

プレビュー段階でセキュア スコアの変更に慣れていただけるほか、ご自分の環境のセキュリティを高めるのに役立つその他の修復機能をご確認いただけます。

詳細については、[強化されたセキュア スコア (プレビュー)](secure-score-security-controls.md) に関するページを参照してください。



## <a name="november-2019"></a>2019 年 11 月

11 月の更新プログラムには次のものが含まれます。
 - [北米リージョンで Azure Key Vault 用 Threat Protection (プレビュー)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [Azure Storage 用 Threat Protection にマルウェア評価スクリーニングを追加](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Logic Apps を使用したワークフローの自動化 (プレビュー)](#workflow-automation-with-logic-apps-preview)
 - [大量のリソースのクイック修正の一般提供を開始](#quick-fix-for-bulk-resources-generally-available)
 - [コンテナー イメージの脆弱性スキャン (プレビュー)](#scan-container-images-for-vulnerabilities-preview)
 - [規制コンプライアンス標準の追加 (プレビュー)](#additional-regulatory-compliance-standards-preview)
 - [Azure Kubernetes Service 用 Threat Protection (プレビュー)](#threat-protection-for-azure-kubernetes-service-preview)
 - [仮想マシンの脆弱性評価 (プレビュー)](#virtual-machine-vulnerability-assessment-preview)
 - [Azure Virtual Machines の SQL Server 向け Advanced Data Security (プレビュー)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [カスタム ポリシーのサポート (プレビュー)](#support-for-custom-policies-preview)
 - [コミュニティおよびパートナー向けのプラットフォームで Azure Security Center のカバレッジを拡大](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [推奨事項とアラートのエクスポートによる高度な統合 (プレビュー)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [Windows Admin Center から Security Center へのオンプレミス サーバーのオンボード (プレビュー)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>北米リージョンで Azure Key Vault 用 Threat Protection (プレビュー)

Azure Key Vault は、キー、シークレット、暗号化キー、ポリシーをクラウドで一元管理する機能を提供することによって、データを保護し、クラウド アプリケーションのパフォーマンスを向上させる非常に重要なサービスです。 Azure Key Vault には、機密性の高い、ビジネスに不可欠なデータが格納されているため、キー コンテナーとそこに格納されるデータの最大限のセキュリティが必要となります。

Azure Security Center による Azure Key Vault 用 Threat Protection のサポートにより、キー コンテナーへのアクセスやキー コンテナーの悪用を試みる、害を及ぼす可能性のある通常とは異なる試行を検出する、セキュリティ インテリジェンスのレイヤーが追加されます。 この新しい保護レイヤーにより、お客様は、セキュリティ専門家でなくてもキー コンテナーに対する脅威に対処することが可能となり、セキュリティ監視システムを管理できるようになります。 この機能は、北米リージョンでパブリック プレビュー段階にあります。


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Azure Storage 用 Threat Protection にマルウェア評価スクリーニングを追加

Azure Storage 用 Threat Protection は、Microsoft 脅威インテリジェンスを利用した新しい検出機能を提供します。これらの機能では、ハッシュ評価分析を使用して Azure Storage へのマルウェアのアップロードを検出したり、アクティブな Tor 出口ノード (匿名化プロキシ) からの不審なアクセスを検出したりすることができます。 Azure Security Center を使用して、ストレージ アカウント全体で検出されたマルウェアを表示できるようになりました。


### <a name="workflow-automation-with-logic-apps-preview"></a>Logic Apps を使用したワークフローの自動化 (プレビュー)

セキュリティと IT/運用が一元管理されている組織では、環境で不一致が見つかった場合に組織内で必要なアクションを実行する内部ワークフロー プロセスを実装しています。 多くの場合、これらのワークフローは反復可能なプロセスであるため、自動化によって組織内のプロセスを大幅に効率化できます。

このたび、Azure Logic Apps を利用して自動化構成を作成し、推奨事項やアラートなど、ASC の特定の結果に基づいてそれらを自動的にトリガーするポリシーを作成できる新しい機能が Security Center に導入されます。 Azure Logic App は、Logic App コネクタの広範なコミュニティによってサポートされているカスタム アクションを実行するように構成することも、メールの送信や ServiceNow™ チケットのオープンなど、Security Center が提供するテンプレートのいずれかを使用するように構成することもできます。

ワークフローを実行するための Security Center の自動および手動機能の詳細については、「[ワークフローの自動化](workflow-automation.md)」をご覧ください。

Logic Apps の作成方法については、[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) に関する記事をご覧ください。


### <a name="quick-fix-for-bulk-resources-generally-available"></a>大量のリソースのクイック修正の一般提供を開始

セキュリティ スコアの一部として、ユーザーには多くのタスクが示されるため、大規模なフリートで問題を効果的に修復するのが困難になる可能性があります。

セキュリティの構成の誤りを簡単に修復し、大量のリソースに関する推奨事項をすばやく修復できるようにして、セキュリティ スコアを向上させるには、クイック修正を使用します。

この操作により、ユーザーは修復を適用するリソースを選択し、ユーザーに代わって設定を構成する修復アクションを起動できます。

このたび、Security Center の推奨事項ページの一部として、クイック修正の一般提供が開始されました。

クイック修正が有効になっている推奨事項については、[セキュリティの推奨事項のリファレンス ガイド](recommendations-reference.md)をご覧ください。


### <a name="scan-container-images-for-vulnerabilities-preview"></a>コンテナー イメージの脆弱性スキャン (プレビュー)

Azure Security Center で、Azure Container Registry にあるコンテナー イメージの脆弱性をスキャンできるようになりました。

イメージのスキャンでは、コンテナー イメージ ファイルを解析し、(Qualys を利用して) 既知の脆弱性が存在するかどうかが確認されます。

スキャン自体は、新しいコンテナー イメージを Azure Container Registry にプッシュすると自動的にトリガーされます。 検出された脆弱性は Security Center の推奨事項として表示され、修正プログラムを適用してそれらの脆弱性による攻撃対象領域を削減する方法に関する情報と共に Azure セキュリティ スコアに含められます。


### <a name="additional-regulatory-compliance-standards-preview"></a>規制コンプライアンス標準の追加 (プレビュー)

規制コンプライアンス ダッシュボードは、Security Center の評価に基づいて、コンプライアンス体制に関する分析情報を提供します。 ダッシュボードでは、お使いの環境が特定の規制基準や業界ベンチマークで指定されたコントロールと要件にどのように準拠しているかが示され、これらの要件に対処する方法に関する規範的な推奨事項が提供されます。

これまで、規制コンプライアンス ダッシュボードでは、Azure CIS 1.1.0、PCI-DSS、ISO 27001、SOC-TSP の 4 つの組み込み標準がサポートされていました。 このたび、Microsoft は、サポート対象となる追加の標準として、NIST SP 800-53 R4、SWIFT CSP CSCF v2020、Canada Federal PBMM、UK Official および UK NHS のパブリック プレビュー リリースを発表しました。 また、Azure CIS 1.1.0 の更新バージョンもリリースし、この標準のより多くのコントロールに対応して拡張性を強化します。

規制コンプライアンス ダッシュボードでの標準セットのカスタイマイズの詳細については、[こちら](update-regulatory-compliance-packages.md)をご覧ください。


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Azure Kubernetes Service 用 Threat Protection (プレビュー)

Kubernetes は、ソフトウェアをクラウドにデプロイして管理するための新しい標準になりつつあります。 Kubernetes の豊富な経験を持つ人はほとんどおらず、多くの人が一般的なエンジニアリングと管理にのみ注目し、セキュリティ面を見落としています。 コンテナーに焦点を合わせた攻撃対象領域が無防備な状態で攻撃者にさらされないように、Kubernetes 環境のセキュリティを慎重に構成する必要があります。 Security Center では、コンテナー領域でのサポートを、Azure で最も急成長しているサービスの 1 つである Azure Kubernetes Service (AKS) まで拡大しています。

このパブリック プレビュー リリースの新しい機能は次のとおりです。

- **検出と可視化** - Security Center の登録済みサブスクリプション内のマネージド AKS インスタンスの継続的な検出。
- **セキュリティ スコアの推奨事項** - お客様のセキュリティ スコアの一部として、AKS のセキュリティのベスト プラクティスへの準拠を支援する実用的な項目 (例: "ロールベースのアクセス制御を使用して、Kubernetes Service クラスターへのアクセスを制限する必要があります")。
- **脅威の検出** - ホストおよびクラスターベースの分析 (例: "特権コンテナーが検出されました")。


### <a name="virtual-machine-vulnerability-assessment-preview"></a>仮想マシンの脆弱性評価 (プレビュー)

多くの場合、仮想マシンにインストールされているアプリケーションには、仮想マシンの侵害につながるおそれのある脆弱性が存在する可能性があります。 Microsoft は、Security Center Standard レベルに、追加料金なしで仮想マシンの脆弱性評価を組み込むことを発表しました。 Qualys を利用した脆弱性評価 (パブリック プレビュー) を使用すると、仮想マシンにインストールされているすべてのアプリケーションを継続的にスキャンして脆弱なアプリケーションを検出し、Security Center ポータルのエクスペリエンスに結果を表示できます。 Security Center がすべてのデプロイ操作を処理するため、ユーザーが追加の作業を行う必要はありません。 今後、お客様固有のビジネス ニーズに対応する脆弱性評価オプションを提供する予定です。

Azure Virtual Machines の脆弱性評価の詳細については、[こちら](deploy-vulnerability-assessment-vm.md)をご覧ください。


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Azure Virtual Machines の SQL Server 向け Advanced Data Security (プレビュー)

IaaS VM 上で実行されている SQL DB の脅威からの保護と脆弱性評価の、Azure Security Center によるサポートのプレビューが開始されました。

[脆弱性評価](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)では、データベースの潜在的な脆弱性を検出、追跡、修復できるサービスを簡単に構成できます。 Azure セキュリティ スコアの一部としてセキュリティ体制が可視化され、セキュリティの問題を解決してデータベースの防御機能を強化するための手順が含まれます。

[Advanced Threat Protection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) では、SQL Server へのアクセスや SQL Server の悪用を試みる、害を及ぼす可能性のある通常とは異なる試行を示す異常なアクティビティが検出されます。 データベースでの不審なアクティビティを継続的に監視し、異常なデータベース アクセス パターンに対してアクション指向のセキュリティ通知を提供します。 このようなアラートからは、不審なアクティビティの詳細と、脅威の調査や危険性の軽減のために推奨されるアクションが提示されます。


### <a name="support-for-custom-policies-preview"></a>カスタム ポリシーのサポート (プレビュー)

Azure Security Center でカスタム ポリシーがサポートされるようになりました (プレビュー)。

お客様は、Azure Policy で作成したポリシーに基づく独自のセキュリティ評価によって、Security Center の現在のセキュリティ評価の対象範囲を拡張することを求めてきました。 カスタム ポリシーのサポートにより、これが可能になりました。

これらの新しいポリシーは、Security Center の推奨事項エクスペリエンス、セキュリティ スコア、規制コンプライアンス標準ダッシュボードに含まれるようになります。 カスタム ポリシーのサポートにより、Azure Policy でカスタム イニシアチブを作成し、Security Center にポリシーとして追加して、推奨事項として表示できるようになりました。


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>コミュニティおよびパートナー向けのプラットフォームで Azure Security Center のカバレッジを拡大

Security Center を使用して、Microsoft だけでなく、Check Point、Tenable、CyberArk などのパートナーの既存のソリューションからも推奨事項を受け取ることができます。今後、さらに多くの統合が提供される予定です。  Security Center のシンプルなオンボード フローにより、既存のソリューションを Security Center に接続することで、セキュリティ体制に関する推奨事項を 1 か所で表示し、統合レポートを実行することが可能になります。組み込みの推奨事項とパートナーの推奨事項の両方に対して、Security Center のすべての機能を活用することもできます。 また、Security Center の推奨事項をパートナー製品にエクスポートすることもできます。

Microsoft インテリジェント セキュリティ アソシエーションの詳細については、[こちら](https://www.microsoft.com/security/partnerships/intelligent-security-association)をご覧ください。



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>推奨事項とアラートのエクスポートによる高度な統合 (プレビュー)

Security Center 上でエンタープライズ レベルのシナリオに対応するために、Azure portal または API 以外の場所でも Security Center のアラートと推奨事項を使用できるようになりました。 これらは、イベント ハブおよび Log Analytics ワークスペースに直接エクスポートできます。 これらの新しい機能を使用して作成できるワークフローは次のとおりです。

- Log Analytics ワークスペースにエクスポートすることにより、PowerBI でカスタム ダッシュボードを作成できます。
- イベント ハブにエクスポートすることにより、Security Center のアラートと推奨事項をサード パーティの SIEM、リアルタイムのサード パーティ ソリューション、または Azure Data Explorer にエクスポートできるようになります。


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Windows Admin Center から Security Center へのオンプレミス サーバーのオンボード (プレビュー)

Windows Admin Center は、Azure にデプロイされていない Windows サーバー用の管理ポータルであり、バックアップやシステム更新などの Azure 管理機能を提供します。 最近、これらの非 Azure サーバーをオンボードして、Windows Admin Center のエクスペリエンスから直接 ASC で保護する機能が追加されました。

この新しいエクスペリエンスにより、ユーザーは WAC サーバーを Azure Security Center にオンボードし、Windows Admin Center のエクスペリエンスでセキュリティのアラートや推奨事項を直接表示できるようになります。


## <a name="september-2019"></a>2019 年 9 月

9 月の更新プログラムには次のものが含まれます。

 - [適応型アプリケーション制御のルール管理の改善](#managing-rules-with-adaptive-application-controls-improvements)
 - [Azure Policy を使用してコンテナーのセキュリティの推奨事項を制御](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>適応型アプリケーション制御のルール管理の改善

適応型アプリケーション制御を使用した仮想マシンのルール管理のエクスペリエンスが改善されました。 Azure Security Center の適応型アプリケーション制御を使用すると、仮想マシン上で実行できるアプリケーションを制御できます。 ルール管理の全般的な改善に加え、新たに追加される機能により、新しいルールを追加するときに保護するファイルの種類を制御できるようになります。

適応型アプリケーション制御の詳細については、[こちら](security-center-adaptive-application.md)をご覧ください。


### <a name="control-container-security-recommendation-using-azure-policy"></a>Azure Policy を使用してコンテナーのセキュリティの推奨事項を制御

コンテナーのセキュリティの脆弱性を修復するための Azure Security Center の推奨事項を、Azure Policy を使用して有効または無効にすることができるようになりました。

有効なセキュリティ ポリシーを表示するには、Security Center から [セキュリティ ポリシー] ページを開きます。


## <a name="august-2019"></a>2019 年 8 月

8 月の更新プログラムには次のものが含まれます。

 - [Azure Firewall 用 Just-In-Time (JIT) VM アクセス](#just-in-time-jit-vm-access-for-azure-firewall)
 - [セキュリティ体制を強化するシングル クリック修復 (プレビュー)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [テナント間の管理](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Azure Firewall 用 Just-In-Time (JIT) VM アクセス 

Azure Firewall 用 Just-In-Time (JIT) VM アクセスの一般提供が開始されました。 これを使用することで、NSG で保護された環境に加え、Azure Firewall で保護された環境もセキュリティで保護できます。

JIT VM アクセスでは、NSG と Azure Firewall の規則を使用して、必要な場合にのみ、VM への制御されたアクセスを提供することで、ネットワーク帯域幅消費型攻撃にさらされるリスクを低減します。

VM で JIT を有効にする場合は、保護するポート、ポートを開放しておく時間、これらのポートにアクセスできる承認済み IP アドレスを指定したポリシーを作成します。 このポリシーは、ユーザーがアクセスを要求したときに実行できる操作を制御するのに役立ちます。

要求は Azure アクティビティ ログに記録されるので、アクセスの監視と監査を簡単に行うことができます。 Just-In-Time ページは、JIT が有効になっている既存の VM や JIT が推奨される VM をすばやく特定する際にも役立ちます。

Azure Firewall の詳細については、[こちら](https://docs.microsoft.com/azure/firewall/overview)をご覧ください。


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>セキュリティ体制を強化するシングル クリック修復 (プレビュー)

セキュリティ スコアは、ワークロードのセキュリティに対する姿勢を評価するのに役立つツールです。 セキュリティの推奨事項が見直され、優先順位が自動的に示されるので、どの推奨事項を最初に実行すべきかがわかります。 これは、最も重大なセキュリティの脆弱性を見つけて、調査に優先順位を付けるのに役立ちます。

セキュリティの構成の誤りを簡単に修復し、セキュリティ スコアを速やかに向上させるために、大量のリソースに関する推奨事項をシングル クリックで修復できる新しい機能が追加されました。

この操作により、ユーザーは修復を適用するリソースを選択し、ユーザーに代わって設定を構成する修復アクションを起動できます。

クイック修正が有効になっている推奨事項については、[セキュリティの推奨事項のリファレンス ガイド](recommendations-reference.md)をご覧ください。


### <a name="cross-tenant-management"></a>テナント間の管理

Security Center では、Azure Lighthouse の一部として、テナント間の管理のシナリオがサポートされるようになりました。 これにより、Security Center で複数のテナントのセキュリティ体制を可視化し、管理できるようになります。 

テナント間の管理エクスペリエンスの詳細については、[こちら](security-center-cross-tenant-management.md)をご覧ください。


## <a name="july-2019"></a>2019 年 7 月

### <a name="updates-to-network-recommendations"></a>ネットワークの推奨事項の更新

Azure Security Center (ASC) で新しいネットワークの推奨事項が導入され、既存の推奨事項が改善されました。 Security Center を使用することで、リソースのネットワーク保護をさらに強化できます。 

ネットワークの推奨事項の詳細については、[こちら](recommendations-reference.md#recs-network)をご覧ください。


## <a name="june-2019"></a>2019 年 6 月

### <a name="adaptive-network-hardening---generally-available"></a>アダプティブ ネットワークのセキュリティ強化機能 - 一般提供の開始

パブリック クラウドで実行されているワークロードの最大の攻撃対象領域の 1 つは、パブリック インターネットとの間の接続です。 Azure ワークロードを必要なソース範囲でのみ利用できるようにするために、どのネットワーク セキュリティ グループ (NSG) 規則を設定すればよいかをお客様が把握するのは困難です。 この機能を使用すると、Security Center が Azure ワークロードのネットワーク トラフィックと接続のパターンを学習し、インターネットに接続する仮想マシンに対して、NSG 規則の推奨事項を提供します。 これにより、お客様は、ネットワーク アクセス ポリシーをより適切に構成し、攻撃にさらされるリスクを抑えることができます。 

アダプティブ ネットワークのセキュリティ強化機能の詳細については、[こちら](security-center-adaptive-network-hardening.md)をご覧ください。
