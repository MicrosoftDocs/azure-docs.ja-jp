---
title: Azure AD ドメイン サービスでセキュリティ監査を有効にする | Microsoft Docs
description: Azure AD ドメイン サービスでセキュリティ監査を有効にする
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: 3105296b3c670d3d44789c93878fa1fc6076973b
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566706"
---
# <a name="enable-security-audits-for-azure-ad-domain-services-preview"></a>Azure AD ドメイン サービスでセキュリティ監査を有効にする (プレビュー)
Azure AD ドメイン サービス セキュリティ監査では、Azure AD ドメイン サービス ポータルを使用して対象のリソースにセキュリティ監査イベントをストリームできます。 これらのイベントを受け取ることができるリソースには、Azure Storage、Azure Log Analytics ワークスペース、または Azure Event Hub が含まれます。 セキュリティ監査イベントを有効にすると、その直後に、Azure AD ドメイン サービスは、選択されたカテゴリのすべての監査対象イベントを対象のリソースに送信します。 セキュリティ監査イベントによって、監査されたイベントを Azure ストレージにアーカイブできるようになります。 さらに、イベント ハブを使用してセキュリティ情報およびイベント管理 (SIEM) ソフトウェア (または同等のもの) にイベントのストリームしたり、Azure portal から Azure Log Analytics を使用して独自の分析を行ったりすることができます。 

> [!IMPORTANT]
> Azure AD ドメイン サービスのセキュリティ監査は、Azure AD ドメイン サービスの Azure Resource Manager ベースのインスタンスでのみ使用できます。
>
>

## <a name="auditing-event-categories"></a>イベント カテゴリを監査する
Azure AD ドメイン サービスのセキュリティ監査は、従来の Active Directory ドメイン サービス ドメイン コントローラーの監査と一致しています。 既存の監査のパターンを再利用することにより、イベントを分析するときに、同じロジックを使用できるようにしています。 Azure AD ドメイン サービスのセキュリティ監査には、次のイベント カテゴリが含まれています。

