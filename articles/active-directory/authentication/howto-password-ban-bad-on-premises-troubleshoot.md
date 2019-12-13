---
title: パスワード保護のトラブルシューティング - Azure Active Directory
description: Azure AD パスワード保護の一般的なトラブルシューティングについて説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd609eb1f289c0a104bddaa08a60e7dc6202acee
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847662"
---
# <a name="azure-ad-password-protection-troubleshooting"></a>Azure AD パスワード保護のトラブルシューティング

Azure AD パスワード保護をデプロイした後、トラブルシューティングを必要とする場合があります。 この記事では、いくつかの一般的なトラブルシューティング手順を理解しやすいように詳しく説明しています。

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>DC エージェントがディレクトリ内でプロキシを見つけることができない

この問題の主な症状は、DC エージェント管理イベント ログ内の 30017 イベントに相当します。

この問題の一般的な原因は、プロキシがまだ登録されていないことです。 プロキシが登録されている場合は、特定の DC エージェントでそのプロキシを表示できるようになるまでの AD レプリケーションの待機時間が原因の遅延がある可能性があります。

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>DC エージェントがプロキシ サーバーと通信できない

この問題の主な症状は、DC エージェント管理イベント ログ内の 30018 イベントに相当します。 この問題には、次に示すいくつかの原因が考えられます。

1. DC エージェントが、登録されているプロキシへのネットワーク接続を許可しない、ネットワークの分離された部分にあります。 この問題は、Azure からパスワード ポリシーをダウンロードするために他の DC エージェントがプロキシと通信できる限りは、問題にならない可能性があります。 ダウンロードが完了すると、これらのポリシーは、sysvol 共有内のポリシー ファイルのレプリケーションを介して、分離された DC によって取得されます。

1. プロキシ ホスト コンピューターが RPC エンドポイント マッパー エンドポイント (ポート 135) へのアクセスをブロックしている

   Azure AD パスワード保護プロキシのインストーラーでは、ポート 135 へのアクセスを許可する Windows ファイアウォールの受信規則が自動的に作成されます。 このルールを後で削除したり無効にしたりすると、DC エージェントはプロキシ サービスと通信できなくなります。 別のファイアウォール製品ではなく組み込みの Windows ファイアウォールを無効にしている場合は、そのファイアウォールを、ポート 135 へのアクセスを許可するように構成する必要があります。

1. プロキシ ホスト コンピューターがプロキシ サービスによってリッスンされる RPC エンドポイント (動的または静的) へのアクセスをブロックしている

   Azure AD パスワード保護プロキシのインストーラーでは、Azure AD パスワード保護プロキシ サービスによってリッスンされる受信ポートへのアクセスを許可する Windows ファイアウォールの受信規則が、自動的に作成されます。 このルールを後で削除したり無効にしたりすると、DC エージェントはプロキシ サービスと通信できなくなります。 別のファイアウォール製品ではなく組み込みの Windows ファイアウォールを無効にしている場合は、そのファイアウォールを、Azure AD パスワード保護プロキシ サービスによってリッスンされる受信ポートへのアクセスを許可するように構成する必要があります。 この構成は、より具体的には、(`Set-AzureADPasswordProtectionProxyConfiguration` コマンドレットを使用して) プロキシ サービスが特定の静的な RPC ポートをリッスンするように構成されている場合に行われることがあります。

1. プロキシ ホスト コンピューターは、ドメイン コントローラーがコンピューターにログオンする機能を許可するようには構成されていません。 この動作は、"ネットワーク経由でコンピューターへアクセス" ユーザー特権の割り当てによって制御されます。 フォレスト内のすべてのドメインのすべてのドメイン コントローラーに、この特権を付与する必要があります。 多くの場合、この設定は、より大きなネットワーク強化作業の一部として制約されます。

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>プロキシ サービスが Azure と通信できない

1. プロキシ コンピューターが「[デプロイ要件](howto-password-ban-bad-on-premises-deploy.md)」に示されているエンドポイントに接続できることを確認してください。

