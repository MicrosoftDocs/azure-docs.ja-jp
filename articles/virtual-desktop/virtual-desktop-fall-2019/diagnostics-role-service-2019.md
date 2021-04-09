---
title: Windows Virtual Desktop (クラシック) の診断の問題 - Azure
description: Windows Virtual Desktop (クラシック) の診断機能を使用して問題を診断する方法。
author: Heidilohr
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7e652f04b42b132e7c1307503b1764dda7b2036b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88009343"
---
# <a name="identify-and-diagnose-issues-in-windows-virtual-desktop-classic"></a>Windows Virtual Desktop (クラシック) での問題の特定と診断

>[!IMPORTANT]
>この内容は、Azure Resource Manager Windows Virtual Desktop オブジェクトをサポートしていない Windows Virtual Desktop (クラシック) に適用されます。 Azure Resource Manager Windows Virtual Desktop オブジェクトを管理しようとしている場合は、[こちらの記事](../diagnostics-role-service.md)を参照してください。

Windows Virtual Desktop では、管理者が単一のインターフェイスを使用して問題を特定できる診断機能が提供されます。 Windows Virtual Desktop ロールでは、ユーザーがシステムとやり取りするたびに診断アクティビティがログに記録されます。 各ログには、トランザクションに関連する Windows Virtual Desktop ロール、エラー メッセージ、テナント情報、ユーザー情報などの関連情報が含まれています。 診断アクティビティは、エンドユーザーのアクションと管理者のアクションの両方によって作成され、3 つの主要バケットに分類できます。

* フィード サブスクリプション アクティビティ: エンドユーザーが Microsoft リモート デスクトップ アプリケーションを通じてフィードに接続しようとするたびに、これらのアクティビティがトリガーされます。
* 接続アクティビティ: エンドユーザーが Microsoft リモート デスクトップ アプリケーションを通じてデスクトップまたは RemoteApp に接続しようとするたびに、これらのアクティビティがトリガーされます。
* 管理アクティビティ: 管理者がホスト プールの作成、アプリ グループへのユーザーの割り当て、ロール割り当ての作成などの管理操作をシステムに対して実行するたびに、これらのアクティビティがトリガーされます。

診断ロール サービス自体が Windows Virtual Desktop の一部であるため、Windows Virtual Desktop に到達しない接続は診断結果に表示されません。 Windows Virtual Desktop 接続の問題は、エンドユーザーにネットワーク接続の問題が発生しているときに発生する可能性があります。

まず、PowerShell セッション内で使用する [Windows Virtual Desktop PowerShell モジュールをダウンロードしてインポート](/powershell/windows-virtual-desktop/overview/)します (まだ行っていない場合)。 その後、次のコマンドレットを実行して、ご自分のアカウントにサインインします。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>PowerShell を使用した問題の診断

Windows Virtual Desktop 診断では、1 つの PowerShell コマンドレットだけが使用されますが、問題を絞り込んで特定するために多くの省略可能なパラメーターが含まれています。 以下のセクションでは、問題を診断するために実行できるコマンドレットの一覧を示します。 ほとんどのフィルターは一緒に適用できます。 `<tenantName>` などの角かっこで囲まれた値は、自分の状況に適用される値で置き換える必要があります。

>[!IMPORTANT]
>診断機能は、シングルユーザーのトラブルシューティング用です。 PowerShell を使用するすべてのクエリには、 *-UserName* または *-ActivityID* パラメーターのいずれかが含まれている必要があります。 監視機能については、Log Analytics を使用します。 診断データをワークスペースに送信する方法の詳細については、「[診断機能に Log Analytics を使用する](diagnostics-log-analytics-2019.md)」を参照してください。

### <a name="filter-diagnostic-activities-by-user"></a>ユーザーによって診断アクティビティをフィルター処理する

**-UserName** パラメーターでは、次のコマンドレット例に示すように、指定したユーザーによって開始された診断アクティビティの一覧が返されます。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

**-UserName** パラメーターは、他の省略可能なフィルター パラメーターと組み合わせることもできます。

### <a name="filter-diagnostic-activities-by-time"></a>時間によって診断アクティビティをフィルター処理する

**-StartTime** パラメーターと **-EndTime** パラメーターを使用して、返される診断アクティビティの一覧をフィルター処理することができます。 **-StartTime** パラメーターでは、次の例に示すように、特定の日付から開始する診断アクティビティの一覧が返されます。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

