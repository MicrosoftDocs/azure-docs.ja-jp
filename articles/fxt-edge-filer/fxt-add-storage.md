---
title: 'チュートリアル: Azure FXT Edge Filer クラスターにストレージを追加する'
description: バックエンド ストレージとクライアント側の擬似名前空間を Azure FXT Edge Filer 用に構成する方法
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 7d10c6c1ce440b2ffe964dc78379ef3ab108e78e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92217526"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>チュートリアル: バックエンド ストレージを追加して仮想名前空間を構成する

このチュートリアルでは、キャッシュ用のバックエッジ ストレージを追加する方法と、クライアント側の仮想ファイル システムをセットアップする方法について説明します。

クラスターはバックエンド ストレージ システムに接続してデータ クライアントの要求にアクセスし、キャッシュよりも永続的な方法で変更を保存します。

名前空間は、クライアント側のワークフローを変更せずにバックエンド ストレージを交換できるようにする、クライアント側の疑似ファイル システムです。

このチュートリアルでは、次の事項について説明します。

> [!div class="checklist"]
>
> * バックエンド ストレージを Azure FXT Edge Filer クラスターに追加する方法
> * ストレージのクライアント側パスを定義する方法

## <a name="about-back-end-storage"></a>バックエンド ストレージについて

Azure FXT Edge Filer クラスターでは、*コア ファイラー* 定義を使用して、バックエンド ストレージ システムを FXT クラスターにリンクします。

Azure FXT Edge Filer は複数の一般的な NAS ハードウェア システムと互換性があり、Azure Blob または他のクラウド ストレージの空のコンテナーを使用できます。

クラウド ストレージ ボリューム上のすべてのデータを FXT オペレーティング システムが完全に管理できるよう、クラウド ストレージ コンテナーは空の状態で追加する必要があります。 コンテナーをコア ファイラーとしてクラスターに追加した後に、既存のデータをクラウド コンテナーに移動できます。

コア ファイラーをシステムに追加するには、コントロール パネルを使用します。

> [!NOTE]
>
> Amazon AWS または Google Cloud ストレージを使用する場合、FlashCloud<sup>TM</sup> 機能ライセンスをインストールする必要があります。 Microsoft の担当者に問い合わせてライセンス キーを入手した後、従来の構成ガイドにある[機能ライセンスの追加または削除](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses)に関する指示に従います。
>
> Azure Blob ストレージのサポートは、Azure FXT Edge Filer ソフトウェア ライセンスに含まれています。

コア ファイラーの追加の詳細については、クラスター構成ガイドの以下のセクションを参照してください。

* コア ファイラーを追加するための選択と準備の詳細については、[コア ファイラーの操作](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview)に関するページをお読みください。
* 詳細な前提条件および手順説明については、以下の記事を参照してください。

  * [Adding a New NAS Core Filer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas) (新しい NAS コア ファイラーの追加)
  * [Adding a New Cloud Core Filer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud) (新しいクラウド コア ファイラーの追加)

コア ファイラーを追加した後、[Core Filer Details] (コア ファイラーの詳細) 設定ページでその設定を更新できます。

## <a name="add-a-core-filer"></a>コア ファイラーを追加する

**[Core Filer] \(コア ファイラー\)** > **[Manage Core Filers] \(コア ファイラーの管理\)** 設定ページにある **[作成]** ボタンをクリックしてコア ファイラーを定義しします。

![[Manage Core Filers] (コア ファイラーの管理) ページで、コア ファイラーの一覧の上にある作成ボタンをクリック](media/fxt-cluster-config/create-core-filer-button.png)

**[Add New Core Filer] \(新しいコア ファイラーの追加\)** ウィザードは、バックエンド ストレージにリンクするコア ファイラーを作成するプロセスを案内します。 クラスター構成ガイドでプロセスの手順を説明していますが、NFS/NAS ストレージの場合とクラウド ストレージの場合で手順が異なります (リンクは上にあります)。

サブタスクには次のものが含まれます。

* コア ファイラーの種類 (NAS またはクラウド) を指定する

  ![ハードウェア NAS の新規コア ファイラー ウィザードの最初のページ。 "クラウド コア ファイラー" のオプションが無効になっており、ライセンスが存在しないというエラー メッセージが表示される。](media/fxt-cluster-config/new-nas-1.png)

* コア ファイラーの名前を設定します。 それが表しているストレージ システムがクラスター管理者に理解しやすい名前を選びます。

* NAS コア ファイラーの場合、完全修飾ドメイン名 (FQDN) または IP アドレスを指定します。 FQDN はすべてのコア ファイラーに対して推奨され、SMB アクセスに必須です。

