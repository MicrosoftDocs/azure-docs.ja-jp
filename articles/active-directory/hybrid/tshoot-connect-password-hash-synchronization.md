---
title: Azure AD Connect Sync によるパスワード ハッシュ同期のトラブルシューティング | Microsoft Docs
description: この記事では、パスワード ハッシュ同期の問題のトラブルシューティングを行う方法に関する情報を提供します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c6ec162ceb51c3bf19be42219b054d8371ff221
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98247354"
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>Azure AD Connect Sync を使用したパスワード ハッシュ同期のトラブルシューティング

このトピックでは、パスワード ハッシュ同期の問題のトラブルシューティングを行う手順を示します。 パスワードが想定どおりに同期しない場合、そのパスワードが、ユーザーのサブセット、またはすべてのユーザーを対象している可能性があります。

Azure Active Directory (Azure AD) Connect バージョン 1.1.614.0 以降のデプロイについては、ウィザードのトラブルシューティング タスクを使用して、パスワード ハッシュ同期の問題のトラブルシューティングを行ってください。

* パスワードが同期されない問題がある場合は、「[パスワードが同期されない: トラブルシューティング タスクによるトラブルシューティング](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task)」を参照してください。

* 個々のオブジェクトに問題がある場合は、「[1 つのオブジェクトがパスワードを同期していない: トラブルシューティング タスクによるトラブルシューティング](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task)」を参照してください。

バージョン 1.1.524.0 以降のデプロイについては、パスワード ハッシュ同期の問題のトラブルシューティングに使用できる診断コマンドレットが用意されています。

* パスワードが同期されない問題がある場合は、「[パスワードが同期されない: 診断コマンドレットによるトラブルシューティング](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet)」を参照してください。

* 個々のオブジェクトに問題がある場合は、「[1 つのオブジェクトがパスワードを同期していない: 診断コマンドレットによるトラブルシューティング](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet)」を参照してください。

前のバージョンの Azure AD Connect デプロイの場合:

