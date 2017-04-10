---
title: "Azure 実行アカウントの構成 | Microsoft Docs"
description: "Azure Automation のセキュリティプリンシパル認証の作成、テスト、使用例をわかりやすく説明しています。"
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
keywords: "サービス プリンシパル名, setspn, Azure の認証"
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/06/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: fbca3d195290551d37606e231b997a40a602351f
ms.lasthandoff: 03/29/2017


---

# <a name="authenticate-runbooks-with-an-azure-run-as-account"></a>Azure 実行アカウントを使用した Runbook の認証
この記事では、Azure Automation アカウントを Azure Portal で構成する方法について説明します。 具体的には、実行アカウント機能を使って Runbook の認証を行い、Azure Resource Manager または Azure サービス管理のリソースを管理します。

Azure Portal で Automation アカウントを作成すると、次の 2 つのアカウントが自動的に作成されます。

* 実行アカウント。 Azure Active Directory (Azure AD) のサービス プリンシパルと証明書は、このアカウントで作成します。 また、Runbook を使って Resource Manager リソースを管理する共同作成者の RBAC (ロールベースのアクセス制御) も、このアカウントで割り当てます。
* クラシック実行アカウント。 サービス管理リソースまたはクラシック リソースを Runbook で管理する際に必要な管理証明書は、このアカウントでアップロードします。

Automation アカウントを作成することによって必要な作業が単純化され、オートメーションのニーズを満たす Runbook をすぐに作成し、デプロイすることができます。

実行アカウントとクラシック実行アカウントを使用すると、次のことができます。

* Azure Portal での Runbook を使用した Resource Manager リソースまたはサービス管理リソースの管理に、Azure を使用した標準的な認証方法を取り入れる。
* グローバル Runbook の使用を自動化する (Azure アラート内で構成可能)。

> [!NOTE]
> [Azure アラート統合機能](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)と Automation Global Runbook の連携には、実行アカウントとクラシック実行アカウントを使って構成された Automation アカウントが必要です。 既に実行アカウントとクラシック実行アカウントが定義されている Automation アカウントを選んで利用できるほか、Automation アカウントを新たに作成することもできます。
>  

この記事では、Azure Portal からの Automation アカウントの作成方法を紹介すると共に、Azure PowerShell を使用して Automation アカウントを更新する方法、アカウント構成を管理する方法、Runbook 内で認証を行う方法について説明します。

Automation アカウントの作成を開始する前に、以下の事柄を理解し、考慮に入れることをお勧めします。

* Automation アカウントを作成しても、既にクラシック デプロイメント モデルまたは Resource Manager デプロイメント モデルで作成した Automation アカウントは影響を受けません。
* このプロセスは、Azure Portal で作成した Automation アカウントに対してのみ有効です。 Azure クラシック ポータルからアカウントを作成しようとしても、実行アカウントの構成はレプリケートされません。
* 現時点で、クラシック リソースを管理するための Runbook と資産 (スケジュールや変数など) を所有しており、かつ Runbook を新しいクラシック実行アカウントで認証する場合は、次のいずれかの操作を行います。

  * クラシック実行アカウントを作成するには、「実行アカウントを管理する」セクションの手順に従います。 
  * 既存のアカウントを更新するには、「PowerShell を使用して Automation アカウントを更新する」セクションの PowerShell スクリプトを使用します。
