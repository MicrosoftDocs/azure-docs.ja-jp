---
title: Azure DataBox による Azure File Sync へのオンプレミス NAS 移行
description: オンプレミスのネットワーク接続ストレージ (NAS) の場所から、Azure File Sync を使用したハイブリッド クラウド デプロイに、Azure DataBox によってファイルを移行する方法について説明します。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/5/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 144b2f23e40f315441c3de2482ae8aeffe77ec75
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583602"
---
# <a name="use-databox-to-migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>DataBox を使用して Azure File Sync によってネットワーク接続ストレージ (NAS) からハイブリッド クラウド デプロイに移行する

この移行に関する記事は、NFS、Azure File Sync、Azure DataBox というキーワードに関連するものの 1 つです。 この記事がご使用のシナリオに当てはまるかどうかを確認してください。

> [!div class="checklist"]
> * データ ソース: ネットワーク接続ストレージ (NAS)
> * 移行ルート: NAS &rArr; DataBox &rArr; Azure ファイル共有 &rArr; Windows Server と同期
> * オンプレミスのファイルのキャッシュ: はい、最終的な目標は Azure File Sync のデプロイです。

自分のシナリオが異なる場合は、[移行ガイドの表](storage-files-migration-overview.md#migration-guides)を参照してください。

Azure File Sync は直接接続記憶域 (DAS) の場所で動作し、ネットワーク接続ストレージ (NAS) の場所への同期をサポートしていません。
このため、ファイルの移行が必要になります。この記事では、そのような移行の計画と実行について説明します。

## <a name="migration-goals"></a>移行の目標

目標は、NAS アプライアンス上にある共有を Windows Server に移行することです。 その後は、ハイブリッド クラウド デプロイに Azure File Sync を使用します。 この移行は、運用データの整合性と、移行中の可用性が保証される方法で行う必要があります。 後者については、ダウンタイムを最小限に抑えて、通常のメンテナンス期間に収まるか、わずかに超えるだけで済むようにする必要があります。

## <a name="migration-overview"></a>移行の概要

移行プロセスは、いくつかのフェーズで構成されます。 Azure ストレージ アカウントとファイル共有をデプロイし、オンプレミスの Windows Server をデプロイし、Azure File Sync を構成し、RoboCopy を使用して移行し、最後にカットオーバーする必要があります。 以下のセクションでは、移行プロセスの各フェーズについて詳しく説明します。

> [!TIP]
> この記事に戻った場合は、右側にあるナビゲーションを使用して、中断した移行フェーズに移動してください。

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>フェーズ 1:必要な Azure ファイル共有の数を特定する

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>フェーズ 2: Azure ストレージ リソースをデプロイする

このフェーズでは、フェーズ 1 のマッピング テーブルを参照し、それを使用して、適切な数の Azure ストレージ アカウントと、その中のファイル共有をプロビジョニングします。

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-databox-appliances-you-need"></a>フェーズ 3: 必要な Azure DataBox アプライアンスの数を決定する

前のフェーズを完了したときにのみ、この手順を開始してください。 この時点で、Azure ストレージ リソース (ストレージ アカウントとファイル共有) が作成されているはずです。 DataBox を注文するときに、DataBox でのデータの移動先となるストレージ アカウントを指定する必要があります。

このフェーズでは、前のフェーズからの移行プランの結果を、使用可能な DataBox オプションの制限にマッピングする必要があります。 これらの考慮事項は、選択する必要がある DataBox オプションと、それらのうちいくつが NAS 共有を Azure ファイル共有に移動するために必要になるかについて計画を立てるのに役立ちます。

必要なデバイスの種類と数を決定する際、次の重要な制限事項を考慮してください。

* すべての Azure DataBox は、最大 10 個のストレージ アカウントにデータを移動できます。 
* DataBox の各オプションには、それら独自の使用可能な容量があります。 「[DataBox のオプション](#databox-options)」を参照してください。

作成することを決定したストレージ アカウントの数とそれぞれの共有については、移行プランを参照してください。 次に、NAS 上でそれぞれの共有のサイズを確認します。 この情報を組み合わせることで、どのアプライアンスからどのストレージ アカウントにデータを送信するかを最適化し、決定することができます。 2 つの DataBox デバイスから、ファイルを同じストレージ アカウントに移動することはできますが、1 つのファイル共有のコンテンツを 2 つの DataBox に分割しないでください。

### <a name="databox-options"></a>DataBox のオプション

標準的な移行の場合、次に示す 3 種類の DataBox のオプションから、いずれか、または組み合わせて選択する必要があります。 

* DataBox Disks: 容量が 8 TiB の SSD ディスクが 1 台から最大 5 台 (最大で合計 40 TiB) が Microsoft からお客様に発送されます。 暗号化とファイル システムのオーバーヘッドにより、使用可能な容量は約 20% 少なくなります。 詳細については、[DataBox Disks のドキュメント](../../databox/data-box-disk-overview.md)を参照してください。
* DataBox: これが最も一般的なオプションです。 NAS と同様に機能する、耐久性のある DataBox アプライアンスがお客様に発送されます。 使用可能な容量は 80 TiB です。 詳細については、[DataBox のドキュメント](../../databox/data-box-overview.md)をご覧ください。
* DataBox Heavy: このオプションは、NAS と同様に機能する、耐久性のある車輪付きの DataBox アプライアンスで、容量は 1 PiB です。 暗号化とファイル システムのオーバーヘッドにより、使用可能な容量は約 20% 少なくなります。 詳細については、[DataBox Heavy のドキュメント](../../databox/data-box-heavy-overview.md)を参照してください。

## <a name="phase-4-provision-a-suitable-windows-server-on-premises"></a>フェーズ 4: オンプレミスで適切な Windows Server をプロビジョニングする

Azure DataBox が届くのを待っている間に、Azure File Sync で使用する 1 つ以上の Windows Server のニーズに関する確認を先に始めることができます。

* 仮想マシンまたは物理サーバーとして、Windows Server 2019 (最低でも 2012R2) のサーバーを作成します。 Windows Server フェールオーバー クラスターもサポートされています。
* 直接接続記憶域 (NAS ではなく DAS、NAS はサポートされていません) をプロビジョニングまたは追加します。

デプロイする Windows Server のリソース構成 (コンピューティングと RAM) は、主に同期する項目 (ファイルとフォルダー) の数によって変わります。 ご心配な場合は、より高いパフォーマンス構成を使用することをお勧めします。

[同期する必要がある項目 (ファイルとフォルダー) の数に基づいて Windows サーバーのサイズを変更する方法を参照してください。](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> 前記のリンク先の記事では、サーバー メモリ (RAM) の範囲に関する表が示されています。 サーバーの数を少なくすることはできますが、初期同期にはより長い時間かかることが予想されます。

## <a name="phase-5-copy-files-onto-your-databox"></a>フェーズ 5: ファイルを DataBox にコピーする

DataBox が届いたら、NAS アプライアンスから認識できる経路に DataBox をセットアップする必要があります。 注文した種類の DataBox のセットアップ ドキュメントに従ってください。

* [Data Box を設定する](../../databox/data-box-quickstart-portal.md)
* [Data Box Disk を設定する](../../databox/data-box-disk-quickstart-portal.md)
* [Data Box Heavy を設定する](../../databox/data-box-heavy-quickstart-portal.md)

DataBox の種類によっては、DataBox コピー ツールが使用できる場合もあります。 現時点では、これらはファイルを完全な忠実性で DataBox にコピーするものではないため、Azure ファイル共有への移行には推奨されません。 代わりに RoboCopy を使用してください。

DataBox が届くと、注文時に指定したストレージ アカウントごとに事前プロビジョニングされた SMB 共有が利用できます。

* ファイルが Premium の Azure ファイル共有に配置される場合は、Premium の "ファイル ストレージ" のストレージ アカウントごとに 1 つの SMB 共有が存在します。
* ファイルが Standard ストレージ アカウントに配置される場合、Standard の (GPv1 および GPv2) ストレージ アカウントごとに 3 つの SMB 共有が存在します。 `_AzFiles` で終わるファイル共有のみが、移行に関連します。 すべてのブロックおよびページの BLOB 共有を無視します。

Azure DataBox のドキュメントの手順に従ってください。

1. [Data Box に接続する](../../databox/data-box-deploy-copy-data.md)
1. Data Box にデータをコピーする
1. [Azure の出発点として DataBox を準備する](../../databox/data-box-deploy-picked-up.md)

リンクされた DataBox ドキュメントには、RoboCopy コマンドが指定されています。 ただし、このコマンドは、ファイルとフォルダーの完全な忠実性を維持するのに適していません。 代わりに次のコマンドを使用します。

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]


## <a name="phase-6-deploy-the-azure-file-sync-cloud-resource"></a>フェーズ 6: Azure File Sync クラウド リソースをデプロイする

このガイドを続行する前に、すべてのファイルが適切な Azure ファイル共有に到達するまで待ちます。 DataBox データの発送と取り込みの処理には時間がかかります。

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-7-deploy-the-azure-file-sync-agent"></a>フェーズ 7: Azure File Sync エージェントをデプロイする

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-8-configure-azure-file-sync-on-the-windows-server"></a>フェーズ 8: Windows Server 上で Azure File Sync を構成する

このプロセスのために、登録済みのオンプレミス Windows サーバーを準備し、インターネットに接続しておく必要があります。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

クラウドを使った階層化機能を有効にし、最初のダウンロード セクションで [Namespace only]\(名前空間のみ\) を選択します。

> [!IMPORTANT]
> クラウドを使った階層化は、ローカル サーバーが、クラウドに格納されているよりもストレージ容量が少なくても、完全な名前空間を使用できるようになる AFS 機能です。 ローカル環境で関心のあるデータも、アクセスのパフォーマンスを上げるためにローカルにキャッシュされます。 クラウドを使った階層化は、Azure File Sync の "サーバー エンドポイント" ごとのオプション機能です。 この機能を使用する必要があるのは、すべてのクラウド データを保持するのに十分なローカル ディスク容量が Windows Server にない場合と、クラウドからすべてのデータはダウンロードしないようにする場合です。

同期グループ作成の手順と、すべての Azure ファイル共有とサーバーの場所に対するサーバー エンドポイントとしての一致するサーバー フォルダーの追加の手順を繰り返します。これは、同期用に構成するために必要です。名前空間の同期が完了するまで待ちます。 次のセクションで、これを確認する方法について詳しく説明します。

> [!NOTE]
> サーバー エンドポイントの作成後、同期は機能しています。 ただし、同期では DataBox を介して Azure ファイル共有に移動したファイルとフォルダーを列挙 (検出) する必要があります。 名前空間のサイズによっては、クラウドの名前空間がサーバーに表示されるまでにかなりの時間がかかることがあります。

## <a name="phase-9-wait-for-the-namespace-to-fully-appear-on-the-server"></a>フェーズ 9: 名前空間がサーバーに完全に表示されるまで待機する

移行の次の手順では、サーバーでクラウド共有から名前空間を完全にダウンロードするのを待つことが絶対に必要です。 サーバーへのファイルの移動の開始が早すぎる場合、不要なアップロードや、さらにはファイル同期の競合のリスクを冒している可能性があります。

サーバーで初回ダウンロード同期が完了したかどうかを確認するには、同期している Windows Server でイベント ビューアーを開き、Azure File Sync テレメトリ イベント ログを使用します。
テレメトリ イベント ログは、イベント ビューアーの Applications and Services\Microsoft\FileSync\Agent にあります。

最新の 9102 イベントを検索します。 同期セッションが完了すると、イベント ID 9102 がログに記録されます。 イベント テキストには、ダウンロード同期の方向のフィールドがあります。 (`HResult` が 0 で、ファイルがダウンロード済みの必要があります)。
 
サーバーで名前空間のダウンロードが完了したことを知るには、この種類とコンテンツの 2 つの連続するイベントを確認する必要があります。 2 つの 9102 イベントの間に異なるイベントが発生していても問題ありません。

## <a name="phase-10-catch-up-robocopy-from-your-nas"></a>フェーズ 10: NAS からのキャッチアップ RoboCopy を実行する

ご使用のサーバーで、クラウド共有からの名前空間全体の初期同期が完了したら、この手順に進むことができます。 この手順を続行するには、上記の手順が完了していることが絶対に必要です。 詳細については、前のセクションを参照してください。

この手順では、共有を DataBox.にフォークした時点よりも後に NAS の最新の変更内容を使用してクラウド共有をキャッチアップするために、RoboCopy ジョブを実行します。
このキャッチアップ RoboCopy は、NAS 共有で発生したチャーンの量によって、短時間で終了したり、しばらく時間がかかったりすることがあります。

> [!WARNING]
> Windows Server 2019 では RoboCopy の動作が機能低下したため、RoboCopy の/MIR スイッチは階層化されたターゲット ディレクトリに対応していません。 移行のこのフェーズでは、Windows Server 2019 および Windows 10 クライアントを使用しないでください。 中間の Windows Server 2016 で RoboCopy を使用してください。

基本的な移行方法は、NAS アプライアンスから Windows Server へは RoboCopy で、Azure ファイル共有へは Azure File Sync です。

Windows Server ターゲット フォルダーへの最初のローカル コピーを実行します。

1. NAS アプライアンス上で最初の場所を特定します。
1. Windows Server 上で、既に Azure File Sync が構成されている対応するフォルダーを特定します。
1. RoboCopy を使用してコピーを開始します

次の RoboCopy コマンドを実行すると、NAS ストレージから Windows Server のターゲット フォルダーに差分 (更新されたファイルおよびフォルダー) のみがコピーされます。 その後、Windows Server によってそれらが Azure ファイル共有に同期されます。 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

NAS アプライアンスでファイルが占める量より少ないストレージを Windows Server にプロビジョニングした場合は、クラウドを使った階層化を構成してあります。 ローカル環境の Windows Server ボリュームがいっぱいになると、[クラウドを使った階層化](storage-sync-cloud-tiering-overview.md)により、既に正常に同期されているファイルの階層化が開始されます。 クラウドを使った階層化により、NAS アプライアンスからのコピーを続けるのに十分な領域が生成されます。 クラウドを使った階層化では、1 時間に 1 回、同期されたものが確認されて、ボリューム空き領域 99% になるようにディスク領域が解放されます。
RoboCopy では、Windows Server のローカル ストレージにあるよりも多くのファイルを移動しなければならないこともあります。 平均すると、RoboCopy での移動速度の方が、Azure File Sync でファイルをアップロードしてローカル ボリューム外に階層化するよりもずっと速いことが期待されます。 RoboCopy は失敗します。 そのようなことが起こらない順序で共有を処理することをお勧めします。 たとえば、すべての共有に対して RoboCopy ジョブを同時に開始しないようにしたり、Windows Server の現在の空き領域に収まる共有のみを移動したりするようにします。 良い点としては、/MIR スイッチでデルタのみが移動されるため、デルタが移動されると、再起動されたジョブによってこのファイルを再び移動しなくてもよいということがあります。

### <a name="user-cut-over"></a>ユーザーのカットオーバー

RoboCopy コマンドを初めて実行するときは、ユーザーとアプリケーションがまだ NAS 上のファイルにアクセスしていて、それを変更する可能性があります。 RoboCopy があるディレクトリを処理し、次のディレクトリに移動した後、ソースの場所 (NAS) のユーザーがファイルを追加、変更、または削除し、現在の RoboCopy の実行では処理されない可能性があります。 これは正しい動作です。

最初の実行では、チャーンされた大量のデータが Windows Server に移動され、その後 Azure File Sync 経由でクラウドに移動されます。この最初のコピーには、次の条件によっては時間がかかることがあります。

* アップロードの帯域幅
* ローカル ネットワークの速度と、それに合った最適な RoboCopy スレッドの数
* RoboCopy と Azure File Sync によって処理される必要がある項目 (ファイルとフォルダー) の数

最初の実行が完了した後、コマンドを再度実行します。

同じ同期に対して RoboCopy を 2 回目に実行したときは、前回の実行以降に発生した変更のみを転送すればよいため、処理は短時間で完了します。 同じ共有に対してジョブを繰り返し実行できます。

ダウンタイムを許容できる場合は、NAS ベースの共有へのユーザー アクセスを削除する必要があります。 これは、ユーザーがファイルとフォルダー構造およびコンテンツを変更できないようにするいずれの手順でも行えます。 たとえば、自分の DFS 名前空間を存在しない場所に指定したり、共有のルート ACL を変更します。

最後に 1 回 RoboCopy ラウンドを実行します。 それにより、漏れている可能性があるすべての変更が取得されます。
この最後の手順にかかる時間は、RoboCopy のスキャンの速度に依存します。 前回の実行にかかった時間を測定することで、(ダウンタイムに相当する) 時間を見積もることができます。

Windows Server フォルダーに共有を作成し、必要に応じて、その共有を指すように DFS-N のデプロイを調整します。 NAS SMB 共有と同じ共有レベルのアクセス許可を設定してください。 エンタープライズ クラスのドメイン参加 NAS を使用していた場合、ユーザーの SID は Active Directory に存在するユーザーと自動的に一致し、RoboCopy によってファイルとメタデータが完全に忠実にコピーされます。 NAS でローカル ユーザーを使用していた場合は、これらのユーザーを Windows Server のローカル ユーザーとして作成し直し、RoboCopy によって Windows Server に移動された既存の SID を、新しい Windows Server ローカル ユーザーの SID にマップする必要があります。

これで、共通のルートまたはボリュームへの共有または共有のグループの移行は完了しました。 (フェーズ 1 からのマッピングによって異なります)

これらの複数のコピーを並行して実行することができます。 一度に 1 つの Azure ファイル共有のスコープを処理することをお勧めします。

## <a name="troubleshoot"></a>トラブルシューティング

最も可能性の高い問題は、Windows Server 側で "*ボリュームがいっぱい*" になったために RoboCopy コマンドが失敗することです。 クラウドを使った階層化は 1 時間ごとに動作し、同期されたローカル環境の Windows Server ディスクから内容が退避されます。 目標は、ボリュームの空き領域を 99% にすることです。

同期を進行させ、クラウドを使った階層化にディスク領域を解放させます。 これは、Windows Server のエクスプローラーで確認できます。

Windows Server に十分な空き容量がある場合は、コマンドを再実行すると問題が解決されます。 このような状況が発生しても壊れるものは何もなく、安心して進めることができます。 コマンドを再実行する不便さだけが唯一の影響です。

Azure File Sync の問題のトラブルシューティングについては、次のセクションのリンクを確認してください。

## <a name="next-steps"></a>次のステップ

Azure ファイル共有と Azure File Sync については、さらに知るべきことがあります。以下の記事では、高度なオプション、ベスト プラクティスについて説明します。トラブルシューティングのヘルプもあります。 これらの記事は、それぞれに対応する [Azure ファイル共有のドキュメント](storage-files-introduction.md)にリンクしています。

* [移行の概要](storage-files-migration-overview.md)
* [AFS の概要](./storage-sync-files-planning.md)
* [AFS デプロイ ガイド](./storage-how-to-create-file-share.md)
* [AFS のトラブルシューティング](storage-sync-files-troubleshoot.md)
