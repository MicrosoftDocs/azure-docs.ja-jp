---
title: Azure Automanage の一般的なオンボード エラーのトラブルシューティング
description: Automanage の一般的なオンボード エラーとそれらをトラブルシューティングする方法
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alsin
ms.openlocfilehash: 18165ce5f39b32fe1c5af28bc88e8e1bd0e9cb62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955552"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Automanage の一般的なオンボード エラーのトラブルシューティング
Automanage は、サービスへのマシンのオンボードに失敗する場合があります。 このドキュメントでは、デプロイの失敗をトラブルシューティングする方法について説明し、デプロイが失敗する可能性があるいくつかの一般的な理由を示し、軽減策として考えられる次の手順について説明します。

## <a name="troubleshooting-deployment-failures"></a>デプロイ失敗のトラブルシューティング
マシンを Automanage にオンボードすると、Azure Resource Manager のデプロイが作成されます。 オンボードに失敗した場合、デプロイを参照して、失敗した理由の詳細を調べると役立つことがあります。 下の図に示すように、失敗の詳細ポップアップにデプロイへのリンクがあります。

:::image type="content" source="media\common-errors\failure-flyout.png" alt-text="Automanage の失敗の詳細ポップアップ。":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>失敗した VM を含むリソース グループのデプロイを確認する
失敗のポップアップには、オンボードに失敗したマシンが含まれているリソース グループ内のデプロイへのリンクと、デプロイをフィルター処理するために使用できるプレフィックス名が含まれています。 リンクをクリックすると、デプロイ ブレードが表示され、そこでデプロイをフィルター処理して、お使いのマシンへの Automanage デプロイを表示することができます。 複数のリージョンにわたってデプロイする場合は、正しいリージョンのデプロイをクリックしてください。

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>失敗した VM を含むサブスクリプションのデプロイを確認する
リソース グループのデプロイにエラーが表示されない場合は、次の手順として、オンボードに失敗した VM を含むサブスクリプションのデプロイを確認します。 失敗のポップアップの **[Deployments for subscription]\(サブスクリプションのデプロイ\)** リンクをクリックし、**Automanage-DefaultResourceGroup** フィルターを使用して、デプロイをフィルター処理します。 失敗ブレードのリソース グループ名を使用して、デプロイをフィルター処理します。 デプロイ名の末尾には、リージョン名が付けられます。 複数のリージョンにわたってデプロイする場合は、正しいリージョンのデプロイをクリックしてください。

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Log Analytics ワークスペースにリンクされているサブスクリプションのデプロイを確認する
失敗した VM を含むリソース グループまたはサブスクリプションに失敗したデプロイが見つからない場合、および失敗した VM が別のサブスクリプションの Log Analytics ワークスペースに接続されている場合は、Log Analytics ワークスペースにリンクされているサブスクリプションにアクセスして、失敗したデプロイがないか確認します。

## <a name="common-deployment-errors"></a>一般的なデプロイのエラー

エラー |  対応策
:-----|:-------------|
Automanage アカウントのアクセス許可が不十分なエラー | これは、新しい Automanage アカウントを含むサブスクリプションを新しいテナントに最近移動した場合に発生する可能性があります。 これを解決する手順については、[こちら](./repair-automanage-account.md)を参照してください。
ワークスペース リージョンがリージョン マッピング要件に一致しない | Automanage がマシンをオンボードできませんでしたが、マシンが現在リンクされている Log Analytics ワークスペースが、サポートされている Automation リージョンにマップされていません。 既存の Log Analytics ワークスペースと Automation アカウントが、[サポートされているリージョン マッピング](../automation/how-to/region-mappings.md)に配置されていることを確認します。
"'System deny assignment created by managed application' (マネージド アプリケーションによって作成されたシステム拒否割り当て) という名前の拒否割り当てによりアクセスが拒否されました" | リソースに対して [denyAssignment](../role-based-access-control/deny-assignments.md) が作成されたため、リソースへのアクセスを自動管理できませんでした。 これは、[ブループリント](../governance/blueprints/concepts/resource-locking.md)または[マネージド アプリケーション](../azure-resource-manager/managed-applications/overview.md)のいずれかが原因で発生した可能性があります。
"OS Information: Name='(null)', ver='(null)', agent status='Not Ready'. (OS 情報: 名前 ='(null)'、バージョン ='(null)'、エージェントの状態 ='準備中'。)" | [サポートされる最小エージェント バージョン](/troubleshoot/azure/virtual-machines/support-extensions-agent-version)が実行されていること、エージェントが実行されていること ([linux](/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) と [windows](/troubleshoot/azure/virtual-machines/windows-azure-guest-agent))、およびエージェントが最新の状態であること ([linux](../virtual-machines/extensions/update-linux-agent.md) と [windows](../virtual-machines/extensions/agent-windows.md)) を確認します。
"VM has reported a failure when processing extension 'IaaSAntimalware' (拡張機能 'IaaSAntimalware' の処理中に VM によってエラーがレポートされました)" | VM に別のマルウェア対策またはウイルス対策オファリングが既にインストールされていないことを確認します。 それでも解決しない場合は、サポートにご連絡ください。
ASC ワークスペース: Automanage は現在、"_この場所_" では Log Analytics サービスをサポートいません。 | [サポートされているリージョン](./automanage-virtual-machines.md#supported-regions)に VM があることを確認してください。
ポリシー違反に起因してテンプレートのデプロイが失敗しました。 詳細をご覧ください。 | Automanage への VM のオンボードを妨げるポリシーがあります。 Automanage にオンボードする VM が含まれているサブスクリプションまたはリソース グループに適用されているポリシーを確認します。
"割り当てに失敗しました。入手できる情報は他にありません。" | Microsoft Azure サポートのケースを開いてください。

## <a name="next-steps"></a>次のステップ

* [Azure Automanage の詳細情報](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [Azure portal で Azure Automanage for virtual machines を有効にする](quick-create-virtual-machines-portal.md)