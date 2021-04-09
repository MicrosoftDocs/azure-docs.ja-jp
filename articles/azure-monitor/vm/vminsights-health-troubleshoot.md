---
title: VM 分析情報のゲストの正常性のトラブルシューティング (プレビュー)
description: VM 分析情報の正常性に関する問題があるときに実行できるトラブルシューティング手順について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: d8b37569ebaa8e75be601a1efd65a23a61aeaa75
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051941"
---
# <a name="troubleshoot-vm-insights-guest-health-preview"></a>VM 分析情報のゲストの正常性のトラブルシューティング (プレビュー)
この記事では、VM 分析情報の正常性に関する問題があるときに実行できるトラブルシューティング手順について説明します。

## <a name="error-message-that-no-data-is-available"></a>使用できるデータがないというエラー メッセージ 

![データなし](media/vminsights-health-troubleshoot/no-data.png)


### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>仮想マシンが構成要件を満たしていることを確認する

1. 仮想マシンが Azure 仮想マシンであることを確認します。 Azure Arc for servers は現在サポートされていません。
2. 仮想マシンで、[サポートされているオペレーティング システム](vminsights-health-enable.md?current-limitations.md)が実行中であることを確認します。
3. 仮想マシンが、[サポートされているリージョン](vminsights-health-enable.md?current-limitations.md)にインストールされていることを確認します。
4. Log Analytics ワークスペースが、[サポートされているリージョン](vminsights-health-enable.md?current-limitations.md)にインストールされていることを確認します。

### <a name="verify-that-the-vm-is-properly-onboarded"></a>VM が適切にオンボードされていることを確認する
Azure Monitor エージェント拡張機能とゲスト VM 正常性エージェントが、仮想マシンに正常にプロビジョニングされていることを確認します。 Azure portal の仮想マシンのメニューから **[拡張機能]** を選択し、2 つのエージェントが一覧表示されていることを確認します。

![VM 拡張機能](media/vminsights-health-troubleshoot/extensions.png)

### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>仮想マシンでシステム割り当て ID が有効になっていることを確認する
仮想マシンでシステム割り当て ID が有効になっていることを確認します。 Azure portal で、仮想マシンのメニューから **[ID]** を選択します。 ユーザー マネージド IDが有効な場合、システム マネージド ID の状態に関係なく、Azure Monitor エージェントは構成サービスと通信できないため、ゲストの正常性拡張機能は機能しません。

![システム割り当て ID](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>データ収集ルールを確認する
データ ソースとして正常性拡張機能を指定するデータ収集ルールが、仮想マシンに関連付けられていることを確認します。

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>アクセス許可が十分でないため無効な要求であることを示すエラー メッセージ
このエラーは、**Microsoft.WorkloadMonitor** リソース プロバイダーがサブスクリプションに登録されなかったことを示しています。 このリソース プロバイダーの登録の詳細については、「[Azure リソース プロバイダーと種類](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)」を参照してください。 

![正しくない要求](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="next-steps"></a>次のステップ

- [VM 分析情報のゲストの正常性の概要を確認する](vminsights-health-overview.md)