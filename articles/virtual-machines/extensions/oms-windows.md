---
title: Windows 用の Log Analytics 仮想マシン拡張機能
description: 仮想マシン拡張機能を使用して、Windows 仮想マシンに Log Analytics エージェントをデプロイします。
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 06/26/2020
ms.openlocfilehash: 7757bd765bcb02782b6199f71c4a6e460b7b8143
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559020"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>Windows 用の Log Analytics 仮想マシン拡張機能

Azure Monitor ログでは、クラウドとオンプレミスの資産全体にわたって監視機能を提供します。 Windows 用の Log Analytics エージェント仮想マシン拡張機能は、Microsoft によって発行およびサポートされています。 この拡張機能では、Azure 仮想マシンに Log Analytics エージェントがインストールされ、仮想マシンが既存の Log Analytics ワークスペースに登録されます。 このドキュメントでは、Windows 用の Log Analytics 仮想マシン拡張機能でサポートされているプラットフォーム、構成、デプロイ オプションについて詳しく説明します。

## <a name="prerequisites"></a>前提条件

### <a name="operating-system"></a>オペレーティング システム

サポートされる Windows オペレーティング システムの詳細については、「[ エージェントの概要](../../azure-monitor/agents/agents-overview.md#supported-operating-systems)」の記事を参照してください。

### <a name="agent-and-vm-extension-version"></a>エージェントおよび VM 拡張機能のバージョン
次の表は、Windows Log Analytics VM 拡張機能と Log Analytics エージェント バンドルのバージョンのマッピングをリリースごとに示しています。 

| Log Analytics Windows Agent バンドルのバージョン | Log Analytics Windows VM 拡張機能のバージョン | リリース日 | リリース ノート |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18053| 1.0.18053.0 | 2020 年 10 月   | <ul><li>新しいエージェント トラブルシューティング ツール</li><li>エージェントが Azure サービスに対する証明書の変更を処理する方法を更新</li></ul> |
| 10.20.18040 | 1.0.18040.2 | 2020 年 8 月   | <ul><li>Azure Arc の問題を解決</li></ul> |
| 10.20.18038 | 1.0.18038 | 2020 年 4 月   | <ul><li>Azure Monitor Private Link スコープを使用した Private Link 経由の接続を有効化</li><li>ワークスペースへのインジェストの急増を回避するインジェスト調整の追加</li><li>追加の Azure Government クラウドおよびリージョンのサポートを追加</li><li>HealthService.exe がクラッシュするバグの解消</li></ul> |
| 10.20.18029 | 1.0.18029 | 2020 年 3 月   | <ul><li>SHA-2 コード署名サポートを追加</li><li>VM 拡張機能のインストールと管理を改善</li><li>Azure Arc for Servers 統合のバグを解決</li><li>カスタマー サポート用の組み込みのトラブルシューティング ツールを追加</li><li>追加の Azure Government リージョンのサポートを追加</li> |
| 10.20.18018 | 1.0.18018 | 2019 年 10 月 | <ul><li> 軽微なバグの修正と安定性の改善 </li></ul> |
| 10.20.18011 | 1.0.18011 | 2019 年 7 月 | <ul><li> 軽微なバグの修正と安定性の改善 </li><li> MaxExpressionDepth を 10000 に引き上げ </li></ul> |
| 10.20.18001 | 1.0.18001 | 2019 年 6 月 | <ul><li> 軽微なバグの修正と安定性の改善 </li><li> プロキシ接続時に既定の資格情報を無効にする機能を追加 (WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH のサポート) </li></ul>|
| 10.19.13515 | 1.0.13515 | 2019 年 3 月 | <ul><li>軽微な安定化の修正 </li></ul> |
| 10.19.10006 | 該当なし | 2018 年 12 月 | <ul><li> 軽微な安定化の修正 </li></ul> | 
| 8.0.11136 | 該当なし | 2018 年 9 月 |  <ul><li> VM の移動時にリソース ID の変更を検出するサポートを追加しました </li><li> 非拡張インストールを使用するときにリソース ID を報告するためのサポートを追加しました </li></ul>| 
| 8.0.11103 | 該当なし |  2018 年 4 月 | |
| 8.0.11081 | 1.0.11081 | 2017 年 11 月 | | 
| 8.0.11072 | 1.0.11072 | 2017 年 9 月 | |
| 8.0.11049 | 1.0.11049 | 2017 年 2 月 | |


### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center は自動的に Log Analytics エージェントをプロビジョニングし、Azure サブスクリプションの既定の Log Analytics ワークスペースに接続します。 Azure Security Center を使用している場合は、このドキュメントの手順を実行しないでください。 実行すると、構成されているワークスペースが上書きされ、Azure Security Center との接続が中断されます。

### <a name="internet-connectivity"></a>インターネット接続
Windows 用の Log Analytics エージェント拡張機能では、ターゲットの仮想マシンがインターネットに接続されている必要があります。 

## <a name="extension-schema"></a>拡張機能のスキーマ

次の JSON は、Log Analytics エージェント拡張機能のスキーマを示しています。 この拡張機能では、ターゲット Log Analytics ワークスペースのワークスペース ID とワークスペース キーが必要です。 これらは Azure portal 内のワークスペースの設定で確認できます。 ワークスペース キーは機密データとして取り扱う必要があるため、保護された設定構成に格納される必要があります。 Azure VM 拡張機能の保護された設定データは暗号化され、ターゲットの仮想マシンでのみ、暗号化が解除されます。 **workspaceId** と **workspaceKey** の大文字と小文字は区別されることに注意してください。

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>プロパティ値

| 名前 | 値/例 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (例)* | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (例) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\* Log Analytics API では、workspaceId は consumerId という名称になります。

> [!NOTE]
> その他のプロパティについては、Azure の「[Windows コンピューターを Azure Monitor に接続する](../../azure-monitor/agents/agent-windows.md)」を参照してください。

## <a name="template-deployment"></a>テンプレートのデプロイ

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 前のセクションで詳しく説明した JSON スキーマを Azure Resource Manager テンプレートで使用すると、Azure Resource Manager テンプレートのデプロイ時に Log Analytics エージェント拡張機能を実行できます。 Log Analytics エージェント VM 拡張機能を含むサンプル テンプレートは、[Azure クイック スタート ギャラリー](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm)にあります。 

>[!NOTE]
>このテンプレートでは、複数のワークスペースに報告するようにエージェントを構成する場合に複数のワークスペース ID とワークスペース キーを指定することはサポートされていません。 複数のワークスペースに報告するようにエージェントを構成するには、「[ワークスペースの追加または削除](../../azure-monitor/agents/agent-manage.md#adding-or-removing-a-workspace)」を参照してください。  

仮想マシン拡張機能の JSON は、仮想マシン リソース内に入れ子にすることも、Resource Manager JSON テンプレートのルートまたは最上位レベルに配置することもできます。 JSON の配置は、リソースの名前と種類の値に影響します。 詳細については、[子リソースの名前と種類の設定](../../azure-resource-manager/templates/child-resource-name-type.md)に関する記事を参照してください。 

次の例では、Log Analytics 拡張機能が仮想マシン リソース内で入れ子になっていることを前提としています。 拡張機能リソースを入れ子にすると、JSON は仮想マシンの `"resources": []` オブジェクトに配置されます。


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

拡張機能 JSON をテンプレートのルートに配置すると、リソース名には親仮想マシンへの参照が含まれて、種類は入れ子になっている構成を反映します。 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>PowerShell でのデプロイ

`Set-AzVMExtension` コマンドを使用して、Log Analytics エージェント仮想マシン拡張機能を既存の仮想マシンにデプロイすることができます。 このコマンドを実行する前に、パブリック構成とプライベート構成を PowerShell ハッシュ テーブルに格納しておく必要があります。 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "MicrosoftMonitoringAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータを取得するには、Azure Portal または Azure PowerShell モジュールを使用します。 特定の VM の拡張機能のデプロイ状態を確認するには、Azure PowerShell モジュールを使用して次のコマンドを実行します。

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

拡張機能の実行の出力は、次のディレクトリ内のファイルにログ記録されます。

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。
