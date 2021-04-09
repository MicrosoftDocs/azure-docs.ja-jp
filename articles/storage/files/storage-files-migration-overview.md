---
title: Azure ファイル共有への移行
description: Azure ファイル共有への移行について説明し、移行ガイドを紹介します。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 27056f39885949d52c9fcc0d1472033cfc8f9aa0
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554872"
---
# <a name="migrate-to-azure-file-shares"></a>Azure ファイル共有への移行

この記事では、Azure ファイル共有への移行の基本的な側面を取り上げます。

この記事には、移行の基本と移行ガイドの表が含まれています。 これらのガイドは、ファイルを Azure ファイル共有に移動するのに役立ちます。 ガイドは、データの場所と、移動先のデプロイメント モデル (クラウドのみまたはハイブリッド) に基づいて整理されています。

## <a name="migration-basics"></a>移行の基本

Azure には、複数の種類のクラウド ストレージが用意されています。 Azure へのファイル移行の基本的側面の 1 つは、お使いのデータに適した Azure ストレージ オプションを見極めることです。

[Azure ファイル共有](storage-files-introduction.md)は、汎用ファイル データに適しています。 このデータには、オンプレミスの SMB または NFS 共有を使用する対象となるあらゆるデータが含まれています。 [Azure File Sync](storage-sync-files-planning.md) を使用すると、Windows サーバーが実行中のオンプレミス サーバーに複数の Azure ファイル共有のコンテンツをキャッシュできます。

オンプレミス サーバーで現在実行中のアプリケーションの場合、ファイルを Azure ファイル共有に格納することが適している場合もあります。 アプリケーションを Azure に移動し、Azure ファイル共有を共有ストレージとして使用できます。 このシナリオでは、[Azure Disk](../../virtual-machines/managed-disks-overview.md) を考慮することもできます。

クラウド アプリケーションによっては、SMB、マシンローカル データ アクセス、および共有アクセスに依存しないものもあります。 これらのアプリでは、[Azure BLOB](../blobs/storage-blobs-overview.md) のようなオブジェクト ストレージが多くの場合に最適です。

すべての移行で重要なことは、ファイルを現在の保存場所から Azure に移動するときに、該当するファイルのすべての忠実性を取得することです。 Azure ストレージ オプションがサポートする忠実性の程度と、シナリオで必要となる忠実性の程度も、適切な Azure ストレージを選択するのに役立ちます。 汎用ファイル データは従来、ファイル メタデータに依存しています。 アプリ データはそうではない場合があります。

ファイルの基本的なコンポーネントは次の 2 つです。

- **データ ストリーム**: ファイルのデータ ストリームにはファイル コンテンツが格納されます。
- **ファイル メタデータ**: ファイル メタデータには次のサブコンポーネントがあります。
   * ファイル属性 (読み取り専用など)
   * ファイルのアクセス許可。"*NTFS アクセス許可*" または "*ファイルとフォルダーの ACL*" と呼ばれることがあります
   * タイムスタンプ。特に作成時と最終更新時のタイムスタンプ
   * 代替データ ストリーム。これは、より多くの非標準プロパティを格納するための領域です

移行におけるファイルの忠実性は、次の機能として定義できます。

- ソースについての該当するすべてのファイル情報を格納する。
- 移行ツールを使用してファイルを転送する。
- 移行先のターゲット ストレージにファイルを格納する。

