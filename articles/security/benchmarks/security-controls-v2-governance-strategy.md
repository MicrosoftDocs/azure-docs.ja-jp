---
title: Azure セキュリティ ベンチマーク V2 - ガバナンスと戦略
description: Azure セキュリティ ベンチマーク V2 ガバナンスと戦略
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 277033e41ec7e02b89eca8cf74fe6854acb51cc1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727024"
---
# <a name="security-control-v2-governance-and-strategy"></a>セキュリティ コントロール V2:ガバナンスと戦略

ガバナンスと戦略では、セキュリティ アシュアランスをガイドし維持するための整合性のあるセキュリティ戦略と文書化されたガバナンス アプローチを実現するためのガイダンスを提供します。たとえば、さまざまなクラウド セキュリティ機能、統一された技術的戦略、およびサポート ポリシーおよび標準に応じたロールと責任を確立します。

## <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: 資産の管理とデータ保護の戦略を定義する

| Azure ID | CIS コントロール v7.1 ID | NNIST SP 800-53 r4 ID |
|--|--|--|--|
| GS-1 | 2、13 | SC、AC |

システムとデータを継続的に監視および保護するための明確な戦略が文書化および伝達されるようにします。 ビジネスに不可欠なデータとシステムの検出、評価、保護、監視の優先順位を決定します。

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

- ビジネス リスクに応じたデータ分類標準

- リスクと資産のインベントリに対するセキュリティ組織の可視性

- Azure サービスを使用するためのセキュリティ組織の承認

- ライフサイクルを通じた資産のセキュリティ

- 組織のデータ分類に従った必要なアクセス制御戦略

- Azure ネイティブおよびサードパーティのデータ保護機能の使用

- 転送中および保存中のユース ケースのデータ暗号化要件

- 適切な暗号化標準

詳細については、次のリファレンスを参照してください。
- [Azure セキュリティ アーキテクチャに関する推奨事項 - ストレージ、データ、暗号化](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Azure のセキュリティの基礎 - Azure のデータ セキュリティ、暗号化、ストレージ](../fundamentals/encryption-overview.md)

- [クラウド導入フレームワーク - Azure のデータ セキュリティと暗号化のベスト プラクティス](../fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure セキュリティ ベンチマーク - アセット管理](security-controls-v2-asset-management.md)

