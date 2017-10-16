---
title: "Azure Files の概要 | Microsoft Docs"
description: "業界標準の SMB プロトコルを使用してクラウド上にネットワーク ファイル共有を作成および使用できるサービス、Azure Files の概要。"
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
ms.openlocfilehash: 5a4a26957c115277e7558c210560777af63d2d0f
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="introduction-to-azure-files"></a>Azure Files の概要
Azure Files は、クラウド上で、業界標準の[サーバー メッセージ ブロック (SMB) プロトコル](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) (別称 Common Internet File System または CIFS) を介してアクセスできる、完全に管理されたファイル共有を提供します。 Azure Files は、クラウドまたはオンプレミスにある Windows、macOS、および Linux に同時にマウントできます。 さらに、データが使用されている場所の近くから高速アクセスするため、Azure File Sync (プレビュー) を使用して、Windows サーバーに Azure File 共有をキャッシュできます。

## <a name="videos"></a>ビデオ
| Azure Files の概要 (27 分) | Azure Files のチュートリアル (5 分)  |
|-|-|
| [![Azure Files の概要に関する動画のスクリーンキャスト - クリックして再生](./media/storage-files-introduction/azure-files-introduction-video-snapshot1.png)](https://www.youtube.com/watch?v=zlrpomv5RLs) | [![Azure Files のチュートリアルのスクリーンキャスト - クリックして再生](./media/storage-files-introduction/azure-files-introduction-video-snapshot2.png)](https://channel9.msdn.com/Blogs/Azure/Azure-File-storage-with-Windows/) |

## <a name="why-azure-files-is-useful"></a>Azure Files の適用ケース
Azure ファイル共有は、以下の作業に使用できます。

* **オンプレミスのファイル サーバーの置換または補完**:  
    Azure Files を使用して、従来のオンプレミス ファイル サーバーまたは NAS デバイスを完全に置き換えたり、補完することができます。 Windows、macOS、Linux などの一般的なオペレーティング システムに、世界中のどこからでも、Azure ファイル共有を簡単にマウントできます。 また、Azure File Sync を使用すれば、Azure のファイル共有を、オンプレミスまたはクラウドにある Windows サーバーにレプリケートすることも可能で、使用されているデータを分散キャッシュしてパフォーマンスを向上できます。

* **アプリケーションの "リフトアンドシフト"**:  
    Azure Files を使用すると、ファイル アプリケーションやユーザー データをファイル共有に保存し、アプリケーションをクラウドに簡単に "リフト アンド シフト" できます。 Azure Files を使用すると、アプリケーションとそのデータの両方が Azure に移動される "従来の" リフト アンド シフト シナリオと、アプリケーション データは Azure Files に移動され、アプリケーションはオンプレミスで実行を継続する、"ハイブリッド" リフト アンド シフト シナリオの両方に対応できます。 

* **クラウド開発の簡略化**:  
    また、Azure Files は、新しいクラウド開発プロジェクトを簡素化するためにさまざまな方法で使用できます。 For example:
    * **共有アプリケーションの設定**:  
        分散アプリケーションの一般的なパターンでは、複数のアプリケーション インスタンスからアクセスできる集中管理された場所に構成ファイルが配置されます。 アプリケーション インスタンスは File REST API を介してその構成を読み込むことができ、ユーザーは、必要に応じて、ローカルで SMB 共有をマウントすることでそれにアクセスできます。

    * **診断の共有**:  
        Azure のファイル共有は、クラウド アプリケーションが、ログ、メトリック、およびクラッシュ ダンプを書き込める便利な場所です。 ログは、File REST API を通してアプリケーション インスタンスによって書き込まれ、開発者は、ローカル コンピューターにファイル共有をマウントすることによってログにアクセスできます。 これにより、柔軟性が向上するため、開発者は、使い慣れた既存のツールを引き続き使用して、クラウド開発を行えます。

    * **開発/テスト/デバッグ**:  
        クラウド内の VM で作業している開発者または管理者には、一連のツールまたはユーティリティが必要になることがよくあります。 このようなユーティリティやツールを各 VM にコピーする作業には時間がかかります。 ローカルから VM に Azure ファイル共有をマウントすることによって、開発者や管理者はユーティリティやツールにすばやくアクセスでき、コピーする必要がなくなります。

## <a name="key-benefits"></a>主な利点
* **共有アクセス**。 Azure ファイル共有では、業界標準の SMB プロトコルがサポートされています。そのため、アプリケーションの互換性を気にせずに、オンプレミスのファイル共有を Azure ファイル共有にシームレスに置き換えることができます。 複数のマシン、アプリケーション/インスタンスでファイル システムを共有できる Azure Files は、共有性を必要とするアプリケーションにとって、大きな利点になります。 
* **完全管理型**。 Azure ファイル共有は、ハードウェアまたは OS を管理することなく、作成できます。 つまり、重要なセキュリティ アップグレードの際にサーバー OS に修正プログラムを適用したり、故障したハード ディスクを交換したりする作業は必要ありません。
* **スクリプトとツール**。 PowerShell コマンドレットと Azure CLI を使用して、Azure アプリケーションの管理の一環として Azure ファイル共有を作成、マウント、管理することができます。Azure ファイル共有の作成と管理には、Azure ポータルと Azure ストレージ エクスプローラーを使用できます。 
* **回復性**。 Azure Files は、当初から、常に使用できることを目的にして構築されています。 オンプレミスのファイル共有を Azure Files に置き換えることで、ローカルの停電またはネットワークの問題に対処するために夜間に起き出す必要がなくなります。 
* **慣れているプログラミング方法**。 Azure で実行されているアプリケーションは、ファイル [システム I/O API](https://msdn.microsoft.com/library/system.io.file.aspx) を介して共有内のデータにアクセスできます。 そのため、開発者は、既存のコードとスキルを活用して、既存のアプリケーションを移行することができます。 システム IO API の他に、[Azure Storage クライアント ライブラリ](https://msdn.microsoft.com/library/azure/dn261237.aspx)または [Azure Storage REST API](/rest/api/storageservices/file-service-rest-api) も使用できます。

## <a name="next-steps"></a>次のステップ
* [Azure ファイル共有の作成](storage-how-to-create-file-share.md)
* [Windows での接続とマウント](storage-how-to-use-files-windows.md)
* [Linux での接続とマウント](storage-how-to-use-files-linux.md)
* [macOS での接続とマウント](storage-how-to-use-files-mac.md)
