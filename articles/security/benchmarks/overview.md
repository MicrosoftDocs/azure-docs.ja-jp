---
title: Azure セキュリティ ベンチマーク V2 の概要
description: Azure セキュリティ ベンチマーク V2 の概要
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 3bc97a932e622838315f717cc7b036a559787805
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97369159"
---
# <a name="overview-of-the-azure-security-benchmark-v2"></a>Azure Security Benchmark (V2) の概要

Azure セキュリティベンチマーク (ASB) は、Azure 上のワークロード、データ、およびサービスのセキュリティを強化するための規範的なベスト プラクティスと推奨事項を提供します。

このベンチマークは、次のものを含む包括的なセキュリティ ガイダンスの一部になっています。

- **クラウド導入フレームワーク** – [戦略](/azure/cloud-adoption-framework/strategy/define-security-strategy)、[ロールと責任](/azure/cloud-adoption-framework/organize/cloud-security)、[Azure のセキュリティに関する上位 10 件のベスト プラクティス](/azure/cloud-adoption-framework/get-started/security#step-1-establish-essential-security-practices)、[リファレンス実装](/azure/cloud-adoption-framework/ready/enterprise-scale/)など、セキュリティに関するガイダンスです。
- **Azure Well-Architected フレームワーク** – Azure での [ワークロードのをセキュリティ保護](/assessments/?mode=pre-assessment&session=local)に関するガイダンスです。
- **Microsoft セキュリティのベスト プラクティス** – Azure での例を使用した [推奨事項](/security/compass/microsoft-security-compass-introduction)です。

 Azure セキュリティ ベンチマークの対象は、クラウド中心のコントロール領域です。 これらのコントロールは、Center for Internet Security (CIS) コントロール バージョン 7.1 および米国標準技術研究所 (NIST) SP 800-53 で説明されているような、よく知られたセキュリティ ベンチマークと一貫性があります。
Azure セキュリティ ベンチマークには、次のコントロールが含まれます。

| ASB コントロール ドメイン | 説明 
|--|--|
| [ネットワーク セキュリティ (NS)](security-controls-v2-network-security.md) | ネットワーク セキュリティでは、仮想ネットワークのセキュリティ保護、プライベート接続の確立、外部からの攻撃の防止と軽減、DNS の保護など、Azure ネットワークをセキュリティで保護するためのコントロールを対象とします。 |
| [ID 管理 (IM)](security-controls-v2-identity-management.md) | ID 管理では、シングル サインオンの使用、強力な認証、アプリケーションのマネージド ID (およびサービス プリンシパル)、条件付きアクセス、アカウント異常監視など、Azure Active Directory を使用してセキュリティで保護された ID とアクセス制御を確立するためのコントロールを対象とします。 |
| [特権アクセス (PA)](security-controls-v2-privileged-access.md) | 特権アクセスでは、管理モデル、管理アカウント、特権アクセス ワークステーションを意図的なリスクと偶発的なリスクから保護するためのさまざまなコントロールなど、Azure テナントおよびリソースへの特権アクセスを保護するためのコントロールを対象とします。 |
| [データ保護 (DP)](security-controls-v2-data-protection.md) | データ保護では、Azure でアクセス制御、暗号化、およびログ記録を使用した機密データ資産の検出、分類、保護、監視など、保存時、転送中、および承認されたアクセス メカニズムを介したデータ保護のコントロールを対象とします。 |
| [アセット管理 (AM)](security-controls-v2-asset-management.md) | アセット管理では、セキュリティ担当者に対するアクセス許可、資産インベントリへのセキュリティ アクセス、サービスとリソース (在庫、追跡、および修正) の承認の管理に関する推奨事項など、Azure リソースに対するセキュリティの可視性とガバナンスを確保するためのコントロールを対象とします。 |
| [ログと脅威検出 (LT)](security-controls-v2-logging-threat-detection.md) | ログと脅威検出では、Azure での脅威の検出や Azure サービスの監査ログの有効化、収集、および格納を行うコントロールを対象とします。たとえば、Azure サービスのネイティブ脅威検出を使用して高品質アラートを生成するコントロールによる検出、調査、修復のプロセスの有効化のほか、Azure Monitor によるログの収集、Azure Sentinel によるセキュリティ分析の一元化、時間の同期、およびログの保持などがあります。 |
| [インシデント対応 (IR)](security-controls-v2-incident-response.md) | インシデント対応では、Azure Security Center や Sentinel などの Azure サービスを使用してインシデント対応プロセスを自動化するなど、インシデント対応のライフ サイクル (準備、検出と分析、インシデント発生後のアクティビティ) におけるコントロールを対象とします。 |
| [体制と脆弱性の管理 (PV)](security-controls-v2-posture-vulnerability-management.md) | 体制と脆弱性の管理では、脆弱性のスキャン、侵入テスト、修復、Azure リソースでのセキュリティ構成の追跡、レポート、修正など、Azure のセキュリティ体制を評価し改善するためのコントロールに重点を置きます。 |
| [エンドポイント セキュリティ (ES)](security-controls-v2-endpoint-security.md) | エンドポイント セキュリティでは、Azure 環境でのエンドポイントに対するエンドポイントの検出と応答 (EDR) およびマルウェア対策サービスの使用など、エンドポイントの検出および応答でのコントロールを対象とします。 |
| [バックアップと回復 (BR)](security-controls-v2-backup-recovery.md) | バックアップと回復により、データと構成のバックアップがさまざまなサービス レベルで確実に実行、検証、保護されるようにコントロールがカバーされています。 |
| [ガバナンスと戦略 (GS)](security-controls-v2-governance-strategy.md) | ガバナンスと戦略では、セキュリティ アシュアランスをガイドし維持するための整合性のあるセキュリティ戦略と文書化されたガバナンス アプローチを実現するためのガイダンスを提供します。たとえば、さまざまなクラウド セキュリティ機能、統一された技術的戦略、およびサポート ポリシーおよび標準に応じたロールと責任を確立します。 |

## <a name="azure-security-benchmark-recommendations"></a>Azure セキュリティ ベンチマークの推奨事項

各推奨事項には次の情報が含まれます。

- **Azure ID**: 推奨事項に対応する Azure セキュリティ ベンチマーク ID。
- **CIS Controls v7.1 ID**:この推奨事項に対応する CIS Controls v7.1 コントロール。
- **NIST SP 800-53 r4 ID**:この推奨事項に対応する NIST SP 800-53 r4 (中) コントロール。
- **[詳細]** :推奨事項の根拠と、その実装方法に関するガイダンスへのリンク。 推奨事項が Azure Security Center によってサポートされている場合は、その情報も一覧表示されます。
- **責任**: お客様またはサービス プロバイダー、あるいはその両方が、この推奨事項の実装に責任があるかどうか。 セキュリティ責任は、パブリック クラウドで共有されます。 一部のセキュリティ コントロールはクラウド サービス プロバイダーのみが使用できるため、プロバイダーはそれらに対処する必要があります。 これらは一般的な見解です。個々のサービスによっては、責任が Azure セキュリティ ベンチマークに記載されているものと異なることがあります。 これらの違いについては、個々のサービスのベースラインの推奨事項で説明されています。
- **顧客のセキュリティ上の利害関係者**:お客様の組織でそれぞれのコントロールについて説明責任を持つ、責任を負う、または助言が求められる [セキュリティ職能](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)。 これは、会社のセキュリティ組織構造と、Azure セキュリティに関連して設定したロールと責任に応じて、組織ごとに異なる可能性があります。

> [!NOTE]
> ASB と業界のベンチマーク (NIST や CIS など) のコントロール マッピングは、NIST または CIS で定義されているコントロール要件に完全または部分的に対応するために特定の Azure 機能を使用できるということを示しているにすぎません。 このように実装しても、CIS または NIST の対応するコントロールに完全に準拠することには必ずしもならないので注意してください。

Azure セキュリティ ベンチマークの取り組みに関するフィードバックと積極的な参加をお待ちしております。 Azure セキュリティ ベンチマーク チームに直接情報を提供したい場合は、 https://aka.ms/AzSecBenchmark のフォームに入力してください

## <a name="download"></a>ダウンロード

[スプレッドシート形式](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Security%20Benchmark)で Azure セキュリティ ベンチマークをダウンロードできます。

## <a name="next-steps"></a>次のステップ 
- 最初セキュリティ コントロールを参照してください。[ネットワークのセキュリティ](security-control-network-security.md)
- 「[Azure セキュリティ ベンチマークの概要](introduction.md)」を読む
- [Azure セキュリティの基礎](../fundamentals/index.yml)について学習する