- [Azure セキュリティ ベンチマーク - データ保護](security-controls-v2-data-protection.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [すべての利害関係者](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: 企業のセグメント化戦略を定義する

| Azure ID | CIS コントロール v7.1 ID | NNIST SP 800-53 r4 ID |
|--|--|--|--|
| GS-2 | 4、9、16 | AC、CA、SC |

ID、ネットワーク、アプリケーション、サブスクリプション、管理グループ、その他のコントロールを利用し、アセットへのアクセスをセグメント化する企業規模の戦略を確立します。

セキュリティ分離の必要性と、互いと通信し、データにアクセスする必要があるシステムの日常的操作を有効にするという必要性のバランスを慎重に取ります。

セグメント化戦略は、ネットワーク セキュリティ、ID とアクセス モデル、アプリケーション アクセス許可とアクセス モデル、人的プロセスの制御など、あらゆるコントロールの種類を対象として確実に一貫性をもって実装します。

- [Azure のセグメント化戦略に関するガイド (動画)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure のセグメント化戦略に関するガイド (ドキュメント)](/security/compass/governance#enterprise-segmentation-strategy)

- [ネットワークのセグメント化を企業のセグメント化戦略に合わせる](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [すべての利害関係者](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: セキュリティ態勢管理の戦略を定義する

| Azure ID | CIS コントロール v7.1 ID | NNIST SP 800-53 r4 ID |
|--|--|--|--|
| GS-3 | 20、3、5 | RA、CM、SC |

個々の資産とそれらがホストされている環境に対するリスクを継続的に測定し、軽減します。 高い価値を持つ資産と、攻撃に晒される可能性の高い部分 (公開されたアプリケーション、ネットワークのイングレス ポイントとエグレス ポイント、ユーザーと管理者のエンドポイントなど) を優先します。

- [Azure セキュリティ ベンチマーク - 体制と脆弱性の管理](security-controls-v2-posture-vulnerability-management.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [すべての利害関係者](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: 組織の役割、責任、責務を整合させる

| Azure ID | CIS コントロール v7.1 ID | NNIST SP 800-53 r4 ID |
|--|--|--|--|
| GS-4 | N/A | PL、PM |

セキュリティ組織における役割と責任に関する明確な戦略が文書化されて伝えられるようにします。 セキュリティに関する決定についてわかりやすく説明すること、共有される責任モデルについて全員に教育すること、クラウドをセキュリティで保護するテクノロジについて技術チームに教育することを優先とします。

- [Azure のセキュリティのベスト プラクティス 1 – 人: クラウド セキュリティに関する取り組みについてチームを教育する](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure のセキュリティのベスト プラクティス 2 - 人: クラウド セキュリティ テクノロジについてチームを教育する](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure のセキュリティのベスト プラクティス 3 - プロセス: クラウドのセキュリティに関する意思決定のアカウンタビリティを割り当てる](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [すべての利害関係者](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-network-security-strategy"></a>GS-5: ネットワーク セキュリティ戦略を定義する

| Azure ID | CIS コントロール v7.1 ID | NNIST SP 800-53 r4 ID |
|--|--|--|--|
| GS-5 | 9 | CA、SC |

組織全体のセキュリティ アクセス制御戦略の一環として、Azure ネットワーク セキュリティ アプローチを確立します。

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。

- 一元的なネットワーク管理とセキュリティ責任

- エンタープライズ セグメント化戦略と一致する仮想ネットワーク セグメント化モデル

- さまざまな脅威と攻撃のシナリオにおける修復戦略

- インターネット エッジとイングレスおよびエグレス戦略

- クラウドとオンプレミスのハイブリッド相互依存戦略

- 最新のネットワーク セキュリティ成果物 (ネットワーク図、参照ネットワーク アーキテクチャなど)

詳細については、次のリファレンスを参照してください。

- [Azure のセキュリティのベスト プラクティス 11 - アーキテクチャ。単一の統合セキュリティ戦略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure セキュリティ ベンチマーク - ネットワーク セキュリティ](security-controls-v2-network-security.md)

- [Azure のネットワーク セキュリティの概要](../fundamentals/network-overview.md)

- [エンタープライズ ネットワーク アーキテクチャ戦略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [すべての利害関係者](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: ID と特権アクセス戦略を定義する

| Azure ID | CIS コントロール v7.1 ID | NNIST SP 800-53 r4 ID |
|--|--|--|--|
| GS-6 | 16、4 | AC、AU、SC |

組織全体のセキュリティ アクセス制御戦略の一環として、Azure の ID と特権アクセス アプローチを確立します。

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。

- 一元化された ID と認証システム、および他の内部および外部 ID システムとのその相互接続

- さまざまなユース ケースおよび条件における強力な認証方法

- 高い特権を持つユーザーの保護

- 異常なユーザー アクティビティの監視と処理

- ユーザー ID とアクセスの確認と調整のプロセス

詳細については、次のリファレンスを参照してください。

- [Azure セキュリティ ベンチマーク - ID 管理](security-controls-v2-identity-management.md)

- [Azure セキュリティ ベンチマーク - 特権アクセス](security-controls-v2-privileged-access.md)

- [Azure のセキュリティのベスト プラクティス 11 - アーキテクチャ。単一の統合セキュリティ戦略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure ID 管理のセキュリティの概要](../fundamentals/identity-management-overview.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [すべての利害関係者](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: ログ記録と脅威対応戦略を定義する

| Azure ID | CIS コントロール v7.1 ID | NNIST SP 800-53 r4 ID |
|--|--|--|--|
| GS-7 | 19 | IR、AU、RA、SC |

コンプライアンス要件を満たしながら脅威を迅速に検出して修復するための、ログ記録と脅威対応戦略を確立します。 統合と手動手順ではなく脅威に集中できるように、高品質のアラートとシームレスなエクスペリエンスをアナリストに提供することを優先します。 

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

- セキュリティ運用 (SecOps) 組織の役割と責任 

- NIST または他の業界フレームワークと一致する、明確に定義されたインシデント対応プロセス 

- 脅威の検出、インシデント対応、コンプライアンスのニーズに対応するためのログのキャプチャと保持

- SIEM、Azure のネイティブ機能、その他のソースを使用した、脅威に関する情報の一元的な可視化と関連付け 

- 顧客、サプライヤー、および関心を持つパブリック パーティに関するコミュニケーションと通知の計画

- ログ記録と脅威の検出、フォレンジクス、攻撃の修復と根絶など、インシデント処理に対する Azure ネイティブおよびサードパーティのプラットフォームの使用

- インシデントとインシデント発生後のアクティビティを処理するためのプロセス (教訓や証拠の保持など)

詳細については、次のリファレンスを参照してください。
- [Azure セキュリティ ベンチマーク - ログと脅威検出](security-controls-v2-logging-threat-detection.md)

- [Azure セキュリティ ベンチマーク - インシデント対応](security-controls-v2-incident-response.md)

- [Azure のセキュリティのベスト プラクティス 4 - プロセス: クラウドのインシデント対応プロセスを更新する](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [Azure 導入フレームワーク、ログ、およびレポートの決定ガイド](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure のエンタープライズ スケーリング、管理、監視](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [すべての利害関係者](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-8-define-backup-and-recovery-strategy"></a>GS-8:バックアップと復旧の戦略を定義する

| Azure ID | CIS コントロール v7.1 ID | NNIST SP 800-53 r4 ID |
|--|--|--|--|
| GS-8 | 10 | CP |

組織の Azure のバックアップと復旧の戦略を確立します。 

この戦略には、次の要素に関する文書化されたガイダンス、ポリシー、標準が含まれている必要があります。 

- ビジネス回復性の目標に従った目標復旧時間 (RTO) と目標復旧時点 (RPO) の定義

- アプリケーションとインフラストラクチャのセットアップにおける冗長性の設計

- アクセスの制御とデータ暗号化を使用したバックアップの保護

詳細については、次のリファレンスを参照してください。
- [Azure セキュリティ ベンチマーク - バックアップと復旧](security-controls-v2-backup-recovery.md)

- [Azure の優れたアーキテクチャ フレームワーク - Azure アプリケーションのバックアップとディザスター リカバリー](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Azure 導入フレームワーク - ビジネス継続性とディザスター リカバリー](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [すべての利害関係者](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)