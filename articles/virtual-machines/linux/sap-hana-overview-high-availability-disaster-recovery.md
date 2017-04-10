---
title: "SAP HANA on Azure (L インスタンス) の高可用性と障害復旧 | Microsoft Docs"
description: "高可用性を確立し、SAP HANA on Azure (L インスタンス) の障害復旧を計画します。"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fa842efd99718be7fa9eaf8aac8030c32cbceeec
ms.openlocfilehash: 661733edbabd61b42bfb44b4ed107b1e757c2e28
ms.lasthandoff: 03/01/2017


---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Azure での SAP HANA (L インスタンス) の高可用性と障害復旧 

高可用性と障害復旧は、ミッション クリティカルな SAP HANA on Azure (L インスタンス) サーバーを実行する場合の重要な要素です。 正しい高可用性/障害復旧戦略を適切に策定、実装するには、SAP、システム インテグレーター、Microsoft と協力することが重要です。 また、環境に固有の目標復旧時点と目標復旧時間を検討することも重要です。

## <a name="high-availability"></a>高可用性

Microsoft では、次のような "すぐに利用できる" SAP HANA 高可用性手法をサポートしています。

- **ストレージ レプリケーション:** すべてのデータを (同じデータセンター内または別のデータセンター内の) 別の場所にレプリケートする、ストレージ システムの機能です。 SAP HANA は、この手法と関係なく動作します。
- **HANA システム レプリケーション:** 別の SAP HANA システムへの SAP HANA の全データのレプリケーションです。 目標復旧時間は、一定の間隔で実行されるデータ レプリケーションによって最小限に抑えられます。 SAP HANA では、非同期、メモリ内同期、同期 (同じデータセンター内または 100 km 未満の距離のデータセンター内にある SAP HANA システムのみに推奨) の各モードがサポートされています。 HANA L インスタンス スタンプの現在の設計では、HANA システム レプリケーションは高可用性を実現するためにのみ使用できます。
- **ホストの自動フェールオーバー:** システム レプリケーションの代替として使用されるローカルな障害復旧ソリューションです。 マスター ノードが使用できなくなると、1 つまたは複数のスタンバイ SAP HANA ノードがスケールアウト モードで構成され、SAP HANA が自動的に別のノードにフェールオーバーされます。

SAP HANA の高可用性の詳細については、以下の SAP 情報を参照してください。

