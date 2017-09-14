---
title: "Azure File Storage の概要 | Microsoft Docs"
description: "Microsoft Cloud でネットワーク ファイル共有を提供する Azure File Storage の概要"
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
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 498af5cffb76e026c9a87127cab238f0f23b668a
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---

# <a name="introduction-to-azure-file-storage"></a>Azure File Storage の概要

Azure File Storage は、業界標準の[サーバー メッセージ ブロック (SMB) プロトコル](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)と [共通インターネット ファイル システム (CIFS)](https://technet.microsoft.com/library/cc939973.aspx) を使用して、クラウド内のネットワーク ファイル共有を提供します。 Azure ファイル共有には、Azure Virtual Machines と、Windows、macOS、または Linux を実行しているオンプレミスのデプロイが同時にマウントできます。 汎用ストレージ アカウントでは、Azure File Storage、Azure Blob Storage、および Azure Queue Storage にアクセスできます。

## <a name="videos"></a>ビデオ
| Azure File Storage の概要 (27 分) | Azure File Storage のチュートリアル (5 分)  |
|-|-|
| [![Azure File Storage の概要に関する動画のスクリーンキャスト - クリックして再生](./media/storage-files-introduction/azure-files-introduction-video-snapshot1.png)](https://www.youtube.com/watch?v=zlrpomv5RLs) | [![Azure File Storage のチュートリアルのスクリーンキャスト - クリックして再生](./media/storage-files-introduction/azure-files-introduction-video-snapshot2.png)](https://channel9.msdn.com/Blogs/Azure/Azure-File-storage-with-Windows/) |

## <a name="why-azure-file-storage-is-useful"></a>Azure File Storage が便利な理由

Azure File Storage を使用すると、オンプレミスまたはクラウドでホストされている Windows Server、Linux、または NAS ベースのファイル サーバーを、OS に依存しないクラウド ファイル共有に置き換えることができます。 Azure File Storage には、次の利点があります。

* **共有アクセス** Azure File Storage では、業界標準の SMB プロトコルがサポートされています。そのため、アプリケーションの互換性を気にせずに、オンプレミスのファイル共有を Azure ファイル共有にシームレスに置き換えることができます。 複数のマシンおよびアプリケーション/インスタンスからファイル共有にアクセスできることは、Azure File Storage の大きな利点です。

* **完全管理** Azure ファイル共有は、ハードウェアや OS を管理しなくても作成できます。つまり、重要なセキュリティ アップグレードのためにサーバー OS に修正プログラムを適用したり、故障したハード ディスクを交換したりする必要がありません。

* **スクリプトとツール** PowerShell コマンドレットと Azure CLI を使用すると、Azure アプリケーションの管理の一部として Azure ファイル共有を作成、マウント、管理することができます。 [Azure Portal](https://portal.azure.com) と [Azure ストレージ エクスプローラー](https://storageexplorer.com)を使用して、Azure ファイル共有を作成および管理することができます。 

* **回復性** Azure File Storage は、当初から、常に使用できることを目的にして構築されています。 オンプレミスのファイル共有を Azure File Storage に置き換えることで、ローカルの停電またはネットワークの問題に対処するために夜間に起き出す必要がなくなります。 

* **慣れているプログラミング方法** Azure で実行されているアプリケーションは、[ファイル システム I/O API](https://msdn.microsoft.com/library/system.io.file.aspx) を介して共有内のデータにアクセスできます。 そのため、開発者は、既存のコードとスキルを活用して、既存のアプリケーションを移行することができます。 システム IO API の他に、任意の Azure Storage クライアント ライブラリ ([.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet) 用のものなど) と、[Azure Storage REST API](/rest/api/storageservices/file-service-rest-api) も使用することができます。

Azure ファイル共有は、以下の作業に使用できます。

* **オンプレミスのファイル サーバーの置換** Azure File Storage を使用して、従来のオンプレミス ファイル サーバーまたは NAS デバイス上のファイル共有を完全に置き換えることができます。 Windows、macOS、Linux などの一般的なオペレーティング システムは、世界中のどこからでも、Azure ファイル共有を簡単にマウントできます。

* **アプリケーションの "リフトアンドシフト"**

    Azure File Storage を使用すると、アプリケーションの部分間でデータを共有するためにオンプレミスのファイル共有を使用するアプリケーションをクラウドに簡単に "リフトアンドシフト" することができます。 それを実装するために、各 VM がファイル共有に接続し、オンプレミスのファイル共有の場合と同様にファイルを読み書きできるようになります。

* **クラウド開発の簡略化**
    
    Azure File Storage は、新しいクラウド開発プロジェクトを簡略化するために、さまざまな方法で使用することができます。
    
    * **共有アプリケーションの設定**
    
        分散アプリケーションの一般的なパターンでは、多数の異なる VM からアクセスできる集中管理された場所に構成ファイルが配置されます。 このような構成ファイルを Azure ファイル共有に格納し、すべてのアプリケーション インスタンスで読み取ることができるようになっています。 また、REST インターフェイスを介してこれらの設定を管理することもできるため、世界中から構成ファイルにアクセスできます。

    * **診断の共有**
    
        Azure ファイル共有は、ログ、メトリック、クラッシュ ダンプのような診断ファイルを保存するために使用することもできます。 SMB と REST インターフェイスの両方を介してファイル共有を使用できるようにすることで、アプリケーションは各種分析ツールを構築または利用して、診断データを処理および分析できます。

    * **開発/テスト/デバッグ**

        クラウド内の VM で作業している開発者または管理者には、一連のツールまたはユーティリティが必要になることがよくあります。 各仮想マシンに必要なユーティリティをインストールおよび配信するのは、時間がかかる作業になる場合があります。 Azure File Storage を使用すると、開発者または管理者は好みのツールをファイル共有に格納し、任意の仮想マシンから簡単に接続できます。
        
## <a name="how-does-it-work"></a>それはどのように機能しますか?

Azure ファイル共有の管理は、オンプレミスのファイル共有の管理よりずっと簡単です。 次の図は、Azure File Storage の管理の構造を示しています。

![ファイル構造](./media/storage-files-introduction/files-concepts.png)

* **ストレージ アカウント** Azure Storage にアクセスするときは必ずストレージ アカウントを使用します。 ストレージ アカウントの容量の詳細については、[拡張性とパフォーマンスのターゲット](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)に関するページを参照してください。

* **共有** File Storage 共有は、Azure 内の SMB ファイル共有です。 ディレクトリとファイルはすべて親の共有に作成する必要があります。 アカウントに含まれる共有の数と、共有に格納できるファイル数には制限がなく、ファイル共有の合計容量 5 TB まで増やすことができます。

* **ディレクトリ** ディレクトリの階層 (オプション)。

* **ファイル** 共有内のファイル。 ファイルのサイズの上限は 1 TB です。

* **URL 形式** ファイルは、次の URL 形式を使用してアドレス指定できます。  

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory/directories>/<file>
    ```

## <a name="next-steps"></a>次のステップ

* [Azure ファイル共有の作成](storage-how-to-create-file-share.md)
* [Windows での接続とマウント](storage-how-to-use-files-windows.md)
* [Linux での接続とマウント](storage-how-to-use-files-linux.md)
* [macOS での接続とマウント](storage-how-to-use-files-mac.md)
* [FAQ](../storage-files-faq.md)
* [Windows 上でのトラブルシューティング](storage-troubleshoot-windows-file-connection-problems.md)   
* [Linux 上でのトラブルシューティング](storage-troubleshoot-linux-file-connection-problems.md)   

<!-- Rena I would remove any articles from here that are more than a year old. - Robin-->
### <a name="conceptual-articles-and-videos"></a>概念に関する記事とビデオ
* [Azure File Storage: Windows および Linux 用の円滑なクラウド SMB ファイル システム](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)

### <a name="tooling-support-for-azure-file-storage"></a>Azure File Storage 用のツールのサポート
* [Microsoft Azure Storage で AzCopy を使用する方法](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Azure Storage での Azure CLI の使用](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)

### <a name="blog-posts"></a>ブログ記事
* [Azure File Storage の一般提供開始](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure File storage (Azure File Storage の内部)](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Microsoft Azure File サービスの概要](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Azure Files へのデータの移行](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>リファレンス
* [.NET 用ストレージ クライアント ライブラリ リファレンス](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [File サービスの REST API リファレンス](http://msdn.microsoft.com/library/azure/dn167006.aspx)