**-StartTime** パラメーターを指定したコマンドレットに **-EndTime** パラメーターを追加して、結果を受け取る特定の期間を指定できます。 次のコマンドレット例では、8 月 1 日から 8 月 10 日までの診断アクティビティの一覧が返されます。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

**-StartTime** パラメーターと **-EndTime** パラメーターは、他の省略可能なフィルター パラメーターと組み合わせることもできます。

### <a name="filter-diagnostic-activities-by-activity-type"></a>アクティビティの種類によって診断アクティビティをフィルター処理する

**-ActivityType** パラメーターを使用して、診断アクティビティをアクティビティの種類によってフィルター処理することもできます。 次のコマンドレットでは、エンドユーザー接続の一覧が返されます。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

次のコマンドレットでは、管理者の管理タスクの一覧が返されます。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

**Get-RdsDiagnosticActivities** コマンドレットでは、フィードを ActivityType として指定することは現在サポートされていません。

### <a name="filter-diagnostic-activities-by-outcome"></a>結果によって診断アクティビティをフィルター処理する

**-Outcome** パラメーターを使用して、返される診断アクティビティの一覧を結果でフィルター処理することができます。 次のコマンドレット例では、成功した診断アクティビティの一覧が返されます。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

次のコマンドレット例では、失敗した診断アクティビティの一覧が返されます。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

**-Outcome** パラメーターは、他の省略可能なフィルター パラメーターと組み合わせることもできます。

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>アクティビティ ID によって特定の診断アクティビティを取得する

**-ActivityId** パラメーターでは、次のコマンドレット例に示すように、特定の診断アクティビティ (存在する場合) が返されます。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>アクティビティ ID ごとに失敗したアクティビティのエラー メッセージを表示する

失敗したアクティビティのエラー メッセージを表示するには、 **-Detailed** パラメーターを指定してコマンドレットを実行する必要があります。 **Select-Object** コマンドレットを実行することで、エラーの一覧を表示できます。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>詳細な診断アクティビティを取得する

**-Detailed** パラメーターは、返される各診断アクティビティの詳細を提供します。 各アクティビティの形式は、そのアクティビティの種類によって異なります。 **-Detailed** パラメーターは、次の例に示すように、任意の **Get-RdsDiagnosticActivities** クエリに追加できます。

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>一般的なエラー シナリオ

エラー シナリオは、サービスの内部と Windows Virtual Desktop の外部に分類されます。

* 内部の問題: テナント管理者が緩和できず、サポートの問題として解決する必要があるシナリオを指定します。 [Windows Virtual Desktop Tech コミュニティ](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)を通してフィードバックを提供する場合は、アクティビティ ID と問題が発生したときのおおよその時間枠を含めてください。
* 外部の問題: システム管理者が緩和できるシナリオに関連します。 これらは、Windows Virtual Desktop の外部で発生します。

次の表では、管理者が経験する可能性のある一般的なエラーの一覧を示します。

>[!NOTE]
>この一覧には、最も一般的なエラーが含まれており、定期的に更新されます。 最新の情報を得るために、必ず 1 か月に 1 回以上この記事をご確認ください。

### <a name="external-management-error-codes"></a>外部管理エラー コード

