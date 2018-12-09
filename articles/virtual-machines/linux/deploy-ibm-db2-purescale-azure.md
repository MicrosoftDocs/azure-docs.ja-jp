---
title: Azure 上に IBM Db2 pureScale を展開する
description: z/OS 上で実行される IBM Db2 環境から Azure 上の IBM Db2 pureScale への企業の移行に最近使用されている[アーキテクチャの例](ibm-db2-purescale-azure.md)を展開する方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: njray
ms.openlocfilehash: 24bdc9867af869437cc0e440a80e5bf4813abbae
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/19/2018
ms.locfileid: "51978073"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Azure 上に IBM Db2 pureScale を展開する

この記事では、z/OS 上で実行される IBM Db2 環境から Azure 上の IBM Db2 pureScale への企業の移行に最近使用されている[アーキテクチャの例](ibm-db2-purescale-azure.md)を展開する方法について説明します。

移行中に使用される手順に従うには、GitHub 上の [Db2onAzure](http://aka.ms/db2onazure) リポジトリにあるインストール スクリプトをご覧ください。 これらのスクリプトは、一般的な中規模サイズのオンライン トランザクション処理 (OLTP) ワークロードに使用されるアーキテクチャに基づきます。

## <a name="get-started"></a>作業開始

このアーキテクチャを展開するには、GitHub 上の [Db2onAzure](http://aka.ms/db2onazure) リポジトリにある deploy.sh スクリプトをダウンロードして実行します。

また、リポジトリには、Grafana ダッシュボードを設定するために使用できるスクリプトも含まれています。このダッシュボードは、Db2 に含まれるオープンソース監視および警告システムである Prometheus を照会するために使用できます。

> [!NOTE]
> クライアント上の deploy.sh スクリプトは、プライベート SSH キーを作成して、HTTPS 経由で展開テンプレートにそのキーを渡します。 セキュリティを強化するために、シークレット、キー、およびパスワードの格納には、[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) を使用することをお勧めします。

## <a name="how-the-deployment-script-works"></a>展開スクリプトの仕組み

deploy.sh スクリプトは、このアーキテクチャに使用される Azure リソースを作成して構成します。 スクリプトでは、ターゲット環境で使用される Azure サブスクリプションと仮想マシンの入力を要求してから、次の操作を実行します。

-   インストール用に、Azure 上にリソース グループ、仮想ネットワーク、およびサブネットを設定します。

-   環境用に、NSG および SSH を設定します。

-   GlusterFS および Db2 pureScale 仮想マシンの両方に、複数の NIC を設定します。

-   GlusterFS ストレージ仮想マシンを作成します。

-   ジャンプボックス仮想マシンを作成します。

-   Db2 pureScale 仮想マシンを作成します。

-   Db2 pureScale から ping を実行する監視仮想マシンを作成します。

-   テストに使用する Windows 仮想マシンを作成します。ただし、このマシン上には何もインストールしません。

次に、展開スクリプトは、Azure 上の共有ストレージに対して iSCSI 仮想ストレージ エリア ネットワーク (vSAN) を設定します。 この例では、iSCSI は GlusterFS に接続します。 このソリューションは、単一の Windows ノードとして iSCSI ターゲットをインストールするオプションも提供します  (iSCSI は TCP/IP 経由で、共有ブロック ストレージ インターフェイスを提供します。これにより、Db2 pureScale セットアップ手順で、共有ストレージに接続するためにデバイス インターフェイスを使用できるようになります)。GlusterFS の基本については、「Getting started with GlusterFS」(GlusterFS の概要) の「[Architecture: Types of volumes](https://docs.gluster.org/en/latest/Quick-Start-Guide/Architecture/)」(アーキテクチャ: ボリュームの種類) セクションをご覧ください。

展開スクリプトでは、以下の一般的な手順を実行します。

1.  Azure 上に共有ストレージ クラスターを設定します。 共有ストレージ クラスターの設定には、GlusterFS が使用されます。 これには、少なくとも 2 つの Linux ノードが関係します。 セットアップの詳細については、Red Hat Gluster ドキュメントの「[Setting up Red Hat Gluster Storage in Microsoft Azure](https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.1/html/deployment_guide_for_public_cloud/chap-documentation-deployment_guide_for_public_cloud-azure-setting_up_rhgs_azure)」(Microsoft Azure に Red Hat Gluster ストレージをセットアップする) をご覧ください。

2.  iSCSI Direct インターフェイスを GlusterFS 対応のターゲット Linux サーバーに設定します。 セットアップの詳細については、「GlusterFS Administration Guide」(GlusterFS 管理ガイド) の「[GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)」をご覧ください。

3.  Linux 仮想マシン上に、iSCSI ターゲットを使用して GlusterFS クラスターにアクセスする iSCSI イニシエーターを設定します。 セットアップの詳細については、RootUsers ドキュメントの「[How To Configure An iSCSI Target And Initiator In Linux](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/)」(Linux で iSCSI ターゲットおよびイニシエーターを構成する方法) をご覧ください。

4.  ISCSI インターフェイスのストレージ層として、GlusterFS をインストールします。

ISCSI デバイスを作成した後は、最後の手順として Db2 pureScale をインストールします。 Db2 pureScale セットアップの一環として、[IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (以前は GPFS と呼ばれていた) がコンパイルされ、GlusterFS クラスター上にインストールされます。 このクラスター化されたファイル システムによって、Db2 pureScale では、Db2 pureScale エンジンを実行する複数の仮想マシン間のデータを共有することが可能になります。 詳細については、IBM Web サイトの「[IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html)」をご覧ください。

## <a name="db2-purescale-response-file"></a>Db2 pureScale の応答ファイル

GitHub リポジトリには、Db2server.rsp という応答 (.rsp) ファイルが用意されています。このファイルを利用して、Db2 pureScale インストールのための自動化されたスクリプトを生成できます。 次の表に、応答ファイルにおいてセットアップに使用される Db2 pureScale オプションを示します。 必要に応じて、インストール環境に合わせて応答ファイルをカスタマイズできます。

> [!NOTE]
> サンプル応答ファイル Db2server.rsp は、GitHub 上の [Db2onAzure](http://aka.ms/db2onazure) リポジトリにあります。 このファイルを使用する場合、お使いの環境で機能するように、事前にファイルを編集しておく必要があります。

**Db2 pureScale 応答ファイルのオプション**

| 画面の名前               | フィールド                                        | 値                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| ようこそ                   |                                              | 新規インストール                                                                                           |
| プロジェクトの選択          |                                              | Db2 バージョン 11.1.3.3。 Db2 pureScale を搭載した各サーバー エディション                                              |
| 構成             | Directory                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | インストールの種類を選択する                 | 通常                                                                                               |
|                           | IBM 利用規約に同意する                     | オン                                                                                               |
| インスタンス所有者            | インスタンスに対する既存ユーザー、ユーザー名        | Db2sdin1                                                                                              |
| フェンスされたユーザー               | 既存のユーザー、ユーザー名                     | Db2sdfe1                                                                                              |
| クラスター ファイル システム       | 共有ディスクのパーティション デバイス パス            | /dev/dm-2                                                                                             |
|                           | マウント ポイント                                  | /Db2sd\_1804a                                                                                         |
|                           | データ用の共有ディスク                         | /dev/dm-1                                                                                             |
|                           | マウント ポイント (データ)                           | /Db2fs/datafs1                                                                                        |
|                           | ログ用の共有ディスク                          | /dev/dm-0                                                                                             |
|                           | マウント ポイント (ログ)                            | /Db2fs/logfs1                                                                                         |
|                           | Db2 クラスター サービスのタイブレーカー。 デバイス パス | /dev/dm-3                                                                                             |
| ホスト一覧                 | d1 [eth1]、d2 [eth1]、cf1 [eth1]、cf2 [eth1] |                                                                                                       |
|                           | 推奨されるプライマリ CF                         | cf1                                                                                                   |
|                           | 推奨されるセカンダリ CF                       | cf2                                                                                                   |
| 応答ファイルと概要 | 最初のオプション                                 | IBM Db2 pureScale 機能を搭載した Db2 サーバー エディションをインストールして、応答ファイルに自分の設定を保存する |
|                           | 応答ファイル名                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>この展開に関するメモ

-   /dev-dm0、/dev-dm1、/dev-dm2、および /dev-dm3 の値は、セットアップが行われる仮想マシン (自動化されたスクリプトでは d0) 上でのリブート後に変更できます。 適切な値を見つけるために、セットアップを実行するサーバー上で応答ファイルを完成させる前に、次のコマンドを発行できます。

```
   [root\@d0 rhel]\# ls -als /dev/mapper
   total 0
   0 drwxr-xr-x 2 root root 140 May 30 11:07 .
   0 drwxr-xr-x 19 root root 4060 May 30 11:31 ..
   0 crw------- 1 root root 10, 236 May 30 11:04 control
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2data1 -\> ../dm-1
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2log1 -\> ../dm-0
   0 lrwxrwxrwx 1 root root 7 May 30 11:26 db2shared -\> ../dm-2
   0 lrwxrwxrwx 1 root root 7 May 30 11:08 db2tieb -\> ../dm-3
```

-   セットアップ スクリプトでは、実際の名前を簡単に見つけられるように、iSCSI ディスクのエイリアスを使用します。

-   d0 上でセットアップ スクリプトが実行された場合、d1、cf0、および cf1 上では **/dev/dm-\*** の値が異なる場合があります。 Db2 pureScale のセットアップでは、考慮しなくてもかまいません。

## <a name="troubleshooting-and-known-issues"></a>トラブルシューティングと既知の問題

GitHub リポジトリには、作成者によって管理されるナレッジ ベースがあります。 これには、遭遇する可能性がある潜在的な問題と、試行できる解決策が示されています。 たとえば、次のような場合に、既知の問題が発生することがあります。

-   ゲートウェイ IP アドレスへの到達を試行している。

-   GPL をコンパイルしている。

-   ホスト間のセキュリティ ハンドシェイクが失敗する。

-   Db2 インストーラーが既存のファイル システムを検出する。

-   GPFS を手動でインストールしている。

-   GPFS が既に作成されている場合に、Db2 pureScale をインストールしている。

-   Db2 pureScale と GPFS を削除している。

上記およびその他の既知の問題の詳細については、[Db2onAzure](http://aka.ms/Db2onAzure) リポジトリの kb.md ファイルをご覧ください。

## <a name="next-steps"></a>次の手順

-   [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)

-   [Db2 pureScale 機能のインストールに必要なユーザーを作成する](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [Db2icrt - インスタンス コマンドを作成する](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [Db2 pureScale クラスター データ ソリューション](http://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Platform Modernization Alliance (プラットフォーム最新化アライアンス): Azure 上の IBM Db2](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)

-   [Azure 仮想データ センターのリフト アンド シフト ガイド](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
