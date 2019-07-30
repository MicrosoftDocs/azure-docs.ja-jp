---
title: NetBackup を使用したバックアップ ターゲットとしての StorSimple 8000 シリーズ | Microsoft Docs
description: Veritas NetBackup を使用した StorSimple バックアップ ターゲットの構成について説明します。
services: storsimple
documentationcenter: ''
author: harshakirank
manager: matd
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2017
ms.author: matd
ms.openlocfilehash: 957fff73f2406e0e057a7c978dd76a6bd9c156b7
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876203"
---
# <a name="storsimple-as-a-backup-target-with-netbackup"></a>NetBackup を使用したバックアップ ターゲットとしての StorSimple

## <a name="overview"></a>概要

Azure StorSimple は、Microsoft が提供するハイブリッド クラウド ストレージ ソリューションです。 StorSimple では、Azure ストレージ アカウントをオンプレミス ソリューションの拡張機能として使用し、オンプレミス ストレージとクラウド ストレージにまたがって自動的にデータを階層化することでデータ急増の難点に対応できます。

この記事では、Veritas NetBackup と StorSimple の統合について取り上げ、両方のソリューションを統合するためのベスト プラクティスについて説明します。 StorSimple との統合を最適化できるように Veritas NetBackup をセットアップする方法についての推奨事項も紹介します。 個別のバックアップ要件とサービス レベル アグリーメント (SLA) に合わせて Veritas NetBackup を最適にセットアップする方法については、Veritas のベスト プラクティス、バックアップ アーキテクト、および管理者に従います。

構成手順と主要な概念について説明しますが、この記事は構成またはインストールの方法を詳しく説明するガイドではありません。 基本的なコンポーネントとインフラストラクチャが正常に動作しており、ここで説明する概念をサポートする準備が整っていることを前提としています。

### <a name="who-should-read-this"></a>対象読者

この記事の情報は特に、ストレージ、Windows Server 2012 R2、イーサネット、クラウド サービス、Veritas NetBackup に関する知識を持つバックアップ管理者、ストレージ管理者、ストレージ アーキテクトの役に立ちます。

### <a name="supported-versions"></a>サポートされているバージョン