* パスワードが同期されない問題がある場合は、「[パスワードが同期されない: 手動によるトラブルシューティング手順](#no-passwords-are-synchronized-manual-troubleshooting-steps)」を参照してください。

* 個々のオブジェクトに問題がある場合は、「[1 つのオブジェクトがパスワードを同期していない: 手動によるトラブルシューティング手順](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps)」を参照してください。



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>パスワードが同期されない: トラブルシューティング タスクによるトラブルシューティング

トラブルシューティング タスクを使用して、パスワードが同期されない理由を確認できます。

> [!NOTE]
> トラブルシューティング タスクは、Azure AD Connect バージョン 1.1.614.0 以降のみで使用できます。

### <a name="run-the-troubleshooting-task"></a>トラブルシューティング タスクを実行する

パスワードが同期されない問題のトラブルシューティングを行うには:

1. **[管理者として実行]** オプションを指定して、Azure AD Connect サーバーで新しい Windows PowerShell セッションを開きます。

2. `Set-ExecutionPolicy RemoteSigned` または `Set-ExecutionPolicy Unrestricted` を実行します。

3. Azure AD Connect ウィザードを開始します。

4. **[追加のタスク]** ページに移動し、 **[トラブルシューティング]** を選択して **[次へ]** をクリックします。

5. トラブルシューティング ページで、 **[起動]** をクリックして PowerShell でトラブルシューティング メニューを起動します。

6. メイン メニューで、 **[Troubleshoot password hash synchronization]\(パスワード ハッシュ同期のトラブルシューティング\)** を選択します。

7. サブ メニューで、 **[Password hash synchronization does not work at all]\(パスワード ハッシュ同期がまったく機能しない\)** を選択します。

### <a name="understand-the-results-of-the-troubleshooting-task"></a>トラブルシューティング タスクの結果を理解する

トラブルシューティング タスクでは、次のチェックが実行されます。

* パスワード ハッシュ同期機能が Azure AD テナントに対して有効になっていることを検証します。

* Azure AD Connect サーバーがステージング モードでないことを検証します。

* 既存のオンプレミスの Active Directory コネクタ (既存の Active Directory フォレストに対応) ごとに次の手順を実行します。

   * パスワード ハッシュ同期機能が有効になっていることを検証します。
   
   * Windows アプリケーション イベント ログでパスワード ハッシュ同期のハートビート イベントを検索します。

   * オンプレミスの Active Directory コネクタの Active Directory ドメインごとに次の手順を実行します。

      * Azure AD Connect サーバーからドメインにアクセスできることを検証します。

      * オンプレミスの Active Directory コネクタで使用される Active Directory Domain Services (AD DS) アカウントに、パスワード ハッシュ同期に必要な適切なユーザー名、パスワード、およびアクセス許可が付与されていることを検証します。

次の図は、単一ドメインのオンプレミス Active Directory トポロジに対するコマンドレットの結果を示しています。

![パスワード ハッシュ同期の診断出力](./media/tshoot-connect-password-hash-synchronization/phsglobalgeneral.png)

このセクションの残りの部分では、タスクによって返される具体的な結果と、それに対応する問題について説明します。

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>パスワード ハッシュ同期機能が有効になっていない

Azure AD Connect ウィザードを使用してパスワード ハッシュ同期を有効にしていなかった場合は、次のエラーが返されます。

![パスワード ハッシュ同期が有効になっていない](./media/tshoot-connect-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Azure AD Connect サーバーがステージング モードである

Azure AD Connect サーバーがステージング モードである場合、パスワード ハッシュ同期は一時的に無効になり、次のエラーが返されます。

![Azure AD Connect サーバーがステージング モードである](./media/tshoot-connect-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>パスワード ハッシュ同期のハートビート イベントがない

オンプレミスの Active Directory コネクタごとに、独自のパスワード ハッシュ同期チャネルがあります。 パスワード ハッシュ同期チャネルが確立され、同期するパスワード変更がない場合は、Windows アプリケーション イベント ログでハートビート イベント (EventId 654) が 30 分に 1 回生成されます。 このコマンドレットは、オンプレミスの Active Directory コネクタごとに、過去 3 時間の対応するハートビートを検索します。 ハートビート イベントが見つからないと、次のエラーが返されます。

![パスワード ハッシュ同期のハートビート イベントがない](./media/tshoot-connect-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>AD DS アカウントに適切なアクセス許可がない

パスワード ハッシュを同期するためにオンプレミスの Active Directory コネクタによって使用される AD DS アカウントに、適切なアクセス許可がない場合は、次のエラーが返されます。

![AD DS アカウントのユーザー名またはパスワードが正しくない場合に返されるエラーを示すスクリーンショット。](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>AD DS アカウントのユーザー名またはパスワードが正しくない

パスワード ハッシュを同期するためにオンプレミスの Active Directory コネクタによって使用される AD DS アカウントに、誤ったユーザー名またはパスワードが指定さている場合は、次のエラーが返されます。

![正しくない資格情報](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>1 つのオブジェクトがパスワードを同期していない: トラブルシューティング タスクによるトラブルシューティング

トラブルシューティング タスクを使用して、1 つのオブジェクトがパスワードを同期していない理由を確認できます。

> [!NOTE]
> トラブルシューティング タスクは、Azure AD Connect バージョン 1.1.614.0 以降のみで使用できます。

### <a name="run-the-diagnostics-cmdlet"></a>診断コマンドレットを実行する

特定のユーザー オブジェクトに関する問題のトラブルシューティングを行うには:

1. **[管理者として実行]** オプションを指定して、Azure AD Connect サーバーで新しい Windows PowerShell セッションを開きます。

2. `Set-ExecutionPolicy RemoteSigned` または `Set-ExecutionPolicy Unrestricted` を実行します。

3. Azure AD Connect ウィザードを開始します。

4. **[追加のタスク]** ページに移動し、 **[トラブルシューティング]** を選択して **[次へ]** をクリックします。

5. トラブルシューティング ページで、 **[起動]** をクリックして PowerShell でトラブルシューティング メニューを起動します。

6. メイン メニューで、 **[Troubleshoot password hash synchronization]\(パスワード ハッシュ同期のトラブルシューティング\)** を選択します。

7. サブ メニューで、 **[Password is not synchronized for a specific user account]\(特定のユーザー アカウントのパスワードが同期されない\)** を選択します。

### <a name="understand-the-results-of-the-troubleshooting-task"></a>トラブルシューティング タスクの結果を理解する

トラブルシューティング タスクでは、次のチェックが実行されます。

* Active Directory コネクタ スペース、メタバース、および Azure AD コネクタ スペースで、Active Directory オブジェクトの状態を調べます。

* パスワード ハッシュ同期が有効で、Active Directory オブジェクトに適用されている同期規則があることを検証します。

* オブジェクトに対するパスワード同期の前回の試行結果を取得および表示しようとします。

次の図は、1 つのオブジェクトに対するパスワード ハッシュ同期のトラブルシューティングを行ったときの、コマンドレットの結果を示しています。

![パスワード ハッシュ同期の診断出力 - 1 つのオブジェクト](./media/tshoot-connect-password-hash-synchronization/phssingleobjectgeneral.png)

このセクションの残りの部分では、コマンドレットによって返される具体的な結果と、それに対応する問題について説明します。

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>Active Directory オブジェクトが Azure AD にエクスポートされない

Azure AD テナントに対応するオブジェクトがないため、このオンプレミス Active Directory アカウントのパスワード ハッシュ同期が失敗します。 次のエラーが返されます。

![Azure AD オブジェクトがない](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>ユーザーに一時パスワードがある

現在、Azure AD Connect では、一時パスワードと Azure AD の同期はサポートされていません。 オンプレミスの Active Directory ユーザーで **[Change password at next logon]\(次回ログオン時にパスワード変更が必要\)** オプションが設定されている場合、パスワードは一時的なものと見なされます。 次のエラーが返されます。

![一時パスワードがエクスポートされない](./media/tshoot-connect-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>パスワード同期の前回の試行結果を使用できない

既定では、Azure AD Connect では、パスワード ハッシュ同期の試行結果が 7 日間保存されます。 選択した Active Directory オブジェクトについて使用できる結果がない場合は、次の警告が返されます。

![1 つのオブジェクトの診断出力 - パスワード同期履歴がない](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>パスワードが同期されない: 診断コマンドレットによるトラブルシューティング

パスワードが同期されない理由を確認するには、`Invoke-ADSyncDiagnostics` コマンドレットを使用できます。

> [!NOTE]
> `Invoke-ADSyncDiagnostics` コマンドレットは、Azure AD Connect バージョン 1.1.524.0 以降のみで使用できます。

### <a name="run-the-diagnostics-cmdlet"></a>診断コマンドレットを実行する

パスワードが同期されない問題のトラブルシューティングを行うには:

1. **[管理者として実行]** オプションを指定して、Azure AD Connect サーバーで新しい Windows PowerShell セッションを開きます。

2. `Set-ExecutionPolicy RemoteSigned` または `Set-ExecutionPolicy Unrestricted` を実行します。

3. `Import-Module ADSyncDiagnostics` を実行します。

4. `Invoke-ADSyncDiagnostics -PasswordSync` を実行します。



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>1 つのオブジェクトがパスワードを同期していない: 診断コマンドレットによるトラブルシューティング

1 つのオブジェクトがパスワードを同期していない理由を確認するには、`Invoke-ADSyncDiagnostics` コマンドレットを使用できます。

> [!NOTE]
> `Invoke-ADSyncDiagnostics` コマンドレットは、Azure AD Connect バージョン 1.1.524.0 以降のみで使用できます。

### <a name="run-the-diagnostics-cmdlet"></a>診断コマンドレットを実行する

ユーザーのパスワードが同期されない問題のトラブルシューティングを行うには:

1. **[管理者として実行]** オプションを指定して、Azure AD Connect サーバーで新しい Windows PowerShell セッションを開きます。

2. `Set-ExecutionPolicy RemoteSigned` または `Set-ExecutionPolicy Unrestricted` を実行します。

3. `Import-Module ADSyncDiagnostics` を実行します。

4. 次のコマンドレットを実行します。

   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```

   次に例を示します。

   ```powershell
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>パスワードが同期されない: 手動によるトラブルシューティング手順

パスワードが同期されない理由を確認するには、次の手順に従います。

1. Connect サーバーは[ステージング モード](how-to-connect-sync-staging-server.md)ですか。 ステージング モードのサーバーは、パスワードを同期しません。

2. 「[パスワード同期設定の状態の取得](#get-the-status-of-password-sync-settings)」セクションにあるスクリプトを実行してください。 これにより、パスワード同期の構成の概要が示されます。  

    ![パスワード同期設定からの PowerShell スクリプトの出力](./media/tshoot-connect-password-hash-synchronization/psverifyconfig.png)  

3. この機能が Azure AD で有効ではない場合、または同期チャネルの状態が有効ではない場合は、Connect のインストール ウィザードを実行してください。 **[同期オプションのカスタマイズ]** を選択し、パスワード同期の選択を解除します。この変更により、一時的に機能が無効になります。 その後、もう一度ウィザードを実行し、パスワード同期を再度有効にします。スクリプトを再実行して、構成が正しいことを確認します。

4. イベント ログでエラーを調べます。 問題を示す次のイベントを探します。
    * ソース:"Directory synchronization" ID: 0, 611, 652, 655" イベントが表示されている場合は、接続に問題があります。 イベント ログ メッセージに、問題のあるフォレストの情報が含まれています。 詳細については、「[接続に関する問題](#connectivity problem)」を参照してください。

5. ハートビートが表示されない場合、または他に何も動作していない場合は、「[すべてのパスワードの完全同期の開始](#trigger-a-full-sync-of-all-passwords)」を実行します。 スクリプトは 1 回のみ実行してください。

6. 「パスワードを同期していない 1 つのオブジェクトのトラブルシューティング」セクションをお読みください。

### <a name="connectivity-problems"></a>接続に関する問題

Azure AD との接続がありますか。

アカウントには、すべてのドメインでパスワード ハッシュを読み取るために必要なアクセス許可がありますか。 簡単設定を使用して Connect をインストールした場合、アクセス許可は既に正しい状態にあります。 

カスタム インストールを使用した場合は、次の手順に従って、手動でアクセス許可を設定します。
    
1. Active Directory コネクタで使用されるアカウントを検索するには、**Synchronization Service Manager** を起動します。 
 
2. **[コネクタ]** に移動し、トラブルシューティングを行うオンプレミスの Active Directory フォレストを検索します。 
 
3. コネクタを選択し、 **[プロパティ]** をクリックします。 
 
4. **[Active Directory フォレストに接続]** を選択します。  
    
    ![Active Directory コネクタで使用されるアカウント](./media/tshoot-connect-password-hash-synchronization/connectoraccount.png)  
    ユーザー名と、アカウントがあるドメインをメモしておきます。
    
5. **[Active Directory ユーザーとコンピューター]** を起動し、前に見つけたアカウントで、フォレスト内のすべてのドメインのルートに対して、次のアクセス許可が設定されていることを確認します。
    * ディレクトリの変更のレプリケート
    * ディレクトリの変更をすべてにレプリケート

6. ドメイン コントローラーは、Azure AD Connect からアクセスできますか。 Connect サーバーがすべてのドメイン コントローラーに接続できない場合は、 **[Only use preferred domain controller]\(優先ドメイン コントローラーのみ使用する\)** を構成します。  
    
    ![Active Directory コネクタで使用されるドメイン コントローラー](./media/tshoot-connect-password-hash-synchronization/preferreddc.png)  

7. **Synchronization Service Manager** と **[ディレクトリ パーティションの構成]** に戻ります。 
 
8. **[ディレクトリ パーティションの選択]** でドメインを選択し、 **[Only use preferred domain controller]\(優先ドメイン コントローラーのみ使用する\)** チェック ボックスをオンにして、 **[構成]** をクリックします。 

9. 一覧で、Connect がパスワード同期に使用するドメイン コントローラーを入力します。同じ一覧がインポートとエクスポートにも使用されます。 すべてのドメインに対してこの手順を実行します。

> [!NOTE]
> これらの変更を適用するには、**Microsoft Azure AD Sync** (ADSync) サービスを再起動します。

10. スクリプトによってハートビートがないことが示されたら、「[すべてのパスワードの完全同期の開始](#trigger-a-full-sync-of-all-passwords)」にあるスクリプトを実行します。

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>1 つのオブジェクトがパスワードを同期していない: 手動によるトラブルシューティング手順

オブジェクトの状態を確認することで、パスワード ハッシュ同期の問題を簡単に解決できます。

1. **[Active Directory ユーザーとコンピューター]** で、ユーザーを検索し、 **[ユーザーは次回ログオン時にパスワードの変更が必要]** チェック ボックスがオフになっていることを確認します。  

    ![Active Directory productive passwords](./media/tshoot-connect-password-hash-synchronization/adprodpassword.png)  

    チェック ボックスがオンになっている場合、ユーザーは、サインインしてパスワードを変更するよう求められます。 一時パスワードは、Azure AD と同期されません。

2. パスワードが Active Directory で正しく表示されたら、同期エンジンのユーザーをフォローします。 オンプレミスの Active Directory から Azure AD までユーザーをフォローすると、オブジェクトに記述的なエラーが発生しているかどうかを確認できます。

    a. [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) を起動します。

    b. **[コネクタ]** をクリックします。

    c. ユーザーが存在する **Active Directory コネクタ** を選択します。

    d. **[Search Connector Space (コネクタ スペースの検索)]** を選択します。

    e. **[スコープ]** ボックスで、 **[DN or Anchor]\(DN またはアンカー\)** を選択し、トラブルシューティングを行うユーザーの完全な DN を入力します。

    ![DN でコネクタ スペース内のユーザーを検索](./media/tshoot-connect-password-hash-synchronization/searchcs.png)  

    f. 探しているユーザーを特定し、 **[プロパティ]** をクリックして、すべての属性を参照します。 ユーザーが検索結果にない場合は、[フィルター処理規則](how-to-connect-sync-configure-filtering.md)を確認し、ユーザーに対して[変更の適用と検証](how-to-connect-sync-configure-filtering.md#apply-and-verify-changes)を実行して Connect に表示します。

    g. 過去 1 週間のオブジェクトのパスワード同期の詳細を確認するには、 **[ログ]** をクリックします。  

    ![オブジェクト ログの詳細](./media/tshoot-connect-password-hash-synchronization/csobjectlog.png)  

    オブジェクト ログが空の場合、Azure AD Connect は Active Directory からパスワード ハッシュを読み取ることができていません。 接続エラーのトラブルシューティングに進みます。 **[成功]** 以外の値が表示される場合は、「[パスワード同期ログ](#password-sync-log)」の表をご覧ください。

    h. **[系列]** タブを選択し、 **[PasswordSync]** 列の少なくとも 1 つの同期規則が **True** であることを確認します。 既定の構成では、同期規則の名前は **[In from AD - User AccountEnabled]\(AD からの受信 - ユーザー AccountEnabled)** です。  

    ![ユーザーに関する系列情報](./media/tshoot-connect-password-hash-synchronization/cspasswordsync.png)  

    i. **[Metaverse Object Properties]\(メタバース オブジェクトのプロパティ\)** をクリックして、ユーザー属性の一覧を表示します。  

    ![[Metaverse Object Properties]\(メタバース オブジェクトのプロパティ\) のユーザー属性の一覧を示すスクリーンショット。](./media/tshoot-connect-password-hash-synchronization/mvpasswordsync.png)  

    **cloudFiltered** 属性が存在しないことを確認します。 ドメイン属性 (domainFQDN と domainNetBios) に必要な値があることを確認します。

    j. **[コネクタ]** タブをクリックします。オンプレミスの Active Directory と Azure AD の両方へのコネクタが表示されることを確認します。

    ![メタバース情報](./media/tshoot-connect-password-hash-synchronization/mvconnectors.png)  

    k. Azure AD を表す行を選択し、 **[プロパティ]** 、 **[系列]** タブの順にクリックします。コネクタ スペース オブジェクトでは、 **[PasswordSync]** 列の送信規則が **True** に設定されている必要があります。 既定の構成では、 **[Out to AAD - User Join]** という名前の同期規則です。  

    ![[コネクタ スペース オブジェクトのプロパティ] ダイアログ ボックス](./media/tshoot-connect-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>パスワード同期ログ

状態列には次の値が入ります。

| Status | 説明 |
| --- | --- |
| Success |パスワードが正常に同期されました。 |
| FilteredByTarget |パスワードは **[ユーザーは次回ログオン時にパスワードの変更が必要]** に設定されています。 パスワードは同期されていません。 |
| NoTargetConnection |メタバースまたは Azure AD コネクタ スペースなオブジェクトがありません。 |
| SourceConnectorNotPresent |オンプレミスの Active Directory コネクタ スペースにオブジェクトがありません。 |
| TargetNotExportedToDirectory |Azure AD コネクタ スペースのオブジェクトはまだエクスポートされていません。 |
| MigratedCheckDetailsForMoreInfo |ログ エントリはビルド 1.0.9125.0 より前に作成されており、従来の状態で表示されます。 |
| エラー |サービスから不明なエラーが返されました。 |
| Unknown |パスワード ハッシュのバッチを処理するときにエラーが発生しました。  |
| MissingAttribute |Azure AD Domain Services で必要な特定の属性 (たとえば、Kerberos ハッシュ) を使用できません。 |
| RetryRequestedByTarget |以前、Azure AD Domain Services で必要な特定の属性 (たとえば、Kerberos ハッシュ) を使用できませんでした。 ユーザーのパスワード ハッシュの再同期が試行されました。 |

## <a name="scripts-to-help-troubleshooting"></a>トラブルシューティングに役立つスクリプト

### <a name="get-the-status-of-password-sync-settings"></a>パスワード同期設定の状態の取得

```powershell
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature
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
> このスクリプトは 1 回のみ実行してください。 複数回実行する必要がある場合は、別の問題があります。 問題のトラブルシューティングを行う場合は、Microsoft サポートに連絡してください。

次のスクリプトを使用して、すべてのパスワードの完全同期をトリガーできます。

```powershell
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

* [Azure AD Connect Sync によるパスワード ハッシュ同期の導入](how-to-connect-password-hash-synchronization.md)
* [Azure AD Connect 同期: 同期オプションをカスタマイズする](how-to-connect-sync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)
