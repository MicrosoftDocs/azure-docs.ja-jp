---
title: Linux の Azure File Sync への移行
description: Linux サーバーの場所から、Azure File Sync と Azure ファイル共有によるハイブリッド クラウド デプロイに、ファイルを移行する方法について説明します。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: ff26318cafdf493579961fc718643f831ae9efeb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564256"
---
# <a name="migrate-from-linux-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Azure File Sync を使用して Linux からハイブリッド クラウド デプロイに移行する

この移行に関する記事は、NFS および Azure File Sync というキーワードに関連するものの 1 つです。この記事がご使用のシナリオに当てはまるかどうかを確認してください。

> [!div class="checklist"]
> * データ ソース: ネットワーク接続ストレージ (NAS)
> * 移行ルート: SAMBA を搭載した Linux サーバー &rArr; Windows Server 2012R2 またはそれ以降 &rArr; Azure ファイル共有と同期
> * オンプレミスのファイルのキャッシュ: はい、最終的な目標は Azure File Sync のデプロイです。

シナリオが異なる場合は、[移行ガイドの表](storage-files-migration-overview.md#migration-guides)を参照してください。

Azure File Sync は、Windows Server インスタンスでは直接接続記憶域 (DAS) を使用して機能します。 Linux クライアント、リモート Server Message Block (SMB) 共有、Network File System (NFS) 共有との間の同期はサポートされていません。

そのため、ファイル サービスをハイブリッド デプロイに変換するには、Windows Server に移行する必要があります。 この記事では、そのような移行の計画と実行について説明します。

## <a name="migration-goals"></a>移行の目標

目標は、Linux Samba サーバー上にある共有を Windows Server インスタンスに移行することです。 その後、ハイブリッド クラウド デプロイに Azure File Sync を使用します。 この移行は、運用データの整合性と、移行中の可用性が保証される方法で行う必要があります。 後者については、ダウンタイムを最小限に抑えて、通常のメンテナンス期間に収まるか、わずかに超えるだけで済むようにする必要があります。

## <a name="migration-overview"></a>移行の概要

Azure Files の[移行の概要に関する記事](storage-files-migration-overview.md)で説明されているように、適切なコピー ツールと方法を使用することが重要です。 Linux Samba サーバーでは、SMB 共有がローカル ネットワークに直接公開されています。 この移行シナリオでファイルを移動するには、Windows Server に組み込まれている Robocopy が最適です。

Linux サーバー上で Samba を実行しておらず、フォルダーを Windows Server 上のハイブリッド デプロイに移行したい場合は、Robocopy ではなく Linux のコピー ツールを使用できます。 コピー ツールの忠実性の機能に注意してください。 コピー ツールで探す内容については、移行の概要の記事で[移行の基本に関するセクション](storage-files-migration-overview.md#migration-basics)を参照してください。

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>フェーズ 1:必要な Azure ファイル共有の数を特定する

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-instance-on-premises"></a>フェーズ 2:オンプレミスで適切な Windows Server インスタンスをプロビジョニングする

* 仮想マシンまたは物理サーバーとして、Windows Server 2019 インスタンスを作成します。 Windows Server 2012 R2 が最小要件です。 また、Windows Server フェールオーバー クラスターもサポートされています。
* 直接接続ストレージ (DAS) をプロビジョニングまたは追加します。 ネットワーク接続ストレージ (NAS) はサポートされていません。

  Azure File Syncs の[クラウドを使った階層化](storage-sync-cloud-tiering-overview.md)機能を使用する場合、プロビジョニングするストレージの容量は、現在 Linux Samba サーバー上で使用している量より少なくてもかまいません。 

プロビジョニングするストレージの容量は、現在 Linux Samba サーバーで使用している量より小さくてもかまいません。 この構成を選択する場合、Azure File Sync の[クラウドを使った階層化](storage-sync-cloud-tiering-overview.md)機能も使用する必要があります。 ただし、後のフェーズで、大きい Linux Samba サーバー領域から小さい Windows Server ボリュームにファイルをコピーする場合は、バッチ処理を行う必要があります。

  1. ディスクに収まるファイルのセットを移動します。
  2. ファイル同期とクラウドを使った階層化が連携するようにします。
  3. ボリュームにより多くの空き領域が作成されたら、次のファイルのバッチに進みます。 または、新しい `/LFSM` スイッチを使用するために、後に続く [RoboCopy セクション](#phase-7-robocopy)にある RoboCopy コマンドを確認してください。 `/LFSM` を使用すると、RoboCopy ジョブを大幅に簡略化できますが、依存する可能性がある他の RoboCopy スイッチとの互換性はありません。
    
  Linux Samba サーバー上でファイルが占めているのと同等の領域を Windows Server インスタンス上でプロビジョニングすることにより、このバッチ処理手法を回避できます。 Windows で重複除去を有効にすることを検討します。 この大量のストレージを Windows Server インスタンスに永続的にコミットしたくない場合は、移行後、クラウドを使った階層化ポリシーを調整する前に、ボリューム サイズを小さくすることができます。 それにより、Azure ファイル共有の小規模なオンプレミス キャッシュが作成されます。

デプロイする Windows Server インスタンスのリソース構成 (コンピューティングと RAM) は、主に同期する項目 (ファイルとフォルダー) の数によって変わります。 ご心配な場合は、より高いパフォーマンス構成を使用することをお勧めします。

[同期する必要がある項目 (ファイルとフォルダー) の数に基づいて Windows Server インスタンスをサイズ指定する方法を参照してください。](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> 前記のリンク先の記事では、サーバー メモリ (RAM) の範囲に関する表が示されています。 サーバーの数を少なくすることはできますが、初期同期にはかなり長い時間かかることが予想されます。

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>フェーズ 3: Azure File Sync クラウド リソースをデプロイする

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>フェーズ 4: Azure ストレージ リソースをデプロイする

このフェーズでは、フェーズ 1 のマッピング テーブルを参照し、それを使用して、適切な数の Azure ストレージ アカウントと、その中のファイル共有をプロビジョニングします。

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>フェーズ 5:Azure File Sync エージェントをデプロイする

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server-deployment"></a>フェーズ 6:Windows Server デプロイで Azure File Sync を構成する

このプロセスのために、登録済みのオンプレミス Windows Server インスタンスを準備して、インターネットに接続しておく必要があります。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> クラウドを使った階層化は、ローカル サーバーが、クラウドに格納されているよりもストレージ容量が少なくても、完全な名前空間を使用できるようにする Azure File Sync 機能です。 ローカル環境で関心のあるデータも、アクセスのパフォーマンスを上げるためにローカルにキャッシュされます。 クラウドを使った階層化は、Azure File Sync のサーバー エンドポイントごとのオプション機能です。

> [!WARNING]
> Linux Samba サーバーで使用されているデータより少ないストレージを Windows Server のボリュームにプロビジョニングした場合は、クラウドを使った階層化が必須です。 クラウドを使った階層化を有効にしない場合、サーバーではすべてのファイルを格納するための領域は解放されません。 移行のために一時的に、階層化ポリシーをボリュームの空き領域 99% に設定します。 移行が完了した後、クラウドを使った階層化の設定に戻り、長期的により有効なレベルにポリシーを確実に設定してください。

同期グループを作成する手順と、すべての Azure ファイル共有とサーバーの場所に対するサーバー エンドポイントと一致するサーバー フォルダーを追加する手順を繰り返します。これは、同期のために構成する必要があります。

すべてのサーバー エンドポイントの作成後、同期は機能しています。 テスト ファイルを作成し、サーバーの場所から接続されている Azure ファイル共有に同期されることを確認できます (同期グループのクラウド エンドポイントで記述されているように)。

そうしないと、サーバーのフォルダーと Azure ファイル共有のどちらの場所も空であり、データを待機します。 次のステップでは、Azure File Sync によってクラウドに移動されるように、Windows Server インスタンスへのファイルのコピーを開始します。 クラウドを使った階層化を有効にした場合、ローカル ボリューム上の容量が不足した場合に、サーバー上でファイルの階層化が開始されます。

## <a name="phase-7-robocopy"></a>フェーズ 7: Robocopy

基本的な移行方法では、Robocopy を使用してファイルをコピーし、Azure File Sync を使用して同期を実行します。

Windows Server ターゲット フォルダーへの最初のローカル コピーを実行します。

1. Linux Samba サーバー上で最初の場所を特定します。
1. Windows Server インスタンス上で、既に Azure File Sync が構成されている対応するフォルダーを特定します。
1. Robocopy を使用してコピーを開始します。

次の Robocopy コマンドを実行すると、Linux Samba サーバーのストレージから Windows Server ターゲット フォルダーにファイルがコピーされます。 Windows Server によって、それが Azure ファイル共有に同期されます。 

Linux Samba サーバー上でファイルが占める量より少ないストレージを Windows Server インスタンスにプロビジョニングした場合は、クラウドを使った階層化を構成済みです。 ローカルの Windows Server ボリュームがいっぱいになると、[クラウドを使った階層化](storage-sync-cloud-tiering-overview.md)により、既に正常に同期されているファイルの階層化が開始されます。 クラウドを使った階層化により、Linux Samba サーバーからのコピーを続けるのに十分な領域が生成されます。 クラウドを使った階層化では、1 時間に 1 回、同期されたものが確認されて、ボリュームの空き領域が 99% のポリシーに達するようにディスク領域が解放されます。

Robocopy によるファイルの移動が速すぎて、クラウドへの同期とローカルでの階層化が追いつかず、ローカル ディスク領域の不足が引き起こされる可能性があります。 そうなると、Robocopy は失敗します。 問題が回避される順序で共有を処理することをお勧めします。 たとえば、すべての共有に対して Robocopy ジョブを同時に開始しないことを検討してください。 または、Windows Server インスタンス上の現在の空き領域に合った共有を移動することを検討してください。 Robocopy ジョブが失敗した場合、次のミラー/パージ オプションを使用すれば、いつでもコマンドを再実行できます。

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

## <a name="phase-8-user-cut-over"></a>フェーズ 8: ユーザーのカットオーバー

Robocopy コマンドを初めて実行するときは、ユーザーとアプリケーションがまだ Linux Samba サーバー上のファイルにアクセスしていて、それらを変更する可能性があります。 Robocopy があるディレクトリを処理し、次のディレクトリに移動した後、ソースの場所 (Linux) のユーザーがファイルを追加、変更、または削除し、現在のこの Robocopy の実行では処理されない可能性があります。 これは正しい動作です。

最初の実行では、データの大部分が Windows Server インスタンスに移動された後、Azure File Sync 経由でクラウドに移動されます。この最初のコピーには、次の条件によっては時間がかかることがあります。

* ダウンロードの帯域幅。
* アップロードの帯域幅。
* ローカル ネットワークの速度と、それに合った最適な Robocopy スレッドの数。
* Robocopy と Azure File Sync によって処理される必要がある項目 (ファイルとフォルダー) の数。

最初の実行が完了した後、再度コマンドを実行します。

転送する必要があるのは前回の実行以降に発生した変更だけなので、2 回目の処理は短時間で完了します。 この 2 回目の間に、まだ新しい変更の実行が蓄積される可能性があります。

特定の場所に対する Robocopy の処理が完了するまでの時間が、許容されるダウンタイム時間以下になるまで、このプロセスを繰り返します。

許容されるダウンタイムを考慮し、Linux の場所をオフラインにする準備が整ったら、ユーザーがその場所にアクセスできなくなった共有ルート上の ACL を変更できます。 または、Linux サーバー上のこのフォルダー内のコンテンツが変更されないように、その他の適切な手順を実行できます。

最後に 1 回 Robocopy を実行します。 それにより、見過ごされた可能性があるすべての変更が取得されます。 この最後の手順にかかる時間は、Robocopy のスキャンの速度に依存します。 前回の実行にかかった時間を測定することで、(ダウンタイムに相当する) 時間を見積もることができます。

Windows Server フォルダーに共有を作成し、必要に応じて、その共有を指すように DFS-N のデプロイを調整します。 Linux Samba サーバーの SMB 共有と同じ共有レベルのアクセス許可を設定してください。 Linux Samba サーバー上でローカル ユーザーを使用している場合、これらのユーザーを Windows Server のローカル ユーザーとして再作成する必要があります。 また、Robocopy によって Windows Server インスタンスに移動した既存の SID を、お使いの新しい Windows Server ローカル ユーザーの SID にマップする必要があります。 Active Directory アカウントと ACL を使用していた場合、Robocopy ではそれらがそのまま移動されるため、それ以上の操作は必要ありません。

これで、共通のルートまたはボリュームへの共有または共有のグループの移行は完了しました (フェーズ 1 からのマッピングによって異なります)。

これらの複数のコピーを並行して実行することができます。 一度に 1 つの Azure ファイル共有のスコープを処理することをお勧めします。

> [!WARNING]
> Linux Samba サーバーから Windows Server インスタンスにすべてのデータを移動して、移行が完了したら、Azure portal 上で "*すべての*" 同期グループに戻ります。 クラウドを使った階層化ボリュームの空き領域の割合を、キャッシュの使用状況により適するように (20 % など) 調整します。 

クラウドを使った階層化ボリュームの空き領域のポリシーはボリューム レベルで機能し、複数のサーバー エンドポイントがそこから同期される可能性があります。 1 つのサーバー エンドポイントでも空き領域を調整し忘れた場合、同期では最も制限の厳しい規則が引き続き適用され、99% の空きディスク領域の確保が試行されます。 その場合、ローカル キャッシュが期待どおりに動作しない可能性があります。 アクセス頻度の低いアーカイブ データのみが含まれるボリューム用の名前空間を使用し、残りのストレージ領域を他のシナリオ用に確保することを目的としている場合、パフォーマンスは許容範囲になる可能性があります。

## <a name="troubleshoot"></a>トラブルシューティング

最も一般的な問題は、Windows Server 側で **ボリュームがいっぱい** になったために Robocopy コマンドが失敗することです。 クラウドを使った階層化は 1 時間ごとに動作し、同期されたローカルの Windows Server ディスクからコンテンツが退避されます。 目標は、ボリューム上の空き領域を 99% にすることです。

同期を進行させ、クラウドを使った階層化にディスク領域を解放させます。 これは、Windows Server 上のエクスプローラーで確認できます。

Windows Server インスタンスに空き容量が十分にある場合は、コマンドを再実行すると問題が解決されます。 このような状況が発生しても影響はなく、安心して進めることができます。 コマンドを再実行する不便さだけが、唯一の影響です。

Azure File Sync の問題のトラブルシューティングについては、次のセクションにあるリンクを確認してください。

## <a name="next-steps"></a>次のステップ

Azure ファイル共有と Azure File Sync については、さらに知るべきことがあります。以下の記事には、詳細なオプション、ベスト プラクティス、およびトラブルシューティングのヘルプが含まれています。 これらの記事は、それぞれに対応する [Azure ファイル共有のドキュメント](storage-files-introduction.md)にリンクしています。

* [Azure File Sync の概要](./storage-sync-files-planning.md)
* [Azure File Sync デプロイ ガイド](./storage-how-to-create-file-share.md)
* [Azure File Sync に関するトラブルシューティング](storage-sync-files-troubleshoot.md)