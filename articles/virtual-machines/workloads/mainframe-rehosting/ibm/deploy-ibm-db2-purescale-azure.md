---
title: Azure 上に IBM DB2 pureScale をデプロイする
description: 最近、z/OS 上で実行される IBM DB2 環境から Azure 上の IBM DB2 pureScale に企業が移行する際に、あるアーキテクチャの例が使用されています。ここでは、そのアーキテクチャの例をデプロイする方法について説明します。
author: njray
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.topic: how-to
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 33ff6174d7e5107076dda177731c9daec7e57266
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104956147"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Azure 上に IBM DB2 pureScale をデプロイする

最近、z/OS 上で実行される IBM DB2 環境から Azure 上の IBM DB2 pureScale に企業のお客様が移行する際に、ある[アーキテクチャの例](ibm-db2-purescale-azure.md)が使用されています。この記事では、そのアーキテクチャの例をデプロイする方法について説明します。

移行の際に使用される手順に従うには、GitHub 上の [Db2onAzure](https://aka.ms/db2onazure) リポジトリ内にあるインストール スクリプトをご覧ください。 これらのスクリプトは、一般的な中規模サイズのオンライン トランザクション処理 (OLTP) ワークロード用のアーキテクチャに基づきます。

## <a name="get-started"></a>はじめに

このアーキテクチャをデプロイするには、GitHub 上の [DB2onAzure](https://aka.ms/db2onazure) リポジトリ内にある deploy.sh スクリプトをダウンロードして実行します。

このリポジトリには、Grafana ダッシュボードを設定するためのスクリプトも含まれています。 ダッシュボードを使用すると、DB2 に含まれているオープンソースの監視およびアラート システムである Prometheus に対してクエリを実行できます。

> [!NOTE]
> クライアント上の deploy.sh スクリプトは、プライベート SSH キーを作成して、HTTPS 経由で展開テンプレートにそのキーを渡します。 セキュリティを強化するために、シークレット、キー、およびパスワードの格納には、[Azure Key Vault](../../../../key-vault/general/overview.md) を使用することをお勧めします。

## <a name="how-the-deployment-script-works"></a>展開スクリプトの仕組み

deploy.sh スクリプトを実行すると、このアーキテクチャの Azure リソースが作成および構成されます。 スクリプトでは、ターゲット環境で使用される Azure サブスクリプションと仮想マシンの入力を要求してから、次の操作を実行します。

-   インストール用に、Azure 上にリソース グループ、仮想ネットワーク、およびサブネットを設定します。

-   環境に応じて、ネットワーク セキュリティ グループと SSH を設定します。

-   共有ストレージおよび DB2 pureScale 仮想マシンの両方に、複数の NIC を設定します。

-   共有ストレージ仮想マシンを作成します。 記憶域スペース ダイレクトまたは別のストレージ ソリューションを使用する場合は、「[記憶域スペース ダイレクトの概要](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)」を参照してください。

-   ジャンプボックス仮想マシンを作成します。

-   DB2 pureScale 仮想マシンを作成します。

-   DB2 pureScale から ping を実行する監視仮想マシンを作成します。 DB2 pureScale のご利用のバージョンで監視を必要としない場合は、デプロイのこの部分をスキップします。

-   テストに使用する Windows 仮想マシンを作成します。ただし、このマシン上には何もインストールしません。

次に、デプロイ スクリプトにより、Azure 上の共有ストレージに対して iSCSI 仮想記憶域ネットワーク (vSAN) が設定されます。 この例では、iSCSI が共有ストレージ クラスターに接続されます。 元の顧客ソリューションでは、GlusterFS が使用されていました。 ただし、IBM ではこのアプローチはサポートされなくなりました。 IBM からのサポートを維持するには、サポートされている iSCSI 互換ファイル システムを使用する必要があります。 Microsoft では、記憶域スペース ダイレクト (S2D) をオプションとして提供しています。

このソリューションは、単一の Windows ノードとして iSCSI ターゲットをインストールするオプションも提供します  iSCSI は TCP/IP 上で共有ブロック ストレージ インターフェイスを提供します。これにより、DB2 pureScale セットアップ手順で、デバイス インターフェイスを使用して、共有ストレージに接続できます。

通常、デプロイ スクリプトでは、次の手順が実行されます。

1.  Azure 上に共有ストレージ クラスターを設定します。 この手順を実行するには、2 つ以上の Linux ノードが必要です。

2.  共有ストレージ クラスター用のターゲット Linux サーバー上で iSCSI Direct インターフェイスを設定します。

3.  Linux 仮想マシン上で iSCSI イニシエーターを設定します。 イニシエーターでは、iSCSI ターゲットを使用して共有ストレージ クラスターへのアクセスが行われます。 セットアップの詳細については、RootUsers ドキュメントの「[How To Configure An iSCSI Target And Initiator In Linux (Linux 内で iSCSI ターゲットおよびイニシエーターを構成する方法)](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/)」をご覧ください。

4.  iSCSI インターフェイスの共有ストレージ層をインストールします。

スクリプトで iSCSI デバイスを作成したら、最後の手順として DB2 pureScale をインストールします。 DB2 pureScale のセットアップの一環として、[IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (以前は GPFS と呼ばれていた) がコンパイルされ、GlusterFS クラスター上にインストールされます。 このクラスター化されたファイル システムにより、DB2 pureScale では、DB2 pureScale エンジンを実行する仮想マシン間でデータを共有することが可能になります。 詳細については、IBM Web サイトの「[IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html)」をご覧ください。

## <a name="db2-purescale-response-file"></a>DB2 pureScale の応答ファイル

GitHub リポジトリには、DB2server.rsp という応答 (.rsp) ファイルが用意されています。このファイルを利用して、DB2 pureScale インストールのための自動化されたスクリプトを生成できます。 次の表に、応答ファイルでセットアップのために使用される DB2 pureScale オプションを示します。 必要に応じて、お使いの環境に合わせて応答ファイルをカスタマイズできます。

> [!NOTE]
> サンプル応答ファイル DB2server.rsp は、GitHub 上の [DB2onAzure](https://aka.ms/db2onazure) リポジトリにあります。 このファイルを使用する場合、お使いの環境で機能するように、事前にファイルを編集しておく必要があります。

| 画面の名前               | フィールド                                        | 値                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| ようこそ                   |                                              | 新規インストール                                                                                           |
| プロジェクトの選択          |                                              | DB2 バージョン 11.1.3.3。 DB2 pureScale を搭載した各サーバー エディション                                              |
| 構成             | ディレクトリ                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | インストールの種類を選択する                 | 通常                                                                                               |
|                           | IBM 利用規約に同意する                     | オン                                                                                               |
| インスタンス所有者            | インスタンスに対する既存ユーザー、ユーザー名        | DB2sdin1                                                                                              |
| フェンスされたユーザー               | 既存のユーザー、ユーザー名                     | DB2sdfe1                                                                                              |
| クラスター ファイル システム       | 共有ディスクのパーティション デバイス パス            | /dev/dm-2                                                                                             |
|                           | マウント ポイント                                  | /DB2sd\_1804a                                                                                         |
|                           | データ用の共有ディスク                         | /dev/dm-1                                                                                             |
|                           | マウント ポイント (データ)                           | /DB2fs/datafs1                                                                                        |
|                           | ログ用の共有ディスク                          | /dev/dm-0                                                                                             |
|                           | マウント ポイント (ログ)                            | /DB2fs/logfs1                                                                                         |
|                           | DB2 クラスター サービスのタイブレーカー。 デバイス パス | /dev/dm-3                                                                                             |
| ホスト一覧                 | d1 [eth1]、d2 [eth1]、cf1 [eth1]、cf2 [eth1] |                                                                                                       |
|                           | 推奨されるプライマリ CF                         | cf1                                                                                                   |
|                           | 推奨されるセカンダリ CF                       | cf2                                                                                                   |
| 応答ファイルと概要 | 最初のオプション                                 | IBM DB2 pureScale 機能を搭載した DB2 サーバー エディションをインストールして、応答ファイルに自分の設定を保存する |
|                           | 応答ファイル名                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>この展開に関するメモ

- /dev-dm0、/dev-dm1、/dev-dm2、および /dev-dm3 の値は、セットアップが行われる仮想マシン (自動化されたスクリプトでは d0) 上での再起動後に変更できます。 適切な値を見つけるには、セットアップを実行するサーバー上で応答ファイルが完了する前に、次のコマンドを発行します。

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

- セットアップ スクリプトでは、実際の名前を簡単に見つけられるように、iSCSI ディスクのエイリアスを使用します。

- d0 上でセットアップ スクリプトが実行された場合、d1、cf0、および cf1 上では **/dev/dm-\*** の値が異なる場合があります。 この値の違いは、DB2 pureScale のセットアップには影響しません。

## <a name="troubleshooting-and-known-issues"></a>トラブルシューティングと既知の問題

GitHub リポジトリには、作成者によって管理されるナレッジ ベースがあります。 これには、遭遇する可能性がある潜在的な問題と、試行できる解決策が示されています。 たとえば、既知の問題は、次の場合に発生することがあります。

-   ゲートウェイ IP アドレスへの到達を試行している。

-   一般公衆利用許諾書 (GPL) をコンパイルしようとしている。

-   ホスト間のセキュリティ ハンドシェイクが失敗する。

-   DB2 インストーラーで既存のファイル システムが検出された。

-   IBM Spectrum Scale を手動でインストールしようとしている。

-   IBM Spectrum Scale が既に作成されているのに DB2 pureScale をインストールしようとしている。

-   DB2 pureScale と IBM Spectrum Scale を削除しようとしている。

上記およびその他の既知の問題の詳細については、[DB2onAzure](https://aka.ms/DB2onAzure) リポジトリ内の kb.md ファイルをご覧ください。

## <a name="next-steps"></a>次のステップ

-   [DB2 pureScale 機能のインストールに必要なユーザーを作成する](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [DB2icrt - インスタンス コマンドを作成する](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [DB2 pureScale クラスター データ ソリューション](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Azure 仮想データ センターのリフト アンド シフト ガイド](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
