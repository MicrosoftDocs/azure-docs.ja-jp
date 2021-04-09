---
title: SAP HANA on Azure (L インスタンス) での HANA のインストール方法 | Microsoft Docs
description: SAP HANA on Azure (Large Instances) に HANA をインストールする方法。
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 59ea41254c16f32f19a67e7811177a4667a85e7d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666708"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (Large Instances) に HANA をインストールする

SAP HANA on Azure (Large Instances) に HANA をインストールするには、まず以下を行う必要があります。
- お客様に代わって SAP HANA L インスタンスをデプロイするためのすべてのデータを Microsoft に提供する。
- Microsoft から SAP HANA L インスタンスを受け取る。
- オンプレミス ネットワークに接続されている Azure 仮想ネットワークを作成する。
- HANA Large Instances の ExpressRoute 回線を同じ Azure 仮想ネットワークに接続する。
- HANA Large Instances のジャンプ ボックスとして使用する Azure 仮想マシンをインストールする。
- ジャンプ ボックスから HANA L インスタンス ユニットへの接続とその逆方向の接続が可能であることを確認する。
- 必要なパッケージとパッチがすべてインストールされているかどうかを確認する。
- 使用しているオペレーティング システムへの HANA のインストールに関する SAP ノートとドキュメントを読む。 そのオペレーティング システム リリースで、選択した HANA リリースがサポートされていることを確認する。

次のセクションでは、HANA インストール パッケージをジャンプ ボックス仮想マシンにダウンロードする例を紹介します。 この例では、オペレーティング システムは Windows です。

## <a name="download-the-sap-hana-installation-bits"></a>SAP HANA インストール ビットのダウンロード
HANA L インスタンス ユニットは、インターネットに直接接続されません。 SAP から HANA L インスタンス仮想マシンにインストール パッケージを直接ダウンロードすることはできません。 その代わり、ジャンプ ボックス仮想マシンにパッケージをダウンロードします。

SAP S ユーザーなどのユーザーに、SAP Marketplace にアクセスできるようにしてもらう必要があります。

