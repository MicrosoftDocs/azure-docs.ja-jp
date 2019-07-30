---
title: Linux 用の Azure Monitor 仮想マシン拡張機能 | Microsoft Docs
description: 仮想マシン拡張機能を使用して、Linux 仮想マシンに Log Analytics エージェントをデプロイします。
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: roiyz
ms.openlocfilehash: 7d8192a3b6ff732481a4d48f6e188b4bb3989cda
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705931"
---
# <a name="azure-monitor-virtual-machine-extension-for-linux"></a>Linux 用の Azure Monitor 仮想マシン拡張機能

## <a name="overview"></a>概要

Azure Monitor ログは、クラウドとオンプレミスの資産全体にまたがる監視、アラート、アラート修復の機能を提供します。 Linux 用の Log Analytics Agent 仮想マシン拡張機能は、Microsoft によって発行およびサポートされています。 この拡張機能では、Azure 仮想マシンに Log Analytics エージェントがインストールされ、仮想マシンが既存の Log Analytics ワークスペースに登録されます。 このドキュメントでは、Linux 用の Azure Monitor 仮想マシン拡張機能でサポートされているプラットフォーム、構成、デプロイ オプションについて詳しく説明します。

>[!NOTE]
>現在 Microsoft Operations Management Suite (OMS) から Azure Monitor への移行作業が進行していますが、その一環として、OMS エージェント for Windows と OMS エージェント for Linux は、それぞれ Log Analytics エージェント for Windows および Log Analytics エージェント for Linux という名称になります。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>前提条件

### <a name="operating-system"></a>オペレーティング システム

Log Analytics Agent 拡張機能は、次の Linux ディストリビューションに対して実行することができます。

| ディストリビューション | Version |
|---|---|
| CentOS Linux | 6 (x86/x64) および 7 (x64) |
| Amazon Linux | 2017.09 (x64) | 
| Oracle Linux | 6 および 7 (x86/x64) |
| Red Hat Enterprise Linux Server | 6 (x86/x64) および 7 (x64) |
| Debian GNU/Linux | 8 および 9 (x86/x64) |
| Ubuntu | 14.04 LTS (x86/x64)、16.04 LTS (x86/x64)、および 18.04 LTS (x64) |
| SUSE Linux Enterprise Server | 12 (x64) および 15 (x64) |

>[!NOTE]
>1\.x より前のバージョンの OpenSSL はどのプラットフォームでもサポートされていません。x86_64 プラットフォーム (64 ビット) でサポートされているバージョンは 1.10 のみです。  
>

### <a name="agent-prerequisites"></a>エージェントの前提条件

次の表には、エージェントがインストールされるサポートされている Linux ディストリビューションに必要なパッケージが明記されています。

|必須パッケージ |説明 |最小バージョン |
|-----------------|------------|----------------|
|Glibc |    GNU C ライブラリ | 2.5-12 
|Openssl    | OpenSSL ライブラリ | 1.0.x または 1.1.x |
|Curl | cURL Web クライアント | 7.15.5 |
|Python-ctypes | | 
|PAM | Pluggable Authentication Module (プラグ可能な認証モジュール) | | 

>[!NOTE]
>syslog メッセージを収集するには、rsyslog または syslog-ng が必要となります。 syslog イベントの収集に関して、バージョン 5 の Red Hat Enterprise Linux、CentOS、Oracle Linux 版の既定の syslog デーモン (sysklog) はサポートされません。 このバージョンの各種ディストリビューションから syslog データを収集するには、rsyslog デーモンをインストールし、sysklog を置き換えるように構成する必要があります。

### <a name="agent-and-vm-extension-version"></a>エージェントおよび VM 拡張機能のバージョン
次の表は、Azure Monitor VM 拡張機能と Log Analytics エージェント バンドルのバージョンのマッピングをリリースごとに示しています。 Log Analytics エージェント バンドルのバージョンのリリース ノートへのリンクが含まれます。 リリース ノートには、特定のエージェント リリースでのバグ修正と利用可能な新機能の詳細が記載されています。  

| Azure Monitor Linux VM 拡張機能のバージョン | Log Analytics Agent バンドルのバージョン | 
|--------------------------------|--------------------------|
| 1.11.9 | [1.11.0-7](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.11.0-7) |
| 1.10.0 | [1.10.0-1](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.10.0-1) |
| 1.9.1 | [1.9.0-0](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.9.0-0) |
| 1.8.11 | [1.8.1-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.8.1.256)| 
| 1.8.0 | [1.8.0-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/1.8.0-256)| 
| 1.7.9 | [1.6.1-3](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.1.3)| 
| 1.6.42.0 | [1.6.0-42](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.0-42)| 
| 1.4.60.2 | [1.4.4-210](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.4-210)| 
| 1.4.59.1 | [1.4.3-174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2-125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2-124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1-123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|
| 1.4.45.2 | [1.4.0-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.0-45)|
| 1.3.127.5 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)| 
| 1.3.127.7 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)|
| 1.3.18.7 | [1.3.4-15](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201704-v1.3.4-15)|  

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center は自動的に Log Analytics エージェントをプロビジョニングし、これを、Azure サブスクリプションで ASC によって作成される既定の Log Analytics ワークスペースに接続します。 Azure Security Center を使用している場合は、このドキュメントの手順を実行しないでください。 実行すると、構成されているワークスペースが上書きされ、Azure Security Center との接続が中断されます。

