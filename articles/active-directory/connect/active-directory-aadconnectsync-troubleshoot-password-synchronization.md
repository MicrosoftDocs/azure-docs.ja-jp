---
title: "Azure AD Connect Sync によるパスワード同期のトラブルシューティング | Microsoft Docs"
description: "パスワード同期の問題のトラブルシューティングを行う方法に関する情報を提供します"
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: e7ad2cb4c464c7095f704cc137f1c42422fbb40e
ms.openlocfilehash: 4c42821b95d666721b84d4976966b4886e517193
ms.lasthandoff: 03/01/2017


---
# <a name="troubleshoot-password-synchronization-with-azure-ad-connect-sync"></a>Azure AD Connect Sync を使用したパスワード同期のトラブルシューティング
このトピックでは、パスワード同期の問題のトラブルシューティングを行う手順を示します。 パスワードが予想どおりに同期していない場合は、ユーザーのサブセットかすべてのユーザーを対象している可能性があります。

* パスワードが同期されない問題がある場合は、「 [パスワードが同期されない問題のトラブルシューティング](#no-passwords-are-synchronized)」を参照してください。
* 個々のオブジェクトに問題がある場合は、「 [パスワードを同期していない&1; つのオブジェクトのトラブルシューティング](#one-object-is-not-synchronizing-passwords)」を参照してください。

## <a name="no-passwords-are-synchronized"></a>パスワードが同期されない
次の手順に従って、パスワードが同期されない理由を確認します。

1. Connect サーバーは[ステージング モード](active-directory-aadconnectsync-operations.md#staging-mode)ですか。 ステージング モードのサーバーは、パスワードを同期しません。
2. 「[パスワード同期設定の状態の取得](#get-the-status-of-password-sync-settings)」セクションにあるスクリプトを実行してください。 これにより、パスワード同期の構成の概要が示されます。  
![PowerShell script output from password sync settings](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/psverifyconfig.png)  
3. この機能が Azure AD で有効ではない場合または同期チャネルの状態が有効ではない場合は、Connect のインストール ウィザードを実行してください。 **[同期オプションのカスタマイズ]** を選択し、パスワード同期の選択を解除します。 この変更により、一時的に機能が無効になります。 その後、もう一度ウィザードを実行し、パスワード同期を再度有効にします。 スクリプトを再実行して、構成が正しいことを確認します。
4. イベント ログでエラーを調べます。 問題を示す次のイベントを探します。
    1. 「Source: "Directory synchronization" ID: 0, 611, 652, 655」が表示されている場合は、接続に問題があります。 イベント ログ メッセージに、問題のあるフォレストの情報が含まれています。 詳しくは、[接続の問題](#connectivity problem)に関する記事をご覧ください
5. ハートビートが表示されない場合、または他に何も動作していない場合は、「[すべてのパスワードの完全同期の開始](#trigger-a-full-sync-of-all-passwords)」を実行します。 このスクリプトは&1; 回だけ実行します。
6. 「[パスワードを同期していない&1; つのオブジェクトのトラブルシューティング](#one-object-is-not-synchronizing-passwords)」のセクションを読みます。

### <a name="connectivity-problem"></a>接続の問題

1. Azure AD との接続がありますか。
2. アカウントには、すべてのドメインでパスワード ハッシュを読み取るために必要なアクセス許可がありますか。 簡単設定を使用して Connect をインストールした場合、アクセス許可は既に正しい状態にあります。 カスタム インストールを使用した場合は、アクセス許可を手動で設定する必要があります。
    1. Active Directory コネクタが使用するアカウントを検索するには、**Synchronization Service Manager** を起動します。 **[コネクタ]** に移動し、トラブルシューティングを行うオンプレミスの Active Directory フォレストを検索します。 コネクタを選び、**[プロパティ]** をクリックします。 **[Active Directory フォレストに接続]**を選択します。  
    ![AD コネクタで使用されるアカウント](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/connectoraccount.png)  
    ユーザー名と、アカウントがあるドメインをメモしておきます。
    2. **[Active Directory ユーザーとコンピューター]** を起動します。 前の手順で見つかったアカウントが、フォレスト内のすべてのドメインのルートに次の権限セットを持つことを確認します。
        * ディレクトリの変更のレプリケート
        * ディレクトリの変更をすべてにレプリケート
3. ドメイン コントローラーは、Azure AD Connect からアクセスできますか。 Connect サーバーがすべてのドメイン コントローラーに接続できない場合は、**[Only use preferred domain controller (優先ドメイン コントローラーのみ使用する)]** を構成する必要があります。  
    ![AD コネクタによって使用されるドメイン コントローラー](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/preferreddc.png)  
    **Synchronization Service Manager** と **[ディレクトリ パーティションの構成]** に戻ります。 **[Select directory partitions (ディレクトリ パーティションの選択)]** でドメインを選び、**[Only use preferred domain controllers (優先ドメイン コントローラーのみ使用する)]** チェックボックスをオンにして、**[構成]** をクリックします。 一覧で、Connect がパスワード同期に使用するドメイン コントローラーを入力します。 同じ一覧がインポートとエクスポートにも使用されます。 すべてのドメインに対してこの手順を実行します。
4. スクリプトによってハートビートがないことが示されたら、「 [すべてのパスワードの完全同期の開始](#trigger-a-full-sync-of-all-passwords)」にあるスクリプトを実行します。

## <a name="one-object-is-not-synchronizing-passwords"></a>1 つのオブジェクトがパスワードを同期していない
オブジェクトの状態を確認することで、パスワード同期の問題を簡単に解決できます。

1. まず、 **[Active Directory ユーザーとコンピューター]**を開きます。 ユーザーを検索し、 **[ユーザーは次回ログオン時にパスワード変更が必要]** チェック ボックスがオフになっていることを確認します。  
![Active Directory productive passwords](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/adprodpassword.png)  
チェック ボックスがオンになっている場合、ユーザーはサインインしてパスワードを変更するよう求められます。 一時パスワードは、Azure AD に同期されません。
2. Active Directory で正しく表示されたら、次の手順では、同期エンジンのユーザーをフォローします。 オンプレミスの Active Directory から Azure AD までユーザーをフォローすると、オブジェクトに記述的なエラーが発生しているかどうかを確認できます。
    1. **[Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)** を起動します。
    2. **[コネクタ]**をクリックします。
    3. ユーザーが存在する **Active Directory コネクタ** を選択します。
    4. **[Search Connector Space (コネクタ スペースの検索)]**を選択します。
    5. **[Scope (スコープ)]** で、**[DN or anchor (DN またはアンカー)]** を選択します。 トラブルシューティングを行うユーザーの完全な DN を入力します。
    ![DN による cs 内のユーザーの検索](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/searchcs.png)  
    6. 探しているユーザーを特定し、**[プロパティ]** をクリックして属性を参照します。 ユーザーが検索結果にない場合は、[フィルター処理規則](active-directory-aadconnectsync-configure-filtering.md)を確認し、ユーザーに対して[変更の適用と検証](active-directory-aadconnectsync-configure-filtering.md#apply-and-verify-changes)を実行して Connect に表示します。
    7. 過去&1; 週間のオブジェクトのパスワード同期の詳細を確認するには、 **[ログ]**をクリックします。  
    ![オブジェクトの詳細](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/csobjectlog.png)  
    オブジェクト ログが空の場合、Azure AD Connect は Active Directory からパスワード ハッシュを読み取ることができていません。 [接続エラー](#connectivity-errors)のトラブルシューティングに進みます。 **[成功]** 以外の値が表示される場合は、「[パスワード同期ログ](#password-sync-log)」の表をご覧ください。
    8. **[lineage (系列)]** タブを選択し、少なくとも&1; つの同期規則の **[パスワード同期]** に **[True]** と表示されていることを確認します。 既定の構成では、 **[In from AD - User AccountEnabled]**という名前の同期規則です。  
    ![ユーザーに関する系列情報](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/cspasswordsync.png)  
    9. **[Metaverse Object Properties (メタバース オブジェクトのプロパティ)]** をクリックします。 ユーザーの属性の一覧が表示されます。  
    ![メタバース情報](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/mvpasswordsync.png)  
    属性 **cloudFiltered** が存在しないことを確認します。 ドメイン属性 (domainFQDN と domainNetBios) に必要な値があることを確認します。
    10. **[コネクタ]** タブをクリックします。 オンプレミスの AD と Azure AD の両方へのコネクタが表示されることを確認します。
    ![メタバース情報](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/mvconnectors.png)  
    11. Azure AD を表す行を選び、**[プロパティ]** をクリックします。 **[系列]** タブをクリックします。 コネクタ スペース オブジェクトには、**[パスワード同期]** が **True** に設定されている送信規則が必要です。 既定の構成では、 **[Out to AAD - User Join]**という名前の同期規則です。  
    ![ユーザーのコネクタ スペースのプロパティ](./media/active-directory-aadconnectsync-troubleshoot-password-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>パスワード同期ログ
状態列には次の値が入ります。

| 状態 | 説明 |
| --- | --- |
| Success |パスワードが正常に同期されました。 |
| FilteredByTarget |パスワードは **[ユーザーは次回ログオン時にパスワードの変更が必要]**に設定されています。 パスワードは同期されていません。 |
| NoTargetConnection |メタバースまたは Azure AD コネクタ スペースなオブジェクトがありません。 |
| SourceConnectorNotPresent |オンプレミスの Active Directory コネクタ スペースにオブジェクトがありません。 |
| TargetNotExportedToDirectory |Azure AD コネクタ スペースのオブジェクトはまだエクスポートされていません。 |
| MigratedCheckDetailsForMoreInfo |ログ エントリはビルド 1.0.9125.0 より前に作成されており、従来の状態で表示されます。 |

## <a name="scripts-to-help-troubleshooting"></a>トラブルシューティングに役立つスクリプト

### <a name="get-the-status-of-password-sync-settings"></a>パスワード同期設定の状態の取得
```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>すべてのパスワードの完全同期の開始
> [!NOTE]
> このスクリプトは&1; 回だけ実行します。 複数回実行する必要がある場合は、別の問題があります。 Microsoft サポートに連絡して、問題のトラブルシューティングの支援を受けてください。

次のスクリプトを使用してすべてのパスワードの完全同期を開始できます。

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>次のステップ
* [Azure AD Connect Sync によるパスワード同期の導入](active-directory-aadconnectsync-implement-password-synchronization.md)
* [Azure AD Connect Sync: 同期オプションのカスタマイズ](active-directory-aadconnectsync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)

