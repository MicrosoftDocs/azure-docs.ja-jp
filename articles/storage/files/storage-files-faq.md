---
title: Azure Files についてよく寄せられる質問 (FAQ) | Microsoft Docs
description: Azure Files についてよく寄せられる質問への回答を紹介します。 Azure ファイル共有は、クラウドまたはオンプレミスの Windows、Linux、macOS デプロイで同時にマウントできます。
author: roygara
ms.service: storage
ms.date: 02/23/2020
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 81cabe8dea178b2988039640065cb0eabc3287af
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103470895"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Azure Files に関してよく寄せられる質問 (FAQ)
[Azure Files](storage-files-introduction.md) では、業界標準の[サーバー メッセージ ブロック (SMB) プロトコル](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)および[ネットワーク ファイル システム (NFS) プロトコル](https://en.wikipedia.org/wiki/Network_File_System) (プレビュー) を介してアクセスできる、フル マネージドのファイル共有がクラウド上で提供されます。 Azure ファイル共有は、クラウドまたはオンプレミスにデプロイされた Windows、Linux、macOS で同時にマウントできます。 また、データが使用される場所に近接した Windows Server マシンに、Azure File Sync で Azure ファイル共有をキャッシュすることによって、高速なアクセスを実現することもできます。

この記事では、Azure Files での Azure File Sync の使用を含め、Azure Files の機能についてよく寄せられる質問にお答えします。 ご質問に対する回答がここで見つからない場合は、次のチャネルでお問い合わせください (上から順に)。

1. この記事のコメント セクション。
2. [Azure Storage に関する Microsoft Q&A 質問ページ](/answers/topics/azure-file-storage.html)。
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)。 
4. Microsoft サポート。 新しいサポート要求を作成するには、Azure Portal の **[ヘルプ]** タブで、 **[ヘルプとサポート]** ボタンを選択し、 **[新しいサポート要求]** を選択します。

