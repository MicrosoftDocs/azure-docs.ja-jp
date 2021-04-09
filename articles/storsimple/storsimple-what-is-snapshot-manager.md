---
title: StorSimple Snapshot Manager について | Microsoft Docs
description: StorSimple Snapshot Manager の概要、アーキテクチャ、および機能について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6094c31e-e2d9-4592-8a15-76bdcf60a754
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2214f085c30419cefb3f6f84139d5592873173f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017986"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>StorSimple Snapshot Manager の概要

## <a name="overview"></a>概要
StorSimple Snapshot Manager は、Microsoft Azure StorSimple 環境でのデータ保護とバックアップ管理を簡素化する、Microsoft 管理コンソール (MMC) スナップインです。 StorSimple Snapshot Manager では、データ センター内やクラウド内の Microsoft Azure StorSimple データを 1 つの統合ストレージ ソリューションとして管理できるため、バックアップ プロセスが簡素化され、コストが削減されます。

ここでは、StorSimple Snapshot Manager の概要、機能、Microsoft Azure StorSimple における役割について説明します。 

StorSimple デバイス、StorSimple Manager サービス、StorSimple Snapshot Manager、SharePoint 用 StorSimple アダプターを含む Microsoft Azure StorSimple システム全体の概要については、「 [StorSimple 8000 シリーズ: ハイブリッド クラウド ストレージ ソリューション](storsimple-overview.md)」を参照してください。 

> [!NOTE]
> * StorSimple Snapshot Manager を使用して、Microsoft Azure StorSimple Virtual Array (別名 StorSimple オンプレミス仮想デバイス) を管理することはできません。
> * StorSimple デバイスに StorSimple Update 2 をインストールする場合は、 **StorSimple Update 2 をインストールする前に**、最新バージョンの StorSimple Snapshot Manager をダウンロードしてインストールする必要があります。 最新バージョンの StorSimple Snapshot Manager には下位互換性があり、製品版のすべての Microsoft Azure StorSimple で動作します。 以前のバージョンの StorSimple Snapshot Manager を使用している場合は更新する必要があります (新しいバージョンをインストールする前に、以前のバージョンをアンインストールする必要はありません)。
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>StorSimple Snapshot Manager の目的とアーキテクチャ
StorSimple Snapshot Manager は、ローカルとクラウドのデータについて、特定の時点の整合性のあるバックアップ コピーを作成できる中央管理コンソールです。 たとえば、このコンソールを使用して次の操作を実行できます。

* ボリュームを構成、バックアップ、削除する。
* ボリューム グループを構成してバックアップ データのアプリケーション整合性を確保する。
* 事前に指定されたスケジュールでデータがバックアップされるようにバックアップ ポリシーを管理する。
* クラウドに保存して障害復旧に使用できる、ローカルとクラウドのスナップショットを作成する。

StorSimple Snapshot Manager によって、ホスト上の VSS プロバイダーに登録されているアプリケーションの一覧が取得されます。 次に、アプリケーション整合性バックアップを作成するために、アプリケーションが使用するボリュームがチェックされ、構成するボリューム グループが提案されます。 StorSimple Snapshot Manager では、これらのボリューム グループを使用して、アプリケーション整合性のあるバックアップ コピーを生成します  ("アプリケーション整合性" とは、関連するすべてのファイルとデータベースが同期されており、特定の時点におけるアプリケーションの正確な状態が反映されていることをいいます)。 

StorSimple Snapshot Manager のバックアップの形式は増分スナップショットであり、前回のバックアップ以降の変更部分だけがキャプチャされます。 そのため、バックアップに必要なストレージが減り、バックアップの作成と復元を短時間で実行できます。 StorSimple Snapshot Manager では、スナップショットでキャプチャされるデータのアプリケーション整合性を確保するために、Windows ボリューム シャドウ コピー サービス (VSS) を使用します  (詳細については、「Windows ボリューム シャドウ コピー サービスとの統合」をご覧ください)。StorSimple Snapshot Manager では、バックアップ スケジュールを作成することも、必要に応じて即時バックアップを作成することもできます。 バックアップからデータを復元する必要がある場合は、StorSimple Snapshot Manager を使用してローカル スナップショットまたはクラウド スナップショットのカタログから選択できます。 Azure StorSimple は、必要なときに必要なデータだけを復元するので、復元操作の実行中にデータの可用性が長時間損なわれることはありません。