|数値コード|エラー コード|推奨されている解決方法|
|---|---|---|
|1322|ConnectionFailedNoMappingOfSIDinAD|ユーザーは Azure Active Directory のメンバーではありません。 [Active Directory 管理センター](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center)の手順に従って追加してください。|
|3|UnauthorizedAccess|管理用の PowerShell コマンドレットを実行しようとしたユーザーにそれを行うためのアクセス許可がないか、ユーザー名に入力ミスがありました。|
|1000|TenantNotFound|入力したテナント名が既存のどのテナントとも一致しません。 テナント名に入力ミスがないことを確認し、もう一度やり直してください。|
|1006|TenantCannotBeRemovedHasSessionHostPools|オブジェクトが含まれているテナントは削除できません。 最初にセッション ホスト プールを削除してから、もう一度やり直してください。|
|2000|HostPoolNotFound|入力したホスト プール名が既存のどのホスト プールとも一致しません。 ホスト プール名に入力ミスがないことを確認し、もう一度やり直してください。|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|オブジェクトが含まれているホスト プールは削除できません。 最初にホスト プール内のすべてのアプリ グループを削除してください。|
|2004|HostPoolCannotBeRemovedHasSessionHosts|セッション ホスト プールを削除する前に、まずすべてのセッション ホストを削除してください。|
|5001|SessionHostNotFound|クエリを実行したセッション ホストがオフラインになっている可能性があります。 ホスト プールの状態を確認してください。|
|5008|SessionHostUserSessionsExist |目的の管理アクティビティを実行する前に、セッション ホスト上のすべてのユーザーをサインアウトさせる必要があります。|
|6000|AppGroupNotFound|入力したアプリ グループ名が既存のどのアプリ グループとも一致しません。 アプリ グループ名に入力ミスがないことを確認し、もう一度やり直してください。|
|6022|RemoteAppNotFound|入力した RemoteApp 名がどの RemoteApp とも一致しません。 RemoteApp 名に入力ミスがないことを確認し、もう一度やり直してください。|
|6010|PublishedItemsExist|公開しようとしているリソースの名前が既に存在するリソースと同じです。 リソース名を変更し、もう一度やり直してください。|
|7002|NameNotValidWhiteSpace|名前に空白文字を使用しないでください。|
|8000|InvalidAuthorizationRoleScope|入力したロール名が既存のどのロール名とも一致しません。 ロール名に入力ミスがないことを確認し、もう一度やり直してください。 |
|8001|UserNotFound |入力したユーザー名が既存のどのユーザー名とも一致しません。 名前に入力ミスがないことを確認し、もう一度やり直してください。|
|8005|UserNotFoundInAAD |入力したユーザー名が既存のどのユーザー名とも一致しません。 名前に入力ミスがないことを確認し、もう一度やり直してください。|
|8008|TenantConsentRequired|[こちら](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop)の指示に従って、テナントに同意を付与してください。|

### <a name="external-connection-error-codes"></a>外部接続のエラー コード

|数値コード|エラー コード|推奨されている解決方法|
|---|---|---|
|-2147467259|ConnectionFailedAdErrorNoSuchMember|ユーザーは Active Directory のメンバーではありません。 [Active Directory 管理センター](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center)の手順に従って追加してください。|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|セッション ホストは、Active Directory に正しく参加していません。|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|セッション ホストが使用できないため、接続が失敗しました。 セッション ホストの正常性を確認してください。|
|-2146233088|ConnectionFailedClientDisconnect|このエラーが頻繁に発生する場合は、ユーザーのコンピューターがネットワークに接続されていることを確認してください。|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|ホスト ユーザーが接続しようとしたセッションが正常ではありません。 仮想マシンをデバッグしてください。|
|-2146233088|ConnectionFailedUserNotAuthorized|ユーザーは、公開されたアプリまたはデスクトップにアクセスするためのアクセス許可を持っていません。 このエラーは、公開されたリソースを管理者が削除した後に発生する可能性があります。 リモート デスクトップ アプリケーション内のフィードを更新するようユーザーに依頼してください。|
|2|FileNotFound|ユーザーがアクセスしようとしたアプリケーションが正しくインストールされていないか、正しくないパスに設定されています。|
|3|InvalidCredentials|ユーザーが入力したユーザー名またはパスワードが、既存のどのユーザー名またはパスワードとも一致しません。 資格情報に入力ミスがないことを確認し、もう一度やり直してください。|
|8|ConnectionBroken|クライアントとゲートウェイまたはサーバーとの間の接続が削除されました。 予期せず発生した場合を除き、アクションは不要です。|
|14|UnexpectedNetworkDisconnect|ネットワークへの接続が削除されました。 ユーザーにもう一度接続するよう依頼してください。|
|24|ReverseConnectFailed|ホスト仮想マシンには、RD ゲートウェイへの直接の見通し線がありません。 ゲートウェイ IP アドレスを解決できることを確認してください。|
|1322|ConnectionFailedNoMappingOfSIDinAD|ユーザーは Active Directory のメンバーではありません。 [Active Directory 管理センター](/windows-server/identity/ad-ds/get-started/adac/active-directory-administrative-center)の手順に従って追加してください。|

## <a name="next-steps"></a>次のステップ

Windows Virtual Desktop 内のロールについて詳しくは、「[Windows Virtual Desktop 環境](environment-setup-2019.md)」をご覧ください。

Windows Virtual Desktop に使用可能な PowerShell コマンドレットの一覧を表示するには、[PowerShell リファレンス](/powershell/windows-virtual-desktop/overview)に関する記事をご覧ください。
