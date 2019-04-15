---
title: Azure AD パスワード保護のトラブルシューティング - Azure Active Directory
description: Azure AD パスワード保護の一般的なトラブルシューティングについて説明します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 108ead982529d2ac6549cceffd9d2177ab6456bf
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2019
ms.locfileid: "58863180"
---
# <a name="azure-ad-password-protection-troubleshooting"></a>Azure AD パスワード保護のトラブルシューティング

Azure AD パスワード保護をデプロイした後、トラブルシューティングを必要とする場合があります。 この記事では、いくつかの一般的なトラブルシューティング手順を理解しやすいように詳しく説明しています。

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>DC エージェントがディレクトリ内でプロキシを見つけることができない

この問題の主な症状は、DC エージェント管理イベント ログ内の 30017 イベントに相当します。

この問題の一般的な原因は、プロキシがまだ登録されていないことです。 プロキシが登録されている場合は、特定の DC エージェントでそのプロキシを表示できるようになるまでの AD レプリケーションの待機時間が原因の遅延がある可能性があります。

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>DC エージェントがプロキシ サーバーと通信できない

この問題の主な症状は、DC エージェント管理イベント ログ内の 30018 イベントに相当します。 これには、次に示すいくつかの原因が考えられます。

1. DC エージェントが、登録されているプロキシへのネットワーク接続を許可しない、ネットワークの分離された部分にあります。 そのため、この問題は、DC エージェントが Azure からパスワード ポリシーをダウンロードするためにプロキシと通信でき、それによって sysvol 共有内のポリシー ファイルのレプリケーションを使用して分離された DC によって取得される限り、要求されたり無害であったりする可能性があります。

1. プロキシ ホスト コンピューターが RPC エンドポイント マッパー エンドポイント (ポート 135) へのアクセスをブロックしている

   Azure AD パスワード保護プロキシのインストーラーでは、ポート 135 へのアクセスを許可する Windows ファイアウォールの受信規則が自動的に作成されます。 このルールを後で削除したり無効にしたりすると、DC エージェントはプロキシ サービスと通信できなくなります。 別のファイアウォール製品ではなく組み込みの Windows ファイアウォールを無効にしている場合は、そのファイアウォールを、ポート 135 へのアクセスを許可するように構成する必要があります。

1. プロキシ ホスト コンピューターがプロキシ サービスによってリッスンされる RPC エンドポイント (動的または静的) へのアクセスをブロックしている

   Azure AD パスワード保護プロキシのインストーラーでは、Azure AD パスワード保護プロキシ サービスによってリッスンされる受信ポートへのアクセスを許可する Windows ファイアウォールの受信規則が、自動的に作成されます。 このルールを後で削除したり無効にしたりすると、DC エージェントはプロキシ サービスと通信できなくなります。 別のファイアウォール製品ではなく組み込みの Windows ファイアウォールを無効にしている場合は、そのファイアウォールを、Azure AD パスワード保護プロキシ サービスによってリッスンされる受信ポートへのアクセスを許可するように構成する必要があります。 この構成は、より具体的には、(`Set-AzureADPasswordProtectionProxyConfiguration` コマンドレットを使用して) プロキシ サービスが特定の静的な RPC ポートをリッスンするように構成されている場合に行われることがあります。

## <a name="the-proxy-service-can-receive-calls-from-dc-agents-in-the-domain-but-is-unable-to-communicate-with-azure"></a>プロキシ サービスがドメイン内の DC エージェントからの呼び出しを受信できるが、Azure と通信できない

1. プロキシ コンピューターが「[デプロイ要件](howto-password-ban-bad-on-premises-deploy.md)」に示されているエンドポイントに接続できることを確認してください。

1. フォレストとすべてのプロキシ サーバーが同じ Azure テナントに対して登録されていることを確認します。

   これは、PowerShell コマンドレット `Get-AzureADPasswordProtectionProxy` と `Get-AzureADPasswordProtectionDCAgent` を実行し、返された各項目の `AzureTenant` プロパティを比較することで確認できます。 正しく動作させるためには、すべての DC エージェントとプロキシ サーバーにわたって、これらがフォレスト内で一致している必要があります。

   Azure テナントの登録に矛盾した状態が存在する場合、`Register-AzureADPasswordProtectionProxy` と `Register-AzureADPasswordProtectionForest` のいずれかまたは両方の PowerShell コマンドレットを必要に応じて実行し、すべての登録について確実に同じ Azure テナントの資格情報を使用することで、これを修復できます。

## <a name="the-dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files-and-other-state"></a>DC エージェントがパスワード ポリシー ファイルやその他の状態を暗号化または暗号化解除できない

この問題はさまざまな症状で発生する可能性がありますが、通常は一般的な根本原因があります。

Azure AD パスワード保護には、Microsoft キー配布サービスによって提供される暗号化と復号化の機能に対する重要な依存関係があり、Windows Server 2012 以降を実行しているドメイン コントローラーで使用できます。 KDS サービスは、ドメイン内の Windows Server 2012 以降のすべてのドメイン コントローラーで有効にし、機能するようにする必要があります。

既定では、KDS サービスのサービス開始モードは、手動 (トリガーで開始) として構成されます。 この構成は、クライアントが初めてサービスを使用しようとすると、サービスオンデマンドで開始されることを意味します。 この既定のサービス開始モードは、Azure AD パスワード保護で使用してもかまいません。