* キャッシュ ポリシーを選択する - ウィザードの 2 ページ目では、新しいコア ファイラーで利用できるキャッシュ ポリシーが一覧表示されます。 詳細については、[クラスター構成ガイドのキャッシュ ポリシーに関するセクション](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html)をお読みください。

  ![ハードウェア NAS の新規コア ファイラー ウィザードの 2 ページ目。[Cache Policy] (キャッシュ ポリシー) ドロップダウン メニューを開くと 3 つのキャッシュ ポリシー オプション (バイパス、読み取りキャッシュ、読み取り/書き込みキャッシュ) が有効であり、その他のオプションは無効。](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* クラウド ストレージの場合、パラメーターの中でも特に、クラウド サービスとアクセス資格情報を指定する必要があります。 詳細については、クラスター構成ガイドの[クラウド サービスとプロトコル](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol)に関する項目をお読みください。

  ![新規コア ファイラー ウィザードのクラウド コア ファイラー情報](media/fxt-cluster-config/new-core-filer-cloud3.png)
  
  このクラスターに対してクラウドのアクセス資格情報を追加済みの場合、一覧に表示されます。 **[クラスター]**  >  **[Cloud Credentials] \(クラウドの資格情報\)** 設定ページで資格情報を更新および追加します。

ウィザードですべての必要な設定を入力したら、**[Add Filer] \(ファイラーの追加\)** ボタンをクリックして変更内容を送信します。

しばらくすると、ストレージ システムが **[ダッシュボード]** でコア ファイラーの一覧に表示され、コア ファイラーの設定ページからアクセスできるようになります。

![[Manage Core Filers] (コア ファイラーの管理) 設定ページで "Flurry-NAS" コア ファイラーの詳細ビューを展開した状態](media/fxt-cluster-config/core-filer-in-manage-page.png)

このスクリーンショットのコア ファイラーには VServer がありません。 クライアントがストレージにアクセスできるよう、コア ファイラーを VServer にリンクしてジャンクションを作成する必要があります。 これらの手順については、次の「[名前空間を構成する](#configure-the-namespace)」で説明します。

## <a name="configure-the-namespace"></a>名前空間を構成する

Azure FXT Edge Filer クラスターは、*クラスター名前空間* と呼ばれる仮想ファイル システムを作成します。これは、さまざまなバックエンド システムに格納されているデータへのクライアント アクセスを簡素化します。 クライアントは仮想パスを使用してファイルを要求するので、クライアントのワークフローを変更しなくてもストレージ システムを追加または交換できます。

クラスター名前空間を使用すると、クラウドと NAS の各ストレージ システムを同じようなファイル構造で表現することもできます。

クラスターの VServer は名前空間を管理し、クライアントにコンテンツを供給します。 クラスター名前空間を作成するための 2 つの手順があります。

1. VServer を作成する
1. バックエンド ストレージ システムとクライアント側のファイル システム パスの間にジャンクションをセットアップする

### <a name="create-a-vserver"></a>VServer を作成する

VServer は、クライアントと、クラスターのコア ファイラーとの間でデータ フローを制御する仮想ファイル サーバーです。

* VServer はクライアント側の IP アドレスをホストします
* VServer は名前空間を作成し、クライアント側の仮想ディレクトリ構造をバックエンド ストレージ上のエクスポートにマップするジャンクションを定義します
* VServer は、コア ファイラーのエクスポート ポリシーやユーザー認証システムなどのファイル アクセス制御を実施します
* VServer は SMB インフラストラクチャを提供します

クラスター VServer の構成を始める前に、リンク先のドキュメントをお読みください。名前空間や VServer について理解が難しい点があれば、Microsoft の担当者にお問い合わせください。 VLAN を使用する場合、VServer を作成する前に [VLAN を作成](fxt-configure-network.md#adjust-network-settings)します。

クラスター構成ガイドの以下のセクションは、FXT VServer とグローバル名前空間の各機能について理解するために役立ちます。

* [Creating and Working with VServers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers) (VServer の作成と操作)
* [Using a Global Namespace](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) (グローバル名前空間の使用)
* [Creating a VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) (VServer の作成)

クラスターには少なくとも 1 つの VServer が必要です。

新しい VServer を作成するには、次の情報が必要です。

* VServer に設定する名前

* VServer が処理するクライアント側 IP アドレスの範囲

  VServer を作成するときは、連続した IP アドレスの単一の範囲を指定する必要があります。 アドレスは後から **[Client Facing Network] \(クライアント側のネットワーク\)** 設定ページを使用して追加できます。

* ネットワークに VLAN がある場合、どの VLAN をこの VServer に使用するか

**[VServer]**  >  **[Manage VServers] \(VServer の管理\)** 設定ページを使用して新しい vserver を作成します。 詳細については、クラスター構成ガイドの [VServer の作成](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)に関する項目をお読みください。

![新しい VServer を作成するためのポップアップ ウィンドウ](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>ジャンクションを作成する

*ジャンクション* は、バックエンド ストレージのパスを、クライアントが認識できる名前空間にマップします。

このシステムを使用すれば、複数のコア ファイラーからのストレージを 1 つの仮想パスに収容できるため、クライアント マウント ポイントで使用されるパスを単純化し、シームレスに容量を拡張することができます。

![[Add New Junction] (新しいジャンクションの追加) ウィザード ページで設定を入力した状態](media/fxt-cluster-config/add-junction-full.png)

名前空間ジャンクションの作成の詳細については、クラスター構成ガイドの [**VServer** > **名前空間**](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html)に関する項目を参照してください。

![ジャンクションの詳細を表示する [VServer] > [名前空間] 設定ページ](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>エクスポート規則を構成する

VServer とコア ファイラーの両方が揃ったら、コア ファイラー エクスポート上のファイルにクライアントがどのようにアクセスできるかを制御するエクスポート規則とエクスポート ポリシーをカスタマイズする必要があります。

まず **[VServer]**  >  **[Export Rules] \(エクスポート規則\)** ページを使用して、新しい規則を追加するか、既定のポリシーを変更するか、または独自のカスタム エクスポート ポリシーを作成します。

次に、 **[VServer]**  >  **[ポリシーのエクスポート]** ページを使用して、その VServer からのアクセス時に、カスタマイズしたポリシーをコア ファイラーのエクスポートに適用します。

詳細については、クラスター構成ガイドにある、[コア ファイラーのエクスポートのアクセス制御](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html)に関するページをお読みください。

## <a name="next-steps"></a>次の手順

ストレージを追加し、クライアント側の名前空間を構成した後、クラスターの初期セットアップを完了します。 

> [!div class="nextstepaction"]
> [クラスターのネットワーク設定を構成する](fxt-configure-network.md)
