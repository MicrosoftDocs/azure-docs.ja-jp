---
title: Azure Arc 対応サーバーを Azure Sentinel にオンボードする
description: Azure Arc 対応サーバーを Azure Sentinel に追加し、そのセキュリティ状態を予防的に監視する方法について説明します。
ms.date: 11/16/2020
ms.topic: conceptual
ms.openlocfilehash: 2364ba72ac5b10ec4e1f433cc6d591c3ca389ecd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100584739"
---
# <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>Azure Arc 対応サーバーを Azure Sentinel にオンボードする

この記事は、Azure Arc 対応サーバーを [Azure Sentinel](../../sentinel/overview.md) にオンボードして、セキュリティ関連イベントの収集を開始するのに役立つことを目的としています。 Azure Sentinel では、企業全体にわたって、アラートの検出、脅威の可視性、予防的なハンティング、脅威への対応を提供します。

## <a name="prerequisites"></a>前提条件

始める前に、次の要件を満たしていることを確認します。

- [Log Analytics ワークスペース。](../../azure-monitor/logs/data-platform-logs.md) Log Analytics ワークスペースの詳細については、「[Azure Monitor ログのデプロイの設計](../../azure-monitor/logs/design-logs-deployment.md)」を参照してください。

- [サブスクリプションで有効になっている](../../sentinel/quickstart-onboard.md) Azure Sentinel。

- マシンまたはサーバーが Azure Arc 対応サーバーに接続されている。

## <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>Azure Arc 対応サーバーを Azure Sentinel にオンボードする

Azure Sentinel には、Microsoft ソリューション用のすぐに使用できるコネクタが多数用意されており、リアルタイムの統合が提供されます。 物理マシンと仮想マシンの場合、ログを収集して Azure Sentinel に転送する Log Analytics エージェントをインストールできます。 Arc 対応サーバーでは、以下の方法を使用した Log Analytics エージェントのデプロイがサポートされています。

- VM 拡張機能フレームワークの使用。

    Azure Arc 対応サーバーのこの機能を使用すると、Azure 以外の Windows や Linux のサーバーに、Log Analytics エージェントの VM 拡張機能をデプロイできます。 VM 拡張機能は、ハイブリッド コンピューターまたは Arc 対応サーバーで管理されているサーバーで、次の方法を使用して管理できます。

    - [Azure Portal](manage-vm-extensions-portal.md)
    - [Azure CLI](manage-vm-extensions-cli.md)
    - [Azure PowerShell](manage-vm-extensions-powershell.md)
    - Azure [Resource Manager テンプレート](manage-vm-extensions-template.md)

- Azure Policy の使用。

    この方法を利用し、Azure Policy の "[Log Analytics エージェントを Linux または Windows Azure Arc マシンにデプロイする](../../governance/policy/samples/built-in-policies.md#monitoring)" という組み込みポリシーを使用して、Arc 対応サーバーに Log Analytics エージェントがインストールされているかどうかを監査します。 エージェントがインストールされていない場合は、修復タスクを使用して自動的にそれがデプロイされます。 または、Azure Monitor for VMs を使用してマシンを監視する予定の場合は、代わりに "[Azure Monitor for VMs を有効にする](../../governance/policy/samples/built-in-initiatives.md#monitoring)" というイニシアティブを使用して Log Analytics エージェントのインストールと構成を行います。

Azure Policy を使用して、Windows または Linux 用の Log Analytics エージェントをインストールすることをお勧めします。

Arc 対応サーバーが接続されると、データの Azure Sentinel へのストリーミングが開始され、操作を開始する準備が整います。 [組み込みのブック](../../sentinel/quickstart-get-visibility.md)でログを表示したり、Log Analytics でクエリを作成して[データを調査](../../sentinel/tutorial-investigate-cases.md)したりできます。

## <a name="next-steps"></a>次のステップ

[Azure Sentinel を使用した脅威の検出](../../sentinel/tutorial-detect-threats-built-in.md)の概要。