---
title: "macOS を使用して SMB 経由で Azure ファイル共有をマウントする | Microsoft Docs"
description: "macOS を使用して SMB 経由で Azure ファイル共有をマウントする方法について説明します。"
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: 6e71a13f99160fdd310be1e9a59717c9fecbf35d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>macOS を使用して SMB 経由で Azure ファイル共有をマウントする
[Azure Files](storage-files-introduction.md) は、業界標準を使用して Azure でネットワーク ファイル共有を作成および使用できるようにする、Microsoft のサービスです。 Azure ファイル共有は、macOS Sierra (10.12) および El Capitan (10.11) でマウントできます。 この記事では、Terminal を使用して macOS と Finder UI で Azure ファイル共有をマウントする、2 つの異なる方法を示します。

> [!Note]  
> SMB 経由で Azure ファイル共有をマウントする前に、SMB パケット署名を無効にすることをお勧めします。 そうしないと、macOS から Azure ファイル共有にアクセスするときにパフォーマンスが低下する可能性があります。 SMB 接続は暗号化されるため、接続のセキュリティには影響しません。 Terminal で次のコマンドを実行すると、この [SMB パケット署名の無効化についての Apple サポート記事](https://support.apple.com/HT205926)で説明されているように、SMB パケット署名が無効になります。  
>    ```
>    sudo -s
>    echo "[default]" >> /etc/nsmb.conf
>    echo "signing_required=no" >> /etc/nsmb.conf
>    exit
>    ```

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>macOS 上の Azure ファイル共有をマウントするための前提条件
* **ストレージ アカウント名**: Azure ファイル共有をマウントするには、ストレージ アカウントの名前が必要になります。

* **ストレージ アカウント キー**: Azure ファイル共有をマウントするには、プライマリ (またはセカンダリ) ストレージ キーが必要になります。 SAS キーは現在、マウントではサポートされていません。

* **ポート 445 が開いていることの確認**: SMB は TCP ポート 445 経由で通信します。 クライアント マシン (Mac) で、ファイアウォールが TCP ポート 445 をブロックしていないことを確認します。

## <a name="mount-an-azure-file-share-via-finder"></a>Finder による Azure ファイル共有のマウント
1. **Finder を開きます**。macOS 上では既定で Finder が開かれていますが、ドック上の "macOS の顔アイコン" をクリックすると、Finder が現在選択されているアプリケーションであることを確認できます。  
    ![macOS の顔アイコン](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **[移動] メニューの [サーバーへ接続] を選択します**。[前提条件](#preq)の UNC パスを使用し、先頭の 2 つのバックスラッシュ (`\\`) は `smb://` に置き換え、他のすべてのバックスラッシュは (`\`) はスラッシュ (`/`) に置き換えます。 リンクは、次のようになります。![[サーバーへ接続] ダイアログ](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **ユーザー名とパスワードの入力を求められたら、共有名とストレージ アカウント キーを使用します**。[サーバーへ接続] ダイアログで [接続] をクリックすると、ユーザー名とパスワードの入力を求められます (macOS ユーザー名が自動的に設定されます)。 macOS キーチェーンに共有名/ストレージ アカウント キーを指定することもできます。

4. **Azure ファイル共有を自由に使用します**。ユーザー名とパスワードを共有名とストレージ アカウント キーに置き換えた後、共有がマウントされます。 これを、通常のローカル フォルダー/ファイル共有を使用する場合のように使用することができます。たとえば、ファイル共有にファイルをドラッグ アンド ドロップすることができます。

    ![マウントされた Azure ファイル共有のスナップショット](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Terminal による Azure ファイル共有のマウント
1. `<storage-account-name>` をストレージ アカウントの名前に置き換えます。 入力を求められたら、パスワードとしてストレージ アカウント キーを指定します。 

    ```
    mount_smbfs //<storage-account-name>@<storage-account-name>.file.core.windows.net/<share-name> <desired-mount-point>
    ```

2. **Azure ファイル共有を自由に使用します**。Azure ファイル共有は、前のコマンドで指定されたマウント ポイントにマウントされます。  

    ![マウントされた Azure ファイル共有のスナップショット](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>次のステップ
Azure Files の詳細については、次のリンクをご覧ください。

* [Apple サポート記事 - Mac 上のファイル共有に接続する方法](https://support.apple.com/HT204445)
* [FAQ](../storage-files-faq.md)
* [Windows 上でのトラブルシューティング](storage-troubleshoot-windows-file-connection-problems.md)      
* [Linux 上でのトラブルシューティング](storage-troubleshoot-linux-file-connection-problems.md)    