-   NetBackup 7.7.x 以降のバージョン
-   [StorSimple Update 3 以降のバージョン](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>StorSimple をバックアップ ターゲットとして使用する理由

StorSimple は、以下の理由からバックアップ ターゲットに適しています。

-   変更を行うことなくバックアップ先として使用できる、バックアップ アプリケーション用の標準的なローカル ストレージが提供されます。 StorSimple を使用すると、最新のバックアップから復元を素早く行うこともできます。
-   クラウド階層化が Azure クラウド ストレージ アカウントとシームレスに統合されており、費用効果の高い Azure Storage を使用できます。
-   障害復旧のためのオフサイト ストレージを自動的に提供します。

## <a name="key-concepts"></a>主要な概念

すべてのストレージ ソリューションと同様、ソリューションのストレージ性能、SLA、変更率、容量増加の必要性について慎重に評価することが不可欠です。 クラウドの階層を導入することで、お客様のクラウドへのアクセス時間と処理能力が、StorSimple のジョブを実行する能力に根本的な影響を与えます。

StorSimple は、適切に定義されたデータのワーキング セット (ホットデータ) 上で動作するアプリケーションにストレージを提供するよう設計されています。 このモデルでは、データのワーキング セットはローカルの階層に格納され、残りの非ワーキング/コールド/アーカイブ セットはクラウドに格納されます。 このモデルを示したのが次のグラフです。 ほぼ平らな緑の線は、StorSimple デバイスのローカル階層に格納されているデータを表しています。 赤い線は、すべての階層で StorSimple ソリューションに格納されているデータの総量を表しています。 平らな緑の線と右上がりの曲線を描く赤い線の間の空間は、クラウドに格納されているデータの総量を表しています。

**StorSimple の階層化**
![StorSimple の階層化の図](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)

このグラフを見ると、StorSimple をバックアップ ターゲットとして稼働させることが適切であることがわかります。 StorSimple を使用すると次のことが可能になります。
-   データのローカル ワーキングセットから最も頻度に復元を実行する。
-   復元の頻度が低いオフサイトのディザスター リカバリーと過去のデータ用にクラウドを使用する。

## <a name="storsimple-benefits"></a>StorSimple の利点

StorSimple は、オンプレミスとクラウドのストレージへのシームレスなアクセスを生かして、Microsoft Azure とシームレスに統合するオンプレミス ソリューションを提供します。

StorSimple では、ソリッドステート デバイス (SSD) ストレージおよびシリアル接続 SCSI (SAS) ストレージを備えたオンプレミス デバイスと、Azure Storage の間で自動的に階層化が行われます。 自動階層化では、アクセス頻度の高いデータは SSD 層と SAS 層でローカルに保管されます。 アクセス頻度の低いデータは Azure Storage に移動されます。

StorSimple には次の利点があります。

-   クラウドを使用してこれまでにない重複除去レベルを実現するための一意の重複除去と圧縮アルゴリズム
-   高可用性
-   Azure を使用した geo レプリケーション
-   Azure の統合
-   クラウドのデータ暗号化
-   ディザスター リカバリーとコンプライアンスの強化

StorSimple には 2 つの主要なデプロイメント シナリオ (プライマリ バックアップ ターゲットとセカンダリ バックアップ ターゲット) が用意されていますが、基本的にはシンプルなブロック ストレージ デバイスです。 StorSimple では、すべての圧縮と重複除去が行われます。 クラウドとアプリケーションおよびファイル システムの間で、データはシームレスに送受信されます。

StorSimple について詳しくは、「[StorSimple 8000 シリーズ:ハイブリッド クラウド ストレージ ソリューション](storsimple-overview.md)」をご覧ください。 また、[StorSimple 8000 シリーズの技術仕様](storsimple-technical-specifications-and-compliance.md)に関するページも参照してください。

> [!IMPORTANT]
> バックアップ ターゲットとして StorSimple デバイスを使用できるのは、StorSimple 8000 の Update 3 以降のバージョンのみです。

## <a name="architecture-overview"></a>アーキテクチャの概要

以下の各表は、デバイス モデルとそのアーキテクチャをまとめたものです。

**StorSimple のローカル ストレージとクラウド ストレージの容量**

| ストレージの容量       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| ローカル ストレージの容量 | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| クラウド ストレージの容量 | &gt; 200 TiB\* | &gt; 500 TiB\* |

\*ストレージの容量は、重複除去または圧縮がないことを前提としています。

**StorSimple のプライマリ バックアップとセカンダリ バックアップの容量**

| バックアップ シナリオ  | ローカル ストレージの容量  | クラウド ストレージの容量  |
|---|---|---|
| プライマリ バックアップ  | 回復ポイントの目標 (RPO) を達成する高速復旧のためにローカル ストレージに格納される最近のバックアップ | バックアップ履歴 (RPO) はクラウドの容量に収まります |
| セカンダリ バックアップ | クラウドの容量では、バックアップ データのセカンダリ コピーを格納できます  | 該当なし  |

## <a name="storsimple-as-a-primary-backup-target"></a>プライマリ バックアップ ターゲットとしての StorSimple

このシナリオでは、StorSimple ボリュームは、バックアップ アプリケーションにとって唯一のバックアップ用リポジトリになります。 次の図は、すべてのバックアップで、バックアップと復元の両方に StorSimple 階層化ボリュームを使用するソリューション アーキテクチャを表しています。

![プライマリ バックアップ ターゲットとしての StorSimple の論理図](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>プライマリ ターゲット バックアップの論理的ステップ

1.  バックアップ サーバーが対象のバックアップ エージェントに接続され、バックアップ エージェントからデータがバックアップ サーバーに転送されます。
2.  バックアップ サーバーから StorSimple 階層化ボリュームにデータが書き込まれます。
3.  バックアップ サーバーがカタログ データベースを更新して、バックアップ ジョブを完了します。
4.  スナップショット スクリプトにより、StorSimple Snapshot Manager (起動または削除) がトリガーされます。
5.  保持ポリシーに基づき、バックアップ サーバーから期限切れのバックアップが削除されます。

### <a name="primary-target-restore-logical-steps"></a>プライマリ ターゲットの復元の論理的ステップ

1.  バックアップ サーバーが、ストレージ リポジトリから適切なデータの復元を開始します。
2.  バックアップ エージェントがバックアップ サーバーからデータを受信します。
3.  バックアップ サーバーが復元ジョブを完了します。

## <a name="storsimple-as-a-secondary-backup-target"></a>セカンダリ バックアップ ターゲットとしての StorSimple

このシナリオでの StorSimple ボリュームの主な用途は、長期間の保持またはアーカイブです。

次の図は、初回のバックアップと復元のターゲットで高性能ボリュームにしているアーキテクチャを表しています。 これらのバックアップは、設定したスケジュールで StorSimple 階層化ボリュームにコピーおよびアーカイブされます。

高性能ボリュームのサイズは、保持ポリシーの容量とパフォーマンスの要件に対応できるものにする必要があります。

![セカンダリ バックアップ ターゲットとしての StorSimple の論理図](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>セカンダリ ターゲット バックアップの論理的ステップ

1.  バックアップ サーバーが対象のバックアップ エージェントに接続され、バックアップ エージェントからデータがバックアップ サーバーに転送されます。
2.  バックアップ サーバーが、高性能ストレージにデータを書き込みます。
3.  バックアップ サーバーがカタログ データベースを更新して、バックアップ ジョブを完了します。
4.  保持ポリシーに基づき、バックアップ サーバーが StorSimple にバックアップをコピーします。
5.  スナップショット スクリプトにより、StorSimple Snapshot Manager (起動または削除) がトリガーされます。
6.  バックアップ サーバーが、保持ポリシーに基づいて、期限切れのバックアップを削除します。

### <a name="secondary-target-restore-logical-steps"></a>セカンダリ ターゲットの復元の論理的ステップ

1.  バックアップ サーバーが、ストレージ リポジトリから適切なデータの復元を開始します。
2.  バックアップ エージェントがバックアップ サーバーからデータを受信します。
3.  バックアップ サーバーが復元ジョブを完了します。

## <a name="deploy-the-solution"></a>ソリューションのデプロイ方法

このソリューションをデプロイするには、次の 3 つの手順が必要になります。
1. ネットワーク インフラストラクチャを準備する。
2. StorSimple デバイスをバックアップ ターゲットとしてデプロイする。
3. Veritas NetBackup をデプロイする。

各手順について、以降のセクションで詳しく説明します。

### <a name="set-up-the-network"></a>ネットワークのセットアップ

StorSimple は Azure クラウドと統合されたソリューションであるため、Azure クラウドに有効かつ正常に接続できる必要があります。 この接続は、クラウド スナップショット、データ管理、メタデータの転送のような操作のほか、Azure クラウド ストレージに古くアクセスの少ないデータを格納するのに使用されます。

ソリューションを最適に実行するには、次のネットワークのベスト プラクティスに従うことをお勧めします。

-   Azure と StorSimple 階層化を接続するリンクは、実際の帯域幅要件を満たしている必要があります。 これを達成するには、RPO と回復時刻の目標 (RTO) の SLA に対応した適切なサービスの品質 (QoS) レベルをインフラストラクチャ スイッチに適用します。

-   Azure Blob Storage へのアクセスの最大待機時間は、80 ミリ秒程度である必要があります。

### <a name="deploy-storsimple"></a>StorSimple のデプロイ方法

StorSimple のデプロイの詳細なガイダンスについては、[オンプレミスの StorSimple デバイスのデプロイ](storsimple-deployment-walkthrough-u2.md)に関するページを参照してください。

### <a name="deploy-netbackup"></a>NetBackup のデプロイ方法

NetBackup 7.7.x のデプロイの詳細なガイダンスについては、[NetBackup 7.7.x のドキュメント](http://www.veritas.com/docs/000094423)を参照してください。

## <a name="set-up-the-solution"></a>ソリューションのセットアップ

このセクションでは、構成の例をいくつかご紹介します。 次の例と推奨事項では、最も基本的な実装について説明します。 この実装例は、直接的には実際のバックアップ要件に当てはまらない可能性があります。

### <a name="set-up-storsimple"></a>StorSimple のセットアップ

| StorSimple のデプロイメント タスク  | その他のコメント |
|---|---|
| オンプレミスの StorSimple デバイスのデプロイ。 | サポートされているバージョン:Update 3 以降。 |
| バックアップ ターゲットを有効にする。 | バックアップ ターゲット モードの有効化または無効化、および状態の取得には次のコマンドを使用します。 詳細については、[StorSimple デバイスへのリモート接続](storsimple-remote-connect.md)に関するページをご覧ください。</br> バックアップ モードを有効にする: `Set-HCSBackupApplianceMode -enable` </br> バックアップ モードを無効にする: `Set-HCSBackupApplianceMode -disable` </br> バックアップ モード設定の現在の状態を取得する: `Get-HCSBackupApplianceMode` |
| バックアップ データを格納するボリュームの共通ボリューム コンテナーを作成する。 ボリューム コンテナー内のすべてのデータが重複除去されます。 | StorSimple のボリューム コンテナーでは、重複除去のドメインを定義します。  |
| StorSimple ボリュームを作成する。 | ボリューム サイズはクラウド スナップショットの実行時間に影響するため、予想される使用量に可能な限り近いサイズのボリュームを作成します。 ボリューム サイズの決定方法については、「[保持ポリシー](#retention-policies)」を参照してください。</br> </br> StorSimple 階層化ボリュームを使用し、 **[アクセス頻度の低いアーカイブ データにこのボリュームを使用します]** チェック ボックスをオンにします。 </br> ローカル固定ボリュームのみの使用はサポートされていません。 |
| すべてのバックアップ ターゲット ボリュームに対し、一意の StorSimple バックアップ ポリシーを作成する。 | ボリュームの整合性グループは、StorSimple のバックアップ ポリシーで定義されます。 |
| スナップショットに有効期限があるためスケジュールを無効にする。 | スナップショットは後処理オペレーションとしてトリガーされます。 |

### <a name="set-up-the-host-backup-server-storage"></a>ホスト バックアップ サーバー ストレージのセットアップ

次のガイドラインに従って、ホスト バックアップ サーバー ストレージをセットアップします。  

- スパン ボリューム (Windows ディスクの管理で作成されたもの) は使用しないでください。スパン ボリュームはサポートされていません。
- アロケーション サイズ 64 KB の NTFS を使用してボリュームをフォーマットします。
- StorSimple ボリュームを NetBackup サーバーに直接マッピングします。
    - 物理サーバーに iSCSI を使用します。
    - 仮想サーバーにパススルー ディスクを使用します。


## <a name="best-practices-for-storsimple-and-netbackup"></a>StorSimple と NetBackup のベスト プラクティス

以下の各セクションのガイドラインに従って、ソリューションを設定します。

### <a name="operating-system-best-practices"></a>オペレーティング システムのベスト プラクティス

- Windows Server の暗号化と、NTFS ファイル システムの重複除去を無効にします。
- StorSimple ボリュームでの Windows Server の最適化を無効にします。
- StorSimple ボリュームでの Windows Server のインデックス作成を無効にします。
- (StorSimple ボリュームに対してではなく) ソース ホストでウィルス対策スキャンを実行します。
- タスク マネージャーで、既定の [ Windows Server のメンテナンス](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx)を無効にします。 これは、次の方法のいずれかで実行します。
  - Windows タスク スケジューラでメンテナンス コンフィギュレータを無効にする。
  - Windows Sysinternals から [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) をダウンロードする。 PsExec のダウンロード後、Windows PowerShell を管理者として実行し、次のように入力します。
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>StorSimple のベスト プラクティス

-   StorSimple デバイスが [Update 3 以降](storsimple-install-update-3.md)に更新されていることを確認します。
-   iSCSI とクラウド トラフィックを分離します。 StorSimple とバックアップ サーバーの間のトラフィックには専用の iSCSI 接続を使用します。
-   StorSimple デバイスが専用のバックアップ ターゲットであることを確認します。 混合ワークロードは、RTO と RPO に影響を与えるため、サポートされていません。

### <a name="netbackup-best-practices"></a>NetBackup のベスト プラクティス

-   NetBackup データベースは、サーバーにローカルである必要があります。StorSimple ボリューム上に配置しないでください。
-   障害復旧のために、NetBackup データベースは StorSimple ボリューム上にバックアップします。
-   このソリューションでは、NetBackup のフル バックアップと増分バックアップ (NetBackup では差分バックアップとも呼ばれます) をサポートします。 合成バックアップや累積増分バックアップは使用しないことをお勧めします。
-   バックアップ データ ファイルに含めるデータは、特定のジョブのものだけに限定する必要があります。 たとえば、複数のジョブにまたがってメディアを追加することはできません。

これらの要件を実装するための最新の NetBackup 設定およびベスト プラクティスについては、[www.veritas.com](https://www.veritas.com) の NetBackup ドキュメントを参照してください。


## <a name="retention-policies"></a>保持ポリシー

最も一般的なタイプのバックアップ保持ポリシーの 1 つが、Grandfather-Father-Son (GFS) ポリシーです。 GFS ポリシーでは、増分バックアップを 1 日 1 回実行し、完全バックアップは週 1 回および月 1 回実行します。 このポリシーでは StorSimple 階層化ボリュームを 6 個使用します。ボリュームの 1 つには週単位、月単位、および年単位の完全バックアップを格納し、残りの 5 個には日単位の増分バックアップを格納します。

次の例では、GFS 方式を使用しています。 この例では、次の条件を前提としています。

-   非重複除去または圧縮データを使用します。
-   完全バックアップはそれぞれ 1 TiB です。
-   毎日の増分バックアップはそれぞれ 500 GiB です。
-   4 個の週単位バックアップは 1 か月間保持します。
-   12 個の月単位バックアップは 1 年間保持します。
-   1 個の年単位バックアップは 10 年間保持します。

上記の前提条件に基づき、月単位と年単位の完全バックアップ用に 26 TiB の StorSimple 階層化ボリュームを作成します。 日単位の増分バックアップそれぞれに対し、5 TiB の StorSimple 階層化ボリュームを作成します。

| バックアップ タイプとリテンション | サイズ (TiB) | GFS 乗数\* | 合計容量 (TiB)  |
|---|---|---|---|
| 週単位 - 完全 | 1 | 4  | 4 |
| 毎日 - 増分 | 0.5 | 20 (1 か月あたり同じ週数をサイクルする) | 12 (追加のクォータに対し 2) |
| 月単位 - 完全 | 1 | 12 | 12 |
| 年単位 - 完全 | 1  | 10 | 10 |
| GFS 要件 |   | 38 |   |
| 追加のクォータ  | 4  |   | GFS 要件合計 42  |

\* GFS 乗数は、バックアップ ポリシー要件を遵守するために保護および保持する必要があるコピー数を指します。

## <a name="set-up-netbackup-storage"></a>NetBackup ストレージのセットアップ

### <a name="to-set-up-netbackup-storage"></a>NetBackup ストレージをセットアップするには

1.  NetBackup 管理コンソールで、 **[Media and Device Management (メディアとデバイスの管理)]**  >  **[Devices (デバイス)]**  >  **[Disk Pools (ディスク プール)]** の順に選択します。 Disk Pool Configuration (ディスク プールの構成) ウィザードで、ストレージ サーバーの種類として **[AdvancedDisk]** を選択し、 **[Next (次へ)]** を選択します。

    ![NetBackup 管理コンソール、Disk Pool Configuration (ディスク プールの構成) ウィザード](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

2.  サーバーを選択し、 **[Next (次へ)]** を選択します。

    ![NetBackup 管理コンソール、サーバーを選択する](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

3.  StorSimple ボリュームを選択します。

    ![NetBackup 管理コンソール、StorSimple ボリューム ディスクを選択する](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

4.  バックアップ ターゲットの名前を入力し、 **[Next (次へ)]**  >  **[Next (次へ)]** の順に選択して、ウィザードを終了します。

5.  設定を確認し、 **[Finish (完了)]** を選択します。

6.  各ボリューム割り当ての最後に、「[StorSimple と NetBackup のベスト プラクティス](#best-practices-for-storsimple-and-netbackup)」に記載されている推奨事項に合わせて、ストレージ デバイスの設定を変更します。

7. StorSimple ボリュームの割り当てが完了するまで、手順 1. ～ 6. を繰り返します。

    ![NetBackup 管理コンソール、ディスクの構成](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>プライマリ バックアップ ターゲットとして StorSimple をセットアップする

> [!NOTE]
> クラウドに階層化されたバックアップからのデータの復元は、クラウドのスピードで実行されます。

次の図は、バックアップ ジョブに対する一般的なボリュームのマッピングを示しています。 この場合、週単位のバックアップはすべて "土曜日 - 完全" ディスクにマッピングし、増分バックアップは月曜日から金曜日の増分ディスクにマッピングしています。 すべてのバックアップと復元は、StorSimple 階層化ボリュームから行われます。

![プライマリ バックアップ ターゲット構成の論理図](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>プライマリ バックアップ ターゲットとしての StorSimple の GFS スケジュールの例

4 週間、月単位、および年単位の GFS ローテーション スケジュールの例を以下に示します。

| 頻度/バックアップ タイプ | 完全 | 増分 (1 - 5 日目)  |   
|---|---|---|
| 週単位 (第 1 - 4 週) | 土曜日 | 月曜日 - 金曜日 |
| 月単位  | 土曜日  |   |
| 年単位 | 土曜日  |   |

## <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>StorSimple ボリュームを NetBackup バックアップ ジョブに割り当てる

次の手順は、NetBackup とターゲット ホストが NetBackup エージェントのガイドラインに従って構成されていることを前提としています。

### <a name="to-assign-storsimple-volumes-to-a-netbackup-backup-job"></a>StorSimple ボリュームを NetBackup バックアップ ジョブに割り当てるには

1. NetBackup 管理コンソールで **[NetBackup Management (NetBackup 管理)]** を選択し、 **[Policies (ポリシー)]** を右クリックして **[New Policy (新しいポリシー)]** を選択します。

   ![NetBackup 管理コンソール、新しいポリシーを作成する](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

2. **[Add a New Policy (新しいポリシーの追加)]** ダイアログ ボックスでポリシーの名前を入力し、 **[Use Policy Configuration Wizard (ポリシー構成ウィザードを使用する)]** チェック ボックスをオンにします。 **[OK]** を選択します。

   ![NetBackup 管理コンソール、[Add a New Policy (新しいポリシーの追加)] ダイアログ ボックス](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

3. Backup Policy Configuration (バックアップ ポリシーの構成) ウィザードでバックアップの種類を選択し、 **[Next (次へ)]** を選択します。

   ![NetBackup 管理コンソール、バックアップの種類を選択する](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

4. ポリシーの種類を選択するには、 **[Standard (標準)]** を選択し、 **[Next (次へ)]** を選択します。

   ![NetBackup 管理コンソール、ポリシーの種類を選択する](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

5. ホストを選択し、 **[Detect client operating system (クライアント オペレーティング システムを検出する)]** チェック ボックスをオンにして、 **[Add (追加)]** を選択します。 **[次へ]** を選択します。

   ![NetBackup 管理コンソール、新しいポリシーのクライアントを一覧表示する](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

6. バックアップするドライブを選択します。

   ![NetBackup 管理コンソール、新しいポリシーのためのバックアップの選択](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

7. バックアップ ローテーション要件を満たす頻度と保有期間の値を選択します。

   ![NetBackup 管理コンソール、新しいポリシーのためのバックアップ頻度とローテーション](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

8. **[Next (次へ)]**  >  **[Next (次へ)]**  >  **[Finish (完了)]** の順に選択します。  ポリシーが作成されたら、スケジュールを変更できます。

9. 作成したポリシーを選択して展開し、 **[Schedules (スケジュール)]** を選択します。

   ![NetBackup 管理コンソール、新しいポリシーのスケジュール](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

10. **[Differential-Inc (差分 - 増分)]** を右クリックし、 **[Copy to new (新規にコピー)]** を選択して、 **[OK]** を選択します。

    ![NetBackup 管理コンソール、新しいポリシーにスケジュールをコピーする](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

11. 新しく作成したスケジュールを右クリックし、 **[Change (変更)]** を選択します。

12. **[Attributes (属性)]** タブで **[Override policy storage selection (ポリシー ストレージの選択をオーバーライドする)]** チェック ボックスをオンにし、月曜日の増分バックアップが格納されるボリュームを選択します。

    ![NetBackup 管理コンソール、スケジュールを変更する](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

13. **[Start Window (開始時間帯)]** タブでバックアップの時間帯を選択します。

    ![NetBackup 管理コンソール、開始時間帯を変更する](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

14. **[OK]** を選択します。

15. 各増分バックアップに対して、手順 10. ～ 14. を繰り返します。 適切なボリュームを選択し、作成する各バックアップのスケジュールを設定します。

16. **[Differential-Inc (差分 - 増分)]** スケジュールを右クリックし、削除します。

17. バックアップのニーズに合わせて、完全スケジュールを変更します。

    ![NetBackup 管理コンソール、完全スケジュールを変更する](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

18. 開始時間帯を変更します。

    ![NetBackup 管理コンソール、開始時間帯を変更する](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

19. 最終的なスケジュールは次のようになります。

    ![NetBackup 管理コンソール、最終的なスケジュール](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>セカンダリ バックアップ ターゲットとして StorSimple をセットアップする

> [!NOTE]
>クラウドに階層化済みのバックアップからのデータ復元は、クラウドのスピードで実行されます。

このモデルでは、一時キャッシュとして機能する (StorSimple 以外の) ストレージ メディアが必要です。 たとえば、Redundant Array of Independent Disks (RAID) ボリュームを使用して、領域、入出力 (I/O)、および帯域幅に対応することができます。 RAID 5、50、10 を使用することをお勧めします。

次の図は、一般的な保持期間が短い (サーバーに) ローカルなボリュームと、保持期間が長いアーカイブ ボリュームを表しています。 このシナリオでは、すべてのバックアップは (サーバーに) ローカルな RAID ボリュームで実行されます。 これらのバックアップは、アーカイブ ボリュームに定期的に複製、アーカイブされます。 (サーバーに) ローカルな RAID ボリュームのサイズは、短期間の保持容量とパフォーマンスの要件に対応できるものにする必要があります。

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>セカンダリ バックアップ ターゲットとしての StorSimple の GFS の例

![セカンダリ バックアップ ターゲットとしての StorSimple の論理図](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

次の表に、ローカル ディスクおよび StorSimple ディスク上で実行するようにバックアップを設定する方法を示します。 この表には、個々および全体の容量要件も記載されています。

### <a name="backup-configuration-and-capacity-requirements"></a>バックアップの構成と容量要件

| バックアップ タイプとリテンション | 構成済みストレージ | サイズ (TiB) | GFS 乗数 | 合計容量\* (TiB) |
|---|---|---|---|---|
| 第 1 週 (完全と増分) |ローカル ディスク (短期)| 1 | 1 | 1 |
| StorSimple 第 2 - 4 週 |StorSimple ディスク (長期) | 1 | 4 | 4 |
| 月単位 - 完全 |StorSimple ディスク (長期) | 1 | 12 | 12 |
| 年単位 - 完全 |StorSimple ディスク (長期) | 1 | 1 | 1 |
|GFS ボリュームのサイズ要件 |  |  |  | 18*|

\* 合計容量は、StorSimple ディスクの 17 TiB とローカル RAID ボリュームの 1 TiB の合計です。


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>GFS スケジュールの例:週、月、年単位の GFS ローテーション スケジュール

| 週 | 完全 | 増分 (1 日目) | 増分 (2 日目) | 増分 (3 日目) | 増分 (4 日目) | 増分 (5 日目) |
|---|---|---|---|---|---|---|
| 第 1 週 | ローカル RAID ボリューム  | ローカル RAID ボリューム | ローカル RAID ボリューム | ローカル RAID ボリューム | ローカル RAID ボリューム | ローカル RAID ボリューム |
| 第 2 週 | StorSimple 第 2 - 4 週 |   |   |   |   |   |
| 第 3 週 | StorSimple 第 2 - 4 週 |   |   |   |   |   |
| 第 4 週 | StorSimple 第 2 - 4 週 |   |   |   |   |   |
| 月単位 | StorSimple 月単位 |   |   |   |   |   |
| 年単位 | StorSimple 年単位  |   |   |   |   |   |


## <a name="assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>StorSimple ボリュームを NetBackup アーカイブおよび複製ジョブに割り当てる

NetBackup にはストレージおよびメディア管理用のさまざまなオプションがあるため、ストレージ ライフサイクル ポリシー (SLP) の要件を適切に評価するには、Veritas または NetBackup アーキテクトに相談することをお勧めします。

初期ディスク プールを定義した後、さらに 3 つのストレージ ライフサイクル ポリシーを定義する必要があります (合計で 4 つのポリシー)。
* ローカル RAID ボリューム
* StorSimple 第 2 ～ 4 週
* StorSimple 月単位 - 完全
* StorSimple 年単位- 完全

### <a name="to-assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>NetBackup アーカイブおよび複製ジョブに StorSimple ボリュームを割り当てるには

1. NetBackup 管理コンソールで、 **[Storage (ストレージ)]**  >  **[Storage Lifecycle Policies (ストレージ ライフサイクル ポリシー)]**  >  **[New Storage Lifecycle Policy (新しいストレージ ライフサイクル ポリシー)]** の順に選択します。

   ![NetBackup 管理コンソール、新しいストレージ ライフサイクル ポリシー](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

2. スナップショットの名前を入力し、 **[Add (追加)]** を選択します。

3. **[New Operation (新しい操作)]** ダイアログ ボックスの **[Properties (プロパティ)]** タブで、 **[Operation (操作)]** の **[Backup (バックアップ)]** を選択します。 **[Destination storage (対象ストレージ)]** 、 **[Retention type (保持期間の種類)]** 、 **[Retention period (保持期間)]** で、目的の値を選択します。 **[OK]** を選択します。

   ![NetBackup 管理コンソール、[New Operation (新しい操作)] ダイアログ ボックス](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

   これで、最初のバックアップ操作とリポジトリが定義されます。

4. 前の操作を選択して強調表示し、 **[Add (追加)]** を選択します。 **[Change Storage Operation (ストレージ操作の変更)]** ダイアログ ボックスの **[Destination storage (対象ストレージ)]** 、 **[Retention type (保持期間の種類)]** 、 **[Retention period (保持期間)]** で、目的の値を選択します。

   ![NetBackup 管理コンソール、[Change Storage Operation (ストレージ操作の変更)] ダイアログ ボックス](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

5. 前の操作を選択して強調表示し、 **[Add (追加)]** を選択します。 **[New Storage Lifecycle Policy (新しいストレージ ライフサイクル ポリシー)]** ダイアログ ボックスで、1 年間の毎月のバックアップを追加します。

   ![NetBackup 管理コンソール、[New Storage Lifecycle Policy (新しいストレージ ライフサイクル ポリシー)] ダイアログ ボックス](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

6. 必要な包括的 SLP 保持ポリシーを作成し終わるまで、手順 4. ～ 5. を繰り返します。

   ![NetBackup 管理コンソール、[New Storage Lifecycle Policy (新しいストレージ ライフサイクル ポリシー)] ダイアログ ボックスでポリシーを追加する](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

7. SLP 保持ポリシーの定義が完了したら、 **[Policy (ポリシー)]** でバックアップ ポリシーを定義します。手順については、「[StorSimple ボリュームを NetBackup バックアップ ジョブに割り当てる](#assigning-storsimple-volumes-to-a-netbackup-backup-job)」を参照してください。

8. **[Change Schedule (スケジュールの変更)]** ダイアログ ボックスの **[Schedules (スケジュール)]** で、 **[Full (完全)]** を右クリックし、 **[Change (変更)]** を選択します。

   ![NetBackup 管理コンソール、[Change Schedule (スケジュールの変更)] ダイアログ ボックス](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

9. **[Override policy storage selection (ポリシー ストレージの選択をオーバーライドする)]** チェック ボックスをオンにし、手順 1. ～ 6. で作成した SLP 保持ポリシーを選択します。

   ![NetBackup 管理コンソール、ポリシー ストレージの選択をオーバーライドする](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

10. **[OK]** を選択し、次に増分バックアップ スケジュールに対し同じ手順を繰り返します。

    ![NetBackup 管理コンソール、増分バックアップの [Change Schedule (スケジュールの変更)] ダイアログ ボックス](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)


| バックアップ タイプとリテンション | サイズ (TiB) | GFS 乗数\* | 合計容量 (TiB)  |
|---|---|---|---|
| 週単位 - 完全 |  1  |  4 | 4  |
| 毎日 - 増分  | 0.5  | 20 (サイクルは 1 か月の週数と同じ) | 12 (追加のクォータに対し 2) |
| 月単位 - 完全  | 1 | 12 | 12 |
| 年単位 - 完全 | 1  | 10 | 10 |
| GFS 要件  |     |     | 38 |
| 追加のクォータ  | 4  |    | GFS 要件合計 42 |

\* GFS 乗数は、バックアップ ポリシー要件を遵守するために保護および保持する必要があるコピー数を指します。

## <a name="storsimple-cloud-snapshots"></a>StorSimple クラウド スナップショット

StorSimple クラウド スナップショットは、StorSimple デバイスに存在するデータを保護するものです。 クラウド スナップショットの作成は、ローカルのバックアップ テープをオフサイトの施設に配送することに相当します。 Azure の geo 冗長ストレージを使用する場合、クラウド スナップショットの作成は、複数のサイトにバックアップ テープを配送することに相当します。 障害発生後にデバイスの復旧が必要な場合、別の StorSimple デバイスをオンラインにしてフェールオーバーを実行することが考えられます。 フェールオーバー後は、最近のクラウド スナップショットからデータに (クラウドのスピードで) アクセスできるようになります。

次のセクションでは、バックアップの後処理中に StorSimple クラウド スナップショットを開始および削除するショート スクリプトの作成方法について説明します。

> [!NOTE]
> 手動またはプログラムによって作成されたスナップショットは、StorSimple スナップショットの有効期間ポリシーに準拠しません。 これらのスナップショットは、手動またはプログラムによって削除する必要があります。

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>スクリプトを使用してクラウド スナップショットを開始および削除する

> [!NOTE]
> StorSimple スナップショットを削除する前に、コンプライアンスとデータ リテンション期間の影響を慎重に評価します。 バックアップ後スクリプトを実行する方法の詳細については、[NetBackup のドキュメント](http://www.veritas.com/docs/000094423)を参照してください。

### <a name="backup-lifecycle"></a>バックアップ ライフサイクル

![バックアップ ライフサイクルの図](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

### <a name="requirements"></a>必要条件

-   スクリプトを実行するサーバーは、Azure クラウドのリソースにアクセスできる必要があります。
-   ユーザー アカウントは、必要な権限を持っていなければなりません。
-   関連する StorSimple ボリュームを使用する StorSimple バックアップ ポリシーを設定し、無効にする必要があります。
-   StorSimple リソース名、登録キー、デバイス名、バックアップ ポリシー ID が必要になります。

### <a name="to-start-or-delete-a-cloud-snapshot"></a>クラウド スナップショットを開始または削除する方法

1. [Azure PowerShell をインストールします](/powershell/azure/overview)。
2. [Manage-CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) PowerShell スクリプトをダウンロードして設定します。
3. スクリプトを実行するサーバーで PowerShell を管理者として実行します。 スクリプトによって行われる変更を確認するには、`-WhatIf $true` を指定してスクリプトを実行してください。 検証が完了したら、`-WhatIf $false` を渡します。 次のコマンドを実行します。
   ```powershell
   .\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
   ```
4. スクリプトを NetBackup のバックアップ ジョブに追加します。 そのためには、NetBackup ジョブ オプションの前処理および後処理コマンドを編集します。

> [!NOTE]
> StorSimple クラウド スナップショット バックアップ ポリシーは、日単位のバックアップ ジョブの最後に後処理スクリプトとして実行することをお勧めします。 RPO および RTO に適合するようバックアップ アプリケーション環境をバックアップ、復元する方法の詳細については、担当バックアップ アーキテクトにご相談ください。

## <a name="storsimple-as-a-restore-source"></a>復元元としての StorSimple

StorSimple デバイスからの復元は、他のブロック ストレージ デバイスからの復元と同じように行われます。 クラウドに階層化されているデータの復元は、クラウドのスピードで実行されます。 ローカル データの復元は、デバイスのローカル ディスクのスピードで実行されます。 復元の実行方法については、[NetBackup のドキュメント](http://www.veritas.com/docs/000094423)を参照してください。 NetBackup の復元のベスト プラクティスに従うことをお勧めします。

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple のフェールオーバーと障害復旧

> [!NOTE]
> バックアップ ターゲットのシナリオについて、StorSimple Cloud Appliance は復元先としてサポートされていません。

障害は、さまざまな要素によって引き起こされます。 次の表は、よくあるディザスター リカバリーのシナリオをまとめたものです。

| シナリオ | 影響 | 復旧方法 | メモ |
|---|---|---|---|
| StorSimple デバイスの不具合 | バックアップと復元のオペレーションが中断されます。 | 不具合のあるデバイスを交換し、[StorSimple フェールオーバーと障害復旧](storsimple-device-failover-disaster-recovery.md)を実行します。 | デバイスの復旧後に復元を実行する必要がある場合、完全なデータのワーキング セットがクラウドから新しいデバイスに送られます。 すべてのオペレーションはクラウドのスピードで実行されます。 インデックスとカタログの再スキャン プロセスでは、すべてのバックアップ セットがスキャンされ、クラウドの階層からローカル デバイスの階層に戻される場合があり、時間のかかる可能性があります。 |
| NetBackup サーバーの障害 | バックアップと復元のオペレーションが中断されます。 | バックアップ サーバーを再構築し、データベースの復元を実行します。 | ディザスター リカバリー サイトの NetBackup サーバーを再構築または復元する必要があります。 データベースを最新の時点に復元します。 復元された NetBackup データベースが最新のバックアップ ジョブと同期しない場合、インデックスとカタログの作成が必要です。 このインデックスとカタログの再スキャン プロセスでは、すべてのバックアップ セットがスキャンされ、クラウドの階層からローカル デバイスの階層に戻される場合があります。 これにはさらに時間がかかります。 |
| バックアップ サーバーと StorSimple 両方の損失を招くサイトの不具合 | バックアップと復元のオペレーションが中断されます。 | まず StorSimple を復元し、次に NetBackup を復元します。 | まず StorSimple を復元し、次に NetBackup を復元します。 デバイスの復旧後に復元を実行する必要がある場合、完全なデータのワーキング セットがクラウドから新しいデバイスに送られます。 すべてのオペレーションはクラウドのスピードで実行されます。 |

## <a name="references"></a>参照

この記事では次のドキュメントを参考にしています。

- [StorSimple のマルチパス I/O のセットアップ](storsimple-configure-mpio-windows-server.md)
- [ストレージ シナリオ:シン プロビジョニング](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [GPT ドライブを使用する (Using GPT drives)](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [共有フォルダーのシャドウ コピーのセットアップ](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>次の手順

- [バックアップ セットからの復元方法](storsimple-restore-from-backup-set-u2.md)について確認します。
- [デバイスのフェールオーバーと障害復旧](storsimple-device-failover-disaster-recovery.md)の実行方法について確認します。