KDS サービス開始モードが [無効] に構成されている場合、Azure AD パスワード保護を正常に機能させるためには、この構成を修正する必要があります。

この問題の簡単なテストは、サービス管理 MMC コンソールを使用するか、他のサービス管理ツールを使用して (たとえば、コマンド プロンプト コンソールから "net start kdssvc" を実行して)、KDS サービスを手動で開始することです。 KDS サービスは、正常に開始して実行を維持することが期待されます。

KDS サービスを開始できない最も一般的な根本原因は、Active Directory ドメイン コントローラーのオブジェクトが既定のドメイン コントローラー OU の外部にあることです。 この構成は KDS サービスではサポートされておらず、Azure AD パスワード保護によって課せられた制限ではありません。 この状態の修正は、ドメイン コントローラーのオブジェクトを既定のドメイン コントローラー OU の下の場所に移動することです。

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>脆弱なパスワードが受け入れられているが、受け入れるべきではない

この問題にはいくつかの原因が考えられます。

1. DC エージェントがポリシーをダウンロードできないか、既存のポリシーの暗号化を解除できません。 上記のトピックで考えられる原因を確認してください。

1. パスワード ポリシーの適用モードがまだ [監査] に設定されています。 この構成が有効な場合は、Azure AD パスワード保護ポータルを使用してそのモードを [強制] に再構成します。 「[パスワード保護を有効にする](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)」を参照してください。

1. パスワード ポリシーが無効にされています。 この構成が有効な場合は、Azure AD パスワード保護ポータルを使用してそのモードを [有効] に再構成します。 「[パスワード保護を有効にする](howto-password-ban-bad-on-premises-operations.md#enable-password-protection)」を参照してください。

1. DC エージェント ソフトウェアがドメイン内のすべてのドメイン コントローラーにはインストールされていません。 このような状況では、パスワードの変更操作中にリモートの Windows クライアントが特定のドメイン コントローラーを確実にターゲットにするようにすることは困難です。 DC エージェント ソフトウェアがインストールされている特定の DC を正常にターゲットにしていたと思われる場合は、DC エージェント管理イベント ログを再度確認することで確認できます。結果に関係なく、パスワードの検証結果を文書化するイベントが少なくとも 1 つあります。 パスワードが変更されたユーザーのイベントがない場合、パスワード変更は別のドメイン コントローラーで処理された可能性があります。

   別のテストとして、DC エージェント ソフトウェアがインストールされている DC に直接ログインした状態で、パスワードを設定したり変更したりしてみてください。 この手法は運用環境の Active Directory ドメインでは推奨されません。

   DC エージェント ソフトウェアの増分デプロイはこれらの制限の下でサポートされていますが、Microsoft では、DC エージェント ソフトウェアを、できるだけ早くドメイン内のすべてのドメイン コントローラーにインストールすることを強くお勧めします。

1. パスワード検証アルゴリズムが実際に期待どおりに動作している可能性があります。 「[パスワードの評価方法](concept-password-ban-bad.md#how-are-passwords-evaluated)」を参照してください。

## <a name="directory-services-repair-mode"></a>ディレクトリ サービスの修復モード

ドメイン コントローラーがディレクトリ サービスの修復モードで起動された場合、DC エージェント サービスでこの条件が検出され、現在アクティブなポリシー構成に関係なく、すべてのパスワード検証または適用アクティビティが無効にされます。

## <a name="emergency-remediation"></a>緊急時の修復

DC エージェント サービスが問題の原因である状況が発生した場合、DC エージェント サービスは直ちにシャットダウンされる可能性があります。 DC エージェントのパスワード フィルター dll が実行中ではないサービスをまだ呼び出そうとすると、警告イベント (10012、10013) がログに記録されますが、その間にすべての受信パスワードが承認されます。 DC エージェント サービスは、必要に応じて Windows サービス コントロール マネージャーを使用してスタートアップの種類を "無効" に構成することもできます。

また、Azure AD パスワード保護ポータルで有効モードを [いいえ] に設定することで修復する方法もあります。 更新されたポリシーがダウンロードされたら、各 DC エージェント サービスが休止モードに入り、すべてパスワードが現状のままで受け入れられます。 詳細については、「[強制モード](howto-password-ban-bad-on-premises-operations.md#enforce-mode)」を参照してください。

## <a name="domain-controller-demotion"></a>ドメイン コントローラーの降格

DC エージェント ソフトウェアを実行中でもドメイン コントローラーを降格させることができます。 ただし、降格手続き中も DC エージェント ソフトウェアによって現在のパスワード ポリシーが継続的に適用されることに管理者は注意する必要があります。 新しいローカル管理者アカウントのパスワード (降格操作の一環で指定されます) は、他のパスワードと同様に検証されます。 DC の降格手続きの一環でローカル管理者アカウントに安全なパスワードを選択することをお勧めします。ただし、DC エージェント ソフトウェアによる新しいローカル管理者アカウントのパスワードの検証は、既存の降格操作手続きに影響する可能性があります。

降格が成功し、ドメイン コントローラーが再起動され、通常のメンバー サーバーとして改めて実行されると、DC エージェント ソフトウェアはパッシブ モードで動作するようになります。 このソフトウェアはいつでもアンインストールできます。

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
