---
title: パスワード保護の監視とログ記録 - Azure Active Directory
description: Azure AD パスワード保護の監視とログ記録について
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: c74ea99d3a0e39729bb4d89f012d7b790bf0568b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847713"
---
# <a name="azure-ad-password-protection-monitoring-and-logging"></a>Azure AD パスワード保護の監視とログ記録

Azure AD パスワード保護のデプロイ後、監視とレポートは重要なタスクです。 この記事では、各サービスが情報をログに記録する場所や、Azure AD パスワード保護の使用について報告する方法など、さまざまな監視手法を理解できるように詳しく説明します。

監視およびレポート作成は、イベント ログ メッセージまたは PowerShell コマンドレットの実行によって行われます。 DC エージェントとプロキシ サービスは両方とも、イベント ログ メッセージを記録します。 以下で説明するすべての PowerShell コマンドレットは、プロキシ サーバーでのみ使用できます (AzureADPasswordProtection PowerShell モジュールを参照)。 DC エージェント ソフトウェアでは、PowerShell モジュールはインストールされません。

## <a name="dc-agent-event-logging"></a>DC エージェント イベントのログ記録

各ドメイン コントローラーでは、DC エージェント サービス ソフトウェアによって、各個人のパスワード検証操作の結果 (およびその他の状態) がローカルのイベント ログに書き込まれます。

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

DC エージェント管理ログは、ソフトウェアの動作に関する情報の主要なソースです。

トレース ログは既定では無効になっていることに注意してください。

さまざまな DC エージェント コンポーネントによってログに記録されるイベントは、以下の範囲に当てはまります。

|コンポーネント |イベント ID の範囲|
| --- | --- |
|DC エージェントのパスワード フィルター DLL| 10000 から 19999|
|DC エージェント サービスのホスティング プロセス| 20000 から 29999|
|DC エージェント サービス ポリシー検証ロジック| 30000 から 39999|

## <a name="dc-agent-admin-event-log"></a>DC エージェント管理イベント ログ

### <a name="password-validation-outcome-events"></a>パスワード検証結果イベント

各ドメイン コントローラーでは、DC エージェント サービス ソフトウェアによって、各個人のパスワード検証の結果が DC エージェント管理イベント ログに書き込まれます。

パスワード検証操作が適切に実行されるために、通常、DC エージェントのパスワード フィルター dll から 1 つのイベントがログに記録されます。 パスワード検証操作が失敗した場合、通常、DC エージェント サービスのイベントと DC エージェント パスワード フィルター DLL のイベントの 2 つがログに記録されます。

これらの状況をキャプチャする各イベントは、次の要因に基づいてログに記録されます。

* 特定のパスワードが設定されているか、変更されているか。
* 特定のパスワードの検証に合格したか、失敗したか。
* Microsoft グローバル ポリシーと組織ポリシーのどちらか一方または両方の組み合わせのために検証が失敗したか。
* 現在のパスワード ポリシーで、監査のみモードが現在オンかオフか。

主なパスワード検証関連イベントは次のとおりです。

|   |パスワードの変更 |パスワードの設定|
| --- | :---: | :---: |
|合格 |10014 |10015|
|失敗 (顧客のパスワード ポリシーのため)| 10016、30002| 10017、30003|
|失敗 (Microsoft のパスワード ポリシーのため)| 10016、30004| 10017、30005|
|失敗 (Microsoft と顧客のパスワード ポリシーの組み合わせのため)| 10016、30026| 10017、30027|
|監査のみの合格 (顧客のパスワード ポリシーに失敗)| 10024、30008| 10025、30007|
|監査のみの合格 (Microsoft のパスワード ポリシーに失敗)| 10024、30010| 10025、30009|
|監査のみの合格 (Microsoft と顧客のパスワード ポリシーの組み合わせに失敗)| 10024、30028| 10025、30029|

上の表のケースで "ポリシーの組み合わせ" となっているのは、Microsoft 禁止パスワード リストと顧客禁止パスワード リストの両方から少なくとも 1 つのトークンが、ユーザーのパスワードに含まれることが検出された状況を指します。

イベントのペアが一緒に記録されるときは、同じ CorrelationId によって両方のイベントが明示的に関連付けられます。

### <a name="password-validation-summary-reporting-via-powershell"></a>PowerShell によるパスワード検証概要レポート