| 監査のカテゴリ名 | 説明 |
|:---|:---|
| アカウント ログオン|ドメイン コントローラーまたはローカルのセキュリティ アカウント マネージャー (SAM) アカウントのデータの認証に対する試行を監査します。</p>ログオンとログオフ ポリシーの設定、およびイベントは、特定のコンピューターへのアクセスの試行を追跡します。 このカテゴリの設定とイベントは、使用されるアカウントのデータベースに注目します。 このカテゴリには、次のサブカテゴリが含まれます。<ul><li>[資格情報の検証の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Kerberos 認証サービスの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Kerberos サービス チケット操作の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[その他のログオン/ログオフ イベントの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| アカウント管理|ユーザーおよびコンピューター アカウントとグループの変更を監査します。 このカテゴリには、次のサブカテゴリが含まれます。<ul><li>[アプリケーション グループ管理の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[コンピューター アカウント管理の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[配布グループ管理の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[その他のアカウント管理の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[セキュリティ グループ管理の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[ユーザー アカウント管理の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| 詳細の追跡|そのコンピューター上の個々のアプリケーションとユーザーのアクティビティを監査し、コンピューターの使用方法について理解します。 このカテゴリには、次のサブカテゴリが含まれます。<ul><li>[DPAPI アクティビティの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[PNP アクティビティの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[プロセス作成の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[プロセス終了の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[RPC イベントの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| ディレクトリ サービス アクセス|Active Directory ドメイン サービス (AD DS) 内のオブジェクトに対するアクセスおよび変更の試行を監査します。 これらの監査イベントは、ドメイン コントローラーにのみ記録されます。 このカテゴリには、次のサブカテゴリが含まれます。<ul><li>[詳細なディレクトリ サービスのレプリケーションの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[ディレクトリ サービスへのアクセスの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[ディレクトリ サービスの変更の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[ディレクトリ サービスのレプリケーションの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| ログオン ログオフ|対話的またはネットワーク経由でのコンピューターへのログオンの試行を監査します。 これらのイベントは、ユーザー アクティビティを追跡し、ネットワーク リソースに対する攻撃の可能性を識別するために役立ちます。 このカテゴリには、次のサブカテゴリが含まれます。<ul><li>[アカウントのロックアウトの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[ユーザー/デバイスの要求の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[IPsec 拡張モードの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[グループ メンバーシップの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[IPsec メイン モードの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[IPsec クイック モードの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[ログオフの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[ログオンの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[ネットワーク ポリシー サーバーの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[その他のログオン/ログオフ イベントの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[特別なログオンの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|オブジェクト アクセス| ネットワークまたはコンピューター上の特定のオブジェクトまたはオブジェクトの型へのアクセスの試行を監査します。 このカテゴリには、次のサブカテゴリが含まれます。<ul><li>[生成されたアプリケーションの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[証明書サービスの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[詳細なファイル共有の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[ファイル共有の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[ファイル システムの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[フィルタリング プラットフォーム接続の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[フィルタリング プラットフォームのパケット破棄の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[ハンドル操作の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[カーネル オブジェクトの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[その他のオブジェクト アクセス イベントの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[レジストリの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[リムーバブル記憶域の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[SAM の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[集約型アクセス ポリシー ステージングの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|ポリシーの変更|ローカル システムまたはネットワークの重要なセキュリティ ポリシーの変更を監査します。 通常、ポリシーは管理者がネットワーク リソースをセキュリティで保護するために設定します。 これらのポリシーの変更や変更の試行を監視することは、ネットワークのセキュリティ管理において重要な側面となる場合があります。 このカテゴリには、次のサブカテゴリが含まれます。<ul><li>[監査ポリシー変更の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[認証ポリシー変更の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[承認ポリシー変更の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[フィルタリング プラットフォームのポリシー変更の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[MPSSVC ルールレベルのポリシー変更の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[その他のポリシー変更の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|特権の使用| 1 つまたは複数のシステムで特定のアクセス許可の使用を監査します。 このカテゴリには、次のサブカテゴリが含まれます。<ul><li>[機密性が高くない特権の使用の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[機密性が高い特権の使用の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[その他の特権の使用イベントの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|システム| 他のカテゴリに含まれておらず、セキュリティに影響を及ぼす可能性があるコンピューターへのシステム レベルの変更を監査します。 このカテゴリには、次のサブカテゴリが含まれます。<ul><li>[IPsec ドライバーの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[その他のシステム イベントの監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[セキュリティ状態の変更の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[セキュリティ システム拡張機能の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[システムの整合性の監査](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>カテゴリごとのイベント ID
 Azure AD ドメイン サービスのセキュリティ監査は、特定のアクションによって監査可能なイベントがトリガーされた際に、次のイベント ID を記録します。

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

## <a name="enable-security-audit-events"></a>セキュリティ監査イベントを有効にする
以下のガイダンスは、Azure AD ドメイン サービスのセキュリティ監査イベントを正常にサブスクライブするために役立ちます。

> [!IMPORTANT]
> Azure AD ドメイン サービスのセキュリティ監査は、さかのぼって適用されません。 過去のイベントを取得する、または過去のイベントを再生することはできません。 サービスを有効にした後に発生するイベントのみが送信されます。
>

### <a name="choose-the-target-resource"></a>ターゲット リソースを選択する
セキュリティ監査のターゲット リソースには、Azure Storage、Azure Event Hubs、Azure Log Analytics ワークスペースの任意の組み合わせを使用できます。 使用状況に合う最適なリソースについては、次の表を検討してください。

> [!IMPORTANT]
> Azure AD ドメイン サービスのセキュリティ監査を有効にする前に、ターゲット リソースを作成する必要があります。
>

| ターゲット リソース | シナリオ |
|:---|:---|
|Azure Storage|主なニーズがセキュリティ監査イベントをアーカイブ用に格納することである場合、このターゲットの使用を検討します。 その他のターゲットもアーカイブの目的で使用できますが、主なニーズがアーカイブ以上である機能を提供しています。 Azure Storage アカウントの作成については、「[ストレージ アカウントの作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal#create-a-storage-account-1)」を参照してください。|
|Azure Event Hubs|主なニーズがデータ分析ソフトウェアやセキュリティ情報およびイベント管理 (SIEM) ソフトウェアなどの追加ソフトウェアとセキュリティ監査イベントを共有することである場合は、このターゲットの使用を検討します。 イベント ハブを作成するには、「[クイックスタート: Azure portal を使用したイベント ハブの作成](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)」を参照してください。|
|Azure Log Analytics ワークスペース|主なニーズが Azure portal でセキュリティ監査を直接分析して確認することである場合は、このターゲットの使用を検討します。  Log Analytics ワークスペースを作成するには、「[Azure portal で Log Analytics ワークスペースを作成する](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)」を参照してください。|

## <a name="using-the-azure-portal-to-enable-security-audit-events"></a>Azure portal を使用してセキュリティ監査イベントを有効にする 
1. Azure Portal ( https://portal.azure.com ) にサインインします。  Azure portal で、[すべてのサービス] をクリックします。 リソースの一覧で「**Domain**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Azure AD ドメイン サービス]** をクリックします。
2. 一覧から Azure AD ドメイン サービス インスタンスをクリックします。
3. 左のアクションの一覧から、 **[診断設定 (プレビュー)]** をクリックします。</p>
![診断設定アクション](./media/security-audit-events/diagnostic-settings-action.png)
4. 診断構成の名前を入力します (**aadds-auditing** など)。</p>
![診断設定ページ](./media/security-audit-events/diagnostic-settings-page.png)
5. セキュリティ監査イベントで使用する、対象のリソースの横にある適切なチェックボックスを選択します。
    > [!NOTE]
    > このページから、ターゲット リソースを作成することはできません。
    >
    
    **Azure Storage:**</p>
    **[ストレージ アカウントへのアーカイブ]** を選択します。 **[構成]** をクリックします。 セキュリティ監査イベントのアーカイブに使用する **[サブスクリプション]** と **[ストレージ アカウント]** を選択します。 Click **OK**.</p>
    
    ![診断ストレージ設定](./media/security-audit-events/diag-settings-storage.png)
    
    **Azure イベント ハブ:**</p>
    **[イベント ハブへのストリーム]** を選択します。 **[構成]** をクリックします。 **[イベント ハブの選択]** ページで、イベント ハブを作成するために使用する **[サブスクリプション]** を選択します。 次に、 **[イベント ハブの名前空間]** 、 **[イベント ハブ名]** 、および **[イベント ハブ ポリシー名]** を選択します。 Click **OK**.</p>
    ![診断イベント ハブ設定](./media/security-audit-events/diag-settings-eventhub.png)
    
    **Azure Log Analytics ワークスペース:**</p>
    **[Log Analytics への送信]** を選択します。 セキュリティ監査イベントを格納するために使用する **[サブスクリプション]** と **[Log Analytics ワークスペース]** を選択します。</p>
    ![診断ワークスペースの設定](./media/security-audit-events/diag-settings-log-analytics.png)

6. 特定のターゲット リソースに含めるログのカテゴリを選択します。 ストレージ アカウントを使用する場合は、保持ポリシーを構成できます。

    > [!NOTE]
    > 1 つの構成内の各ターゲット リソースについて、さまざまなログ カテゴリを選択することができます。 これにより、Log Analytics で保持するログのカテゴリや、アーカイブするログのカテゴリを選択することができます。
    >

7. **[保存]** をクリックして変更を確定します。 構成を保存すると、その直後に、ターゲット リソースが Azure AD ドメイン サービス セキュリティ監査イベントを受信します。

## <a name="using-azure-powershell-to-enable-security-audit-events"></a>Azure PowerShell を使用してセキュリティ監査イベントを有効にする
 
### <a name="prerequisites"></a>前提条件

記事の指示に従って、 [Azure PowerShell モジュールをインストールして Azure サブスクリプションに接続します](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)。

### <a name="enable-security-audits"></a>セキュリティ監査を有効にする

1. **Connect AzAccount** Azure PowerShell コマンドレットを使用して、適切なテナントとサブスクリプションの Azure Resource Manager による認証を受けます。
2. セキュリティ監査イベントのターゲット リソースを作成します。</p>
    **Azure Storage:**</p>
    [ストレージ アカウントの作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-powershell)に従ってストレージ アカウントを作成します。</p>
    **Azure イベント ハブ:**</p>
    [クイック スタート: Azure PowerShell を使用したイベント ハブの作成](https://docs.microsoft.com/azure/event-hubs/event-hubs-quickstart-powershell)に従ってイベント ハブを作成します。 [New-AzEventHubAuthorizationRule](https://docs.microsoft.com/powershell/module/az.eventhub/new-azeventhubauthorizationrule?view=azps-2.3.2) Azure PowerShell コマンドレットを使用して Active Directory AD ドメイン サービスのイベント ハブ**名前空間**へのアクセスを許可する承認規則を作成しなければならない場合があります。 承認規則には、**管理**、**リッスン**、および**送信**権限を含める必要があります。
    > [!IMPORTANT]
    > イベント ハブではなく、イベント ハブ名前空間に承認規則を設定していることを確認します。
       
    </p>
    
    **Azure Log Analytics ワークスペース:**</p>
    ワークスペースを作成するには、[Azure PowerShell を使用して Log Analytics ワークスペースを作成する](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace-posh)に従います。
3. Azure AD ドメイン サービス インスタンスのリソース ID を取得します。 開かれている認証済みの Windows PowerShell コンソールに、次のコマンドを入力します。 今後のコマンドレットの Azure AD ドメイン サービス リソース ID パラメーターには、 **$aadds.ResourceId** 変数を使用します。
    ```powershell
    $aadds = Get-AzResource -name aaddsDomainName
    ``` 
4. **Set-AzDiagnosticSetting** コマンドレットを使用し、Azure AD ドメイン サービスのセキュリティ監査イベントのターゲット リソースを使用するように Azure 診断設定を構成します。 下の例では、変数 $aadds.ResourceId が Azure AD ドメイン サービス インスタンスのリソース ID を表しています (手順 3 を参照してください)。</p>
    **Azure Storage:**
    ```powershell
    Set-AzDiagnosticSetting `
    -ResourceId $aadds.ResourceId` 
    -StorageAccountId storageAccountId `
    -Enabled $true
    ```
    *storageAccountId* を自分のストレージ アカウント ID に置き換えます。</p>
    
    **Azure イベント ハブ:**
    ```powershell
    Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -EventHubName eventHubName `
    -EventHubAuthorizationRuleId eventHubRuleId `
    -Enabled $true
    ```
    *eventHubName* をイベント ハブの名前に置き換えます。 *eventHubRuleId* を以前に作成した承認規則 ID に置き換えます。</p>
    
    **Azure Log Analytics ワークスペース:**
    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -WorkspaceID workspaceId `
    -Enabled $true
    ```
    *workspaceId* を以前に作成した Log Analytics ワークスペースの ID に置き換えます。 

## <a name="view-security-audit-events-using-azure-monitor"></a>Azure Monitor を使用してセキュリティ監査イベントを表示する
Log Analytic ワークスペースを使用すると、Azure Monitor と Kusto クエリ言語を使用して、セキュリティ監査イベントを表示および分析できます。 このクエリ言語は、読みやすい構文で強力な分析機能を提供する読み取り専用の用途向けに設計されています。
Kusto クエリ言語の開始に役立つリソースをこちらでご覧ください。
* [Azure Monitor のドキュメント](https://docs.microsoft.com/azure/azure-monitor/)
* [Azure Monitor で Log Analytics の使用を開始する](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)
* [Azure Monitor でログ クエリの使用を開始する](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)
* [Log Analytics データのダッシュボードを作成して共有する](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-logs-dashboards)

## <a name="sample-queries"></a>サンプル クエリ

### <a name="sample-query-1"></a>サンプル クエリ 1
直近 7 日間のすべてのアカウント ロックアウト イベント。
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>サンプル クエリ 2
2019 年 6 月 26 日午前 9 時から 2019 年 7 月 1 日午前 0 時までのすべてのアカウント ロックアウト イベント (4740) を日時の昇順でソート。
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01) 
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>サンプル クエリ 3
7 日前まで (今から) の user という名前のアカウントのログオン イベント。
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>サンプル クエリ 4
今から 7 日前までの user という名前のアカウントのログオン イベントで、正しくないパスワードを使用してサインインしようとしたもの (0xC0000006a)。
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>サンプル クエリ 5
今から 7 日前までの user という名前のアカウントのログオン イベントで、ロックアウトされたアカウントにサインインしようとしたもの (0xC0000234)。
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>サンプル クエリ 6
今から 7 日前までに、すべてのロックアウトされたユーザーに対してサインインしようとしたアカウント ログオン イベントの数。
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="related-content"></a>関連コンテンツ
* Kusto クエリ言語の[概要](https://docs.microsoft.com/azure/kusto/query/)
* クエリの基本を理解するための [Kusto チュートリアル](https://docs.microsoft.com/azure/kusto/query/tutorial)
* データを表示する新しい方法を学習する際に役立つ[サンプル クエリ](https://docs.microsoft.com/azure/kusto/query/samples)
* Kusto [ベスト プラクティス](https://docs.microsoft.com/azure/kusto/query/best-practices) – 成功のためにクエリを最適化する














 