- [SAP HANA の高可用性のホワイトペーパー](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA 管理ガイド](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP HANA システム レプリケーションに関する SAP Academy ビデオ](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP サポート ノート #1999880 - SAP HANA システム レプリケーションに関する FAQ](https://bcs.wdf.sap.corp/sap/support/notes/1999880)
- [SAP サポート ノート #2165547 - SAP HANA システム レプリケーション環境での SAP HANA のバックアップと復元](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP サポート ノート #1984882 - 最小/ゼロ ダウンタイムでのハードウェア交換のための SAP HANA システム レプリケーションの使用](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="disaster-recovery"></a>障害復旧

SAP HANA on Azure (L インスタンス) は、1 つの地政学的リージョン内の&2; つの Azure リージョンで提供されます。 2 つの異なるリージョンの&2; つの L インスタンス スタンプ間には、障害復旧時にデータをレプリケートするための直接ネットワーク接続があります。 データのレプリケーションは、ストレージインフラストラクチャ ベースです。 このレプリケーションは既定では実行されません。 障害復旧が指定されたお客様の構成に基づいて行われます。 現在の設計では、HANA システム レプリケーションは障害復旧に使用できません。

ただし、障害復旧を利用するには、2 つの異なる Azure リージョンへのネットワーク接続の設計を開始する必要があります。 これを行うには、オンプレミスからメイン Azure リージョンへの Azure ExpressRoute 回線接続のほか、オンプレミスから障害復旧リージョンへの回線接続が必要になります。 この方法では、Microsoft Enterprise Edge Router (MSEE) の場所を含む Azure リージョン全体で問題が発生した状況に対処できます。

2 つ目の方法では、いずれかのリージョンの SAP HANA on Azure (L インスタンス) に接続されるすべての Azure 仮想ネットワークを、これらの ExpressRoute 回線の両方に接続できます。 この方法では、オンプレミスの場所を Azure と接続する MSEE の場所のいずれか&1; つのみが動作を停止した場合に対処できます。

次の図は、障害復旧に最適な構成を示しています。

![障害復旧に最適な構成](./media/sap-hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)

ネットワークの障害復旧の構成に最適なケースでは、オンプレミスから&2; つの異なる Azure リージョンへの ExpressRoute 回線が&2; つあります。 1 つの回線は、実稼働インスタンスを実行しているリージョン #1 につながっています。 2 つ目の ExpressRoute 回線は、いくつかの非実稼働環境 HANA インスタンスを実行しているリージョン #2 につながっています  (このことは、MSEE と L インスタンス スタンプを含む Azure リージョン全体が切断された場合に重要です)。

2 つ目の方法では、各種の仮想ネットワークが、SAP HANA on Azure (L インスタンス) に接続している各種の ExpressRoute 回線に接続されます。 後で説明するように、MSEE の障害が発生している場所をバイパスしたり、障害復旧の目標復旧時点を短縮したりすることができます。

障害復旧セットアップの次の要件は、以下のとおりです。

- 製品 SKU と同じサイズの SAP HANA on Azure (L インスタンス) SKU を指定し、障害復旧リージョンにデプロイする必要があります。 これらのインスタンスは、テスト、サンドボックス、または QA HANA インスタンスを実行するために使用できます。
- 必要に応じて、障害復旧サイトで復旧する SAP HANA on Azure (L インスタンス) SKU ごとに障害復旧プロファイルを指定する必要があります。 こうすることで、ストレージ ボリュームが割り当てられます。これらのストレージ ボリュームは、実稼働リージョンから障害復旧リージョンへのストレージ レプリケーションのターゲットになります。

上記の要件を満たしたら、ストレージ レプリケーションを開始する必要があります。 SAP HANA on Azure (L インスタンス) に使用されるストレージ インフラストラクチャで、ストレージ レプリケーションの基盤となるのは、ストレージ スナップショットです。 障害復旧レプリケーションを開始するには、以下を実行する必要があります。

- 前に説明した、ブート LUN のスナップショット。
- 前に説明した、HANA 関連ボリュームのスナップショット。

これらのスナップショットの実行後、障害復旧リージョンの障害復旧プロファイルに関連付けられたボリュームで、ボリュームの初期レプリカがシード処理されます。

次に、1 時間おきに最新のストレージ スナップショットが使用され、ストレージ ボリュームで増加した差分がレプリケートされます。

この構成で実現される目標復旧時点は 60 ～ 90 分です。 障害復旧のケースでより良い目標復旧時点を実現するには、SAP HANA on Azure (L インスタンス) の HANA トランザクション ログ バックアップを他の Azure リージョンにコピーします。 この目標復旧時点を実現するには、次の操作を行います。

1. HANA トランザクション ログをできる限り頻繁に /hana/log/backup にバックアップします。
2. トランザクション ログのバックアップが終了したら、それを Azure 仮想マシン (VM) にコピーします。この VM は、SAP HANA on Azure (L インスタンス) サーバーに接続されている仮想ネットワーク内にあります。
3. その VM から、障害復旧リージョンの仮想ネットワーク内にある VM にバックアップをコピーします。
4. そのリージョンの VM にトランザクション ログ バックアップを保持します。

障害が発生した場合は、障害復旧プロファイルが実際のサーバーにデプロイされた後で、VM から障害復旧リージョンでプライマリになった SAP HANA on Azure (L インスタンス) にトランザクション ログ バックアップをコピーし、これらのバックアップを復元します。 この復旧が可能であるのは、障害復旧ディスクでの HANA の状態が HANA スナップショットの状態であるためです。 これが、トランザクション ログ バックアップを今後復元する際のオフセット ポイントになります。

## <a name="backup-and-restore"></a>バックアップと復元

データベースを運用するうえで最も重要な点の&1; つは、各種の危機的な事象からデータベースを確実に保護することです。 こうした事象の原因には、自然災害から単純なユーザー エラーまで、あらゆることが考えられます。

データベースをバックアップし、任意の時点 (たとえば、だれかが重要なデータを削除してしまう前など) に復元できるようにすることで、中断が発生する前の状態にできる限り近い状態への復元が可能になります。

最良の結果を得るには、次の&2; 種類のバックアップを実行する必要があります。

- データベースのバックアップ
- トランザクション ログのバックアップ

アプリケーション レベルで実行されるデータベースの完全バックアップに加え、ストレージ スナップショットによるバックアップを実行することによって、さらに万全を期すことができます。 データベースを復元するには、ログ バックアップを実行すること (および、既にコミットされたトランザクションをログから削除すること) も重要です。

SAP HANA on Azure (L インスタンス) には、次の&2; つのバックアップと復元のオプションがあります。

- 自分で実行する (DIY)。 計算して十分なディスク領域を確保した後、ディスク バックアップ方法を使用して、(これらのディスクへの) データベースとログの完全バックアップを実行します。 時間の経過と共に、バックアップは Azure ストレージ アカウントにコピーされます (実質的に無制限のストレージで Azure ベースのファイル サーバーをセットアップした後)。または、Azure バックアップ コンテナーまたは Azure コールド ストレージを使用します。 そのほか、Commvault などのサードパーティのデータ保護ツールを使用して、バックアップがストレージ アカウントにコピーされた後にそのバックアップを保存するというオプションもあります。 DIY バックアップ オプションは、コンプライアンスと監査の目的で長期間保存しなければならないデータで必要になることがあります。
- SAP HANA on Azure (L インスタンス) の基になるインフラストラクチャによって提供されるバックアップと復元の機能を使用する。 このオプションはバックアップのニーズに応えており、手動バックアップがほぼ不要になります (データのバックアップがコンプライアンスのために必要な場合を除く)。 このセクションの残りの部分では、HANA (L インスタンス) で提供されるバックアップと復元の機能について説明します。

> [!NOTE]
> HANA (L インスタンス) の基になるインフラストラクチャで使用されているスナップショット テクノロジは、SAP HANA スナップショットへの依存関係があります。 SAP HANA スナップショットは、SAP HANA マルチテナント データベース コンテナーと一緒には機能しません。 そのため、このバックアップ方法は SAP HANA マルチテナント データベース コンテナーのデプロイに使用できません。

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (L インスタンス) のストレージ スナップショットの使用

SAP HANA on Azure (L インスタンス) の基になっているストレージ インフラストラクチャでは、ボリュームのストレージ スナップショットの概念がサポートされています。 特定のボリュームのバックアップと復元の両方がサポートされますが、次の点を考慮する必要があります。

- データベース バックアップの代わりに、ストレージ ボリューム スナップショットが頻繁に作成されます。
- ストレージ スナップショットの実行前に、ストレージ スナップショットによって SAP HANA スナップショットが開始されます。 この SAP HANA スナップショットは、ストレージ スナップショットの復旧後に最終的なログ復元を設定するポイントです。
- ストレージ スナップショットが正常に実行された時点で、SAP HANA スナップショットは削除されます。
- ログ バックアップが頻繁に作成され、ログ バックアップ ボリュームまたは Azure に保存されます。
- データベースを特定の時点に復元する必要がある場合は、Microsoft Azure サポート (運用停止) または SAP HANA on Azure サービス管理に対して、特定のストレージ スナップショットへの復元の要求を行います (たとえば、元の状態へのサンドボックス システムの計画的な復元)。
- ストレージ スナップショットに含まれている SAP HANA スナップショットは、ストレージ スナップショットの作成後に実行、保存されたログ バックアップを適用するためのオフセット ポイントです。
- これらのログ バックアップは、データベースを特定の時点に復元するために作成されます。

backup\_name を指定すると、次のボリュームのスナップショットが作成されます。

- hana/data
- hana/log
- hana/log\_backup (バックアップとして hana/log にマウントされます)
- hana/shared

### <a name="storage-snapshot-considerations"></a>ストレージ スナップショットに関する考慮事項

>[!NOTE]
>ストレージ スナップショットは、追加の記憶域スペースを割り当てる必要があるため、無料では提供され "_ません_"。

SAP HANA on Azure (L インスタンス) のストレージ スナップショットに固有のメカニズムは、次のとおりです。

- 作成時点での特定のストレージ スナップショットは、ごくわずかな記憶域しか使用しません。
- データの内容は変化し、ストレージ ボリュームで SAP HANA データ ファイルの内容が変化するため、スナップショットは元のブロックの内容を保存する必要があります。
- ストレージ スナップショットのサイズは増加します。 スナップショットの存在期間が長くなるほど、ストレージ スナップショットが大きくなります。
- ストレージ スナップショットの有効期間に SAP HANA データベース ボリュームに対する変更が多くなるほど、ストレージ スナップショットの領域の消費量が大きくなります。

SAP HANA on Azure (L インスタンス) には、SAP HANA のデータとログ ボリューム用の固定ボリューム サイズがあります。 これらのボリュームのスナップショットを実行すると、ボリューム領域が消費されるため、ユーザーの責任において、(SAP HANA on Azure (L インスタンス) のプロセス内で) ストレージ スナップショットをスケジュールする必要があります。

以降のセクションでは、これらのスナップショットの実行に関して、次のような一般的な推奨事項も含めて説明します。

- ハードウェアはボリュームごとに 255 個のスナップショットを保持できますが、この数よりも十分に少なくしておくことを強くお勧めします。
- ストレージ スナップショットを実行する前に、空き領域を監視して追跡します。
- 空き領域に基づいて、ストレージ スナップショットの数を減らします。 場合によっては、保持するスナップショットの数を減らしたり、ボリュームを拡大したりする必要があります  (追加のストレージは 1 TB 単位で指定できます)。
- (R3load で、またはバックアップから SAP HANA データベースを復元して) システム移行ツールを使って SAP HANA にデータを移動するなどの操作を行っている間は、ストレージ スナップショットを実行しないことを強くお勧めします  (システムの移行は新しい SAP HANA システムに対して行われるため、ストレージ スナップショットを実行する必要がありません)。
- SAP HANA テーブルの大規模な再編成中は、できるだけストレージ スナップショットを避けてください。
- ストレージ スナップショットは、SAP HANA on Azure (L インスタンス) の障害復旧機能を利用するための前提条件です。

### <a name="setting-up-storage-snapshots"></a>ストレージ スナップショットのセットアップ

1. HANA (L インスタンス) サーバーの Linux オペレーティング システムに Perl がインストールされていることを確認します。
2. /etc/ssh/ssh\_config を変更して、_MACs hmac-sha1_ という行を追加します。
3. 実行している各 SAP HANA インスタンスのマスター ノードで、SAP HANA バックアップ ユーザー アカウントを作成します (該当する場合)。
4. すべての SAP HANA (L インスタンス) サーバーに、SAP HANA HDB クライアントをインストールする必要があります。
5. スナップショットの作成を制御する、基になるストレージ インフラストラクチャにアクセスするために、各リージョンの最初の SAP HANA (L インスタンス) サーバーで公開キーを作成する必要があります。
6. スクリプト azure\_hana\_backup.pl を、/scripts から SAP HANA インストールの **hdbsql** の場所にコピーします。
7. HANABackupDetails.txt ファイルを、/scripts から Perl スクリプトと同じ場所にコピーします。
8. 適切な顧客仕様での必要に応じて、HANABackupDetails.txt ファイルを変更します。

### <a name="step-1-install-sap-hana-hdbclient"></a>手順 1. SAP HANA HDBClient をインストールする

SAP HANA on Azure (L インスタンス) にインストールされている Linux には、バックアップと障害復旧の目的で SAP HANA ストレージ スナップショットを実行するために必要なフォルダーとスクリプトが含まれています。 ただし、SAP HANA のインストール中に、ユーザーが SAP HANA HDBclient をインストールする必要があります  (Microsoft は、HDBclient も SAP HANA もインストールしません)。

### <a name="step-2-change-etcsshsshconfig"></a>手順 2. /etc/ssh/ssh\_config を変更する

次に示すように、_MACs hmac-sha1_ 行を追加して /etc/ssh/ssh\_config を変更します。
```
#   RhostsRSAAuthentication no
#   RSAAuthentication yes
#   PasswordAuthentication yes
#   HostbasedAuthentication no
#   GSSAPIAuthentication no
#   GSSAPIDelegateCredentials no
#   GSSAPIKeyExchange no
#   GSSAPITrustDNS no
#   BatchMode no
#   CheckHostIP yes
#   AddressFamily any
#   ConnectTimeout 0
#   StrictHostKeyChecking ask
#   IdentityFile ~/.ssh/identity
#   IdentityFile ~/.ssh/id_rsa
#   IdentityFile ~/.ssh/id_dsa
#   Port 22
Protocol 2
#   Cipher 3des
#   Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc
#   MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160
MACs hmac-sha1
#   EscapeChar ~
#   Tunnel no
#   TunnelDevice any:any
#   PermitLocalCommand no
#   VisualHostKey no
#   ProxyCommand ssh -q -W %h:%p gateway.example.com
```

### <a name="step-3-create-a-public-key"></a>手順 3. 公開キーを作成する

各 Azure リージョンの最初の SAP HANA on Azure (L インスタンス) サーバーで、ストレージ インフラストラクチャへのアクセスに使用される公開キーを作成し、スナップショットを作成できるようにします。 公開キーを使用すると、ストレージへのサインインにパスワードが不要になり、パスワード資格情報を保持せずに済みます。 公開キーを生成するには、SAP HANA (L インスタンス) サーバーの Linux で、次のコマンドを実行します。
```
  ssh-keygen –t dsa –b 1024
```
新しい場所は _/root/.ssh/id\_dsa.pub です。 実際のパスフレーズを入力しないようにしてください。入力してしまうと、サインインのたびにパスフレーズの入力が必要になります。 代わりに、**Enter** キーを&2; 回押して、サインイン時のパスフレーズ入力要求を消します。

フォルダーを /root/.ssh/ に変更して **Is** コマンドを実行することで、公開キーが想定どおり修正されたことを確認します。 キーがあれば、次のコマンドを実行してコピーできます。

![このコマンドを実行すると公開キーがコピーされる](./media/sap-hana-overview-high-availability-disaster-recovery/image2-public-key.png)

この時点で、SAP HANA on Azure サービス管理に連絡し、キーを提供します。 サービス担当者は、その公開キーを使用して、基になるストレージ インフラストラクチャにそのキーを登録します。

### <a name="step-4-create-an-sap-hana-user-account"></a>手順 4. SAP HANA ユーザー アカウントを作成する

バックアップのために、SAP HANA Studio 内で SAP HANA ユーザー アカウントを作成します。 このアカウントには、"_BACKUP ADMIN_" と "_CATALOG READ_" という権限を設定する必要があります。 この例では、SCADMIN というユーザー名が作成されます。

![HANA Studio でのユーザーの作成](./media/sap-hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

### <a name="step-5-authorize-the-sap-hana-user-account"></a>手順 5. SAP HANA ユーザー アカウントを承認する

SAP HANA ユーザー アカウントを承認します (スクリプトで使用するとき、スクリプトが実行されるたびに承認が要求されないようにするため)。 SAP HANA のコマンド `hdbuserstore` を使用して SAP HANA のユーザー キーを作成できます。これは&1; つ以上の SAP HANA ノードに格納されます。 ユーザーはこのユーザー キーを使用すると、後で説明するスクリプト プロセス内からパスワードを管理しなくても SAP HANA にアクセスできます。

>[!IMPORTANT]
>次のコマンドを `_root_` として実行します。 そうしないと、スクリプトは正しく動作しません。

次のように `hdbuserstore` コマンドを入力します。

![hdbuserstore コマンドを入力する](./media/sap-hana-overview-high-availability-disaster-recovery/image4-hdbuserstore-command.png)

次の例では、ユーザーは SCADMIN01、ホスト名は lhanad01 であるため、コマンドは次のようになります。
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
スケールアウトの HANA インスタンスでは、すべてのスクリプトを&1; つのサーバーから管理します。 この例では、SAP HANA のキー SCADMIN01 は、キーに関連付けられているホストが反映されるようにホストごとに変更する必要があります。 つまり、SAP HANA バックアップ アカウントには、HANA DB のインスタンス番号 **lhanad** が追加されます。 キーは、割り当て先のホストに対する管理特権を持つ必要があります。また、スケールアウトのためのバックアップ ユーザーは、すべての SAP HANA インスタンスへのアクセス権を持つ必要があります。
```
hdbuserstore set SCADMIN01 lhanad:30015 SCADMIN <password>
hdbuserstore set SCADMIN02 lhanad:30115 SCADMIN <password>
hdbuserstore set SCADMIN03 lhanad:30215 SCADMIN <password>
```

### <a name="step-6-copy-items-from-the-scripts-folder"></a>手順 6. /scripts フォルダーからアイテムをコピーする

(インストールのゴールド イメージに含まれている) /scripts フォルダーから次のアイテムを **hdbsql** の作業ディレクトリにコピーします。 現在の HANA インストールでは、このディレクトリは /hana/shared/D01/exe/linuxx86\_64/hdb です。
```
azure\_hana\_backup.pl
testHANAConnection.pl
testStorageSnapshotConnection.pl
removeTestStorageSnapshot.pl
HANABackupCustomerDetails.txt
```
スケールアウトまたは OLAP を実行している場合は、次のアイテムをコピーします。
```
azure\_hana\_backup\_bw.pl
testHANAConnectionBW.pl
testStorageSnapshotConnectionBW.pl
removeTestStorageSnapshotBW.pl
HANABackupCustomerDetailsBW.txt
```
HANABackupCustomerDetails.txt ファイルは、スケールアップ デプロイの場合、次のように変更できます。 これは、ストレージ スナップショットを実行するスクリプト用の管理と構成のファイルです。 インスタンスがデプロイされたときに、SAP HANA on Azure サービス管理から "_ストレージ バックアップ名_" と "_ストレージ IP アドレス_" を受け取っています。 どの変数も、シーケンス、順序、またはスペースを変更することはできません。変更すると、スクリプトが正常に動作しなくなります。

スケールアップ デプロイの場合、構成ファイルは次のようになります。
```
#Provided by Microsoft Service Management
Storage Backup Name: lhanad01backup
Storage IP Address: 10.250.20.21
#Created by customer using hdbuserstore
HANA Backup Name: SCADMIND01
```
スケールアウト構成の場合、HANABackupCustomerDetailsBW.txt ファイルは次のようになります。
```
#Provided by Microsoft Service Management
Storage Backup Name: lhanad01backup
Storage IP Address: 10.250.20.21
#Node IP addresses, instance numbers, and HANA backup name
#provided by customer.  HANA backup name created using
#hdbuserstore utility.
Node 1 IP Address: 10.254.15.21
Node 1 HANA instance number: 01
Node 1 HANA Backup Name: SCADMIN01
Node 2 IP Address: 10.254.15.22
Node 2 HANA instance number: 02
Node 2 HANA Backup Name: SCADMIN02
Node 3 IP Address: 10.254.15.23
Node 3 HANA instance number: 03
Node 3 HANA Backup Name: SCADMIN03
Node 4 IP Address: 10.254.15.24
Node 4 HANA instance number: 04
Node 4 HANA Backup Name: SCADMIN04
Node 5 IP Address: 10.254.15.25
Node 5 HANA instance number: 05
Node 5 HANA Backup Name: SCADMIN05
Node 6 IP Address: 10.254.15.26
Node 6 HANA instance number: 06
Node 6 HANA Backup Name: SCADMIN06
Node 7 IP Address: 10.254.15.27
Node 7 HANA instance number: 07
Node 7 HANA Backup Name: SCADMIN07
Node 8 IP Address: 10.254.15.28
Node 8 HANA instance number: 08
Node 8 HANA Backup Name: SCADMIN08
```
>[!NOTE]
>現在、実際の HANA ストレージ スナップショット スクリプトでは Node 1 の詳細のみが使用されます。 マスター バックアップ ノードを変更する場合に、Node 1 の詳細を変更して他のどのノードでも代替できるようにしておくため、すべての HANA ノードとの間のアクセスをテストすることをお勧めします。

構成ファイル内の構成が正しいこと、または HANA インスタンスへの接続が適切であることを確認するには、次のいずれかのスクリプトを実行します。
- (SAP ワークロードから独立した) スケールアップ構成の場合:

 ```
testHANAConnection.pl
```
- スケールアウト構成の場合:

 ```
testHANAConnectionBW.pl
```

マスター HANA インスタンスが、必要なすべての HANA サーバーにアクセスできることを確認します。 スクリプトへのパラメーターはありませんが、スクリプトを正しく実行するには適切な HANABackupCustomerDetails/HANABackupCustomerDetailsBW ファイルを作成する必要があります。 シェル コマンド エラー コードのみが返されるため、スクリプトですべてのインスタンスのエラーチェックを行うことはできません。 それでも、ダブルチェックを行ううえで有益なコメントをいくつかこのスクリプトから得ることができます。

スクリプトを実行するには、次の手順を実行します。
```
 ./testHANAConnection.pl
```
 このスクリプトは、HANA インスタンスの状態の取得に成功した場合、HANA 接続が成功したというメッセージを出力します。

さらに、マスター HANA インスタンス サーバーがストレージにサインインできることを確認するために使用できるスクリプトがもう&1; 種類あります。 azure\_hana\_backup(\_bw).pl スクリプトを実行する前に、次のスクリプトを実行する必要があります。 ボリュームにスナップショットが存在しない場合、ボリュームが単純に空であるのか、それともスナップショットの詳細を取得する際に ssh エラーが発生しているのかを特定することができません。 このため、このスクリプトでは次の&2; つのステップが実行されます。

- ストレージ コンソールにアクセスできることを確認する。
- HANA インスタンスでボリュームごとにテスト (ダミー) スナップショットを作成する。

このため、HANA インスタンスが引数として含まれます。 ここでも、ストレージ接続のエラー チェックはできませんが、実行が失敗した際にスクリプトから役に立つヘルプを得られます。

スクリプトは、次のように実行されます。
```
 ./testStorageSnapshotConnection.pl <hana instance>
```
または、次のように実行されます。
```
./testStorageSnapshotConnectionBW.pl <hana instance>
```
このスクリプトは、ユーザーが所有するサーバー インスタンスによって使用される論理ユニット番号 (LUN) を中心にして編成されたデプロイ済みストレージ テナントにユーザーが適切にサインインできるというメッセージも出力します。

最初のストレージ スナップショットベースのバックアップを実行する前に、次のスクリプトを実行して、構成が正しいことを確認する必要があります。

これらのスクリプトの実行後、次を実行してスナップショットを削除できます。
```
./removeTestStorageSnapshot.pl <hana instance>
```
または
```
./removeTestStorageSnapshot.pl <hana instance>
```

### <a name="step-7-perform-on-demand-snapshots"></a>手順 7. オンデマンドのスナップショットを実行する

以下の説明に従って、オンデマンドのスナップショット (および cron を使用してスケジュールされた定期スナップショット) を実行します。

スケールアップ構成の場合、次のスクリプトを実行します。
```
./azure_hana_backup.pl lhanad01 customer 20
```
スケールアウト構成の場合、次のスクリプトを実行します。
```
./azure_hana_backup_bw.pl lhanad01 customer 20
```
スケールアウトのスクリプトは、SAP HANA またはストレージ スナップショットの作成に進む前に、すべての HANA サーバーにアクセスできることと、すべての HANA インスタンスが適切な状態を返すことを確認するために、いくつかの追加のチェックを行います。

以下の引数は必須です。

- バックアップを要求する HANA インスタンス。
- ストレージ スナップショットのスナップショット プレフィックス。
- 特定のプレフィックスで保持するスナップショットの数。

```
./azure_hana_backup.pl lhanad01 customer 20
```

スクリプトを実行すると、次の&3; つのフェーズで、ストレージ スナップショットが作成されます。

- HANA スナップショットを実行する。
- ストレージ スナップショットを実行する。
- HANA スナップショットを削除する。

スクリプトを、コピー先の HDB 実行可能フォルダーから呼び出して実行します。 これにより、少なくとも次のボリュームがバックアップされますが、ボリューム名に明示的な SAP HANA インスタンス名を含むボリュームもバックアップされます。
```
hana_data_<hana instance>_prod_t020_vol
hana_log_<hana instance>_prod_t020_vol
hana_log_backup_<hana instance>_prod_t020_vol
hana_shared_<hana instance>_prod_t020_vol
```
保有期間は、スクリプト実行時にパラメーターとして指定されたスナップショットの数 (前の例では 20) で厳密に管理されます。 そのため、その期間は、実行の期間と、スクリプトの呼び出し内のスナップショット数との相関関係にあります。 保持されるスナップショットの数が、スクリプトの呼び出しでパラメーターとして指定された数を超える場合は、新しいスナップショットの実行前に、このラベル (前の例では _custom_) の最も古いストレージ スナップショットが削除されます。 つまり、呼び出しの最後のパラメーターとして指定した数が、スナップショットの数を制御するために使用できる数になります。

>[!NOTE]
>ラベルを変更するとすぐに、カウントが再開されます。

マルチノード環境のスナップショットを作成している場合は、SAP HANA on Azure サービス管理によって提供される HANA インスタンス名を引数として含める必要があります。 単一ノード環境では SAP HANA on Azure (L インスタンス) ユニットの名前で十分ですが、引き続き HANA インスタンス名をお勧めします。

さらに、同じスクリプトを使用して boot volumes\LUNs をバックアップできます。 cron でブートの毎週または毎晩のバックアップ スケジュールを設定することをお勧めしますが、初めて HANA を実行する際に、ブート ボリュームを少なくとも&1; 回はバックアップする必要があります。 SAP HANA インスタンス名を追加するのではなく、次のようにスクリプトに引数として _boot_ を挿入します。
```
./azure_hana_backup boot customer 20
```
ブート ボリュームにも、同じ保持ポリシーが適用されます。 前に説明したように、SAP 拡張パッケージ (EHP) のアップグレード中や別のストレージ スナップショットを作成する必要があるときなど、特別な場合にのみ、オンデマンド スナップショットを使用します。

cron を使用してスケジュールされたストレージ スナップショットを実行することをお勧めします。また、すべてのバックアップと障害復旧のニーズで同じスクリプトを (要求されるさまざまなバックアップ時刻に合わせてスクリプトの入力を変更して) 使用することをお勧めします。 実行時間に応じて、cron でさまざまなスケジュールを設定できます。1 時間ごと、12 時間ごと、毎日、または毎週に設定できます。 cron スケジュールは、前に説明した長期間のオフサイト バックアップ用の保持ラベルに適したストレージ スナップショットを作成するように設計されています。 スクリプトには、要求される頻度に応じてすべての実稼働ボリュームをバックアップするためのコマンドが含まれています (ブート ボリュームは毎日バックアップされますが、データとログ ファイルは&1; 時間ごとにバックアップされます)。

次の cron スクリプト内のエントリは、毎時 10 分、12 時間ごとの 10 分、および毎日 10 分に実行されます。 cron ジョブは、特定の時間内に SAP HANA ストレージ スナップショットが&1; つのみ実行されるように作成されます。そのため、毎時と毎日のバックアップが同時 (午前&12;:10) に行われることはありません。 スナップショットの作成とレプリケーションを最適化できるように、SAP HANA on Azure サービス管理によってバックアップを実行するための推奨時間が示されます。

/etc/crontab の既定の cron スケジュールは、次のとおりです。
```
10 1-11,13-23 * * * ./azure_hana_backup.pl lhanad01 hourly 66
10 12 * * *  ./azure_hana_backup.pl lhanad01 12hour 14
```
上の cron 命令では、HANA ボリューム (ブート ボリュームを除く) はそのラベルで毎時のスナップショットを取得します。 これらのスナップショットのうち、66 個が保持されます。 さらに、12 時間ごとのラベルのスナップショットは 14 個保持されます。 3 日間毎時のスナップショットを取得し、さらに 4 日間 12 時間ごとのスナップショットを取得できるため、丸一週間分のスナップショットを取得できます。

cron でのスケジュールは、スクリプトが数分間隔で実行されない限り、特定の時間に実行されるスクリプトが&1; つのみであるため、わかりにくくなる場合があります。 長期的な保有用に毎日のバックアップが必要な場合、毎日のスナップショットを 12 時間ごとのスナップショットと共に保持する (それぞれの保有数は 7) か、毎時のスナップショットが 10 分後に行われるようにずらします。 実稼働ボリュームに保持されるのは毎日のスナップショット&1; つのみです。
```
10 1-11,13-23 * * * ./azure_hana_backup.pl lhanad01 hourly 66
10 12 * * *  ./azure_hana_backup.pl lhanad01 12hour 7
10 0 * * * ./azure_hana_backup.pl lhanad01 daily 7
```
ここに表示されている頻度は単なる例です。 スナップショットの最適な数を把握するには、次の条件を使用します。

- 特定の時点に復旧するための目標復旧時間の要件。
- 領域の使用量。
- 実行される可能性のある障害復旧のための目標復旧時点と目標復旧時間の要件。
- ディスクに対する HANA データベースの完全バックアップの最終的な実行。 ディスクまたは _backint_ インターフェイスに対するデータベースの完全バックアップが実行されるたびに、ストレージ スナップショットの実行は失敗します。 ストレージ スナップショットに加えてデータベースの完全バックアップを実行する予定がある場合は、その実行中にストレージ スナップショットの実行が無効になるようにします。

>[!IMPORTANT]
> SAP HANA バックアップに対するストレージ スナップショットの使用は、スナップショットが SAP HANA ログ バックアップと共に実行される場合にのみ有効です。 これらのログ バックアップは、ストレージ スナップショット間の期間をカバーできる必要があります。 30 日間の特定の時点への復旧をユーザーに確約している場合は、次が必要です。

- 30 日前のストレージ スナップショットにアクセスできること。
- 過去 30 日間の連続したログ バックアップ。

ログ バックアップの範囲では、バックアップ ログ ボリュームのスナップショットも作成します。 しかし、次の目的で必ず定期的なログ バックアップを実行してください。

- 特定の時点への復旧を実行するために必要な連続したログ バックアップを作成する。
- SAP HANA ログ ボリュームの領域不足を回避する。

最後の手順の&1; つとして、SAP HANA Studio で SAP HANA バックアップ ログをスケジュールします。 SAP HANA バックアップ ログのターゲットの場所は、特別に作成された hana/log\_backups ボリュームで、マウント ポイントは /hana/log/backups です。

![SAP HANA Studio での SAP HANA バックアップ ログのスケジュール](./media/sap-hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

バックアップの間隔は 15 分よりも短くすることができます。 一部のユーザーは 1 分ごとにログ バックアップを実行する場合もありますが、15 分より "_短く_" することはお勧めしません。

最後の手順は、(SAP HANA の初期インストール後に) ファイルベースのバックアップを実行し、バックアップ カタログ内に存在する必要がある単一のバックアップ エントリを作成することです。 これを実行しないと、SAP HANA は指定されたログ バックアップを開始できません。

![ファイル ベースのバックアップで単一のバックアップ エントリが作成されるようにする](./media/sap-hana-overview-high-availability-disaster-recovery/image6-make-backup.png)

### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>ディスク ボリュームにあるスナップショットの数とサイズの監視

特定のストレージ ボリュームでは、スナップショットの数と、スナップショットによるストレージの使用量を監視できます。 `ls` コマンドでは、スナップショットのディレクトリまたはファイルが表示されません。 一方、Linux OS コマンドの `du` を次のコマンドと共に使用すると、それらが表示されます。

- `du –sh .snapshot` では、スナップショットのディレクトリ内にあるすべてのスナップショットの合計が表示されます。
- `du –sh --max-depth=1` では、.snapshot フォルダーに保存されているすべてのスナップショットとそれらの各スナップショットのサイズが一覧表示されます。
- `du –hc` では、すべてのスナップショットによって使用される合計サイズが表示されます。

作成および格納したスナップショットがボリューム上のストレージを使用し尽くしていないことを確認するには、上記のコマンドを使用します。

### <a name="reducing-the-number-of-snapshots-on-a-server"></a>サーバー上のスナップショットの数の削減

既に説明したように、特定のラベルで格納されるスナップショットの数を減らすことができます。 スナップショットを開始するコマンドの最後の&2; つのパラメーターは、ラベルと、保持するスナップショットの数です。
```
./azure_hana_backup.pl lhanad01 customer 20
```
前の例では、スナップショット ラベルは _customer_ であり、このラベルの付いた保持するスナップショットの数は _20_ です。 ディスク領域の使用量に応じて、格納されるスナップショットの数を減らすことができます。 スナップショットの数を減らす簡単な方法は、最後のパラメーターを 5 に設定してスクリプトを実行することです。
```
./azure_hana_backup.pl lhanad01 customer 5
```
この設定でスクリプトを実行すると、スナップショットの数は (新しいストレージ スナップショットを含めて) _5_ つになります。

 >[!NOTE]
 > このスクリプトでは、過去の最新のスナップショットが作成されて&1; 時間経過している場合にのみスナップショットの数が減らされます。 このスクリプトでは作成から&1; 時間経過していないスナップショットは削除されません。

これらの制限は、提供されるオプションの障害復旧機能に関連しています。

特定のプレフィックスが付いた一連のスナップショットを保持する必要がなくなった場合、保持数を _0_ にしてスクリプトを実行し、そのプレフィックスが一致するすべてのスナップショットを削除できます。 ただし、すべてのスナップショットを削除すると、障害復旧の機能に影響が生じる可能性があります。

### <a name="recovering-to-the-most-recent-hana-snapshot"></a>最新の HANA スナップショットへの復旧

運用停止のシナリオが発生した場合は、SAP HANA on Azure サービス管理での顧客インシデントとして、ストレージ スナップショットからの復旧のプロセスを開始できます。 運用システムのデータが削除されており、データを取得する唯一の方法が運用データベースの復元である場合、このような予期しないシナリオは緊急度の高い問題である場合があります。

一方、特定の時点への復旧は緊急度が低く、数日前から計画できる場合があります。 この復旧は、優先度の高い問題として提起するのではなく、SAP HANA on Azure サービス管理と共に計画できます。 例を挙げると、新しい拡張パッケージを適用して SAP ソフトウェアのアップグレードを試すことを計画しており、後で EHP アップグレード前の状態を表すスナップショットに戻す必要がある場合です。

要求を発行する前に、少し準備を行う必要があります。 そうすることで、SAP HANA on Azure サービス管理チームが要求を処理し、復元されたボリュームを提供できます。 その後、スナップショットに基づいて HANA データベースを復元するのはユーザーです。 要求の準備は次の方法で行います。

>[!NOTE]
>使用中の SAP HANA リリースによっては、ユーザー インターフェイスが以下のスクリーンショットと異なる場合があります。

1. 復元するスナップショットを決めます。 他に指示がない限り、hana/data ボリュームのみが復元されます。

2. HANA インスタンスをシャットダウンします。

 ![HANA インスタンスをシャットダウンする](./media/sap-hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. 各 HANA データベース ノードで、データ ボリュームのマウントを解除します。 データ ボリュームのマウントが解除されていない場合、スナップショットの復元は失敗します。

 ![各 HANA データベース ノードでデータ ボリュームのマウントを解除する](./media/sap-hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. 特定のスナップショットの復元を指示するための Azure サポート要求を開きます。

 - 復元中: SAP HANA on Azure サービス管理は、失われているデータがないことを確認するために、電話会議への出席を依頼する場合があります。

 - 復元後: SAP HANA on Azure サービス管理から、ストレージ スナップショットの復元が完了した時点で通知が届きます。

5. 復元処理が完了したら、すべてのデータ ボリュームを再マウントします。

 ![すべてのデータ ボリュームを再マウントする](./media/sap-hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

6. SAP HANA Studio で復旧オプションを選択します (SAP HANA Studio で HANA DB に再接続したときに自動的に選択されない場合)。 次の例は、最新の HANA スナップショットへの復元を示しています。 ストレージ スナップショットには&1; つの HANA スナップショットが組み込まれています。最新のストレージ スナップショットに復元している場合、それは最も新しい HANA スナップショットになります  (それより前のストレージ スナップショットに復元している場合は、ストレージ スナップショットが作成された時刻に基づいて HANA スナップショットを探す必要があります)。

 ![SAP HANA Studio 内で復旧オプションを選択する](./media/sap-hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

7. **[Recover the database to a specific data backup or storage snapshot (特定のデータ バックアップまたはストレージ スナップショットにデータベースを復旧する)]** を選択します。

 ![[Specify Recovery Type (復旧の種類を指定する)] ウィンドウ](./media/sap-hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

8. **[Specify backup without catalog (カタログのないバックアップを指定する)]** を選択します。

 ![[Specify Backup Location (バックアップ場所を指定する)] ウィンドウ](./media/sap-hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

9. **[Destination Type (復旧先の種類)]** の一覧で **[Snapshot (スナップショット)]** を選択します。

 ![[Specify the Backup to Recover (復旧するバックアップを指定する)] ウィンドウ](./media/sap-hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

10. **[Finish (完了)]** をクリックして復旧処理を開始します。

 ![[Finish (完了)] をクリックして復旧処理を開始する](./media/sap-hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

11. HANA データベースが復元され、ストレージ スナップショットに含まれている HANA スナップショットに復旧されます。

 ![HANA データベースが復元され、HANA スナップショットに復旧される](./media/sap-hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recovering-to-the-most-recent-state"></a>最新の状態への復旧

次の手順を使用して、ストレージ スナップショットに含まれている HANA スナップショットを復元します。 次に、トランザクション ログ バックアップを、ストレージ スナップショットを復元する前のデータベースの最新状態に復元します。

>[!IMPORTANT]
>作業を進める前に、完全かつ連続した一連のトランザクション ログ バックアップがあることを確認します。 これらのバックアップがない場合、データベースの現在の状態を復元することはできません。

1. 前の「最新の HANA スナップショットへの復旧」で説明した手順 1. ～ 6. を完了します。

2. **[Recover the database to its most recent state (データベースを最新の状態に復旧する)]** を選択します。

 ![[Recover the database to its most recent state (データベースを最新の状態に復旧する)] を選択する](./media/sap-hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

3. 最新の HANA ログ バックアップの場所を指定します。 この場所には、HANA スナップショットから最新の状態までの HANA トランザクション ログ バックアップがすべて含まれている必要があります。

 ![最新の HANA ログ バックアップの場所を指定する](./media/sap-hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

4. データベースを復旧するためのベースとなるバックアップを選択します。 この例では、ストレージ スナップショットに含まれている HANA スナップショットです  (次のスクリーンショットに表示されているスナップショットは&1; つのみです)。

 ![データベースを復旧するためのベースとなるバックアップを選択する](./media/sap-hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

5. HANA スナップショットの時刻から最新の状態までの間に差分がない場合は、**[Use Delta Backups (差分バックアップを使用する)]** チェック ボックスをオフにします。

 ![差分がない場合に [Use Delta Backups (差分バックアップを使用する)] チェック ボックスをオフにする](./media/sap-hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

6. 概要画面で **[Finish (完了)]** をクリックして復元処理を開始します。

 ![概要画面で [Finish (完了)] をクリックする](./media/sap-hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recovering-to-another-point-in-time"></a>異なる特定の時点への復旧
(ストレージ スナップショットに含まれている) HANA スナップショットから、HANA スナップショットによる特定の時点の復旧より後のスナップショットまでの時点に復旧する場合、次の手順を実行します。

1. HANA スナップショットから復旧時点までのすべてのトランザクション ログ バックアップがあることを確認します。
2. 「最新の状態への復旧」の操作を開始します。
3. 操作の手順 2. で、**[Specify Recovery Type (復旧の種類を指定する)]** ウィンドウの **[Recover the database to the following point in time (データベースを次の時点に復旧する)]** を選択し、特定の時点を指定してから、手順 3. ～ 6. を完了します。

## <a name="monitoring-the-execution-of-snapshots"></a>スナップショットの実行の監視

ストレージ スナップショットの実行を監視する必要があります。 ストレージ スナップショットを実行するスクリプトでは、出力がファイルに書き込まれ、Perl スクリプトと同じ場所に保存されます。 スナップショットごとに個別のファイルが書き込まれます。 各ファイルの出力には、スナップショット スクリプトが実行するさまざまな段階が明示されます。

- スナップショットを作成する必要があるボリュームを探す
- これらのボリュームから作成されたスナップショットを探す
- 指定されたスナップショット数に一致するように、最終的に存在しているスナップショットを削除する
- HANA スナップショットを作成する
- 複数のボリュームにわたるストレージ スナップショットを作成する
- HANA スナップショットを削除する
- 最新のスナップショットの名前を **.0** に変更する

スクリプトの最も重要な部分は、次のとおりです。
```
**********************Creating HANA snapshot**********************
Creating the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data create snapshot"" ...
HANA snapshot created successfully.
**********************Creating Storage snapshot**********************
Taking snapshot hourly.recent for hana_data_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_backup_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_shared_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for sapmnt_lhanad01_t020_vol ...
Snapshot created successfully.
**********************Deleting HANA snapshot**********************
Deleting the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data drop snapshot"" ...
HANA snapshot deletion successfully.
```
このサンプルから、スクリプトが HANA スナップショットの作成を記録する方法がわかります。 スケールアウトの場合は、このプロセスがマスター ノードで開始されます。 マスター ノードは各ワーカー ノードでスナップショットの同期的な作成を開始します。 その後、ストレージ スナップショットが作成されます。 ストレージ スナップショットの実行に成功すると、HANA スナップショットが削除されます。

