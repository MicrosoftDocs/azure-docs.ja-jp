---
title: Windows VM 拡張機能のエラーのトラブルシューティング | Microsoft Docs
description: Azure Windows VM 拡張機能のエラーのトラブルシューティングについて説明します
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: cf53df30dfccb76a6f33621038ba7f031a69f6de
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2019
ms.locfileid: "55979695"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Azure Windows VM 拡張機能のエラーのトラブルシューティング
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>拡張機能の状態表示
Azure リソース マネージャー テンプレートは、Azure Powershell から実行できます。 このテンプレートを実行すると、Azure Resource Explorer またはコマンド ライン ツールから拡張機能の状態を表示できます。

たとえば次のようになります。

Azure PowerShell:

      Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status

出力例を次に示します。

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## <a name="troubleshooting-extension-failures"></a>拡張機能のエラーのトラブルシューティング
### <a name="rerun-the-extension-on-the-vm"></a>VM での拡張機能の再実行
VM でカスタム スクリプト拡張機能を使用してスクリプトを実行している場合、VM は正常に作成されたがスクリプトは失敗するというエラーが発生することがあります。 このような状況でこのエラーから回復するためにお勧めする方法は、拡張機能を削除してもう一度テンプレートを実行することです。
注:将来は、この機能が強化され、拡張機能をアンインストールする必要はなくなります。

#### <a name="remove-the-extension-from-azure-powershell"></a>Azure Powershell から拡張機能を削除する
    Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

拡張機能を削除した後、テンプレートを再実行して、VM 上でスクリプトを実行できます。

