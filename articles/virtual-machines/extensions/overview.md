---
title: Azure 仮想マシンの拡張機能とその機能
description: Azure VM の拡張機能について詳しく説明します
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.date: 08/03/2020
ms.openlocfilehash: e1b96293db0389201fdab3340d8f0e74fefc4c52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559717"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Azure 仮想マシンの拡張機能とその機能
拡張機能は、Azure VM でのデプロイ後の構成と自動化を提供する小さなアプリケーションです。 Azure プラットフォームでは、VM の構成、監視、セキュリティ、およびユーティリティのアプリケーションを対象とする多くの拡張機能をホストします。 公開元は、アプリケーションを取得し、それを拡張機能にまとめて、インストールを簡略化します。 ユーザーは必須パラメーターを指定するだけで済みます。 

## <a name="how-can-i-find-what-extensions-are-available"></a>使用可能な拡張機能を見つける方法
VM を選択し、左側のメニューから **[拡張機能]** を選択すると、使用可能な拡張機能を表示できます。 拡張機能の完全な一覧を取得するには、[Linux 用 VM 拡張機能の検出](features-linux.md)と [Windows 用 VM 拡張機能の検出](features-windows.md)に関する記事をご覧ください。

## <a name="how-can-i-install-an-extension"></a>拡張機能をインストールする方法
Azure VM 拡張機能は、Azure CLI、PowerShell、Resource Manager テンプレート、および Azure portal を使用して管理できます。 拡張機能を試すには、Azure portal に移動し、カスタム スクリプト拡張機能を選択してから、コマンドまたはスクリプトを渡して拡張機能を実行します。

詳細については、[Windows のカスタム スクリプト拡張機能](custom-script-windows.md)と [Linux のカスタム スクリプト拡張機能](custom-script-linux.md)に関する記事をご覧ください。

## <a name="how-do-i-manage-extension-application-lifecycle"></a>拡張アプリケーションのライフサイクルを管理する方法
拡張機能をインストールまたは削除するために、VM に直接接続する必要はありません。 Azure 拡張機能のライフサイクルは、VM の外部で管理され、Azure プラットフォームに統合されています。

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>拡張機能について検討すべきその他の項目
個別の VM 拡張アプリケーションによっては、エンドポイントへのアクセスなどの独自の環境要件がある場合があります。 各拡張機能には、どのオペレーティング システムがサポートされているかなど、前提条件について説明する記事があります。

## <a name="troubleshoot-extensions"></a>拡張機能のトラブルシューティングを行う

各拡張機能のトラブルシューティング情報については、拡張機能の概要の「**トラブルシューティングとサポート**」セクションを参照してください。 利用可能なトラブルシューティング情報の一覧を次に示します。

| 名前空間 | トラブルシューティング |
|-----------|-----------------|
| microsoft.azure.monitoring.dependencyagent.dependencyagentlinux | [Linux 用の Azure Monitor の依存関係](agent-dependency-linux.md#troubleshoot-and-support) |
| microsoft.azure.monitoring.dependencyagent.dependencyagentwindows | [Windows 用の Azure Monitor の依存関係](agent-dependency-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryptionforlinux | [Linux 用の Azure Disk Encryption](azure-disk-enc-linux.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryption | [Windows 用の Azure Disk Encryption](azure-disk-enc-windows.md#troubleshoot-and-support) |
| microsoft.compute.customscriptextension | [Windows 用のカスタム スクリプト](custom-script-windows.md#troubleshoot-and-support) |
| microsoft.ostcextensions.customscriptforlinux | [Linux 用の Desired State Configuration](dsc-linux.md#troubleshoot-and-support) |
| microsoft.powershell.dsc | [Windows 用の Desired State Configuration](dsc-windows.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverlinux | [Linux 用の NVIDIA GPU ドライバー拡張機能](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverwindows | [Windows 用の NVIDIA GPU ドライバー拡張機能](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.iaasantimalware | [Windows 用のマルウェア対策拡張機能](iaas-antimalware-windows.md#troubleshoot-and-support) |
| microsoft.enterprisecloud.monitoring.omsagentforlinux | [Linux 用の Azure Monitor](oms-linux.md#troubleshoot-and-support)
| microsoft.enterprisecloud.monitoring.microsoftmonitoringagent | [Windows 用の Azure Monitor](oms-windows.md#troubleshoot-and-support) |
| stackify.linuxagent.extension.stackifylinuxagentextension | [Linux 用の Stackify Retrace](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux.microsoft.ostcextensions | [Linux 用のパスワードのリセット](vmaccess.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Linux 用のスナップショット](vmsnapshot-linux.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Windows 用のスナップショット](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>次のステップ
* Linux エージェントと拡張機能のしくみの詳細については、[Linux 用の Azure VM 拡張機能とその機能](features-linux.md)に関する記事を参照してください。
* Windows ゲスト エージェントと拡張機能のしくみの詳細については、[Windows 用の Azure VM 拡張機能とその機能](features-windows.md)に関する記事を参照してください。  
* Windows ゲスト エージェントをインストールする場合は、[Azure Windows 仮想マシン エージェントの概要](agent-windows.md)に関するページをご覧ください。  
* Linux エージェントをインストールする場合は、[Azure Linux 仮想マシン エージェントの概要](agent-linux.md)に関するページをご覧ください。  

