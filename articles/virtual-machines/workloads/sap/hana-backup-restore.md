---
title: SAP HANA on Azure での HANA のバックアップと復元 (ラージ インスタンス) | Microsoft Docs
description: SAP HANA on Azure で HANA のバックアップと復元を実行する方法 (ラージ インスタンス)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 705dfb0b62327d9675fe9bf3e5860b1396e2fab9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101308"
---
# <a name="backup-and-restore"></a>バックアップと復元

>[!IMPORTANT]
>この記事は、SAP HANA の管理ドキュメントまたは SAP Note の代わりになるものではありません。 SAP HANA の管理と操作 (特にバックアップ、復元、高可用性、ディザスター リカバリー) について、読者が十分な理解と専門知識を有していることを前提としています。 この記事では、SAP HANA Studio のスクリーンショットを示します。 SAP 管理ツールの画面およびツール自体の内容、構造、特徴は、SAP HANA のリリースごとに変わる可能性があります。

お使いの環境、および使用する HANA のバージョンとリリースに応じた手順とプロセスを理解することが重要です。 この記事で説明されている一部のプロセスは、一般的な理解を深めるために簡略化されています。 最終的な操作ハンドブックの詳細な手順として使用するためのものではありません。 使用する構成に対応した操作ハンドブックを作成する場合は、実際のプロセスをテストおよび練習し、特定の構成に関連するプロセスを文書化してください。 

データベースを運用するうえで最も重要な点の 1 つは、危機的な事象からデータベースを保護することです。 こうした事象の原因には、自然災害から単純なユーザー エラーまで、あらゆることが考えられます。

データベースをバックアップし、任意の時点 (たとえば、だれかが重要なデータを削除してしまう前など) に復元できるようにすることで、中断が発生する前の状態にできる限り近い状態への復元が可能になります。

復元機能を実現するには、2 種類のバックアップを実行する必要があります。

- データベースのバックアップ:完全、増分、または差分バックアップ
- トランザクション ログのバックアップ

アプリケーション レベルで実行されるデータベースの完全バックアップに加え、ストレージ スナップショットによるバックアップを実行することもできます。 ストレージ スナップショットはトランザクション ログ バックアップの代わりになるものではありません。 データベースを特定の時点に復元したり、既にコミットされたトランザクションのログを削除したりするために、トランザクション ログ バックアップが重要であることに変わりはありません。 ストレージ スナップショットでは、データベースのロールフォワード イメージが速やかに提供されるので、復旧を迅速化できます。 

SAP HANA on Azure (L インスタンス) には、次の 2 つのバックアップと復元のオプションがあります。

- **自分で実行する (DIY)。** 十分なディスク領域があることを確認した後、次のいずれかのディスク バックアップ方法を使って、データベースとログの完全バックアップを実行します。 HANA L インスタンス ユニットに接続されているボリュームに直接バックアップするか、Azure 仮想マシン (VM) にセットアップされている NFS 共有にバックアップすることができます。 後者の場合、Azure で Linux VM をセットアップし、Azure Storage を VM に接続して、その VM で構成済みの NFS サーバーを使用してストレージを共有します。 HANA Large Instances ユニットに直接接続されているボリュームに対してバックアップを実行する場合は、Azure ストレージ アカウントにバックアップをコピーします。 Azure Storage に基づく NFS 共有をエクスポートする Azure VM をセットアップした後で、これを行います。 Azure Backup コンテナーまたは Azure コールド ストレージを使用することもできます。 

   もう 1 つの方法は、バックアップが Azure ストレージ アカウントにコピーされた後で、サード パーティのデータ保護ツールを使ってバックアップを保存することです。 DIY バックアップ オプションは、コンプライアンスと監査のためにデータを長期間保存しなければならない場合にも必要になることがあります。 どの場合も、バックアップは VM と Azure Storage を介して提供される NFS 共有にコピーされます。

- **インフラストラクチャのバックアップおよび復元機能。** SAP HANA on Azure (Large Instances) の基になるインフラストラクチャによって提供されるバックアップと復元の機能を使うこともできます。 このオプションは、バックアップと高速復元のニーズに対応します。 このセクションの残りの部分では、HANA L インスタンスで提供されるバックアップと復元の機能について説明します。 ここでは、HANA Large Instances によって提供されるディザスター リカバリー機能に対するバックアップと復元の関係のについても説明します。

> [!NOTE]
>   HANA Large Instances の基になるインフラストラクチャで使われているスナップショット テクノロジは、SAP HANA スナップショットに依存しています。 現在のところ、SAP HANA スナップショットは、SAP HANA マルチテナント データベース コンテナーの複数のテナントでは機能しません。 デプロイされているテナントが 1 つだけの場合は、SAP HANA スナップショットが機能するので、この方法を使うことができます。

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (Large Instances) のストレージ スナップショットを使用する

SAP HANA on Azure (L インスタンス) の基になっているストレージ インフラストラクチャでは、ボリュームのストレージ スナップショットがサポートされています。 ボリュームのバックアップと復元の両方がサポートされていますが、次の点を考慮する必要があります。