* 新しい実行アカウントまたはクラシック実行 Automation アカウントを使って認証を行うには、「[認証コードの例](#authentication-code-examples)」のセクションにあるサンプル コードを使用して既存の Runbook を編集する必要があります。

    >[!NOTE]
    >実行アカウントは、Resource Manager リソースに対する認証を証明書ベースのサービス プリンシパルを使って行う場合に使用されます。 クラシック実行アカウントは、サービス管理リソースに対する認証を管理証明書を使って行う場合に使用します。

## <a name="create-an-automation-account-from-the-azure-portal"></a>Azure Portal から Automation アカウントを作成する
このセクションで Azure Portal から Azure Automation アカウントを作成すると、Azure Portal によって実行アカウントとクラシック実行アカウントの両方が作成されます。

>[!NOTE]
>Automation アカウントを作成するためには、サービス管理者ロールのメンバーであるか、サブスクリプションのアクセス権を付与できる共同管理者である必要があります。 また、そのサブスクリプションの既定の Active Directory インスタンスにユーザーとして追加されている必要があります。 このアカウントを特権ロールに割り当てる必要はありません。
>
>サブスクリプションの Active Directory インスタンスのメンバーになっていない状態で、サブスクリプションの共同管理者ロールに追加された場合、Active Directory にゲストとして追加されることになります。 この場合、"…を作成するためのアクセス許可がありません" という警告が **[Automation アカウントの追加]** ブレードに表示されます。
>
>先に共同管理者ロールに追加されていたユーザーは、サブスクリプションの Active Directory インスタンスから削除した後、Active Directory の完全なユーザーとして再度追加できます。 このような状況を検証するには、Azure Portal の **[Azure Active Directory]** ウィンドウで、**[ユーザーとグループ]**、**[すべてのユーザー]**、特定のユーザー、**[プロファイル]** の順に選択します。 ユーザーのプロファイルの下部にある **[ユーザー タイプ]** 属性の値は、**[ゲスト]** と一致しないようにする必要があります。
>

1. サブスクリプション管理ロールのメンバーかつサブスクリプションの共同管理者であるアカウントを使用して、Azure Portal にサインインします。

2. **[Automation アカウント]**を選択します。

3. **[Automation アカウント]** ブレードで **[追加]** をクリックします。
**[Automation アカウントの追加]** ブレードが表示されます。

 ![[Automation アカウントの追加] ブレード](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)

   > [!NOTE]
   > お使いのアカウントがサブスクリプション管理者ロールのメンバーでなく、サブスクリプションの共同管理者でもない場合、**[Automation アカウントの追加]** ブレードに次の警告が表示されます。
   >
   >![Add Automation Account Warning](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)
   >
   >

4. **[Automation アカウントの追加]** ブレードの **[名前]** ボックスに、新しい Automation アカウントの名前を入力します。

5. 複数のサブスクリプションがある場合は、次の手順に従います。

    a. **[サブスクリプション]** で、新しいアカウントのサブスクリプションを指定します。

    b. **[リソース グループ]** で、**[新規作成]** または **[既存のものを使用]** をクリックします。

    c. **[場所]** で、Azure データセンターを指定します。

6. **[Azure 実行アカウントの作成]** で、**[はい]** を選択し、**[作成]** をクリックします。

   > [!NOTE]
   > 実行アカウントを作成しなかった場合 (**[いいえ]** を選択した場合)、**[Automation アカウントの追加]** ブレードに警告メッセージが表示されます。 Azure Portal でアカウントを作成している間は、クラシック サブスクリプションまたは Resource Manager サブスクリプションのディレクトリ サービスには対応する認証 ID が割り当てられず、 サブスクリプション内のリソースにアクセスすることはできません。 この場合、このアカウントを参照する Runbook は認証を通過できず、これらのデプロイメント モデルのリソースに対するタスクを実行することができません。
   >
   > ![[Automation アカウントの追加] ブレードの警告メッセージ](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)
   >
   > またサービス プリンシパルが作成されないので、共同作成者ロールは割り当てられません。
   >

7. Azure によって Automation アカウントが作成されている間、メニューの **[通知]** で進行状況を追跡できます。

### <a name="resources"></a>リソース
Automation アカウントが正常に作成されると、いくつかのリソースが自動的に作成されます。 これらのリソースの概要を以下の 2 つの表に示します。

#### <a name="run-as-account-resources"></a>実行アカウントのリソース

| リソース | Description |
| --- | --- |
| AzureAutomationTutorial Runbook | 実行アカウントを使用した認証の方法と、すべての Resource Manager リソースの取得方法を示す、サンプルのグラフィカルな Runbook。 |
| AzureAutomationTutorialScript Runbook | 実行アカウントを使用した認証の方法と、すべての Resource Manager リソースの取得方法を示す、サンプルの PowerShell Runbook。 |
| AzureRunAsCertificate | Automation アカウントの作成時に自動的に作成される証明書資産、または既存のアカウント用に PowerShell スクリプト (後述) を使用して作成される証明書資産。 この証明書により、Azure に対する認証を行い、Runbook から Azure Resource Manager リソースを管理できるようになります。 この証明書には、1 年の有効期間があります。 |
| AzureRunAsConnection | Automation アカウントの作成時に自動的に作成される接続資産、または既存のアカウント用に PowerShell スクリプトを使用して作成される接続資産。 |

#### <a name="classic-run-as-account-resources"></a>クラシック実行アカウントのリソース

| リソース | Description |
| --- | --- |
| AzureClassicAutomationTutorial Runbook | クラシック デプロイメント モデルを使ってサブスクリプションに作成されたすべての VM をクラシック実行アカウント (証明書) を使って取得し、その VM の名前と状態を出力するサンプルのグラフィカルな Runbook。 |
| AzureClassicAutomationTutorial Script Runbook | サブスクリプション内のすべてのクラシック VM をクラシック実行アカウント (証明書) を使って取得し、その VM の名前と状態を出力するサンプルの PowerShell Runbook。 |
| AzureClassicRunAsCertificate | Runbook から Azure のクラシック リソースを管理できるよう、Azure に対する認証に使用される自動的に作成される証明書資産。 この証明書には、1 年の有効期間があります。 |
| AzureClassicRunAsConnection | Runbook から Azure のクラシック リソースを管理できるよう、Azure に対する認証に使用される自動的に作成される接続資産。 |

## <a name="verify-run-as-authentication"></a>実行アカウントの認証を検証する
新しい実行アカウントを使用して正しく認証できることを確認するために小さなテストを実施します。

1. 先ほど作成した Automation アカウントを Azure ポータルで開きます。

2. **[Runbook]** タイルをクリックして、Runbook の一覧を開きます。

3. **AzureAutomationTutorialScript** Runbook を選択し、**[開始]** をクリックして、Runbook を開始します。 次のイベントが発生します。
 * [Runbook ジョブ](automation-runbook-execution.md)が作成されると、**[ジョブ]** ブレードが表示され、ジョブの状態が **[ジョブの概要]** タイルに表示されます。
 * 最初のジョブの状態は "**キュー登録済み**" であり、クラウドの runbook worker が使用できるようになるのを待っていることを示します。
 * その後、ワーカーがジョブを要求すると**開始中**になり、
 * Runbook が実行を開始すると**実行中**になります。
 * Runbook ジョブの実行が完了すると、状態は **[完了]** と表示されます。

       ![Security Principal Runbook Test](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)
4. Runbook の詳細な結果を表示するには、**[出力]** タイルをクリックします。  
**[出力]** ブレードが表示され、Runbook が正常に認証されて、リソース グループ内で使用可能なすべてのリソースの一覧が返されたことがわかります。

5. **[出力]** ブレードを閉じて、**[ジョブの概要]** ブレードに戻ります。

6. **[ジョブの概要]** ブレードと、対応する **[AzureAutomationTutorialScript]** Runbook ブレードを閉じます。

## <a name="verify-classic-run-as-authentication"></a>クラシック実行認証を検証する
新しいクラシック実行アカウントを使用して正しく認証できることを確認するために、先ほどと同様の小さなテストを実施します。

1. 先ほど作成した Automation アカウントを Azure ポータルで開きます。

2. **[Runbook]** タイルをクリックして、Runbook の一覧を開きます。

3. **AzureClassicAutomationTutorialScript** Runbook を選択し、**[開始]** をクリックして、Runbook を開始します。 次のイベントが発生します。

 * [Runbook ジョブ](automation-runbook-execution.md)が作成されると、**[ジョブ]** ブレードが表示され、ジョブの状態が **[ジョブの概要]** タイルに表示されます。
 * 最初のジョブの状態は "**キュー登録済み**" であり、クラウドの runbook worker が使用できるようになるのを待っていることを示します。
 * その後、ワーカーがジョブを要求すると**開始中**になり、
 * Runbook が実行を開始すると**実行中**になります。
 * Runbook ジョブの実行が完了すると、状態は **[完了]** と表示されます。

    ![Security Principal Runbook Test](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
4. Runbook の詳細な結果を表示するには、**[出力]** タイルをクリックします。  
**[出力]** ブレードが表示され、Runbook が正常に認証されて、サブスクリプション内のすべてのクラシック VM の一覧が返されたことがわかります。

5. **[出力]** ブレードを閉じて、**[ジョブの概要]** ブレードに戻ります。

6. **[ジョブの概要]** ブレードと、対応する **[AzureAutomationTutorialScript]** Runbook ブレードを閉じます。

## <a name="managing-your-run-as-account"></a>実行アカウントを管理する
証明書は、Automation アカウントの有効期限が切れる前のどこかの時点で書き換える必要があります。 また、実行アカウントが侵害されたと思われる場合は、実行アカウントを削除して再作成することができます。 このセクションでは、これらの操作を実行する方法について説明します。

### <a name="self-signed-certificate-renewal"></a>自己署名証明書の書き換え
実行アカウント用に作成された自己署名証明書は、作成日から 1 年後に有効期限が切れます。 期限切れになる前に、いつでも書き換えることができます。 証明書を書き換えるとき、実行待ちまたは実行中の Runbook が悪影響を受けないようにすると共に、その実行アカウントでの認証に支障をきたさないよう、現行の有効な証明書は保持されます。 証明書は、有効期限日を迎えるまで存在し続けます。

> [!NOTE]
> エンタープライズ証明機関によって発行された証明書を使用するように Automation 実行アカウントを構成している場合、このオプションを使用すると、そのエンタープライズ証明書は自己署名証明書に置き換えられます。

証明書を書き換えるには、次の手順に従います。

1. Azure Portal で Automation アカウントを開きます。

2. **[Automation アカウント]** ブレードで、**[アカウントのプロパティ]** ウィンドウの **[アカウントの設定]** で **[実行アカウント]** を選択します。

    ![Automation アカウントのプロパティ ウィンドウ](media/automation-sec-configure-azure-runas-account/automation-account-properties-pane.png)
3. **実行アカウント**のプロパティ ブレードで、証明書を書き換える実行アカウントまたはクラシック実行アカウントを選択します。

4. 選択したアカウントの **[プロパティ]** ブレードで、**[証明書の書き換え]** をクリックします。

    ![実行アカウントの証明書を書き換える](media/automation-sec-configure-azure-runas-account/automation-account-renew-runas-certificate.png)

5. 証明書が書き換えられている間、メニューの **[通知]** で進行状況を追跡できます。

### <a name="delete-a-run-as-or-classic-run-as-account"></a>実行アカウントまたはクラシック実行アカウントの削除
このセクションでは、実行アカウントまたはクラシック実行アカウントを削除して再作成する方法について説明します。 この操作を実行するとき、Automation アカウントが保持されます。 削除した実行アカウントまたはクラシック実行アカウントは、Azure Portal で再作成できます。

1. Azure Portal で Automation アカウントを開きます。

2. **[Automation アカウント]** ブレードの [アカウントのプロパティ] ウィンドウで **[実行アカウント]** を選択します。

3. **実行アカウント**のプロパティ ブレードで、削除する実行アカウントまたはクラシック実行アカウントを選択します。 次に、選択したアカウントの **[プロパティ]** ブレードで **[削除]** をクリックします。

 ![Azure 実行アカウントを削除する](media/automation-sec-configure-azure-runas-account/automation-account-delete-runas.png)

4. アカウントが削除されている間、メニューの **[通知]** で進行状況を追跡できます。

5. 削除したアカウントは、**実行アカウント**のプロパティ ブレードで再作成できます。このとき、作成オプションとして **[Azure 実行アカウント]** を選択します。

 ![Automation 実行アカウントの再作成](media/automation-sec-configure-azure-runas-account/automation-account-create-runas.png)

### <a name="misconfiguration"></a>誤った構成
実行アカウントまたはクラシック実行アカウントが正しく機能するうえで必要な構成項目が削除されたり、初期セットアップ中に適切に作成されなかったりすることがあります。 たとえば、次のような項目です。

* 証明書資産
* 接続資産
* 共同作業者ロールからの実行アカウントの削除
* Azure AD のサービス プリンシパルまたはアプリケーション

このような誤った構成については、Automation アカウントによって変更が検出され、対応する**実行アカウント**のプロパティ ブレードに *[不完全]* 状態として表示されます。

![実行アカウントの構成が不完全な状態](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config.png)

この実行アカウントを選択すると、アカウントの **[プロパティ]** ウィンドウに次のエラー メッセージが表示されます。

![実行構成が不完全であることを示す警告メッセージ](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config-msg.png)をクリックします。

こうした実行アカウントの問題は、アカウントを削除してから再作成すればすぐに解決できます。

## <a name="update-your-automation-account-by-using-powershell"></a>PowerShell を使用して Automation アカウントを更新する
次の条件に該当する場合は、PowerShell を使用して既存の Automation アカウントを更新できます。

* Automation アカウントは作成済みであるものの、実行アカウントの作成を拒否した場合。
* Resource Manager リソースを管理するための Automation アカウントを既に所有しており、そのアカウントを更新する形で Runbook 認証用の実行アカウントを追加する場合。
* クラシック リソースを管理するための Automation アカウントを既に所有しており、それを、新しいアカウントを作成する代わりにクラシック実行アカウントを使用するように更新し、Runbook と資産をそのアカウントに移行する場合。   
* エンタープライズ証明機関 (CA) によって発行された証明書を使用して実行アカウントとクラシック実行アカウントを作成する場合。

このスクリプトには、以下の前提条件があります。

* このスクリプトは、Azure Resource Manager モジュール 2.01 以降がインストールされた Windows 10 と Windows Server 2016 でのみ実行できます。 前のバージョンの Windows ではサポートされません。
* Azure PowerShell 1.0 以降。 PowerShell 1.0 リリースについては、[Azure PowerShell のインストールと構成方法](/powershell/azureps-cmdlets-docs)に関するページを参照してください。
* Automation アカウント。このアカウントは、後述の PowerShell スクリプトの *–AutomationAccountName* パラメーターと *-ApplicationDisplayName* パラメーターの値として参照されます。

スクリプトの必須パラメーターである *SubscriptionID*、*ResourceGroup*、*AutomationAccountName* の値を取得するには、次の手順に従います。
1. Azure Portal で、**[Automation アカウント]** ブレードから Automation アカウントを選択し、**[すべての設定]** を選択します。 
2. **[すべての設定]** ブレードで、**[アカウント設定]** の **[プロパティ]** を選択します。 
3. **[プロパティ]** ブレードに表示される値をメモします。

![Automation アカウントの "プロパティ" ブレード](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### <a name="create-a-run-as-account-powershell-script"></a>実行アカウント用の PowerShell スクリプトを作成する
この PowerShell スクリプトでは、以下の構成がサポートされます。

* 自己署名証明書を使用して実行アカウントを作成する。
* 自己署名証明書を使用して実行アカウントとクラシック実行アカウントを作成する。
* エンタープライズ証明書を使用して実行アカウントとクラシック実行アカウントを作成する。
* Azure Government クラウドで自己署名証明書を使用して実行アカウントとクラシック実行アカウントを作成する。

このスクリプトは、選択された構成オプションに応じて以下の処理を実行します。

**実行アカウントの場合:**

* 自己署名証明書またはエンタープライズ証明書の公開キーでエクスポートされる Azure AD アプリケーションを作成し、このアプリケーションの Azure AD におけるサービス プリンシパル アカウントを作成します。また、現在のサブスクリプションにおける、このアカウントの共同作成者ロールが割り当てられます。 この設定は所有者ロールや他の任意のロールに変更できます。 詳細については、「[Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」を参照してください。
* 指定された Automation アカウントに *AzureRunAsCertificate* という名前の Automation 証明書資産を作成します。 この証明書資産には、Azure AD アプリケーションによって使用される証明書の秘密キーが格納されます。
* 指定された Automation アカウントに *AzureRunAsConnection* という名前の Automation 接続資産を作成します。 この接続資産には、アプリケーション ID、テナント ID、サブスクリプション ID、証明書の拇印が格納されます。

**クラシック実行アカウントの場合:**

* 指定された Automation アカウントに *AzureClassicRunAsCertificate* という名前の Automation 証明書資産を作成します。 この証明書資産には、管理証明書によって使用される証明書の秘密キーが格納されます。
* 指定された Automation アカウントに *AzureClassicRunAsConnection* という名前の Automation 接続資産を作成します。 この接続資産には、サブスクリプション名、サブスクリプション ID、証明書の資産名が格納されます。

>[!NOTE]
> クラシック実行アカウントを作成するオプションを選択した場合、スクリプトの実行後に、Automation アカウントが作成されたサブスクリプションの管理ストアに公開証明書 (.cer ファイル名拡張子) をアップロードしてください。
> 

スクリプトを実行して証明書をアップロードするには、次の手順に従います。

1. ご使用のコンピューターに次のスクリプトを保存します。 この例では、*New-RunAsAccount.ps1* というファイル名で保存します。

        #Requires -RunAsAdministrator
         Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory=$true)]
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory=$true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$EnvironmentName="AzureCloud",

        [Parameter(Mandatory=$false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
        )

        function CreateSelfSignedCertificate([string] $keyVaultName, [string] $certificateName, [string] $selfSignedCertPlainPassword,
                                      [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
           -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
           -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired)

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
        }

        function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $CurrentDate = Get-Date
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $KeyId = (New-Guid).Guid

        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= [DateTime]$PfxCert.GetExpirationDateString()
        $KeyCredential.EndDate = $KeyCredential.EndDate.AddDays(-1)
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.CertValue  = $keyValue

        # Use key credentials and create an Azure AD application
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $KeyCredential
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           Sleep -s 10
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        }
           return $Application.ApplicationId.ToString();
        }

        function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName,[string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
        }

        function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
        }

        Import-Module AzureRM.Profile
        Import-Module AzureRM.Resources

        $AzureRMProfileVersion= (Get-Module AzureRM.Profile).Version
        if (!(($AzureRMProfileVersion.Major -ge 2 -and $AzureRMProfileVersion.Minor -ge 1) -or ($AzureRMProfileVersion.Major -gt 2)))
        {
           Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
           return
        }

        Login-AzureRmAccount -EnvironmentName $EnvironmentName
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

        # Create a Run As account by using a service principal
        $CertifcateAssetName = "AzureRunAsCertificate"
        $ConnectionAssetName = "AzureRunAsConnection"
        $ConnectionTypeName = "AzureServicePrincipal"

        if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
        } else {
          $CertificateName = $AutomationAccountName+$CertifcateAssetName
          $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
          $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
          $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
          CreateSelfSignedCertificate $KeyVaultName $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create a service principal
        $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
        $ApplicationId=CreateServicePrincipal $PfxCert $ApplicationDisplayName

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

        # Populate the ConnectionFieldValues
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1
        $Thumbprint = $PfxCert.Thumbprint
        $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

        if ($CreateClassicRunAsAccount) {
            # Create a Run As account by using a service principal
            $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
            $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
            $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
            $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
                    "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates." + [Environment]::NewLine +
                    "Then click Upload and upload the .cer format of #CERT#"

             if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
             $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
             $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
             $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        } else {
             $ClassicRunAsAccountCertificateName = $AutomationAccountName+$ClassicRunAsAccountCertifcateAssetName
             $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
             $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
             $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
             $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
             CreateSelfSignedCertificate $KeyVaultName $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.SubscriptionName
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red $UploadMessage
        }

2. コンピューターの **[スタート]** をクリックし、昇格されたユーザー権限で **Windows PowerShell** を起動します。

3. 昇格された PowerShell コマンドライン シェルから、手順 1. で作成したスクリプトがあるフォルダーに移動します。

4. 必要な構成に応じたパラメーター値を使ってスクリプトを実行します。

    **自己署名証明書を使用して実行アカウントを作成する**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false`

    **自己署名証明書を使用して実行アカウントとクラシック実行アカウントを作成する**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **エンタープライズ証明書を使用して実行アカウントとクラシック実行アカウントを作成する**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Azure Government クラウドで自己署名証明書を使用して実行アカウントとクラシック実行アカウントを作成する**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`

    > [!NOTE]
    > スクリプトの実行後、Azure に対する認証が求められます。 サブスクリプション管理ロールのメンバーかつサブスクリプションの共同管理者であるアカウントを使用してサインインしてください。
    >
    >

スクリプトが正常に実行されたら、次のことを確認してください。
* 自己署名公開証明書 (.cer ファイル) を使ってクラシック実行アカウントを作成した場合、コンピューターの一時ファイル フォルダーに証明書が作成されて保存されます。PowerShell セッションの実行に使用したユーザー プロファイル下の *%USERPROFILE%\AppData\Local\Temp* を探してください。
* エンタープライズ公開証明書 (.cer ファイル) を使ってクラシック実行アカウントを作成した場合は、その証明書を使用します。 [Management API 証明書を Azure クラシック ポータルにアップロード](../azure-api-management-certs.md)する手順を実行した後、「[サービス管理リソースで認証を行うサンプル コード](#sample-code-to-authenticate-with-service-management-resources)」を参照し、サービス管理リソースに対する資格情報の構成を確認します。 
* クラシック実行アカウントを "*作成しなかった場合*" は、「[サービス管理リソースで認証を行うサンプル コード](#sample-code-to-authenticate-with-resource-manager-resources)」を参照して、Resource Manager リソースに対する認証を行い、資格情報の構成を確認します。

## <a name="sample-code-to-authenticate-with-resource-manager-resources"></a>Resource Manager リソースで認証を行うサンプル コード
*AzureAutomationTutorialScript* のサンプル Runbook から次の更新済みサンプル コードを取得して使用することで、実行アカウントを使用して認証を行い、Runbook で Resource Manager リソースを管理することができます。

    $connectionName = "AzureRunAsConnection"
    $SubId = Get-AutomationVariable -Name 'SubscriptionId'
    try
    {
       # Get the connection "AzureRunAsConnection "
       $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

       "Signing in to Azure..."
       Add-AzureRmAccount `
         -ServicePrincipal `
         -TenantId $servicePrincipalConnection.TenantId `
         -ApplicationId $servicePrincipalConnection.ApplicationId `
         -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
       "Setting context to a specific subscription"     
       Set-AzureRmContext -SubscriptionId $SubId              
    }
    catch {
        if (!$servicePrincipalConnection)
        {
           $ErrorMessage = "Connection $connectionName not found."
           throw $ErrorMessage
         } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
         }
    }

複数のサブスクリプション間での作業がしやすいよう、スクリプトには、サブスクリプション コンテキストの参照をサポートする 2 つのコード行が追加されています。 *SubscriptionId* という名前の変数資産には、サブスクリプションの ID が格納されています。 `Add-AzureRmAccount` コマンドレット ステートメントの後に、[`Set-AzureRmContext`](https://msdn.microsoft.com/library/mt619263.aspx) コマンドレットと *-SubscriptionId* パラメーター セットが記述されています。 変数名が一般的すぎる場合は、識別しやすい名前になるよう、プレフィックスやその他の名前付け規則を含むように変数名を変更してください。 また、*-SubscriptionId* の代わりに、*-SubscriptionName* パラメーター セットを対応する変数資産と共に使用することもできます。

Runbook での認証に使用されるコマンドレット (`Add-AzureRmAccount`) は、*ServicePrincipalCertificate* パラメーター セットを使用します。 認証に使用するのはサービス プリンシパル証明書であり、ユーザーの資格情報ではありません。

## <a name="sample-code-to-authenticate-with-service-management-resources"></a>サービス管理リソースで認証を行うサンプル コード
サンプルの *AzureClassicAutomationTutorialScript* Runbook から次の更新済みサンプル コードを取得して使用することで、クラシック実行アカウントを使用して認証を行い、Runbook でクラシック リソースを管理することができます。

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID

## <a name="next-steps"></a>次のステップ
* [Azure AD のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/active-directory-application-objects.md)
* [Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)
* [Azure Cloud Services の証明書の概要](../cloud-services/cloud-services-certs-create.md)

