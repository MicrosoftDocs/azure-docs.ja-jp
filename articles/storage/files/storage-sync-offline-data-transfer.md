---
title: Azure Data Box を使用して Azure File Sync にデータを移行する
description: Azure File Sync と互換性のある方法で大量のデータをオフラインで移行します。同期を有効にした後で、ファイルの競合を回避し、ファイルとフォルダーの ACL およびタイムスタンプを保持します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 94abb33d39765a19306a013576d43fb2602d1c37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017629"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>Azure Data Box を使用して Azure File Sync に大量のデータを移行する
次に示す 2 つの方法で Azure File Sync に大量のデータを移行できます。

* **Azure File Sync を使用してファイルをアップロードする。** これは最もシンプルな方法です。 ファイルをローカルで Windows Server 2012 R2 以降に移動し、Azure File Sync エージェントをインストールします。 同期を設定すると、サーバーからファイルがアップロードされます (現在、お客様の平均アップロード速度は、約 2 日ごとに 1 TiB です)。サーバーがデータセンター用に大量の帯域幅を使用しないように、[帯域幅調整スケジュール](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)を設定する必要があります。
* **ファイルをオフラインで転送する。** 十分な帯域幅がない場合は、合理的な時間内にファイルを Azure にアップロードできない可能性があります。 課題はファイル セット全体の初期同期です。 この課題を克服するために、[Azure Data Box ファミリ](https://azure.microsoft.com/services/storage/databox)などのオフライン一括移行ツールを使用します。 

この記事では、Azure File Sync と互換性のある方法でファイルをオフライン移行する方法について説明します。同期を有効にした後で、以下の手順に従って、ファイルの競合を回避し、ファイルとフォルダーのアクセス制御リスト (ACL) とタイムスタンプを保持してください。

## <a name="migration-tools"></a>移行ツール
この記事で説明するプロセスは、Data Box だけでなく、その他のオフライン移行ツールにも対応しています。 また、インターネットでそのまま動作する AzCopy、Robocopy などのツールや、パートナーのツールとサービスにも対応しています。 最初のアップロードの課題を克服した場合でも、この記事に記載されている手順に従って、Azure File Sync と互換性のある方法でこれらのツールを使用してください。

Azure File Sync を導入する前に、Windows Server 間で移動しなければならないケースもあります。そのような場合には [Storage Migration Service](/windows-server/storage/storage-migration-service/overview) (SMS) が役立つ可能性があります。 Azure File Sync でサポートされるバージョンのサーバー OS (Windows Server 2012R2 以上) が必要であったり、単純に Azure File Sync 用の新しいシステムを購入するなど、移行の理由が何であれ、円滑な移行を支援するさまざまな機能と利点を SMS は備えています。

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>ツールを使用してデータをオフライン転送する利点
Data Box などの転送ツールをオフライン移行に使用する主な利点は次のとおりです。

- ネットワーク経由ですべてのファイルをアップロードする必要はありません。 大規模な名前空間の場合、このツールによりネットワークの帯域幅と時間を大幅に節約できます。
- Azure File Sync を使用すると、使用する転送ツール (Data Box、Azure Import/Export サービスなど) に関係なく、ライブ サーバーは、ユーザーが Azure にデータを移動した後に変更されたファイルのみをアップロードします。
- Azure File Sync を使用すると、オフラインの一括移行ツールによって ACL が転送されない場合でも、ファイルとフォルダーの ACL が確実に同期されます。
- Data Box と Azure File Sync を使用すると、ダウンタイムは発生しません。 Data Box を使用して Azure にデータを転送すると、ネットワーク帯域幅を効率的に使用し、ファイルの忠実性を維持できます。 また、ユーザーが Azure にデータを移動した後に変更されたファイルのみをアップロードすることによって、名前空間を最新の状態に保ちます。

## <a name="prerequisites-for-the-offline-data-transfer"></a>オフライン データ転送の前提条件
オフライン データ転送を完了する前に、移行するサーバーでの同期を有効にする必要があります。 開始する前の考慮すべきその他の事項は次のとおりです。

- 一括移行に Data Box を使用する予定の場合は、[Data Box のデプロイの前提条件](../../databox/data-box-deploy-ordered.md#prerequisites)に関する説明を確認してください。
- 最終的な Azure File Sync トポロジを計画します:[Azure ファイル同期のデプロイの計画](storage-sync-files-planning.md)
- 同期するファイル共有を保持する Azure ストレージ アカウントを選択します。 一括移行が、同じストレージ アカウント内の一時的なステージング共有に対して行われるようにしてください。 一括移行は、同じストレージ アカウントに存在する最終およびステージングの共有を使用してのみ有効にできます。
- 一括移行は、サーバーの場所との新しい同期関係を作成するときにのみ使用できます。 既存の同期関係で一括移行を有効にすることはできません。


## <a name="process-for-offline-data-transfer"></a>オフライン データ転送のプロセス
ここでは、Azure Data Box などの一括移行ツールと互換性のある方法で Azure File Sync を設定する方法について説明します。

![Azure File Sync の設定方法を示す図](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| 手順 | Detail |
|---|---------------------------------------------------------------------------------------|
| ![手順 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [お客様の Data Box を注文します](../../databox/data-box-deploy-ordered.md)。 お客様のニーズに対応するため、Data Box ファミリには[いくつかの製品](https://azure.microsoft.com/services/storage/databox/data)が用意されています。 Data Box を受け取ったら、Data Box 上の UNC パス ( *\\<DeviceIPAddres\>\<StorageAccountName_AzFile\>\<ShareName\>* ) に、[ドキュメントの記載に従ってデータをコピー](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box)します。 ここで、*ShareName* はステージング共有の名前です。 Data Box を Azure に送り返します。 |
| ![手順 2.](media/storage-sync-files-offline-data-transfer/bullet_2.png) | 一時的なステージング共有として選択した Azure ファイル共有にファイルが表示されるまで待ちます。 *それらの共有への同期を有効にしないでください。* |
| ![手順 3.](media/storage-sync-files-offline-data-transfer/bullet_3.png) | <ul><li>Data Box によって作成されたファイル共有ごとに、空の新しい共有を作成します。 この新しい共有は Data Box 共有と同じストレージ アカウントにある必要があります。 [新しい Azure ファイル共有を作成する方法](storage-how-to-create-file-share.md)。</li><li>ストレージ同期サービスで[同期グループを作成](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint)します。 空の共有をクラウド エンドポイントとして参照します。 Data Box のファイル共有ごとにこの手順を繰り返します。 [Azure File Sync を設定します](storage-sync-files-deployment-guide.md)。</li></ul> |
| ![手順 4.](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [ライブ サーバー ディレクトリをサーバー エンドポイントとして追加](storage-sync-files-deployment-guide.md#create-a-server-endpoint)します。 このプロセスでは、Azure にファイルを移動したことを指定し、ステージング共有を参照します。 必要に応じて、クラウドの階層化を有効または無効にできます。 ライブ サーバー上でサーバー エンドポイントを作成している間は、ステージング共有を参照します。 **[サーバー エンドポイントの追加]** ブレードの **[オフラインのデータ転送]** で、 **[有効]** を選択し、クラウド エンドポイントと同じストレージ アカウントに存在する必要があるステージング共有を選択します。 ここで、利用可能な共有の一覧は、まだ同期されていないストレージ アカウントと共有によってフィルター処理されます。 この表の後のスクリーンショットでは、Azure portal でサーバー エンドポイントを作成するときに、DataBox 共有を参照する方法を示します。 |
| ![手順 5.](media/storage-sync-files-offline-data-transfer/bullet_5.png) | 前の手順でサーバー エンドポイントを追加すると、データは適切なソースから自動的に送信されます。 「[共有の同期](#syncing-the-share)」セクションでは、データが DataBox 共有または Windows Server から送信されるタイミングについて説明しています。 |
| |

![新しいサーバー エンドポイントを作成するときにオフライン データ転送を有効にする方法を示す、Azure portal ユーザー インターフェイスのスクリーンショット](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>共有の同期
サーバー エンドポイントを作成すると、同期が開始されます。 同期プロセスでは、サーバー上の各ファイルが、Data Box がファイルを配置したステージング共有にも存在するかどうかが判断されます。 ファイルが存在する場合、同期プロセスでは、サーバーからファイルがアップロードされるのではなく、ステージング共有からファイルがコピーされます。 ファイルがステージング共有に存在しない場合、またはローカル サーバーで新しいバージョンが利用可能な場合は、同期プロセスによってローカル サーバーからファイルがアップロードされます。

共有を同期すると、存在しないファイル属性、アクセス許可、またはタイムスタンプがローカル サーバー上のファイルのバリアントからマージされ、DataBox 共有にあるそのファイルに対応するファイルと結合されます。 これにより、各ファイルとフォルダーは、Azure ファイル共有内のすべてのファイルの忠実性を保持した状態になります。

> [!IMPORTANT]
> 一括移行モードは、サーバー エンドポイントの作成中にのみ有効にできます。 サーバー エンドポイントの確立後、既に同期されているサーバーから名前空間に一括移行済みデータを統合することはできません。

## <a name="acls-and-timestamps-on-files-and-folders"></a>ファイルとフォルダーの ACL とタイムスタンプ
Azure File Sync は、使用した一括移行ツールが最初に ACL を転送しなかった場合でも、ファイルおよびフォルダーの ACL がライブ サーバーから確実に同期されるようにします。 そのため、ステージング共有にファイルやフォルダーの ACL が含まれている必要はありません。 新しいサーバー エンドポイントを作成するときにオフライン データ移行機能を有効にすると、すべてのファイルの ACL がサーバー上で同期されます。 新しく作成および変更されたタイムスタンプも同期されます。

## <a name="shape-of-the-namespace"></a>名前空間の形状
同期を有効にすると、サーバーのコンテンツによって名前空間の形状が決定されます。 Data Box のスナップショットと移行の完了後にファイルがローカル サーバーから削除された場合、それらのファイルがライブの同期名前空間に移動されることはありません。 それらはステージング共有に残りますが、コピーされることはありません。 これが必要なのは、同期でライブ サーバーに応じて名前空間が維持されるためです。 Data Box の "*スナップショット*" は、効率的なファイル コピーのための単なるステージング グラウンドです。 ライブ名前空間の形状に対する権限ではありません。

## <a name="cleaning-up-after-bulk-migration"></a>一括移行後のクリーンアップ 
サーバーが名前空間の初期同期を完了すると、Data Box の一括移行ファイルではステージング ファイル共有を使用します。 Azure portal の **[サーバー エンドポイントのプロパティ]** ブレードにある **[オフラインのデータ転送]** セクションでは、状態が **[進行中]** から **[完了]** に変わります。 

![オフライン データ転送の状態および無効化コントロールが配置されている [サーバー エンドポイントのプロパティ] ブレードのスクリーンショット](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

この段階では、次のようにしてステージング共有をクリーンアップしてコストを節約できます。

1. 状態が **[完了]** のときに、 **[サーバー エンドポイントのプロパティ]** ブレードで **[オフラインのデータ転送を無効にする]** を選択します。
2. コストを節約するためにステージング共有を削除することを検討します。 ステージング共有にファイルとフォルダーの ACL が含まれていることはほとんどないため、ステージング共有はそれほど役に立ちません。 バックアップの特定の時点の目的で、[同期する Azure ファイル共有の実際のスナップショット](storage-snapshots-files.md)を作成します。 スケジュールに基づいて[スナップショットを作成するように Azure Backup を設定する]( ../../backup/backup-afs.md)ことができます。

状態が **[完了]** の場合、または構成の誤りが原因で中止したい場合にのみ、オフライン データ転送モードを無効にしてください。 デプロイ時にこのモードを無効にすると、ステージング共有がまだ利用可能であっても、ファイルはサーバーからアップロードされ始めます。

> [!IMPORTANT]
> オフライン データ転送モードを無効にした後は、一括移行からのステージング共有がまだ利用可能であっても、オフライン データ転送モードを再度有効にすることはできません。

## <a name="azure-file-sync-and-pre-seeded-files-in-the-cloud"></a>Azure File Sync とクラウドに事前シード処理されたファイル

Azure ファイル共有で DataBox 以外の方法によってファイルをシード処理したことがある場合は、クラウド バックアップからの AzCopy や RoboCopy を始めとする方法を使用した場合でも、この記事で説明した[オフライン データ転送のプロセス](#process-for-offline-data-transfer)に従う必要があります。 ファイルをクラウドに移動する方法として、DataBox を無視する必要があるだけです。 ただし、最終的な Azure File Sync に接続された共有ではなく、*ステージング共有* にファイルをシード処理するプロセスに確実に従っていることが重要です。

> [!WARNING]
> 最終的な Azure File Sync に接続された共有ではなく、**ステージング共有にファイルをシード処理するプロセスに従ってください**。 そうしないと、ファイルの競合が発生する可能性があります (両方のファイル バージョンが格納されます)。また、ライブ サーバーで削除されたファイルが、古いシードされたファイルのセットにまだ存在する場合は、それが元に戻る可能性があります。 さらに、フォルダーの変更は相互にマージされるため、このようなミスの後は、名前空間を分離するのが非常に困難になります。

## <a name="next-steps"></a>次のステップ
- [Azure ファイル同期のデプロイの計画](storage-sync-files-planning.md)
- [Azure File Sync をデプロイする](storage-sync-files-deployment-guide.md)