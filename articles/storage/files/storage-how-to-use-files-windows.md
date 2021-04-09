---
title: Windows で Azure ファイル共有を使用する | Microsoft Docs
description: Windows と Windows Server での Azure ファイル共有の使用方法について説明します。 オンプレミスまたは Azure VM で実行されている Windows インストールで、SMB 3.0 を使用して Azure ファイル共有を利用します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e64b7efdd430287a7a3a969c5bf62b0c0e2aec9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94626896"
---
# <a name="use-an-azure-file-share-with-windows"></a>Windows で Azure ファイル共有を使用する
[Azure Files](storage-files-introduction.md) は、Microsoft の使いやすいクラウド ファイル システムです。 Azure ファイル共有は、Windows と Windows Server でシームレスに使うことができます。 この記事では、Windows と Windows Server で Azure ファイル共有を使う際の注意点について取り上げます。

Azure ファイル共有がホストされている Azure リージョンの外の Azure ファイル共有 (オンプレミスの共有、他の Azure リージョン内の共有など) を使用するために、OS は SMB 3.0 をサポートする必要があります。 

Azure ファイル共有は、Azure VM とオンプレミスのどちらかで実行されている Windows インストール済み環境で使用できます。 次の表は、ファイル共有へのアクセスがサポートされる環境を OS バージョンごとに示したものです。

| Windows のバージョン        | SMB のバージョン | Azure VM でマウント可能 | オンプレミスでマウント可能 |
|------------------------|-------------|-----------------------|-----------------------|
| Windows Server 2019 | SMB 3.0 | はい | はい |
| Windows 10<sup>1</sup> | SMB 3.0 | はい | はい |
| Windows Server 半期チャネル<sup>2</sup> | SMB 3.0 | はい | はい |
| Windows Server 2016 | SMB 3.0 | はい | はい |
| Windows 8.1 | SMB 3.0 | はい | はい |
| Windows Server 2012 R2 | SMB 3.0 | はい | はい |
| Windows Server 2012 | SMB 3.0 | はい | はい |
| Windows 7<sup>3</sup> | SMB 2.1 | はい | いいえ |
| Windows Server 2008 R2<sup>3</sup> | SMB 2.1 | はい | いいえ |

