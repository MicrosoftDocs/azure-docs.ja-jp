---
title: Azure AD Connect Health エージェントのインストール | Microsoft Docs
description: このページでは、Azure AD Connect Health for AD FS と for Sync のエージェントのインストールについて説明します。
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: mtillman
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 30db5dc373dd0fae24760bdad669fd872cba4b4d
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2018
ms.locfileid: "41918831"
---
# <a name="azure-ad-connect-health-agent-installation"></a>Azure AD Connect Health エージェントのインストール
このドキュメントでは、Azure AD Connect Health エージェントをインストールして構成する手順を紹介します。 エージェントは [こちら](active-directory-aadconnect-health.md#download-and-install-azure-ad-connect-health-agent)からダウンロードできます。

## <a name="requirements"></a>必要条件
次の表に、Azure AD Connect Health を使用するための要件の一覧を示します。

| 要件 | 説明 |
| --- | --- |
| Azure AD Premium |Azure AD Connect Health は Azure AD Premium の機能です。使用するためには Azure AD Premium が必要となります。 </br></br>詳細については、「[Azure AD Premium の概要](../fundamentals/active-directory-get-started-premium.md)」を参照してください。 </br>30 日間無料試用版をすぐにご利用の場合は、[こちら](https://azure.microsoft.com/trial/get-started-active-directory/)のページにアクセスしてください。 |
| Azure AD Connect Health の使用を開始するには、Azure AD のグローバル管理者であること |既定では、Azure AD Connect Health の使用を開始してポータルにアクセスし、操作を実行するために Health エージェントのインストールと構成を行うことができるのは、グローバル管理者のみです。 詳細については、[Azure AD ディレクトリの管理](../fundamentals/active-directory-administer.md)に関するページを参照してください。 <br><br> ロールベースのアクセス制御を使用して、Azure AD Connect Health へのアクセスを組織の他のユーザーに許可できます。 詳細については、[Azure AD Connect Health のロールベースのアクセス制御](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)に関するセクションを参照してください。 </br></br>**重要:** エージェントのインストール時に使用するアカウントは、職場または学校アカウントである必要があります。 Microsoft アカウントを使用することはできません。 詳細については、「[Azure への組織としてのサインアップ](../fundamentals/sign-up-organization.md)」を参照してください。 |
| Azure AD Connect Health エージェントが対象となる個々のサーバーにインストールされていること | Azure AD Connect Health がデータを受信し、監視機能および分析機能を提供するためには、対象となるサーバーに Health エージェントがインストールおよび構成されている必要があります。 </br></br>たとえば、AD FS インフラストラクチャからデータを入手するためには、AD FS サーバーと Web アプリケーション プロキシ サーバーにエージェントがインストールされている必要があります。 同様に、オンプレミス AD DS インフラストラクチャに関するデータを入手するためには、ドメイン コントローラーにエージェントがインストールされている必要があります。 </br></br> |
| Azure サービスのエンドポイントに対する送信接続 | エージェントをインストールしたり実行したりするためには、Azure AD Connect Health サービスのエンド ポイントへの接続が必要となります。 ファイアウォールを使用して送信接続がブロックされている場合は、確実に以下のエンドポイントを許可リストに追加してください。 [送信接続エンドポイント](active-directory-aadconnect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints)に関するセクションをご覧ください。 | 
|IP アドレスに基づく送信接続 | ファイアウォールでの IP アドレスに基づくフィルタリングについては、[Azure の IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)に関するページをご覧ください。|
| 送信トラフィックの SSL 検査がフィルタリングまたは無効化されていること | ネットワーク層で送信トラフィックの SSL 検査または SSL 終了が設定されている場合、エージェントの登録手順が失敗する可能性があります。 詳細については、[SSL 検査のセットアップ方法](https://technet.microsoft.com/library/ee796230.aspx)に関するページをご覧ください |
| エージェントを実行するサーバー上のファイアウォール ポート |エージェントが Azure AD Health サービス エンドポイントと通信するには、次のファイアウォール ポートが開いている必要があります。</br></br><li>TCP ポート 443</li><li>TCP ポート 5671</li> </br>詳細については、[ファイアウォール ポートの有効化](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx)に関するページを参照してください。 |
| IE セキュリティ強化が有効になっている場合は以下の Web サイトが許可されていること |エージェントのインストール対象となるサーバーで IE セキュリティ強化が有効になっている場合、次の Web サイトを許可する必要があります。</br></br><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com</li><li>https:\//login.windows.net</li><li>Azure Active Directory によって信頼されている組織のフェデレーション サーバー  例: https:\//sts.contoso.com</li> 詳細については、[IE の構成方法](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing)に関するページを参照してください。 |
| PowerShell v4.0 以降がインストールされていること | <li>Windows Server 2008 R2 には PowerShell v2.0 が付属しますが、それだけではエージェントの要件が満たされません。  後出の「[Windows Server 2008 R2 サーバーへのエージェントのインストール](#agent-installation-on-windows-server-2008-r2-servers)」の説明に従って PowerShell を更新してください。</li><li>Windows Server 2012 には PowerShell v3.0 が付属しますが、それだけではエージェントの要件が満たされません。  Windows Management Framework を[更新](http://www.microsoft.com/download/details.aspx?id=40855)します。</li><li>Windows Server 2012 R2 以降には、要件を満たした新しいバージョンの PowerShell が付属します。</li>|
|FIPS の無効化|FIPS は Azure AD Connect Health エージェントでサポートされていません。|

### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>Azure サービスのエンドポイントに対する送信接続
 エージェントをインストールしたり実行したりするためには、Azure AD Connect Health サービスのエンド ポイントへの接続が必要となります。 ファイアウォールを使用して送信接続がブロックされている場合は、確実に以下のエンドポイントを許可リストに追加してください。 詳細については、[送信接続の確認](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)に関するページを参照してください。  
 
| ドメイン環境 | 必要な Azure サービス エンドポイント |
| --- | --- |
| 一般 | <li>&#42;.blob.core.windows.net </li><li>&#42;.aadconnecthealth.azure.com </li><li>&#42;.queue.core.windows.net </li><li>&#42;.servicebus.windows.net - ポート: 5671 </li><li>&#42;.table.core.windows.net </li><li>&#42;.adhybridhealth.azure.com/</li><li>https:\//management.azure.com </li><li>https:\//policykeyservice.dc.ad.msft.net/</li><li>https:\//login.windows.net</li><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com *このエンドポイントは、登録時の検出目的でのみ使用されます。</li> | 
| Azure Germany | <li>&#42;.blob.core.cloudapi.de </li><li>&#42;.queue.core.cloudapi.de </li><li>&#42;.servicebus.cloudapi.de </li><li>&#42;.table.core.cloudapi.de </li><li>&#42;.aadconnecthealth.microsoftazure.de </li><li>https:\//management.microsoftazure.de </li><li>https:\//policykeyservice.aadcdi.microsoftazure.de </li><li>https:\//login.microsoftonline.de </li><li>https:\//secure.aadcdn.microsoftonline-p.de </li><li>https:\//www.office.de *このエンドポイントは、登録時の検出目的でのみ使用されます。</li> |
| Azure Government | <li>&#42;.blob.core.usgovcloudapi.net </li><li>&#42;.queue.core.usgovcloudapi.net </li> <li>&#42;.servicebus.usgovcloudapi.net </li> <li>&#42;.table.core.usgovcloudapi.net </li><li>&#42;.aadconnecthealth.microsoftazure.us </li> <li>https:\//management.usgovcloudapi.net </li><li>https:\//policykeyservice.aadcdi.azure.us </li><li>https:\//login.microsoftonline.us </li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com *このエンドポイントは、登録時の検出目的でのみ使用されます。</li> |  
 

## <a name="download-and-install-the-azure-ad-connect-health-agent"></a>Azure AD Connect Health エージェントのダウンロードとインストール
* Azure AD Connect Health の[要件を必ず満たし](active-directory-aadconnect-health-agent-install.md#requirements)てください。
* Azure AD Connect Health for AD FS の使用を開始します
    * [Azure AD Connect Health for AD FS エージェントをダウンロードします](http://go.microsoft.com/fwlink/?LinkID=518973)
    * [インストール手順を参照します](#installing-the-azure-ad-connect-health-agent-for-ad-fs)。
* Azure AD Connect Health for sync の使用を開始します
    * [最新バージョンの Azure AD Connect をダウンロードしてインストールします](http://go.microsoft.com/fwlink/?linkid=615771)。 同期用の Health エージェントは、Azure AD Connect のインストールの一環としてインストールされます (バージョン 1.0.9125.0 以上)。
* Azure AD Connect Health for AD DS の使用を開始します
    * [Azure AD Connect Health for AD DS エージェントをダウンロードします](http://go.microsoft.com/fwlink/?LinkID=820540)。
    * [インストール手順を参照します](#installing-the-azure-ad-connect-health-agent-for-ad-ds)。

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-fs"></a>Azure AD Connect Health エージェント for AD FS のインストール
> [!NOTE]
> AD FS サーバーは、同期サーバーと異なる必要があります。 同期サーバーに AD FS エージェントをインストールしないでください。
>
  
インストールの前に、AD FS サーバーのホスト名が一意であり、AD FS サービスに存在しないことを確認します。   
エージェントのインストールを開始するには、ダウンロードした .exe ファイルをダブルクリックします。 最初の画面で [インストール] をクリックします。

![Verify Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

インストールが完了したら、[すぐに構成する] をクリックします。

![Verify Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

これにより PowerShell ウィンドウが起動され、エージェント登録プロセスが開始されます。 画面の指示に従って、エージェントの登録を実行するアクセス許可を持つ Azure AD アカウントでサインインします。 既定では、管理者アカウントがアクセス許可を持ちます。

![Verify Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)

サインイン後も、PowerShell は続行されます。 完了したら PowerShell を閉じます。これで構成は完了です。

この時点でエージェント サービスが自動的に開始され、エージェントが必要なデータを安全な方法でクラウド サービスにアップロードできるようになります。

前のセクションで挙げたすべての前提条件が満たされていない場合、PowerShell ウィンドウに警告が表示されます。 [要件](active-directory-aadconnect-health-agent-install.md#requirements)が満たされていることを必ず確認したうえで、エージェントをインストールしてください。 以下のスクリーンショットは、これらのエラーの例です。

![Verify Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

エージェントがインストール済みであることを確認するには、サーバーで以下のサービスを探します。 構成が完了していれば、これらのサービスが既に実行されているはずです。 そうでない場合は、構成が完了するまで停止しています。

* Azure AD Connect Health AD FS Diagnostics Service
* Azure AD Connect Health AD FS Insights Service
* Azure AD Connect Health AD FS Monitoring Service

![Verify Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)

### <a name="agent-installation-on-windows-server-2008-r2-servers"></a>Windows Server 2008 R2 サーバーへのエージェントのインストール
Windows Server 2008 R2 サーバーでの手順:

1. Service Pack 1 以降がサーバーで実行されていることを確認します。
2. エージェントをインストールするために、[IE セキュリティ強化の構成] をオフにします。
3. AD Health エージェントをインストールする前に、それぞれのサーバーに Windows PowerShell 4.0 をインストールします。 Windows PowerShell 4.0 をインストールするには:
   * 次のリンクを使用してオフライン インストーラーをダウンロードし、 [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) をインストールします。
   * ([Windows の機能] から) PowerShell ISE をインストールします。
   * [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855)
   * Internet Explorer Version 10 以降をサーバーにインストールします。 (ヘルス サービスが、ユーザーの Azure Admin 資格情報を使用してユーザーを認証するために必須となります。)
4. Windows Server 2008 R2 への Windows PowerShell 4.0 のインストールに関するさらに詳しい情報については、[こちら](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx)の wiki 記事を参照してください。

### <a name="enable-auditing-for-ad-fs"></a>AD FS の監査の有効化
> [!NOTE]
> このセクションが該当するのは AD FS サーバーのみです。 Web アプリケーション プロキシ サーバーでは次の手順に従う必要はありません。
>

利用状況分析機能でデータを収集し、分析するには、AD FS 監査ログ内の情報に Azure AD Connect Health エージェントからアクセスできることが必要です。 既定では、これらのログが有効になっていません。 AD FS サーバーで、AD FS の監査を有効にしたり、AD FS の監査ログを特定したりするには、以下の手順に従ってください。

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2008-r2"></a>Windows Server 2008 R2 で AD FS の監査を有効にするには
1. **[スタート]** ボタンをクリックし、**[プログラム]**、**[管理ツール]** の順にポイントして、**[ローカル セキュリティ ポリシー]** をクリックします。
2. **"セキュリティの設定\ローカル ポリシー\ユーザー権利の割り当て"** フォルダーに移動し、**[セキュリティ監査の生成]** をダブルクリックします。
3. **[ローカル セキュリティの設定]** タブで、AD FS 2.0 サービス アカウントが表示されていることを確認します。 表示されない場合は、**[ユーザーまたはグループの追加]** をクリックしてこのアカウントをリストに追加し、**[OK]** をクリックします。
4. 管理者特権でコマンド プロンプトを開き、次のコマンドを実行して監査を有効にします。<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. **[ローカル セキュリティ ポリシー]** を閉じます。
<br>   -- **次の手順は、プライマリ AD FS サーバーにのみ必要です。** -- </br>
6. **AD FS 管理**スナップインを開きます。 AD FS 管理スナップインを開くには、**[スタート]** ボタンをクリックし、**[プログラム]**、**[管理ツール]** の順にポイントして、**[AD FS 2.0 管理]** をクリックします。
7. **操作**ウィンドウで、**[フェデレーション サービス プロパティの編集]** をクリックします。
8. **[フェデレーション サービス プロパティ]** ダイアログ ボックスの **[イベント]** タブをクリックします。
9. **[成功の監査]** チェック ボックスと **[失敗の監査]** チェック ボックスをオンにします。
10. Click **OK**.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Windows Server 2012 R2 で AD FS の監査を有効にするには
1. スタート画面の **[サーバー マネージャー]** またはデスクトップのタスク バーにある [サーバー マネージャー] を開いて **[ローカル セキュリティ ポリシー]** を開き、**[ツール]、[ローカル セキュリティ ポリシー]** の順にクリックします。
2. **"セキュリティの設定\ローカル ポリシー\ユーザー権利の割り当て"** フォルダーに移動し、**[セキュリティ監査の生成]** をダブルクリックします。
3. **[ローカル セキュリティの設定]** タブで、AD FS サービス アカウントが表示されていることを確認します。 表示されない場合は、**[ユーザーまたはグループの追加]** をクリックしてこのアカウントをリストに追加し、**[OK]** をクリックします。
4. 管理者特権でコマンド プロンプトを開き、次のコマンドを実行して監査を有効にします。```auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable```
5. **[ローカル セキュリティ ポリシー]** を閉じます。
<br>   -- **次の手順は、プライマリ AD FS サーバーにのみ必要です。** -- </br>
6. **AD FS 管理**スナップインを開きます (サーバー マネージャーの [ツール] をクリックし、[AD FS の管理] を選択します)。
7. **操作**ウィンドウで、**[フェデレーション サービス プロパティの編集]** をクリックします。
8. **[フェデレーション サービス プロパティ]** ダイアログ ボックスの **[イベント]** タブをクリックします。
9. **[成功の監査] チェック ボックスと [失敗の監査] チェック ボックス**をオンにし、**[OK]** をクリックします。

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>Windows Server 2016 で AD FS の監査を有効にするには
1. スタート画面の **[サーバー マネージャー]** またはデスクトップのタスク バーにある [サーバー マネージャー] を開いて **[ローカル セキュリティ ポリシー]** を開き、**[ツール]、[ローカル セキュリティ ポリシー]** の順にクリックします。
2. **"セキュリティの設定\ローカル ポリシー\ユーザー権利の割り当て"** フォルダーに移動し、**[セキュリティ監査の生成]** をダブルクリックします。
3. **[ローカル セキュリティの設定]** タブで、AD FS サービス アカウントが表示されていることを確認します。 表示されない場合は、**[ユーザーまたはグループの追加]** をクリックして AD FS サービス アカウントをリストに追加し、**[OK]** をクリックします。
4. 昇格された特権でコマンド プロンプトを開き、次のコマンドを実行して監査を有効にします。<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. **[ローカル セキュリティ ポリシー]** を閉じます。
<br>   -- **次の手順は、プライマリ AD FS サーバーにのみ必要です。** -- </br>
6. **AD FS 管理**スナップインを開きます (サーバー マネージャーの [ツール] をクリックし、[AD FS の管理] を選択します)。
7. **操作**ウィンドウで、**[フェデレーション サービス プロパティの編集]** をクリックします。
8. **[フェデレーション サービス プロパティ]** ダイアログ ボックスの **[イベント]** タブをクリックします。
9. **[成功の監査] チェック ボックスと [失敗の監査] チェック ボックス**をオンにし、**[OK]** をクリックします。 これは既定で有効になっています。
10. PowerShell ウィンドウを開き、次のコマンドを実行します。```Set-AdfsProperties -AuditLevel Verbose```

既定では "basic" 監査レベルが有効になっていることに注意してください。 詳細については、[Windows Server 2016 での AD FS 監査の強化](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server)に関する記事をご覧ください。


#### <a name="to-locate-the-ad-fs-audit-logs"></a>AD FS の監査ログを特定するには
1. **イベント ビューアー**を開きます。
2. [Windows ログ] に移動し、 **[セキュリティ]** を選択します。
3. 右側の **[現在のログをフィルター]** をクリックします。
4. [イベント ソース] の **[AD FS の監査]** を選択します。

    監査ログについては、[FAQ](active-directory-aadconnect-health-faq.md#operations-questions) も参照してください。

![AD FS audit logs](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [!WARNING]
> グループ ポリシーで AD FS 監査を無効にできます。 AD FS 監査が無効になっている場合、ログイン アクティビティに関する利用状況分析は利用できません。 グループ ポリシーで AD FS 監査が無効にされていないことを確認してください。>
>


## <a name="installing-the-azure-ad-connect-health-agent-for-sync"></a>Azure AD Connect Health エージェント for Sync のインストール
> [!NOTE]
> 同期サーバーは、AD FS サーバーと異なる必要があります。 AD FS サーバーに同期エージェントをインストールしないでください。
>

Azure AD Connect Health エージェント for Sync は、最新ビルドの Azure AD Connect に自動的にインストールされます。 Azure AD Connect for Sync を使用するには、最新バージョンの Azure AD Connect をダウンロードし、インストールする必要があります。 最新バージョンは [こちら](http://www.microsoft.com/download/details.aspx?id=47594)からダウンロードできます。

エージェントがインストール済みであることを確認するには、サーバーで以下のサービスを探します。 構成が完了していれば、これらのサービスが既に実行されているはずです。 そうでない場合は、構成が完了するまで停止しています。

* Azure AD Connect Health Sync 分析サービス
* Azure AD Connect Health Sync 監視サービス

![Azure AD Connect Health for Sync の確認](./media/active-directory-aadconnect-health-sync/services.png)

> [!NOTE]
> Azure AD Connect Health を使用するには、Azure AD Premium が必要です。 Azure AD Premium を持っていない場合、Azure Portal で構成を完了できません。 詳細については、[要件のページ](active-directory-aadconnect-health-agent-install.md#requirements)を参照してください。
>
>

## <a name="manual-azure-ad-connect-health-for-sync-registration"></a>手動による Azure AD Connect Health for Sync の登録
Azure AD Connect が正常にインストールされた後で、Azure AD Connect Health for Sync エージェントの登録に失敗した場合は、次の PowerShell コマンドを使用してエージェントを手動で登録できます。

> [!IMPORTANT]
> この PowerShell コマンドは、Azure AD Connect をインストールした後でエージェントの登録が失敗した場合にのみ使用してください。
>
>

以下の PowerShell コマンドは、Azure AD Connect のインストールと構成が正常に完了した後に Health エージェントの登録が失敗した場合にのみ実行する必要があります。 Azure AD Connect Health サービスは、エージェントが正常に登録された後で開始されます。

次の PowerShell コマンドを使用して、Azure AD Connect Health for Sync エージェントを手動で登録することができます。

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

このコマンドは次のパラメーターを受け取ります。

* AttributeFiltering: $true (既定) - Azure AD Connect が既定の属性セットを同期しておらず、フィルター処理された属性セットを使用するようにカスタマイズされている場合。 それ以外の場合は $false です。
* StagingMode: $false (既定) - Azure AD Connect サーバーがステージング モードになっていない場合。サーバーがステージング モードになるように構成されている場合は $true です。

認証情報の入力を求められたら、Azure AD Connect の構成に使用したのと同じグローバル管理者アカウントを使用する必要があります (admin@domain.onmicrosoft.com など)。

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds"></a>Azure AD Connect Health エージェント for AD DS のインストール
エージェントのインストールを開始するには、ダウンロードした .exe ファイルをダブルクリックします。 最初の画面で [インストール] をクリックします。

![Verify Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install1.png)

インストールが完了したら、[すぐに構成する] をクリックします。

![Verify Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install2.png)

コマンド プロンプトが起動され、続けて PowerShell の Register-AzureADConnectHealthADDSAgent が実行されます。 Azure へのサインインを求めるメッセージが表示されたら、サインインしてください。

![Verify Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install3.png)

サインイン後も、PowerShell は続行されます。 完了したら PowerShell を閉じます。これで構成は完了です。

この時点でサービスが自動的に開始され、エージェントによるデータの監視と収集ができるようになります。 前のセクションで挙げたすべての前提条件が満たされていない場合、PowerShell ウィンドウに警告が表示されます。 [要件](active-directory-aadconnect-health-agent-install.md#requirements)が満たされていることを必ず確認したうえで、エージェントをインストールしてください。 以下のスクリーンショットは、これらのエラーの例です。

![Verify Azure AD Connect Health for AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install4.png)

エージェントがインストール済みであることを確認するには、ドメイン コントローラーで以下のサービスを探します。

* Azure AD Connect Health AD DS Insights Service
* Azure AD Connect Health AD DS Monitoring Service

構成が完了していれば、これらのサービスが既に実行されているはずです。 そうでない場合は、構成が完了するまで停止しています。

![Verify Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install5.png)


## <a name="agent-registration-using-powershell"></a>PowerShell を使用したエージェントの登録
適切なエージェントの setup.exe をインストールしたら、ロールに応じて以下の PowerShell コマンドを使用して、エージェントの登録手順を実行できます。 PowerShell ウィンドウを開き、適切なコマンドを実行します。

```
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

これらのコマンドは "Credential" をパラメーターとして受け入れて、非対話型の方法またはサーバー コア マシンで登録を実行します。
* Credential は、パラメーターとして渡される PowerShell 変数内でキャプチャできます。
* エージェントを登録するためのアクセス許可を持ち、MFA が有効になっていない任意の Azure AD ID を指定できます。
* 既定では、グローバル管理者がエージェントの登録を実行するためのアクセス許可を持ちます。 より低い権限を持つ ID に対してこの手順の実行を許可することもできます。 詳細については、[ロール ベースのアクセス制御](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)に関するセクションをご覧ください。

```
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>HTTP プロキシを使用するための Azure AD Connect Health エージェントの構成
HTTP プロキシを使用するように Azure AD Connect Health エージェントを構成できます。

> [!NOTE]
> * エージェントが Microsoft Windows HTTP サービスではなく System.Net を使用して Web 要求を行うので、"Netsh WinHttp set ProxyServerAddress" はサポートされていません。
> * 構成済みの Http プロキシ アドレスを使用して、暗号化された Https メッセージがパススルーされます。
> * 認証されたプロキシ (HTTPBasic を使用) はサポートされていません。
>
>

### <a name="change-health-agent-proxy-configuration"></a>Health エージェントのプロキシ構成の変更
HTTP プロキシを使用するように Azure AD Connect Health エージェントを構成する場合、以下のオプションがあります。

> [!NOTE]
> プロキシ設定を更新するには、すべての Azure AD Connect Health エージェント サービスを再起動する必要があります。 次のコマンドを実行します。<br>
> Restart-Service AdHealth*
>
>

#### <a name="import-existing-proxy-settings"></a>既存のプロキシ設定のインポート
##### <a name="import-from-internet-explorer"></a>Internet Explorer からのインポート
Internet Explorer HTTP プロキシ設定は、インポートして、Azure AD Connect Health エージェントで使用することができます。 Health エージェントを実行している各サーバーで、次の PowerShell コマンドを実行します。

    Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### <a name="import-from-winhttp"></a>WinHTTP からのインポート
WinHTTP プロキシ設定は、インポートして、Azure AD Connect Health エージェントで使用することができます。 Health エージェントを実行している各サーバーで、次の PowerShell コマンドを実行します。

    Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### <a name="specify-proxy-addresses-manually"></a>プロキシ アドレスの手動での指定
Health エージェントを実行している各サーバーで、プロキシ サーバーを手動で指定するには、次の PowerShell コマンドを実行します。

    Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

例: *Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver: 443*

* "address" には、DNS で解決可能なサーバー名または IPv4 アドレスを指定できます。
* "port" は省略できます。 省略した場合、既定のポートとして 443 が選択されます。

#### <a name="clear-existing-proxy-configuration"></a>既存のプロキシ構成のクリア
次のコマンドを実行することで、既存のプロキシ構成をクリアすることができます。

    Set-AzureAdConnectHealthProxySettings -NoProxy


### <a name="read-current-proxy-settings"></a>現在のプロキシ設定の読み取り
現在構成されているプロキシ設定を読み取るには、次のコマンドを実行します。

    Get-AzureAdConnectHealthProxySettings


## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Azure AD Connect Health サービスへの接続テスト
Azure AD Connect Health エージェントが Azure AD Connect Health サービスとの接続を失うことになるような問題が発生することがあります。 ネットワークの問題、アクセス許可の問題や、その他のさまざまな理由があります。

エージェントが Azure AD Connect Health サービスに 2 時間以上データを送信できない場合は、ポータルに "ヘルス サービス データが最新ではありません" というアラートが表示されます。 影響を受ける Azure AD Connect Health エージェントがデータを Azure AD Connect Health サービスにアップロードできるかどうかを確認するには、次の PowerShell コマンドを実行します。

    Test-AzureADConnectHealthConnectivity -Role ADFS

role パラメーターは、現在、以下の値を受け取ります。

* ADFS
* 同期
* ADDS

> [!NOTE]
> 接続ツールを使用するには、まず、エージェントの登録を完了する必要があります。 エージェントの登録を完了できない場合は、Azure AD Connect Health のすべての[要件](active-directory-aadconnect-health-agent-install.md#requirements)が満たされていることを確認してください。 この接続テストは、既定ではエージェントの登録中に実行されます。
>
>

## <a name="related-links"></a>関連リンク
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health の操作](active-directory-aadconnect-health-operations.md)
* [AD FS での Azure AD Connect Health の使用](active-directory-aadconnect-health-adfs.md)
* [Azure AD Connect Health for Sync の使用](active-directory-aadconnect-health-sync.md)
* [AD DS での Azure AD Connect Health の使用](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health の FAQ](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health のバージョンの履歴](active-directory-aadconnect-health-version-history.md)
