---
title: Azure Files の概要 | Microsoft Docs
description: 業界標準の SMB プロトコルを使用してクラウド上にネットワーク ファイル共有を作成および使用できるサービス、Azure Files の概要。
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f0dcd763240205bd396fc8cd0301c2046098473b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98070135"
---
# <a name="what-is-azure-files"></a>Azure Files とは
Azure Files では、業界標準の[サーバー メッセージ ブロック (SMB) プロトコル](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)または[ネットワーク ファイル システム (NFS) プロトコル](https://en.wikipedia.org/wiki/Network_File_System)を介してアクセスできる、フル マネージドのファイル共有がクラウド上で提供されます。 Azure ファイル共有は、クラウドまたはオンプレミスのデプロイにより、同時にマウントできます。 Azure Files SMB ファイル共有には、Windows、Linux、および macOS クライアントからアクセスできます。 Azure Files NFS ファイル共有には、Linux または macOS クライアントからアクセスできます。 さらに、データが使用されている場所の近くから高速アクセスするため、Azure File Sync を使用して、Windows サーバーに Azure Files SMB ファイル共有をキャッシュできます。

## <a name="videos"></a>ビデオ
| Azure File Sync の概要 | Azure Files と Sync (Ignite 2019)  |
|-|-|
| [![Azure File Sync の概要に関する動画のスクリーンキャスト - クリックして再生](./media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Azure Files と Sync に関するプレゼンテーションのスクリーンキャスト - クリックして再生](./media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/embed/6E2p28XwovU) |

Azure Files の一般的なユース ケースに関するビデオをいくつかご紹介します。
* [ファイル サーバーをサーバーレス Azure ファイル共有に置き換える](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)
* [AD 認証を利用して Windows Virtual Desktop で Azure Files の FSLogix プロファイル コンテナーを開始する](https://www.youtube.com/embed/9S5A1IJqfOQ)

## <a name="why-azure-files-is-useful"></a>Azure Files の適用ケース
Azure ファイル共有は、以下の作業に使用できます。

* **オンプレミスのファイル サーバーの置換または補完**:  
    Azure Files を使用して、従来のオンプレミス ファイル サーバーまたは NAS デバイスを完全に置き換えたり、補完することができます。 Windows、macOS、Linux などの一般的なオペレーティング システムに、世界中のどこからでも、Azure ファイル共有を簡単にマウントできます。 また、Azure File Sync を使用すると、Azure File SMB ファイル共有を、オンプレミスまたはクラウドにある Windows サーバーにレプリケートすることも可能です。これにより、使用されているデータを分散キャッシュしてパフォーマンスを向上させることができます。 [Azure Files AD 認証](storage-files-active-directory-overview.md)の最新リリースでは、オンプレミスでホストされている AD と Azure File SMB ファイル共有が連携して、引き続きアクセス制御を実現することができます。 

* **アプリケーションの "リフトアンドシフト"** :  
    Azure Files を使用すると、ファイル アプリケーションやユーザー データをファイル共有に保存し、アプリケーションをクラウドに簡単に "リフト アンド シフト" できます。 Azure Files を使用すると、アプリケーションとそのデータの両方が Azure に移動される "従来の" リフト アンド シフト シナリオと、アプリケーション データは Azure Files に移動され、アプリケーションはオンプレミスで実行を継続する、"ハイブリッド" リフト アンド シフト シナリオの両方に対応できます。 

* **クラウド開発の簡略化**:  
    また、Azure Files は、新しいクラウド開発プロジェクトを簡素化するためにさまざまな方法で使用できます。 次に例を示します。
    * **共有アプリケーションの設定**:  
        分散アプリケーションの一般的なパターンでは、複数のアプリケーション インスタンスからアクセスできる集中管理された場所に構成ファイルが配置されます。 アプリケーション インスタンスは File REST API を介してその構成を読み込むことができ、ユーザーは、必要に応じて、ローカルで SMB 共有をマウントすることでそれにアクセスできます。

    * **診断の共有**:  
        Azure ファイル共有は、クラウド アプリケーションが、ログ、メトリック、およびクラッシュ ダンプを書き込める便利な場所です。 ログは、File REST API を通してアプリケーション インスタンスによって書き込まれ、開発者は、ローカル コンピューターにファイル共有をマウントすることによってログにアクセスできます。 これにより、柔軟性が向上するため、開発者は、使い慣れた既存のツールを引き続き使用して、クラウド開発を行えます。

    * **開発/テスト/デバッグ**:  
        クラウド内の VM で作業している開発者または管理者には、一連のツールまたはユーティリティが必要になることがよくあります。 このようなユーティリティやツールを各 VM にコピーする作業には時間がかかります。 ローカルから VM に Azure ファイル共有をマウントすることによって、開発者や管理者はユーティリティやツールにすばやくアクセスでき、コピーする必要がなくなります。
* **コンテナー化**:  
    Azure ファイル共有は、ステートフル コンテナーの永続ボリュームとして使用できます。 コンテナーによって、開発者によるイノベーションを促進する "build once, run anywhere" (1 回のビルドで、どこでも実行できる) 機能を実現できます。 開始時に毎回、生データにアクセスするコンテナーの場合は、これらのコンテナーが実行されているインスタンスにかかわらずファイル システムにアクセスできるように、共有ファイル システムを使用する必要があります。

## <a name="key-benefits"></a>主な利点
* **共有アクセス**。 Azure ファイル共有では、業界標準の SMB プロトコルおよび NFS プロトコルがサポートされています。そのため、アプリケーションの互換性を気にせずに、オンプレミスのファイル共有を Azure ファイル共有にシームレスに置き換えることができます。 複数のマシン、アプリケーション/インスタンスでファイル システムを共有できる Azure Files は、共有性を必要とするアプリケーションにとって、大きな利点になります。 
* **フル マネージド**。 Azure ファイル共有は、ハードウェアまたは OS を管理することなく、作成できます。 つまり、重要なセキュリティ アップグレードの際にサーバー OS に修正プログラムを適用したり、故障したハード ディスクを交換したりする作業は必要ありません。
* **スクリプトとツール**。 PowerShell コマンドレットと Azure CLI を使用して、Azure アプリケーションの管理の一環として Azure ファイル共有を作成、マウント、管理できます。 Azure portal と Azure Storage Explorer を使用して、Azure ファイル共有を作成および管理できます。 
* **回復性**。 Azure Files は、当初から、常に使用できることを目的にして構築されています。 オンプレミスのファイル共有を Azure Files に置き換えることで、ローカルの停電またはネットワークの問題に対処するために夜間に起き出す必要がなくなります。 
* **慣れているプログラミング方法**。 Azure で実行されているアプリケーションは、ファイル [システム I/O API](/dotnet/api/system.io.file) を介して共有内のデータにアクセスできます。 そのため、開発者は、既存のコードとスキルを活用して、既存のアプリケーションを移行することができます。 システム IO API の他に、[Azure Storage クライアント ライブラリ](/previous-versions/azure/dn261237(v=azure.100))または [Azure Storage REST API](/rest/api/storageservices/file-service-rest-api) も使用できます。

## <a name="next-steps"></a>次の手順
* [使用可能なファイル共有プロトコルの学習](storage-files-compare-protocols.md)
* [Azure ファイル共有の作成](storage-how-to-create-file-share.md)
* [Windows での SMB 共有の接続とマウント](storage-how-to-use-files-windows.md)
* [Linux での SMB 共有の接続とマウント](storage-how-to-use-files-linux.md)
* [macOS での SMB 共有の接続とマウント](storage-how-to-use-files-mac.md)
* [NFS 共有を作成する方法](storage-files-how-to-create-nfs-shares.md)