<sup>1</sup>Windows 10、バージョン 1507、1607、1803、1809、1903、1909、および 2004。  
<sup>2</sup>Windows Server、バージョン 1809、1903、1909、2004。  
<sup>3</sup>Windows 7 および Windows Server 2008 R2 に対する Microsoft の通常のサポートは終了しました。 [拡張セキュリティ更新 (ESU) プログラム](https://support.microsoft.com/help/4497181/lifecycle-faq-extended-security-updates)を介してのみ、セキュリティ更新プログラムの追加サポートを購入できます。 これらのオペレーティング システムから移行することを強くお勧めします。

> [!Note]  
> 常に、各 Windows バージョンの最新のサポート技術情報を参照することをお勧めします。

## <a name="prerequisites"></a>前提条件 

ポート 445 が開いていることを確認します。SMB プロトコルでは、TCP ポート 445 が開いている必要があります。ポート 445 がブロックされている場合は、接続が失敗します。 ポート 445 がファイアウォールでブロックされているかどうかは、`Test-NetConnection` コマンドレットで確認できます。 ブロックされた 445 ポートを回避する方法については、「[原因 1:ポート 445 がブロックされている](storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked)」セクション (Windows トラブルシューティング ガイド) を参照してください。

## <a name="using-an-azure-file-share-with-windows"></a>Windows で Azure ファイル共有を使用する
Windows で Azure ファイル共有を使用するには、Azure ファイル共有にドライブ文字 (マウント ポイントのパス) を割り当ててマウントするか、または対応する [UNC パス](/windows/win32/fileio/naming-a-file)経由でアクセスする必要があります。 

この記事では、ストレージ アカウント キーを使用してファイル共有にアクセスします。 ストレージ アカウント キーは、アクセスするファイル共有内のファイルとフォルダーすべてに対する管理者アクセス許可を含んだストレージ アカウントの管理者キーであると共に、ストレージ アカウントに格納されているすべてのファイル共有および他のストレージ リソース (BLOB、キュー、テーブルなど) の管理者キーでもあります。 それで対応できないワークロードについては、[Azure File Sync](storage-sync-files-planning.md) または [SMB 経由の ID ベースの認証](storage-files-active-directory-overview.md)を使用できます。

SMB ファイル共有が想定されている基幹業務 (LOB) アプリケーションを Azure にリフトアンドシフトする一般的なパターンは、専用の Windows ファイル サーバーを Azure VM で実行する代わりとして Azure ファイル共有を使うことです。 基幹業務アプリケーションで Azure ファイル共有を使うための移行に関して、その作業を成功させるうえで重要な考慮事項があります。多くの基幹業務アプリケーションは、VM の管理者アカウントではなく、制限されたシステム アクセス許可を与えられた専用のサービス アカウントのコンテキストで実行されるということです。 そのため、Azure ファイル共有の資格情報をマウント/保存する際は、自分の管理者アカウントからではなく、必ずサービス アカウントのコンテキストから行う必要があります。

### <a name="mount-the-azure-file-share"></a>Azure ファイル共有をマウントする

Azure portal には、ファイル共有をホストに直接マウントするために使用できるスクリプトが用意されています。 この用意されているスクリプトを使用することをお勧めします。

このスクリプトを入手するには:

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. マウントするファイル共有が含まれているストレージ アカウントに移動します。
1. **[ファイル共有]** を選択します。
1. マウントするファイル共有を選択します。

    :::image type="content" source="media/storage-how-to-use-files-windows/select-file-shares.png" alt-text="例":::

1. **[接続]** を選択します。

    :::image type="content" source="media/storage-how-to-use-files-windows/file-share-connect-icon.png" alt-text="ファイル共有の接続アイコンのスクリーンショット。":::

1. 共有のマウント先のドライブ文字を選択します。
1. 表示されたスクリプトをコピーします。

    :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="テキストの例":::

1. ファイル共有をマウントするホスト上のシェルにスクリプトを貼り付けて実行します。

これで、Azure ファイル共有がマウントされました。

### <a name="mount-the-azure-file-share-with-file-explorer"></a>エクスプローラーを使用した Azure ファイル共有のマウント
> [!Note]  
> 以下の手順は Windows 10 で操作する場合であり、それ以前のリリースでは少し異なることがある点に注意してください。 

1. エクスプローラーを開きます。 [スタート] メニューから開くことも、Windows + E キーを押すショートカットで開くこともできます。

1. ウィンドウの左側の **[PC]** に移動します。 これで、リボンで使用できるメニューが変更されます。 [コンピューター] メニューの **[ネットワーク ドライブの割り当て]** を選択します。
    
    ![[ネットワーク ドライブの割り当て] ドロップダウン メニューのスクリーンショット](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

1. ドライブ文字を選択し、UNC パスを入力します。UNC パスの形式は `\\<storageAccountName>.file.core.windows.net\<fileShareName>` です (例: `\\anexampleaccountname.file.core.windows.net\example-share-name`)。
    
    ![[ネットワーク ドライブの割り当て] ダイアログのスクリーンショット](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

1. ユーザー名として先頭に `AZURE\` を付けたストレージ アカウント名を使用し、パスワードとしてストレージ アカウント キーを使用します。
    
    ![ネットワーク資格情報ダイアログのスクリーンショット](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

1. Azure ファイル共有を自由に使用します。
    
    ![Azure ファイル共有がマウントされました](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

1. Azure ファイル共有をマウント解除することになったら、エクスプローラーの **[ネットワークの場所]** の下にある共有のエントリを右クリックし、 **[切断]** を選択します。

### <a name="accessing-share-snapshots-from-windows"></a>Windows から共有スナップショットへのアクセス
手動で、またはスクリプトや Azure Backup のようなサービスを通じて自動で共有スナップショットを取得した場合、Windows のファイル共有内にある以前のバージョンの共有、ディレクトリ、または特定のファイルを表示することができます。 共有スナップショットは、[Azure PowerShell](storage-how-to-use-files-powershell.md)、[Azure CLI](storage-how-to-use-files-cli.md)、または [Azure portal](storage-how-to-use-files-portal.md) を使用して取得できます。

#### <a name="list-previous-versions"></a>以前のバージョンを一覧表示する
復元が必要な項目または親項目を参照します。 ダブルクリックして、目的のディレクトリに移動します。 右クリックして、メニューから **[プロパティ]** を選択します。

![選択したディレクトリの右クリック メニュー](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

**[以前のバージョン]** を選択して、このディレクトリの共有スナップショットの一覧を表示します。 ネットワークの速度とディレクトリ内の共有のスナップショットの数に応じて、一覧の読み込みに数秒かかる場合があります。

![[以前のバージョン] タブ](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

**[開く]** を選択して、特定のスナップショットを開くことができます。 

![開かれたスナップショット](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>以前のバージョンから復元する
共有スナップショット作成時のディレクトリ全体の内容を元の場所に再帰的にコピーするには、 **[復元]** を選択します。

 ![警告メッセージ内の [復元] ボタン](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="securing-windowswindows-server"></a>Windows/Windows Server のセキュリティ保護
Windows で Azure ファイル共有をマウントするには、ポート 445 がアクセス可能な状態になっている必要があります。 ポート 445 は、SMB 1 に固有のセキュリティ リスクから、多くの組織でブロックされています。 SMB 1 は Windows と Windows Server に備わっているレガシ ファイル システム プロトコルであり、CIFS (Common Internet File System) と呼ばれることもあります。 SMB 1 は、旧式で効率が悪く、また何よりもセキュリティに不安があるプロトコルです。 さいわい、Azure Files は SMB 1 をサポートしておらず、サポートされているいずれのバージョンの Windows および Windows Server も、SMB 1 を削除するか無効にすることができます。 Azure ファイル共有を運用環境で使用する前に、SMB 1 のクライアントおよびサーバーを Windows から削除するか無効にすることを[強くお勧め](https://aka.ms/stopusingsmb1)します。

次の表は、Windows のバージョンごとに、SMB 1 の状態についての詳しい情報をまとめたものです。

| Windows のバージョン                           | SMB 1 の既定の状態 | 無効化/削除の方法       | 
|-------------------------------------------|----------------------|-----------------------------|
| Windows Server 2019                       | 無効             | Windows の機能を使って削除 |
| Windows Server バージョン 1709 以降            | 無効             | Windows の機能を使って削除 |
| Windows 10 バージョン 1709 以降                | 無効             | Windows の機能を使って削除 |
| Windows Server 2016                       | Enabled              | Windows の機能を使って削除 |
| Windows 10 バージョン 1507、1607、1703 | Enabled              | Windows の機能を使って削除 |
| Windows Server 2012 R2                    | Enabled              | Windows の機能を使って削除 | 
| Windows 8.1                               | Enabled              | Windows の機能を使って削除 | 
| Windows Server 2012                       | Enabled              | レジストリで無効化       | 
| Windows Server 2008 R2                    | Enabled              | レジストリで無効化       |
| Windows 7                                 | Enabled              | レジストリで無効化       | 

### <a name="auditing-smb-1-usage"></a>SMB 1 の使用状況の監査
> Windows Server 2019、Windows Server 半期チャネル (バージョン 1709 および 1803)、Windows Server 2016、Windows 10 (バージョン 1507、1607、1703、1709、1803)、Windows Server 2012 R2、Windows 8.1 が対象となります

お使いの環境から SMB 1 を削除する前に、その変更によって支障が生じるクライアントがないかを確認するために、SMB 1 の使用状況を監査したい場合があります。 SMB 共有に対する要求が SMB 1 で行われると、イベント ログの `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit` に、監査イベントが記録されます。 

> [!Note]  
> Windows Server 2012 R2 と Windows 8.1 で監査のサポートを有効にするには、少なくとも [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720) をインストールする必要があります。

監査を有効にするには、管理者特権の PowerShell セッションから次のコマンドレットを実行します。

```powershell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>Windows Server から SMB 1 を削除する
> Windows Server 2019、Windows Server 半期チャネル (バージョン 1709 および 1803)、Windows Server 2016、Windows Server 2012 R2 が対象となります

Windows Server インスタンスから SMB 1 を削除するには、管理者特権の PowerShell セッションから次のコマンドレットを実行します。

```powershell
Remove-WindowsFeature -Name FS-SMB1
```

削除処理を完了するために、サーバーを再起動してください。 

> [!Note]  
> Windows 10 および Windows Server バージョン 1709 以降では、SMB 1 が既定ではインストールされておらず、SMB 1 クライアントおよび SMB 1 サーバー用の別個の Windows 機能が存在します。 SMB 1 サーバー (`FS-SMB1-SERVER`) と SMB 1 クライアント (`FS-SMB1-CLIENT`) は、どちらもインストールしないことをお勧めします。

### <a name="removing-smb-1-from-windows-client"></a>Windows クライアントから SMB 1 を削除する
> Windows 10 (バージョン 1507、1607、1703、1709、1803) および Windows 8.1 が対象となります。

Windows クライアントから SMB 1 を削除するには、管理者特権の PowerShell セッションから次のコマンドレットを実行します。

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

削除処理を完了するために、PC を再起動してください。

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>レガシ バージョンの Windows/Windows Server で SMB 1 を無効にする
> Windows Server 2012、Windows Server 2008 R2、Windows 7 が対象となります。

レガシ バージョンの Windows/Windows Server では SMB 1 を完全に削除することはできませんが、レジストリで無効にすることができます。 SMB 1 を無効にするには、`DWORD` 型で値 `0` の新しいレジストリ キー `SMB1` を `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters` に作成します。

次の PowerShell コマンドレットを使って簡単に実行することもできます。

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" SMB1 -Type DWORD -Value 0 –Force
```

SMB 1 を無効にするには、このレジストリ キーを作成した後、サーバーを再起動する必要があります。

### <a name="smb-resources"></a>SMB のリソース
- [SMB 1 の使用を停止する](https://blogs.technet.microsoft.com/filecab/2016/09/16/stop-using-smb1/)
- [SMB 1 製品一覧](https://blogs.technet.microsoft.com/filecab/2017/06/01/smb1-product-clearinghouse/)
- [DSCEA で環境内の SMB 1 を検出する](/archive/blogs/ralphkyttle/discover-smb1-in-your-environment-with-dscea)
- [グループ ポリシーで SMB 1 を無効にする](/archive/blogs/secguide/disabling-smbv1-through-group-policy)

## <a name="next-steps"></a>次のステップ
Azure Files の詳細については、次のリンクをご覧ください。
- [Azure Files のデプロイの計画](storage-files-planning.md)
- [FAQ](./storage-files-faq.md)
- [Windows 上でのトラブルシューティング](storage-troubleshoot-windows-file-connection-problems.md)