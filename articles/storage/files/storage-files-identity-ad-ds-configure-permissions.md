---
title: ユーザーがファイル レベルで行える操作を制御する - Azure ファイル共有
description: Azure ファイル共有に対するオンプレミスの AD DS 認証に対する Windows ACL アクセス許可を構成する方法について説明します。 詳細なアクセス制御を活用できるようにする。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/16/2020
ms.author: rogarana
ms.openlocfilehash: 698b4ebedfc9b41e8c5732a0a81226a971d65585
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103470763"
---
# <a name="part-three-configure-directory-and-file-level-permissions-over-smb"></a>パート 3: SMB 経由でディレクトリとファイル レベルのアクセス許可を構成する 

この記事を開始する前に、前の記事「[ID に共有レベルのアクセス許可を割り当てる](storage-files-identity-ad-ds-assign-permissions.md)」を完了し、共有レベルのアクセス許可が確実に設定されているようにしてください。

Azure RBAC によって共有レベルのアクセス許可を割り当てたら、詳細なアクセス制御を活用するために、ルート、ディレクトリ、またはファイル レベルで適切な Windows ACL を構成する必要があります。 Azure RBAC 共有レベルのアクセス許可は、ユーザーが共有にアクセスできるかどうかを決定する高レベルのゲートキーパーと考えてください。 一方、Windows ACL は、さらに細かなレベルで機能し、ディレクトリまたはファイル レベルでユーザーが実行できる操作を決定します。 ユーザーがファイルまたはディレクトリにアクセスしようとしたときに、共有レベルとファイル/ディレクトリ レベルの両方のアクセス許可が適用されます。したがって、両方に違いがある場合は、最も制限の厳しい方だけが適用されます。 たとえば、ユーザーがファイル レベルで読み取り/書き込みアクセス権を持っているが、共有レベルでは読み取りアクセス権しかない場合は、そのファイルは読み取ることしかできません。 同じことはこの逆にも当てはまります。ユーザーが共有レベルで読み取り/書き込みアクセス権を持っていても、ファイル レベルでは読み取りアクセス権しか持っていない場合は、やはりファイルを読み取ることしかできません。

## <a name="azure-rbac-permissions"></a>Azure RBAC アクセス許可

次の表は、この構成に関連する Azure RBAC アクセス許可を示しています。


| 組み込みのロール  | NTFS アクセス許可  | 結果のアクセス  |
|---------|---------|---------|
|記憶域ファイル データの SMB 共有の閲覧者 | フル コントロール、変更、読み取り、書き込み、実行 | 読み取り & 実行  |
|     |   Read |     Read  |
|記憶域ファイル データの SMB 共有の共同作成者  |  フル コントロール    |  変更、読み取り、書き込み、実行 |
|     |  変更         |  変更    |
|     |  読み取り & 実行 |  読み取り & 実行 |
|     |  Read           |  Read    |
|     |  Write          |  Write   |
|記憶域ファイル データの SMB 共有の管理者特権共同作成者 | フル コントロール  |  変更、読み取り、書き込み、編集、実行 |
|     |  変更          |  変更 |
|     |  読み取り & 実行  |  読み取り & 実行 |
|     |  Read            |  Read   |
|     |  Write           |  Write  |



## <a name="supported-permissions"></a>サポートされているアクセス許可

Azure Files では、基本的な Windows ACL と詳細な Windows ACL で構成される完全なセットをサポートします。 Azure ファイル共有内のディレクトリとファイルの Windows ACL を表示および構成するには、共有をマウントしてから、Windows エクスプローラーを使用するか、Windows の [icacls](/windows-server/administration/windows-commands/icacls) コマンドを実行するか、または [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) コマンドを実行します。 

スーパーユーザー アクセス許可を持つ ACL を構成するには、ドメインに参加している VM からのストレージ アカウント キーを使って共有をマウントする必要があります。 コマンド プロンプトから Azure ファイル共有をマウントし、Windows ACL を構成するには、次のセクションの説明に従ってください。

ファイル共有のルート ディレクトリには、次のアクセス許可が含まれています。

- BUILTIN\Administrators:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT authority \authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

