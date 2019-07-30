---
title: Azure Files についてよく寄せられる質問 (FAQ) | Microsoft Docs
description: Azure Files についてよく寄せられる質問とその回答を紹介します。
services: storage
author: roygara
ms.service: storage
ms.date: 01/02/2019
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: c32d9954b3c90a5f7e9c5475acdb141f7154cf76
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540350"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Azure Files に関してよく寄せられる質問 (FAQ)
[Azure Files](storage-files-introduction.md) はクラウドで、業界標準の [Server Message Block (SMB) プロトコル](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)を介してアクセスできる、完全に管理されたファイル共有を提供します。 Azure ファイル共有は、クラウドまたはオンプレミスにデプロイされた Windows、Linux、macOS で同時にマウントできます。 また、データが使用される場所に近接した Windows Server マシンに、Azure File Sync で Azure ファイル共有をキャッシュすることによって、高速なアクセスを実現することもできます。

この記事では、Azure Files での Azure File Sync の使用を含め、Azure Files の機能についてよく寄せられる質問にお答えします。 ご質問に対する回答がここで見つからない場合は、次のチャネルでお問い合わせください (上から順に)。

1. この記事のコメント セクション。
2. [Azure Storage フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)。
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)。 
4. Microsoft サポート。 新しいサポート要求を作成するには、Azure Portal の **[ヘルプ]** タブで、 **[ヘルプとサポート]** ボタンを選択し、 **[新しいサポート要求]** を選択します。

