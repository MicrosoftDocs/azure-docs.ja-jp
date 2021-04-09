---
title: Azure Active Directory Domain Services でセキュリティ監査を有効にする | Microsoft Docs
description: セキュリティ監査を有効にして、Azure AD Domain Services で分析とアラートのイベントのログ記録を一元化する方法について説明します
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: caf46850b3d8d6946225575b8a9a732a90847482
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574146"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services でセキュリティ監査を有効にする

Azure Active Directory Domain Services (Azure AD DS) セキュリティ監査を使用すると、Azure でセキュリティ イベントを対象のリソースにストリーミングできます。 これらのリソースには、Azure Storage、Azure Log Analytics ワークスペース、または Azure Event Hub が含まれます。 セキュリティ監査イベントを有効にすると、Azure AD DS は、選択されたカテゴリのすべての監査対象イベントを対象のリソースに送信します。

Azure ストレージ内にイベントをアーカイブし、Azure Event Hubs を使用してセキュリティ情報およびイベント管理 (SIEM) ソフトウェア (または同等のもの) にイベントをストリーム配信したり、Azure portal から Azure Log Analytics ワークスペースを使用して独自の分析を行ったりすることができます。

> [!IMPORTANT]
> Azure AD DS セキュリティ監査は、Azure Resource Manager ベースのマネージド ドメインでのみ使用できます。 移行方法の詳細については、「[クラシック仮想ネットワーク モデルから Resource Manager への Azure AD DS の移行][migrate-azure-adds]」を参照してください。

## <a name="security-audit-destinations"></a>セキュリティ監査の出力先

Azure AD DS セキュリティ監査のターゲット リソースには、Azure Storage、Azure Event Hubs、または Azure Log Analytics ワークスペースを使用できます。 これらの出力先は、組み合わせて使用できます。 たとえば、Azure Storage はセキュリティ監査イベントをアーカイブするために使用できますが、Azure Log Analytics ワークスペースでは、短期の情報を分析してレポートを作成できます。

次の表は、対象となるリソースの種類ごとのシナリオの概要を示しています。

> [!IMPORTANT]
> Azure AD DS のセキュリティ監査を有効にする前に、ターゲット リソースを作成する必要があります。 これらのリソースは、Azure portal、Azure PowerShell、または Azure CLI を使用して作成することができます。

| ターゲット リソース | シナリオ |
|:---|:---|
|Azure Storage| 主なニーズがセキュリティ監査イベントをアーカイブ用に格納することである場合は、このターゲットを使用してください。 その他のターゲットもアーカイブの目的で使用できますが、主なニーズがアーカイブ以上である機能を提供しています。 <br /><br />Azure AD DS セキュリティ監査イベントを有効にする前に、まず [Azure Storage アカウント](../storage/common/storage-account-create.md) を作成します。|
|Azure Event Hubs| 主なニーズがデータ分析ソフトウェアやセキュリティ情報およびイベント管理 (SIEM) ソフトウェアなどの追加ソフトウェアとセキュリティ監査イベントを共有することである場合は、このターゲットを使用してください。<br /><br />Azure AD DS のセキュリティ監査イベントを有効にする前に、[Azure portal を使用してイベント ハブを作成してください](../event-hubs/event-hubs-create.md)|
|Azure Log Analytics ワークスペース| 主なニーズが Azure portal でセキュリティ監査を直接分析して確認することである場合は、このターゲットを使用してください。<br /><br />Azure AD DS のセキュリティ監査イベントを有効にする前に、[Azure portal で Log Analytics ワークスペースを作成してください](../azure-monitor/logs/quick-create-workspace.md)。|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>Azure portal を使用してセキュリティ監査イベントを有効にする

Azure portal を使用して Azure AD DS のセキュリティ監査イベントを有効にするには、次の手順を完了します。