|ユーザー|定義|
|---|---|
|BUILTIN\Administrators (BUILTIN\Administrators)|オンプレミスの AD DS 環境のドメイン管理者であるすべてのユーザー。
|BUILTIN\Users|AD 内のビルトイン セキュリティ グループ。 既定では、NT AUTHORITY\Authenticated Users が含まれています。 従来のファイル サーバーの場合は、サーバーごとにメンバーシップの定義を構成できます。 Azure Files の場合は、ホスティング サーバーが存在しないため、BUILTIN\Users には NT AUTHORITY\Authenticated Users と同じユーザーのセットが含まれます。|
|NT AUTHORITY\SYSTEM|ファイル サーバーのオペレーティング システムのサービス アカウント。 このようなサービス アカウントは、Azure Files コンテキストでは適用されません。 これは、ハイブリッド シナリオでの Windows Files サーバー エクスペリエンスと一貫性を持たせるために、ルート ディレクトリに含まれています。|
|NT AUTHORITY\Authenticated Users|有効な Kerberos トークンを取得できる AD 内のすべてのユーザー。|
|CREATOR OWNER|ディレクトリまたはファイルのオブジェクトにはそれぞれ、そのオブジェクトの所有者が含まれています。 そのオブジェクト上で "CREATOR OWNER" に ACL が割り当てられている場合、このオブジェクトの所有者であるユーザーは、ACL によって定義されたオブジェクトに対するアクセス許可を持っています。|



## <a name="mount-a-file-share-from-the-command-prompt"></a>コマンド プロンプトからファイル共有をマウントする

Windows の `net use` コマンドを使用して Azure ファイル共有をマウントします。 次の例では、プレースホルダーをお客様独自の値に置き換えてください。 ファイル共有のマウントの詳細については、「[Windows で Azure ファイル共有を使用する](storage-how-to-use-files-windows.md)」を参照してください。 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN,   Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Azure Files への接続で問題が発生した場合は、[Windows での Azure Files マウント エラーに対して発行したトラブルシューティング ツール](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/)に関するページをご覧ください。 また、ポート 445 がブロックされている場合のシナリオを回避するための[ガイダンス](./storage-files-faq.md#on-premises-access)も提供されています。 

## <a name="configure-windows-acls"></a>Windows ACL を構成する

ストレージ アカウント キーを使用してファイル共有をマウントしたら、Windows ACL (NTFS アクセス許可とも呼ばれます) を構成する必要があります。 Windows ACL を構成するには、エクスプローラーか icacls のどちらかを使用する必要があります。

Windows DACL が AD DS ID に対して構成されたオンプレミスのファイル サーバーにディレクトリまたはファイルがある場合は、Robocopy などの従来のファイル コピー ツールか [Azure AzCopy v 10.4+](https://github.com/Azure/azure-storage-azcopy/releases) を使用して、ACL を保持している Azure Files にコピーできます。 Azure File Sync によってディレクトリとファイルが Azure Files に階層化されている場合は、ACL が引き継がれ、ネイティブ形式で保持されます。

### <a name="configure-windows-acls-with-icacls"></a>icacls で Windows ACL を構成する

ルート ディレクトリを含む、ファイル共有下のすべてのディレクトリとファイル共有に完全なアクセス許可を付与するには、次の Windows コマンドを使用します。 例中のプレースホルダーをお客様独自の値に置き換えてください。

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

icacls を使用して Windows ACL を設定する方法や、サポートされるさまざまな種類のアクセス許可の詳細については、[コマンド ライン リファレンスの icacls](/windows-server/administration/windows-commands/icacls) に関するページをご覧ください。

### <a name="configure-windows-acls-with-windows-file-explorer"></a>Windows エクスプローラーを使用して Windows ACL を構成する

Windows エクスプローラーを使用して、ルート ディレクトリを含む、ファイル共有下のすべてのディレクトリとファイル共有に完全なアクセス許可を付与します。 AD ドメイン情報をエクスプローラーで正しく読み込むことができない場合は、オンプレミス AD 環境の信頼構成が原因の可能性があります。 クライアント コンピューターは、Azure ファイル認証で登録されている AD ドメイン コントローラーに接続できませんでした。 この場合は、icacls を Windows ACL 構成で使用します。

1. Windows エクスプローラーを開き、ファイルまたはディレクトリを右クリックし、 **[プロパティ]** を選択します。
1. **[セキュリティ]** タブをクリックします。
1. **[編集]** を選択して アクセス許可を変更します。
1. 既存のユーザーの権限を変更することや、 **[追加]** を選択して新しいユーザーにアクセス許可を付与することができます。
1. 新しいユーザーを追加するためのプロンプト ウィンドウで、アクセス許可を付与するターゲット ユーザーの名前を **[選択するオブジェクト名を入力してください]** ボックスに入力し、 **[名前の確認]** を選択して、ターゲット ユーザーの完全な UPN 名を見つけます。
1.    **[OK]** を選択します。
1.    **[セキュリティ]** タブで、新しいユーザーに付与するすべてのアクセス許可を選択します。
1.    **[適用]** を選択します。


## <a name="next-steps"></a>次のステップ

機能が有効になり、構成されたので、次の記事に進んで、ドメインに参加している VM から Azure ファイル共有をマウントします。

[パート 4: ドメインに参加している VM からファイル共有をマウントする](storage-files-identity-ad-ds-mount-file-share.md)
