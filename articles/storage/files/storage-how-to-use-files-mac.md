---
title: macOS を使用して SMB 経由で Azure ファイル共有をマウントする | Microsoft Docs
description: macOS の Finder またはターミナルを使用して SMB 経由で Azure ファイル共有をマウントする方法について説明します。 Azure Files は、Microsoft の使いやすいクラウド ファイル システムです。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/23/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 119f4c0ea434bc431b40c905d9142e187b7d9474
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91326067"
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>macOS を使用して SMB 経由で Azure ファイル共有をマウントする
[Azure Files](storage-files-introduction.md) は、Microsoft の使いやすいクラウド ファイル システムです。 Azure ファイル共有は、macOS High Sierra 10.13 以降で、業界標準の SMB 3 プロトコルを使用してマウントできます。 この記事では、Terminal を使用して macOS と Finder UI で Azure ファイル共有をマウントする、2 つの異なる方法を示します。

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>macOS 上の Azure ファイル共有をマウントするための前提条件
* **ストレージ アカウント名**: Azure ファイル共有をマウントするには、ストレージ アカウントの名前が必要です。

* **ストレージ アカウント キー**: Azure ファイル共有をマウントするには、プライマリ (またはセカンダリ) ストレージ キーが必要です。 現時点では、SAS キーは、マウントではサポートされていません。

* **ポート 445 が開いていることの確認**: SMB は TCP ポート 445 経由で通信します。 クライアント マシン (Mac) で、ファイアウォールが TCP ポート 445 をブロックしていないことを確認します。

## <a name="mount-an-azure-file-share-via-finder"></a>Finder による Azure ファイル共有のマウント
1. **Finder を開きます**。macOS 上では既定で Finder が開かれていますが、ドック上の "macOS の顔アイコン" をクリックすると、Finder が現在選択されているアプリケーションであることを確認できます。  
    ![macOS の顔アイコン](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **[移動] メニューの [サーバーへ接続] を選択します**。前提条件の UNC パスを使用し、先頭の 2 つのバックスラッシュ (`\\`) は `smb://` に置き換え、他のすべてのバックスラッシュ (`\`) はスラッシュ (`/`) に置き換えます。 リンクは、次のようになります。![[サーバーへ接続] ダイアログ](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **ユーザー名とパスワードの入力を求められたら、ストレージ アカウント名とストレージ アカウント キーを使用します**。[サーバーへ接続] ダイアログで [接続] をクリックすると、ユーザー名とパスワードの入力を求められます (macOS ユーザー名が自動的に設定されます)。 macOS キーチェーンにストレージ アカウント名/ストレージ アカウント キーを指定することもできます。

4. **Azure ファイル共有を自由に使用します**。ユーザー名とパスワードを共有名とストレージ アカウント キーに置き換えた後、共有がマウントされます。 これを、通常のローカル フォルダー/ファイル共有を使用する場合のように使用することができます。たとえば、ファイル共有にファイルをドラッグ アンド ドロップすることができます。

    ![マウントされた Azure ファイル共有のスナップショット](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Terminal による Azure ファイル共有のマウント
1. `<storage-account-name>`、`<storage-account-key>`、および `<share-name>` を実際の環境の適切な値に置き換えます。

    ```
    open smb://<storage-account-name>:<storage-account-key>@<storage-account-name>.file.core.windows.net/<share-name>
    ```

2. **Azure ファイル共有を自由に使用します**。Azure ファイル共有は、前のコマンドで指定されたマウント ポイントにマウントされます。  

    ![マウントされた Azure ファイル共有のスナップショット](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>次の手順
* [Mac を共有コンピューターおよびサーバーに接続する - Apple サポート](https://support.apple.com/guide/mac-help/connect-mac-shared-computers-servers-mchlp1140/mac)