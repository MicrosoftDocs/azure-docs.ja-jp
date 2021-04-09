---
title: Cloud Shell 用の Azure セキュリティ ベースライン
description: Cloud Shell セキュリティ ベースラインによって、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ce8c5107ebb86106537ffce21f852d8b67ecbd78
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703475"
---
# <a name="azure-security-baseline-for-cloud-shell"></a>Cloud Shell 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインによって、[Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview-v1.md) のガイダンスが Cloud Shell に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。
内容は、Cloud Shell に適用される Azure セキュリティ ベンチマークと関連ガイダンスで定義されている **セキュリティ コントロール** によってグループ化されています。 Cloud Shell に適用できない **コントロール** は、除外されています。

 
Cloud Shell を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Cloud Shell セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**:顧客は Azure Cloud Shell を自分が所有する仮想ネットワークにデプロイする場合があります。

顧客が所有する仮想ネットワークに Azure Cloud Shell をデプロイする場合は、既存の仮想ネットワークを作成または使用する必要があります。 選択した仮想ネットワークで、ネットワーク セキュリティ グループがサブネットに適用され、アプリケーションの信頼されたポートとソースに固有のネットワーク アクセス制御が構成されていることを確認します。

- [Azure 仮想ネットワークに Cloud Shell をデプロイする](private-vnet.md)

