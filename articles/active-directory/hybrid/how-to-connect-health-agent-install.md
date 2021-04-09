---
title: Azure Active Directory に Connect Health エージェントをインストールする
description: この Azure AD Connect Health の記事では、Active Directory フェデレーションサービス (AD FS) 用と Sync 用のエージェントのインストールについて説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/20/2020
ms.topic: how-to
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a3aff7b99dad910a9691eef2004df856ca883789
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103224386"
---
# <a name="azure-ad-connect-health-agent-installation"></a>Azure AD Connect Health エージェントのインストール

この記事では、Azure Active Directory (Azure AD) Connect Health エージェントをインストールして構成する方法について説明します。 エージェントをダウンロードするには、[これらの手順](how-to-connect-install-roadmap.md#download-and-install-azure-ad-connect-health-agent)を参照してください。

## <a name="requirements"></a>必要条件

次の表に、Azure AD Connect Health を使用するための要件の一覧を示します。

| 要件 | 説明 |
| --- | --- |
| Azure AD Premium (P1 または P2) サブスクリプションがあります。  |Azure AD Connect Health は Azure AD Premium (P1 または P2) の機能です。 詳細については、[Azure AD Premium へのサインアップ](../fundamentals/active-directory-get-started-premium.md)に関する記事を参照してください。 <br /><br />30 日間の無料試用版の利用を開始する場合は、[試用版の開始](https://azure.microsoft.com/trial/get-started-active-directory/)に関するページを参照してください。 |
| 自分が Azure AD の全体管理者である。 |既定では、正常性エージェントのインストールと構成、ポータルへのアクセス、および Azure AD Connect Health 内での任意の操作を行うことができるのは、全体管理者のみです。 詳細については、[Azure AD ディレクトリの管理](../fundamentals/active-directory-whatis.md)に関するページを参照してください。 <br /><br /> Azure ロールベースのアクセス制御 (Azure RBAC) を使用することで、Azure AD Connect Health へのアクセスを組織の他のユーザーに許可できます。 詳細については、[Azure AD Connect Health の Azure RBAC](how-to-connect-health-operations.md#manage-access-with-azure-rbac) に関するページを参照してください。 <br /><br />**重要**:職場または学校アカウントを使用してエージェントをインストールしてください。 Microsoft アカウントは使用できません。 詳細については、[Azure への組織としてのサインアップ](../fundamentals/sign-up-organization.md)に関するページを参照してください。 |
| Azure AD Connect Health エージェントが対象となる個々のサーバーにインストールされている。 | Health エージェントがデータを受信し、監視および分析の機能を提供できるように、対象となるサーバーにそれらがインストールおよび構成されている必要があります。 <br /><br />たとえば、Active Directory フェデレーション サービス (AD FS) インフラストラクチャからデータを取得するには、AD FS サーバーと Web アプリケーション プロキシ サーバーにエージェントをインストールする必要があります。 同様に、オンプレミスの Azure AD Domain Services (Azure AD DS) インフラストラクチャからデータを取得するには、ドメイン コントローラーにエージェントをインストールする必要があります。  |
| Azure サービス エンドポイントに送信接続がある。 | エージェントをインストールしたり実行したりするためには、Azure AD Connect Health サービスのエンド ポイントへの接続が必要となります。 ファイアウォールにより送信接続がブロックされている場合は、[送信接続エンドポイント](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints)を許可リストに追加します。 |
|送信接続が IP アドレスに基づいている。 | IP アドレスに基づくファイアウォールのフィルタリングの詳細については、[Azure の IP 範囲](https://www.microsoft.com/download/details.aspx?id=56519)に関するページを参照してください。|
| 送信トラフィックの TLS 検査がフィルター処理されているか無効になっている。 | ネットワーク層で送信トラフィックの TLS 検査または終了が設定されている場合、エージェントの登録手順またはデータのアップロード操作が失敗する可能性があります。 詳細については、[TLS 検査の設定](/previous-versions/tn-archive/ee796230(v=technet.10))に関するページを参照してください。 |
| サーバー上のファイアウォール ポートでエージェントが実行されている。 |エージェントが Azure AD Connect Health サービス エンドポイントと通信できるように、次のファイアウォール ポートが開いている必要があります。 <br /><li>TCP ポート 443</li><li>TCP ポート 5671</li> <br />エージェントの最新バージョンでは、ポート 5671 は必要ありません。 ポート 443 のみが必要になるように、最新バージョンにアップグレードしてください。 詳細については、「[ハイブリッド ID で必要なポートとプロトコル](./reference-connect-ports.md)」を参照してください。 |
| Internet Explorer のセキュリティ強化が有効になっている場合に、指定された Web サイトを許可する。  |Internet Explorer のセキュリティ強化が有効になっている場合は、エージェントをインストールするサーバーで次の Web サイトを許可します。<br /><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com</li><li>https:\//login.windows.net</li><li>https:\//aadcdn.msftauth.net</li><li>Azure AD によって信頼されている組織のフェデレーション サーバー (たとえば、https:\//sts.contoso.com)</li> <br />詳細については、[Internet Explorer の構成方法](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing)に関するページを参照してください。 ネットワークにプロキシがある場合は、この表の最後に示されている注意を参照してください。|
| PowerShell バージョン 4.0 以降がインストールされている。 | Windows Server 2012 には、PowerShell バージョン 3.0 が含まれています。 このバージョンは、エージェントには十分では "*ありません*"。</br></br> Windows Server 2012 R2 以降には、要件を満たす最新バージョンの PowerShell が含まれています。|
|FIPS (Federal Information Processing Standards) が無効になっている。|Azure AD Connect Health エージェントで FIPS はサポートされていません。|

> [!IMPORTANT]
> Windows Server Core では、Azure AD Connect Health エージェントのインストールがサポートされていません。


> [!NOTE]
> ロックダウンが頻繁で、非常に制限されている環境がある場合は、Internet Explorer のセキュリティ強化のために、表に記載されたものだけではなく、さらに URL を追加する必要があります。 また、次のセクションの表に記載されている URL も追加してください。  


### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>Azure サービスのエンドポイントに対する送信接続

インストール中や実行時、エージェントには Azure AD Connect Health サービス エンド ポイントへの接続が必要です。 ファイアウォールにより送信接続がブロックされている場合は、次の表の URL が既定でブロックされていないことを確認してください。 

これらの URL のセキュリティの監視や検査は無効にしないでください。 代わりに、他のインターネット トラフィックを許可するようにそれらを許可します。 

これらの URL では、Azure AD Connect Health サービス エンドポイントとの通信が許可されます。 この記事の後半で、`Test-AzureADConnectHealthConnectivity` を使用して[送信接続を確認する](#test-connectivity-to-azure-ad-connect-health-service)方法について説明します。

| ドメイン環境 | 必要な Azure サービス エンドポイント |
| --- | --- |
| 一般 | <li>&#42;.blob.core.windows.net </li><li>&#42;.aadconnecthealth.azure.com </li><li>&#42;.servicebus.windows.net - ポート:5671 (このエンドポイントは最新バージョンのエージェントでは不要です)</li><li>&#42;.adhybridhealth.azure.com/</li><li>https:\//management.azure.com </li><li>https:\//policykeyservice.dc.ad.msft.net/</li><li>https:\//login.windows.net</li><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com (このエンドポイントは、登録時の検出目的でのみ使用されます。)</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |
| Azure Germany | <li>&#42;.blob.core.cloudapi.de </li><li>&#42;.servicebus.cloudapi.de </li> <li>&#42;.aadconnecthealth.microsoftazure.de </li><li>https:\//management.microsoftazure.de </li><li>https:\//policykeyservice.aadcdi.microsoftazure.de </li><li>https:\//login.microsoftonline.de </li><li>https:\//secure.aadcdn.microsoftonline-p.de </li><li>https:\//www.office.de (このエンドポイントは、登録時の検出目的でのみ使用されます。)</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |
| Azure Government | <li>&#42;.blob.core.usgovcloudapi.net </li> <li>&#42;.servicebus.usgovcloudapi.net </li> <li>&#42;.aadconnecthealth.microsoftazure.us </li> <li>https:\//management.usgovcloudapi.net </li><li>https:\//policykeyservice.aadcdi.azure.us </li><li>https:\//login.microsoftonline.us </li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com (このエンドポイントは、登録時の検出目的でのみ使用されます。)</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |


## <a name="install-the-agent"></a>エージェントをインストールする

Azure AD Connect Health エージェントをダウンロードしてインストールするには: 

* Azure AD Connect Health の[要件](how-to-connect-health-agent-install.md#requirements)を満たしていることを確認します。
* Azure AD Connect Health for AD FS の使用を開始します。
    * [AD FS 用 Azure AD Connect Health エージェントをダウンロードします](https://go.microsoft.com/fwlink/?LinkID=518973)。
    * [インストール手順](#install-the-agent-for-ad-fs)を参照します。
* Azure AD Connect Health for Sync の使用を開始します。
    * [最新バージョンの Azure AD Connect をダウンロードしてインストールします](https://go.microsoft.com/fwlink/?linkid=615771)。 Sync 用の正常性エージェントは、Azure AD Connect のインストールの一環としてインストールされます (バージョン 1.0.9125.0 以降)。
* Azure AD Connect Health for Azure AD DS の使用を開始します。
    * [Azure AD DS 用 Azure AD Connect Health エージェントをダウンロードします](https://go.microsoft.com/fwlink/?LinkID=820540)。
    * [インストール手順](#install-the-agent-for-azure-ad-ds)を参照します。

## <a name="install-the-agent-for-ad-fs"></a>AD FS 用のエージェントをインストールする

> [!NOTE]
> AD FS サーバーは、Sync サーバーと異なる必要があります。 Sync サーバーに AD FS エージェントをインストールしないでください。
>

エージェントをインストールする前に、AD FS サーバーのホスト名が一意であり、AD FS サービスに存在しないことを確認してください。
エージェントのインストールを開始するには、ダウンロードした *.exe* ファイルをダブルクリックします。 最初のウィンドウで、 **[インストール]** を選択します。

![Azure AD Connect Health AD FS エージェントのインストール ウィンドウを示すスクリーンショット。](./media/how-to-connect-health-agent-install/install1.png)

インストールが完了したら、 **[今すぐ構成]** を選択します。

![Azure AD Connect Health AD FS エージェントのインストールの確認メッセージを示すスクリーンショット。](./media/how-to-connect-health-agent-install/install2.png)

PowerShell ウィンドウが開き、エージェント登録プロセスが開始されます。 プロンプトが表示されたら、エージェントを登録するためのアクセス許可を持つ Azure AD アカウントを使用してサインインします。 既定では、全体管理者アカウントがアクセス許可を保持しています。

![Azure AD Connect Health AD FS のサインイン ウィンドウを示すスクリーンショット。](./media/how-to-connect-health-agent-install/install3.png)

サインインすると、PowerShell が続行されます。 これが終了したら、PowerShell を閉じることができます。 構成は完了です。

この時点でエージェント サービスが自動的に開始し、エージェントは必要なデータをクラウド サービスに安全にアップロードできるようになります。

すべての前提条件を満たしていない場合は、PowerShell ウィンドウに警告が表示されます。 必ず[要件](how-to-connect-health-agent-install.md#requirements)を満たしてから、エージェントをインストールしてください。 次のスクリーンショットは、こうした警告の例を示しています。

![Azure AD Connect Health AD FS の構成のスクリプトを示すスクリーンショット。](./media/how-to-connect-health-agent-install/install4.png)

エージェントがインストールされたことを確認するには、サーバーで次のサービスを探します。 構成が完了していれば、これらのサービスが既に実行されているはずです。 そうでない場合は、構成が完了するまで停止しています。

* Azure AD Connect Health AD FS Diagnostics Service
* Azure AD Connect Health AD FS Insights Service
* Azure AD Connect Health AD FS Monitoring Service

![Azure AD Connect Health AD FS のサービスを示すスクリーンショット。](./media/how-to-connect-health-agent-install/install5.png)


### <a name="enable-auditing-for-ad-fs"></a>AD FS の監査を有効にする

> [!NOTE]
> このセクションが該当するのは AD FS サーバーのみです。 Web アプリケーション プロキシ サーバーではこれらの手順に従う必要はありません。
>

利用状況分析機能では、データを収集して分析する必要があります。 そのため、Azure AD Connect Health エージェントには、AD FS 監査ログの情報が必要です。 既定では、これらのログは有効になっていません。 AD FS サーバーで、AD FS の監査を有効にしたり、AD FS の監査ログを特定したりするには、次の手順を使用します。

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Windows Server 2012 R2 で AD FS の監査を有効にするには

1. [スタート] 画面で **[サーバー マネージャー]** を開き、 **[ローカル セキュリティ ポリシー]** を開きます。 または、タスク バーで **[サーバー マネージャー]** を開き、 **[ツール]、[ローカル セキュリティ ポリシー]** を選択します。
2. " *[セキュリティの設定]\[ローカル ポリシー]\[ユーザー権利の割り当て]* " フォルダーに移動します。 次に、 **[セキュリティ監査の生成]** をダブルクリックします。
3. **[ローカル セキュリティの設定]** タブで、AD FS サービス アカウントが表示されていることを確認します。 一覧に表示されていない場合は、 **[ユーザーまたはグループの追加]** を選択し、それを一覧に追加します。 **[OK]** をクリックします。
4. 監査を有効にするには、昇格された特権でコマンド プロンプト ウィンドウを開きます。 次に、次のコマンドを実行します。 
    
    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. **[ローカル セキュリティ ポリシー]** を閉じます。
    >[!Important]
    >次の手順は、プライマリ AD FS サーバーにのみ必要です。 
1. **AD FS 管理** スナップインを開きます。 ( **[サーバー マネージャー]** で、 **[ツール]**  >  **[AD FS の管理]** の順に選択します。)
1. **操作** ペインで、 **[フェデレーション サービス プロパティの編集]** を選択します。
1. **[フェデレーション サービス プロパティ]** ダイアログ ボックスの **[イベント]** タブを選択します。
1. **[成功の監査] および [失敗の監査]** のチェック ボックスをオンにし、 **[OK]** を選択します。
1. PowerShell を使用して詳細ログを有効にするには、次のコマンドを使用します。 

    `Set-AdfsProperties -LOGLevel Verbose`

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>Windows Server 2016 で AD FS の監査を有効にするには

1. [スタート] 画面で **[サーバー マネージャー]** を開き、 **[ローカル セキュリティ ポリシー]** を開きます。 または、タスク バーで **[サーバー マネージャー]** を開き、 **[ツール]、[ローカル セキュリティ ポリシー]** を選択します。
2. " *[セキュリティの設定]\[ローカル ポリシー]\[ユーザー権利の割り当て]* " フォルダーに移動し、 **[セキュリティ監査の生成]** をダブルクリックします。
3. **[ローカル セキュリティの設定]** タブで、AD FS サービス アカウントが表示されていることを確認します。 一覧に表示されていない場合は、 **[ユーザーまたはグループの追加]** を選択し、AD FS サービス アカウントを一覧に追加します。 **[OK]** をクリックします。
4. 監査を有効にするには、昇格された特権でコマンド プロンプト ウィンドウを開きます。 次に、次のコマンドを実行します。 

    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. **[ローカル セキュリティ ポリシー]** を閉じます。
    >[!Important]
    >次の手順は、プライマリ AD FS サーバーにのみ必要です。
1. **AD FS 管理** スナップインを開きます。 ( **[サーバー マネージャー]** で、 **[ツール]**  >  **[AD FS の管理]** の順に選択します。)
1. **操作** ペインで、 **[フェデレーション サービス プロパティの編集]** を選択します。
1. **[フェデレーション サービス プロパティ]** ダイアログ ボックスの **[イベント]** タブを選択します。
1. **[成功の監査] および [失敗の監査]** のチェック ボックスをオンにし、 **[OK]** を選択します。 成功の監査と失敗の監査は、既定で有効にする必要があります。
1. PowerShell ウィンドウを開き、次のコマンドを実行します。 

    `Set-AdfsProperties -AuditLevel Verbose`

既定では "基本" 監査レベルが有効になっています。 詳細については、[Windows Server 2016 での AD FS 監査の強化](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server)に関するページを参照してください。


#### <a name="to-locate-the-ad-fs-audit-logs"></a>AD FS の監査ログを特定するには

1. **イベント ビューアー** を開きます。
2. **[Windows ログ]** に移動し、 **[セキュリティ]** を選択します。
3. 右側の **[現在のログをフィルター]** を選択します。
4. **[イベント ソース]** で **[AD FS の監査]** を選択します。

    監査ログの詳細については、「[操作に関する質問](reference-connect-health-faq.md#operations-questions)」を参照してください。

    ![[現在のログをフィルター] ウィンドウを示すスクリーンショット。 [イベント ソース] フィールドで "AD FS の監査" が選択されています。](./media/how-to-connect-health-agent-install/adfsaudit.png)

> [!WARNING]
> グループ ポリシーで AD FS 監査を無効にできます。 AD FS 監査が無効になっている場合、ログイン アクティビティに関する利用状況分析は利用できません。 AD FS 監査を無効にするグループ ポリシーがないことを確認してください。
>


## <a name="install-the-agent-for-sync"></a>Sync 用のエージェントをインストールする

Sync 用 Azure AD Connect Health エージェントは、最新バージョンの Azure AD Connect に自動的にインストールされます。 Azure AD Connect for Sync を使用するには、[最新バージョンの Azure AD Connect をダウンロードして](https://www.microsoft.com/download/details.aspx?id=47594)、インストールします。

エージェントがインストール済みであることを確認するには、サーバーで以下のサービスを探します。 構成が完了していれば、サービスは既に実行されているはずです。 そうでない場合は、構成が完了するまでサービスは停止しています。

* Azure AD Connect Health Sync 分析サービス
* Azure AD Connect Health Sync 監視サービス

![サーバーで実行中の Azure AD Connect Health for Sync サービスを示すスクリーンショット。](./media/how-to-connect-health-agent-install/services.png)

> [!NOTE]
> Azure AD Connect Health を使用するには Azure AD Premium (P1 または P2) が必要であることに注意してください。 Azure AD Premium がない場合、Azure portal で構成を完了できません。 詳細については、「[要件](how-to-connect-health-agent-install.md#requirements)」を参照してください。
>
>

## <a name="manually-register-azure-ad-connect-health-for-sync"></a>Azure AD Connect Health for Sync を手動で登録する

Azure AD Connect を正常にインストールした後、Azure AD Connect Health for Sync エージェントの登録が失敗した場合は、PowerShell コマンドを使用してエージェントを手動で登録できます。

> [!IMPORTANT]
> この PowerShell コマンドは、Azure AD Connect をインストールした後でエージェントの登録が失敗した場合にのみ使用してください。
>
>

次の PowerShell コマンドを使用して、Sync 用 Azure AD Connect Health エージェントを手動で登録します。 Azure AD Connect Health サービスは、エージェントが正常に登録された後で開始されます。

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

このコマンドは次のパラメーターを受け取ります。

* **AttributeFiltering**: `$true` (既定値) Azure AD Connect が既定の属性セットを同期しておらず、フィルター処理された属性セットを使用するようにカスタマイズされている場合。 それ以外の場合は、 `$false`を指定します。
* **StagingMode**: `$false` (既定値) Azure AD Connect サーバーがステージング モードで "*ない*" 場合。 サーバーがステージング モードに構成されている場合は、`$true` を使用します。

認証を求められたら、Azure AD Connect の構成に使用したのと同じ全体管理者アカウント (admin@domain.onmicrosoft.com など) を使用する必要があります。

## <a name="install-the-agent-for-azure-ad-ds"></a>Azure AD DS 用のエージェントをインストールする

エージェントのインストールを開始するには、ダウンロードした *.exe* ファイルをダブルクリックします。 最初のウィンドウで、 **[インストール]** を選択します。

![AD DS 用 Azure AD Connect Health エージェントのインストール ウィンドウを示すスクリーンショット。](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install1.png)

インストールが完了したら、 **[今すぐ構成]** を選択します。

![Azure AD DS 用 Azure AD Connect Health エージェントのインストールを完了したウィンドウを示すスクリーンショット。](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install2.png)

コマンド プロンプト ウィンドウが開きます。 PowerShell で `Register-AzureADConnectHealthADDSAgent` が実行されます。 入力を求められたら、Azure にサインインします。

![Azure AD DS 用 Azure AD Connect Health エージェントのサインイン ウィンドウを示すスクリーンショット。](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install3.png)

サインインすると、PowerShell が続行されます。 これが終了したら、PowerShell を閉じることができます。 構成は完了です。

この時点でサービスが自動的に開始され、エージェントによるデータの監視と収集ができるようになります。 前のセクションで挙げたすべての前提条件が満たされていない場合、PowerShell ウィンドウに警告が表示されます。 必ず[要件](how-to-connect-health-agent-install.md#requirements)を満たしてから、エージェントをインストールしてください。 次のスクリーンショットは、こうした警告の例を示しています。

![Azure AD DS 用 Azure AD Connect Health エージェントの構成に関する警告を示すスクリーンショット。](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install4.png)

エージェントがインストールされていることを確認するには、ドメイン コントローラーで次のサービスを探します。

* Azure AD Connect Health AD DS Insights Service
* Azure AD Connect Health AD DS Monitoring Service

構成が完了していれば、これらのサービスが既に実行されているはずです。 そうでない場合は、構成が完了するまで停止しています。

![ドメイン コントローラーで実行中のサービスを示すスクリーンショット。](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install5.png)

### <a name="quickly-install-the-agent-on-multiple-servers"></a>複数のサーバーにエージェントをすばやくインストールする

1. Azure AD にユーザー アカウントを作成します。 パスワードを使用して、それをセキュリティで保護します。
2. ポータルを使用して、Azure AD Connect Health でこのローカル Azure AD アカウントに **所有者** ロールを割り当てます。 [以下の手順](how-to-connect-health-operations.md#manage-access-with-azure-rbac)に従ってください。 ロールをすべてのサービス インスタンスに割り当てます。 
3. インストールのために、 *.exe* MSI ファイルをローカル ドメイン コントローラーにダウンロードします。
4. 次のスクリプトを実行します。 パラメーターを、新しいユーザー アカウントとそのパスワードに置き換えます。 

    ```powershell
    AdHealthAddsAgentSetup.exe /quiet
    Start-Sleep 30
    $userName = "NEWUSER@DOMAIN"
    $secpasswd = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force
    $myCreds = New-Object System.Management.Automation.PSCredential ($userName, $secpasswd)
    import-module "C:\Program Files\Azure Ad Connect Health Adds Agent\PowerShell\AdHealthAdds"
     
    Register-AzureADConnectHealthADDSAgent -Credential $myCreds
    
    ```

完了したら、次のタスクのうち 1 つ以上を行うことでローカル アカウントのアクセスを削除できます。 
* Azure AD Connect Health のローカル アカウントのロール割り当てを削除します。
* ローカル アカウントのパスワードを回転します。 
* Azure AD ローカル アカウントを無効にします。
* Azure AD ローカル アカウントを削除します。  

## <a name="register-the-agent-by-using-powershell"></a>PowerShell を使用してエージェントを登録する

適切なエージェントの *setup.exe* ファイルをインストールしたら、ロールに応じて次の PowerShell コマンドを使用して、エージェントを登録できます。 PowerShell ウィンドウを開き、適切なコマンドを実行します。

```powershell
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

> [!NOTE]
> ソブリン クラウドに登録するには、次のコマンド ラインを使用します。
>
> ```powershell
> Register-AzureADConnectHealthADFSAgent -UserPrincipalName upn-of-the-user
> Register-AzureADConnectHealthADDSAgent -UserPrincipalName upn-of-the-user
> Register-AzureADConnectHealthSyncAgent -UserPrincipalName upn-of-the-user
> ```
>


これらのコマンドは、`Credential` をパラメーターとして受け入れて非対話的に登録を完了するか、Server Core を実行しているマシンで登録を完了します。 次の点に留意します。
* `Credential` は、パラメーターとして渡される PowerShell 変数内で取得できます。
* エージェントを登録するためのアクセス許可を持ち、多要素認証が有効になって "*いない*" 任意の Azure AD ID を指定できます。
* 既定では、全体管理者には、エージェントを登録するためのアクセス許可があります。 より低い権限を持つ ID に対してこの手順の実行を許可することもできます。 詳細については、[Azure RBAC](how-to-connect-health-operations.md#manage-access-with-azure-rbac) に関するページを参照してください。

```powershell
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>HTTP プロキシを使用するように Azure AD Connect Health エージェントを構成する

HTTP プロキシを使用するように Azure AD Connect Health エージェントを構成できます。

> [!NOTE]
> * `Netsh WinHttp set ProxyServerAddress` がサポートされていません。 エージェントは、Windows HTTP サービスではなく System.Net を使用して Web 要求を行います。
> * 構成済みの HTTP プロキシ アドレスを使用して、暗号化された HTTP メッセージがパススルーされます。
> * 認証されたプロキシ (HTTPBasic を使用) はサポートされていません。
>
>

### <a name="change-the-agent-proxy-configuration"></a>エージェント プロキシ構成を変更する

HTTP プロキシを使用するように Azure AD Connect Health エージェントを構成する場合、以下が可能です。
* 既存のプロキシ設定をインポートする。
* プロキシ アドレスを手動で指定する。
* 既存のプロキシ構成をクリアする。

> [!NOTE]
> プロキシ設定を更新するには、すべての Azure AD Connect Health エージェントサービスを再起動する必要があります。 次のコマンドを実行します。
>
> `Restart-Service AzureADConnectHealth*`

#### <a name="import-existing-proxy-settings"></a>既存のプロキシ設定をインポートする

Azure AD Connect Health エージェントで Internet Explorer HTTP プロキシ設定を使用できるように、その設定をインポートすることができます。 正常性エージェントを実行している各サーバーで、次の PowerShell コマンドを実行します。

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings
```

Azure AD Connect Health エージェントで WinHTTP プロキシ設定を使用できるように、それをインポートすることができます。 正常性エージェントを実行している各サーバーで、次の PowerShell コマンドを実行します。

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp
```

#### <a name="specify-proxy-addresses-manually"></a>プロキシ アドレスを手動で指定する

プロキシ サーバーを手動で指定することができます。 正常性エージェントを実行している各サーバーで、次の PowerShell コマンドを実行します。

```powershell
Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port
```

次に例を示します。 

`Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver: 443`

この例では、次のように記述されています。
* `address` 設定には、DNS で解決可能なサーバー名または IPv4 アドレスを指定できます。
* `port` は省略できます。 その場合、443 が既定のポートになります。

#### <a name="clear-the-existing-proxy-configuration"></a>既存のプロキシ構成をクリアする

次のコマンドを実行することで、既存のプロキシ構成をクリアすることができます。

```powershell
Set-AzureAdConnectHealthProxySettings -NoProxy
```

### <a name="read-current-proxy-settings"></a>現在のプロキシ設定の読み取り

現在のプロキシ設定を読み取るには、次のコマンドを実行します。

```powershell
Get-AzureAdConnectHealthProxySettings
```

## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Azure AD Connect Health サービスへの接続のテスト

場合によっては、Azure AD Connect Health エージェントが Azure AD Connect Health サービスとの接続を失うことがあります。 この接続喪失の原因としては、ネットワークの問題、アクセス許可の問題、およびその他のさまざまな問題が考えられます。

エージェントが Azure AD Connect Health サービスに 2 時間以上データを送信できない場合は、ポータルに "Health Service data is not up to date (Health サービス データが最新ではありません)" というアラートが表示されます。 

影響を受ける Azure AD Connect Health エージェントがデータを Azure AD Connect Health サービスにアップロードできるかどうかを調べるには、次の PowerShell コマンドを実行します。

```powershell
Test-AzureADConnectHealthConnectivity -Role ADFS
```

role パラメーターは、現在、以下の値を受け取ります。

* ADFS
* 同期
* ADDS

> [!NOTE]
> 接続ツールを使用するには、まずエージェントを登録する必要があります。 エージェントの登録を完了できない場合は、Azure AD Connect Health のすべての[要件](how-to-connect-health-agent-install.md#requirements)が満たされていることを確認してください。 既定では、接続はエージェントの登録中にテストされます。
>
>

## <a name="next-steps"></a>次のステップ

次の関連記事を参照してください。

* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health の操作](how-to-connect-health-operations.md)
* [AD FS での Azure AD Connect Health の使用](how-to-connect-health-adfs.md)
* [Azure AD Connect Health for Sync の使用](how-to-connect-health-sync.md)
* [Azure AD DS での Azure AD Connect Health の使用](how-to-connect-health-adds.md)
* [Azure AD Connect Health の FAQ](reference-connect-health-faq.md)
* [Azure AD Connect Health のバージョン履歴](reference-connect-health-version-history.md)
