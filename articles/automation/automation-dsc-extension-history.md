---
title: Azure Desired State Configuration 拡張機能のバージョン履歴を使用する
description: この記事では、Azure の Desired State Configuration (DSC) 拡張機能のバージョン履歴の情報を共有しています。
ms.date: 02/17/2021
keywords: dsc, powershell, azure, 拡張機能
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.openlocfilehash: e51bce6e015ef7367625b010b74e5d2422b35051
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651804"
---
# <a name="work-with-azure-desired-state-configuration-extension-version-history"></a>Azure Desired State Configuration 拡張機能のバージョン履歴を使用する

Azure、Windows Server、および Windows Management Framework (WMF) (Windows PowerShell など) から提供される拡張機能と新機能をサポートするために、Azure Desired State Configuration (DSC) VM [拡張機能](../virtual-machines/extensions/dsc-overview.md)は必要に応じて更新されています。

この記事では、Azure DSC VM 拡張機能の各バージョンに関する情報、サポートする環境、新しい機能や変更に関するコメントや注意事項について説明します。

## <a name="latest-version"></a>最新バージョン

### <a name="version-283"></a>バージョン 2.83

- **リリース日:**
  - 2021 年 2 月
- **OS のサポート:**
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows クライアント 7/8.1/10
  - Nano Server
- **WMF のサポート:**
  - WMF 5.1
  - WMF 5.0 RTM
  - WMF 4.0 Update
  - WMF 4.0
- **環境:**
  - Azure
  - Azure China Vianet 21
  - Azure Government
- **備考:** このリリースには、Windows VM 拡張機能を含む未署名バイナリの修正プログラムが含まれています。

### <a name="version-280"></a>バージョン 2.80

- **リリース日:**
  - 2019 年 9 月 26 日 (Azure) | 2020 年 7 月 6 日 (Azure China Vianet 21) | 2020 年 7月 20 日 (Azure Government)
- **OS のサポート:**
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows クライアント 7/8.1/10
  - Nano Server
- **WMF のサポート:**
  - WMF 5.1
  - WMF 5.0 RTM
  - WMF 4.0 Update
  - WMF 4.0
- **環境:**
  - Azure
  - Azure China Vianet 21
  - Azure Government
- **備考:** このリリースに含まれる新機能はありません。

### <a name="version-276"></a>バージョン 2.76

- **リリース日:**
  - 2018 年 5 月 9 日 (Azure) | 2018 年 6 月 21 日 (Azure China Vianet 21、Azure Government)
- **OS のサポート:**
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows クライアント 7/8.1/10
  - Nano Server
- **WMF のサポート:**
  - WMF 5.1
  - WMF 5.0 RTM
  - WMF 4.0 Update
  - WMF 4.0
- **環境:**
  - Azure
  - Azure China Vianet 21
  - Azure Government
- **備考:** このバージョンでは、Windows Server 2016 に含まれる DSC を使用しています。他の Windows OS の場合は、[Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) がインストールされます (WMF をインストールするには再起動が必要です)。 Nano Server の場合は、DSC ロールが VM にインストールされます。
- **新機能:**
  - 副状態やその他の軽微なバグ修正に関する拡張メタデータが改善されました。

## <a name="supported-versions"></a>サポートされているバージョン

