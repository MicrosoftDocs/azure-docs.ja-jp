---
title: Windows 用 Azure パフォーマンス診断 VM 拡張機能 | Microsoft Docs
description: Windows 用の Azure パフォーマンス診断 VM 拡張機能について説明します。
services: virtual-machines-windows'
documentationcenter: ''
author: genlin
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 769305cc3d838832f8f445ac9623a1724603f968
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58002735"
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Windows 用 Azure パフォーマンス診断 VM 拡張機能

Azure パフォーマンス診断 VM 拡張機能は、Windows VM からパフォーマンス診断データを収集するのに役立ちます。 この拡張機能では、分析を実行し、仮想マシンでのパフォーマンスの問題を特定して解決するための結果と推奨事項のレポートを提供します。 この拡張機能では、[PerfInsights](https://aka.ms/perfinsights) と呼ばれるトラブルシューティング ツールがインストールされます。

> [!NOTE]
> クラシックではない VM に対して Azure portal から VM の診断を実行する場合は、新しいエクスペリエンスを使用することをお勧めします。 詳細については、「[Performance Diagnostics for Azure virtual machines](performance-diagnostics.md)」(Azure 仮想マシンのパフォーマンス診断) を参照してください。 

## <a name="prerequisites"></a>前提条件

この拡張機能は、Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2、および Windows Server 2016 にインストールできます。 また、Windows 8.1 および Windows 10 にインストールすることもできます。

## <a name="extension-schema"></a>拡張機能のスキーマ
次に示す JSON は、Azure パフォーマンス診断 VM 拡張機能のスキーマです。 この拡張機能では、診断の出力とレポートを保存するために、ストレージ アカウントの名前とキーが必要です。 これらは機密性の高い値です。 ストレージ アカウント キーは、保護された設定構成内に格納する必要があります。 Azure VM 拡張機能の保護された設定データは暗号化され、ターゲットの仮想マシンでのみ、暗号化が解除されます。 **storageAccountName** と **storageAccountKey** は大文字と小文字が区別されることに注意してください。 他の必要なパラメーターについては、以下のセクションで示します。

```JSON
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "performanceScenario": "[parameters('performanceScenario')]",
            "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
            "perfCounterTrace": "[parameters('perfCounterTrace')]",
            "networkTrace": "[parameters('networkTrace')]",
            "xperfTrace": "[parameters('xperfTrace')]",
            "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]",
            "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"        
        }
      }
    }
```

### <a name="property-values"></a>プロパティ値

|   **Name**   |**値/例**|       **説明**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|API のバージョン。
|publisher|Microsoft.Azure.Performance.Diagnostics|拡張機能の発行元名前空間。
|type|AzurePerformanceDiagnostics|VM 拡張機能の種類。
|typeHandlerVersion|1.0|拡張機能ハンドラーのバージョン。
|performanceScenario|basic|データをキャプチャするパフォーマンス シナリオ。 有効な値: **basic**、**vmslow**、**azurefiles**、**custom**。
|traceDurationInSeconds|300|いずれかのトレース オプションを選択した場合、トレースの期間。
|perfCounterTrace|p|パフォーマンス カウンターのトレースを有効にするオプション。 有効な値: **p**、または空の値。 このトレースをキャプチャしない場合は、値を空のままにします。
|networkTrace|n|ネットワーク トレースを有効にするオプション。 有効な値は、**n** または空の値です。 このトレースをキャプチャしない場合は、値を空のままにします。
|xperfTrace|x|XPerf のトレースを有効にするオプション。 有効な値: **x**、または空の値。 このトレースをキャプチャしない場合は、値を空のままにします。
|storPortTrace|s|StorPort のトレースを有効にするオプション。 有効な値は、**s** または空の値です。 このトレースをキャプチャしない場合は、値を空のままにします。
|srNumber|123452016365929|サポート チケット番号 (ある場合)。 ない場合は、値を空のままにします。
|requestTimeUtc|2017-09-28T22:08:53.736Z|現在の日時 (UTC)。 ポータルを使ってこの拡張機能をインストールしている場合は、この値を指定する必要はありません。
|resourceId|/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}|VM の一意の識別子。
|storageAccountName|mystorageaccount|診断ログと結果を格納するストレージ アカウントの名前。
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|ストレージ アカウントのキー。

## <a name="install-the-extension"></a>拡張機能のインストール

