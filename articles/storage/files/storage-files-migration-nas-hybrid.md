---
title: オンプレミスの NAS を Azure File Sync に移行する
description: オンプレミスのネットワーク接続ストレージ (NAS) の場所から、Azure File Sync と Azure ファイル共有を使用してハイブリッド クラウド デプロイにファイルを移行する方法について説明します。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fd8e845734169bcd73fa0e087c30c0f2fd6ef4f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85510307"
---
# <a name="migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>Azure File Sync を使用してネットワーク接続ストレージ (NAS) からハイブリッド クラウド デプロイに移行する

Azure File Sync は直接接続記憶域 (DAS) の場所で動作し、ネットワーク接続ストレージ (NAS) の場所への同期をサポートしていません。
このため、ファイルの移行が必要になります。この記事では、そのような移行の計画と実行について説明します。

## <a name="migration-goals"></a>移行の目標

目標は、NAS アプライアンス上にある共有を Windows Server に移行することです。 その後は、ハイブリッド クラウド デプロイに Azure File Sync を使用します。 この移行は、運用データの整合性と、移行中の可用性が保証される方法で行う必要があります。 後者については、ダウンタイムを最小限に抑えて、通常のメンテナンス期間に収まるか、わずかに超えるだけで済むようにする必要があります。

## <a name="migration-overview"></a>移行の概要

Azure Files の[移行の概要に関する記事](storage-files-migration-overview.md)で説明されているように、適切なコピー ツールと方法を使用することが重要です。 NAS アプライアンスでは、SMB 共有がローカル ネットワークに直接公開されています。 この移行シナリオでファイルを移動するには、Windows Server に組み込まれている RoboCopy が最適です。

