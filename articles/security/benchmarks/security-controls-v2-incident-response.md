---
title: Azure セキュリティ ベンチマーク V2 - インシデント対応
description: Azure セキュリティ ベンチマーク V2 インシデント対応
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: b9295482c2464eb80bc49fa707744f49a2fbebfd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035379"
---
# <a name="security-control-v2-incident-response"></a>セキュリティ コントロール V2: インシデント対応

インシデント対応には、インシデント対応のライフサイクルにおけるコントロール (準備、検出と分析、包含、インシデント後のアクティビティ) が含まれます。 これには、Azure Security Center や Sentinel などの Azure サービスを使用したインシデント対応プロセスの自動化も含まれます。

該当する組み込み Azure Policy を確認するには、「[Azure セキュリティ ベンチマーク規制コンプライアンスの組み込みイニシアチブ: インシデント対応](../../governance/policy/samples/azure-security-benchmark.md#incident-response)」を参照してください。

## <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: 準備 – インシデント対応プロセスを Azure 用に更新する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| IR-1 | 19 | IR-4、IR-8 |

組織において、セキュリティ インシデントに対応するためのプロセスが用意されていること、Azure のこれらのプロセスが更新されていること、それらのプロセスを定期的に使用して準備されていることを確認します。

- [企業環境全体にセキュリティを実装する](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [インシデント対応のリファレンス ガイド](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティ運用](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [インシデントの準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [脅威インテリジェンス](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: 準備 – インシデント通知をセットアップする

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| IR-2 | 19.5 | IR-4、IR-5、IR-6、IR-8 |

Azure Security Center でセキュリティ インシデントの連絡先情報を設定します。 この連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 また、インシデント対応のニーズに応じて、異なる Azure サービスでインシデント アラートと通知をカスタマイズするオプションもあります。 

- [Azure Security Center のセキュリティ連絡先を設定する方法](../../security-center/security-center-provide-security-contact-details.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティ運用](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [インシデントの準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: 検出と分析 – 高品質のアラートに基づいてインシデントを作成する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| IR-3 | 19.6 | IR-4、IR-5 |

高品質のアラートを作成し、アラートの品質を測定するプロセスがあることを確認します。 これにより、過去のインシデントから教訓を学び、アナリストに対するアラートに優先順位を付けることができるので、擬陽性に時間を無駄にすることがありません。 

高品質のアラートは、過去のインシデントからの経験、検証されたコミュニティ ソース、およびさまざまなシグナル ソースの融合と関連付けによってアラートを生成してクリーンアップするように設計されたツールに基づいて構築できます。 

Azure Security Center では、多数の Azure 資産について高品質のアラートが提供されます。 ASC データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。 Azure Sentinel を使用すると、高度なアラート ルールを作成し、調査のためにインシデントを自動的に生成できます。 

エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートし、Azure リソースへのリスクを特定します。 アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートします。

- [エクスポートを構成する方法](../../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../../sentinel/connect-azure-security-center.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティ運用](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [インシデントの準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [脅威インテリジェンス](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: 検出と分析 – インシデントを調査する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| IR-4 | 19 | IR-4 |

アナリストが潜在的なインシデントを調査するときに、さまざまなデータ ソースを照会して使用し、発生した内容の完全なビューを構築できることを確認します。 キル チェーン全体で潜在的な攻撃者のアクティビティを追跡して死角を回避するには、さまざまなログを収集する必要があります。 また、他のアナリストのため、および将来において履歴が参照される場合のために、確実に分析情報と学習がキャプチャされているようにします。

調査のためのデータ ソースには、スコープ内のサービスおよび実行中のシステムから既に収集されている一元化されたログ ソースが含まれますが、次のものも含まれます。

- ネットワーク データ - ネットワーク セキュリティ グループのフロー ログ、Azure Network Watcher、Azure Monitor を使用して、ネットワーク フロー ログやその他の分析情報をキャプチャします。 

- 実行中のシステムのスナップショット: 

    - Azure 仮想マシンのスナップショット機能を使用して、実行中のシステムのディスクのスナップショットを作成します。 

    - オペレーティング システムのネイティブ メモリ ダンプ機能を使用して、実行中のシステムのメモリのスナップショットを作成します。

    - Azure サービスのスナップショット機能またはソフトウェア独自の機能を使用して、実行中のシステムのスナップショットを作成します。

Azure Sentinel により、事実上すべてのログソースに対して広範な Data Analytics と、インシデントのライフサイクル全体を管理するためのケース管理ポータルが提供されます。 調査中のインテリジェンス情報を、追跡とレポートのためにインシデントに関連付けることができます。 

- [Windows マシンのディスクのスナップショットを作成する](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux マシンのディスクのスナップショットを作成する](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure サポートの診断情報とメモリ ダンプ コレクション](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Azure Sentinel でインシデントを調査します](../../sentinel/tutorial-investigate-cases.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティ運用](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [インシデントの準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [脅威インテリジェンス](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: 検出と分析 – インシデントの優先順位を付ける

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| IR-5 | 19.8 | CA-2、IR-4 |

アラートの重要度と資産の機密性に基づいて、最初に注目するインシデントについてのコンテキストをアナリストに提供します。 

Azure Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

さらに、タグを使用してリソースをマークし、Azure リソース (特に、機密データを処理するもの) を識別して分類するための命名システムを作成します。 インシデントが発生した Azure リソースと環境の重要度に基づいて、アラートの修復に優先順位を付けることは、お客様の責任です。

- [Security alerts in Azure Security Center](../../security-center/security-center-alerts-overview.md)

- [タグを使用した Azure リソースの整理](../../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティ運用](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [インシデントの準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [脅威インテリジェンス](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: 包含、根絶、復旧 – インシデントの処理を自動化する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| IR-6 | 19 | IR-4、IR-5、IR-6 |

手動による反復タスクを自動化して、応答時間を短縮し、アナリストの負担を軽減します。 手動タスクの実行には時間がかかり、各インシデントの速度が低下し、アナリストが処理できるインシデントの数が減少します。 手動タスクではアナリストの疲労も増加します。これにより、遅延が発生する人的エラーのリスクが増加し、複雑なタスクに効果的に焦点を当てるアナリストの能力が低下します。 Azure Security Center と Azure Sentinel のワークフロー自動化機能を使用して、自動的にアクションをトリガーしたり、プレイブックを実行して受信したセキュリティ アラートに応答したりします。 プレイブックにより、通知の送信、アカウントの無効化、問題のあるネットワークの特定などのアクションが実行されます。 

- [Security Center でワークフロー自動化を構成する](../../security-center/workflow-automation.md)

- [Azure Security Center で脅威への自動対応を設定する](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Azure Sentinel で脅威への自動対応を設定します](../../sentinel/tutorial-respond-threats-playbook.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティ運用](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [インシデントの準備](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [脅威インテリジェンス](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)