`Get-AzureADPasswordProtectionSummaryReport` コマンドレットを使用して、パスワード検証アクティビティの概要ビューを生成できます。 このコマンドレットの出力例は次のとおりです。

```powershell
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

コマンドレット レポートのスコープは、–Forest、-Domain、–DomainController のいずれかのパラメーターの使用の影響を受けることがあります。 パラメーターを指定しないと暗黙的に –Forest が指定されます。

`Get-AzureADPasswordProtectionSummaryReport` コマンドレットでは、DC エージェント管理イベント ログのクエリが実行され、表示されている各結果カテゴリに対応するイベントの合計数がカウントされます。 次の表では、各結果とそれに対応するイベント ID のマッピングを示します。

|Get-AzureADPasswordProtectionSummaryReport のプロパティ |対応するイベント ID|
| :---: | :---: |
|PasswordChangesValidated |10014|
|PasswordSetsValidated |10015|
|PasswordChangesRejected |10016|
|PasswordSetsRejected |10017|
|PasswordChangeAuditOnlyFailures |10024|
|PasswordSetAuditOnlyFailures |10025|
|PasswordChangeErrors |10012|
|PasswordSetErrors |10013|

`Get-AzureADPasswordProtectionSummaryReport` コマンドレットは、PowerShell スクリプトの形式で提供されており、必要な場合は、次の場所に直接参照できることに注意してください。

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> このコマンドレットは、各ドメイン コントローラーに対して PowerShell セッションを開くことで機能します。 成功させるには、各ドメイン コントローラーで PowerShell リモート セッションのサポートを有効にする必要があります。また、クライアントには十分な特権が必要です。 PowerShell のリモート セッション要件の詳細については、PowerShell ウィンドウで 'Get-Help about_Remote_Troubleshooting' を実行します。

> [!NOTE]
> このコマンドレットは、各 DC エージェント サービスの管理イベント ログに対するクエリをリモートで実行することで動作します。 イベント ログに多数のイベントが含まれている場合、コマンドレットの完了に時間がかかることがあります。 また、大規模なデータ セットの一括ネットワーク クエリがドメイン コントローラーのパフォーマンスに影響を与える可能性があります。 そのため、このコマンドレットは、運用環境では慎重に使用する必要があります。

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>イベント ID 10014 (パスワード変更成功) のサンプル イベント ログ メッセージ

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>イベント ID 10017 および 30003 (パスワード設定失敗) のサンプル イベント ログ メッセージ

10017:

```text
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003:

```text
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>イベント ID 30001 (使用可能なポリシーがないためパスワード受け入れ) のサンプル イベント ログ メッセージ

```text
The password for the specified user was accepted because an Azure password policy is not available yet

UserName: SomeUser
FullName: Some User

This condition may be caused by one or more of the following reasons:%n

1. The forest has not yet been registered with Azure.

   Resolution steps: an administrator must register the forest using the Register-AzureADPasswordProtectionForest cmdlet.

2. An Azure AD password protection Proxy is not yet available on at least one machine in the current forest.

   Resolution steps: an administrator must install and register a proxy using the Register-AzureADPasswordProtectionProxy cmdlet.

3. This DC does not have network connectivity to any Azure AD password protection Proxy instances.

   Resolution steps: ensure network connectivity exists to at least one Azure AD password protection Proxy instance.

4. This DC does not have connectivity to other domain controllers in the domain.

   Resolution steps: ensure network connectivity exists to the domain.
