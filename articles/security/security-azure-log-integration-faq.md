---
title: "Azure ログ統合の FAQ | Microsoft Docs"
description: "この FAQ は、Azure ログ統合について寄せられる質問とその回答です。"
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: d06d1ac5-5c3b-49de-800e-4d54b3064c64
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/07/2017
ms.author: TomSh
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 2d5201a335e96cc8595de834858750f6aac884a3
ms.lasthandoff: 03/17/2017


---
# <a name="azure-log-integration-frequently-asked-questions-faq"></a>Azure ログ統合のよく寄せられる質問 (FAQ)
この FAQ では、Azure ログ統合に関する質問に回答します。このサービスを使用すると、未加工のログを、Azure リソースからオンプレミスのセキュリティ情報/イベント管理 (SIEM) システムに統合できます。 この統合は、すべての資産に対してオンプレミスまたはクラウドの統合ダッシュボードを提供します。これにより、アプリケーションに関連付けられているセキュリティ イベントの集計、関連付け、分析を実行し、警告を生成できます。

## <a name="is-the-azure-log-integration-software-free"></a>Azure ログ統合ソフトウェアは無料ですか。
はい。 Azure ログ統合ソフトウェアに料金はかかりません。 

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs-from"></a>Azure ログ統合が Azure VM を取得しているストレージ アカウントを表示するには、どうすればよいですか。
**azlog source list**コマンドを実行します。

## <a name="how-can-i-update-the-proxy-configuration"></a>プロキシ構成を更新するには、どうすればよいですか。
プロキシ設定で Azure ストレージ アクセスが直接許可されていない場合は、**c:\Program Files\Microsoft Azure Log Integration** で **AZLOG.EXE.CONFIG** ファイルを開きます。 ファイルを更新して、 **defaultProxy** セクションに組織のプロキシ アドレスを追加します。 更新の完了後、サービスを停止するには **net stop azlog** コマンドを、開始するには **net start azlog** コマンドを使用します。

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress=http://127.0.0.1:8888
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Windows イベントのサブスクリプション情報を表示するには、どうすればよいですか。
ソースを追加しているときに、 **subscriptionid** を表示名に追加します。

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
イベント XML には、次に示すサブスクリプション ID などのメタデータが含まれます。

![イベント XML][1]

## <a name="error-messages"></a>エラー メッセージ
### <a name="when-running-command-azlog-createazureid-why-do-i-get-the-following-error"></a>**azlog createazureid**コマンドを実行すると、次のエラーが表示されるのはなぜですか。
エラー:

  *Failed to create AAD Application - Tenant 72f988bf-86f1-41af-91ab-2d7cd011db37 - Reason = 'Forbidden' - Message = 'Insufficient privileges to complete the operation.'*

**Azlog createazureid** は、Azure ログインでアクセスできるサブスクリプションの、すべての Azure AD テナントにサービス プリンシパルを作成しようとします。 その Azure AD テナントへの Azure ログインが、単なる Guest ユーザーとしてのログインである場合、コマンドは失敗し、[この操作を完了するのに十分な特権がありません] というメッセージが表示されます。 テナントのユーザーとしてアカウントを追加するようテナント管理者に要求してください。

### <a name="when-running-command-azlog-authorize-why-do-i-get-the-following-error"></a>**azlog authorize**コマンドを実行すると、次のエラーが表示されるのはなぜですか。
エラー:

  *Warning creating Role Assignment - AuthorizationFailed: The client janedo@microsoft.com' with object id 'fe9e03e4-4dad-4328-910f-fd24a9660bd2' does not have authorization to perform action 'Microsoft.Authorization/roleAssignments/write' over scope '/subscriptions/70d95299-d689-4c97-b971-0d8ff0000000'.*

**Azlog authorize** コマンドは、(**Azlog createazureid** で作成された) Azure AD サービス プリンシパルの閲覧者のロールを、提供されたサブスクリプションに割り当てます。 Azure ログインが、サブスクリプションの共同管理者または所有者としてのログインでない場合、ログインは失敗し、[承認に失敗しました] というエラー メッセージが表示されます。 この操作を完了するには、共同管理者または所有者の Azure ロールベースのアクセス制御 (RBAC) が必要です。

## <a name="where-can-i-find-the-definition-of-the-properties-in-audit-log"></a>監査ログのプロパティの定義はどこで確認できますか。
参照:

* [リソース マネージャーの監査操作](../azure-resource-manager/resource-group-audit.md)
* [Azure Monitor REST API でサブスクリプションの管理イベントの一覧を表示](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Azure Security Center の警告の詳細はどこで確認できますか。
「 [Azure Security Center でのセキュリティの警告の管理と対応](../security-center/security-center-managing-and-responding-alerts.md)」をご覧ください。

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>VM 診断で収集した情報を変更するには、どうすればよいですか。
Windows の Azure 診断 [(WAD)](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 構成を取得、変更、および設定する方法の詳細については、「 *PowerShell を使用して Windows を実行している仮想マシンで Azure 診断を有効にする* 」をご覧ください。 サンプルを次に示します。

### <a name="get-the-wad-config"></a>WAD 構成の取得
    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

### <a name="modify-the-wad-config"></a>WAD 構成の変更
次の例の構成では、EventID 4624 と EventID 4625 のみがセキュリティ イベント ログから収集されます。 Microsoft マルウェア対策のイベントは、システム イベント ログから収集されます。 XPath 式の使用の詳細については、「[Consuming Events (イベントの使用)](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85))」を参照してください。

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

### <a name="set-the-wad-configuration"></a>WAD 構成の設定
    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

変更後、ストレージ アカウントをチェックして、適切なイベントが収集されていることを確認します。

インストールおよび構成中に問題が発生した場合、[サポート要求](https://docs.microsoft.com/en-us/azure/azure-supportability/how-to-create-azure-support-request)を作成し、サポートを要求するサービスとして 'Log Integration' を選択します。


<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png