- [セキュリティ規則を使用してネットワーク セキュリティ グループを作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall をデプロイして構成する方法](../firewall/tutorial-firewall-deploy-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**:Azure Cloud Shell は、Azure portal へのアクセスに使用されるときと同じ認可を使用するブラウザー ベースのコマンドライン エクスペリエンスです。この場合、Azure portal への SSO を行えば、Cloud Shell でも認証されます。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Cloud Shell は、Azure portal へのアクセスに使用されるときと同じ認可を使用するブラウザーベースのコマンドライン エクスペリエンスです。この場合、Azure portal に接続するために必要な多要素認証が Cloud Shell でも必要になります。

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「脆弱性の管理](../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**:Azure Cloud Shell は、クラウド リソースの対話型管理に使用されるブラウザーベースのコマンドライン エクスペリエンスです。  各顧客のコンテナーがエフェメラルである場合は、セッションごとに新しいコンテナーが使用されます。  コンテナー イメージは、Cloud Shell チームによって監視および更新されます。

Azure Cloud Shell を使用すると、組織のニーズに応じて、独自のツールやソフトウェアを独自のイメージにインストールできます。

顧客は環境内で実行されているソフトウェアに対して、自動化された脆弱性スキャン ツールを実行する責任があります。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3:サード パーティ ソフトウェア タイトル用の自動化された修正プログラム管理ソリューションをデプロイする

**ガイダンス**:(適用なし) Azure Cloud Shell は、クラウド リソースの対話型管理に使用されるブラウザーベースのコマンドライン エクスペリエンスです。  各顧客のコンテナーがエフェメラルである場合は、セッションごとに新しいコンテナーが使用されます。  コンテナー イメージは、Cloud Shell チームによって監視および更新されます。

Azure Cloud Shell を使用すると、組織のニーズに応じて、独自のツールやソフトウェアを独自のイメージにインストールできます。

顧客は環境内で実行されているソフトウェア修正プログラムを管理する責任があります。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**:Azure Cloud Shell は、クラウド リソースの対話型管理に使用されるブラウザーベースのコマンドライン エクスペリエンスです。  各顧客のコンテナーがエフェメラルである場合は、セッションごとに新しいコンテナーが使用されます。  コンテナー イメージは、Cloud Shell チームによって監視および更新されます。

Azure Cloud Shell を使用すると、組織のニーズに応じて、独自のツールやソフトウェアを独自のイメージにインストールできます。

顧客はソフトウェアの脆弱性スキャンで検出された脆弱性を修復する責任があります。 スキャン結果を一定の間隔でエクスポートして、前回のスキャンと結果を比較し、脆弱性が修復されていることを確認します。 Azure Security Center によって提案された脆弱性管理の推奨事項を使用する場合は、選択したソリューションのポータルに切り替えてスキャン データの履歴を表示できます。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**:Azure Cloud Shell は、クラウド リソースの対話型管理に使用されるブラウザーベースのコマンドライン エクスペリエンスです。  各顧客のコンテナーがエフェメラルである場合は、セッションごとに新しいコンテナーが使用されます。  コンテナー イメージは、Cloud Shell チームによって監視および更新されます。

Azure Cloud Shell を使用すると、組織のニーズに応じて、独自のツールやソフトウェアを独自のイメージにインストールできます。

顧客はソフトウェアの脆弱性スキャンで検出された脆弱性を修復する責任があります。  一般的なリスク スコアリング プログラム (例: Common Vulnerability Scoring System) またはサードパーティのスキャンツールによって提供された既定のリスク評価を使用します。 

- [NIST の出版物 -- 一般的な脆弱性のスコアリング システム](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**:Azure Cloud Shell は、クラウド リソースの対話型管理に使用されるブラウザーベースのコマンドライン エクスペリエンスです。  各顧客のコンテナーがエフェメラルである場合は、セッションごとに新しいコンテナーが使用されます。  コンテナー イメージとツールは、Cloud Shell チームによって監視および更新されます。  顧客は組織のニーズに応じて、独自のツールを独自のイメージにインストールできます。ツールのインストール時に、`sudo` のアクセス許可は必要ありません。

顧客は組織のニーズに応じて、Azure Cloud Shell でインストールされる承認済みソフトウェアのインベントリを作成することが推奨されています。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**:Azure Cloud Shell は、顧客が所有する資産を使用しない無料のサービスです。  コンテナー イメージとツールは、Cloud Shell チームによって監視および更新されます。 

Azure Cloud Shell を使用すると、組織のニーズに応じて、独自のツールやソフトウェアを独自のイメージにインストールできます。

顧客は環境内で実行されているソフトウェア アプリケーションを監視して、組織のポリシーに従って承認されるようにする責任があります。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**:Azure Cloud Shell は、顧客が所有する資産を使用しない無料のサービスです。  コンテナー イメージとツールは、Cloud Shell チームによって監視および更新されます。 

Azure Cloud Shell を使用すると、組織のニーズに応じて、独自のツールやソフトウェアを独自のイメージにインストールできます。

顧客は環境内で実行されているソフトウェア アプリケーションを監視して、組織のポリシーに従って未承認のソフトウェアが管理されていることを確認する責任があります。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**:Azure Cloud Shell は、顧客が所有する資産を使用しない無料のサービスです。  コンテナー イメージとツールは、Cloud Shell チームによって監視および更新されます。  顧客が特定のツールを削除できない場合もあります。

Azure Cloud Shell を使用すると、組織のニーズに応じて、独自のツールやアプリケーションを独自のイメージにインストールできます。

顧客は環境内で実行されているアプリケーションを監視して、組織のポリシーに従って承認されるようにする責任があります。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10:承認されたソフトウェア タイトルのインベントリを管理する

**ガイダンス**:(適用なし) Azure Cloud Shell は、クラウド リソースの対話型管理に使用されるブラウザーベースのコマンドライン エクスペリエンスです。  各顧客のコンテナーがエフェメラルである場合は、セッションごとに新しいコンテナーが使用されます。  コンテナー イメージは、Cloud Shell チームによって監視および更新されます。

Azure Cloud Shell を使用すると、組織のニーズに応じて、独自のツールやソフトウェアを独自のイメージにインストールできます。

顧客は環境内で実行されている承認済みソフトウェアのインベントリを保守して、組織のポリシーに従ってソフトウェアが承認されるようにする責任があります。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12:コンピューティング リソースでスクリプトを実行するユーザーの機能を制限する

**ガイダンス**:Azure Cloud Shell は、クラウド リソースの対話型管理に使用されるブラウザーベースのコマンドライン エクスペリエンスです。  Cloud Shell 内で実行されるアクションは、ローカル環境で実行されるツールまたは言語で実行されるアクションと同様に機能します。  各ツールや言語で実行されるアクションは、制限される必要があります。顧客が Cloud Shell へのアクセスを制限したり、ユーザーが使用できるものを制限したりすることはできません。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**:Azure Cloud Shell は、顧客の仮想ネットワークで分離できます。

- [Azure 仮想ネットワークに Cloud Shell をデプロイする](private-vnet.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**:Cloud Shell でスクリプトを実行したり、作成したり、Cloud Shell 環境にアップロードしたりすることができます。  資格情報を Azure Key Vault に移動することが推奨されています。

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、[Azure セキュリティ ベンチマークの「マルウェアからの防御](../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**:Azure Cloud Shell は、クラウド リソースの対話型管理に使用されるブラウザーベースのコマンドライン エクスペリエンスです。  各顧客のコンテナーがエフェメラルである場合は、セッションごとに新しいコンテナーが使用されます。  コンテナー イメージとツールは、Cloud Shell チームによって監視および更新されます。  顧客は組織のニーズに応じて、独自のツールを独自のイメージにインストールできます。ツールのインストール時に、`sudo` のアクセス許可は必要ありません。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**:Azure Cloud Shell は、クラウド リソースの対話型管理に使用されるブラウザーベースのコマンドライン エクスペリエンスです。  各顧客のコンテナーがエフェメラルである場合は、セッションごとに新しいコンテナーが使用されます。  コンテナー イメージとツールは、Cloud Shell チームによって監視および更新されます。  顧客は組織のニーズに応じて、独自のツールを独自のイメージにインストールできます。ツールのインストール時に、`sudo` のアクセス許可は必要ありません。

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。

- [Azure Security Center 内でワークフロー自動化を構成する方法](../security-center/security-center-planning-and-operations-guide.md) 

- [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [お客様は、独自のインシデント対応計画の作成に役立つ NIST の「コンピューター セキュリティ インシデント対応ガイド」を利用することもできます](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

また、サブスクリプション ( 稼働、非稼働など) を明確にマークし、Azure リソースを明確に識別および分類するための命名システムを作成します。

- [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

- [NIST の出版物「IT 計画と機能に関するテスト、トレーニング、演習プログラムのガイド」を参照してください。](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) で、不正なユーザーまたは権限のないユーザーによるお客様のデータへのアクセスが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**:連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。

- [連続エクスポートを構成する方法](../security-center/continuous-export.md) 

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**:セキュリティ アラートやセキュリティに関する推奨事項に対して "Logic Apps" 経由で応答を自動的にトリガーするには、Azure Security Center のワークフローの自動化機能を使用します。

- [ワークフローの自動化と Logic Apps を構成する方法](../security-center/workflow-automation.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、[Azure セキュリティ ベンチマークの「侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft クラウド侵入テストの実施ルールに従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](/azure/security/benchmarks/overview)」を参照してください。
- [Azure セキュリティ ベースライン](/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
