---
title: Windows Virtual Desktop (クラシック) の診断ツールをデプロイする - Azure
description: Windows Virtual Desktop (クラシック) の診断 UX ツールをデプロイする方法。
author: Heidilohr
ms.topic: how-to
ms.date: 12/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9bdba36e2ff4721bd024369788c7dc04f066e9a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565582"
---
# <a name="deploy-the-windows-virtual-desktop-classic-diagnostics-tool"></a>Windows Virtual Desktop (クラシック) の診断ツールをデプロイする

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトがサポートされていない Windows Virtual Desktop (クラシック) に適用されます。

Windows Virtual Desktop の診断ツールでは、次のことを行うことができます。

- 1 週間にわたり 1 人のユーザーの診断アクティビティ (管理、接続、またはフィード) を参照します。
- Log Analytics ワークスペースから接続アクティビティについてのセッション ホスト情報を収集します。
- 特定のホストの仮想マシン (VM) のパフォーマンスの詳細を確認します。
- セッション ホストにサインインしているユーザーを確認します。
- 特定のセッション ホストでアクティブなユーザーにメッセージを送信します。
- セッション ホストからユーザーをサインアウトします。

## <a name="prerequisites"></a>前提条件

ツールの Azure Resource Manager テンプレートをデプロイする前に、Azure Active Directory アプリの登録と Log Analytics ワークスペースを作成する必要があります。 この操作を行うには、ユーザーまたは管理者が次のアクセス許可を必要とします。

- Azure サブスクリプションの所有者
- Azure サブスクリプション内にリソースを作成するためのアクセス許可
- Azure AD アプリを作成するためのアクセス許可
- RDS 所有者または共同作成者の権限

また、開始する前に、次の 2 つの PowerShell モジュールもインストールする必要があります。

- [Azure PowerShell モジュール](/powershell/azure/install-az-ps)
- [Azure AD モジュール](/powershell/azure/active-directory/install-adv2)

サインインするときに、サブスクリプション ID が準備できていることを確認します。

すべて整ったら、Azure AD アプリの登録を作成できます。

## <a name="create-an-azure-active-directory-app-registration"></a>Azure Active Directory アプリの登録を作成する

このセクションでは、PowerShell を使用して、サービス プリンシパルで Azure Active Directory アプリを作成し、それに対する API アクセス許可を取得する方法について説明します。

>[!NOTE]
>API アクセス許可は、Windows Virtual Desktop、Log Analytics、および Microsoft Graph API アクセス許可で、Azure Active Directory アプリケーションに追加されます。

1. PowerShell を管理者として開きます。
2. 診断ツールに使用する Azure サブスクリプションで、所有者または共同作成者のアクセス許可を持つアカウントを使用して Azure にサインインします。
   ```powershell
   Login-AzAccount
   ```
3. 同じアカウントを使用して Azure AD にサインインします。
   ```powershell
   Connect-AzureAD
   ```
4. [RDS-Templates GitHub リポジトリ](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts)に移動し、PowerShell で **CreateADAppRegistrationforDiagnostics.ps1** スクリプトを実行します。
5.  スクリプトからアプリに名前を付けるように求められたら、一意のアプリ名を入力します。


スクリプトが正常に実行されると、出力に次の内容が表示されるはずです。

-  アプリにサービス プリンシパル ロールの割り当てがあることを確認するメッセージ。
-  診断ツールをデプロイするときに必要になるクライアント ID とクライアント シークレット キー。

アプリの登録が完了したので、次に Log Analytics ワークスペースを構成します。

## <a name="configure-your-log-analytics-workspace"></a>Log Analytics ワークスペースを構成する

