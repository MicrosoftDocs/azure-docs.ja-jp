---
title: 非準拠の Azure Automation State Configuration サーバーを修復する
description: この記事では、構成状態がドリフトになっているサーバーにオンデマンドで構成を再適用する方法について説明します。
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: 98164c20af2a5d6ae7ccac43331a2876c23ccfb7
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102500174"
---
# <a name="remediate-noncompliant-azure-automation-state-configuration-servers"></a>非準拠の Azure Automation State Configuration サーバーを修復する

サーバーが Azure Automation State Configuration に登録されるとき、構成モードが `ApplyOnly`、`ApplyandMonitor`、または `ApplyAndAutoCorrect` に設定されます。 モードが `ApplyAndAutoCorrect` に設定されていない場合、何らかの理由で準拠状態から逸脱したサーバーは、手動で修正されるまで非準拠のままです。

Azure コンピューティングでは、ユーザーが仮想マシン内でスクリプトを実行できるようにする実行コマンドという機能が提供されています。
このドキュメントでは、構成ドリフトを手動で補正する場合のこの機能のサンプル スクリプトを提供します。

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>PowerShell を使用して Windows 仮想マシンのドリフトを補正する

`Run` コマンドの機能を使用して、Windows 仮想マシンのドリフトを補正することができます。 「[実行コマンドを使用して Windows VM で PowerShell スクリプトを実行する](../virtual-machines/windows/run-command.md)」を参照してください。

Azure Automation State Configuration ノードで強制的に最新の構成をダウンロードして適用するには、[Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration) コマンドレットを使用します。

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Linux 仮想マシンのドリフトを補正する

Linux 仮想マシンの場合、`Run` コマンドを使用することはできません。 これらのマシンでは、登録プロセスを繰り返すことによってのみドリフトを補正できます。 

Azure ノードの場合は、Azure portal から、または Az モジュール コマンドレットを使用して、ドリフトを補正することができます。 このプロセスの詳細については、[Azure portal を使用した VM の有効化](automation-dsc-onboarding.md#enable-a-vm-using-azure-portal)に関するページをご覧ください。

ハイブリッド ノードの場合は、Python スクリプトを使用してドリフトを補正できます。 [Linux コンピューターからの DSC 操作の実行](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

- PowerShell コマンドレットのリファレンスについては、「[Az.Automation](/powershell/module/az.automation/#automation)」をご覧ください。
- 継続的なデプロイ パイプラインで Azure Automation State Configuration を使う例については、「[Chocolatey を使用して継続的配置を設定する](automation-dsc-cd-chocolatey.md)」をご覧ください。
