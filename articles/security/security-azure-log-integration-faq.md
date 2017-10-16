---
title: "Azure ログ統合の FAQ | Microsoft Docs"
description: "この記事は、Azure ログ統合について寄せられる質問とその回答です。"
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
ms.workload8: na
ms.date: 08/07/2017
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: bfdc7154160bb6bb7dc9c46eb2352ce74310c4de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-log-integration-faq"></a>Azure ログ統合 のFAQ
この記事では、Azure ログ統合についてよく寄せられる質問 (FAQ) とその回答を紹介します。 

Azure ログ統合は Windows オペレーティング システムのサービスです。このサービスを利用すると、未加工のログを Azure リソースからオンプレミスの Security Information and Event Management (SIEM) システムに統合できます。 この統合によって、オンプレミスでもクラウド上でも、すべての資産を一元化されたダッシュボードで利用できるようになります。 そうすることで、お使いのアプリケーションに関連するセキュリティ イベントの集計、関連付け、分析、および警告を行えます。

## <a name="is-the-azure-log-integration-software-free"></a>Azure ログ統合ソフトウェアは無料ですか。
はい。 Azure ログ統合ソフトウェアに料金はかかりません。

## <a name="where-is-azure-log-integration-available"></a>Azure ログ統合はどこで利用できますか。

現時点では、Azure 商用サービスおよび Azure Government で利用できます。中国やドイツでは利用できません。

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs"></a>Azure ログ統合が Azure VM ログの取得元としているストレージ アカウントを表示するには、どうすればよいですか。
**azlog source list**コマンドを実行します。

## <a name="how-can-i-tell-which-subscription-the-azure-log-integration-logs-are-from"></a>Azure ログ統合でログの取得元となっているサブスクリプションはどのようにしてわかりますか。

**AzureResourcemanagerJson** ディレクトリに格納される監査ログの場合、サブスクリプション ID はログのファイル名で確認できます。 **AzureSecurityCenterJson** フォルダー内のログについても同様です。 For example:

20170407T070805_2768037.0000000023.**1111e5ee-1111-111b-a11e-1e111e1111dc**.json

Azure Active Directory 監査ログには、名前の一部として、テナント ID が含まれます。

イベント ハブから読み取られた診断ログには、名前の一部にサブスクリプション ID は含まれていません。 その代わりに、イベント ハブ ソースの作成の一環で指定されたフレンドリ名が含まれます。 

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
ソースを追加するときに、サブスクリプション ID をフレンドリ名に追加します。

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
イベント XML には、次に示すサブスクリプション ID などのメタデータが含まれます。

![イベント XML][1]

## <a name="error-messages"></a>エラー メッセージ
### <a name="when-i-run-the-command-azlog-createazureid-why-do-i-get-the-following-error"></a>**azlog createazureid** コマンドを実行すると、次のエラーが表示されるのはなぜですか。
エラー:

  *Failed to create AAD Application - Tenant 72f988bf-86f1-41af-91ab-2d7cd011db37 - Reason = 'Forbidden' - Message = 'Insufficient privileges to complete the operation.'*

**azlog createazureid** コマンドは、Azure ログインでアクセスできるサブスクリプションの、すべての Azure AD テナントにサービス プリンシパルを作成しようとします。 その Azure AD テナントへの Azure ログインが、単なるゲスト ユーザーとしてのログインである場合、コマンドは失敗し、"この操作を完了するのに十分な権限がありません" というメッセージが表示されます。 アカウントをテナントのユーザーとして追加するようテナント管理者に依頼してください。

### <a name="when-i-run-the-command-azlog-authorize-why-do-i-get-the-following-error"></a>**azlog authorize** コマンドを実行すると、次のエラーが表示されるのはなぜですか。
エラー:

  *Warning creating Role Assignment - AuthorizationFailed: The client janedo@microsoft.com' with object id 'fe9e03e4-4dad-4328-910f-fd24a9660bd2' does not have authorization to perform action 'Microsoft.Authorization/roleAssignments/write' over scope '/subscriptions/70d95299-d689-4c97-b971-0d8ff0000000'.*

**azlog authorize** コマンドは、(**azlog createazureid** で作成された) Azure AD サービス プリンシパルの閲覧者のロールを、指定されたサブスクリプションに割り当てます。 Azure ログインが、サブスクリプションの共同管理者または所有者としてのログインでない場合、ログインは失敗し、"承認に失敗しました" というエラー メッセージが表示されます。 このアクションを完了するには、共同管理者または所有者の Azure ロールベースのアクセス制御 (RBAC) が必要です。

## <a name="where-can-i-find-the-definition-of-the-properties-in-the-audit-log"></a>監査ログのプロパティの定義はどこで確認できますか。
参照:

* [Azure Resource Manager の監査操作](../azure-resource-manager/resource-group-audit.md)
* [Azure Monitor REST API でサブスクリプションの管理イベントの一覧を表示](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Azure Security Center の警告の詳細はどこで確認できますか。
「 [Azure Security Center でのセキュリティの警告の管理と対応](../security-center/security-center-managing-and-responding-alerts.md)」をご覧ください。

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>VM 診断で収集した情報を変更するには、どうすればよいですか。
Azure 診断の構成を取得、変更および設定する方法の詳細については、「[PowerShell を使用して Windows を実行している仮想マシンで Azure 診断を有効にする](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。 

Azure 診断の構成を取得する例を次に示します。

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

Azure 診断の構成を変更する例を次に示します。 この構成では、イベント ID 4624 と イベント ID 4625のみがセキュリティ イベント ログから収集されます。 Azure 向け Microsoft マルウェア対策のイベントは、システム イベント ログから収集されます。 XPath 式の使用に関する詳細については、「[Consuming Events (イベントの利用)](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85))」をご覧ください。

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

Azure 診断の構成を設定する例を次に示します。

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

変更後、ストレージ アカウントをチェックして、適切なイベントが収集されていることを確認します。

インストールおよび構成中に問題が起きる場合は、[[サポート リクエスト]](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) を開いてください。 サポートを依頼しようとしているサービスとして **[ログ統合]** を選択します。

## <a name="can-i-use-azure-log-integration-to-integrate-network-watcher-logs-into-my-siem"></a>Azure ログ統合を使用して Network Watcher のログを自分の SIEM に統合することはできますか。

Azure Network Watcher では大量のログ情報が生成されます。 これらのログは、SIEM に送信されることを想定していません。 Network Watcher のログの送信先としてサポートされているのは、ストレージ アカウントのみです。 Azure ログ統合では、これらのログを読み取って SIEM で利用できるようにすることはサポートされていません。

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png
