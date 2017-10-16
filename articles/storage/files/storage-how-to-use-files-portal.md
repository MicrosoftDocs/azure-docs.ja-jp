---
title: "Azure ポータルから Azure Files を管理する方法 | Microsoft Docs"
description: "Azure ポータルを使用して Azure Files を管理する方法を説明します。"
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fc521fa40428caacebb072cb1aacdebf0ce977ac
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="how-to-use-azure-files-from-the-azure-portal"></a>Azure Portal から Azure Files を使用する方法
[Azure ポータル](https://portal.azure.com)では、ユーザー インターフェイスを使用して、Azure Files を管理できます。 Web ブラウザーから、次の操作を実行できます。

* ファイル共有の作成
* ファイル共有からのファイルのアップロードおよびダウンロード
* 各ファイル共有の実際の使用状況の監視
* ファイル共有のサイズ クォータの調整
* Windows または Linux クライアントからファイル共有をマウントするために使用するマウンド コマンドをコピーします。

## <a name="create-file-share"></a>ファイル共有の作成
1. Azure ポータルにサインインします。
2. ナビゲーション メニューで、**[ストレージ アカウント]** または **[ストレージ アカウント (クラシック)]** をクリックします。
    
    ![Screenshot that shows how to create file share in the portal](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share1.png)

3. ストレージ アカウントを選択します。

    ![Screenshot that shows how to create file share in the portal](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share2.png)

4. [Files] サービスを選択します。

    ![Screenshot that shows how to create file share in the portal](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share3.png)

5. [ファイル共有] をクリックし、リンクをクリックして最初のファイル共有を作成します。

    ![Screenshot that shows how to create file share in the portal](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share4.png)

6. 最初のファイル共有を作成するには、ファイル共有名とファイル共有のサイズ (最大 5120 GB) を入力します。 作成されたファイル共有は、SMB 2.1 または SMB 3.0 をサポートしている任意のファイル システムからマウントできます。 ファイル共有の **[クォータ]** をクリックすると、ファイルのサイズを最大 5,120 GB まで変更することができます。 Azure Files を使用するストレージ コストを見積もるには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用してください。

    ![Screenshot that shows how to create file share in the portal](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share5.png)

## <a name="upload-and-download-files"></a>ファイルのアップロードとダウンロード
1. 既に作成した 1 つのファイル共有を選択します。

    ![Screenshot that shows how to upload and download files from the portal](./media/storage-how-to-use-files-portal/use-files-portal-upload-file1.png)

2. **[アップロード]** をクリックして、ファイルをアップロードするためのユーザー インターフェイスを開きます。

    ![Screenshot that shows how to upload files from the portal](./media/storage-how-to-use-files-portal/use-files-portal-upload-file2.png)

## <a name="connect-to-file-share"></a>ファイル共有への接続
-  **[接続]** をクリックし、Windows および Linux からファイル共有にマウントするためのコマンド ラインを取得します。 Linux ユーザーの場合、他の Linux ディストリビューションのマウント操作手順の詳細については、[Linux で Azure Files を使用する方法](../storage-how-to-use-files-linux.md)に関するページも参照してください。

    ![Screenshot that shows how to mount the file share](./media/storage-how-to-use-files-portal/use-files-portal-connect.png)
-  Windows または Linux 上のファイル共有にマウントするためのコマンドをコピーして、Azure VM またはオンプレミス マシンから実行できます。

    ![Windows と Linux のマウント コマンドを示すスクリーンショット](./media/storage-how-to-use-files-portal/use-files-portal-show-mount-commands.png)

**ヒント:**  
マウントのためのストレージ アカウント アクセス キーを確認するには、接続ページの下部の **[このストレージ アカウントのアクセス キーを表示する]** をクリックします。

## <a name="see-also"></a>関連項目
Azure Files の詳細については、次のリンクをご覧ください。

* [FAQ](../storage-files-faq.md)
* [Windows 上でのトラブルシューティング](storage-troubleshoot-windows-file-connection-problems.md)      
* [Linux 上でのトラブルシューティング](storage-troubleshoot-linux-file-connection-problems.md)    
