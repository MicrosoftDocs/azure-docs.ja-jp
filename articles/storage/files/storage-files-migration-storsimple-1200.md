---
title: StorSimple 1200 の Azure File Sync への移行
description: StorSimple 1200 シリーズ仮想アプライアンスを Azure File Sync に移行する方法について説明します。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d9cf7b3cf996e41f90e3a40a6ee08d0fd51c8457
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85510345"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>StorSimple 1200 の Azure File Sync への移行

StorSimple 1200 シリーズは、オンプレミスのデータ センターで実行される仮想アプライアンスです。 このアプライアンスから Azure File Sync 環境にデータを移行することができます。 Azure File Sync は、StorSimple アプライアンスの移行先となる、既定の戦略的な長期 Azure サービスです。

StorSimple 1200 シリーズは 2022 年 12 月に[サポート終了](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series)となります。  できるだけ早く移行の計画を開始することが重要です。 この記事では、Azure File Sync への移行を成功させるために必要な背景知識と移行手順について説明します。 

## <a name="azure-file-sync"></a>Azure File Sync

> [!IMPORTANT]
> Microsoft はお客様の移行を支援できるよう取り組んでいます。 カスタマイズした移行計画について、および移行中のサポートについては、AzureFilesMigration@microsoft .com にメールをお送りください。

Azure File Sync は、次の 2 つの主要なコンポーネントに基づく Microsoft のクラウド サービスです。

* ファイルの同期とクラウドを使った階層化。
* Azure のネイティブ ストレージとしてのファイル共有。SMB や file REST などの複数のプロトコルを介してアクセスできます。 Azure ファイル共有は、ネットワーク ドライブとしてネイティブにマウントできる Windows サーバー上のファイル共有に相当します。 属性、アクセス許可、タイムスタンプなどの重要なファイルの忠実性がサポートされています。 StorSimple とは異なり、クラウドに格納されているファイルとフォルダーを解釈するためにアプリケーションやサービスは必要ありません。 汎用のファイル サーバー データや一部のアプリケーション データをクラウドに格納するための、理想的で最も柔軟な方法です。

この記事では、移行手順を中心に説明します。 移行前に Azure File Sync について詳しく知りたい場合は、次の記事をお勧めします。