## <a name="general"></a>全般
* <a id="why-files-useful"></a>
  **Azure Files にはどのような利点がありますか。**  
   Azure Files を使用すると、物理サーバー、デバイス、アプライアンスに伴うオーバーヘッドの管理に縛られることなく、クラウドにファイル共有を作成することができます。 OS 更新プログラムの適用、不良ディスクの交換など、単調な作業は Microsoft が代わって行います。 Azure Files を利用できるシナリオの詳細については、「[Azure Files の適用ケース](storage-files-introduction.md#why-azure-files-is-useful)」をご覧ください。

* <a id="file-access-options"></a>
  **Azure Files でファイルにアクセスするさまざまな方法を挙げてください。**  
    SMB 3.0 プロトコルを使用してローカル マシンにファイル共有をマウントするか、[Storage Explorer](https://storageexplorer.com/) などのツールを使用してファイル共有内のファイルにアクセスできます。 アプリケーションから、ストレージ クライアント ライブラリ、REST API、PowerShell、または Azure CLI を使用して、Azure ファイル共有内のファイルにアクセスできます。

* <a id="what-is-afs"></a>
  **Azure File Sync とは何ですか。**  
    Azure File Sync を使用すると、オンプレミスのファイル サーバーの柔軟性、パフォーマンス、互換性を維持したまま Azure Files で組織のファイル共有を一元化できます。 Azure File Sync により、ご利用の Windows Server マシンが Azure ファイル共有の高速キャッシュに変わります。 SMB、ネットワーク ファイル システム (NFS)、ファイル転送プロトコル サービス (FTPS) など、Windows Server 上で利用できるあらゆるプロトコルを使用して、データにローカルでアクセスすることができます。 キャッシュは、世界中にいくつでも必要に応じて設置することができます。

* <a id="files-versus-blobs"></a>
  **データに Azure Blob Storage ではなく Azure ファイル共有を使用する理由を教えてください。**  
    Azure Files と Azure Blob Storage はどちらも、クラウドに大量のデータを保存する手段として利用できますが、その用途にやや違いがあります。 
    
    Azure Blob Storage は、構造化されていないデータを格納する必要がある大規模なクラウド ネイティブ アプリケーションに役立ちます。 パフォーマンスとスケールを最大限に高めるため、Azure Blob Storage は通常のファイル システムよりも単純なストレージの抽象化です。 Azure Blob Storage には必ず、REST ベースのクライアント ライブラリ経由で (または REST ベースのプロトコルを介して直接) アクセスする必要があります。

    Azure Files は、明確にファイル システムです。 Azure Files には、オンプレミスのオペレーティング システムで長年使われてきた、皆さんもおなじみのファイルの概念がすべて備わっています。 Azure Blob Storage と同様、Azure Files にも REST インターフェイスと REST ベースのクライアント ライブラリが用意されています。 Azure Files が Azure Blob Storage と違うのは、Azure ファイル共有に SMB でアクセスできることです。 SMB を使用すれば、オンプレミスの VM であれ、クラウドの VM であれ、Windows、Linux、macOS 上で Azure ファイル共有を直接マウントすることができます。コードを記述したり、特別なドライバーをファイル システムにアタッチする必要はありません。 また、データが使用される場所に近接したオンプレミスのファイル サーバーに Azure File Sync で Azure ファイル共有をキャッシュすることによって、高速なアクセスを実現することもできます。 
   
    Azure Files と Azure Blob Storage の違いに関する詳細な説明については、「[Azure BLOB、Azure Files、Azure Disks の使い分け](../common/storage-decide-blobs-files-disks.md)」をご覧ください。 Azure Blob Storage の詳細については、「[Blob Storage の概要](../blobs/storage-blobs-introduction.md)」をご覧ください。

* <a id="files-versus-disks"></a>**Azure Disks ではなく Azure ファイル共有を使用する理由を教えてください。**  
    Azure Disks におけるディスクは、ただのディスクに過ぎません。 Azure Disks を有効活用するには、Azure で実行されている仮想マシンにディスクをアタッチする必要があります。 Azure Disks は、オンプレミスのサーバーで使われるディスクとまったく同じ用途で使用することができます。 OS システム ディスクや OS のスワップ領域、アプリケーションの専用ストレージとして使用可能です。 Azure ファイル共有を使用する代わりに Azure Disks を使用してクラウドにファイル サーバーを作成するのも面白い使い方です。 Azure Files では現在サポートされていないデプロイ オプションを必要とする場合 (NFS プロトコル サポートや Premium Storage など)、Azure Virtual Machines にファイル サーバーをデプロイすれば、高パフォーマンスで Azure にファイル ストレージを設置できます。 

    ただし、Azure Disks をバックエンド ストレージとしたファイル サーバーを実行すると、いくつかの理由から、通常は Azure ファイル共有を使用するよりもはるかに高額になります。 第一に、ディスク ストレージの料金を支払うだけでなく、1 つまたは複数の Azure VM を実行する費用を支払う必要があります。 第二に、ファイル サーバーの実行に使用される VM を管理する必要もあります。 たとえば OS のアップグレードを自分で行う必要があります。 最後に、オンプレミスでデータをキャッシュする必要が最終的に生じた場合、分散ファイル システム レプリケーション (DFSR) などのレプリケーション テクノロジをユーザーの判断で設定して管理し、キャッシュを行う必要があります。

    (Azure Disks をバックエンド ストレージとして使用することに加え) Azure Virtual Machines にホストされたファイル サーバーと Azure Files の両方の利点を活かす方法の 1 つは、クラウド VM 上でホストされているファイル サーバーに Azure File Sync をインストールすることです。 Azure ファイル共有がお使いのファイル サーバーと同じリージョンにある場合、クラウドの階層化を有効にして、ボリュームの空き領域を最大 (99%) に設定できます。 これによりデータの重複を最小限に抑えることができます。 また、NFS プロトコル サポートを必要とするアプリケーションなど、どのようなアプリケーションでも必要に応じてファイル サーバーで使用することができます。

    Azure で高パフォーマンスで高可用なファイル サーバーを設定する方法については、「[Deploying IaaS VM Guest Clusters in Microsoft Azure (Microsoft Azure に IaaS VM ゲスト クラスターをデプロイする)](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/)」をご覧ください。 Azure Files と Azure Disks の違いに関する詳細な説明については、「[Azure BLOB、Azure Files、Azure Disks の使い分け](../common/storage-decide-blobs-files-disks.md)」をご覧ください。 Azure ディスクの詳細については、「[Azure Managed Disks の概要](../../virtual-machines/windows/managed-disks-overview.md)」をご覧ください。

* <a id="get-started"></a>
  **Azure Files を使い始めるにはどうしたらよいですか。**  
   Azure Files は簡単に使い始めることができます。 まず[ファイル共有を作成](storage-how-to-create-file-share.md)し、お好きなオペレーティング システムでマウントします。 

  * [Windows でのマウント](storage-how-to-use-files-windows.md)
  * [Linux でのマウント](storage-how-to-use-files-linux.md)
  * [macOS でのマウント](storage-how-to-use-files-mac.md)

    Azure ファイル共有をデプロイして組織内の運用ファイル共有を置き換える方法についての詳しいガイドは、「[Azure Files のデプロイの計画](storage-files-planning.md)」をご覧ください。

* <a id="redundancy-options"></a>
  **Azure Files では、どのようなストレージ冗長性オプションがサポートされていますか。**  
    現在、Azure Files では、ローカル冗長ストレージ (LRS)、ゾーン冗長ストレージ (ZRS)、geo 冗長ストレージ (GRS) がサポートされています。 今後は読み取りアクセス geo 冗長ストレージ (RA-GRS) のサポートを計画していますが、現時点でスケジュールをお伝えすることはできません。

* <a id="tier-options"></a>
  **Azure Files では、どのストレージ層がサポートされていますか。**  
    Azure Files は、Premium と Standard という 2 つのストレージ層をサポートしています。 Standard ファイル共有は汎用 (GPv1 または GPv2) ストレージ アカウントで作成され、Premium ファイル共有は FileStorage ストレージ アカウントで作成されます。 [Standard ファイル共有](storage-how-to-create-file-share.md)と[Premium ファイル共有](storage-how-to-create-premium-fileshare.md)の作成方法を参照してください。 
    
    > [!NOTE]
    > BLOB ストレージ アカウントまたは *Premium* 汎用 (GPv1 または GPv2) ストレージ アカウントから Azure ファイル共有を作成することはできません。 Standard Azure ファイル共有は *Standard* 汎用アカウントでのみ作成し、Premium Azure ファイル共有は FileStorage ストレージ アカウントでのみ作成する必要があります。 *Premium* 汎用 (GPv1 および GPv2) ストレージ アカウントは、Premium ページ BLOB 専用です。 

* <a id="give-us-feedback"></a>
  **Azure Files に追加してほしい機能があります。追加できますか。**  
    Azure Files チームでは、サービスに関するあらゆるフィードバックをお待ちしています。 [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) で機能のリクエストにご投票ください。 多くの新しい機能を皆様に喜んでいただけることを楽しみにしています。

## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
  **Azure File Sync は、どのリージョンでサポートされていますか。**  
    提供されているリージョンの一覧は、Azure File Sync プランニング ガイドの「[利用可能なリージョン](storage-sync-files-planning.md#region-availability)」セクションでご覧いただけます。 パブリック リージョン以外のリージョンも含め、今後サポート対象リージョンを拡大していく予定です。

* <a id="cross-domain-sync"></a>
  **同じ同期グループ内にドメイン参加とドメイン非参加のサーバーを保持することはできますか。**  
    はい。 異なる Active Directory に属しているサーバー エンドポイントを 1 つの同期グループに含めることは可能です。ドメインに参加していない場合でも同様です。 この構成は技術的には機能するものの、通常の構成としては、お勧めできません。あるサーバー上のファイルやフォルダー用に定義されたアクセス制御リスト (ACL) が、同期グループ内の他のサーバーでは適用できない可能性があるためです。 最良の結果を得るには、同じ Active Directory フォレスト内にあるサーバー間、別の Active Directory フォレスト内にあるものの信頼関係が確立されているサーバー間、またはドメインに属していないサーバー間で同期することをお勧めします。 これらの構成を混在させることは避けるようにしてください。

* <a id="afs-change-detection"></a>
  **SMB またはポータルで Azure ファイル共有に直接ファイルを作成しました。このファイルが同期グループのサーバーに同期されるまでどのくらいの時間がかかりますか。**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**2 つのサーバーで同じファイルがほぼ同時に変更された場合、どうなりますか。**  
    Azure File Sync では、シンプルな競合解決方法が採用されています。ファイルに対して 2 つのサーバーで同時に変更を加えた場合、その両方の変更が保持されます。 最後に書き込まれた変更では、元のファイル名が維持されます。 古いファイルでは、"ソース" マシンと競合番号が名前に追加されます。 次の命名規則が使用されます。 
   
    \<拡張子を除くファイル名\>-\<マシン名\>\[-#\].\<拡張子\>  

    たとえば、CompanyReport.docx で競合が生じたとします。古い方の書き込みが CentralServer で行われた場合、最初の競合で生じるファイルの名前は CompanyReport-CentralServer.docx となります。 2 回目の競合では、CompanyReport-CentralServer-1.docx という名前になります。

* <a id="afs-storage-redundancy"></a>
  **Azure File Sync では、geo 冗長ストレージはサポートされますか。**  
    はい。Azure Files では、ローカル冗長ストレージ (LRS) と geo 冗長ストレージ (GRS) の両方がサポートされます。 GRS 用に構成されたアカウントから、ペアになったリージョン間でストレージ アカウントのフェールオーバーを開始する場合は、新しいリージョンをデータのみのバックアップとして扱うことをお勧めします。 Azure File Sync は、新しいプライマリ リージョンで自動的に同期を開始することはありません。 

* <a id="sizeondisk-versus-size"></a>
  **ファイルの "*ディスク上のサイズ*" プロパティが、Azure File Sync を使用した後の "*サイズ*" プロパティと一致しないのはどうしてですか。**  
  「[クラウドの階層化について](storage-sync-cloud-tiering.md#sizeondisk-versus-size)」を参照してください。

* <a id="is-my-file-tiered"></a>
  **ファイルが階層化されているかどうかは、どうやって判断できますか。**  
  「[クラウドの階層化について](storage-sync-cloud-tiering.md#is-my-file-tiered)」を参照してください。

* <a id="afs-recall-file"></a>**使用したいファイルが階層化されています。ローカルで使用するためにこのファイルをディスクに再現するには、どうすればよいですか。**  
  「[クラウドの階層化について](storage-sync-cloud-tiering.md#afs-recall-file)」を参照してください。

* <a id="afs-force-tiering"></a>
  **ファイルまたはディレクトリを強制的に階層化するには、どうすればよいですか。**  
  「[クラウドの階層化について](storage-sync-cloud-tiering.md#afs-force-tiering)」を参照してください。

* <a id="afs-effective-vfs"></a>
  **ボリューム上に複数のサーバー エンドポイントがある場合、*ボリュームの空き領域*はどのように解釈されますか。**  
  「[クラウドの階層化について](storage-sync-cloud-tiering.md#afs-effective-vfs)」を参照してください。

* <a id="afs-files-excluded"></a>
  **Azure File Sync によって自動的に除外されるのは、どのファイルまたはフォルダーですか。**  
    Azure File Sync は既定で、次のファイルを除外します。
  * desktop.ini
  * thumbs.db
  * ehthumbs.db
  * ~$\*.\*
  * \*.laccdb
  * \*.tmp
  * 635D02A9D91C401B97884B82B3BCDAEA.\*

    また、既定で、次のフォルダーも除外します。

  * \System Volume Information
  * \$RECYCLE.BIN
  * \SyncShareState

* <a id="afs-os-support"></a>
  **Windows Server 2008 R2、Linux、または自分のネットワーク接続ストレージ (NAS) デバイスで Azure File Sync を使用することはできますか。**  
    現在、Azure File Sync は Windows Server 2019、Windows Server 2016、および Windows Server 2012 R2 のみをサポートしています。 現時点でお伝えできる他の計画はありませんが、お客様の要望に応じてサポートするプラットフォームを増やしていきたいと考えています。 サポート対象としてご希望のプラットフォームがあれば、[Azure Files の UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) までお寄せください。

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **階層化されたファイルがサーバー エンドポイント名前空間の外部に存在するのはなぜですか。**  
    Azure File Sync エージェント バージョン 3 より前の Azure File Sync は、サーバー エンドポイントと同じボリューム上であってもサーバー エンドポイントの外部に存在する階層化されたファイルの移動をブロックしました。 他のボリュームに対する、コピー操作、階層化されていないファイルの移動、および階層化されたファイルの移動は、影響を受けませんでした。 このような動作の理由は、ファイル エクスプローラーおよび他の Windows の API による同じボリューム上での移動操作は、(ほとんど) 瞬時の名前変更操作であるという、暗黙の仮定によるものでした。 これは、Azure File Sync がクラウドからデータを呼び戻している間、エクスプローラーや他の移動方法 (コマンド ラインや PowerShell など) が応答しないように見えることを意味します。 [Azure File Sync エージェント バージョン 3.0.12.0](storage-files-release-notes.md#supported-versions) 以降の Azure File Sync では、サーバー エンドポイントの外部にある階層化されたファイルを移動できます。 階層化されたファイルがサーバー エンドポイントの外部で階層化されたファイルとして存在できるようにし、バックグラウンドでファイルを呼び戻すことにより、上で説明したような悪影響を防ぎます。 つまり、同じボリューム上での移動は瞬時であり、移動が完了した後で、ファイルをディスクに呼び戻すためのすべての処理を行います。 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **サーバーでの Azure File Sync に関して問題があります (同期、クラウド階層化など)。サーバー エンドポイントを削除して再作成する必要がありますか。**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **ストレージ同期サービスやストレージ アカウントを別のリソース グループまたはサブスクリプションに移動できますか。**  
   はい。ストレージ同期サービスやストレージ アカウントは、既存の Azure AD テナント内の別のリソース グループまたはサブスクリプションに移動できます。 ストレージ アカウントを移動する場合は、そのストレージ アカウントにハイブリッド ファイル同期サービス アクセス権を付与する必要があります (「[Azure File Sync がストレージ アカウントへのアクセス権を持っていることを確認します](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)」を参照してください)。

    > [!Note]  
    > Azure File Sync では、別の Azure AD テナントへのサブスクリプションの移動がサポートされません。
    
* <a id="afs-ntfs-acls"></a>
  **Azure File Sync では、Azure Files の格納データと共にディレクトリ レベルまたはファイル レベルの NTFS ACL が保持されますか。**

    オンプレミスのファイル サーバーから伝達された NTFS ACL は、Azure File Sync によってメタデータとして保持されます。 Azure Files では、Azure File Sync サービスによって管理されているファイル共有へのアクセスに、Azure AD 資格情報による認証を使用することはできません。
    
## <a name="security-authentication-and-access-control"></a>セキュリティ、認証、およびアクセス制御
* <a id="ad-support"></a>
**Azure Files では、Active Directory ベースの認証とアクセス制御はサポートされていますか。**  
    
    はい。Azure Files では、Azure Active Directory (Azure AD) による ID ベースの認証およびアクセス制御がサポートされています (プレビュー)。 Azure Files での SMB 経由の Azure AD 認証では、ドメイン参加 VM が Azure AD 資格情報で共有、ディレクトリ、ファイルにアクセスできるよう、Azure Active Directory Domain Services が利用されます。 詳細については、「[SMB を使用した Azure Files の Azure Active Directory 認証の概要 (プレビュー)](storage-files-active-directory-overview.md)」を参照してください。 

    Azure Files では、その他 2 つの方法でアクセス制御を管理できます。

    - Shared Access Signature (SAS) を使用すると、指定した期間中有効な特定のアクセス許可があるトークンを生成できます。 たとえば、特定のファイルへの読み取り専用のアクセス許可が付与された、有効期限が 10 分間のトークンを生成できます。 このトークンを所有するすべてのユーザーは、トークンが有効な間、そのファイルへの 10 分間の読み取り専用アクセスを持ちます。 現在、Shared Access Signature キーは、REST API またはクライアント ライブラリでしかサポートされていません。 ストレージ アカウント キーを使って SMB 経由で Azure ファイル共有をマウントする必要があります。

    - Azure File Sync は、(Active Directory ベースかローカルかに関係なく) すべての随意 ACL (DACL) を保持し、同期先のすべてのサーバー エンドポイントにレプリケートします。 Windows Server は既に Active Directory で認証できるので、Azure File Sync は、Active Directory ベースの認証の完全サポートと ACL のサポートが実現するまでの優れた応急策となります。

* <a id="ad-support-regions"></a>
**Azure Files での SMB 経由の Azure AD のプレビューは、すべての Azure リージョンで利用できますか。**

    このプレビューは、すべてのパブリック リージョンで使用できます。

* <a id="ad-support-on-premises"></a>
**Azure Files での SMB 経由の Azure AD 認証 (プレビュー) では、オンプレミス マシンからの Azure AD を使用した認証はサポートされていますか。**

    いいえ。プレビュー リリースでは、Azure Files でオンプレミス マシンからの Azure AD による認証はサポートされていません。

* <a id="ad-support-devices"></a>
**Azure Files での SMB 経由の Azure AD 認証 (プレビュー) では、Azure AD に参加済みまたは登録済みのデバイスからの Azure AD 資格情報を使用した SMB アクセスはサポートされていますか。**

    いいえ。このシナリオはサポートされていません。

* <a id="ad-support-rest-apis"></a>
**ディレクトリまたはファイルの NTFS ACL の取得、設定、またはコピーをサポートする REST API はありますか。**

    プレビュー リリースでは、ディレクトリまたはファイルの NTFS ACL を取得、設定、またはコピーするための REST API はサポートされていません。

* <a id="ad-vm-subscription"></a>
**異なるサブスクリプションの VM から Azure AD 資格情報を使用して Azure Files にアクセスすることはできますか。**

    ファイル共有のデプロイ元であるサブスクリプションが、VM のドメイン参加先である Azure AD Domain Services デプロイと同じ Azure AD テナントに関連付けられている場合は、同じ Azure AD 資格情報を使用して Azure Files にアクセスできます。 制限は、サブスクリプションにではなく、関連付けられている Azure AD テナントに課せられます。    
    
* <a id="ad-support-subscription"></a>
**ファイル共有が関連付けられているプライマリ テナントとは異なる Azure AD テナントを使用して、Azure Files での SMB 経由の Azure AD 認証を有効にすることはできますか。**

    いいえ。Azure Files では、ファイル共有と同じサブスクリプションに存在する Azure AD テナントとの Azure AD 統合のみがサポートされます。 Azure AD テナントに関連付けることができるサブスクリプションは 1 つだけです。

* <a id="ad-linux-vms"></a>
**Azure Files での SMB 経由の Azure AD 認証 (プレビュー) では Linux VM がサポートされますか。**

    いいえ。プレビュー リリースでは、Linux VM からの認証はサポートされていません。

* <a id="ad-aad-smb-afs"></a>
**SMB 機能経由の Azure AD 認証は、Azure File Sync によって管理されているファイル共有で利用できますか。**

    いいえ。Azure Files では、Azure File Sync によって管理されているファイル共有での NTFS ACL の保持がサポートされていません。オンプレミスのファイル サーバーから伝達されたファイル ACL は、Azure File Sync によって保持されます。Azure Files に対してネイティブに構成された NTFS ACL は、Azure File Sync サービスによって上書きされます。 さらに Azure Files では、Azure File Sync サービスによって管理されているファイル共有へのアクセスに、Azure AD 資格情報による認証を使用することはできません。

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

   Azure Files は、Azure Storage 内の他のストレージ サービスと同じストレージ アーキテクチャ上で実行されます。 他の Azure Storage サービスで使用されているデータ コンプライアンス ポリシーが Azure Files でも適用されます。 Azure Storage のデータ コンプライアンスの詳細については、「[Azure Storage のコンプライアンス認証](https://docs.microsoft.com/azure/storage/common/storage-compliance-offerings)」を参照するか、[Microsoft セキュリティ センター](https://microsoft.com/trustcenter/default.aspx)にアクセスできます。

## <a name="on-premises-access"></a>オンプレミスのアクセス

* <a id="port-445-blocked"></a>
**Azure Files のマウントに失敗している My ISP または IT blocks Port 445 です。どうすればよいですか。**

    [ポート 445 のブロックを回避するさまざまな方法についてはこちらで](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked)確認できます。 Azure ファイルは、リージョンやデータセンターの外側からは SMB 3.0 (暗号化サポートあり) を使用した接続のみを許可します。 SMB 3.0 プロトコルには、インターネット経由で使用する場合に非常に安全となる、チャネル暗号化などのさまざまなセキュリティ機能が導入されています。 ただし、より古い SMB バージョンで確認された脆弱性の履歴的理由から、ポート 445 がブロックされている可能性もあります。 理想としては、ポートは SMB 1.0 トラフィックに対してのみブロックされ、SMB 1.0 はすべてのクライアントでオフにされる必要があります。

* <a id="expressroute-not-required"></a>
**Azure Files に接続するためや、Azure File Sync をオンプレミスで使用するために、Azure ExpressRoute を使用する必要はありますか。**  

    いいえ。 ExpressRoute から Azure ファイル共有にアクセスする必要はありません。 Azure ファイル共有を直接オンプレミスにマウントしている場合、必要なのは、インターネット アクセスのためにポート 445 (TCP 送信) が開放されていることだけです (これは SMB が通信に使用するポートです)。 Azure File Sync を使用している場合、必要なのは、HTTPS アクセスのためのポート 443 (TCP 送信) だけです (SMB は必要ありません)。 ただし、ExpressRoute は、これらのアクセス オプションのいずれでも使用 "*できます*"。

* <a id="mount-locally"></a>
**ローカル マシンに Azure ファイル共有をマウントするにはどうすればよいですか。**  

    ポート 445 (TCP 送信) が開放されており、クライアントが SMB 3.0 プロトコルをサポートしている (たとえば、Windows 10 や Windows Server 2016 を使用している) 場合は、SMB プロトコル経由でファイル共有をマウントできます。 ポート 445 が組織のポリシーまたはお使いの ISP によってブロックされている場合は、Azure File Sync を使用して Azure ファイル共有にアクセスできます。

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Azure ファイル共有をバックアップする方法を教えてください。**  
    誤って削除した場合のために、定期的に[共有スナップショット](storage-snapshots-files.md)を使用して保護できます。 AzCopy、RoboCopy、またはマウントされているファイル共有をバックアップできるサードパーティ製のバックアップ ツールを使用することもできます。 Azure Backup では、Azure Files をバックアップできます。 詳細については、[Azure Backup で Azure ファイル共有をバックアップする](https://docs.microsoft.com/azure/backup/backup-azure-files)方法に関するページを参照してください。

## <a name="share-snapshots"></a>共有スナップショット

### <a name="share-snapshots-general"></a>共有スナップショット:全般
* <a id="what-are-snaphots"></a>
**ファイル共有スナップショットとは何ですか。**  
    Azure ファイル共有スナップショットでは、読み取り専用バージョンのファイル共有を作成できます。 また、Azure Files を使用すると、以前のバージョンのコンテンツをコピーして同じ共有に戻したり、Azure またはオンプレミス内で場所を変更してさらに変更を加えたりできます。 共有スナップショットの詳細については、[共有スナップショットの概要](storage-snapshots-files.md)に関する記事をご覧ください。

* <a id="where-are-snapshots-stored"></a>
**共有スナップショットはどこに格納されていますか。**  
    共有スナップショットは、ファイル共有と同じストレージ アカウントに格納されています。

* <a id="snapshot-perf-impact"></a>
**共有スナップショットを使用することに関してパフォーマンス上の影響はありますか。**  
    共有スナップショットには、パフォーマンスのオーバーヘッドはありません。

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
    共有スナップショットは、ファイル共有レベルで作成されます。 ファイル共有スナップショットから個々のファイルを復元できますが、ファイルレベルの共有スナップショットは作成できません。 ただし、共有レベルの共有スナップショットを取得しており、特定のファイルが変更された共有スナップショットを一覧表示したい場合は、Windows にマウントされた共有で、**以前のバージョン**からこれを行うことができます。 
    
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
    Azure ファイル共有のサイズ (Premium および Standard) は最大 100 TiB までスケールアップできます。 GA オファリングとして最大 100 TiB の Premium ファイル共有サイズを利用できます。 GA オファリングとして最大 5 TiB の Standard ファイル共有サイズを利用できますが、プレビューでは最大 100 TiB のサイズを利用できます。 Standard レベルの大きいファイル共有のプレビューのオンボード手順については、この計画ガイドの「[大きなファイル共有へのオンボード (Standard レベル)](storage-files-planning.md#onboard-to-larger-file-shares-standard-tier)」セクションを参照してください。

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
    その構成は現在、Azure ファイル共有ではサポートされていません。 Azure Blob Storage 用にこれを設定する方法の詳細については、「[Deploy a Cloud Witness for a Failover Cluster (フェールオーバー クラスターのクラスター監視のデプロイ)](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)」をご覧ください。

* <a id="containers"></a>
**Azure Container Instances で Azure ファイル共有をマウントできますか。**  
    はい。Azure ファイル共有は、コンテナー インスタンスの有効期間が過ぎた後も情報を保持する方法として最適です。 詳細については、「[Azure Container Instances での Azure ファイル共有のマウント](../../container-instances/container-instances-mounting-azure-files-volume.md)」を参照してください。

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