```

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>イベント ID 30006 (新規ポリシー適用中) のサンプル イベント ログ メッセージ

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>イベント ID 30019 (Azure AD パスワード保護が無効) のサンプル イベント ログ メッセージ

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>DC エージェント操作ログ

DC エージェント サービスでは、操作関連のイベントも次のログに記録されます。

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>DC エージェント トレース ログ

DC エージェント サービスでは、デバッグ レベルの詳細なトレース イベントも次のログに記録できます。

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

トレース ログは既定では無効です。

> [!WARNING]
> 有効にすると、トレース ログは大量のイベントを受け取り、ドメイン コントローラーのパフォーマンスに影響を与える可能性があります。 そのため、この拡張ログは、問題を深く調査する必要がある場合にのみ、ごく短時間、有効にすることをお勧めします。

## <a name="dc-agent-text-logging"></a>DC エージェント テキスト ログ

次のレジストリ値を設定することで、テキスト ログに書き込むように DC エージェント サービスを構成できます。

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

テキスト ログは既定では無効です。 この値の変更を有効にするには、DC エージェント サービスを再起動する必要があります。 有効にすると、DC エージェント サービスは次の場所にあるログ ファイルに書き込みます。

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> テキスト ログは、トレース ログに記録できるものと同じデバッグ レベルのエントリを受け取りますが、一般に、確認および分析するのが簡単な形式です。

> [!WARNING]
> 有効にすると、このログは大量のイベントを受け取り、ドメイン コントローラーのパフォーマンスに影響を与える可能性があります。 そのため、この拡張ログは、問題を深く調査する必要がある場合にのみ、ごく短時間、有効にすることをお勧めします。

## <a name="dc-agent-performance-monitoring"></a>DC エージェントのパフォーマンスの監視

DC エージェント サービス ソフトウェアによって、**Azure AD Password Protection** というパフォーマンス カウンター オブジェクトがインストールされます。 現在、次のパフォーマンス カウンターを使用できます。

|パフォーマンス カウンター名 | 説明|
| --- | --- |
|Passwords processed |このカウンターには、前回の再起動以降に処理された (承認または拒否された) パスワードの合計数が表示されます。|
|Passwords accepted |このカウンターには、前回の再起動以降に承認されたパスワードの合計数が表示されます。|
|Passwords rejected |このカウンターには、前回の再起動以降に拒否されたパスワードの合計数が表示されます。|
|Password filter requests in progress |このカウンターには、現在進行中のパスワード フィルター要求の数が表示されます。|
|Peak password filter requests |このカウンターには、前回の再起動以降の同時パスワード フィルター要求のピーク数が表示されます。|
|Password filter request errors |このカウンターには、前回の再起動以降にエラーのために失敗したパスワード フィルター要求の合計数が表示されます。 Azure AD パスワード保護 DC エージェント サービスが実行されていないと、エラーが発生する可能性があります。|
|Password filter requests/sec |このカウンターには、パスワードの処理速度が表示されます。|
|Password filter request processing time |このカウンターには、パスワード フィルター要求の処理に必要な平均時間が表示されます。|
|Peak password filter request processing time |このカウンターには、前回の再起動以降のパスワード フィルター要求のピーク処理時間が表示されます。|
|Passwords accepted due to audit mode |このカウンターには、通常は拒否されるところ、パスワード ポリシーが監査モードに構成されていたために承認されたパスワードの合計数 (前回の再起動以降) が表示されます。|

## <a name="dc-agent-discovery"></a>DC エージェントの検出

`Get-AzureADPasswordProtectionDCAgent` コマンドレットを使用すると、ドメインまたはフォレスト内で実行されているさまざまな DC エージェントに関する基本的な情報を表示できます。 この情報は、実行中の DC エージェント サービスによって登録された serviceConnectionPoint オブジェクトから取得されます。

このコマンドレットの出力例は次のとおりです。

```powershell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

さまざまなプロパティは、各 DC エージェント サービスによって約 1 時間ごとに更新されます。 さらに、データは Active Directory レプリケーションの待機時間の影響を受けます。

コマンドレットのクエリの範囲は、-Forest パラメーターまたは -Domain パラメーターの使用の影響を受ける可能性があります。

HeartbeatUTC の値が古い場合は、そのドメイン コントローラーの Azure AD パスワード保護 DC エージェントが実行されていない、アンインストールされている、またはマシンが降格されてドメイン コントローラーではなくなっていることを示している可能性があります。

PasswordPolicyDateUTC の値が古い場合は、そのマシンの Azure AD パスワード保護 DC エージェントが正しく動作していないことを示している可能性があります。

## <a name="dc-agent-newer-version-available"></a>DC エージェントの新しいバージョンが使用可能

DC エージェント サービスは、新しいバージョンの DC エージェント ソフトウェアがあることを検出すると、30034 警告イベントを操作ログに記録します。その例を次に示します。

```text
An update for Azure AD Password Protection DC Agent is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
```

前述のイベントには、新しいソフトウェアのバージョンが明記されていません。 その情報については、イベント メッセージに記載されたリンク先にアクセスする必要があります。

> [!NOTE]
> 前述のイベント メッセージでは自動アップグレード ("autoupgrade") について触れられていますが、現在 DC エージェント ソフトウェアでは、その機能はサポートされません。

