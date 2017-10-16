---
title: "Azure Files についてよく寄せられる質問 | Microsoft Docs"
description: "Azure Files についてよく寄せられる質問とその回答を紹介します。"
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
ms.date: 09/19/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: eb0b4d7cde568ab1809daa8025120828e75f5d76
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---
# <a name="frequently-asked-questions-about-azure-files"></a>Azure Files に関してよく寄せられる質問

## <a name="general"></a>全般
* **Q.Azure Files とは**  
   
    Azure Files は、Azure での分散ファイル システムです。 ユーザーが、サポート対象の Azure Virtual Machine またはオンプレミスのコンピューターでストレージをネイティブ共有としてマウントできるようにする SMB プロトコル インターフェイスを提供します。

* **Q.Azure Files はどのように便利なのですか?**  
   
    Azure Files は、複数の VM やプラットフォームにわたって共有データ アクセスを提供します。 「[Azure Files が便利な理由](storage-files-introduction.md#why-azure-files-is-useful)」を参照してください。

* **Q.Azure File、Azure Blob、Azure Disk は、それぞれどのような場合に使用する必要があるでしょうか。**  
   
    Microsoft Azure では、クラウドでデータの格納とアクセスを行うためにいくつかの方法を提供しています。  
   
    Azure Files - SMB インターフェイス、クライアント ライブラリ、および格納されているファイルにどこからでも容易にアクセスできる REST インターフェイスを備えています。  
   
    Azure Blob - クライアント ライブラリと、大規模な非構造化データをブロック BLOB に格納してアクセスできる REST インターフェイスを備えています。  
   
    Azure データ ディスク - クライアント ライブラリと、アタッチされた仮想ハード ディスクにデータを永続的に格納し、アクセスできる REST インターフェイスを備えています。  
   
    詳細については、「[Azure BLOB、Azure Files、Azure データ ディスクの使い分け](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」をご覧ください。

* **Q.どのようにすれば Azure Files の使用を開始できますか?**  
   
    Azure のファイル共有を作成することで開始できます。 Azure File 共有は、Azure Portal、Azure Storage PowerShell コマンドレット、Azure Storage クライアント ライブラリ、または Azure Storage REST API を使用して作成することができます。このチュートリアルでは、以下のことを学習します。

    * [Azure Portal を使用して Azure File 共有を作成する方法](storage-how-to-use-files-portal.md)
    * [PowerShell を使用して Azure File 共有を作成する方法](storage-how-to-create-file-share.md#create-file-share-through-powershell)
    * [CLI を使用して Azure File 共有を作成する方法](storage-how-to-create-file-share.md#create-file-share-through-command-line-interface-cli)

* **Q.Azure Files では、どのようなレプリケーションがサポートされていますか?**  
   
    Azure Files で現在サポートされているのは LRS と GRS のみです。 RA-GRS もサポート予定ですが、まだ予定は公表できません。
    
## <a name="scale-targetslimits"></a>ターゲット/制限のスケール

* **Q.同じファイルに同時にアクセスできるクライアントの数はどのくらいですか?**

    1 つのファイルに 2000 のオープン ハンドル クォータがあります。 2000 のオープン ハンドルを作成したら、クォータに達したことを示すエラーが表示されます。

## <a name="security-authentication-and-access-control"></a>セキュリティ、認証、およびアクセス制御

* **Q.Azure Files でファイルにアクセスするさまざまな方法を挙げてください。**
    
    SMB 3.0 プロトコルを使用してローカル コンピューターにファイル共有をマウントするか、[ストレージ エクスプローラー](http://storageexplorer.com/)などのツールを使用してファイル共有内のファイルにアクセスできます。 アプリケーションから、ストレージ クライアント ライブラリ、REST API、または Powershell を使用して、Azure File 共有内のファイルにアクセスできます。

* **Q.Web ブラウザーを使用して特定のファイルにアクセスできるようにするにはどうすればよいですか?**
    
    SAS を使用すると、指定した期間中有効な特定のアクセス許可を持つトークンを生成できます。 たとえば、特定の期間に特定のファイルに対する読み取り専用アクセス権だけを持つトークンを生成できます。 この URL を持つユーザーは、それが有効な間、任意の Web ブラウザーから直接ファイルにアクセスできます。 SAS キーは、ストレージ エクスプローラーなどの UI から簡単に生成できます。

* **Q.共有内のフォルダーに読み取り専用または書き込み専用の権限を指定できますか。**
    
    SMB を使用してファイル共有をマウントした場合、このレベルでアクセス許可を制御することはできません。 ただし、REST API またはクライアント ライブラリを使用して Shared Access Signature (SAS) を作成することでこれを実現することができます。  

* **Q.Azure Files のサーバー側暗号化を有効にするにはどうすればよいですか?**

    Azure Files の[サーバー側暗号化](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)は、すべてのリージョン、パブリック クラウド、国内クラウドで一般に使用できるようになっています。 [Azure Portal](https://portal.azure.com/)、[Microsoft Azure Storage リソース プロバイダー API](/rest/api/storagerp/storageaccounts)、[Azure Powershell](https://msdn.microsoft.com/library/azure/mt607151.aspx)、または [Azure CLI](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) を使用して、Azure Files の SSE を有効にすることができます。
    
    Azure Files で SSE を有効にすると、そのストレージ アカウント内のファイル ストレージに書き込まれる新しいデータはすべて自動的に暗号化されます。 この機能は、既存または新規のストレージ アカウント内の既存または新規の共有に書き込まれるすべての新しいデータに使用できます。 この機能の有効化については、追加料金は発生しません。 詳細については、[Azure Files で SSE を有効にする方法](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)に関する記事を参照してください。

* **Q.Azure Files では、Active Directory ベースの認証はサポートされていますか?**
   
    現在、AD ベースの認証や、ACL はサポートしていませんが、今後サポートする可能性はあります。 現時点では、ファイル共有の認証には、Azure Storage のアカウント キーを使用しています。 REST API またはクライアント ライブラリを使用した Shared Access Signature (SAS) での回避策を提供しています。 SAS を使用すると、指定した期間中有効な特定のアクセス許可を持つトークンを生成できます。 たとえば、特定のファイルへの読み取り専用のアクセス許可を持ち、有効期限が 10 分間であるトークンを生成できます。 このトークンを所有するすべてのユーザーは、トークンが有効な間、そのファイルへの 10 分間の読み取り専用アクセスを持ちます。
   
    SAS は、REST API またはクライアント ライブラリを使用してのみサポートされます。 SMB プロトコルを使用してファイル共有をマウントした場合、SAS を使用してそのコンテンツへのアクセスを委任することはできません。 
    
* **Q.Azure Files ではどのようなデータ コンプライアンス ポリシーがサポートされていますか?**

   Azure Files は、Azure Storage の他のストレージ サービスと同じストレージ アーキテクチャ上で実行され、同じデータ コンプライアンス ポリシーを適用します。 Azure Storage のデータ コンプライアンスの詳細については、[Microsoft Azure のデータ保護に関するドキュメント](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409)をダウンロードして参照することができます。

## <a name="on-premises-access"></a>オンプレミスのアクセス

* **Q. オンプレミスの VM から Azure Files に接続するために、Azure ExpressRoute を使用する必要がありますか?**
   
    いいえ。 ExpressRoute がない場合も、インターネット アクセス用にポート 445 (TCP 送信) が開いている場合は、オンプレミスからファイル共有にアクセスできます。 ただし、必要であれば Azure Files で ExpressRoute を使用できます。

* **Q.ローカル コンピューターに Azure File 共有をマウントするにはどうすればよいですか。** 
    
    ポート 445 (TCP 送信) が開いており、クライアントが SMB 3.0 プロトコルをサポートしている (たとえば、Windows 10 や Windows Server 2012 を使用している) 場合は、SMB プロトコル経由でファイル共有をマウントできます。 ポートのブロックを解除するには、ローカルの ISP プロバイダーを利用してください。 その間、[ストレージ エクスプローラー](../../vs-azure-tools-storage-explorer-files.md#view-a-file-shares-contents)を使用してファイルを表示できます。


## <a name="billing-and-pricing"></a>課金と価格
* **Q.Azure VM とファイル共有間のネットワーク トラフィックは、サブスクリプションに課金される外部帯域幅としてカウントされますか。**
   
    ファイル共有と VM が同じ Azure リージョン内にある場合、それらの間のトラフィックは無料です。 それらが異なるリージョンに置かれている場合、それらの間のトラフィックは外部帯域幅として課金されます。

## <a name="backup"></a>バックアップ

* **Q.Azure File 共有はどのようにバックアップしますか。**
    
    AzCopy、RoboCopy、またはマウントされているファイル共有をバックアップできるサードパーティー製のバックアップ ツールを使用できます。 近い将来、ファイル共有のスナップショットを作成する機能が用意される予定です。この機能を使用して、Azure File 共有をバックアップできるようになります。

## <a name="performance"></a>パフォーマンス

* **Q.Azure Files のスケールの上限を教えてください。**
    Azure Files のスケーラビリティおよびパフォーマンスのターゲットの詳細については、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#scalability-targets-for-blobs-queues-tables-and-files)」を参照してください。

* **Q.Azure Files にファイルを解凍する際にパフォーマンスが低かった場合は、どうすればよいですか。**
    
    Azure Files に大量のファイルを転送する場合、ネットワーク転送に最適化されている AzCopy (Windows 用、Linux/Unix 用はプレビュー) または Azure Powershell を使用することをお勧めします。

* **Q.Azure Files の低パフォーマンスの問題を解決するため、どのような修正プログラムがリリースされていますか?**
    
    Windows チームは先日、ユーザーが Windows 8.1 または Windows Server 2012 R2 から Azure Files にアクセスする際に生じる低パフォーマンスの問題を解決するための修正プログラムをリリースしました。 詳細については、関連するサポート技術情報の記事「[Slow performance when you access Azure Files from Windows 8.1 or Server 2012 R2 (Windows 8.1 または Server 2012 R2 から Azure Files にアクセスするときにパフォーマンスが低下する)](https://support.microsoft.com/kb/3114025)」を参照してください。

## <a name="features-and-interoperability-with-other-services"></a>機能およびその他のサービスとの相互運用性
* **Q.フェールオーバー クラスターの "ファイル共有監視" は、Azure Files の用途の 1 つですか?**
   
    これは現在サポートされていません。

* **Q.REST API に名前変更操作はありますか。**
   
    現時点ではありません。

* **Q.共有は入れ子にできますか。つまり共有の下に共有を配置できますか。**
    
    いいえ。 ファイル共有はマウント可能な仮想ドライバーであるため、共有の入れ子はサポートしていません。

* **Q.IBM MQ での Azure Files の使用**
    
    IBM は、IBM MQ ユーザーが IBM のサービスで Azure Files を構成する際にガイドとなるドキュメントを公開しました。 詳細については、「 [How to setup IBM MQ Multi instance queue manager with Microsoft Azure File Service (IBM MQ マルチ インスタンス キュー マネージャーを Microsoft Azure File サービスでセットアップする方法)](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)」を参照してください。


## <a name="troubleshooting"></a>トラブルシューティング
* **Q.Azure Files のエラーのトラブルシューティングを行うにはどうすればよいですか?**
    
    エンド ツー エンドのトラブルシューティング ガイダンスについては、[Azure File のトラブルシューティングに関する記事](storage-troubleshoot-windows-file-connection-problems.md)を参照してください。 


## <a name="see-also"></a>関連項目
Azure Files の詳細については、次のリンクを参照してください。

### <a name="conceptual-articles-and-videos"></a>概念に関する記事とビデオ
* [Azure Files: Windows および Linux 用の円滑なクラウド SMB ファイル システム](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Linux で Azure Files を使用する方法](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>File Storage 用のツールのサポート
* [Microsoft Azure Storage で AzCopy を使用する方法](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Azure Storage での Azure CLI の使用](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Azure Files に関する問題のトラブルシューティング](storage-troubleshoot-linux-file-connection-problems.md)

### <a name="blog-posts"></a>ブログ記事
* [Azure Files が一般公開されました](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Azure Files の内部](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Microsoft Azure File サービスの概要](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Azure Files へのデータの移行](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>リファレンス
* [.NET 用ストレージ クライアント ライブラリ リファレンス](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [File サービスの REST API リファレンス](http://msdn.microsoft.com/library/azure/dn167006.aspx)