1. フォレストとすべてのプロキシ サーバーが同じ Azure テナントに対して登録されていることを確認します。

   この要件は、PowerShell コマンドレット `Get-AzureADPasswordProtectionProxy` と `Get-AzureADPasswordProtectionDCAgent` を実行し、返された各項目の `AzureTenant` プロパティを比較することで確認できます。 正しく動作するためには、報告されたテナント名がすべての DC エージェントとプロキシ サーバーにおいて同じである必要があります。

   Azure テナントの登録に矛盾した状態が存在する場合、`Register-AzureADPasswordProtectionProxy` と `Register-AzureADPasswordProtectionForest` のいずれかまたは両方の PowerShell コマンドレットを必要に応じて実行し、すべての登録について確実に同じ Azure テナントの資格情報を使用することで、この問題を解決できます。

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>DC エージェントがパスワード ポリシー ファイルを暗号化または暗号化解除できない

Azure AD パスワード保護には、Microsoft キー配布サービスによって提供される暗号化と復号化の機能に対する重要な依存関係があります。 暗号化または暗号化解除の失敗は、さまざまな症状で示され、複数の原因が存在する可能性があります。

1. KDS サービスが有効になっていて、ドメイン内の Windows Server 2012 以降のすべてのドメイン コントローラーで機能することを確認します。

   既定では、KDS サービスのサービス開始モードは、手動 (トリガーで開始) として構成されます。 この構成は、クライアントが初めてサービスを使用しようとすると、サービスオンデマンドで開始されることを意味します。 この既定のサービス開始モードは、Azure AD パスワード保護で使用してもかまいません。

   KDS サービス開始モードが [無効] に構成されている場合、Azure AD パスワード保護を正常に機能させるためには、この構成を修正する必要があります。

   この問題の簡単なテストは、サービス管理 MMC コンソールを使用するか、他の管理ツールを使用して (たとえば、コマンド プロンプト コンソールから "net start kdssvc" を実行して)、KDS サービスを手動で開始することです。 KDS サービスは、正常に開始して実行を維持することが期待されます。

   KDS サービスを開始できない最も一般的な根本原因は、Active Directory ドメイン コントローラーのオブジェクトが既定のドメイン コントローラー OU の外部にあることです。 この構成は KDS サービスではサポートされておらず、Azure AD パスワード保護によって課せられた制限ではありません。 この状態の修正は、ドメイン コントローラーのオブジェクトを既定のドメイン コントローラー OU の下の場所に移動することです。

1. Windows Server 2012 R2 から Windows Server 2016 での、互換性のない KDS 暗号化バッファー形式の変更

   Windows Server 2016 で導入された KDS セキュリティ修正プログラムでは、KDS 暗号化バッファーの形式が変更されており、これらのバッファーは、Windows Server 2012 および Windows Server 2012 R2 での暗号化解除に失敗することがあります。 逆方向は問題ありません。Windows Server 2012 および Windows Server 2012 R2 で KDS 暗号化されたバッファーは常に、Windows Server 2016 以降で正常に暗号化解除されます。 Active Directory ドメイン内のドメイン コントローラーでこれらのオペレーティング システムが混在して実行されている場合は、Azure AD パスワード保護の暗号化解除エラーがときどき報告されることがあります。 セキュリティ修正プログラムの性質上、また特定の時点でどの Azure AD パスワード保護 DC エージェント上のドメイン コントローラーによってデータが暗号化されるかわからないため、これらの障害のタイミングや症状を正確に予測することはできません。

   Microsoft はこの問題の修正を調査していますが、まだ ETA は利用できません。 それまでの間は、このような互換性のないオペレーティング システムを Active Directory ドメインに混在させないようにする以外に、この問題を回避することはできません。 つまり、Windows Server 2012 と Windows Server 2012 R2 のドメイン コントローラーのみを実行するか、Windows Server 2016 以降のドメイン コントローラーのみを実行する必要があります。

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>脆弱なパスワードが受け入れられているが、受け入れるべきではない