- フェーズ 1:[必要な Azure ファイル共有の数を特定する](#phase-1-identify-how-many-azure-file-shares-you-need)
- フェーズ 2:[オンプレミスで適切な Windows Server をプロビジョニングする](#phase-2-provision-a-suitable-windows-server-on-premises)
- フェーズ 3: [Azure File Sync クラウド リソースをデプロイする](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- フェーズ 4: [Azure ストレージ リソースをデプロイする](#phase-4-deploy-azure-storage-resources)
- フェーズ 5:[Azure File Sync エージェントをデプロイする](#phase-5-deploy-the-azure-file-sync-agent)
- フェーズ 6:[Windows Server で Azure File Sync を構成する](#phase-6-configure-azure-file-sync-on-the-windows-server)
- フェーズ 7: [RoboCopy を実行する](#phase-7-robocopy)
- フェーズ 8: [ユーザーのカットオーバー](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>フェーズ 1:必要な Azure ファイル共有の数を特定する

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>フェーズ 2:オンプレミスで適切な Windows Server をプロビジョニングする

* 仮想マシンまたは物理サーバーとして、Windows Server 2019 (最低でも 2012R2) のサーバーを作成します。 Windows Server フェールオーバー クラスターもサポートされています。
* 直接接続記憶域 (NAS ではなく DAS、NAS はサポートされていません) をプロビジョニングまたは追加します。

    Azure File Syncs の[クラウドを使った階層化](storage-sync-cloud-tiering.md)機能を使用する場合、プロビジョニングするストレージの容量は、現在 NAS アプライアンスで使用している量より小さくてもかまいません。
    ただし、後のフェーズで、大きい NAS 領域から小さい Windows Server ボリュームにファイルをコピーする場合は、バッチ処理を行う必要があります。

    1. ディスクに収まるファイルのセットを移動します
    2. ファイル同期とクラウドを使った階層化が連携するようにします
    3. ボリュームにより多くの空き領域が作成されたら、次のファイルのバッチに進みます。 
    
    NAS アプライアンスでファイルが占めているのと同等の領域を Windows Server 上でプロビジョニングすることにより、このバッチ処理手法を回避できます。 NAS と Windows で重複除去を検討してください。 この大量のストレージを Windows Server に永続的にコミットしたくない場合は、移行後、クラウドの階層化ポリシーを調整する前に、ボリュームのサイズを小さくすることができます。 それにより、Azure ファイル共有の小規模なオンプレミス キャッシュが作成されます。

デプロイする Windows Server のリソース構成 (コンピューティングと RAM) は、主に同期する項目 (ファイルとフォルダー) の数によって変わります。 懸念がある場合は、より高いパフォーマンス構成を使用することをお勧めします。

[同期する必要がある項目 (ファイルとフォルダー) の数に基づいて Windows サーバーのサイズを変更する方法を参照してください。](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> 前記のリンク先の記事では、サーバー メモリ (RAM) の範囲に関する表が示されています。 サーバーの数を少なくすることはできますが、初期同期にはより長い時間かかることが予想されます。

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>フェーズ 3: Azure File Sync クラウド リソースをデプロイする

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>フェーズ 4: Azure ストレージ リソースをデプロイする

このフェーズでは、フェーズ 1 のマッピング テーブルを参照し、それを使用して、適切な数の Azure ストレージ アカウントと、その中のファイル共有をプロビジョニングします。

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>フェーズ 5:Azure File Sync エージェントをデプロイする

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>フェーズ 6:Windows サーバー上で Azure File Sync を構成する

このプロセスのために、登録済みのオンプレミス Windows サーバーを準備し、インターネットに接続しておく必要があります。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> クラウドを使った階層化は、ローカル サーバーが、クラウドに格納されているよりもストレージ容量が少なくても、完全な名前空間を使用できるようになる AFS 機能です。 ローカル環境で関心のあるデータも、アクセスのパフォーマンスを上げるためにローカルにキャッシュされます。 クラウドを使った階層化は、Azure File Sync の "サーバー エンドポイント" ごとのオプション機能です。

> [!WARNING]
> NAS アプライアンスで使用されているデータより少ないストレージを Windows Server のボリュームにプロビジョニングする場合は、クラウドを使った階層化が必須です。 クラウドを使った階層化を有効にしない場合、サーバーではすべてのファイルを格納するための領域は解放されません。 移行のために一時的に、階層化ポリシーをボリューム空き領域 99% に設定します。 移行が完了した後、クラウドを使った階層化の設定に戻り、長期的により有効なレベルに設定してください。

同期グループ作成の手順と、すべての Azure ファイル共有とサーバーの場所に対するサーバー エンドポイントとしての一致するサーバー フォルダーの追加の手順を繰り返します。これは、同期用に構成するために必要です。

すべてのサーバー エンドポイントの作成後、同期は機能しています。 テスト ファイルを作成し、サーバーの場所から接続されている Azure ファイル共有に同期されることを確認できます (同期グループのクラウド エンドポイントで記述されているように)。

そうしないと、サーバーのフォルダーと Azure ファイル共有のどちらの場所も空で、どちらの場所でもデータが待機されています。 次のステップでは、Azure File Sync によってクラウドに移動されるように、Windows Server へのファイルのコピーを始めます。 クラウドを使った階層化を有効にした場合、ローカル ボリュームの容量が不足した場合のために、サーバーでファイルの階層化が開始されます。

## <a name="phase-7-robocopy"></a>フェーズ 7: RoboCopy を実行する

基本的な移行方法は、NAS アプライアンスから Windows Server へは RoboCopy で、Azure ファイル共有へは Azure File Sync です。

Windows Server ターゲット フォルダーへの最初のローカル コピーを実行します。

* NAS アプライアンス上で最初の場所を特定します。
* Windows Server 上で、既に Azure File Sync が構成されている対応するフォルダーを特定します。
* RoboCopy を使用してコピーを開始します

次の RoboCopy コマンドを実行すると、NAS ストレージから Windows Server のターゲット フォルダーにファイルがコピーされます。 Windows Server によってそれが Azure ファイル共有に同期されます。 

NAS アプライアンスでファイルが占める量より少ないストレージを Windows Server にプロビジョニングした場合は、クラウドを使った階層化を構成してあります。 ローカル環境の Windows Server ボリュームがいっぱいになると、[クラウドを使った階層化](storage-sync-cloud-tiering.md)により、既に正常に同期されているファイルの階層化が開始されます。 クラウドを使った階層化により、NAS アプライアンスからのコピーを続けるのに十分な領域が生成されます。 クラウドを使った階層化では、1 時間に 1 回、同期されたものが確認されて、ボリューム空き領域 99% になるようにディスク領域が解放されます。
RoboCopy によるファイルの移動が速すぎて、クラウドへの同期とローカル環境の階層化が追いつかず、ローカル ディスク領域が不足する可能性があります。 RoboCopy は失敗します。 そのようなことが起こらない順序で共有を処理することをお勧めします。 たとえば、すべての共有に対して RoboCopy ジョブを同時に開始しないようにしたり、Windows Server の現在の空き領域に収まる共有のみを移動したりするようにします。

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

背景:

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      RoboCopy でマルチスレッドを実行できるようにします。 既定値は 8 です。最大値は 128 です。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:\<file name\>
   :::column-end:::
   :::column span="1":::
      状態を UNICODE 形式でログ ファイルに出力します (既存のログを上書きします)。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      コンソール ウィンドウに出力します。 ログ ファイルへの出力と組み合わせて使用されます。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      バックアップ アプリケーションが使用するのと同じモードで RoboCopy を実行します。 これにより、現在のユーザーがアクセス許可を持っていないファイルを、RoboCopy で移動できます。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      同じターゲットつまりコピー先に対して、この RoboCopy コマンドを複数回順番に実行できます。 前にコピーされたものが識別されて、省略されます。 前回の実行以降に発生した変更、追加、"*削除*" だけが処理されます。 コマンドが以前に実行されていない場合は、何も省略されません。 */MIR* フラグは、まだアクティブに使用および変更されているソースの場所に適したオプションです。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      ファイル コピーの忠実性 (既定値は /COPY:DAT)、コピー フラグ: D = データ、A = 属性、T = タイムスタンプ、S = セキュリティ = NTFS ACL、O = 所有者情報、U = 監査情報
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      すべてのファイル情報をコピーします (/COPY:DATSOU と同等)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY:copyflag[s]
   :::column-end:::
   :::column span="1":::
      ディレクトリのコピーの忠実性 (既定値は /DCOPY:DA)、コピー フラグ: D = データ、A = 属性、T = タイムスタンプ
   :::column-end:::
:::row-end:::

## <a name="phase-8-user-cut-over"></a>フェーズ 8: ユーザーのカットオーバー

RoboCopy コマンドを初めて実行するときは、ユーザーとアプリケーションがまだ NAS 上のファイルにアクセスしていて、それを変更する可能性があります。 RoboCopy があるディレクトリを処理し、次のディレクトリに移動した後、ソースの場所 (NAS) のユーザーがファイルを追加、変更、または削除し、現在の RoboCopy の実行では処理されない可能性があります。 これは正しい動作です。

最初の実行では、データの大部分が Windows Server に移動された後、Azure File Sync 経由でクラウドに移動されます。この最初のコピーには、次の条件によっては時間がかかることがあります。

* ダウンロードの帯域幅
* アップロードの帯域幅
* ローカル ネットワークの速度と、それに合った最適な RoboCopy スレッドの数
* RoboCopy と Azure File Sync によって処理される必要がある項目 (ファイルとフォルダー) の数

最初の実行が完了した後、コマンドを再度実行します。

2 回目に転送する必要があるのは、前回の実行以降に発生した変更だけなので、処理は短時間で完了します。 この 2 回目の実行の間に、まだ新しい変更が蓄積される可能性があります。

特定の場所に対する RoboCopy が完了するまでの時間が、許容されるダウンタイム時間以下になるまで、このプロセスを繰り返します。

ダウンタイムが許容できるものと見なされ、NAS の場所をオフラインにする準備ができた場合: ユーザー アクセスをオフラインにするには、共有ルートの ACL を変更して、ユーザーがその場所にアクセスできないようにするか、またはその他の適切な手順を実行して、NAS 上のこのフォルダーの内容が変更されないようにします。

最後に 1 回 RoboCopy ラウンドを実行します。 それにより、漏れている可能性があるすべての変更が取得されます。
この最後の手順にかかる時間は、RoboCopy のスキャンの速度に依存します。 前回の実行にかかった時間を測定することで、(ダウンタイムに相当する) 時間を見積もることができます。

Windows Server フォルダーに共有を作成し、必要に応じて、その共有を指すように DFS-N のデプロイを調整します。 NAS SMB 共有と同じ共有レベルのアクセス許可を設定してください。 エンタープライズ クラスのドメイン参加 NAS を使用していた場合、ユーザーの SID は Active Directory に存在するユーザーと自動的に一致し、RoboCopy によってファイルとメタデータが完全に忠実にコピーされます。 NAS でローカル ユーザーを使用していた場合は、これらのユーザーを Windows Server のローカル ユーザーとして作成し直し、RoboCopy によって Windows Server に移動された既存の SID を、新しい Windows Server ローカル ユーザーの SID にマップする必要があります。

これで、共通のルートまたはボリュームへの共有または共有のグループの移行は完了しました。 (フェーズ 1 からのマッピングによって異なります)

これらの複数のコピーを並行して実行することができます。 一度に 1 つの Azure ファイル共有のスコープを処理することをお勧めします。

> [!WARNING]
> NAS から Windows Server にすべてのデータを移動したら、移行は完了です。Azure portal で***すべての***同期グループに戻り、クラウドを使った階層化ボリュームの空き領域の割合の値を、キャッシュの使用に適したもの (20% など) に調整します。 

クラウドを使った階層化ボリュームの空き領域ポリシーはボリューム レベルで動作し、複数のサーバー エンドポイントがそこから同期する可能性があります。 1 つのサーバー エンドポイントでも空き領域を調整し忘れた場合、同期では最も制限の厳しい規則が引き続き適用され、99% の空きディスク領域が確保されて、ローカル キャッシュが期待どおりに実行されなくなります。 アクセス頻度の低いアーカイブ データのみが含まれるボリューム用の名前空間のみを使用し、残りのストレージ領域を他のシナリオ用に確保することを目的としている場合を除きます。

## <a name="troubleshoot"></a>トラブルシューティング

最も可能性の高い問題は、Windows Server 側で "*ボリュームがいっぱい*" になったために RoboCopy コマンドが失敗することです。 クラウドを使った階層化は 1 時間ごとに動作し、同期されたローカル環境の Windows Server ディスクから内容が退避されます。 目標は、ボリュームの空き領域を 99% にすることです。

同期を進行させ、クラウドを使った階層化にディスク領域を解放させます。 これは、Windows Server のエクスプローラーで確認できます。

Windows Server に十分な空き容量がある場合は、コマンドを再実行すると問題が解決されます。 このような状況が発生しても壊れるものは何もなく、安心して進めることができます。 コマンドを再実行する不便さだけが唯一の影響です。

Azure File Sync の問題のトラブルシューティングについては、次のセクションのリンクを確認してください。

## <a name="next-steps"></a>次のステップ

Azure ファイル共有と Azure File Sync については、さらに知るべきことがあります。以下の記事は、詳細なオプション、ベスト プラクティス、およびトラブルシューティングのヘルプを理解するのに役立ちます。 これらの記事は、それぞれに対応する [Azure ファイル共有のドキュメント](storage-files-introduction.md)にリンクしています。

* [AFS の概要](https://aka.ms/AFS)
* [AFS デプロイ ガイド](storage-files-deployment-guide.md)
* [AFS のトラブルシューティング](storage-sync-files-troubleshoot.md)