![StorSimple Snapshot Manager のアーキテクチャ](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**StorSimple Snapshot Manager のアーキテクチャ** 

## <a name="support-for-multiple-volume-types"></a>各種ボリュームのサポート
StorSimple Snapshot Manager を使用して構成およびバックアップできるボリュームの種類は次のとおりです。 

* **ベーシック ボリューム** – ベーシック ボリュームは、ベーシック ディスク上の単一のパーティションです。 
* **シンプル ボリューム** – シンプル ボリュームは、単一のダイナミック ディスクからのディスク領域を含んだダイナミック ボリュームです。 シンプル ボリュームは、ディスク上の 1 つの領域、または同じディスク上の連結された複数の領域で構成されます  (シンプル ボリュームは、ダイナミック ディスク上にのみ作成できます)。シンプル ボリュームはフォールト トレラントではありません。
* **ダイナミック ボリューム** – ダイナミック ボリュームは、ダイナミック ディスク上に作成されるボリュームです。 ダイナミック ディスクでは、データベースを使用して、コンピューターのダイナミック ディスクに含まれているボリュームの情報を追跡します。 
* **ダイナミック ボリューム + ミラーリング** – ダイナミック ボリューム + ミラーリングは、RAID 1 アーキテクチャを土台として構築されます。 RAID 1 では、まったく同じデータを複数のディスクに書き込むことで、ミラー セットを生成します。 これにより、要求されたデータを格納するどのディスクでも読み取り要求に対応できます。
* **クラスターの共有ボリューム** – クラスターの共有ボリューム (CSV) では、フェールオーバー クラスター内の複数のノードが同じディスクに対して同時に読み取り/書き込みを行うことができます。 ドライブの所有権の変更や、マウント、マウントの解除、ボリュームの削除を行わずに、ノード間でのフェールオーバーを迅速に実行できます。 

> [!IMPORTANT]
> 同じスナップショット内で CSV と非 CSV を混在させないでください。 1 つのスナップショット内での CSV と非 CSV の混在はサポートされていません。 
> 
> 

StorSimple Snapshot Manager を使用すると、ボリューム グループ全体を復元したり、個々のボリュームを複製して個々のファイルを回復したりできます。

* [ボリュームとボリューム グループ](#volumes-and-volume-groups) 
* [バックアップの種類とバックアップ ポリシー](#backup-types-and-backup-policies) 

StorSimple Snapshot Manager の機能とその使用方法の詳細については、「 [StorSimple Snapshot Manager のユーザー インターフェイス](storsimple-use-snapshot-manager.md)」をご覧ください。

## <a name="volumes-and-volume-groups"></a>ボリュームとボリューム グループ
StorSimple Snapshot Manager を使用して、ボリュームを作成し、それらのボリュームをボリューム グループとして構成します。 

StorSimple Snapshot Manager では、ボリューム グループを使用して、アプリケーション整合性のあるバックアップ コピーを作成します。 "アプリケーション整合性" とは、関連するすべてのファイルとデータベースが同期されており、特定の時点におけるアプリケーションの正確な状態が反映されていることをいいます。 ボリューム グループ (*"コンシステンシー グループ"* とも呼ばれます) は、バックアップ/復元ジョブの基礎となります。

ボリューム グループはボリューム コンテナーとは異なります。 ボリューム コンテナーには、クラウド ストレージ アカウントと他の属性 (暗号化や帯域幅消費量など) を共有する 1 つ以上のボリュームが含まれます。 1 つのボリューム コンテナーに、仮想プロビジョニングされた最大 256 個の StorSimple ボリュームを含めることができます。 ボリューム コンテナーの詳細については、「 [StorSimple ボリューム コンテナーの管理](./storsimple-8000-manage-volume-containers.md)」をご覧ください。 ボリューム グループは、バックアップ操作を容易にするために構成するボリュームの集まりです。 異なるボリューム コンテナーに属する 2 つのボリュームを選択し、それらを同じボリューム グループに配置して、そのボリューム グループのバックアップ ポリシーを作成すると、適切なストレージ アカウントを使用して、適切なボリューム コンテナーで各ボリュームがバックアップされます。

> [!NOTE]
> ボリューム グループ内のすべてのボリュームは、1 つのクラウド サービス プロバイダーから取得する必要があります。
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Windows ボリューム シャドウ コピー サービスとの統合
StorSimple Snapshot Manager では、Windows ボリューム シャドウ コピー サービス (VSS) を使用して、アプリケーション整合性のあるデータをキャプチャします。 VSS は、VSS 対応アプリケーションとやり取りして増分スナップショットの作成を調整することで、アプリケーション整合性を促進します。 VSS は、スナップショットの作成時に、アプリケーションが一時的に非アクティブ状態 (休止状態) になるようにします。 

StorSimple Snapshot Manager における VSS の実装は、SQL Server および汎用の NTFS ボリュームと連携します。 このプロセスは次のとおりです。 

1. 要求元が VSS を呼び出し、ターゲット アプリケーション内のライター ソフトウェアから情報を収集するよう VSS に要求します。通常、要求元は、データ管理/保護ソリューション (StorSimple Snapshot Manager など) またはバックアップ アプリケーションです。
2. VSS は、データの説明を取得するためにライター コンポーネントに問い合わせます。 ライターは、バックアップ対象となるデータの説明を返します。 
3. VSS は、バックアップに備えてアプリケーションを準備するようライターに合図を送ります。 ライターは、開いているトランザクションを完了し、トランザクション ログを更新するなどして、バックアップ用のデータを準備し、VSS に通知します。
4. VSS は、アプリケーションのデータ ストアを一時的に停止するようライターに指示し、シャドウ コピーの作成中にデータがボリュームに書き込まれないようにします。 このステップにより、データ整合性が確保されます。所要時間は 60 秒程度です。
5. VSS は、シャドウ コピーを作成するようプロバイダーに指示します。 プロバイダー (ソフトウェア ベースまたはハードウェア ベース) は、現在稼働しているボリュームを適切に操作し、要求に応じてボリュームのシャドウ コピーを作成します。 プロバイダーは、シャドウ コピーの作成が完了すると、VSS に通知します。
6. VSS は、ライターを介して、I/O を再開できることをアプリケーションに通知すると共に、シャドウ コピーの作成中に I/O が正常に一時停止されていたことを確認します。 
7. コピーが正常に完了していた場合、VSS はコピーの場所を要求元に返します。 
8. シャドウ コピーの作成中にデータが書き込まれていた場合、バックアップに整合性がなくなります。 VSS はシャドウ コピーを削除し、要求元に通知します。 要求元は、バックアップ プロセスを自動的に繰り返すことも、後で再試行するよう管理者に通知することもできます。

次の図を参照してください。

![VSS のプロセス](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Windows ボリューム シャドウ コピー サービスのプロセス** 

## <a name="backup-types-and-backup-policies"></a>バックアップの種類とバックアップ ポリシー
StorSimple Snapshot Manager では、データをバックアップし、ローカルとクラウドに保存できます。 StorSimple Snapshot Manager を使用してデータをすぐにバックアップすることも、バックアップ ポリシーを使用して、自動バックアップのスケジュールを作成することもできます。 バックアップ ポリシーでは、保持するスナップショットの数を指定することもできます。 

### <a name="backup-types"></a>バックアップの種類
StorSimple Snapshot Manager を使用して作成できるバックアップの種類は次のとおりです。

* **ローカル スナップショット** - ローカル スナップショットは、StorSimple デバイスに保存されているボリューム データの特定の時点のコピーです。 通常、この種のバックアップの作成と復元は短時間で実行できます。 ローカル スナップショットは、ローカル バックアップ コピーと同様に使用できます。
* **クラウド スナップショット** – クラウド スナップショットは、クラウドに保存されているボリューム データの特定時点のコピーです。 クラウド スナップショットは、別のオフサイト ストレージ システムにレプリケートされたスナップショットに相当します。 クラウド スナップショットは、ディザスター リカバリー シナリオで特に役立ちます。

### <a name="on-demand-and-scheduled-backups"></a>オンデマンド バックアップとスケジュールされたバックアップ
StorSimple Snapshot Manager では、すぐに作成される 1 回限りのバックアップを開始することも、バックアップ ポリシーを使用して、定期的なバックアップ操作をスケジュールすることもできます。

バックアップ ポリシーとは、定期的なバックアップをスケジュールするために使用できる一連の自動化されたルールです。 バックアップ ポリシーでは、特定のボリューム グループのスナップショットを作成する頻度やパラメーターを定義できます。 ポリシーを使用して、ローカル スナップショットとクラウド スナップショットの両方について、開始日と有効期限、時刻、頻度、保持要件などを指定できます。 ポリシーは定義した直後に適用されます。 

StorSimple Snapshot Manager を使用して、必要に応じてバックアップ ポリシーを構成または再構成できます。 

作成するバックアップ ポリシーごとに、次の情報を構成します。

* **名前** – 選択したバックアップ ポリシーの一意の名前。
* **タイプ** – バックアップ ポリシーのタイプ (ローカル スナップショットまたはクラウド スナップショット)。
* **ボリューム グループ** – 選択したバックアップ ポリシーの割り当て先となるボリューム グループ。
* **保持** – 保持するバックアップ コピーの数。 **[すべて]** チェック ボックスをオンにした場合、ボリュームあたりの最大バックアップ コピー数に達するまで、すべてのバックアップ コピーが保持されます。最大数に達した時点でポリシーがエラーとなり、エラー メッセージが生成されます。 保持するバックアップの数 (1 ～ 64) を指定することもできます。
* **日付** – バックアップ ポリシーの作成日。

バックアップ ポリシーの構成については、「 [StorSimple Snapshot Manager を使用したバックアップ ポリシーの作成と管理](storsimple-snapshot-manager-manage-backup-policies.md)」をご覧ください。

### <a name="backup-job-monitoring-and-management"></a>バックアップ ジョブの監視と管理
StorSimple Snapshot Manager を使用して、今後予定されているバックアップ ジョブや完了したバックアップ ジョブの監視と管理を行うことができます。 さらに、StorSimple Snapshot Manager では、最大 64 個の完了したバックアップのカタログが提供されます。 このカタログを使用して、ボリュームまたは個々のファイルを検索し、復元できます。 

バックアップ ジョブの監視については、「 [StorSimple Snapshot Manager を使用したバックアップ ジョブの表示と管理](storsimple-snapshot-manager-manage-backup-jobs.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ
* [StorSimple Snapshot Manager を使用した StorSimple ソリューションの管理の詳細](storsimple-snapshot-manager-admin.md)
* [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220)をダウンロードする。