この問題にはいくつかの原因が考えられます。

1. DC エージェントで実行されているパブリック プレビュー版ソフトウェアの有効期限が切れています。 「[パブリック プレビュー DC エージェント ソフトウェアの有効期限切れ](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired)」を参照してください。

1. DC エージェントがポリシーをダウンロードできないか、既存のポリシーの暗号化を解除できません。 上記のトピックで考えられる原因を確認してください。

1. パスワード ポリシーの適用モードがまだ [監査] に設定されています。 この構成が有効な場合は、Azure AD パスワード保護ポータルを使用してそのモードを [強制] に再構成します。 「[パスワード保護を有効にする](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)」を参照してください。

1. パスワード ポリシーが無効にされています。 この構成が有効な場合は、Azure AD パスワード保護ポータルを使用してそのモードを [有効] に再構成します。 「[パスワード保護を有効にする](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)」を参照してください。

1. DC エージェント ソフトウェアがドメイン内のすべてのドメイン コントローラーにはインストールされていません。 このような状況では、パスワードの変更操作中にリモートの Windows クライアントが特定のドメイン コントローラーを確実にターゲットにするようにすることは困難です。 DC エージェント ソフトウェアがインストールされている特定の DC を正常にターゲットにしていたと思われる場合は、DC エージェント管理イベント ログを再度確認することで確認できます。結果に関係なく、パスワードの検証結果を文書化するイベントが少なくとも 1 つあります。 パスワードが変更されたユーザーのイベントがない場合、パスワード変更は別のドメイン コントローラーで処理された可能性があります。

   別のテストとして、DC エージェント ソフトウェアがインストールされている DC に直接ログインした状態で、パスワードを設定したり変更したりしてみてください。 この手法は運用環境の Active Directory ドメインでは推奨されません。

   DC エージェント ソフトウェアの増分デプロイはこれらの制限の下でサポートされていますが、Microsoft では、DC エージェント ソフトウェアを、できるだけ早くドメイン内のすべてのドメイン コントローラーにインストールすることを強くお勧めします。