### <a name="internet-connectivity"></a>インターネット接続

Linux 用の Log Analytics Agent 拡張機能では、ターゲットの仮想マシンがインターネットに接続されている必要があります。 

## <a name="extension-schema"></a>拡張機能のスキーマ

次の JSON は、Log Analytics Agent 拡張機能のスキーマを示しています。 この拡張機能では、ターゲット Log Analytics ワークスペースのワークスペース ID とワークスペース キーが必要です (これらの値は Azure Portal の [Log Analytics ワークスペース](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key)で確認できます)。 ワークスペース キーは機密データとして取り扱う必要があるため、保護された設定構成に格納される必要があります。 Azure VM 拡張機能の保護された設定データは暗号化され、ターゲットの仮想マシンでのみ、暗号化が解除されます。 **workspaceId** と **workspaceKey** の大文字と小文字は区別されることに注意してください。

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

>[!NOTE]
>上のスキーマは、テンプレートのルート レベルに配置されると仮定しています。 テンプレートの仮想マシン リソース内に配置する場合は、[後で](#template-deployment)説明するように、`type` および `name` プロパティを変更する必要があります。
>

### <a name="property-values"></a>プロパティ値

| EnableAdfsAuthentication | 値/例 |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | OmsAgentForLinux |
| typeHandlerVersion | 1.7 |
| workspaceId (例) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (例) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>テンプレートのデプロイ

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 テンプレートは、デプロイ後の構成 (Azure Monitor ログへのオンボードなど) が必要な仮想マシンを 1 つ以上デプロイするときに最適です。 Log Analytics Agent VM 拡張機能を含む Resource Manager テンプレートのサンプルは、[Azure クイック スタート ギャラリー](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm)にあります。 

仮想マシン拡張機能の JSON 構成は、仮想マシン リソース内に入れ子にすることも、Resource Manager JSON テンプレートのルートまたは最上位レベルに配置することもできます。 JSON 構成の配置は、リソースの名前と種類の値に影響します。 詳細については、[子リソースの名前と種類の設定](../../azure-resource-manager/resource-group-authoring-templates.md#child-resources)に関する記事を参照してください。 

次の例では、VM 拡張機能が仮想マシン リソース内で入れ子になっていることを前提としています。 拡張機能リソースを入れ子にすると、JSON は仮想マシンの `"resources": []` オブジェクトに配置されます。

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

拡張機能 JSON をテンプレートのルートに配置すると、リソース名には親仮想マシンへの参照が含まれて、種類は入れ子になっている構成を反映します。  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI でのデプロイ

Azure CLI を使用して、Log Analytics Agent VM 拡張機能を既存の仮想マシンにデプロイすることができます。 *workspaceId* と *workspaceKey* を、ご使用の Log Analytics ワークスペースの値で置き換えます。 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.7 --protected-settings '{"workspaceKey": "omskey"}' \
  --settings '{"workspaceId": "omsid"}'
```

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータを取得するには、Azure Portal か Azure CLI を使用します。 特定の VM の拡張機能のデプロイ状態を確認するには、Azure CLI を使用して次のコマンドを実行します。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

拡張機能の実行の出力は、次のファイルにログ記録されます。

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>エラー コードとその意味

| エラー コード | 意味 | 可能なアクション |
| :---: | --- | --- |
| 9 | Enable が予定よりも早く呼び出されました | 入手できる最新のバージョンに [Azure Linux エージェントを更新](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent)します。 |
| 10 | VM は既に Log Analytics ワークスペースに接続されています | 拡張スキーマに指定されたワークスペースに VM を接続するには、パブリック設定で stopOnMultipleConnections を false に設定するか、このプロパティを削除します。 この VM は、各ワークスペースに接続された後、課金されます。 |
| 11 | 拡張機能に提供された構成が無効です | 前の例に従って、デプロイするために必要なすべてのプロパティ値を設定します。 |
| 17 | Log Analytics パッケージのインストールが失敗しました | 
| 19 | OMI パッケージのインストールが失敗しました | 
| 20 | SCX パッケージのインストールが失敗しました |
| 51 | VM のオペレーティング システムでは、この拡張機能はサポートされていません | |
| 55 | Azure Monitor サービスに接続できない、必要なパッケージが見つからない、または dpkg パッケージ マネージャーがロックされています| システムがインターネットにアクセスしていること、または有効な HTTP プロキシが指定されていることを確認します。 さらに、ワークスペース ID が正しいこと、および curl ユーティリティと tar ユーティリティがインストールされていることを確認します。 |

その他のトラブルシューティング情報については、[Log Analytics エージェントのトラブルシューティング](../../azure-monitor/platform/vmext-troubleshoot.md)に関する記事を参照してください。

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。
