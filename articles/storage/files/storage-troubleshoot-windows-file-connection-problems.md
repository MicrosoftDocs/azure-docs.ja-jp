---
title: "Windows での Azure Files に関する問題のトラブルシューティング | Microsoft Docs"
description: "Windows での Azure Files に関する問題のトラブルシューティング"
services: storage
documentationcenter: 
author: genlin
manager: willchen
editor: na
tags: storage
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: genli
ms.openlocfilehash: 5aacc8a920c9343c5efa89128aabb1505fc2d9aa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Windows での Azure Files に関する問題のトラブルシューティング

この記事では、Windows クライアントから接続するときに生じる、Microsoft Azure Files に関係する一般的な問題を示します。 これらの問題の考えられる原因と解決策についても説明します。 この記事のトラブルシューティングの手順のほかに、[AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) を使って Windows クライアント環境が前提条件を適切に満たしているかどうかを確認することもできます。 AzFileDiagnostics は、この記事で説明しているほとんどの症状を自動的に検出し、最適なパフォーマンスが得られる環境のセットアップを支援します。 この情報は、[Azure ファイル共有の トラブルシューティング ツール](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares)で入手することもできます。記載されている手順に従って、Azure ファイル共有の接続、マッピング、マウントに関する問題を解決することができます。


<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Azure ファイル共有をマウントまたはマウント解除するときに、エラー 53、エラー 67、またはエラー 87 が発生する

オンプレミスから、または別のデータセンターからファイル共有をマウントしようとすると、以下のエラーが発生する場合があります。

- システム エラー 53 が発生しました。 ネットワーク パスが見つかりませんでした。
- システム エラー 67 が発生しました。 ネットワーク名が見つかりません。
- システム エラー 87 が発生しました。 パラメーターが正しくありません。

### <a name="cause-1-unencrypted-communication-channel"></a>原因 1: 通信チャネルが暗号化されていない

通信チャネルが暗号化されていない場合や、接続の試行が Azure ファイル共有と同じデータセンターから行われていない場合、セキュリティ上の理由により、Azure ファイル共有への接続がブロックされます。 ユーザーのクライアント OS が SMB 暗号化をサポートしている場合に限り、通信チャネルの暗号化を利用できます。

Windows 8 以降および Windows Server 2012 以降の OS であれば、暗号化をサポートしている SMB 3.0 を含む要求をネゴシエートできます。

### <a name="solution-for-cause-1"></a>原因 1 の解決策

以下のいずれかのクライアントから接続します。

- バージョンの要件 (Windows 8 または Windows Server 2012 以降であること) を満たしている
- 同じデータセンター内の仮想マシンから、Azure ファイル共有のために使用される Azure ストレージ アカウントとして接続している

### <a name="cause-2-port-445-is-blocked"></a>原因 2: ポート 445 がブロックされている

ポート 445 から Azure Files データセンターへの送信方向の通信がブロックされている場合、システム エラー 53 またはシステム エラー 67 が発生することがあります。 ポート 445 からのアクセスを許可する ISP または許可しない ISP の概要を確認するには、[TechNet](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) を参照してください。