> [!IMPORTANT]
> Azure AD DS のセキュリティ監査は、さかのぼって適用されません。 過去のイベントを取得したり再生したりすることはできません。 Azure AD DS では、セキュリティ監査を有効にした後に発生するイベントのみ送信できます。

1. Azure Portal ( https://portal.azure.com ) にサインインします。
1. Azure portal の上部で、**Azure AD Domain Services** を検索して選択します。 目的のマネージド ドメインを選択します (例: *aaddscontoso.com* )。
1. Azure AD DS ウィンドウで、左側にある **[診断設定]** を選択します。
1. 診断は、既定では構成されていません。 作業を開始するには、 **[診断設定の追加]** を選択します。

    ![Azure AD Domain Services の診断設定を追加する](./media/security-audit-events/add-diagnostic-settings.png)

1. 診断構成の名前を入力します (*aadds-auditing* など)。

    目的のセキュリティ監査の出力先のチェックボックスをオンにします。 Azure Storage アカウント、Azure イベントハブ、または Log Analytics ワークスペースから選択できます。 これらの出力先リソースは、Azure サブスクリプションに既に存在している必要があります。 このウィザードでは、出力先リソースを作成できません。

    ![キャプチャする必要がある監査イベントの出力先と種類を有効にする](./media/security-audit-events/diagnostic-settings-page.png)

    * **Azure Storage**
        * **[ストレージ アカウントへのアーカイブ]** を選択し、 **[構成]** を選択します。
        * セキュリティ監査イベントのアーカイブに使用する **[サブスクリプション]** と **[ストレージ アカウント]** を選択します。
        * 準備ができたら、 **[OK]** を選択します。
    * **Azure Event Hubs**
        * **[イベント ハブへのストリーム]** を選択し、 **[構成]** をクリックします。
        * **[サブスクリプション]** および **[イベント ハブ名前空間]** を選択します。 必要に応じて、 **[イベント ハブ名]** を選択してから、 **[イベント ハブ ポリシー名]** を選択します。
        * 準備ができたら、 **[OK]** を選択します。
    * **Azure Log Analytics ワークスペース**
        * **[Log Analytics への送信]** を選択してから、セキュリティ監査イベントを格納するために使用する **[サブスクリプション]** と **[Log Analytics ワークスペース]** を選択します。

1. 特定のターゲット リソースに含めるログのカテゴリを選択します。 監査イベントを Azure Storage アカウントに送信する場合は、データを保持する日数を定義する保持ポリシーを構成することもできます。 既定の設定である *0* では、すべてのデータが保持され、一定期間後にイベントがローテーションされることはありません。

    1 つの構成内の各ターゲット リソースについて、さまざまなログ カテゴリを選択することができます。 この機能により、Log Analytics で保持するログのカテゴリや、アーカイブするログのカテゴリなどを選択することができます。

1. 完了したら、 **[保存]** を選択して変更をコミットします。 構成が保存されるとすぐに、ターゲット リソースが Azure AD DS のセキュリティ監査イベントの受信を開始します。

## <a name="enable-security-audit-events-using-azure-powershell"></a>Azure PowerShell を使用してセキュリティ監査イベントを有効にする

Azure PowerShell を使用して Azure AD DS のセキュリティ監査イベントを有効にするには、次の手順を完了します。 必要に応じて、最初に [Azure PowerShell モジュールをインストールし、Azure サブスクリプションに接続](/powershell/azure/install-az-ps)します。

> [!IMPORTANT]
> Azure AD DS のセキュリティ監査は、さかのぼって適用されません。 過去のイベントを取得したり再生したりすることはできません。 Azure AD DS では、セキュリティ監査を有効にした後に発生するイベントのみ送信できます。

1. [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) コマンドレットを使用して、Azure サブスクリプションに対して認証します。 メッセージが表示されたら、アカウント資格情報を入力します。

    ```azurepowershell
    Connect-AzAccount
    ```

1. セキュリティ監査イベントのターゲット リソースを作成します。

    * **Azure ストレージ** - [Azure PowerShell を使用してストレージ アカウントを作成します](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * **Azure イベント ハブ** - [Azure PowerShell を使用してイベント ハブを作成します](../event-hubs/event-hubs-quickstart-powershell.md)。 また、[New-AzEventHubAuthorizationRule](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) コマンドレットを使用して、イベント ハブ *名前空間* に Azure AD DS のアクセス許可を付与する承認規則を作成しなければならない場合もあります。 承認規則には、**管理**、**リッスン**、および **送信** 権限を含める必要があります。

        > [!IMPORTANT]
        > イベント ハブ自体ではなく、イベント ハブ名前空間に承認規則を設定していることを確認します。

    * **Azure Log Analytics ワークスペース** - [Azure PowerShell を使用して Log Analytics ワークスペースを作成します](../azure-monitor/logs/powershell-workspace-configuration.md)。

1. [Get AzResource](/powershell/module/Az.Resources/Get-AzResource) コマンドレットを使用して、Azure AD DS 管理対象ドメインのリソース ID を取得します。 *$aadds.ResourceId* という名前の変数を作成して、値を保持します。

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. [Set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) コマンドレットを使用して、Azure AD Domain Services のセキュリティ監査イベントのターゲット リソースを使用するように Azure 診断設定を構成します。 次の例で、変数 *$aadds.ResourceId* は前の手順から使用しているものです。

    * **Azure storage** - *storageAccountId* を自分のストレージ アカウント名に置き換えます。

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Azure イベント ハブ** - *eventHubName* をお使いのイベント ハブの名前に置き換え、*eventHubRuleId* を承認規則 ID に置き換えます。

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Azure Log Analytic ワークスペース** - *workspaceId* を Log Analytics ワークスペースの ID に置き換えます。

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>Azure Monitor を使用してセキュリティ監査イベントをクエリおよび表示する

Log Analytic ワークスペースを使用すると、Azure Monitor と Kusto クエリ言語を使用して、セキュリティ監査イベントを表示および分析できます。 このクエリ言語は、読みやすい構文で強力な分析機能を提供する読み取り専用の用途向けに設計されています。 Kusto クエリ言語の使用を開始する方法の詳細については、次の記事を参照してください。

* [Azure Monitor のドキュメント](../azure-monitor/index.yml)
* [Azure Monitor で Log Analytics の使用を開始する](../azure-monitor/logs/log-analytics-tutorial.md)
* [Azure Monitor でログ クエリの使用を開始する](../azure-monitor/logs/get-started-queries.md)
* [Log Analytics データのダッシュボードを作成して共有する](../azure-monitor/visualize/tutorial-logs-dashboards.md)

次のサンプル クエリを使用すると、Azure AD DS からのセキュリティ監査イベントの分析を開始できます。

### <a name="sample-query-1"></a>サンプル クエリ 1

直近 7 日間のすべてのアカウント ロックアウト イベントを表示します。

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>サンプル クエリ 2

2020 年 6 月 3 日午前 9 時から 2020 年 6 月 10 日午前 0 時までのすべてのアカウント ロックアウト イベント (*4740*) を、 日時の昇順でソートして表示します。

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-03 09:00) and TimeGenerated <= datetime(2020-06-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>サンプル クエリ 3

7 日前まで (今から) の user という名前のアカウントのサインイン イベントを表示します。

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>サンプル クエリ 4

今から 7 日前までの user という名前のアカウントのサインイン イベントで、正しくないパスワードを使用してサインインしようとしたもの (*0xC0000006a*) を表示します。

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>サンプル クエリ 5

今から 7 日前までの user という名前のアカウントのサインイン イベントで、ロックアウトされたアカウントにサインインしようとしたもの (*0xC0000234*) を表示します。

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>サンプル クエリ 6

今から 7 日前までに、すべてのロックアウトされたユーザーに対してサインインしようとしたアカウント サインイン イベントの数を表示します。

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="audit-event-categories"></a>監査イベントのカテゴリ

Azure AD DS セキュリティ監査は、従来の AD DS ドメイン コントローラーの従来の監査に沿っています。 ハイブリッド環境では、既存の監査パターンを再利用できるため、イベントを分析するときに同じロジックを使用することができます。 トラブルシューティングまたは分析が必要なシナリオに応じて、さまざまな監査イベントのカテゴリを対象にする必要があります。

次の監査イベントのカテゴリを利用できます。

| 監査のカテゴリ名 | 説明 |
|:---|:---|
| アカウント ログオン|ドメイン コントローラーまたはローカルのセキュリティ アカウント マネージャー (SAM) アカウントのデータの認証に対する試行を監査します。</p>ログオンとログオフ ポリシーの設定、およびイベントは、特定のコンピューターへのアクセスの試行を追跡します。 このカテゴリの設定とイベントは、使用されるアカウントのデータベースに注目します。 このカテゴリには、次のサブカテゴリが含まれます。<ul><li>[資格情報の検証の監査](/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Kerberos 認証サービスの監査](/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Kerberos サービス チケット操作の監査](/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[その他のログオン/ログオフ イベントの監査](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| アカウント管理|ユーザーおよびコンピューター アカウントとグループの変更を監査します。 このカテゴリには、次のサブカテゴリが含まれます。<ul><li>[アプリケーション グループ管理の監査](/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[コンピューター アカウント管理の監査](/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[配布グループ管理の監査](/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[その他のアカウント管理の監査](/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[セキュリティ グループ管理の監査](/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[ユーザー アカウント管理の監査](/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| 詳細の追跡|そのコンピューター上の個々のアプリケーションとユーザーのアクティビティを監査し、コンピューターの使用方法について理解します。 このカテゴリには、次のサブカテゴリが含まれます。<ul><li>[DPAPI アクティビティの監査](/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[PNP アクティビティの監査](/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[プロセス作成の監査](/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[プロセス終了の監査](/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[RPC イベントの監査](/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| ディレクトリ サービス アクセス|Active Directory ドメイン サービス (AD DS) 内のオブジェクトに対するアクセスおよび変更の試行を監査します。 これらの監査イベントは、ドメイン コントローラーにのみ記録されます。 このカテゴリには、次のサブカテゴリが含まれます。<ul><li>[詳細なディレクトリ サービスのレプリケーションの監査](/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[ディレクトリ サービスへのアクセスの監査](/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[ディレクトリ サービスの変更の監査](/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[ディレクトリ サービスのレプリケーションの監査](/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| ログオン ログオフ|対話的またはネットワーク経由でのコンピューターへのログオンの試行を監査します。 これらのイベントは、ユーザー アクティビティを追跡し、ネットワーク リソースに対する攻撃の可能性を識別するために役立ちます。 このカテゴリには、次のサブカテゴリが含まれます。<ul><li>[アカウントのロックアウトの監査](/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[ユーザー/デバイスの要求の監査](/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[IPsec 拡張モードの監査](/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[グループ メンバーシップの監査](/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[IPsec メイン モードの監査](/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[IPsec クイック モードの監査](/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[ログオフの監査](/windows/security/threat-protection/auditing/audit-logoff)</li><li>[ログオンの監査](/windows/security/threat-protection/auditing/audit-logon)</li><li>[ネットワーク ポリシー サーバーの監査](/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[その他のログオン/ログオフ イベントの監査](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[特別なログオンの監査](/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|オブジェクト アクセス| ネットワークまたはコンピューター上の特定のオブジェクトまたはオブジェクトの型へのアクセスの試行を監査します。 このカテゴリには、次のサブカテゴリが含まれます。<ul><li>[生成されたアプリケーションの監査](/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[証明書サービスの監査](/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[詳細なファイル共有の監査](/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[ファイル共有の監査](/windows/security/threat-protection/auditing/audit-file-share)</li><li>[ファイル システムの監査](/windows/security/threat-protection/auditing/audit-file-system)</li><li>[フィルタリング プラットフォーム接続の監査](/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[フィルタリング プラットフォームのパケット破棄の監査](/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[ハンドル操作の監査](/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[カーネル オブジェクトの監査](/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[その他のオブジェクト アクセス イベントの監査](/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[レジストリの監査](/windows/security/threat-protection/auditing/audit-registry)</li><li>[リムーバブル記憶域の監査](/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[SAM の監査](/windows/security/threat-protection/auditing/audit-sam)</li><li>[集約型アクセス ポリシー ステージングの監査](/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|ポリシーの変更|ローカル システムまたはネットワークの重要なセキュリティ ポリシーの変更を監査します。 通常、ポリシーは管理者がネットワーク リソースをセキュリティで保護するために設定します。 これらのポリシーの変更や変更の試行を監視することは、ネットワークのセキュリティ管理において重要な側面となる場合があります。 このカテゴリには、次のサブカテゴリが含まれます。<ul><li>[監査ポリシー変更の監査](/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[認証ポリシー変更の監査](/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[承認ポリシー変更の監査](/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[フィルタリング プラットフォームのポリシー変更の監査](/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[MPSSVC ルールレベルのポリシー変更の監査](/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[その他のポリシー変更の監査](/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|特権の使用| 1 つまたは複数のシステムで特定のアクセス許可の使用を監査します。 このカテゴリには、次のサブカテゴリが含まれます。<ul><li>[機密性が高くない特権の使用の監査](/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[機密性が高い特権の使用の監査](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[その他の特権の使用イベントの監査](/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|システム| 他のカテゴリに含まれておらず、セキュリティに影響を及ぼす可能性があるコンピューターへのシステム レベルの変更を監査します。 このカテゴリには、次のサブカテゴリが含まれます。<ul><li>[IPsec ドライバーの監査](/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[その他のシステム イベントの監査](/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[セキュリティ状態の変更の監査](/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[セキュリティ システム拡張機能の監査](/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[システムの整合性の監査](/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>カテゴリごとのイベント ID

 Azure AD DS セキュリティ監査は、特定のアクションによって監査可能なイベントがトリガーされた際に、次のイベント ID を記録します。

| イベント カテゴリ名 | イベント ID |
|:---|:---|
|アカウント ログオン セキュリティ|4767、4774、4775、4776、4777|
|アカウント管理セキュリティ|4720、4722、4723、4724、4725、4726、4727、4728、4729、4730、4731、4732、4733、4734、4735、4737、4738、4740、4741、4742、4743、4754、4755、4756、4757、4758、4764、4765、4766、4780、4781、4782、4793、4798、4799、5376、5377|
|詳細の追跡セキュリティ|なし|
|DS アクセス セキュリティ|5136、5137、5138、5139、5141|
|ログオン ログオフ セキュリティ|4624、4625、4634、4647、4648、4672、4675、4964|
|オブジェクト アクセス セキュリティ|なし|
|ポリシー変更セキュリティ|4670、4703、4704、4705、4706、4707、4713、4715、4716、4717、4718、4719、4739、4864、4865、4866、4867、4904、4906、4911、4912|
|特権の使用セキュリティ|4985|
|システム セキュリティ|4612、4621|

## <a name="next-steps"></a>次のステップ

Kusto の具体的な情報については、次の記事を参照してください。

* Kusto クエリ言語の[概要](/azure/kusto/query/)
* クエリの基本を理解するための [Kusto チュートリアル](/azure/kusto/query/tutorial)
* データを表示する新しい方法を学習する際に役立つ[サンプル クエリ](/azure/kusto/query/samples)
* 成功のためにクエリを最適化する Kusto [ベスト プラクティス](/azure/kusto/query/best-practices)。

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md