1. パスワード検証アルゴリズムが実際に期待どおりに動作している可能性があります。 「[パスワードの評価方法](concept-password-ban-bad.md#how-are-passwords-evaluated)」を参照してください。

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Ntdsutil.exe が弱い DSRM パスワードを設定できない

Active Directory では、常に新しいディレクトリ サービス修復モード パスワードが検証されて、ドメインのパスワードの複雑さ要件が満たされているかどうかが確認されています。この検証では、Azure AD パスワード保護などのパスワード フィルター DLL も呼び出されます。 新しい DSRM パスワードが拒否された場合、次のエラー メッセージが表示されます。

```text
C:\>ntdsutil.exe
ntdsutil: set dsrm password
Reset DSRM Administrator Password: reset password on server null
Please type password for DS Restore Mode Administrator Account: ********
Please confirm new password: ********
Setting password failed.
        WIN32 Error Code: 0xa91
        Error Message: Password doesn't meet the requirements of the filter dll's
```

Azure AD パスワード保護で Active Directory DSRM パスワードのパスワード検証イベント ログ イベントがログに記録されるとき、イベント ログ メッセージにユーザー名が含まれないことが予想されます。 この動作は、DSRM アカウントが実際の Active Directory ドメインの一部ではないローカル アカウントであるために発生します。  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>弱い DSRM パスワードが原因でドメイン コントローラー レプリカの昇格が失敗する

DC 昇格プロセス中に、新しいディレクトリ サービス修復モード パスワードが、検証のためにドメイン内の既存の DC に送信されます。 新しい DSRM パスワードが拒否された場合、次のエラー メッセージが表示されます。

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

上記の問題と同様に、このシナリオでは Azure AD パスワード保護パスワード検証の結果イベントでユーザー名が空になります。

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>弱いローカル管理者パスワードのためにドメイン コントローラーの降格が失敗する

DC エージェント ソフトウェアを実行中でもドメイン コントローラーを降格させることができます。 ただし、降格手続き中も DC エージェント ソフトウェアによって現在のパスワード ポリシーが継続的に適用されることに管理者は注意する必要があります。 新しいローカル管理者アカウントのパスワード (降格操作の一環で指定されます) は、他のパスワードと同様に検証されます。 DC 降格手順の一部として、ローカル管理者アカウントに対してセキュリティで保護されたパスワードを選択することをお勧めします。

降格が成功し、ドメイン コントローラーが再起動され、通常のメンバー サーバーとして改めて実行されると、DC エージェント ソフトウェアはパッシブ モードで動作するようになります。 このソフトウェアはいつでもアンインストールできます。

## <a name="booting-into-directory-services-repair-mode"></a>ディレクトリ サービス修復モードでの起動

ドメイン コントローラーがディレクトリ サービスの修復モードで起動された場合、DC エージェントのパスワード フィルター DLL でこの条件が検出され、現在アクティブなポリシー構成に関係なく、すべてのパスワード検証または適用アクティビティが無効にされます。 DC エージェントのパスワード フィルター DLL は、管理者のイベント ログに 10023 警告イベントを記録します。次に例を示します。

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>パブリック プレビュー DC エージェント ソフトウェアの有効期限切れ

Azure AD パスワード保護のパブリック プレビュー期間中、次の日付になったらパスワード検証要求の処理を停止するよう、DC エージェント ソフトウェアがハードコードされています。

* バージョン 1.2.65.0 は、2019 年 9 月 1 日にパスワード検証要求の処理を停止します。
* バージョン 1.2.25.0 およびそれ以前は、2019 年 7 月 1 日にパスワード検証要求の処理を停止しました。

期限が近づくと、時間制限のあるすべての DC エージェント バージョンは、起動時に DC エージェント管理イベント ログに 10021 イベントを出力します。これは次のようになります。

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

期限が過ぎると、時間制限のあるすべての DC エージェント バージョンは、起動時に DC エージェント管理イベント ログに 10022 イベントを出力します。これは次のようになります。

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

期限は初回起動時にしかチェックされないため、カレンダーの期限が過ぎてからも長期間、これらのイベントが記録されない場合があります。 期限が認識された後、ドメイン コントローラーまたはより大規模な環境への悪影響は、すべてのパスワードが自動的に承認されることを除いては発生しません。

> [!IMPORTANT]
> Microsoft では、有効期限が切れたパブリック プレビュー DC エージェントを、すぐに最新バージョンにアップグレードすることをお勧めします。

アップグレードが必要な環境内の DC エージェントを検出する簡単な方法は、`Get-AzureADPasswordProtectionDCAgent` コマンドレットを実行することです。次に例を示します。

```powershell
PS C:\> Get-AzureADPasswordProtectionDCAgent

ServerFQDN            : bpl1.bpl.com
SoftwareVersion       : 1.2.125.0
Domain                : bpl.com
Forest                : bpl.com
PasswordPolicyDateUTC : 8/1/2019 9:18:05 PM
HeartbeatUTC          : 8/1/2019 10:00:00 PM
AzureTenant           : bpltest.onmicrosoft.com
```

このトピックに関して、SoftwareVersion フィールドは明らかに、注目すべき重要なプロパティです。 PowerShell のフィルターを使用して、既に必要なベースライン バージョン以上である DC エージェントを除外することもできます。次に例を示します。

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

Azure AD パスワード保護プロキシ ソフトウェアでは、どのバージョンでも期間が制限されません。 Microsoft では、DC エージェントとプロキシ エージェントのどちらも、最新バージョンがリリースされたらすぐ、そのバージョンにアップグレードすることもお勧めします。 上記の DC エージェントの例と同様に、`Get-AzureADPasswordProtectionProxy` コマンドレットを使用して、アップグレードが必要なプロキシ エージェントを見つけることができます。

具体的なアップグレード手順の詳細については、[DC エージェントのアップグレード](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent)および[プロキシ エージェントのアップグレード](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-agent)に関する項目を参照してください。

## <a name="emergency-remediation"></a>緊急時の修復

DC エージェント サービスが問題の原因である状況が発生した場合、DC エージェント サービスは直ちにシャットダウンされる可能性があります。 DC エージェントのパスワード フィルター dll が実行中ではないサービスをまだ呼び出そうとすると、警告イベント (10012、10013) がログに記録されますが、その間にすべての受信パスワードが承認されます。 DC エージェント サービスは、必要に応じて Windows サービス コントロール マネージャーを使用してスタートアップの種類を "無効" に構成することもできます。

また、Azure AD パスワード保護ポータルで有効モードを [いいえ] に設定することで修復する方法もあります。 更新されたポリシーがダウンロードされたら、各 DC エージェント サービスが休止モードに入り、すべてパスワードが現状のままで受け入れられます。 詳細については、「[強制モード](howto-password-ban-bad-on-premises-operations.md#enforce-mode)」を参照してください。

## <a name="removal"></a>削除

Azure AD パスワード保護ソフトウェアをアンインストールし、関連するすべての状態をドメインとフォレストからクリーンアップする場合、次の手順で実行できます。

> [!IMPORTANT]
> これらの手順は、順番に実行することが重要です。 プロキシ サービスのインスタンスを実行中のままにすると、定期的に serviceConnectionPoint オブジェクトが再作成されます。 DC エージェント サービスのインスタンスを実行中のままにすると、定期的に serviceConnectionPoint オブジェクトと sysvol 状態が再作成されます。

1. すべてのマシンからプロキシ ソフトウェアをアンインストールします。 この手順では、再起動する**必要はありません**。
2. すべてのドメイン コントローラーから DC エージェント ソフトウェアをアンインストールします。 この手順では、再起動する**必要があります**。
3. 各ドメイン名前付けコンテキストのすべてのプロキシ サービス接続ポイントを手動で削除します。 これらのオブジェクトの場所は、次の Active Directory PowerShell コマンドを使用して検出できます。

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   $keywords 変数値の末尾のアスタリスク ("*") は省略しないでください。

   `Get-ADObject` コマンドで見つかった結果のオブジェクトは、`Remove-ADObject` にパイプ出力するか、手動で削除することができます。

4. 各ドメイン名前付けコンテキストに含まれるすべての DC エージェント接続ポイントを手動で削除します。 ソフトウェアの展開の規模によっては、フォレスト内のドメイン コントローラーごとにこのようなオブジェクトが 1 つ存在することがあります。 そのオブジェクトの場所は、次の Active Directory PowerShell コマンドを使用して検出できます。

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   `Get-ADObject` コマンドで見つかった結果のオブジェクトは、`Remove-ADObject` にパイプ出力するか、手動で削除することができます。

   $keywords 変数値の末尾のアスタリスク ("*") は省略しないでください。

5. フォレストレベルの構成状態を手動で削除します。 フォレストの構成状態は、Active Directory 構成の名前付けコンテキストのコンテナーに保持されます。 次のように検出および削除できます。

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. 次のフォルダーとそのすべての内容を手動で削除して、すべての sysvol 関連の状態を手動で削除します。

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   必要に応じて、特定のドメイン コントローラーのローカルでこのパスにアクセスすることもできます。既定の場所は次のようなパスになります。

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   sysvol 共有が既定以外の場所に設定されている場合は、別のパスになります。

## <a name="next-steps"></a>次の手順

[Azure AD パスワード保護についてよく寄せられる質問](howto-password-ban-bad-on-premises-faq.md)

グローバルおよびカスタムの禁止パスワード リストの詳細については、[不適切なパスワードの禁止](concept-password-ban-bad.md)に関する記事を参照してください。
