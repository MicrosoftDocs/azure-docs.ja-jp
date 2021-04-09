---
title: Azure セキュリティ ベンチマーク V2 の制御 - アセット管理
description: Azure セキュリティ ベンチマーク V2 の制御、アセット管理
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fbeb88b6aa542666481458fde97d7c63f467fa30
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051533"
---
# <a name="security-control-v2-asset-management"></a>セキュリティ コントロール V2: アセット管理

アセット管理には、Azure リソースに対するセキュリティの可視性とガバナンスを確保するためのコントロールが含まれます。 これには、セキュリティ担当者のアクセス許可、資産インベントリへのセキュリティ アクセス、およびサービスとリソース (インベントリ、追跡、および修正) の承認の管理に関する推奨事項が含まれます。

該当する組み込み Azure Policy を確認するには、「[Azure セキュリティ ベンチマーク規制コンプライアンスの組み込みイニシアチブの詳細: ネットワークのセキュリティ](../../governance/policy/samples/azure-security-benchmark.md#asset-management)」を参照してください。

## <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1:セキュリティ チームが資産のリスクを確実に可視化できるようにする

| Azure ID | CIS コントロール v7.1 ID | NNIST SP 800-53 r4 ID |
|--|--|--|--|
| AM-1 | 1.1、1.2 | CM-8、PM-5 |

セキュリティ チームに Azure テナントとサブスクリプションのセキュリティ閲覧者アクセス許可を付与して、セキュリティ チームが Azure Security Center を使用してセキュリティ上のリスクを監視できるようにします。

セキュリティ リスクの監視は、セキュリティ チームの責任の構造に応じて、中央のセキュリティ チームまたはローカル チームの責任になります。 ただし、セキュリティ分析情報とリスクは、常に組織内で一元的に集計する必要があります。 

セキュリティ閲覧者のアクセス許可は、テナント全体 (ルート管理グループ) に幅広く適用することも、管理グループまたは特定のサブスクリプションにスコープ指定することもできます。 

注:ワークロードとサービスを可視化するには、追加のアクセス許可が必要になることがあります。

- [セキュリティ閲覧者ロールの概要](../../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理グループの概要](../../governance/management-groups/overview.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [インフラストラクチャとエンドポイント セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [セキュリティ コンプライアンス管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2:セキュリティ チームが資産インベントリとメタデータに確実にアクセスできるようにする

| Azure ID | CIS コントロール v7.1 ID | NNIST SP 800-53 r4 ID |
|--|--|--|--|
| AM-2 | 1.1、1.2、1.4、1.5、9.1、12.1 | CM-8、PM-5 |

セキュリティ チームが、Azure 上の資産の継続的に更新されるインベントリに確実にアクセスできるようにします。 セキュリティ チームは、組織が新たなリスクにさらされる可能性を評価するため、および継続的なセキュリティ改善への入力として、このインベントリを必要とすることがよくあります。 

Azure Security Center インベントリ機能と Azure Resource Graph を使用すると、クエリを実行してサブスクリプション内のすべてのリソース (Azure サービス、アプリケーション、ネットワーク リソースなど) を検出できます。

タグと Azure の他のメタデータ (名前、説明、カテゴリ) を使用して、組織の分類に従って資産を論理的に整理します。

- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../../governance/resource-graph/first-query-portal.md)

- [Azure Security Center の資産インベントリ管理](../../security-center/asset-inventory.md)

- 資産のタグ付けの詳細については、「[リソースの名前付けとタグ付けの意思決定ガイド](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)」を参照

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [インフラストラクチャとエンドポイント セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [セキュリティ コンプライアンス管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-3-use-only-approved-azure-services"></a>AM-3:承認された Azure サービスのみを使用する

| Azure ID | CIS コントロール v7.1 ID | NNIST SP 800-53 r4 ID |
|--|--|--|--|
| AM-3 | 2.3、2.4 | CM-7、CM-8 |

Azure Policy を使用して、環境内でユーザーがプロビジョニングできるサービスを監査および制限します。 Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。 また、Azure Monitor を使用して、承認されていないサービスが検出されたときにアラートをトリガーするルールを作成することもできます。

- [Azure Policy を構成して管理する](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](../../governance/policy/samples/index.md)

- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../../governance/resource-graph/first-query-portal.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [セキュリティ コンプライアンス管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [体制管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4:アセット ライフサイクル管理のセキュリティを確保する

| Azure ID | CIS コントロール v7.1 ID | NNIST SP 800-53 r4 ID |
|--|--|--|--|
| AM-4 | 2.3、2.4、2.5 | CM-7、CM-8、CM-10、CM-11 |

大きな影響を与える可能性のある変更について、アセット ライフサイクル管理プロセスに対応するセキュリティ ポリシーを確立または更新します。 これらの変更には、ID プロバイダーとアクセス、データの秘密度、ネットワークの構成、管理特権の割り当てに対する変更が含まれます。

不要になったら Azure リソースを削除します。

- [Azure リソース グループとリソースを削除する](../../azure-resource-manager/management/delete-resource-group.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [インフラストラクチャとエンドポイント セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [体制管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [セキュリティ コンプライアンス管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5:Azure Resource Manager を操作するユーザーの機能を制限する

| Azure ID | CIS コントロール v7.1 ID | NNIST SP 800-53 r4 ID |
|--|--|--|--|
| AM-5 | 2.9 | AC-3 |

"Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成して、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure AD 条件付きアクセスを使用します。

- [条件付きアクセスを構成して Azure Resource Manager へのアクセスをブロックする方法](../../role-based-access-control/conditional-access-azure-management.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [体制管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [インフラストラクチャとエンドポイント セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6:コンピューティング リソースで承認済みのアプリケーションのみを使用する

| Azure ID | CIS コントロール v7.1 ID | NNIST SP 800-53 r4 ID |
|--|--|--|--|
| AM-6 | 2.6、2.7 | AC-3、CM-7、CM-8、CM-10、CM-11 |

Azure Virtual Machines で、確実に承認済みのソフトウェアだけが実行され、承認されていないソフトウェアの実行がすべてブロックされるようにします。

アプリケーションの許可リストを検出して生成するには、Azure Security Center (ASC) の適応型アプリケーション制御を使用します。 また、ASC の適応型アプリケーション制御を使用して、Azure Virtual Machines 上で承認されたソフトウェアのみを実行し、承認されていないソフトウェアの実行がすべてブロックされるようにすることもできます。

Windows および Linux VM からのインベントリ情報の収集を自動化するには、Azure Automation Change Tracking と Inventory を使用します。 ソフトウェアの名前、バージョン、発行元、および更新時刻は、Azure portal から入手できます。 ソフトウェアのインストール日やその他の情報を入手するには、ゲストレベルの診断を有効にし、Windows イベント ログを Log Analytics ワークスペースに取り込みます。

スクリプトの種類に基づき、オペレーティング システム固有の構成またはサードパーティのリソースを使用して、ユーザーの Azure コンピューティング リソース内でスクリプトを実行する機能を制限できます。

サードパーティ製のソリューションを使用して、承認されていないソフトウェアを検出して特定することもできます。

- [Azure Security Center の適応型アプリケーション制御を使用する方法](../../security-center/security-center-adaptive-application.md)

- [Azure Automation Change Tracking と Inventory の概要](../../automation/change-tracking/overview.md)

- [Windows 環境で PowerShell スクリプトの実行を制御する方法](/powershell/module/microsoft.powershell.security/set-executionpolicy)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [インフラストラクチャとエンドポイント セキュリティ](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [体制管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [セキュリティ コンプライアンス管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)