これが "システム エラー 53" メッセージの原因であるかどうかを把握するために、Portqry を使用して、TCP:445 エンドポイントを照会できます。 TCP:445 エンドポイントがフィルター処理済みとして表示される場合、TCP ポートがブロックされています。 クエリの使用例を次に示します。

  `g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

TCP ポート 445 がネットワーク パスに基づく規則によってブロックされている場合、次の出力が表示されます。

  `TCP port 445 (microsoft-ds service): FILTERED`

Portqry の使用方法の詳細については、「[Portqry.exe コマンド ライン ユーティリティの説明](https://support.microsoft.com/help/310099)」を参照してください。

### <a name="solution-for-cause-2"></a>原因 2 の解決策

IT 部門と連携して、ポート 445 の送信方向の通信を [Azure の IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)に解放します。

### <a name="cause-3-ntlmv1-is-enabled"></a>原因 3: NTLMv1 が有効になっている

クライアント側で NTLMv1 通信が有効になっていると、システム エラー 53 またはシステム エラー 87 が発生することがあります。 Azure Files では、NTLMv2 認証のみがサポートされています。 NTLMv1 が有効になっていると、クライアントの安全性が低下します。 そのため、Azure Files に対する通信がブロックされます。 

これがエラーの原因であるかどうかを確認するには、次のレジストリ サブキーで値が 3 に設定されていることを確認します。

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

詳細については、TechNet のトピック「[LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx)」を参照してください。

### <a name="solution-for-cause-3"></a>原因 3 の解決策

次のレジストリ サブキーで、**LmCompatibilityLevel** の値を既定値である 3 に戻します。

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Azure ファイル共有にコピーすると、エラー 1816 "このコマンドを実行するための十分なクォータがありません" が発生する

### <a name="cause"></a>原因

エラー 1816 は、ファイル共有がマウントされているコンピューター上のファイルに対して許可されている、同時に開くことのできるハンドルの上限に達したときに発生します。

### <a name="solution"></a>解決策

ハンドルをいくつか閉じて、同時に開いているハンドルの数を減らしてから、再試行します。 詳細については、「[Microsoft Azure Storage のパフォーマンスとスケーラビリティに対するチェック リスト](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」を参照してください。

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Windows で Azure Files との間でのファイルのコピーが遅い

Azure のファイル サービスにファイルを転送しようとした場合に、パフォーマンスが低下することがあります。

- 特定の最小 I/O サイズ要件がない場合は、最適なパフォーマンスを得るために I/O サイズとして 1 MB を使用することをお勧めします。
-   書き込みによって大きくなるファイルの最終サイズがわかっており、まだ書き込まれていないファイル末尾にゼロが含まれていてもソフトウェアに互換性の問題がない場合は、書き込みごとにサイズを増やすのではなく、事前にファイル サイズを設定します。
-   次のように適切なコピー方法を使用します。
    -   2 つのファイル共有間の転送には、[AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) を使用します。
    -   オンプレミス コンピューター上のファイル共有間では、[Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/) を使用します。

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Windows 8.1 または Windows Server 2012 R2 に関する考慮事項

Windows 8.1 または Windows Server 2012 R2 を実行しているクライアントに、[KB3114025](https://support.microsoft.com/help/3114025) 修正プログラムがインストールされていることを確認します。 この修正プログラムによって、ハンドルを作成する処理と閉じる処理のパフォーマンスが向上します。

次のスクリプトを実行すると、この修正プログラムがインストールされているかどうかを確認できます。

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

修正プログラムがインストールされている場合、次のような出力が表示されます。

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Azure Marketplace の Windows Server 2012 R2 イメージには、2015 12 月以降、既定で修正プログラム KB3114025 がインストールされています。

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer"></a>**マイ コンピューター**にドライブ文字を持つフォルダーがない

管理者として net use を使用して Azure ファイル共有をマップすると、共有がないかのような状態になります。

### <a name="cause"></a>原因

既定では、Windows エクスプローラーは管理者として実行されません。 管理コマンド プロンプトから net use を実行すると、ネットワーク ドライブを管理者としてマップすることになります。 マップされたドライブはユーザー主体であるため、異なるユーザー アカウントでドライブがマップされている場合、ログインしているユーザー アカウントにドライブが表示されません。

### <a name="solution"></a>解決策
管理者以外のコマンド ラインから共有をマウントします。 そのほか、[この TechNet の記事](https://technet.microsoft.com/library/ee844140.aspx)に従って **EnableLinkedConnections** レジストリ値を構成する方法もあります。

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>ストレージ アカウントにスラッシュが含まれている場合に、net use コマンドが失敗する

### <a name="cause"></a>原因

net use コマンドは、スラッシュ (/) をコマンド ライン オプションとして解釈します。 ユーザー アカウント名がスラッシュで始まっていると、ドライブのマッピングが失敗します。

### <a name="solution"></a>解決策

この問題を回避する方法としては、次の手順のどちらかを使用できます。

- 次の PowerShell コマンドを実行します。

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc" `

  バッチ ファイルから、コマンドを次のように実行することもできます。

  `Echo new-smbMapping ... | powershell -command –`

- 二重引用符でキーを囲みます。スラッシュが最初の文字である場合を除き、この問題を回避できます。 スラッシュが最初の文字である場合は、対話モードを使用してパスワードを別途入力するか、キーを再生成してスラッシュで始まらないキーを取得します。

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>アプリケーションまたはサービスが、マウントされた Azure Files ドライブにアクセスできない

### <a name="cause"></a>原因

ドライブは、ユーザーごとにマウントされます。 アプリケーションまたはサービスが、ドライブをマウントしたユーザー アカウントとは別のユーザー アカウントで実行されている場合、アプリケーションがドライブを認識しません。

### <a name="solution"></a>解決策

次のいずれかの解決策を使用します。

-   アプリケーションが属しているのと同じユーザー アカウントからドライブをマウントします。 PsExec などのツールを使用することができます。
- net use コマンドのユーザー名とパスワードのパラメーターで、ストレージ アカウント名とキーを渡します。

これらの手順を実行した後、システム/ネットワーク サービス アカウントに対して net use を実行すると、"システム エラー 1312 が発生しました。 指定されたログオン セッションは存在しません。 そのセッションは既に終了している可能性があります" というエラー メッセージが表示されることがあります。 このエラーが発生した場合は、net use に渡されるユーザー名にドメイン情報 ("[ストレージ アカウント名].file.core.windows.net" など) が含まれていることを確認してください。

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>エラー "暗号化をサポートしていない宛先に、ファイルをコピーします"

ファイルがネットワーク経由でコピーされる場合、ファイルはコピー元コンピューターで暗号化が解除された後、プレーンテキストとして送信され、コピー先で再暗号化されます。 ただし、暗号化されたファイルをコピーしようとしている場合は、"暗号化をサポートしていない宛先に、ファイルをコピーします" というエラーが表示されることがあります。

### <a name="cause"></a>原因
この問題は、暗号化ファイル システム (EFS) を使用している場合に発生することがあります。 BitLocker で暗号化されたファイルは Azure Files にコピーできます。 ただし、Azure Files は、NTFS EFS をサポートしていません。

### <a name="workaround"></a>対処法
ネットワーク経由でファイルをコピーするには、まず、ファイルの暗号化を解除します。 次のいずれかの方法を使用します。

- **copy /d** コマンドを使用します。 この方法では、暗号化されたファイルを、暗号化を解除したファイルとしてコピー先に保存することができます。
- 次のレジストリ キーを設定します。
  - Path = HKLM\Software\Policies\Microsoft\Windows\System
  - Value type = DWORD
  - Name = CopyFileAllowDecryptedRemoteDestination
  - Value = 1

レジストリ キーの設定は、ネットワーク共有に対するすべてのコピー操作に影響しますのでご注意ください。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。
まだ支援が必要な場合は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
