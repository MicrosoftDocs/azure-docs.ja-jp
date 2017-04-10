---
title: "Linux VM 拡張機能のエラーのトラブルシューティング | Microsoft Docs"
description: "Azure Linux VM 拡張機能のエラーのトラブルシューティングについて説明します。"
services: virtual-machines-linux
documentationcenter: 
author: kundanap
manager: timlt
editor: 
tags: top-support-issue,azure-resource-manager
ms.assetid: f05d93f3-42fc-4a09-9798-d92f7929c417
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: cdb2f59d227b3c39c4ee21bf3f383f223577bf49


---
# <a name="troubleshooting-azure-linux-vm-extension-failures"></a>Azure Linux VM 拡張機能のエラーのトラブルシューティング
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>拡張機能の状態表示
Azure Resource Manager テンプレートは、Azure CLI から実行できます。 このテンプレートを実行すると、Azure Resource Explorer またはコマンド ライン ツールから拡張機能の状態を表示できます。

たとえば次のようになります。

Azure CLI:

      azure vm get-instance-view


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

## <a name="troubleshooting-extenson-failures"></a>拡張機能のエラーのトラブルシューティング:
### <a name="re-running-the-extension-on-the-vm"></a>VM での拡張機能の再実行
VM でカスタム スクリプト拡張機能を使用してスクリプトを実行している場合、VM は正常に作成されたがスクリプトは失敗するというエラーが発生することがあります。 このような状況でこのエラーから回復するためにお勧めする方法は、拡張機能を削除してもう一度テンプレートに戻ることです。
注: 将来的には、拡張機能のアンインストールが不要になるように機能強化が行われるる予定です。

#### <a name="remove-the-extension-from-azure-cli"></a>Azure CLI からの拡張機能の削除
      azure vm extension set --resource-group "KPRG1" --vm-name "kundanapdemo" --publisher-name "Microsoft.Compute.CustomScriptExtension" --name "myCustomScriptExtension" --version 1.4 --uninstall

ここで、"publsher-name" は "azure vm get-instance-view" の出力の拡張機能の種類に対応し、name はテンプレートの拡張機能のリソース名に対応します。

拡張機能を削除した後、テンプレートを再実行して、VM 上でスクリプトを実行できます。




<!--HONumber=Nov16_HO3-->