1. [SAP Service Marketplace](https://support.sap.com/en/index.html) にサインインしてアクセスします。 **[Download Software]\(ソフトウェアのダウンロード\)**  >  **[Installations and Upgrade]\(インストールとアップグレード\)**  >  **[By Alphabetical Index]\(アルファベット インデックス順\)** を選択します。 **[Under H - SAP HANA Platform Edition]\(H の SAP HANA Platform Edition\)**  >  **[SAP HANA Platform Edition 2.0]**  >  **[Installation]\(インストール\)** の順に選択します。 次のスクリーンショットに示されているファイルをダウンロードします。

   ![ダウンロードするファイルのスクリーンショット](./media/hana-installation/image16_download_hana.PNG)

2. この例では、SAP HANA 2.0 インストール パッケージをダウンロードしました。 Azure ジャンプ ボックス仮想マシンで、以下に示すとおり自己解凍形式のアーカイブをディレクトリに展開します。

   ![自己解凍形式のアーカイブのスクリーンショット](./media/hana-installation/image17_extract_hana.PNG)

3. アーカイブを抽出したら、抽出によって作成されたディレクトリ (この例では 51052030) をコピーします。 作成したディレクトリに、HANA L インスタンス ユニットの /hana/shared ボリュームのディレクトリをコピーします。

   > [!Important]
   > インストール パッケージをルートまたはブート LUN にコピーしないでください。領域が限られており、他のプロセスでも使用する必要があるためです。


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>HANA L インスタンス ユニットへの SAP HANA のインストール
SAP HANA をインストールするために、ルート ユーザーとしてサインインします。 SAP HANA をインストールための十分なアクセス許可を持つのはルートだけです。 /hana/shared にコピーしたディレクトリに対するアクセス許可を設定します。

```
chmod –R 744 <Installation bits folder>
```

グラフィカル ユーザー インターフェイス セットアップを使用して SAP HANA をインストールしたい場合は、HANA Large Instances に gtk2 パッケージがインストールされている必要があります。 インストールされているかどうかを確認するには、次のコマンドを実行します。

```
rpm –qa | grep gtk2
```

(以降の手順では、グラフィカル ユーザー インターフェイスを使用して SAP HANA のセットアップについて説明します。)

インストール ディレクトリに移動し、サブディレクトリ HDB_LCM_LINUX_X86_64 に移動します。 

そのディレクトリから開始します。

```
./hdblcmgui 
```
ここから一連の画面に従って進み、インストールのデータを指定します。 この例では、SAP HANA データベース サーバーと SAP HANA クライアント コンポーネントをインストールします。 したがって、**[SAP HANA Database]\(SAP HANA データベース\)** を選択します。

![SAP HANA ライフサイクル管理画面のスクリーンショット ([SAP HANA Database]\(SAP HANA データベース\) を選択)](./media/hana-installation/image18_hana_selection.PNG)

次の画面で、**[Install New System]\(新しいシステムのインストール\)** を選択します。

![SAP HANA ライフサイクル管理画面のスクリーンショット ([Install New System]\(新しいシステムのインストール\) を選択)](./media/hana-installation/image19_select_new.PNG)

次に、インストールできるいくつかの追加コンポーネントを選択します。

![SAP HANA ライフサイクル管理画面のスクリーンショット (追加コンポーネントの一覧)](./media/hana-installation/image20_select_components.PNG)

ここでは、SAP HANA Client と SAP HANA Studio を選択します。 また、スケールアップ インスタンスもインストールします。 次に、**[Single-Host System]\(シングルホスト システム\)** を選択します。 

![SAP HANA ライフサイクル管理画面のスクリーンショット ([Single-Host System]\(シングルホスト システム\) を選択)](./media/hana-installation/image21_single_host.PNG)

次に、いくつかのデータを入力します。

![SAP HANA ライフサイクル管理画面のスクリーンショット (定義するシステム プロパティ フィールド)](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> HANA システム ID (SID) として、HANA L インスタンスのデプロイを注文したときに Microsoft に提供したものと同じ SID を入力する必要があります。 別の SID を選択すると、さまざまなボリュームでのアクセス許可の問題が原因でインストールが失敗します。

インストール パスには、/hana/shared ディレクトリを使用します。 次の手順では、HANA データ ファイルと HANA ログ ファイルの場所を指定します。


![SAP HANA ライフサイクル管理画面のスクリーンショット (データ エリアとログ エリアのフィールド)](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> システム プロパティを定義するとき (2 つ前の画面) に指定した SID は、マウント ポイントの SID と一致している必要があります。 一致していない場合は、前に戻って、マウント ポイントの SID 値に調整します。

次に、ホスト名を確認し、最終的な修正を行います。 

![SAP HANA ライフサイクル管理画面のスクリーンショット (ホスト名)](./media/hana-installation/image24_review_host_name.PNG)

次に、HANA L インスタンスのデプロイメントを注文したときに Microsoft に提供したデータも取得する必要があります。 

![SAP HANA ライフサイクル管理画面のスクリーンショット (定義するシステム管理者フィールド)](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> ユニットのデプロイを注文するときに Microsoft に提供したものと同じ **システム管理者ユーザー ID** と **ユーザー グループの ID** を入力する必要があります。 それ以外の場合、HANA L インスタンス ユニットへの SAP HANA のインストールは失敗します。

この後に続く 2 つの画面は、ここでは示しません。 それらでは、SAP HANA データベースの SYSTEM ユーザーのパスワードと、sapadm ユーザーのパスワードを入力することができます。 後者は、SAP HANA データベース インスタンスの一部としてインストールされる SAP Host Agent で使用されます。

パスワードを定義すると確認画面が表示されます。 表示されているすべてのデータを確認し、インストールを続行します。 次のように、インストールの進行状況を示す進行状況画面が表示されます。

![SAP HANA ライフサイクル管理画面のスクリーンショット (インストールの進行状況を示す画面)](./media/hana-installation/image27_show_progress.PNG)

インストールが終了すると、次のような画面が表示されます。

![SAP HANA ライフサイクル管理画面のスクリーンショット (インストールの完了を示す画面)](./media/hana-installation/image28_install_finished.PNG)

SAP HANA インスタンスが起動して稼働状態になり、使用する準備が整いました。 SAP HANA インスタンスには、SAP HANA Studio から接続することもできます。 また、最新の更新プログラムを必ず確認して適用してください。


## <a name="next-steps"></a>次のステップ

- [Azure での SAP HANA Large Instances の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md)