移行を円滑に進めるために、[ニーズに合った最適なコピー ツール](#migration-toolbox)を特定し、ストレージ ターゲットをソースと一致させます。

上記の内容を考慮すると、Azure の汎用ファイルのターゲット ストレージは [Azure ファイル共有](storage-files-introduction.md)であることがわかります。

Azure BLOB のオブジェクト ストレージとは異なり、Azure ファイル共有はファイル メタデータをネイティブに格納できます。 また、Azure ファイル共有では、ファイルとフォルダーの階層、属性、およびアクセス許可も保持されます。 ファイルとフォルダーはオンプレミスであるため、NTFS アクセス許可をそれらに保存できます。

オンプレミス ドメイン コントローラーである Active Directory のユーザーは、Azure ファイル共有にネイティブにアクセスできます。 Azure Active Directory Domain Services (Azure AD DS) のユーザーも同様です。 それぞれのユーザーは現在の ID を使用して、共有のアクセス許可およびファイルとフォルダーの ACL に基づいてアクセスを取得します。 この動作は、オンプレミスのファイル共有に接続するユーザーに似ています。

現時点で Azure ファイル共有のファイルに保存できないファイルの忠実性の主な側面が、代替データ ストリームです。 これは、Azure File Sync が使用されているときはオンプレミスで保持されます。

Azure ファイル共有の [Azure AD 認証](storage-files-identity-auth-active-directory-enable.md)および [Azure AD DS 認証](storage-files-identity-auth-active-directory-domain-service-enable.md)に関する記事を参照してください。

## <a name="migration-guides"></a>移行ガイド

次の表に詳細な移行ガイドを示します。

この表の使用方法

1. ファイルが現在保存されているソースシステムの行を探します。

1. 次のいずれかのターゲットを選択します。

   - Azure ファイル共有のコンテンツをオンプレミスでキャッシュするために Azure File Sync を使用したハイブリッド デプロイ
   - クラウド内の Azure ファイル共有

   選択した内容に一致するターゲット列を選択します。

1. ソースとターゲットが交差するテーブル セルに、使用可能な移行シナリオが示されています。 選択してリンクをクリックすると、詳細な移行ガイドに直接移動します。

シナリオにリンクがない場合は、公開されている移行ガイドがまだありません。 この表を定期的にチェックして、更新の有無をご確認ください。 新しいガイドは、入手可能になった時点で公開されます。

| source | ターゲット: </br>ハイブリッド デプロイ | ターゲット: </br>クラウドのみのデプロイ |
|:---|:--|:--|
| | ツールの組み合わせ:| ツールの組み合わせ: |
| Windows Server 2012 R2 以降 | <ul><li>[Azure File Sync](storage-sync-files-deployment-guide.md)</li><li>[Azure File Sync と Azure DataBox](storage-sync-offline-data-transfer.md)</li></ul> | <ul><li>RoboCopy を使用して、マウントされた Azure ファイル共有へ</li><li>Azure File Sync の使用</li></ul> |
| Windows Server 2012 以前 | <ul><li>DataBox と Azure File Sync を使用して最近のサーバー OS へ</li><li>記憶域移行サービス経由で Azure File Sync を使用して最近のサーバーへ、その後アップロード</li></ul> | <ul><li>記憶域移行サービス経由で Azure File Sync を使用して最近のサーバーへ</li><li>RoboCopy を使用して、マウントされた Azure ファイル共有へ</li></ul> |
| ネットワーク接続ストレージ (NAS) | <ul><li>[Azure File Sync のアップロードの使用](storage-files-migration-nas-hybrid.md)</li><li>[DataBox および Azure File Sync の使用](storage-files-migration-nas-hybrid-databox.md)</li></ul> | <ul><li>RoboCopy を使用して、マウントされた Azure ファイル共有へ</li></ul> |
| Linux/Samba | <ul><li>[Azure File Sync と RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>RoboCopy を使用して、マウントされた Azure ファイル共有へ</li></ul> |
| Microsoft Azure StorSimple クラウド アプライアンス 8100 または StorSimple クラウド アプライアンス 8600 | <ul><li>[専用のデータ移行クラウド サービスの使用](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple クラウド アプライアンス 1200 | <ul><li>[Azure File Sync の使用](storage-files-migration-storsimple-1200.md)</li></ul> | |

## <a name="migration-toolbox"></a>移行ツールボックス

### <a name="file-copy-tools"></a>ファイル コピー ツール

Microsoft およびその他のファイル コピー ツールがいくつか用意されています。 移行シナリオで適切なツールを選択するには、次の 3 つの基本的な質問について検討する必要があります。

* ツールによって、ファイル コピーのソースとターゲットの場所がサポートされているか。

* ソースとターゲットの保存場所の間のネットワーク パスまたは使用可能なプロトコル (REST、SMB、NFS など) がサポートされているか。

* ツールによって、ソースとターゲットの場所でサポートされている必要なファイルの忠実性が保持されるか。

    場合によっては、ターゲット ストレージで、ソースと同じ忠実性がサポートされていないことがありますが、 ターゲット ストレージが必要性を十分に満たすものである場合、ツールはターゲットのファイル忠実性の機能にのみ適合する必要があります。

* ツールには、移行戦略にツールを適合させるための機能があるか。

    たとえば、ツールでダウンタイムを最小化できるかどうかを検討します。
    
    ソースをターゲットにミラーリングするオプションがツールでサポートされている場合、ソースにアクセスできる間に通常は同じソースおよびターゲットでツールを複数回実行できます。

    ツールを最初に実行するときに、データの大部分がコピーされます。 この最初の実行は、しばらく時間がかかる場合があります。 多くの場合、ビジネス プロセスのためにデータ ソースをオフラインにするのに要する時間よりも長くかかります。

    ソースをターゲットにミラーリングすることにより (**robocopy /MIR** と同様)、同じソースとターゲットでツールを再度実行できます。 前回の実行後に発生したソースの変更のみを転送する必要があるため、今回の実行はずっと速くなります。 この方法でコピー ツールを再実行すると、ダウンタイムを大幅に短縮できます。

次の表は、Microsoft ツールと、Azure ファイル共有との現時点での適合性を分類したものです。

| 推奨 | ツール | Azure ファイル共有のサポート | ファイルの忠実性の保持 |
| :-: | :-- | :---- | :---- |
|![はい、推奨されます](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | サポートされています。 Azure ファイル共有は、ネットワーク ドライブとしてマウントできます。 | 完全な忠実性。* |
|![はい、推奨されます](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure File Sync | Azure ファイル共有にネイティブに統合されます。 | 完全な忠実性。* |
|![はい、推奨されます](media/storage-files-migration-overview/circle-green-checkmark.png)| 記憶域移行サービス | 間接的にサポートされています。 Azure ファイル共有は、SMS ターゲット サーバーでネットワーク ドライブとしてマウントできます。 | 完全な忠実性。* |
|![はい、推奨されます](media/storage-files-migration-overview/circle-green-checkmark.png)| AzCopy </br>バージョン 10.6 | サポートされています。 | ソース ルート ACL のコピーはサポートされていません。その他の点では、完全な忠実性があります。 * </br>[Azure ファイル共有で AzCopy を使用する方法を確認する](../common/storage-use-azcopy-files.md) |
|![はい、推奨されます](media/storage-files-migration-overview/circle-green-checkmark.png)| Data Box | サポートされています。 | DataBox では、メタデータが完全にサポートされています。 |
|![完全にはお勧めできません](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Azure ストレージ エクスプローラー </br>バージョン 1.14 | サポートされています。 | ACL はコピーされません。 タイムスタンプがサポートされます。  |
|![推奨されません](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | サポートされています。 | メタデータがコピーされません。 |
|||||

" *\* 完全な忠実性: Azure ファイル共有の機能を満たすか上回る。* "

### <a name="migration-helper-tools"></a>移行ヘルパー ツール

このセクションでは、移行の計画と実行に役立つツールについて説明します。

#### <a name="robocopy-from-microsoft-corporation"></a>Microsoft Corporation 製の RoboCopy

RoboCopy は、ファイルの移行に最も適しているツールの 1 つです。 これは Windows の一部として提供されます。 このツールの多くのオプションについては、主要な [RoboCopy ドキュメント](/windows-server/administration/windows-commands/robocopy)がリソースとして役に立ちます。

#### <a name="treesize-from-jam-software-gmbh"></a>JAM Software GmbH 製の TreeSize

Azure File Sync は、合計ストレージ容量ではなく、主に項目 (ファイルとフォルダー) の数に基づいてスケーリングされます。 TreeSize ツールを使用すると、Windows Server ボリューム上の項目数を確認できます。

このツールを使用すると、[Azure File Sync のデプロイ](storage-sync-files-deployment-guide.md)の前にパースペクティブを作成できます。 デプロイ後にクラウドの階層化が使用されている場合にも使用できます。 このシナリオでは、項目数と、サーバー キャッシュを最も使用するディレクトリが表示されます。

このツールのテスト済みのバージョンはバージョン 4.4.1 です。 クラウド階層化ファイルと互換性があります。 通常の操作中に、階層化ファイルがこのツールによって再度呼び出されることはありません。

## <a name="next-steps"></a>次のステップ

1. 必要な Azure ファイル共有 (クラウドのみまたはハイブリッド) のデプロイに適したプランを作成します。
1. 使用可能な移行ガイドの一覧を確認して、Azure ファイル共有のご自身のソースとデプロイに合った詳細なガイドを見つけます。

この記事に記載した Azure Files テクノロジの詳細情報:

* [Azure ファイル共有の概要](storage-files-introduction.md)
* [Azure File Sync のデプロイの計画](storage-sync-files-planning.md)
* [Azure File Sync: クラウドを使った階層化](storage-sync-cloud-tiering-overview.md)