Windows 仮想マシンにこの拡張機能をインストールするには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. この拡張機能をインストールする仮想マシンを選びます。

    ![仮想マシンが強調表示されている Azure Portal のスクリーンショット](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. **[拡張機能]** ブレード、**[追加]** の順に選択します。

    ![[追加] が強調表示されている [拡張機能] ブレードのスクリーンショット](media/performance-diagnostics-vm-extension/select-extensions.png)
4. **[Azure Performance Diagnostics]\(Azure パフォーマンス診断\)** を選択し、使用条件を確認して、**[作成]** を選択します。

    ![[Azure Performance Diagnostics]\(Azure パフォーマンス診断\) が強調表示されている [新しいリソース] 画面のスクリーンショット](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. インストールのパラメーター値を指定し、**[OK]** をクリックして拡張機能をインストールします。 サポートされるシナリオの詳細については、「[PerfInsights を使用する方法](how-to-use-perfInsights.md#supported-troubleshooting-scenarios)」を参照してください。 

    ![[拡張機能のインストール] ダイアログ ボックスのスクリーンショット](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. 正常にインストールされると、次の状態を示すメッセージが表示されます。

    !["プロビジョニング成功" メッセージのスクリーンショット](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > プロビジョニングが成功すると、拡張機能が実行されます。 基本的なシナリオの場合、2 分以内で完了します。 他のシナリオの場合は、インストール中に指定した期間だけ実行されます。

## <a name="remove-the-extension"></a>拡張機能を削除する
仮想マシンから拡張機能を削除するには、以下の手順のようにします。

1. [Azure Portal](https://portal.azure.com) にサインインし、この拡張機能を削除する仮想マシンを選択し、**[拡張機能]** ブレードを選択します。 
2. 一覧でパフォーマンス診断拡張機能のエントリの **[...]** をクリックし、**[アンインストール]** を選択します。

    ![[アンインストール] が強調表示されている [拡張機能] ブレードのスクリーンショット](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > 拡張機能のエントリを選び、**[アンインストール]** オプションを選んでもかまいません。

## <a name="template-deployment"></a>テンプレートのデプロイ
Azure 仮想マシン拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 前のセクションで詳しく説明した JSON スキーマを Azure Resource Manager テンプレートで使用できます。 その場合、Azure Resource Manager テンプレートのデプロイ時に Azure パフォーマンス診断 VM 拡張機能が実行されます。 サンプル テンプレートを次に示します。

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "yourVMName"
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus"
    },
    "storageAccountName": {
      "type": "securestring"
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring"
      "defaultValue": "yourStorageAccountKey"
    },
    "performanceScenario": {
      "type": "string",
      "defaultValue": "basic"
    },
    "srNumber": {
      "type": "string",
      "defaultValue": ""
    },
    "traceDurationInSeconds": {
      "type": "int",
    "defaultValue": 300
    },
    "perfCounterTrace": {
      "type": "string",
      "defaultValue": "p"
    },
    "networkTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "xperfTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "storPortTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "requestTimeUtc": {
      "type": "string",
      "defaultValue": "10/2/2017 11:06:00 PM"
    }       
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "performanceScenario": "[parameters('performanceScenario')]",
            "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
            "perfCounterTrace": "[parameters('perfCounterTrace')]",
            "networkTrace": "[parameters('networkTrace')]",
            "xperfTrace": "[parameters('xperfTrace')]",
            "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]",
            "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

## <a name="powershell-deployment"></a>PowerShell でのデプロイ
`Set-AzVMExtension` コマンドを使って、Azure パフォーマンス診断 VM 拡張機能を既存の仮想マシンにデプロイすることができます。

PowerShell

```
$PublicSettings = @{ "storageAccountName"="mystorageaccount";"performanceScenario"="basic";"traceDurationInSeconds"=300;"perfCounterTrace"="p";"networkTrace"="";"xperfTrace"="";"storPortTrace"="";"srNumber"="";"requestTimeUtc"="2017-09-28T22:08:53.736Z";"resourceId"="VMResourceId" }
$ProtectedSettings = @{"storageAccountKey"="mystoragekey" }

Set-AzVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS
```

## <a name="information-on-the-data-captured"></a>キャプチャされるデータについて
PerfInsights ツールは、選んだシナリオに応じて、さまざまなログ、構成、診断データを収集します。 詳細については、[PerfInsights のドキュメント](https://aka.ms/perfinsights)をご覧ください。

## <a name="view-and-share-the-results"></a>結果を表示および共有する

拡張機能の出力は、インストール時に指定したストレージ アカウントにアップロードした zip ファイルに含まれます。このファイルは [Shared Access Signatures (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) を使用して 30 日間共有されます。 この zip ファイルには、診断ログと、結果と推奨事項に関するレポートが含まれています。 出力 zip ファイルへの SAS リンクは、**C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\\\<version>** フォルダー以下の *zipfilename*_saslink.txt というテキスト ファイル内で確認できます。 このリンクがあれば、誰でも zip ファイルをダウンロードできます。

サポート チケットの作業を行うサポート エンジニアを支援するために、Microsoft がこの SAS リンクを使って診断データをダウンロードする場合があります。

レポートを確認するには、zip ファイルを展開し、**PerfInsights Report.html** ファイルを開きます。

拡張機能を選んで、ポータルから zip ファイルを直接ダウンロードすることもできます。

![パフォーマンス診断の詳細な状態のスクリーンショット](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> ポータルに表示されている SAS リンクが機能しない場合があります。 原因として、エンコードおよびデコード操作中に URL が不正な形式になったことが考えられます。 代わりに、VM の *_saslink.txt ファイルから直接リンクを取得できます。

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

- 拡張機能が正常にプロビジョニングされた場合でも、拡張機能のデプロイの状態 (通知領域) に "デプロイは進行中です" と表示されることがあります。

    拡張機能の状態で拡張機能が正常にプロビジョニングされたことが示されている限り、この問題は無視しても安全です。
- インストール中の問題の一部は、拡張ログを使って対処できます。 拡張機能の実行の出力は、次のディレクトリ内のファイルにログ記録されます。

        C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、**[サポートの要求]** をクリックします。 Azure サポートの使用方法の詳細については、「[Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。