## <a name="general"></a>全般
* <a id="why-files-useful"></a>
  **Azure Files にはどのような利点がありますか。**  
   Azure Files を使用すると、物理サーバー、デバイス、アプライアンスに伴うオーバーヘッドの管理に縛られることなく、クラウドにファイル共有を作成することができます。 OS 更新プログラムの適用、不良ディスクの交換など、単調な作業は Microsoft が代わって行います。 Azure Files を利用できるシナリオの詳細については、「[Azure Files の適用ケース](storage-files-introduction.md#why-azure-files-is-useful)」をご覧ください。

* <a id="file-access-options"></a>
  **Azure Files でファイルにアクセスするさまざまな方法を挙げてください。**  
    SMB ファイル共有は、SMB 3.0 プロトコルを使用してローカル マシンにマウントできます。また、[Storage Explorer](https://storageexplorer.com/) などのツールを使用してファイル共有内のファイルにアクセスすることもできます。 NFS ファイル共有は、Azure portal によって提供されるスクリプトをコピーおよび貼り付けすることで、ローカル マシンにマウントできます。 アプリケーションから、ストレージ クライアント ライブラリ、REST API、PowerShell、または Azure CLI を使用して、Azure ファイル共有内のファイルにアクセスできます。

* <a id="what-is-afs"></a>
  **Azure File Sync とは何ですか。**  
    Azure File Sync を使用すると、オンプレミスのファイル サーバーの柔軟性、パフォーマンス、互換性を維持したまま Azure Files で組織のファイル共有を一元化できます。 Azure File Sync により、ご利用の Windows Server マシンが Azure ファイル共有の高速キャッシュに変わります。 SMB、ネットワーク ファイル システム (NFS)、ファイル転送プロトコル サービス (FTPS) など、Windows Server 上で利用できるあらゆるプロトコルを使用して、データにローカルでアクセスすることができます。 キャッシュは、世界中にいくつでも必要に応じて設置することができます。

* <a id="files-versus-blobs"></a>
  **データに Azure Blob Storage ではなく Azure ファイル共有を使用する理由を教えてください。**  
    Azure Files と Azure Blob Storage はどちらも、クラウドに大量のデータを保存する手段として利用できますが、その用途にやや違いがあります。 
    
    Azure Blob Storage は、構造化されていないデータを格納する必要がある大規模なクラウド ネイティブ アプリケーションに役立ちます。 パフォーマンスとスケールを最大限に高めるため、Azure Blob Storage は通常のファイル システムよりも単純なストレージの抽象化です。 Azure Blob Storage には必ず、REST ベースのクライアント ライブラリ経由で (または REST ベースのプロトコルを介して直接) アクセスする必要があります。

    Azure Files は、明確にファイル システムです。 Azure Files には、オンプレミスのオペレーティング システムで長年使われてきた、皆さんもおなじみのファイルの概念がすべて備わっています。 Azure Blob Storage と同様、Azure Files にも REST インターフェイスと REST ベースのクライアント ライブラリが用意されています。 Azure Blob Storage とは異なり、Azure Files では Azure ファイル共有に SMB または NFS でアクセスできます。 ファイル共有は、オンプレミスの VM とクラウドの VM のどちらであっても、Windows、Linux、macOS 上で直接マウントできます。コードを記述したり、特別なドライバーをファイル システムにアタッチする必要はありません。 また、Azure File Sync を使用して、データが使用される場所に近接したオンプレミスのファイル サーバーで Azure SMB ファイル共有をキャッシュすることによって、高速なアクセスを実現することもできます。 
   
    Azure Files と Azure BLOB ストレージの違いに関する詳細な説明については、「[コア Azure Storage サービスの概要](../common/storage-introduction.md)」を参照してください。 Azure Blob Storage の詳細については、「[Blob Storage の概要](../blobs/storage-blobs-introduction.md)」をご覧ください。

* <a id="files-versus-disks"></a>**Azure Disks ではなく Azure ファイル共有を使用する理由を教えてください。**  
    Azure Disks におけるディスクは、ただのディスクに過ぎません。 Azure Disks を有効活用するには、Azure で実行されている仮想マシンにディスクをアタッチする必要があります。 Azure Disks は、オンプレミスのサーバーで使われるディスクとまったく同じ用途で使用することができます。 OS システム ディスクや OS のスワップ領域、アプリケーションの専用ストレージとして使用可能です。 Azure ファイル共有を使用する代わりに Azure Disks を使用してクラウドにファイル サーバーを作成するのも面白い使い方です。 Azure Files では現在サポートされていないデプロイ オプションを必要とする場合、Azure Virtual Machines にファイル サーバーをデプロイすれば、Azure に高パフォーマンスなファイル ストレージを設置できます。 

    ただし、Azure Disks をバックエンド ストレージとしたファイル サーバーを実行すると、いくつかの理由から、通常は Azure ファイル共有を使用するよりもはるかに高額になります。 第一に、ディスク ストレージの料金を支払うだけでなく、1 つまたは複数の Azure VM を実行する費用を支払う必要があります。 第二に、ファイル サーバーの実行に使用される VM を管理する必要もあります。 たとえば OS のアップグレードを自分で行う必要があります。 最後に、オンプレミスでデータをキャッシュする必要が最終的に生じた場合、分散ファイル システム レプリケーション (DFSR) などのレプリケーション テクノロジをユーザーの判断で設定して管理し、キャッシュを行う必要があります。

    (Azure Disks をバックエンド ストレージとして使用することに加え) Azure Virtual Machines にホストされたファイル サーバーと Azure Files の両方の利点を活かす方法の 1 つは、クラウド VM 上でホストされているファイル サーバーに Azure File Sync をインストールすることです。 Azure ファイル共有がお使いのファイル サーバーと同じリージョンにある場合、クラウドの階層化を有効にして、ボリュームの空き領域を最大 (99%) に設定できます。 これによりデータの重複を最小限に抑えることができます。 また、NFS プロトコル サポートを必要とするアプリケーションなど、どのようなアプリケーションでも必要に応じてファイル サーバーで使用することができます。

    Azure で高パフォーマンスで高可用なファイル サーバーを設定する方法については、「[Deploying IaaS VM Guest Clusters in Microsoft Azure (Microsoft Azure に IaaS VM ゲスト クラスターをデプロイする)](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/)」をご覧ください。 Azure Files と Azure ディスクの違いに関する詳細な説明については、「[コア Azure Storage サービスの概要](../common/storage-introduction.md)」を参照してください。 Azure ディスクの詳細については、「[Azure Managed Disks の概要](../../virtual-machines/managed-disks-overview.md)」をご覧ください。

* <a id="get-started"></a>
  **Azure Files を使い始めるにはどうしたらよいですか。**  
   Azure Files は簡単に使い始めることができます。 まず、[SMB ファイル共有を作成する](storage-how-to-create-file-share.md)か、[NFS 共有を作成する方法](storage-files-how-to-create-nfs-shares.md)を参照して、共有を作成してから、ご希望のオペレーティング システムでマウントします。 

  * [Windows で SMB 共有をマウントする](storage-how-to-use-files-windows.md)
  * [Linux で SMB 共有をマウントする](storage-how-to-use-files-linux.md)
  * [macOS で SMB 共有をマウントする](storage-how-to-use-files-mac.md)
  * [NFS ファイル共有をマウントする](storage-files-how-to-mount-nfs-shares.md)

    Azure ファイル共有をデプロイして組織内の運用ファイル共有を置き換える方法についての詳しいガイドは、「[Azure Files のデプロイの計画](storage-files-planning.md)」をご覧ください。

* <a id="redundancy-options"></a>
  **Azure Files では、どのようなストレージ冗長性オプションがサポートされていますか。**  
    現在、Azure Files では、ローカル冗長ストレージ (LRS)、ゾーン冗長ストレージ (ZRS)、geo 冗長ストレージ (GRS)、および geo ゾーン冗長ストレージ (GZRS) がサポートされています。 Azure Files Premium レベルでは現在、LRS と ZRS のみがサポートされています。

* <a id="tier-options"></a>
  **Azure Files では、どのストレージ層がサポートされていますか。**  
    Azure Files は、Premium と Standard という 2 つのストレージ層をサポートしています。 Standard ファイル共有は汎用 (GPv1 または GPv2) ストレージ アカウントで作成され、Premium ファイル共有は FileStorage ストレージ アカウントで作成されます。 [Standard ファイル共有](storage-how-to-create-file-share.md)と[Premium ファイル共有](./storage-how-to-create-file-share.md)の作成方法を参照してください。 
    
    > [!NOTE]
    > BLOB ストレージ アカウントまたは *Premium* 汎用 (GPv1 または GPv2) ストレージ アカウントから Azure ファイル共有を作成することはできません。 Standard Azure ファイル共有は *Standard* 汎用アカウントでのみ作成し、Premium Azure ファイル共有は FileStorage ストレージ アカウントでのみ作成する必要があります。 *Premium* 汎用 (GPv1 および GPv2) ストレージ アカウントは、Premium ページ BLOB 専用です。 

* <a id="file-locking"></a>
  **Azure Files ではファイルのロックがサポートされますか?**  
    はい、Azure Files では、SMB/Windows 形式のファイル ロックを完全にサポートしています。詳しくは、[こちら](/rest/api/storageservices/managing-file-locks)をご覧ください。

* <a id="give-us-feedback"></a>
  **Azure Files に追加してほしい機能があります。追加できますか。**  
    Azure Files チームでは、サービスに関するあらゆるフィードバックをお待ちしています。 [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) で機能のリクエストにご投票ください。 多くの新しい機能を皆様に喜んでいただけることを楽しみにしています。

## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
  **Azure File Sync は、どのリージョンでサポートされていますか。**  
    提供されているリージョンの一覧は、Azure File Sync プランニング ガイドの「[利用可能なリージョン](storage-sync-files-planning.md#azure-file-sync-region-availability)」セクションでご覧いただけます。 パブリック リージョン以外のリージョンも含め、今後サポート対象リージョンを拡大していく予定です。

* <a id="cross-domain-sync"></a>
  **同じ同期グループ内にドメイン参加とドメイン非参加のサーバーを保持することはできますか。**  
    はい。 異なる Active Directory に属しているサーバー エンドポイントを 1 つの同期グループに含めることは可能です。ドメインに参加していない場合でも同様です。 この構成は技術的には機能するものの、通常の構成としては、お勧めできません。あるサーバー上のファイルやフォルダー用に定義されたアクセス制御リスト (ACL) が、同期グループ内の他のサーバーでは適用できない可能性があるためです。 最良の結果を得るには、同じ Active Directory フォレスト内にあるサーバー間、別の Active Directory フォレスト内にあるものの信頼関係が確立されているサーバー間、またはドメインに属していないサーバー間で同期することをお勧めします。 これらの構成を混在させることは避けるようにしてください。

* <a id="afs-change-detection"></a>
  **SMB またはポータルで Azure ファイル共有に直接ファイルを作成しました。このファイルが同期グループのサーバーに同期されるまでどのくらいの時間がかかりますか。**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]


* <a id="afs-sync-time"></a>
  **Azure File Sync で 1TiB のデータをアップロードするには、どのくらいの時間がかかりますか?**
  
    パフォーマンスは、環境設定、構成、また、これが初期の同期か継続的な同期かに応じて異なります。詳細については、「[Azure File Sync のパフォーマンス メトリック](storage-files-scale-targets.md#azure-file-sync-performance-metrics)」を参照してください

* <a id="afs-conflict-resolution"></a>**2 つのサーバーで同じファイルがほぼ同時に変更された場合、どうなりますか。**  
    Azure File Sync では、シンプルな競合解決方法が採用されています。ファイルに対して 2 つのエンドポイントで同時に変更を加えた場合、その両方の変更が保持されます。 最後に書き込まれた変更では、元のファイル名が維持されます。 (LastWriteTime によって決定される) 古いファイルには、エンドポイント名と競合番号がファイル名に追加されます。 サーバー エンドポイントの場合、エンドポイント名はサーバーの名前です。 クラウド エンドポイントの場合、エンドポイント名は **Cloud** です。 名前は、次の命名規則に従います。 
   
    \<FileNameWithoutExtension\>-\<endpointName\>\[-#\].\<ext\>  

    たとえば、CompanyReport.docx で競合が生じたとします。古い方の書き込みが CentralServer で行われた場合、最初の競合で生じるファイルの名前は CompanyReport-CentralServer.docx となります。 2 回目の競合では、CompanyReport-CentralServer-1.docx という名前になります。 Azure File Sync は、1 つのファイルにつき 100 個の競合ファイルをサポートします。 競合ファイルの最大数に達すると、競合ファイルの数が 100 個未満になるまで、ファイルは同期されません。

* <a id="afs-storage-redundancy"></a>
  **Azure File Sync では、geo 冗長ストレージはサポートされますか。**  
    はい。Azure Files では、ローカル冗長ストレージ (LRS) と geo 冗長ストレージ (GRS) の両方がサポートされます。 GRS 用に構成されたアカウントから、ペアになったリージョン間でストレージ アカウントのフェールオーバーを開始する場合は、新しいリージョンをデータのみのバックアップとして扱うことをお勧めします。 Azure File Sync は、新しいプライマリ リージョンで自動的に同期を開始することはありません。 

* <a id="sizeondisk-versus-size"></a>
  **ファイルの "*ディスク上のサイズ*" プロパティが、Azure File Sync を使用した後の "*サイズ*" プロパティと一致しないのはどうしてですか。**  
  「[Azure File Sync のクラウドを使った階層化について](storage-sync-cloud-tiering-overview.md#tiered-vs-locally-cached-file-behavior)」を参照してください。

* <a id="is-my-file-tiered"></a>
  **ファイルが階層化されているかどうかは、どうやって判断できますか。**  
  [Azure File Sync の階層化ファイルの管理方法](storage-sync-how-to-manage-tiered-files.md#how-to-check-if-your-files-are-being-tiered)に関する記事を参照してください。

* <a id="afs-recall-file"></a>**使用したいファイルが階層化されています。ローカルで使用するためにこのファイルをディスクに再現するには、どうすればよいですか。**  
  [Azure File Sync の階層化ファイルの管理方法](storage-sync-how-to-manage-tiered-files.md#how-to-recall-a-tiered-file-to-disk)に関する記事を参照してください。

* <a id="afs-force-tiering"></a>
  **ファイルまたはディレクトリを強制的に階層化するには、どうすればよいですか。**  
  [Azure File Sync の階層化ファイルの管理方法](storage-sync-how-to-manage-tiered-files.md#how-to-force-a-file-or-directory-to-be-tiered)に関する記事を参照してください。

* <a id="afs-effective-vfs"></a>
  **ボリューム上に複数のサーバー エンドポイントがある場合、*ボリュームの空き領域* はどのように解釈されますか。**  
  [Azure File Sync のクラウドを使った階層化ポリシーの選択](storage-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume)に関するページを参照してください。
  
* <a id="afs-tiered-files-tiering-disabled"></a>
  **クラウドを使った階層化を無効にしたのですが、サーバー エンドポイントの場所に階層化されたファイルがあるのはなぜでしょうか。**  
    階層化されたファイルがサーバー エンドポイントの場所に存在する理由には、次の 2 つがあります。

    - 新しいサーバー エンドポイントを既存の同期グループに追加するときに、最初に名前空間を呼び戻すオプション、または初期ダウンロード モードで名前空間のみを呼び戻すオプションを選択すると、ファイルはローカルにダウンロードされるまで階層化された状態で表示されます。 これを回避するには、初期ダウンロード モードで、階層化されたファイルを回避するオプションを選択します。 手動でファイルを呼び戻すには、[Invoke-StorageSyncFileRecall](storage-sync-how-to-manage-tiered-files.md#how-to-recall-a-tiered-file-to-disk) コマンドレットを使用します。

    - サーバー エンドポイントでクラウドの階層化が有効になっていて、その後無効になった場合、ファイルはアクセスされるまで階層化されたままになります。

* <a id="afs-tiered-files-not-showing-thumbnails"></a>
  **エクスプローラーで階層化されたファイルのサムネイルやプレビューが表示されないのはなぜですか。**  
    階層化されたファイルの場合、サムネイルとプレビューはサーバー エンドポイントでは表示されません。 Windows のサムネイル キャッシュ機能は、オフライン属性が設定されたファイルの読み取りを意図的にスキップするため、これは予期される動作です。 クラウドを使った階層化が有効になっている場合、階層化されたファイルを読み取ると、それらがダウンロード (呼び戻し) されます。

    この動作は Azure File Sync 固有ではありません。Windows エクスプローラーでは、オフライン属性セットが設定されているすべてのファイルに対して "グレーの X" が表示されます。 この X のアイコンは、SMB 経由でファイルにアクセスすると表示されます。 この動作の詳しい説明については、[https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105) を参照してください。

    階層化されたファイルの管理方法については、[階層化されたファイルの管理方法](storage-sync-how-to-manage-tiered-files.md)に関する記事を参照してください。

* <a id="afs-files-excluded"></a>
  **Azure File Sync によって自動的に除外されるのは、どのファイルまたはフォルダーですか。**  
  「[スキップされるファイル](storage-sync-files-planning.md#files-skipped)」を参照してください。

* <a id="afs-os-support"></a>
  **Windows Server 2008 R2、Linux、または自分のネットワーク接続ストレージ (NAS) デバイスで Azure File Sync を使用することはできますか。**  
    現在、Azure File Sync は Windows Server 2019、Windows Server 2016、および Windows Server 2012 R2 のみをサポートしています。 現時点でお伝えできる他の計画はありませんが、お客様の要望に応じてサポートするプラットフォームを増やしていきたいと考えています。 サポート対象としてご希望のプラットフォームがあれば、[Azure Files の UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) までお寄せください。

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **階層化されたファイルがサーバー エンドポイント名前空間の外部に存在するのはなぜですか。**  
    Azure File Sync エージェント バージョン 3 より前の Azure File Sync は、サーバー エンドポイントと同じボリューム上であってもサーバー エンドポイントの外部に存在する階層化されたファイルの移動をブロックしました。 他のボリュームに対する、コピー操作、階層化されていないファイルの移動、および階層化されたファイルの移動は、影響を受けませんでした。 このような動作の理由は、ファイル エクスプローラーおよび他の Windows の API による同じボリューム上での移動操作は、(ほとんど) 瞬時の名前変更操作であるという、暗黙の仮定によるものでした。 これは、Azure File Sync がクラウドからデータを呼び戻している間、エクスプローラーや他の移動方法 (コマンド ラインや PowerShell など) が応答しないように見えることを意味します。 [Azure File Sync エージェント バージョン 3.0.12.0](storage-files-release-notes.md#supported-versions) 以降の Azure File Sync では、サーバー エンドポイントの外部にある階層化されたファイルを移動できます。 階層化されたファイルがサーバー エンドポイントの外部で階層化されたファイルとして存在できるようにし、バックグラウンドでファイルを呼び戻すことにより、上で説明したような悪影響を防ぎます。 つまり、同じボリューム上での移動は瞬時であり、移動が完了した後で、ファイルをディスクに呼び戻すためのすべての処理を行います。 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **サーバーで Azure File Sync に関する問題 (同期、クラウド階層化など) が発生しています。サーバー エンドポイントを削除して再作成する必要がありますか。**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **ストレージ同期サービスやストレージ アカウントを、別のリソース グループ、サブスクリプション、または Azure AD テナントに移動できますか。**  
   はい。ストレージ同期サービスやストレージ アカウントは、別のリソース グループ、サブスクリプション、または Azure AD テナントに移動できます。 ストレージ同期サービスまたはストレージ アカウントを移動した後、Microsoft.StorageSync アプリケーションにストレージ アカウントへのアクセス権を付与する必要があります (「[Azure File Sync がストレージ アカウントへのアクセス権を持っていることを確認します。](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)」を参照してください)。

    > [!Note]  
    > クラウド エンドポイントを作成するときは、ストレージ同期サービスとストレージ アカウントが同じ Azure AD テナントに存在する必要があります。 クラウド エンドポイントが作成された後、ストレージ同期サービスとストレージ アカウントを別の Azure AD テナントに移動できます。
    
* <a id="afs-ntfs-acls"></a>
  **Azure File Sync では、Azure Files の格納データと共にディレクトリ レベルまたはファイル レベルの NTFS ACL が保持されますか。**

    2020 年2 月 24 日の時点で、Azure File Sync によって階層化された新規および既存の ACL は NTFS 形式で保存され、Azure ファイル共有に直接加えられた ACL の変更は、同期グループ内のすべてのサーバーに同期されます。 Azure Files に対して行われた ACL の変更は、Azure File Sync 経由で同期されます。Azure Files にデータをコピーするときは、必要な "忠実性" をサポートするコピー ツールを使用して、属性、タイムスタンプ、ACL を SMB または REST 経由で Azure ファイル共有に必ずコピーしてください。 AzCopy などの Azure コピー ツールを使用する場合は、最新バージョンを使用することが重要です。 [ファイル コピー ツールの表](storage-files-migration-overview.md#file-copy-tools)をチェックして、Azure コピー ツールの概要を確認し、ファイルの重要なメタデータをすべてコピーできることを確認します。

    File Sync で管理されているファイル共有に対して Azure Backup を有効にした場合、ファイル ACL をバックアップ復元ワークフローの一部として引き続き復元できます。 これは、共有全体または個々のファイル/ディレクトリに対して機能します。

    File Sync によって管理されるファイル共有の自己管理型バックアップ ソリューションの一部としてスナップショットを使用している場合、2020 年 2 月 24 日より前に作成された ACL が NTFS ACL に正しく復元されないことがあります。 この問題が発生した場合は、Azure サポートに連絡することを検討してください。
    
## <a name="security-authentication-and-access-control"></a>セキュリティ、認証、およびアクセス制御
* <a id="ad-support"></a>
**Azure Files では、ID ベースの認証とアクセス制御はサポートされていますか。**  
    
    はい。Azure Files では、ID ベースの認証とアクセス制御がサポートされています。 オンプレミス Active Directory Domain Services または Azure Active Directory Domain Services (Azure AD DS) という 2 つの方法のいずれかを選び、ID ベースのアクセス制御を使用できます。 オンプレミス Active Directory Domain Services (AD DS) は、AD DS ドメインに参加しているマシン (オンプレミスまたは Azure) を使用した認証をサポートし、SMB 経由で Azure ファイル共有にアクセスします。 Azure Files での SMB 経由の Azure AD DS 認証により、Azure AD DS ドメイン参加 Windows VM は Azure AD 資格情報を使用して共有、ディレクトリ、およびファイルにアクセスできるようになります。 詳細については、「[ SMB アクセスに対する Azure Files ID ベース認証サポートの概要](storage-files-active-directory-overview.md)」を参照してください。 

    Azure Files では、その他 2 つの方法でアクセス制御を管理できます。

    - Shared Access Signature (SAS) を使用すると、指定した期間中有効な特定のアクセス許可があるトークンを生成できます。 たとえば、特定のファイルへの読み取り専用のアクセス許可が付与された、有効期限が 10 分間のトークンを生成できます。 このトークンを所有するすべてのユーザーは、トークンが有効な間、そのファイルへの 10 分間の読み取り専用アクセスを持ちます。 Shared Access Signature キーは、REST API 経由またはクライアント ライブラリでしかサポートされていません。 ストレージ アカウント キーを使って SMB 経由で Azure ファイル共有をマウントする必要があります。

    - Azure File Sync は、(Active Directory ベースかローカルかに関係なく) すべての随意 ACL (DACL) を保持し、同期先のすべてのサーバー エンドポイントにレプリケートします。 
    
    Azure Storage サービスでサポートされているすべてのプロトコルの包括的な表記については、「[Azure Storage へのアクセスを承認する](../common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。 
    
* <a id="encryption-at-rest"></a>
**Azure ファイル共有に保存時の暗号化を確保するには、どうすればよいですか。**  

    はい。 詳細については、[Azure Storage Service Encryption](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) に関するページを参照してください。

* <a id="access-via-browser"></a>
**Web ブラウザーを使用して特定のファイルにアクセスできるようにするにはどうすればよいですか。**  

    Shared Access Signature を使用すると、指定した期間中有効な特定のアクセス許可があるトークンを生成できます。 たとえば、特定の期間に特定のファイルに対する読み取り専用アクセス権があるトークンを生成できます。 この URL を持つユーザーは、トークンが有効な間、任意の Web ブラウザーから直接ファイルにアクセスできます。 Shared Access Signature キーは、Storage Explorer などの UI から簡単に生成できます。

* <a id="file-level-permissions"></a>
**共有内のフォルダーに読み取り専用または書き込み専用の権限を指定できますか。**  

    SMB を使用してファイル共有をマウントした場合、フォルダーレベルでアクセス許可を制御することはできません。 ただし、REST API またはクライアント ライブラリを使用して Shared Access Signature を作成すれば、共有内のフォルダーに対して読み取り専用または書き込み専用のアクセス許可を指定することができます。

* <a id="ip-restrictions"></a>
**Azure ファイル共有に IP 制限を実装することはできますか。**  

    はい。 Azure ファイル共有へのアクセスはストレージ アカウント レベルで制限できます。 詳細については、[Azure Storage ファイアウォールおよび Virtual Networks の構成](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)に関する記事を参照してください。

* <a id="data-compliance-policies"></a>
**Azure Files ではどのようなデータ コンプライアンス ポリシーがサポートされていますか。**  

   Azure Files は、Azure Storage 内の他のストレージ サービスと同じストレージ アーキテクチャ上で実行されます。 他の Azure Storage サービスで使用されているデータ コンプライアンス ポリシーが Azure Files でも適用されます。 Azure Storage のデータ コンプライアンスの詳細については、「[Azure Storage のコンプライアンス認証](../common/storage-compliance-offerings.md)」を参照するか、[Microsoft セキュリティ センター](https://microsoft.com/trustcenter/default.aspx)にアクセスできます。

* <a id="file-auditing"></a>
**Azure Files のファイル アクセスと変更を監査するにはどうすればよいですか?**

  次の 2 つのオプションによって、Azure Files の監査機能を使用できます。
  - ユーザーが Azure ファイル共有に直接アクセスしている場合は、[Azure Storage ログ (プレビュー)](../blobs/monitor-blob-storage.md?tabs=azure-powershell#analyzing-logs) を使用して、ファイルの変更とユーザー アクセスを追跡できます。 これらのログはトラブルシューティングの目的で使用でき、要求はベスト エフォートでログに記録されます。
  - Azure File Sync エージェントがインストールされている Windows Server 経由でユーザーが Azure ファイル共有にアクセスしている場合は、[監査ポリシー](/windows/security/threat-protection/auditing/apply-a-basic-audit-policy-on-a-file-or-folder) またはサードパーティ製品を使用して、Windows Server 上のファイルの変更とユーザー アクセスを追跡します。 
   
### <a name="ad-ds--azure-ad-ds-authentication"></a>AD DS および Azure AD DS 認証
* <a id="ad-support-devices"></a>
**Azure Files の Azure Active Directory Domain Services (Azure AD DS) 認証では、Azure AD に参加済みまたは登録済みのデバイスからの Azure AD 資格情報を使用した SMB アクセスはサポートされていますか?**

    いいえ。このシナリオはサポートされていません。

* <a id="ad-vm-subscription"></a>
**異なるサブスクリプションの VM から Azure AD 資格情報を使用して Azure ファイル共有にアクセスすることはできますか?**

    ファイル共有のデプロイ元であるサブスクリプションが、VM のドメイン参加先である Azure AD DS デプロイと同じ Azure AD テナントに関連付けられている場合は、同じ Azure AD 資格情報を使用して Azure ファイル共有にアクセスできます。 制限は、サブスクリプションにではなく、関連付けられている Azure AD テナントに課せられます。
    
* <a id="ad-support-subscription"></a>
**Azure ファイル共有のプライマリ テナントとは異なる Azure AD テナントを使用し、Azure ファイル共有のために Azure AD DS またはオンプレミス AD DS 認証を有効にすることはできますか?**

    いいえ。Azure Files では、ファイル共有と同じサブスクリプションに存在する Azure AD テナントとの Azure AD DS またはオンプレミス AD DS 統合のみがサポートされます。 Azure AD テナントに関連付けることができるサブスクリプションは 1 つだけです。 この制限は、Azure AD DS とオンプレミス AD DS の両方の認証方法に適用されます。 認証にオンプレミス AD DS を使用する場合は、ストレージ アカウントが関連付けられている [Azure AD に AD DS 資格情報を同期する必要があります](../../active-directory/hybrid/how-to-connect-install-roadmap.md)。

* <a id="ad-linux-vms"></a>
**Azure ファイル共有のための Azure AD DS またはオンプレミス AD DS 認証は Linux VM をサポートしていますか?**

    いいえ。Linux VM からの認証はサポートされていません。

* <a id="ad-aad-smb-afs"></a>
**Azure File Sync で管理されているファイル共有では、Azure AD DS またはオンプレミス AD DS 認証がサポートされていますか?**

    はい。Azure File Sync によって管理されるファイル共有で Azure AD DS またはオンプレミス AD 認証を有効にすることができます。ローカル ファイル サーバー上のディレクトリまたはファイルの NTFS ACL への変更は、Azure Files に階層化され、その逆も行われます。

* <a id="ad-aad-smb-files"></a>
**自分のストレージ アカウントで AD DS 認証を有効にしていることを確認し、ドメイン情報を取得するにはどうすればよいですか?**

    手順については、[こちら](./storage-files-identity-ad-ds-enable.md#confirm-the-feature-is-enabled)を参照してください。

* <a id=""></a>
**Azure Files の Azure AD Authentication は Linux VM をサポートしていますか?** 

    いいえ。Linux VM からの認証はサポートされていません。

* <a id="ad-multiple-forest"></a>
**Azure ファイル共有のためのオンプレミス AD DS 認証では、複数のフォレストを使用する AD DS 環境との統合をサポートしていますか?**    

    Azure Files オンプレミス AD DS 認証は、ストレージ アカウントが登録されているドメイン サービスのフォレストにのみ統合されます。 別のフォレストからの認証をサポートするには、環境でフォレストの信頼が正しく構成されている必要があります。 Azure Files を AD DS に登録する方法は、通常のファイル サーバーとほとんど同じです。このサーバーでは、認証のために AD で ID (コンピューターまたはサービスのログオン アカウント) が作成されます。 唯一の違いは、ストレージ アカウントの登録済み SPN が、ドメイン サフィックスと一致しない "file.core.windows.net" で終了することです。 ドメインの管理者に問い合わせて、ドメイン サフィックスが異なることで、複数のフォレスト認証を有効にするためにサフィックス ルーティング ポリシーの更新が必要かどうかを確認してください。 サフィックス ルーティング ポリシーを構成する例を次に示します。
    
    例:フォレスト A ドメインのユーザーが、フォレスト B のドメインに対して登録されたストレージ アカウントを使用してファイル共有にアクセスしたい場合、そのストレージ アカウントのサービス プリンシパルにはフォレスト A 内のどのドメインのサフィックスとも一致するサフィックスがないため、自動的には機能しません。この問題に対処するには、"file.core.windows.net" のカスタム サフィックスに対して、フォレスト A からフォレスト B へのサフィックス ルーティング規則を手動で構成します。
    まず、フォレスト B に新しいカスタム サフィックスを追加する必要があります。構成を変更するための適切な管理者アクセス許可があることを確認してから、次の手順を行います。   
    1. フォレスト B のドメインに参加しているマシンにログオンします
    2.  [Active Directory ドメインと信頼関係] コンソールを開きます
    3.  [Active Directory ドメインと信頼関係] を右クリックします
    4.  [プロパティ] をクリックします
    5.  [追加] をクリックします
    6.  UPN サフィックスとして「file.core.windows.net」を追加します
    7.  [適用] をクリックし、[OK] をクリックしてウィザードを閉じます
    
    次に、フォレスト A にサフィックスのルーティング規則を追加し、フォレスト B にリダイレクトされるようにします。
    1.  フォレスト A のドメインに参加しているマシンにログオンします
    2.  [Active Directory ドメインと信頼関係] コンソールを開きます
    3.  ファイル共有にアクセスするドメインを右クリックし、[信頼] タブをクリックし、出力方向の信頼からフォレスト B のドメインを選択します。 2 つのフォレスト間に信頼を構成していない場合は、最初に信頼を設定する必要があります
    4.  [プロパティ]、[名前サフィックス ルーティング] の順にクリックします
    5.  "*.file.core.windows.net" サフィックスが表示されるかどうかを確認します。 そうでない場合は、[更新] をクリックします
    6.  "*.file.core.windows.net" を選択し、[有効] と [適用] をクリックします

* <a id=""></a>
**Azure Files AD DS 認証はどのリージョンで利用できますか?**

    詳細については、[AD DS のリージョン別の提供状況](storage-files-identity-auth-active-directory-enable.md#regional-availability) を参照してください。
    
* <a id="ad-aad-smb-afs"></a>
**Azure File Sync によって管理されているファイル共有に Azure Files Active Directory (AD) 認証を利用できますか?**

    はい。Azure File Sync によって管理されるファイル共有で AD Authentication を有効にすることができます。ローカル ファイル サーバー上のディレクトリまたはファイルの NTFS ACL への変更は、Azure Files に階層化され、その逆も行われます。

* <a id="ad-aad-smb-files"></a>
**AD で自分のストレージ アカウントを表す場合、コンピューター アカウントまたはサービス ログオン アカウントを作成するかによって違いはありますか?** 

    [コンピューター アカウント](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (既定) または [サービスのログオン アカウント](/windows/win32/ad/about-service-logon-accounts)のいずれを作成しても、認証が Azure Files でどのように機能するかに違いはありません。 ストレージ アカウントをご利用の AD 環境内の ID として表す方法については、自分自身で選択することができます。 Join-AzStorageAccountForAuth コマンドレットの既定の DomainAccountType セットは、コンピューター アカウントです。 ただし、ご利用の AD 環境で構成されているパスワードの有効期限は、コンピューター アカウントまたはサービス ログオン アカウントによって異なる可能性があります。また、[AD でストレージ アカウント ID のパスワードを更新する](./storage-files-identity-ad-ds-update-password.md)ことを考慮する必要があります。
 
* <a id="ad-support-rest-apis"></a>
**ディレクトリまたはファイルの Windows ACL の取得、設定、コピーをサポートする REST API はありますか?**

    はい、[2019-07-07](/rest/api/storageservices/versioning-for-the-azure-storage-services#version-2019-07-07) (またはそれ以降) の REST API を使用する場合は、ディレクトリまたはファイルの NTFS ACL を取得、設定、またはコピーする REST API がサポートされます。 また、Microsoft では、次の REST ベースのツールでの永続的な Windows ACL をサポートしています:[AzCopy v10.4+](https://github.com/Azure/azure-storage-azcopy/releases)。

* <a id="ad-support-rest-apis"></a>
**Azure AD または AD 資格情報を使用して新しい接続を初期化する前に、ストレージ アカウント キーを使用してキャッシュされた資格情報を削除し、既存の SMB 接続を削除する方法**

    次の 2 段階のプロセスに従って、ストレージ アカウント キーに関連付けられている保存済みの資格情報を削除し、SMB 接続を削除できます。 
    1. Windows Cmd.exe で次のコマンドレットを実行して、資格情報を削除します。 資格情報が見つからない場合は、保存されていないという意味なので、この手順は省略できます。
    
       cmdkey /delete:Domain:target=storage-account-name.file.core.windows.net
    
    2. ファイル共有への既存の接続を削除します。 マウント パスは、マウントされたドライブ文字または storage-account-name.file.core.windows.net パスのいずれかとして指定できます。
    
       net use <drive-letter/share-path> /delete

## <a name="network-file-system"></a>ネットワーク ファイル システム

* <a id="when-to-use-nfs"></a>
**Azure Files NFS を使用するタイミングは?**

    [NFS 共有 (プレビュー)](storage-files-compare-protocols.md#nfs-shares-preview) を参照してください。

* <a id="backup-nfs-data"></a>
**NFS 共有に格納されているデータをバックアップする方法は?**

    NFS 共有上のデータのバックアップは、rsync などの使い慣れたツールや、サードパーティのバックアップ パートナーの製品などを使用するように計画できます。 初期プレビューの一部には、[Commvault](https://documentation.commvault.com/commvault/v11/article?p=92634.htm)、[Veeam](https://www.veeam.com/blog/?p=123438)、[Veritas](https://players.brightcove.net/4396107486001/default_default/index.html?videoId=6189967101001) などの複数のバックアップ パートナーが含まれています。これらのソリューションは、Azure Files の SMB 3.0 と NFS 4.1 の両方で動作するように拡張されました。

* <a id="migrate-nfs-data"></a>
**既存のデータを NFS 共有に移行することはできますか?**

    同じリージョン内であれば、scp、rsync、SSHFS などの標準ツールを使用してデータを移動できます。 Azure Files NFS は複数のコンピューティング インスタンスから同時にアクセスできるため、並列アップロードによってコピー速度を向上させることができます。 リージョンの外部からデータを取り込む場合は、VPN または Expressroute を使用して、オンプレミスのデータ センターからファイル システムにマウントします。

## <a name="on-premises-access"></a>オンプレミスのアクセス

* <a id="port-445-blocked"></a>
**Azure Files のマウントに失敗している My ISP または IT blocks Port 445 です。どうすればよいですか。**

    [ポート 445 のブロックを回避するさまざまな方法についてはこちらで](./storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked)確認できます。 Azure ファイルは、リージョンやデータセンターの外側からは SMB 3.0 (暗号化サポートあり) を使用した接続のみを許可します。 SMB 3.0 プロトコルには、インターネット経由で使用する場合に非常に安全となる、チャネル暗号化などのさまざまなセキュリティ機能が導入されています。 ただし、より古い SMB バージョンで確認された脆弱性の履歴的理由から、ポート 445 がブロックされている可能性もあります。 理想としては、ポートは SMB 1.0 トラフィックに対してのみブロックされ、SMB 1.0 はすべてのクライアントでオフにされる必要があります。

* <a id="expressroute-not-required"></a>
**Azure Files に接続するためや、Azure File Sync をオンプレミスで使用するために、Azure ExpressRoute を使用する必要はありますか。**  

    いいえ。 ExpressRoute から Azure ファイル共有にアクセスする必要はありません。 Azure ファイル共有を直接オンプレミスにマウントしている場合、必要なのは、インターネット アクセスのためにポート 445 (TCP 送信) が開放されていることだけです (これは SMB が通信に使用するポートです)。 Azure File Sync を使用している場合、必要なのは、HTTPS アクセスのためのポート 443 (TCP 送信) だけです (SMB は必要ありません)。 ただし、ExpressRoute は、これらのアクセス オプションのいずれでも使用 "*できます*"。

* <a id="mount-locally"></a>
**ローカル マシンに Azure ファイル共有をマウントするにはどうすればよいですか。**  

    ポート 445 (TCP 送信) が開放されており、クライアントが SMB 3.0 プロトコルをサポートしている (たとえば、Windows 10 や Windows Server 2016 を使用している) 場合は、SMB プロトコル経由でファイル共有をマウントできます。 ポート 445 が組織のポリシーまたはお使いの ISP によってブロックされている場合は、Azure File Sync を使用して Azure ファイル共有にアクセスできます。

## <a name="backup"></a>バックアップ
* <a id="backup-share"></a>
**Azure ファイル共有をバックアップする方法を教えてください。**  
    誤って削除した場合のために、定期的に[共有スナップショット](storage-snapshots-files.md)を使用して保護できます。 AzCopy、RoboCopy、またはマウントされているファイル共有をバックアップできるサードパーティ製のバックアップ ツールを使用することもできます。 Azure Backup では、Azure Files をバックアップできます。 詳細については、[Azure Backup で Azure ファイル共有をバックアップする](../../backup/backup-afs.md)方法に関するページを参照してください。

## <a name="share-snapshots"></a>共有スナップショット

### <a name="share-snapshots-general"></a>共有スナップショット:全般
* <a id="what-are-snaphots"></a>
**ファイル共有スナップショットとは何ですか。**  
    Azure ファイル共有スナップショットでは、読み取り専用バージョンのファイル共有を作成できます。 また、Azure Files を使用すると、以前のバージョンのコンテンツをコピーして同じ共有に戻したり、Azure またはオンプレミス内で場所を変更してさらに変更を加えたりできます。 共有スナップショットの詳細については、[共有スナップショットの概要](storage-snapshots-files.md)に関する記事をご覧ください。

* <a id="where-are-snapshots-stored"></a>
**共有スナップショットはどこに格納されていますか。**  
    共有スナップショットは、ファイル共有と同じストレージ アカウントに格納されています。

* <a id="snapshot-consistency"></a>
**共有スナップショットにアプリケーション整合性はありますか。**  
    いいえ。共有スナップショットにアプリケーション整合性はありません。 共有スナップショットを取得する前に、ユーザーは、アプリケーションから共有に書き込みをフラッシュする必要があります。

* <a id="snapshot-limits"></a>
**使用できる共有スナップショットの数に制限はありますか。**  
    はい。 Azure Files で保持できる共有スナップショットの数は最大で 200 個です。 共有スナップショットは共有クォータに対してカウントされないので、すべての共有スナップショットで使用される合計領域に共有ごとの制限はありません。 ストレージ アカウントの制限は、引き続き適用されます。 共有スナップショットの数が 200 を超えると、新しい共有スナップショットを作成するために、古い共有スナップショットを削除する必要があります。

* <a id="snapshot-cost"></a>
**共有スナップショットにかかるコストを教えてください。**  
    標準トランザクションと標準ストレージのコストがスナップショットに適用されます。 スナップショットは本質的に増分です。 基本のスナップショットは共有そのものです。 それ以降のスナップショットはすべて増分であり、以前のスナップショットとの差分のみを格納しています。 つまり、ワークロード チャーンが最小であれば、請求書に表示される差分変更は最小になります。 Standard Azure Files の価格設定情報については、「[料金ページ](https://azure.microsoft.com/pricing/details/storage/files/)」を参照してください。 現在、共有スナップショットで使用されるサイズを見る方法として、請求された容量と使用した容量を比較しています。 Microsoft は、報告機能を改善するツールの開発に取り組んでいます。

* <a id="ntfs-acls-snaphsots"></a>
**ディレクトリおよびファイルの NTFS ACL は共有スナップショットで保持されますか。**  
    ディレクトリおよびファイルの NTFS ACL は共有スナップショットで保持されます。

### <a name="create-share-snapshots"></a>共有スナップショットを作成する
* <a id="file-snaphsots"></a>
**個々のファイルの共有スナップショットを作成できますか。**  
    共有スナップショットは、ファイル共有レベルで作成されます。 ファイル共有スナップショットから個々のファイルを復元できますが、ファイルレベルの共有スナップショットは作成できません。 ただし、共有レベルの共有スナップショットを取得しており、特定のファイルが変更された共有スナップショットを一覧表示したい場合は、Windows にマウントされた共有で、**以前のバージョン** からこれを行うことができます。 
    
    ファイル スナップショット機能が必要な場合は、[Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) までお知らせください。

* <a id="encrypted-snapshots"></a>
**暗号化されたファイル共有の共有スナップショットを作成できますか。**  
    保存時の暗号化を有効にした Azure ファイル共有の共有スナップショットを取得できます。 共有スナップショットから暗号化されたファイル共有に、ファイルを復元できます。 お使いの共有が暗号化されている場合、共有スナップショットも暗号化されます。

* <a id="geo-redundant-snaphsots"></a>
**使用している共有スナップショットは geo 冗長性でしょうか。**  
    共有スナップショットには、そのスナップショットが取得された Azure ファイル共有と同じ冗長性があります。 お使いのアカウントに geo 冗長ストレージを選択した場合、共有スナップショットもペアになっているリージョンに冗長的に保存されます。

### <a name="manage-share-snapshots"></a>共有スナップショットを管理する
* <a id="browse-snapshots-linux"></a>
**Linux から共有スナップショットを参照できますか。**  
    Azure CLI を使用して、Linux で共有スナップショットの作成、一覧表示、参照、復元が可能です。

* <a id="copy-snapshots-to-other-storage-account"></a>
**共有スナップショットを別のストレージ アカウントにコピーできますか。**  
    共有スナップショットから別の場所にファイルをコピーできますが、共有スナップショット自体はコピーできません。

### <a name="restore-data-from-share-snapshots"></a>共有スナップショットからデータを復元する
* <a id="promote-share-snapshot"></a>
**共有スナップショットを基本の共有に昇格させることはできますか。**  
    データは、共有スナップショットから他の宛先にコピーできます。 共有スナップショットを基本の共有に昇格させることはできません。

* <a id="restore-snapshotted-file-to-other-share"></a>
**共有スナップショットから別のストレージ アカウントにデータを復元できますか。**  
    はい。 共有スナップショットのファイルは、元の場所にコピーできるほか、別の場所にコピーすることもできます。ストレージ アカウントまたはリージョンが同じでも異なっていてもかまいません。 また、オンプレミスの場所や他のクラウドにファイルをコピーすることもできます。    
  
### <a name="clean-up-share-snapshots"></a>共有スナップショットをクリーンアップする
* <a id="delete-share-keep-snapshots"></a>
**共有スナップショットは削除せずに、共有を削除することはできますか。**  
    共有にアクティブな共有スナップショットがある場合、共有を削除することはできません。 API を使用して共有と一緒に共有スナップショットを削除することはできます。 共有スナップショットと共有の両方を削除するのであれば、Azure Portal で行うこともできます。

* <a id="delete-share-with-snapshots"></a>
**ストレージ アカウントを削除した場合、共有スナップショットはどうなりますか。**  
    ストレージ アカウントを削除すると、共有スナップショットも削除されます。

## <a name="billing-and-pricing"></a>課金と価格
* <a id="vm-file-share-network-traffic"></a>
**Azure VM と Azure ファイル共有の間のネットワーク トラフィックは、サブスクリプションに課金される外部帯域幅としてカウントされますか。**  
    ファイル共有と VM が同じ Azure リージョン内にある場合、そのファイル共有と VM の間のトラフィックに別途料金は発生しません。 ファイル共有と VM がそれぞれ異なるリージョンに存在する場合、両者の間のトラフィックは外部帯域幅として課金されます。

* <a id="share-snapshot-price"></a>
**共有スナップショットにかかるコストを教えてください。**  
     共有スナップショットの容量に関して、プレビュー期間中は料金は発生しません。 Standard ストレージのエグレスとトランザクションのコストが適用されます。 一般公開後は、共有スナップショットの容量とトランザクションの料金が、サブスクリプションに対して課金されます。
     
     共有スナップショットは、本質的に増分です。 基本の共有スナップショットは、共有そのものです。 それ以降の共有スナップショットはすべて増分であり、先行する共有スナップショットとの差分のみが格納されます。 変更されたコンテンツに対してのみ、課金されます。 共有に 100 GiB のデータがあり、最新の共有スナップショットの後に 5 GiB だけが変更された場合、共有スナップショットは追加の 5 GiB のみを消費し、課金は 105 GiB に対して行われます。 トランザクションおよび標準エグレスの課金の詳細については、[課金に関するページ](https://azure.microsoft.com/pricing/details/storage/files/)をご覧ください。

## <a name="scale-and-performance"></a>スケールとパフォーマンス
* <a id="files-scale-limits"></a>
**Azure Files のスケールの上限を教えてください。**  
    Azure Files のスケーラビリティおよびパフォーマンスのターゲットについては、「[Azure Files のスケーラビリティおよびパフォーマンスのターゲット](storage-files-scale-targets.md)」を参照してください。

* <a id="need-larger-share"></a>
**Azure ファイル共有に使用できるサイズ**  
    Azure ファイル共有のサイズ (Premium および Standard) は最大 100 TiB までスケールアップできます。 Standard レベルの大きいファイル共有のオンボード手順については、この計画ガイドの「[大きなファイル共有へのオンボード (Standard レベル)](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib)」セクションを参照してください。

* <a id="lfs-performance-impact"></a>
**ファイル共有のクォータを拡張すると、ワークロードや Azure File Sync に影響しますか。**
    
    いいえ。 クォータを拡張しても、ワークロードや Azure File Sync には影響しません。

* <a id="open-handles-quota"></a>
**同じファイルに同時にアクセスできるクライアントの数はどのくらいですか。**    
    開くことができるハンドルのクォータは、1 つのファイルにつき 2,000 個です。 開いているハンドルが 2,000 個になると、クォータに達したことを伝えるエラー メッセージが表示されます。

* <a id="zip-slow-performance"></a>
**Azure Files にファイルを解凍するとき、パフォーマンスが低下します。どうすればよいですか。**  
    Azure Files に大量のファイルを転送する場合、AzCopy (Windows 用。Linux/Unix 用はプレビュー) または Azure PowerShell を使用することをお勧めします。 これらのツールは、ネットワーク転送に最適化されています。

* <a id="slow-perf-windows-81-2012r2"></a>
**Windows Server 2012 R2 または Windows 8.1 で Azure ファイル共有をマウントした後、パフォーマンスが低下します。**  
    Windows Server 2012 R2 および Windows 8.1 で Azure ファイル共有をマウントした場合、問題が生じることが確認されています。 この問題は、2014 年 4 月の Windows 8.1 および Windows Server 2012 R2 向けの累積更新プログラムのパッチで解決されています。 パフォーマンスを最適化するために、Windows Server 2012 R2 および Windows 8.1 のすべてのインスタンスに、このパッチが適用されていることを確認してください (Windows Update で常に Windows パッチを取得することが推奨されます)。詳細については、関連する Microsoft サポート技術情報の記事「[Slow performance when you access Azure Files from Windows 8.1 or Server 2012 R2 (Windows 8.1 または Server 2012 R2 から Azure Files にアクセスするときにパフォーマンスが低下する)](https://support.microsoft.com/kb/3114025)」を参照してください。

## <a name="features-and-interoperability-with-other-services"></a>機能およびその他のサービスとの相互運用性
* <a id="cluster-witness"></a>
**Azure ファイル共有を Windows Server フェールオーバー クラスターの "*監視用ファイル共有*" として使用できますか。**  
    その構成は現在、Azure ファイル共有ではサポートされていません。 Azure Blob Storage 用にこれを設定する方法の詳細については、「[Deploy a Cloud Witness for a Failover Cluster (フェールオーバー クラスターのクラスター監視のデプロイ)](/windows-server/failover-clustering/deploy-cloud-witness)」をご覧ください。

* <a id="containers"></a>
**Azure Container Instances で Azure ファイル共有をマウントできますか。**  
    はい。Azure ファイル共有は、コンテナー インスタンスの有効期間が過ぎた後も情報を保持する方法として最適です。 詳細については、「[Azure Container Instances での Azure ファイル共有のマウント](../../container-instances/container-instances-volume-azure-files.md)」を参照してください。

* <a id="rest-rename"></a>
**REST API に名前変更の操作はありますか。**  
    現時点ではありません。

* <a id="nested-shares"></a>
**共有は入れ子にできますか。つまり、共有の下に共有を配置できますか。**  
    いいえ。 ファイル共有はマウント可能な "*仮想ドライバーである*" ため、共有の入れ子はサポートしていません。

* <a id="ibm-mq"></a>
**IBM MQ での Azure Files の使用方法**  
    IBM MQ ユーザーが IBM のサービスで Azure Files を構成する際に役立つドキュメントが IBM から提供されています。 詳細については、「[How to set up IBM MQ Multi instance queue manager with Microsoft Azure Files Service (IBM MQ マルチ インスタンス キュー マネージャーを Microsoft Azure Files サービスで設定する方法)](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)」を参照してください。

## <a name="see-also"></a>関連項目
* [Windows での Azure Files に関する問題のトラブルシューティング](storage-troubleshoot-windows-file-connection-problems.md)
* [Linux での Azure Files に関する問題のトラブルシューティング](storage-troubleshoot-linux-file-connection-problems.md)
* [Azure File Sync のトラブルシューティング](storage-sync-files-troubleshoot.md)
