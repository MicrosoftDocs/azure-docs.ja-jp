---
title: Avere vFXT ストレージを構成する - Azure
description: Avere vFXT for Azure のクラスターにバックエンド ストレージ システムを追加する方法について説明します。 クラスターと共に Azure Blob コンテナーを作成している場合は、それはすぐに使用できます。
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: c17d3c7cd2cf6fe5bca725cf94344b2d2cb50bf2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001526"
---
# <a name="configure-storage"></a>記憶域を構成する

この手順では、vFXT クラスター用にバックエンド ストレージ システムを設定します。

> [!TIP]
> Avere vFXT クラスターと共に新しい Azure BLOB コンテナーを作成した場合、そのコンテナーは既に構成され、使用する準備が整っています。

クラスターと共に新しい BLOB コンテナーを作成しなかった場合や、ハードウェアまたはクラウド ベースのストレージ システムを新しく追加する場合は、以下の手順に従ってください。

主要なタスクは次の 2 つです。

1. [コア ファイラーを作成する](#create-a-core-filer): ご利用の vFXT クラスターを既存のストレージ システムまたは Azure Storage アカウント コンテナーに接続します。

1. [名前空間のジャンクションを作成する](#create-a-junction): クライアントがマウントするパスを定義します。

これらの手順では、Avere Control Panel を使用します。 その使用方法については、「[vFXT クラスターへのアクセス](avere-vfxt-cluster-gui.md)」をお読みください。

## <a name="create-a-core-filer"></a>コア ファイラーを作成する

"コア ファイラー" とは、バックエンド ストレージ システムを表す vFXT の用語です。 ストレージは、NetApp や Isilon などのハードウェア NAS アプライアンスでも、クラウド オブジェクト ストアでもかまいません。 コア ファイラーの詳細については、[Avere のクラスター設定ガイド](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers)をご覧ください。

コア ファイラーを追加するには、コア ファイラーの 2 つの主な種類のいずれかを選択します。

* [NAS コア ファイラー](#nas-core-filer) - NAS コア フィルターを追加する方法について説明します
* [Azure Storage クラウド コア ファイラー](#azure-blob-storage-cloud-core-filer) - Azure Blob Storage コンテナーをクラウド コア ファイラーとして追加する方法について説明します

### <a name="nas-core-filer"></a>NAS コア ファイラー

NAS コア ファイラーとしては、オンプレミスの NetApp もしくは Isilon アプライアンス、またはクラウド内の NAS エンドポイントを使用できます。 ストレージ システムは、Avere vFXT クラスターに対する信頼性の高い高速接続 (たとえば、1 GBps の ExpressRoute 接続 (VPN ではなく)) を備え、使用される NAS エクスポートにクラスター ルート アクセスを提供する必要があります。

NAS コア フィルターを追加するには、次の手順に従います。

1. Avere Control Panel の上部にある **[Settings]\(設定\)** タブをクリックします。

1. 左側で **[Core Filer]\(コア ファイラー\)**  >  **[Manage Core Filers]\(コア ファイラーの管理\)** をクリックします。

1. **Create** をクリックしてください。

   ![カーソルが [作成] ボタンの上にある [Add new core filer]\(新しいコア ファイラーの追加\) ページのスクリーンショット](media/avere-vfxt-add-core-filer-start.png)

1. ウィザードで必要な情報を入力します。

   * コア ファイラーの名前を指定します。
   * ある場合は、完全修飾ドメイン名 (FQDN) を指定します。 それ以外の場合は、コア ファイラーに解決される IP アドレスまたはホスト名を指定します。
   * 一覧からファイラー クラスを選択します。 わからない場合は、**[その他]** を選択します。

     ![コア ファイラー名とその完全修飾ドメイン名が入力されている新規コア ファイラー追加ページのスクリーンショット](media/avere-vfxt-add-core-filer.png)
  
   * **[Next]\(次へ\)** をクリックして、キャッシュ ポリシーを選択します。
   * **[Add Filer]\(ファイラーの追加\)** をクリックします。
   * 詳しくは、Avere クラスター設定ガイドの「[Adding a new NAS core filer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html)」(新しい NAS コア ファイラーの追加) をご覧ください。

次に、「[ジャンクションを作成する](#create-a-junction)」に進みます。  

### <a name="azure-blob-storage-cloud-core-filer"></a>Azure Blob Storage クラウド コア フィルター

vFXT クラスターのバックエンド ストレージとして Azure Blob Storage を使用するには、コア ファイラーとして追加する空のコンテナーが必要です。

Blob Storage をクラスターに追加するには、次のタスクが必要です。

* ストレージ アカウントを作成します (後のステップ 1)
* 空の BLOB コンテナーを作成します (ステップ 2 から 3)
* vFXT クラスターのクラウド資格情報としてストレージ アクセス キーを追加します (ステップ 4 から 6)
* vFXT クラスターに対するコア ファイラーとして BLOB コンテナーを追加します (ステップ 7 から 9)
* クライアントがコア ファイラーのマウントに使用する名前空間ジャンクションを作成します (「[ジャンクションを作成する](#create-a-junction)」、ハードウェア ストレージもクラウド ストレージも同じ)

> [!TIP]
> Avere vFXT for Azure クラスターの作成時に新しい BLOB コンテナーを作成する場合は、デプロイ テンプレートによって、コンテナーがコア フィルターとして自動的に構成されます  (これは、要求に応じて利用可能な作成スクリプトを使用する場合にも当てはまります)。コア フィルターを後で構成する必要はありません。
>
> クラスター作成ツールでは、次の構成タスクが自動的に実行されます。
>
> * 指定されたストレージ アカウントに新しい BLOB コンテナーを作成する
> * コンテナーをコア フィルターとして定義する
> * コンテナーへの名前空間ジャンクションを作成する
> * クラスターの仮想ネットワーク内部にストレージ サービス エンドポイントを作成する

クラスターを作成した後で Blob Storage を追加するには、次の手順のようにします。

1. 以下の設定で汎用 V2 ストレージ アカウントを作成します。

   * **サブスクリプション** - vFXT クラスターと同じ
   * **リソース グループ** - vFXT クラスター グループと同じ (省略可能)
   * **場所** - vFXT クラスターと同じ
   * **パフォーマンス** - Standard (Premium Storage はサポートされていません)
   * **アカウントの種類** - 汎用 V2 (StorageV2)
   * **レプリケーション** - ローカル冗長ストレージ (LRS)
   * **アクセス層** - ホット
   * **安全な転送が必須** - 無効 (非既定値)
   * **仮想ネットワーク** - 不要

   Azure portal を使用するか、または下の [Azure へのデプロイ] ボタンをクリックして、作成できます。

   [![ストレージ アカウントを作成するためのボタン](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. アカウントが作成された後、ストレージ アカウントのページを参照します。

   ![Azure portal の新しいストレージ アカウント](media/avere-vfxt-new-storage-acct.png)

1. 新しい BLOB コンテナーを作成する: 概要ページで **[コンテナー]** をクリックして、 **[+ コンテナー]** をクリックします。 任意のコンテナー名を使用し、アクセスが **[プライベート]** に設定されていることを確認します。

   ![[+ コンテナー] ボタンが丸で囲まれ、ポップアップ ページに新しいコンテナーが作成されているストレージ BLOB ページ](media/avere-vfxt-new-blob.png)

1. **[設定]** の **[アクセス キー]** をクリックして、Azure Storage アカウント キーを取得します。 提供されたキーのいずれかをコピーします。

   ![キーをコピーするための Azure portal GUI](media/avere-vfxt-copy-storage-key.png)

1. 自分のクラスターの Avere Control Panel を開きます。 **[設定]** をクリックし、左側のナビゲーション ウィンドウで **[クラスター]** > **[Cloud Credentials]\(クラウド資格情報\)** を開きます。 [Cloud Credentials]\(クラウド資格情報\) ページで、**[Add Credential]\(資格情報の追加\)** をクリックします。

   ![[Cloud Credentials]\(クラウド資格情報\) 構成ページの [Add Credential]\(資格情報の追加\) ボタンをクリック](media/avere-vfxt-new-credential-button.png)

1. 次の情報を入力して、クラウド コア ファイラーの資格情報を作成します。

   | フィールド | 値 |
   | --- | --- |
   | Credential name (資格情報名) | 任意のわかりやすい名前 |
   | サービスの種類 | (Azure Storage アクセス キーを選択) |
   | Tenant | ストレージ アカウント名 |
   | サブスクリプション | サブスクリプション ID |
   | Storage Access Key (ストレージ アクセス キー) | Azure ストレージ アカウント キー (前のステップでコピーしたもの) |

   **[送信]** をクリックします。

   ![Avere Control Panel の設定が終わった資格情報フォーム](media/avere-vfxt-new-credential-submit.png)

1. 次に、コア ファイラーを作成します。 Avere Control Panel の左側で、 **[Core Filer]\(コア ファイラー\)**  >   **[Manage Core Filers]\(コア ファイラーの管理\)** をクリックします。

1. **[Manage Core Filers]\(コア ファイラーの管理\)** 設定ページの **[作成]** ボタンをクリックします。

1. ウィザードで次のように設定します。

   * ファイラーの種類で **[クラウド]** を選択します。
   * 新しいコア ファイラーの名前を指定して、**[Next]\(次へ\)** をクリックします。
   * 既定のキャッシュ ポリシーを受け入れ、3 ページ目に進みます。
   * **[Service type]\(サービスの種類\)** で **[Azure Storage]** を選択します。
   * 前に作成した資格情報を選択します。
   * **[Bucket contents]\(バケットの内容\)** を **[空]** に設定します
   * **[Certificate verification]\(証明書の検証\)** を **[無効]** に変更します
   * **[Compression mode]\(圧縮モード\)** を **[None]\(なし\)** に変更します
   * **[次へ]** をクリックします。
   * 4 ページ目では、コンテナーの名前を **[Bucket name]\(バケット名\)** に「<*ストレージ アカウント名*>/<*コンテナー名*>」の形式で入力します。
   * 必要に応じて、**[Encryption type]\(暗号化の種類\)** を **[None]\(なし\)** に設定します。  Azure Storage は既定で暗号化されます。
   * **[Add Filer]\(ファイラーの追加\)** をクリックします。

   詳しくは、Avere クラスター構成ガイドの「[Adding a new cloud core filer](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>)」(新しいクラウド コア ファイラーの追加) をご覧ください。

ページが更新されるか、または手動でページを更新することもでき、新しいコア ファイラーが表示されます。

次に、[ジャンクションを作成する](#create-a-junction)必要があります。

## <a name="create-a-junction"></a>ジャンクションを作成する

ジャンクションは、クライアント用に作成するパスです。 クライアントは、パスをマウントして、ユーザーが選択したターゲットに到達します。

たとえば、`/vfxt/files` を作成して、NetApp コア ファイラーの `/vol0/data` エクスポートと `/project/resources` サブディレクトリにマップできます。

ジャンクションについて詳しくは、[Avere クラスター構成ガイドの名前空間セクション](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html)をご覧ください。

Avere Control Panel インターフェイスで次の手順を行います。

* 左上の **[VServer]**  >  **[名前空間]** をクリックします。
* / (スラッシュ) で始まる名前空間パスを指定します (例: ``/vfxt/data``)。
* コア ファイラーを選択します。
* コア ファイラーのエクスポートを選択します。
* **[次へ]** をクリックします。

  ![ジャンクション、コア ファイラー、エクスポートのフィールドが設定された "新規ジャンクション追加" ページのスクリーンショット](media/avere-vfxt-add-junction.png)

数秒後にジャンクションが表示されます。 必要に応じて、さらにジャンクションを作成します。

ジャンクションが作成されると、クライアントは名前空間パスを使用して、ストレージ システムからのファイルにアクセスします。

## <a name="next-steps"></a>次のステップ

* [Avere vFXT クラスターをマウントする](avere-vfxt-mount-clients.md)
* [新しい BLOB コンテナーにデータを移動する](avere-vfxt-data-ingest.md)効率的な方法について説明する