## <a name="proxy-service-event-logging"></a>プロキシ サービス イベント ログ

プロキシ サービスでは、次のイベント ログに最小セットのイベントが生成されます。

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

トレース ログは既定では無効になっていることに注意してください。

> [!WARNING]
> 有効にすると、トレース ログは大量のイベントを受け取り、プロキシ ホストのパフォーマンスに影響を与える可能性があります。 そのため、このログは、問題を深く調査する必要がある場合にのみ、ごく短時間、有効にすることをお勧めします。

イベントは、以下の範囲を使用して、さまざまなプロキシ コンポーネントによってログに記録されます。

|コンポーネント |イベント ID の範囲|
| --- | --- |
|プロキシ サービスのホスティング プロセス| 10000 から 19999|
|プロキシ サービスのコア ビジネス ロジック| 20000 から 29999|
|PowerShell コマンドレット| 30000 から 39999|

## <a name="proxy-service-text-logging"></a>プロキシ サービス テキスト ログ

次のレジストリ値を設定することで、テキスト ログに書き込むようにプロキシ サービスを構成できます。

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters!EnableTextLogging = 1 (REG_DWORD 値)

テキスト ログは既定では無効です。 この値の変更を有効にするには、プロキシ サービスを再起動する必要があります。 有効にすると、プロキシ サービスは次の場所にあるログ ファイルに書き込みます。

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> テキスト ログは、トレース ログに記録できるものと同じデバッグ レベルのエントリを受け取りますが、一般に、確認および分析するのが簡単な形式です。

> [!WARNING]
> 有効にすると、このログは大量のイベントを受け取り、コンピューターのパフォーマンスに影響を与える可能性があります。 そのため、この拡張ログは、問題を深く調査する必要がある場合にのみ、ごく短時間、有効にすることをお勧めします。

## <a name="powershell-cmdlet-logging"></a>PowerShell コマンドレット ログ

状態が変換する PowerShell コマンドレット (たとえば、Register-AzureADPasswordProtectionProxy) では、通常、操作ログに結果イベントが記録されます。

さらに、ほとんどの Azure AD パスワード保護 PowerShell コマンドレットでは、次の場所にあるテキスト ログに書き込まれます。

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

コマンドレット エラーが発生し、原因や解決策がすぐにわからない場合は、テキスト ログも参考になる可能性があります。

## <a name="proxy-discovery"></a>プロキシの検出

`Get-AzureADPasswordProtectionProxy` コマンドレットを使用すると、ドメインまたはフォレスト内で実行されているさまざまな Azure AD パスワード保護プロキシ サービスに関する基本的な情報を表示できます。 この情報は、実行中のプロキシ サービスによって登録された serviceConnectionPoint オブジェクトから取得されます。

このコマンドレットの出力例は次のとおりです。

```powershell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

さまざまなプロパティは、各プロキシ サービスによって約 1 時間ごとに更新されます。 さらに、データは Active Directory レプリケーションの待機時間の影響を受けます。

コマンドレットのクエリの範囲は、-Forest パラメーターまたは -Domain パラメーターの使用の影響を受ける可能性があります。

HeartbeatUTC の値が古い場合は、そのコンピューターの Azure AD パスワード保護プロキシが実行されていないか、アンインストールされていることを示している可能性があります。

## <a name="proxy-agent-newer-version-available"></a>プロキシ エージェントの新しいバージョンが使用可能

プロキシ エージェント サービスは、新しいバージョンのプロキシ ソフトウェアがあることを検出すると、20002 警告イベントを操作ログに記録します。その例を次に示します。

```text
An update for Azure AD Password Protection Proxy is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
.
```

前述のイベントには、新しいソフトウェアのバージョンが明記されていません。 その情報については、イベント メッセージに記載されたリンク先にアクセスする必要があります。

自動アップグレードを有効にしてプロキシ エージェントを構成した場合でも、このイベントは生成されます。

## <a name="next-steps"></a>次の手順

[Azure AD パスワード保護のトラブルシューティング](howto-password-ban-bad-on-premises-troubleshoot.md)

グローバルおよびカスタムの禁止パスワード リストの詳細については、[不適切なパスワードの禁止](concept-password-ban-bad.md)に関する記事を参照してください。