- データベースの完全バックアップの代わりに、ストレージ ボリューム スナップショットが頻繁に作成されます。
- /hana/data および /hana/shared (/usr/sap を含む) の各ボリュームに対してスナップショットがトリガーされると、ストレージ スナップショットの実行前に、スナップショット テクノロジーによって SAP HANA スナップショットが開始されます。 この SAP HANA スナップショットは、ストレージ スナップショットの復旧後に最終的なログ復元を設定するポイントです。 HANA スナップショットが成功するには、アクティブな HANA インスタンスが必要です。 HSR のシナリオの場合、HANA スナップショットを実行できない現在のセカンダリ ノードでは、ストレージ スナップショットはサポートされていません。
- ストレージ スナップショットが正常に実行された後、SAP HANA スナップショットは削除されます。
- トランザクション ログ バックアップが頻繁に作成され、/hana/logbackups ボリュームまたは Azure に保存されます。 トランザクション ログ バックアップを格納する /hana/logbackups ボリュームをトリガーしてスナップショットを個別に実行できます。 その場合、HANA スナップショットを実行する必要はありません。
- 運用環境が停止した場合など、特定の時点のデータベースを復元する必要がある場合は、Microsoft Azure サポートまたは SAP HANA on Azure に対して、特定のストレージ スナップショットを復元するよう要求します。 たとえば、サンドボックス システムを元の状態へと計画的に復元する方法があります。
- ストレージ スナップショットに含まれている SAP HANA スナップショットは、ストレージ スナップショットの作成後に実行され、保存されたトランザクション ログ バックアップを適用するためのオフセット ポイントです。
- これらのトランザクション ログ バックアップは、データベースを特定の時点に復元するために作成されます。

次の 3 つのクラスのボリュームをターゲットとするストレージ スナップショットを実行できます。

- /hana/data と /hana/shared (/usr/sap を含む) を対象とする結合スナップショット。 このスナップショットでは、ストレージ スナップショットの準備として SAP HANA スナップショットを作成する必要があります。 SAP HANA スナップショットにより、ストレージの観点からデータベースが整合性のある状態であることが保証されます。 復元プロセスの場合は、それがセットアップするポイントです。
- /hana/logbackups に対する個別スナップショット。
- オペレーティング システム パーティション。

スナップショットの最新のスクリプトとドキュメントを入手するには、[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1) をご覧ください。 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1) からスナップショットのスクリプト パッケージをダウンロードすると、3 つのファイルが取得されます。 ファイルの 1 つは、提供されている機能の PDF に記載されています。 ツール セットをダウンロードした後、「Get the snapshot tools (スナップショット ツールを入手する)」の手順に従います。

## <a name="storage-snapshot-considerations"></a>ストレージ スナップショットに関する考慮事項

>[!NOTE]
>ストレージ スナップショットは、HANA Large Instance ユニットに割り当てられているストレージ スペースを使います。 ストレージ スナップショットのスケジュール設定と、保持するストレージ スナップショットの数については、以下の点を考慮します。 

SAP HANA on Azure (L インスタンス) のストレージ スナップショットに固有のメカニズムは、次のとおりです。

- 作成時点での特定のストレージ スナップショットでは、ストレージをほとんど使われません。
- データの内容が変化し、ストレージ ボリュームで SAP HANA データ ファイルの内容が変化したら、元のブロックの内容とデータの変更がスナップショットに保存される必要があります。
- そのため、ストレージ スナップショットのサイズは増加します。 スナップショットの存在期間が長くなるほど、ストレージ スナップショットが大きくなります。
- ストレージ スナップショットの有効期間に SAP HANA データベース ボリュームに対する変更が多くなるほど、ストレージ スナップショットの領域の消費量が大きくなります。

SAP HANA on Azure (L インスタンス) には、SAP HANA のデータとログ ボリューム用の固定ボリューム サイズがあります。 これらのボリュームのスナップショットを実行すると、ボリューム領域が消費されます。 以下を実行する必要があります。

- ストレージ スナップショットのスケジュールを決めす。
- ストレージ ボリュームの領域使用量を監視します。 
- 格納するスナップショットの数を管理します。 

大量のデータをインポートする場合や、HANA データベースに対する他の大きな変更を実行する場合、ストレージ スナップショットを無効にすることができます。 


以降のセクションでは、これらのスナップショットの実行に関する情報と、一般的な推奨事項について説明します。

- ハードウェアではボリュームごとに 255 個のスナップショットを保持できますが、この数よりも十分に少なくしておくことをお勧めします。 推奨値は 250 以下です。
- ストレージ スナップショットを実行する前に、空き領域を監視して追跡します。
- 空き領域に基づいて、ストレージ スナップショットの数を減らします。 場合によっては、保持するスナップショットの数を減らしたり、ボリュームを拡大したりする必要があります。 追加のストレージは 1 TB 単位で注文できます。
- SAP プラットフォーム移行ツール (R3load) を使用した SAP HANA へのデータの移動や、バックアップからの SAP HANA データベースの復元などの操作の実行中は、/hana/data ボリュームでストレージ スナップショットを無効にします。 
- SAP HANA テーブルの大規模な再編成中は、できるだけストレージ スナップショットを避けます。
- ストレージ スナップショットは、SAP HANA on Azure (L インスタンス) のディザスター リカバリー機能を利用するための前提条件です。

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>セルフサービスのストレージ スナップショットを使用するための前提条件