可能な限り最適なエクスペリエンスを得るために、次のパフォーマンス カウンターを使用して Log Analytics ワークスペースを構成することをお勧めします。これにより、リモート セッションでユーザー エクスペリエンスのステートメントを導き出すことができます。 提案されたしきい値で推奨されるカウンターの一覧については、「[Windows performance counter thresholds](deploy-diagnostics.md#windows-performance-counter-thresholds)」 (Windows パフォーマンス カウンターのしきい値) を参照してください。

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>PowerShell を使用して Azure Log Analytics ワークスペースを作成する

PowerShell スクリプトを実行して Log Analytics ワークスペースを作成し、推奨される Windows パフォーマンス カウンターを構成して、ユーザー エクスペリエンスとアプリのパフォーマンスを監視することができます。

>[!NOTE]
>使用したい PowerShell スクリプトを使わずに作成した既存の Log Analytics ワークスペースが既にある場合は、「[Validate the script results in the Azure portal](#validate-the-script-results-in-the-azure-portal)」 (Azure portal でスクリプトの結果を検証する) までスキップしてください。

PowerShell スクリプトを実行するには:

1.  PowerShell を管理者として開きます。
2.  [RDS-Templates GitHub リポジトリ](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts)に移動し、PowerShell で **CreateLogAnalyticsWorkspaceforDiagnostics.ps1** スクリプトを実行します。
3. 各パラメーターの値を次のように入力します。

    - **ResourceGroupName** に、リソース グループの名前を入力します。
    - **LogAnalyticsWorkspaceName** に、Log Analytics ワークスペースの一意の名前を入力します。
    - **Location** に、使用する Azure リージョンを入力します。
    - **Azure サブスクリプション ID** を入力します。これは、Azure portal の **[サブスクリプション]** の下に見つかります。

4. 代理管理者アクセス権を持つユーザーの資格情報を入力します。
5. 同じユーザーの資格情報を使用して、Azure portal にサインインします。
6. 後で使用するために、LogAnalyticsWorkspace ID を書き留めておきます。
7. PowerShell スクリプトを使用して Log Analytics ワークスペースを設定している場合、パフォーマンス カウンターが既に構成されているはずであるため、「[Validate the script results in the Azure portal](#validate-the-script-results-in-the-azure-portal)」 (Azure portal でスクリプトの結果を検証する) までスキップすることができます。 そうでない場合は、次の手順に進みます。

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>既存の Log Analytics ワークスペースで Windows パフォーマンス カウンターを構成する

このセクションは、前のセクションで PowerShell スクリプトを使用せずに作成された既存の Azure Log Analytics ワークスペースを使用するユーザーを対象としています。 スクリプトを使用していない場合は、推奨される Windows パフォーマンス カウンターを手動で構成する必要があります。

推奨されるパフォーマンス カウンターを手動で構成する方法を次に示します。

1. インターネット ブラウザーを開き、管理者アカウントで [Azure portal](https://portal.azure.com/) にサインインします。
2. 次に、**Log Analytics ワークスペース** に移動して、構成されている Windows パフォーマンス カウンターを確認します。
3. **[設定]** セクションで **[詳細設定]** を選択します。
4. その後、 **[データ]**  >  **[Windows パフォーマンス カウンター]** の順に移動し、次のカウンターを追加します。

    -   LogicalDisk(\*)\\%Free Space
    -   LogicalDisk(C:)\\Avg.ディスク キューの長さ
    -   Memory(\*)\\Available Mbytes
    -   Processor Information(\*)\\Processor Time
    -   User Input Delay per Session(\*)\\Max Input Delay

パフォーマンス カウンターの詳細については、「[Azure Monitor での Windows および Linux のパフォーマンス データ ソース](../../azure-monitor/agents/data-sources-performance-counters.md)」を参照してください。

>[!NOTE]
>構成した追加のカウンターは、診断ツール自体には表示されません。 診断ツールに表示されるようにするには、ツールの構成ファイルを構成する必要があります。 詳細な管理でこれを行う方法の手順については、後日 GitHub で提供されます。

## <a name="validate-the-script-results-in-the-azure-portal"></a>Azure portal でスクリプトの結果を検証する

診断ツールのデプロイを続行する前に、Azure Active Directory アプリケーションに API のアクセス許可があり、Log Analytics ワークスペースに事前構成済みの Windows パフォーマンス カウンターがあることを確認することをお勧めします。

### <a name="review-your-app-registration"></a>アプリの登録を確認する

アプリの登録に API アクセス許可があることを確認するには:

1. ブラウザーを開き、管理者アカウントを使用して [Azure portal](https://portal.azure.com/) に接続します。
2. **[Azure Active Directory]** に移動します。
3. **[アプリの登録]** にアクセスし、 **[すべてのアプリケーション]** を選択します。
4. 「[Azure Active Directory アプリの登録を作成する](deploy-diagnostics.md#create-an-azure-active-directory-app-registration)」の手順 5 で入力したものと同じアプリ名を使用して Azure AD アプリの登録を探します。

### <a name="review-your-log-analytics-workspace"></a>Log Analytics ワークスペースを確認する

Log Analytics ワークスペースに事前構成済みの Windows パフォーマンス カウンターがあることを確認するには:

1. [Azure portal](https://portal.azure.com/) で、 **[Log Analytics ワークスペース]** に移動して、構成されている Windows パフォーマンス カウンターを確認します。
2. **[設定]** で **[詳細設定]** を選択します。
3. その後、 **[データ]**  >  **[Windows パフォーマンス カウンター]** に移動します。
4. 次のカウンターが事前に構成されていることを確認します。

   - LogicalDisk(\*)\\%Free Space:ディスク上の使用可能な合計領域のうちの空き領域の量をパーセントで表示します。
   - LogicalDisk(C:)\\Avg.Disk Queue Length:C ドライブのディスク転送要求の長さ。 この値は、多くても 2 を超えることはできません。
   - Memory(\*)\\Available Mbytes:システムで使用可能なメモリ (メガバイト単位)。
   - Processor Information(\*)\\Processor Time: プロセッサが非アイドル スレッドを実行するために費やした経過時間のパーセンテージ。
   - User Input Delay per Session(\*)\\Max Input Delay

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>Log Analytics ワークスペースで VM に接続する

VM の正常性を確認できるようにするには、Log Analytics 接続を有効にする必要があります。 VM を接続するには、次の手順に従います。

1. ブラウザーを開いて、管理者アカウントで [Azure portal](https://portal.azure.com/) にサインインします。
2. Log Analytics ワークスペースに移動します。
3. 左側のパネルの [ワークスペースのデータ ソース] で **[仮想マシン]** を選択します。
4. 接続先の VM の名前を選択します。
5. **[接続]** を選択します。

## <a name="deploy-the-diagnostics-tool"></a>診断ツールをデプロイする

診断ツールの Azure Resource Manager テンプレートをデプロイするには:

1.  [GitHub の Azure RDS-Templates ページ](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy)に移動します。
2.  Azure にテンプレートをデプロイし、テンプレートの指示に従います。 次の情報を使用できることを確認してください。

    -   Client-Id
    -   Client-Secret
    -   Log Analytics ワークスペース ID

3.  入力パラメーターを指定したら、利用規約に同意し、 **[購入]** を選択します。

デプロイには 2、3 分かかります。 デプロイが正常に完了したら、リソース グループにアクセスし、Web アプリと App Service プランのリソースが存在することを確認します。

その後、リダイレクト URI を設定する必要があります。

### <a name="set-the-redirect-uri"></a>リダイレクト URI を設定する

リダイレクト URI を設定するには:

1.  [Azure portal](https://portal.azure.com/) で、 **[App Services]** に移動し、作成したアプリケーションを見つけます。
2.  概要ページに移動して、そこで見つかった URL をコピーします。
3.  **[アプリの登録]** に移動し、デプロイするアプリを選択します。
4.  左側のパネルの [管理] セクションで、 **[認証]** を選択します。
5.  **[リダイレクト URI]** テキスト ボックスに目的のリダイレクト URI を入力し、メニューの左上隅にある **[保存]** を選択します。
6. [種類] の下のドロップダウンメニューで **[Web]** を選択します。
7. アプリの概要ページから URL を入力し、 **/security/signin-callback** を末尾に追加します。 (例: `https://<yourappname>.azurewebsites.net/security/signin-callback`)。

   > [!div class="mx-imgBorder"]
   > ![リダイレクト URI ページ](../media/redirect-uri-page.png)

8. ここで、ご使用の Azure リソースに移動し、テンプレートで指定した名前の Azure App Services リソースを選択し、それに関連付けられている URL に移動します。 (たとえば、テンプレートで使用したアプリ名が `contosoapp45` の場合、関連付けられている URL は <http://contoso.azurewebsites.net> になります)。
9. 適切な Azure Active Directory ユーザー アカウントを使用してサインインします。
10.   **[Accept]\(承認\)** を選択します。

## <a name="distribute-the-diagnostics-tool"></a>診断ツールを配布する

ユーザーが診断ツールを使用できるようにする前に、ユーザーが次のアクセス許可を持っていることを確認してください。

- ユーザーは、Log Analytics の読み取りアクセス権が必要です。 詳細については、[「Azure Monitor での役割、アクセス許可、およびセキュリティの概要」](../../azure-monitor/roles-permissions-security.md)を参照してください。
-  ユーザーは、Windows Virtual Desktop テナントに対する読み取りアクセス権 (RDS 閲覧者ロール) も必要です。 詳細については、「[Windows Virtual Desktop における委任されたアクセス](delegated-access-virtual-desktop-2019.md)」を参照してください。

また、ユーザーに次の情報を提供する必要もあります。

- アプリの URL
- ユーザーがアクセスできるテナント グループの個々のテナントの名前。

## <a name="use-the-diagnostics-tool"></a>診断ツールの使用

組織から受け取った情報を使用してアカウントにサインインしたら、アクティビティをクエリするユーザー用に UPN を準備します。 検索では、過去 1 週間以内に発生した、指定したアクティビティの種類のすべてのアクティビティが表示されます。

### <a name="how-to-read-activity-search-results"></a>アクティビティ検索結果を読み取る方法

アクティビティは、タイムスタンプで並べ替えられ、最新のアクティビティが先頭になります。 結果でエラーが返された場合は、まずサービス エラーであるかどうかを確認します。 サービス エラーについては、問題のデバッグに役立つアクティビティ情報を含むサポート チケットを作成します。 その他のすべてのエラーの種類は、通常ユーザーまたは管理者が解決できます。 最も一般的なエラー シナリオとその解決方法の一覧については、「[問題の特定と診断](diagnostics-role-service-2019.md#common-error-scenarios)」を参照してください。

>[!NOTE]
>サービス エラーは、リンクされたドキュメントで "外部エラー" と呼ばれています。 これは、PowerShell 参照を更新すると変更されます。

接続アクティビティに、複数のエラーがある場合があります。 アクティビティの種類を展開すると、ユーザーに発生したその他のエラーを表示できます。 エラー コードの名前を選択して、ダイアログを開き、それに関する詳細情報を確認します。

### <a name="investigate-the-session-host"></a>セッションホストを調査する

検索結果で、情報を必要とするセッション ホストを検索して選択します。

セッション ホストの正常性を分析できます。

- 定義済みのしきい値に基づいて、Log Analytics によってクエリされるセッション ホストの正常性情報を取得できます。
- アクティビティがない場合、またはセッション ホストが Log Analytics に接続されていない場合、情報を取得できません。

セッション ホストでユーザーと対話することもできます。

- サインアウトするか、サインインしているユーザーにメッセージを送信することができます。
- 最初に検索したユーザーが既定で選択されていますが、追加のユーザーを選択してメッセージを送信したり、一度に複数のユーザーをサインアウトさせたりすることもできます。

### <a name="windows-performance-counter-thresholds"></a>Windows パフォーマンス カウンターのしきい値

- LogicalDisk(\*)\\%Free Space:

    - 論理ディスク上の使用可能な合計領域のうち、空いている割合を表示します。
    - しきい値: 20% 未満が異常とマークされます。

- LogicalDisk(C:)\\Avg.Disk Queue Length:

    - ストレージシステムの状態を表します。
    - しきい値: 5 を超えると、異常とマークされます。

- Memory(\*)\\Available Mbytes:

    - システムで使用可能なメモリ。
    - しきい値: 500 メガバイト未満が異常とマークされます。

- Processor Information(\*)\\Processor Time:

    - しきい値: 80% を超えると、異常とマークされます。

- [User Input Delay per Session(\*)\\Max Input Delay](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/):

    - しきい値: 2000 ミリ秒を超えると、異常とマークされます。

## <a name="next-steps"></a>次のステップ

- [Log Analytics での診断の使用](diagnostics-log-analytics-2019.md)に関する記事で、アクティビティ ログの監視方法について学習します。
- 「[問題の特定と診断](diagnostics-role-service-2019.md)」で、一般的なエラー シナリオとその解決方法について確認します。