> [!WARNING]
> バージョン 2.4 から 2.13 では、2016 年 8 月に署名証明書が失効した WMF 5.0 パブリック プレビューを使用しています。
> この問題の詳細については、次の[ブログ記事](https://devblogs.microsoft.com/powershell/azure-dsc-extension-versions-2-4-up-to-2-13-will-retire-in-august/)を参照してください。

### <a name="version-275"></a>バージョン 2.75

- **リリース日:** 2018 年 3 月 5 日
- **OS のサポート:** Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1、Windows クライアント 7/8.1/10、Nano Server
- **WMF のサポート:** WMF 5.1、WMF 5.0 RTM、WMF 4.0 Update、WMF 4.0
- **環境:** Azure
- **備考:** このバージョンでは、Windows Server 2016 に含まれる DSC を使用しています。他の Windows OS の場合は、[Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) がインストールされます (WMF をインストールするには再起動が必要です)。 Nano Server の場合は、DSC ロールが VM にインストールされます。
- **新機能:**
  - GitHub での TLS 1.2 の適用以降、Azure Marketplace で入手できる DIY Resource Manager テンプレートを使用して VM を Azure Automation State Configuration にオンボードすることができなくなりました。また DSC 拡張機能を使用して GitHub でホストされている構成を取得できなくなりました。 拡張機能を展開する際に、次のようなエラーが返されます。

    ```json
    {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
        "details": [{
            "code": "Conflict",
            "message": "{
                \"status\": \"Failed\",
                \"error\": {
                    \"code\": \"ResourceDeploymentFailure\",
                    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",
                    \"details\": [ {
                        \"code\": \"VMExtensionProvisioningError\",
                        \"message\": \"VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'.
                        Error message: \\\"The DSC Extension failed to execute: Error downloading
                        https://github.com/Azure/azure-quickstart-templates/raw/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip
                        after 29 attempts: The request was aborted: Could not create SSL/TLS secure channel..\\nMore information about the failure can
                        be found in the logs located under 'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.74.0.0' on the VM.\\\".\"
                    } ]
                }
            }"
        }]
    }
    ```

  - 新しい拡張機能のバージョンでは、TLS 1.2 が適用されるようになりました。 拡張機能を展開するときに Resource Manager テンプレートで既に `AutoUpgradeMinorVersion = true` を設定している場合、拡張機能は 2.75 に自動アップグレードされます。 手動で更新するには、Resource Manager テンプレートで `TypeHandlerVersion = 2.75` を指定してください。

### <a name="version-270---272"></a>バージョン 2.70 から 2.72

- **リリース日:** 2017 年 11 月 13 日
- **OS のサポート:** Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1、Windows クライアント 7/8.1/10、Nano Server
- **WMF のサポート:** WMF 5.1、WMF 5.0 RTM、WMF 4.0 Update、WMF 4.0
- **環境:** Azure
- **備考:** このバージョンでは、Windows Server 2016 に含まれる DSC を使用しています。他の Windows OS の場合は、[Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) がインストールされます (WMF をインストールするには再起動が必要です)。 Nano Server の場合は、DSC ロールが VM にインストールされます。
- **新機能:**
  - バグ修正と改善により、ポータルと Resource Manager テンプレートでの Azure Automation State Configuration の使用が簡略化されました。 詳細については、DSC 拡張機能ドキュメントの「[既定の構成スクリプト](../virtual-machines/extensions/dsc-overview.md)」を参照してください。

### <a name="version-226"></a>バージョン 2.26

- **リリース日:** 2017 年 6 月 9 日
- **OS のサポート:** Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1、Windows クライアント 7/8.1/10、Nano Server
- **WMF のサポート:** WMF 5.1、WMF 5.0 RTM、WMF 4.0 Update、WMF 4.0
- **環境:** Azure
- **備考:** このバージョンでは、Windows Server 2016 に含まれる DSC を使用しています。他の Windows OS の場合は、[Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) がインストールされます (WMF をインストールするには再起動が必要です)。 Nano Server の場合は、DSC ロールが VM にインストールされます。
- **新機能:**
  - テレメトリの改善。

### <a name="version-225"></a>バージョン 2.25

- **リリース日:** 2017 年 6 月 2 日
- **OS のサポート:** Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1、Windows クライアント 7/8.1/10、Nano Server
- **WMF のサポート:** WMF 5.1、WMF 5.0 RTM、WMF 4.0 Update、WMF 4.0
- **環境:** Azure
- **備考:** このバージョンでは、Windows Server 2016 に含まれる DSC を使用しています。他の Windows OS の場合は、[Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) がインストールされます (WMF をインストールするには再起動が必要です)。 Nano Server の場合は、DSC ロールが VM にインストールされます。
- **新機能:**
  - いくつかのバグ修正とその他の軽微な改善が加えられました。

### <a name="version-224"></a>バージョン 2.24

- **リリース日:** 2017 年 4 月 13 日
- **OS のサポート:** Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1、Nano Server
- **WMF のサポート:** WMF 5.1、WMF 5.0 RTM、WMF 4.0 Update、WMF 4.0
- **環境:** Azure
- **備考:** このバージョンでは、Windows Server 2016 に含まれる DSC を使用しています。他の Windows OS の場合は、[Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) がインストールされます (WMF をインストールするには再起動が必要です)。 Nano Server の場合は、DSC ロールが VM にインストールされます。
- **新機能:**
  - VM UUID と DSC エージェント ID を拡張機能のメタデータとして公開します。 その他の軽微な改善が加えられました。

### <a name="version-223"></a>バージョン 2.23

- **リリース日:** 2017 年 3 月 15 日
- **OS のサポート:** Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1、Nano Server
- **WMF のサポート:** WMF 5.1、WMF 5.0 RTM、WMF 4.0 Update、WMF 4.0
- **環境:** Azure
- **備考:** このバージョンでは、Windows Server 2016 に含まれる DSC を使用しています。他の Windows OS の場合は、[Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) がインストールされます (WMF をインストールするには再起動が必要です)。 Nano Server の場合は、DSC ロールが VM にインストールされます。
- **新機能:**
  - バグ修正とその他の改善が加えられました。

### <a name="version-222"></a>バージョン 2.22

- **リリース日:** 2017 年 2 月 8 日
- **OS のサポート:** Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1、Nano Server
- **WMF のサポート:** WMF 5.1、WMF 5.0 RTM、WMF 4.0 Update、WMF 4.0
- **環境:** Azure
- **備考:** このバージョンでは、Windows Server 2016 に含まれる DSC を使用しています。他の Windows OS の場合は、[Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) がインストールされます (WMF をインストールするには再起動が必要です)。 Nano Server の場合は、DSC ロールが VM にインストールされます。
- **新機能:**
  - DSC 拡張機能で WMF 5.1 がサポートされるようになりました。
  - その他の軽微な改善が加えられました。

### <a name="version-221"></a>バージョン 2.21

- **リリース日:** 2016 年 12 月 2 日
- **OS のサポート:** Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1、Nano Server
- **WMF のサポート:** WMF 5.1 Preview、WMF 5.0 RTM、WMF 4.0 Update、WMF 4.0
- **環境:** Azure
- **備考:** このバージョンでは、Windows Server 2016 に含まれる DSC を使用しています。他の Windows OS の場合は、[Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) がインストールされます (WMF をインストールするには再起動が必要です)。 Nano Server の場合は、DSC ロールが VM にインストールされます。
- **新機能:**
  - Nano Server で DSC 拡張機能を使用できるようになりました。 このバージョンには、Nano Server でこの拡張機能を実行するためのコードの変更が含まれています。
  - その他の軽微な改善が加えられました。

### <a name="version-220"></a>バージョン 2.20

- **リリース日:** 2016 年 8 月 2 日
- **OS のサポート:** Windows Server 2016 Technical Preview、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1
- **WMF のサポート:** WMF 5.1 Preview、WMF 5.0 RTM、WMF 4.0 Update、WMF 4.0
- **環境:** Azure
- **備考:** このバージョンでは、Windows Server 2016 Technical Preview に含まれる DSC を使用しています。他の Windows OS の場合は、[Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) がインストールされます (WMF をインストールするには再起動が必要です)。
- **新機能:**
  - WMF 5.1 プレビューのサポート。 このバージョンが最初に公開されたときは任意のアップグレードであり、WMF 5.1 プレビューをインストールするには Resource Manager テンプレートで Wmfversion = '5.1PP' を指定する必要がありました。
    Wmfversion = 'latest' を指定すると、現在でも [WMF 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) がインストールされます。
    WMF 5.1 プレビューの詳細については、[こちらのブログ](https://devblogs.microsoft.com/powershell/announcing-windows-management-framework-wmf-5-1-preview/)を参照してください。
  - 他にも軽微な修正や改善が加えられました。

### <a name="version--219"></a>バージョン 2.19

- **リリース日:** 2016 年 6 月 3 日
- **OS のサポート:** Windows Server 2016 Technical Preview、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1
- **WMF のサポート:** WMF 5.0 RTM、WMF 4.0 Update、WMF 4.0
- **環境:** Azure、Azure China Vianet 21、Azure Government
- **備考:** このバージョンでは、Windows Server 2016 Technical Preview に含まれる DSC を使用しています。他の Windows OS の場合は、[Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) がインストールされます (WMF をインストールするには再起動が必要です)。
- **新機能:**
  - Azure China Vianet 21 で DSC 拡張機能を使用できるようになりました。 このバージョンには、Azure China Vianet 21 上でこの拡張機能を実行するための修正が含まれています。

### <a name="version-218"></a>バージョン 2.18

- **リリース日:** 2016 年 6 月 3 日
- **OS のサポート:** Windows Server 2016 Technical Preview、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1
- **WMF のサポート:** WMF 5.0 RTM、WMF 4.0 Update、WMF 4.0
- **環境:** Azure
- **備考:** このバージョンでは、Windows Server 2016 Technical Preview に含まれる DSC を使用しています。他の Windows OS の場合は、[Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) がインストールされます (WMF をインストールするには再起動が必要です)。
- **新機能:**
  - テレメトリの修正プログラムのダウンロード中 (既知の Azure DNS の問題) またはインストール中にエラーが発生した場合、テレメトリがブロック不可になります。
  - 再起動後に拡張機能で構成の処理が停止する断続的な問題の発生を修正します。 この問題で、DSC 拡張機能は '移行中' の状態のままになっていました。
  - 他にも軽微な修正や改善が加えられました。

### <a name="version-217"></a>バージョン 2.17

- **リリース日:** 2016 年 4 月 26 日
- **OS のサポート:** Windows Server 2016 Technical Preview、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1
- **WMF のサポート:** WMF 5.0 RTM、WMF 4.0 Update、WMF 4.0
- **環境:** Azure
- **備考:** このバージョンでは、Windows Server 2016 Technical Preview に含まれる DSC を使用しています。他の Windows OS の場合は、[Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) がインストールされます (WMF をインストールするには再起動が必要です)。
- **新機能:**
  - WMF 4.0 Update のサポート。 WMF 4.0 Update の詳細については、[こちらのブログ](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-4-0-update-now-available-for-windows-server-2012-windows-server-2008-r2-sp1-and-windows-7-sp1/)を参照してください。
  - DSC 拡張機能のインストール中または拡張機能のインストール後の DSC 構成の適用中にエラーが発生した場合、ロジックを再試行します。 この変更の一環として、前回のインストールが失敗した場合にインストールが再試行され、以前に失敗した DSC の構成が再実行されるようになります。拡張機能のインストールが完了状態 (成功/エラー) に達するまで、または新しい要求が実行されるまで、最大 3 回実行されます。 無効なユーザー設定またはユーザー入力によって拡張機能が失敗した場合は、再試行されません。 この場合、新しい要求と正しいユーザー設定で拡張機能を再度呼び出す必要があります。 

  > [!NOTE]
   > DSC 拡張機能は、再試行については Azure VM エージェントに依存しています。 Azure VM エージェントは、成功状態またはエラー状態に達するまで、最後に失敗した要求を含む拡張機能を呼び出します。

### <a name="version-216"></a>2\.16 のバージョン

- **リリース日:** 2016 年 4 月 21 日
- **OS のサポート:** Windows Server 2016 Technical Preview、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1
- **WMF のサポート:** WMF 5.0 RTM、WMF 4.0
- **環境:** Azure
- **備考:** このバージョンでは、Windows Server 2016 Technical Preview に含まれる DSC を使用しています。他の Windows OS の場合は、[Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) がインストールされます (WMF をインストールするには再起動が必要です)。
- **新機能:**
  - エラー処理が改善され、その他の軽微なバグが修正されました。
  - DSC 拡張機能設定のプロパティが追加されました。 AdvancedOptions に `ForcePullAndApply` が追加され、更新モードが (既定のプッシュ モードではなく) プルの場合、DSC 拡張機能で DSC 構成が有効になります。 DSC 拡張機能の設定の詳細については、[こちらのブログ](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/)を参照してください。

### <a name="version-215"></a>バージョン 2.15

- **リリース日:** 2016 年 3 月 14 日
- **OS のサポート:** Windows Server 2016 Technical Preview、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1
- **WMF のサポート:** WMF 5.0 RTM、WMF 4.0
- **環境:** Azure
- **備考:** このバージョンでは、Windows Server 2016 Technical Preview に含まれる DSC を使用しています。他の Windows OS の場合は、[Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) がインストールされます (WMF をインストールするには再起動が必要です)。
- **新機能:**
  - 拡張機能バージョン 2.14 には、WMF RTM をインストールするための変更が含まれていました。 拡張バージョン 2.13.2.0 から 2.14.0.0 へのアップグレード中に、一部の DSC コマンドレットが失敗することや、構成が "指定されたプロパティ値のインスタンスが見つかりません" というエラーで失敗することがあります。 詳細については、[DSC のリリース ノート](/powershell/scripting/wmf/known-issues/known-issues-dsc)を参照してください。 これらの問題の回避策が 2.15 バージョンで追加されました。 
  - 既にバージョン 2.14 をインストールし、上記の 2 つの問題のいずれかが発生している場合は、これらの手順を手動で実行する必要があります。 管理者特権の PowerShell セッションで、次のコマンドを実行します。
    - `Remove-Item -Path $env:SystemRoot\system32\Configuration\DSCEngineCache.mof`
    - `mofcomp $env:windir\system32\wbem\DscCoreConfProv.mof`

### <a name="version-214"></a>バージョン 2.14

- **リリース日:** 2016 年 2 月 25 日
- **OS のサポート:** Windows Server 2016 Technical Preview、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1
- **WMF のサポート:** WMF 5.0 RTM、WMF 4.0
- **環境:** Azure
- **備考:** このバージョンでは、Windows Server 2016 Technical Preview に含まれる DSC を使用しています。他の Windows OS の場合は、[Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) がインストールされます (WMF をインストールするには再起動が必要です)。
- **新機能:**
  - WMF RTM を使用します。
  - DSC 拡張機能の品質を改善するために、データ収集を有効にします。 詳細については、[こちらのブログ記事](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/)を参照してください。
  - Resource Manager テンプレートの拡張機能の設定形式を更新します。 詳細については、[こちらのブログ記事](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/)を参照してください。
  - バグの修正とその他の機能強化。

## <a name="next-steps"></a>次のステップ

- PowerShell DSC の詳細については、[PowerShell ドキュメント センター](/powershell/scripting/dsc/overview/overview)を参照してください。
- [DSC 拡張機能用の Resource Manager テンプレート](../virtual-machines/extensions/dsc-template.md)を確認します。
- PowerShell DSC で管理できるその他の機能とリソースの詳細については、[PowerShell ギャラリー](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)を閲覧してください。
- 機密パラメーターを構成に渡す方法の詳細については、[DSC 拡張機能ハンドラーで資格情報を安全に管理する方法](../virtual-machines/extensions/dsc-credentials.md)に関するページを参照してください。