スナップショット スクリプトが正常に実行されるようにするには、HANA L インスタンス サーバーの Linux オペレーティング システムに Perl がインストールされていることを確認します。 Perl は、HANA Large Instance ユニットにあらかじめインストールされています。 Perl のバージョンを確認するには、次のコマンドを使います。

`perl -v`

![このコマンドを実行すると公開キーがコピーされる](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>ストレージ スナップショットのセットアップ

HANA Large Instances でストレージ スナップショットを設定するには、次の手順に従います。
1. HANA L インスタンス サーバーの Linux オペレーティング システムに Perl がインストールされていることを確認します。
1. /etc/ssh/ssh\_config を変更して _MACs hmac-sha1_ の行を追加します。
1. 実行されている各 SAP HANA インスタンスのマスター ノードで、SAP HANA バックアップ ユーザー アカウントを作成します (該当する場合)。
1. すべての SAP HANA L インスタンス サーバーに、SAP HANA HDB クライアントをインストールします。
1. スナップショットの作成を制御する、基になるストレージ インフラストラクチャにアクセスするために、各リージョンの最初の SAP HANA (L インスタンス) サーバーで公開キーを作成します。
1. [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1) から SAP HANA インストールの **hdbsql** の場所にスクリプトと構成ファイルをコピーします。
1. 適切な顧客仕様での必要に応じて、*HANABackupDetails.txt* ファイルを変更します。

[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1) から最新のスナップショット スクリプトとドキュメントを入手してください。 前記の手順については、「[Microsoft snapshot tools for SAP HANA on Azure (SAP HANA on Azure 用の Microsoft スナップショット ツール)](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf)」をご覧ください。

### <a name="consideration-for-mcod-scenarios"></a>MCOD のシナリオに関する考慮事項
1 つの HANA Large Instances ユニット上の複数の SAP HANA インスタンスで [MCOD のシナリオ](https://launchpad.support.sap.com/#/notes/1681092)を実行している場合は、個々の SAP HANA インスタンスごとに個別のストレージ ボリュームがプロビジョニングされています。 MDC と他の考慮事項について詳しくは、「[Microsoft snapshot tools for SAP HANA on Azure (SAP HANA on Azure 用の Microsoft スナップショット ツール)](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf)」の「Important things to remember (重要な注意点)」をご覧ください。
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>手順 1:SAP HANA HDB クライアントをインストールする

SAP HANA on Azure (Large Instances) にインストールされている Linux オペレーティング システムには、バックアップとディザスター リカバリーの目的で SAP HANA ストレージ スナップショットを実行するために必要なフォルダーとスクリプトが含まれています。 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1) に新しいリリースがあるかどうかを確認してください。 スクリプトの最新のリリース バージョンは 4.1 です。 異なるスクリプトでは、同じメジャー リリースでもマイナー リリースが異なる可能性があります。

お客様自身で、SAP HANA のインストール時に、SAP HANA HDB クライアントを HANA Large Instances ユニットにインストールする必要があります。

### <a name="step-2-change-the-etcsshssh_config"></a>手順 2:/etc/ssh/ssh\_config を変更する

この手順については、「[Microsoft snapshot tools for SAP HANA on Azure (SAP HANA on Azure 用の Microsoft スナップショット ツール)](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf)」の「Enable communication with storage (ストレージとの通信を有効にする)」をご覧ください。


### <a name="step-3-create-a-public-key"></a>手順 3:公開キーを作成する

HANA Large Instances テナントのストレージ スナップショット インターフェイスにアクセスできるようにするには、公開キーを使用したサインイン手順を確立します。 

テナントの最初の SAP HANA on Azure (Large Instances) サーバーで、ストレージ インフラストラクチャにアクセスするための公開キーを作成します。 公開キーを使用すると、ストレージ スナップショット インターフェイスへのサインインにパスワードが不要になります。 また、公開キーと共にパスワードの資格情報を保持する必要もありません。 

公開キーを生成するには、「[Microsoft snapshot tools for SAP HANA on Azure (SAP HANA on Azure 用の Microsoft スナップショット ツール)](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf)」の「Enable communication with storage (ストレージとの通信を有効にする)」をご覧ください。


### <a name="step-4-create-an-sap-hana-user-account"></a>手順 4:SAP HANA ユーザー アカウントを作成する

SAP HANA スナップショットの作成を開始するには、ストレージ スナップショット スクリプトで使用できるユーザー アカウントを SAP HANA に作成します。 そのために、SAP HANA Studio 内で SAP HANA ユーザー アカウントを作成します。 MDC 用の SID データベースでは "*なく*"、SYSTEMDB に、ユーザーを作成する必要があります。 単一のコンテナー環境では、ユーザーはテナント データベース内に設定されます。 このアカウントには、**バックアップ管理**と**カタログ読み取り**という特権が必要です。 

ユーザー アカウントを設定して使用するには、[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1) の「Enable communication with SAP HANA (SAP HANA との通信を有効にする)」をご覧ください。


### <a name="step-5-authorize-the-sap-hana-user-account"></a>手順 5:SAP HANA ユーザー アカウントを承認する

この手順では、作成した SAP HANA ユーザー アカウントを承認します。これにより、スクリプトの実行時にパスワードを送信する必要がなくなります。 SAP HANA コマンド `hdbuserstore` により、SAP HANA ユーザー キーを作成できるようになります。 キーは、1 つまたは複数の SAP HANA ノードに格納されます。 ユーザー キーを使用すると、ユーザーはスクリプト プロセス内からパスワードを管理しなくても SAP HANA にアクセスできます。 スクリプト プロセスについてはこの記事で後述します。

>[!IMPORTANT]
>これらの構成コマンドを、スナップショット コマンドが実行されるのと同じユーザー コンテキストで実行します。 そうしないと、スナップショット コマンドは正しく機能しません。


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>手順 6:スナップショット スクリプトを取得し、スナップショットを構成して、構成と接続をテストする

[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1) から最新バージョンのスクリプトをダウンロードします。 スクリプトのインストール方法は、スクリプトのリリース 4.1 で変更されました。 詳しくは、「[Microsoft snapshot tools for SAP HANA on Azure (SAP HANA on Azure 用の Microsoft スナップショット ツール)](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf)」の「Enable communication with SAP HANA (SAP HANA との通信を有効にする)」をご覧ください。

コマンドの正確なシーケンスについては、「[Microsoft snapshot tools for SAP HANA on Azure (SAP HANA on Azure 用の Microsoft スナップショット ツール)](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf)」の「Easy installation of snapshot tools (default) (スナップショット ツールの簡単なインストール (既定))」をご覧ください。 既定のインストールを使うことをお勧めします。 

バージョン 3.x から 4.1 へのアップグレードについては、「[SAP HANA on Azure 用の Microsoft スナップショット ツール](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf)」の「既存のインストールをアップグレードする」をご覧ください。 4\.1 ツール セットのアンインストールについては、「[SAP HANA on Azure 用の Microsoft スナップショット ツール](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf)」の「スナップショット ツールのアンインストール」をご覧ください。

「[Microsoft snapshot tools for SAP HANA on Azure (SAP HANA on Azure 用の Microsoft スナップショット ツール)](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf)」の「Complete setup of snapshot tools (スナップショット ツールのセットアップを完了する)」で説明されている手順を実行することを忘れないでください。

インストールされるさまざまなスクリプトとファイルの目的については、「What are these snapshot tools? (これらのスナップショット ツールは何か?)」で説明されています (「[Microsoft snapshot tools for SAP HANA on Azure (SAP HANA on Azure 用の Microsoft スナップショット ツール)](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf)」)。

スナップショット ツールを構成する前に、HANA のバックアップの場所と設定を正しく構成したことも確認します。 詳しくは、「[Microsoft snapshot tools for SAP HANA on Azure (SAP HANA on Azure 用の Microsoft スナップショット ツール)](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf)」の「SAP HANA Configuration (SAP HANA の構成)」をご覧ください。

スナップショット ツール セットの構成については、「[Microsoft snapshot tools for SAP HANA on Azure (SAP HANA on Azure 用の Microsoft スナップショット ツール)](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf)」が「Config file - HANABackupCustomerDetails.txt (構成ファイル - HANABackupCustomerDetails.txt)」で説明されています。

#### <a name="test-connectivity-with-sap-hana"></a>SAP HANA との接続をテストする

*HANABackupCustomerDetails.txt* ファイルにすべての構成データを入力したら、HANA インスタンスのデータに関して構成が正しいかどうかを確認します。 使用するスクリプトは `testHANAConnection` です。このスクリプトは、SAP HANA のスケールアップ構成またはスケールアウト構成に依存しません。

詳しくは、「[Microsoft snapshot tools for SAP HANA on Azure (SAP HANA on Azure 用の Microsoft スナップショット ツール)](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf)」の「Check connectivity with SAP HANA - testHANAConnection (SAP HANA との接続を確認する - testHANAConnection)」をご覧ください。

#### <a name="test-storage-connectivity"></a>ストレージの接続をテストする

次のテスト ステップでは、*HANABackupCustomerDetails.txt* 構成ファイルに設定したデータに基づいて、ストレージへの接続を確認します。 次に、テスト スナップショットを実行します。 `azure_hana_backup`コマンドを実行する前に、このテストを実行する必要があります。 このテストのコマンドのシーケンスについては、「[Microsoft snapshot tools for SAP HANA on Azure (SAP HANA on Azure 用の Microsoft スナップショット ツール)](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf)」の「Check connectivity with storage - testStorageSnapshotConnection (ストレージとの接続を確認する - testStorageSnapshotConnection)」をご覧ください。

ストレージの仮想マシン インターフェイスに正常にサインインすると、スクリプトはフェーズ 2 に進み、テスト スナップショットが作成されます。 SAP HANA の 3 ノード スケールアウト構成の場合、次のように出力されます。

スクリプトによってテスト スナップショットが正常に実行さる場合は、実際のストレージ スナップショットのスケジュールを設定できます。 成功しない場合は、問題を調べてから次に進みます。 実際の最初のスナップショットが完了するまで、テスト スナップショットは保持されます。


### <a name="step-7-perform-snapshots"></a>手順 7:スナップショットを実行する

準備手順が完了したら、実際のストレージ スナップショットの構成とスケジュールを開始できます。 スケジュールされたスクリプトは、SAP HANA のスケールアップ構成とスケールアウト構成で動作します。 バックアップ スクリプトを定期的に実行するには、cron ユーティリティを使ってスクリプトをスケジュールします。 

コマンドの正確な構文と機能については、「[Microsoft snapshot tools for SAP HANA on Azure (SAP HANA on Azure 用の Microsoft スナップショット ツール)](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf)」の「Perform snapshot backup - azure_hana_backup (スナップショットのバックアップを実行する - azure_hana_backup)」をご覧ください。 

スクリプト `azure_hana_backup` を実行すると、次の 3 つのフェーズで、ストレージ スナップショットが作成されます。

1. SAP HANA スナップショットが実行されます。
1. ストレージ スナップショットが実行されます。
1. ストレージ スナップショットの実行前に作成された SAP HANA スナップショットが削除されます。

スクリプトを実行するには、コピー先の HDB 実行可能ファイル フォルダーから呼び出します。 

保持期間は、スクリプトの実行時にパラメーターとして送信されるスナップショットの数で管理されます。 ストレージ スナップショットが対応する期間は、実行期間と、スクリプトの実行時にパラメーターとして送信されるスナップショットの数によって決まります。 

保持されているスナップショットの数が、スクリプトの呼び出しでパラメーターとして指定された数を超えた場合は、新しいスナップショットの実行前に、同じラベルの最も古いストレージ スナップショットが削除されます。 呼び出しの最後のパラメーターとして指定した数が、保持されるスナップショットの数を制御するために使用できる数になります。 この数を利用して、スナップショットに使用されるディスク領域を間接的に制御することもできます。 


## <a name="snapshot-strategies"></a>スナップショット戦略
各種スナップショットの頻度は、HANA L インスタンスのディザスター リカバリー機能を使用するかどうかによって異なります。 この機能はストレージ スナップショットに依存しているため、場合によっては、ストレージ スナップショットの頻度と実行期間について特別な推奨事項が必要になることがあります。 

以下の考慮事項と推奨事項では、HANA L インスタンスが提供するディザスター リカバリー機能を "*使用しない*" ことを前提としています。 代わりに、バックアップを保持し、過去 30 日間の特定の時点への復旧を実現できるようにするための手段として、ストレージ スナップショットを使います。 スナップショットの数と領域の制限がある場合、次のような要件を考慮します。

- ポイントインタイム復旧の復旧時間。
- 使用される領域。
- 障害からの復旧可能性の、回復ポイントと回復時刻の目標。
- ディスクに対する HANA データベースの完全バックアップの最終的な実行。 ディスクまたは **backint** インターフェイスに対するデータベースの完全バックアップが実行されるたびに、ストレージ スナップショットの実行は失敗します。 ストレージ スナップショットに加えてデータベースの完全バックアップを実行する予定がある場合は、その実行中にストレージ スナップショットの実行が無効になるようにします。
- ボリュームごとのスナップショットの数は 250 個に制限されています。

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

HANA Large Instances のディザスター リカバリー機能を使用しない場合は、スナップショット期間の頻度が減ります。 そのような場合、12 時間または 24 時間の間隔で、/hana/data と (/usr/sap が含まれる) /hana/shared の結合スナップショットを実行します。 そのスナップショットを 1 か月間保持します。 ログ バックアップ ボリュームのスナップショットの場合も同じです。 ログ バックアップ ボリュームに対する SAP HANA トランザクション ログ バックアップは、5 から 15 分の間隔で実行されます。

スケジュールされたストレージ スナップショットは、cron を使用することで最適に実行できます。 すべてのバックアップとディザスター リカバリーのニーズに対して、同じスクリプトを使用します。 要求されるさまざまなバックアップ時間に合わせて、スクリプトの入力を変更します。 これらのスナップショットは、実行時間に応じて、cron でのスケジュールが異なります。 1 時間ごと、12 時間ごと、毎日、または毎週に設定されます。 

次に示すのは、/etc/crontab での cron スケジュールの例です。
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
前の例では、1 時間単位の結合スナップショットによって、/hana/data および (/usr/sap が含まれる) /hana/shared/SID の場所を含むボリュームがカバーされています。 この種のスナップショットは、過去 2 日以内の特定の時点への復旧を迅速化するために使用します。 また、これらのボリュームの日単位のスナップショットもあります。 そのため、時間単位のスナップショットによって 2 日間をカバーし、日単位のスナップショットによって 4 週間をカバーできます。 また、トランザクション ログ バックアップ ボリュームが毎日 1 回バックアップされます。 これらのバックアップは 4 週間保持されます。 

crontab の 3 行目でわかるように、HANA トランザクション ログのバックアップが 5 分ごとに実行されるようにスケジュールされています。 ストレージ スナップショットを実行するさまざまな cron ジョブの開始時刻はずらされています。 これにより、すべてのスナップショットが特定の時点に一斉に実行されることはありません。 

次の例では、/hana/data および /hana/shared/SID (/usr/sap を含む) の各場所を含むボリュームを対象とする結合スナップショットを時間単位で実行します。 これらのスナップショットは 2 日間保持します。 トランザクション ログ バックアップ ボリュームのスナップショットは 5 分ごとに実行され、4 時間保持されます。 前と同様に、HANA トランザクション ログ ファイルのバックアップが 5 分ごとに実行されるようにスケジュールされています。 

トランザクション ログ バックアップ ボリュームのスナップショットは、トランザクション ログ バックが開始されてから 2 分遅れて実行されます。 通常の状況では、SAP HANA トランザクション ログ バックアップはこの 2 分以内に完了します。 前と同様に、ブート LUN を含むボリュームがストレージ スナップショットによって 1 日 1 回バックアップされ、4 週間保持されます。

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

次の図は、前の例のシーケンスを示しています。 ブート LUN は除外されています。

![バックアップとスナップショットの関係](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA は、データベースに対するコミットされた変更を記録するために、/hana/log ボリュームに対して定期的な書き込みを実行します。 SAP HANA は、/hana/data ボリュームにセーブポイントを定期的に書き込みます。 crontab での指定に従って、SAP HANA トランザクション ログ バックアップが 5 分ごとに実行されます。 

また、/hana/data および /hana/shared/SID の各ボリュームに対する結合ストレージ スナップショットのトリガーにより、SAP HANA スナップショットが 1 時間ごとに実行されます。 HANA スナップショットが成功すると、結合ストレージ スナップショットが実行されます。 crontab の指示に従って、5 分ごとに (HANA トランザクション ログ バックアップの約 2 分後) /hana/logbackup ボリュームでのストレージ スナップショットが実行されます。

> 

>[!IMPORTANT]
> SAP HANA バックアップに対するストレージ スナップショットの使用は、スナップショットが SAP HANA トランザクション ログ バックアップと共に実行される場合にのみ有効です。 これらのトランザクション ログ バックアップは、ストレージ スナップショット間の期間をカバーする必要があります。 

30 日間の特定の時点への復旧をユーザーに確約している場合は、次のことが必要です。

- 極端な場合、30 日前の /hana/data および /hana/shared/SID に対する結合ストレージ スナップショットにアクセスします。 
- 結合ストレージ スナップショット間の時間をカバーする連続したトランザクション ログ バックアップを保持します。 そのため、トランザクション ログ バックアップ ボリュームの最も古いスナップショットは 30 日前のものである必要があります。 ただし、トランザクション ログ バックアップを、Azure Storage にある別の NFS 共有にコピーしている場合を除きます。 このような場合、その NFS 共有から古いトランザクション ログ バックアップを取得できます。

ストレージ スナップショットとトランザクション ログ バックアップの最終的なストレージ レプリケーションのメリットを得られるようにするには、SAP HANA がトランザクション ログ バックアップを書き込む場所を変更します。 この変更は、HANA Studio で実行できます。 

SAP HANA では完全なログ セグメントが自動的にバックアップされますが、ログ バックアップ間隔を決定論的に指定します。 特に、ディザスター リカバリー オプションを使用している場合、通常は決定論的期間でログ バックアップを実行します。 次の例では、ログ バックアップの間隔を 15 分に設定しています。

![SAP HANA Studio での SAP HANA バックアップ ログのスケジュール](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

また、バックアップの間隔は 15 分よりも短くすることができます。 間隔の短い設定は多くの場合、HANA L インスタンスのディザスター リカバリー機能と組み合わせて使われます。 トランザクション ログ バックアップを 5 分ごとに実行しているお客様もいます。

これまでデータベースをバックアップしたことがない場合は、最後にファイル ベースのデータベース バックアップを実行して、バックアップ カタログ内に存在する必要がある単一のバックアップ エントリを作成します。 これを実行しないと、SAP HANA は指定されたログ バックアップを開始できません。

![ファイル ベースのバックアップで単一のバックアップ エントリが作成されるようにする](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


ストレージ スナップショットの実行が初めて成功した後で、手順 6 で実行したテスト スナップショットを削除します。 詳しくは、「[Microsoft snapshot tools for SAP HANA on Azure (SAP HANA on Azure 用の Microsoft スナップショット ツール)](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf)」の「Remove test snapshots - removeTestStorageSnapshot (テスト スナップショットを削除する - removeTestStorageSnapshot)」をご覧ください。 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>ディスク ボリューム上のスナップショットの数とサイズを監視する

特定のストレージ ボリュームで、スナップショットの数とそれらのスナップショットによるストレージの使用量を監視できます。 `ls` コマンドでは、スナップショットのディレクトリまたはファイルが表示されません。 ストレージ スナップショットは同じボリュームに保存されているので、Linux OS コマンドの `du` を使用すると、それらのスナップショットの詳細が表示されます。 コマンドでは次のオプションを使用します。

- `du –sh .snapshot`:このオプションでは、スナップショット ディレクトリ内にあるすべてのスナップショットの合計が提供されます。
- `du –sh --max-depth=1`:このオプションでは、 **.snapshot** フォルダーに保存されているすべてのスナップショットと、各スナップショットのサイズが一覧表示されます。
- `du –hc`:このオプションでは、すべてのスナップショットによって使用される合計サイズが提供されます。

作成および格納したスナップショットがボリューム上のストレージを使用し尽くしていないことを確認するには、上記のコマンドを使用します。

>[!NOTE]
>ブート LUN のスナップショットは、上記のコマンドでは表示されません。

### <a name="get-details-of-snapshots"></a>スナップショットの詳細を取得する
スナップショットの詳細を取得するには、スクリプト `azure_hana_snapshot_details` を使用します。 ディザスター リカバリーの場所にアクティブなサーバーがある場合は、このスクリプトをいずれかの場所で実行できます。 このスクリプトは、スナップショットを含むボリュームごとに分類して次の出力を提供します。 
   * ボリュームの合計スナップショットのサイズ
   * そのボリュームの各スナップショットに関する次の詳細情報: 
      - スナップショット名 
      - 作成時刻 
      - スナップショットのサイズ
      - スナップショットの頻度
      - 該当する場合、そのスナップショットに関連付けられている HANA バックアップ ID

コマンドの構文と出力については、「[Microsoft snapshot tools for SAP HANA on Azure (SAP HANA on Azure 用の Microsoft スナップショット ツール)](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf)」の「List snapshots - azure_hana_snapshot_details (スナップショットを一覧表示する - azure_hana_snapshot_details)」をご覧ください。 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>サーバー上のスナップショットの数を削減する

前に説明したように、特定のラベルで格納されるスナップショットの数を減らすことができます。 スナップショットを開始するコマンドの最後の 2 つのパラメーターは、ラベルと、保持するスナップショットの数です。

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

前の例で、スナップショット ラベルは **dailyhana** です。 このラベルで保持するスナップショットの数は **28** です。 ディスク領域の使用量に応じて、格納されるスナップショットの数を減らすことができます。 スナップショットの数をたとえば 15 個に減らす簡単な方法は、最後のパラメーターを **15** に設定してスクリプトを実行することです。

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

この設定でスクリプトを実行すると、スナップショットの数は、新しいストレージ スナップショットを含めて、15 個になります。 15 個の最新のスナップショットが保持され、15 個の古いスナップショットが削除されます。

 >[!NOTE]
 > このスクリプトでは、1 時間以上経過したスナップショットが存在する場合にのみ、スナップショットの数が減らされます。 このスクリプトでは、作成から 1 時間経過していないスナップショットは削除されません。 これらの制限は、提供されるオプションのディザスター リカバリー機能に関連しています。

バックアップ プレフィックス (構文例では **hanadaily**) の付いた一連のスナップショットを保持する必要がなくなった場合は、保持数を **0** に設定してスクリプトを実行します。 これにより、そのラベルと一致するすべてのスナップショットが削除されます。 すべてのスナップショットを削除すると、HANA Large Instances のディザスター リカバリー機能に影響する可能性があります。

特定のスナップショットを削除するもう 1 つの方法は、スクリプト `azure_hana_snapshot_delete` を使用することです。 このスクリプトは、HANA Studio で確認できる HANA バックアップ ID またはストレージ スナップショット名自体を使用して、1 つのスナップショットまたは一連のスナップショットを削除することを目的としています。 現在、バックアップ ID は、スナップショットの種類が **hana** である作成済みのスナップショットにのみ関連付けられています。 種類が **logs** および **boot** のスナップショット バックアップでは SAP HANA スナップショットは実行されないため、これらのスナップショットのバックアップ ID はありません。 スナップショット名を入力すると、入力したスナップショット名に一致するすべてのスナップショットがさまざまなボリューム上で検索されます。 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

スクリプトについて詳しくは、「[Microsoft snapshot tools for SAP HANA on Azure (SAP HANA on Azure 用の Microsoft スナップショット ツール)](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.1/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.1.pdf)」の「Delete a snapshot - azure_hana_snapshot_delete (スナップショットを削除する - azure_hana_snapshot_delete)」をご覧ください。

このスクリプトは、**root** ユーザーとして実行します。

>[!IMPORTANT]
>削除しようとしているスナップショット上にのみ存在するデータがある場合、スナップショットが削除されると、そのデータが永久に失われることになります。


## <a name="file-level-restore-from-a-storage-snapshot"></a>ストレージ スナップショットからのファイル レベルの復元

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
スナップショットの種類が **hana** または **logs** の場合、ボリューム上の **.snapshot** ディレクトリにあるスナップショットに直接アクセスできます。 スナップショットごとにサブディレクトリがあります。 スナップショットの作成時点での状態で、該当のサブディレクトリから実際のディレクトリ構造に、各ファイルをコピーします。 

現在のバージョンのスクリプトでは、セルフサービスのスナップショットの復元用に提供されている復元スクリプトは "*ありません*"。 スナップショットの復元は、フェールオーバー中のディザスター リカバリー サイトでのセルフサービス ディザスター リカバリー スクリプトの一部として実行できます。 既存の使用可能なスナップショットから目的のスナップショットを復元するには、サービス要求を開いて、Microsoft の運用チームに問い合わせる必要があります。

>[!NOTE]
>単一ファイルの復元は、HANA Large Instances ユニットの種類とは関係のないブート LUN のスナップショットに対しては機能しません。 **.snapshot** ディレクトリは、ブートLUN では公開されません。 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>最新の HANA スナップショットへの復旧

運用環境停止のシナリオが発生した場合は、Microsoft Azure サポートでの顧客インシデントとして、ストレージ スナップショットからの復旧プロセスを開始できます。 運用システムのデータが削除されており、データを取得する唯一の方法が運用データベースの復元である場合は、緊急度の高い問題となります。

一方、特定の時点への復旧は緊急度が低く、数日前から計画されている場合があります。 この復旧は、優先度の高いフラグを設定するのではなく、SAP HANA on Azure と共に計画できます。 たとえば、新しい拡張機能パッケージを適用して、SAP ソフトウェアのアップグレードを計画する場合があります。 次に、拡張機能パッケージのアップグレード前の状態を表すスナップショットに戻す必要があります。

要求を送信する前に準備する必要があります。 そうすることで、SAP HANA on Azure チームが要求を処理し、復元されたボリュームを提供できます。 その後、スナップショットに基づいて HANA データベースを復元します。

新しいツール セットで復元されたスナップショットを取得する可能性については、「[Manual recovery guide for SAP HANA on Azure from a storage snapshot (ストレージ スナップショットからの SAP HANA on Azure の手動復旧ガイド)](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf)」の「How to restore a snapshot (スナップショットを復元する方法)」をご覧ください。

要求の準備をするには、次の手順のようにします。

1. 復元するスナップショットを決めます。 他に指示がない限り、hana/data ボリュームのみが復元されます。 

1. HANA インスタンスをシャットダウンします。

   ![HANA インスタンスをシャットダウンする](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. 各 HANA データベース ノードで、データ ボリュームのマウントを解除します。 データ ボリュームがオペレーティング システムに引き続きマウントされていると、スナップショットの復元は失敗します。

   ![各 HANA データベース ノードでデータ ボリュームのマウントを解除する](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Azure サポート要求を開き、特定のスナップショットの復元に関する指示を記入します。

   - 復元中:SAP HANA on Azure サービスでは、適切なストレージ スナップショットを確実に復元するための調整、検証、確認のために、電話会議への出席を依頼する場合があります。 

   - 復元後:SAP HANA on Azure サービスから、ストレージ スナップショットの復元が完了した時点で通知が届きます。

1. 復元処理が完了したら、すべてのデータ ボリュームを再マウントします。

   ![すべてのデータ ボリュームを再マウントする](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



ストレージ スナップショットから復旧されたたとえば SAP HANA データ ファイルを取得する別の可能性については、「[Manual recovery guide for SAP HANA on Azure from a storage snapshot (ストレージ スナップショットからの SAP HANA on Azure の手動復旧ガイド)](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf)」のステップ 7 をご覧ください。

スナップショット バックアップからの復元については、「[Manual recovery guide for SAP HANA on Azure from a storage snapshot (ストレージ スナップショットからの SAP HANA on Azure の手動復旧ガイド)](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf)」をご覧ください。 

>[!Note]
>スナップショットが Microsoft の操作によって復元された場合は、ステップ 7 を行う必要はありません。


### <a name="recover-to-another-point-in-time"></a>異なる特定の時点への復旧
特定の時点への復元については、「[Manual recovery guide for SAP HANA on Azure from a storage snapshot (ストレージ スナップショットからの SAP HANA on Azure の手動復旧ガイド)](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf)の「Recover the database to the following point in time (次の時点にデータベースを復旧する)」をご覧ください。 


## <a name="next-steps"></a>次の手順
- [ディザスター リカバリーの原則と準備](hana-concept-preparation.md)に関するページを参照してください。