* [Azure File Sync - 概要](https://aka.ms/AFS "概要")
* [Azure File Sync - デプロイ ガイド](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>移行の目標

目標は、運用データの整合性と可用性を保証することです。 後者については、ダウンタイムを最小限に抑えて、通常のメンテナンス期間に収まるか、わずかに超えるだけで済むようにする必要があります。

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>StorSimple 1200 の Azure File Sync への移行パス

Azure File Sync エージェントを実行するには、ローカル Windows Server が必要です。 Windows サーバーは、2012R2 サーバー以降であれば使用できますが、理想的なのは Windows Server 2019 です。

代替の移行パスは多数あり、そのすべてを説明し、この記事でベスト プラクティスとして推奨されているルートと比較してそれらのリスクや欠点が大きい理由を説明しようとすると、記事があまりに長くなります。

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="この記事で後述する移行ルートの手順の概要。":::

上の図は、この記事のセクションに対応する手順を示したものです。

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>手順 1:オンプレミスの Windows Server とストレージをプロビジョニングする

1. 仮想マシンまたは物理サーバーとして、Windows Server 2019 (最低でも 2012R2) を作成します。 Windows Server フェールオーバー クラスターもサポートされています。
2. 直接接続記憶域 (NAS ではなく DAS、NAS はサポートされていません) をプロビジョニングまたは追加します。 Windows Server 記憶域のサイズは、StorSimple 1200 仮想アプライアンスの利用可能な容量のサイズ以上である必要があります。

### <a name="step-2-configure-your-windows-server-storage"></a>手順 2:Windows Server の記憶域を構成する

この手順では、StorSimple ストレージの構造 (ボリュームと共有) を Windows Server 記憶域の構造にマップします。
ストレージの構造を変更する場合は (ボリュームの数、データ フォルダーとボリュームの関連付け、現在の SMB/NFS 共有の上位または下位のサブフォルダー構造)、ここでこれらの変更を考慮する必要があります。
Azure File Sync の構成後にファイルとフォルダーの構造を変更するのは面倒であり、避ける必要があります。
この記事では、マッピングが 1:1 であると想定しているため、この記事の手順に従うときはマッピングの変更を考慮する必要があります。

* 運用データが、最終的に Windows Server のシステム ボリュームに格納されてはなりません。 システム ボリュームでは、クラウドを使った階層化はサポートされていません。 ただし、移行および StorSimple の代替環境としての継続的な運用には、この機能が必要です。
* StorSimple 1200 仮想アプライアンスと同じ数のボリュームを、Windows Server にプロビジョニングします。
* 必要な Windows Server の役割、機能、設定を構成します。 OS を安全で最新の状態に保つため、Windows Server の更新プログラムにオプトインすることをお勧めします。 同様に、Azure File Sync エージェントなど、Microsoft のアプリケーションを最新の状態に保つため、Microsoft Update にオプトインすることをお勧めします。
* 以下の手順を読む前に、フォルダーまたは共有を構成しないでください。

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>手順 3:最初の Azure File Sync クラウド リソースをデプロイする

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>手順 4:ローカル ボリュームとフォルダー構造を Azure File Sync および Azure ファイル共有リソースと一致させる

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>手順 5:Azure ファイル共有をプロビジョニングする

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>手順 6:Windows Server のターゲット フォルダーを構成する

前の手順では、同期トポロジのコンポーネントを決定するすべての側面を検討しました。 この時点で、アップロード用のファイルを受信するようにサーバーを準備します。

それぞれが独自の Azure ファイル共有に同期する**すべての**フォルダーを作成します。
前に説明したフォルダー構造に従うようにすることが重要です。 たとえば、複数のローカル SMB 共有をまとめて 1 つの Azure ファイル共有に同期する場合は、それらをボリューム上の共通のルート フォルダーの下に配置する必要があります。 ここで、このターゲット ルート フォルダーをボリューム上に作成します。

プロビジョニングした Azure ファイル共有の数は、この手順で作成したフォルダーの数に、ルート レベルで同期するボリュームの数を加えたものと一致している必要があります。

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>手順 7:Azure File Sync エージェントをデプロイする

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>手順 8:同期を構成する

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **クラウドを使った階層化を必ず有効にしておきます。** これは、StorSimple クラウド ストレージのデータの合計サイズを格納するのに十分な領域がローカル サーバーにない場合に必要です。 移行のために一時的に、階層化ポリシーをボリューム空き領域 99% に設定します。

同期グループ作成の手順と、すべての Azure ファイル共有とサーバーの場所に対するサーバー エンドポイントとしての一致するサーバー フォルダーの追加の手順を繰り返します。これは、同期用に構成するために必要です。

### <a name="step-9-copy-your-files"></a>手順 9:ファイルをコピーする

基本的な移行方法は、StorSimple 仮想アプライアンスから Windows Server への RoboCopy と、Azure ファイル共有への Azure File Sync です。

Windows Server ターゲット フォルダーへの最初のローカル コピーを実行します。

* 仮想 StorSimple アプライアンス上で最初の場所を特定します。
* Windows Server 上で、既に Azure File Sync が構成されている対応するフォルダーを特定します。
* RoboCopy を使用してコピーを開始します

次の RoboCopy コマンドを実行すると、StorSimple Azure Storage からローカル StorSimple にファイルが呼び出された後、Windows Server のターゲット フォルダーに移動されます。 Windows Server によってそれが Azure ファイル共有に同期されます。 ローカル環境の Windows Server ボリュームがいっぱいになると、クラウドを使った階層化により、既に正常に同期されているファイルの階層化が開始されます。 クラウドを使った階層化により、StorSimple 仮想アプライアンスからのコピーを続けるのに十分な領域が生成されます。 クラウドを使った階層化では、1 時間に 1 回、同期されたものが確認されて、ボリューム空き領域 99% になるようにディスク領域が解放されます。

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
      /UNILOG:<file name>
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
      同じターゲットつまりコピー先に対して、この RoboCopy コマンドを複数回順番に実行できます。 前にコピーされたものが識別されて、省略されます。 前回の実行以降に発生した変更、追加、"*削除*" だけが処理されます。 コマンドが以前に実行されていない場合は、何も省略されません。 これは、まだアクティブに使用および変更されているソースの場所に適したオプションです。
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

RoboCopy コマンドを初めて実行するときは、ユーザーとアプリケーションがまだ StorSimple のファイルとフォルダーにアクセスしていて、それを変更する場合があります。 RoboCopy があるディレクトリを処理し、次のディレクトリに移動した後、ソースの場所 (StorSimple) のユーザーがファイルを追加、変更、または削除し、現在の RoboCopy の実行では処理されない可能性があります。 問題はありません。

最初の実行では、データの大部分がオンプレミスに戻され、Windows Server に移動された後、Azure File Sync 経由でクラウドにバックアップされます。これには、次の条件によっては時間がかかることがあります。

* ダウンロードの帯域幅
* StorSimple クラウド サービスの呼び出し速度
* アップロードの帯域幅
* いずれかのサービスによって処理される必要がある項目 (ファイルとフォルダー) の数

最初の実行が完了した後、コマンドを再度実行します。

2 回目に転送する必要があるのは、前回の実行以降に発生した変更だけなので、処理は短時間で完了します。 それらの変更は、最近のものなので、まだ StorSimple のローカル環境に存在する可能性があります。 その場合、クラウドから呼び出す必要が減るため、さらに時間が短縮されます。 この 2 回目の実行の間に、まだ新しい変更が蓄積される可能性があります。

完了までにかかる時間が、許容されるダウンタイムになるまで、このプロセスを繰り返します。

ダウンタイムが許容できるものと見なされ、StorSimple の場所をオフラインにする準備ができたら、ここでそれを行います。たとえば、ユーザーがフォルダーにアクセスできないように SMB 共有を削除するか、StorSimple 上のこのフォルダーの内容が変更されないようにするための適切な手順を実行します。

最後に 1 回 RoboCopy ラウンドを実行します。 これにより、漏れている可能性があるすべての変更が取得されます。
この最後の手順にかかる時間は、RoboCopy のスキャンの速度に依存します。 前回の実行にかかった時間を測定することで、(ダウンタイムに相当する) 時間を見積もることができます。

Windows Server フォルダーに共有を作成し、必要に応じて、その共有を指すように DFS-N のデプロイを調整します。 StorSimple SMB 共有と同じ共有レベルのアクセス許可を設定してください。

これで、共通のルートまたはボリュームへの共有または共有のグループの移行は完了しました。 (同じ Azure ファイル共有に移行する必要があるとマップして決定した内容によります)。

これらの複数のコピーを並行して実行することができます。 一度に 1 つの Azure ファイル共有のスコープを処理することをお勧めします。

> [!WARNING]
> StorSimple から Windows Server にすべてのデータを移動したら、移行は完了です。Azure portal で***すべての***同期グループに戻り、クラウドを使った階層化ボリュームの空き領域の割合の値を、キャッシュの使用に適したもの (20% など) に調整します。 

クラウドを使った階層化ボリュームの空き領域ポリシーはボリューム レベルで動作し、複数のサーバー エンドポイントがそこから同期する可能性があります。 1 つのサーバー エンドポイントでも空き領域を調整し忘れた場合、同期では最も制限の厳しい規則が引き続き適用され、99% の空きディスク領域が確保されて、ローカル キャッシュが期待どおりに実行されなくなります。 アクセス頻度の低いアーカイブ データのみが含まれるボリューム用の名前空間のみを使用することを目的としている場合を除きます。

## <a name="troubleshoot"></a>トラブルシューティング

最も可能性の高い問題は、Windows Server 側で "*ボリュームがいっぱい*" になったために RoboCopy コマンドが失敗することです。 その場合は、アップロード速度よりダウンロード速度の方が速い可能性があります。 クラウドを使った階層化は 1 時間ごとに動作し、同期されたローカル環境の Windows Server ディスクから内容が退避されます。

同期を進行させ、クラウドを使った階層化にディスク領域を解放させます。 これは、Windows Server のエクスプローラーで確認できます。

Windows Server に十分な空き容量がある場合は、コマンドを再実行すると問題が解決されます。 このような状況が発生しても壊れるものは何もなく、安心して進めることができます。 コマンドを再実行する不便さだけが唯一の影響です。

他の Azure File Sync の問題が発生することもあります。
可能性は低いですが発生した場合は、**Azure File Sync のトラブルシューティング ガイド**を参照してください。

## <a name="relevant-links"></a>関連するリンク

移行に関するコンテンツ:

* [StorSimple 8000 シリーズ移行ガイド](storage-files-migration-storsimple-8000.md)

Azure File Sync のコンテンツ:

* [AFS の概要](https://aka.ms/AFS)
* [AFS デプロイ ガイド](storage-files-deployment-guide.md)
* [AFS のトラブルシューティング](storage-sync-files-troubleshoot.md)
