---
title: Azure HPC Cache (プレビュー) にストレージを追加する
description: Azure HPC Cache で長期的なファイルの保管にオンプレミス NFS システムまたは Azure BLOB コンテナーを使用できるようにストレージ ターゲットを定義する方法
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: rohogue
ms.openlocfilehash: dbcc68bacf8a11a7a85d5fad7fb4435fd03c7f93
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72024561"
---
# <a name="add-storage-targets"></a>ストレージ ターゲットを追加する

"*ストレージ ターゲット*" は、Azure HPC Cache インスタンスを通じてアクセスされるファイルのバックエンド ストレージです。 NFS ストレージ (オンプレミスのハードウェア システムなど) を追加できるほか、Azure BLOB にデータを格納することができます。

1 つのキャッシュに対して最大 10 個の異なるストレージ ターゲットを定義できます。 このキャッシュでは、すべてのストレージ ターゲットが 1 つの集約された名前空間で提供されます。

キャッシュの仮想ネットワークからストレージのエクスポートにアクセスできなければならないことに注意してください。 オンプレミスのハードウェア ストレージの場合、NFS ストレージへのアクセスに使用されるホスト名を解決できる DNS サーバーの設定が必要になることがあります。 詳細については、「[DNS アクセス](hpc-cache-prereqs.md#dns-access)」を参照してください。

キャッシュを作成した後で、ストレージ ターゲットを追加します。 この手順は、追加するのが Azure Blob Storage であるか NFS エクスポートであるかによって若干異なります。 それぞれの詳細を以下に示します。

## <a name="open-the-storage-targets-page"></a>ストレージ ターゲットのページを開く

Azure portal からキャッシュ インスタンスを開き、左側のサイド バーにある **[ストレージ ターゲット]** をクリックします。 ストレージ ターゲットのページには、既存のターゲットがすべて表示されるほか、新たに追加するためのリンクが表示されます。

![サイドバーの [ストレージ ターゲット] リンクのスクリーンショット。[設定] と [監視] というカテゴリ見出しの間にある [構成] という見出しの下にあります](media/hpc-cache-storage-targets-sidebar.png)

## <a name="add-a-new-azure-blob-storage-target"></a>新しい Azure Blob Storage ターゲットを追加する

新しい Blob Storage ターゲットには、空の BLOB コンテナーか、Azure HPC Cache クラウド ファイル システム フォーマットでデータが事前設定されているコンテナーが必要です。 BLOB コンテナーの事前読み込みの詳細については、[Azure Blob Storage へのデータの移動](hpc-cache-ingest.md)に関するページを参照してください。

Azure BLOB コンテナーを定義するには、次の情報を入力します。

![新しい Azure Blob Storage ターゲットの情報が事前設定されている [ストレージ ターゲットの追加] ページのスクリーンショット](media/hpc-cache-add-blob.png)

<!-- need to replace screenshot after note text is updated with both required RBAC roles and also with correct search term -->

* **[ストレージ ターゲット名]** - Azure HPC Cache 内でこのストレージ ターゲットを識別する名前を設定します。
* **[ターゲットの種類]** - **[BLOB]** を選択します。
* **[ストレージ アカウント]** - 参照するコンテナーを含んだアカウントを選択します。

  [アクセス ロールの追加](#add-the-access-control-roles-to-your-account)に関するセクションの説明に従って、ストレージ アカウントへのアクセスをキャッシュ インスタンスに承認する必要があります。

  使用できるストレージ アカウントの種類の詳細については、「[Blob Storage の要件](hpc-cache-prereqs.md#blob-storage-requirements)」を参照してください。

* **[ストレージ コンテナー]** - このターゲットの BLOB コンテナーを選択します。

* **[Virtual namespace path]\(仮想名前空間パス\)** - このストレージ ターゲットに使用するクライアント側のファイル パスを設定します。 仮想名前空間の機能の詳細については、「[集約された名前空間を構成する](hpc-cache-namespace.md)」を参照してください。

完了したら、 **[OK]** をクリックしてストレージ ターゲットを追加します。

### <a name="add-the-access-control-roles-to-your-account"></a>アクセス制御ロールをアカウントに追加する

Azure HPC Cache では、[ロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) を使用して、Azure Blob Storage ターゲットのストレージ アカウントへのアクセスをキャッシュ アプリケーションに承認します。

ストレージ アカウント所有者は、"HPC Cache リソース プロバイダー" ユーザーの[ストレージ アカウント共同作成者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor)ロールと[ストレージ BLOB データ共同作成者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)ロールを明示的に追加する必要があります。

この作業は事前に行えるほか、Blob Storage ターゲットを追加するページ上のリンクをクリックして行うこともできます。

RBAC ロールを追加する手順:

1. ストレージ アカウントの **[アクセス制御 (IAM)]** ページを開きます ( **[ストレージ ターゲットの追加]** ページのリンクをクリックすると自動的に、選択したアカウントについて、このページが表示されます)。

1. ページ上部の **+** をクリックして **[ロールの割り当てを追加する]** を選択します。

1. [ストレージ アカウント共同作成者] ロールを一覧から選択します。

1. **[アクセスの割り当て先]** フィールドでは、既定値を選択したままにしてください ("Azure AD のユーザー、グループ、サービス プリンシパル")。  

1. **[選択]** フィールドで、"hpc" を検索します。  この文字列は "HPC Cache リソース プロバイダー" という名前の 1 つのセキュリティ プリンシパルと一致するはずです。 そのプリンシパルをクリックして選択します。

   > [!NOTE]
   > "hpc" の検索が機能しない場合は、代わりに文字列 "storagecache" を使用してみてください。 早期にプレビューに参加したユーザーは、サービス プリンシパルに以前の名前を使用する必要がある可能性があります。

1. **[保存]** ボタンをクリックして、ロールの割り当てをストレージ アカウントに追加します。

1. このプロセスを繰り返して、"Storage Blob Data Contributor" というロールを割り当てます。  

![ロールの割り当ての追加の GUI のスクリーンショット](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>新しい NFS ストレージ ターゲットを追加する

NFS ストレージ ターゲットには、ストレージ エクスポートへの到達方法やそのデータを効率的にキャッシュする方法を指定するためにいくつか追加のフィールドがあります。 また、利用できるエクスポートが複数ある場合は、1 つの NFS ホストから複数の名前空間パスを作成することができます。

![NFS ターゲットが定義されている [ストレージ ターゲットの追加] ページのスクリーンショット](media/hpc-cache-add-nfs-target.png)

NFS を使用したストレージ ターゲットについて次の情報を入力します。

* **[ストレージ ターゲット名]** - Azure HPC Cache 内でこのストレージ ターゲットを識別する名前を設定します。

* **[ターゲットの種類]** - **[NFS]** を選択します。

* **[ホスト名]** - NFS ストレージ システムの IP アドレスまたは完全修飾ドメイン名を入力します (ドメイン名を使用するのは、名前を解決できる DNS サーバーにキャッシュからアクセスできる場合のみです)。

* **[使用モデル]** - ワークフローに基づいていずれかのデータ キャッシュ プロファイルを選択します ([後出の「使用モデルを選択する」](#choose-a-usage-model)を参照)。

### <a name="nfs-namespace-paths"></a>NFS 名前空間パス

各パスが同じストレージ システム上の別のエクスポートまたはサブディレクトリを表している限り、1 つの NFS ストレージ ターゲットに複数の仮想パスを使用できます。

1 つのストレージ ターゲットからすべてのパスを作成します。
<!-- You can create multiple namespace paths to represent different exports on the same NFS storage system, but you must create them all from one storage target. -->

各名前空間パスに対して、次の値を入力します。

* **[Virtual namespace path]\(仮想名前空間パス\)** - このストレージ ターゲットに使用するクライアント側のファイル パスを設定します。 仮想名前空間の機能の詳細については、「[集約された名前空間を構成する](hpc-cache-namespace.md)」を参照してください。

<!--  The virtual path should start with a slash ``/``. -->

* **[NFS export path]\(NFS エクスポート パス\)** - NFS エクスポートのパスを入力します。

* **[Subdirectory path]\(サブディレクトリ パス\)** - エクスポートの特定のサブディレクトリをマウントしたい場合は、ここに入力します。 それ以外の場合、このフィールドは空白のままにしてください。

完了したら、 **[OK]** をクリックしてストレージ ターゲットを追加します。

### <a name="choose-a-usage-model"></a>使用モデルを選択する
<!-- referenced from GUI - update aka.ms link if you change this heading -->

NFS ストレージ システムを指すストレージ ターゲットを作成するときは、そのターゲットの "*使用モデル*" を選択する必要があります。 どのようにデータがキャッシュされるかは、このモデルによって決まります。

* 読み取り (高負荷) - 主にデータの読み取りアクセスを高速化するためにキャッシュを使用する場合は、このオプションを選択します。

* 読み取り/書き込み - クライアントが読み取りと書き込みのためにキャッシュを使用する場合は、このオプションを選択します。

* クライアントはキャッシュをバイパス - クライアントがデータを最初にキャッシュに書き込むことをせず直接ストレージ システムに書き込む場合は、このオプションを選択します。

## <a name="next-steps"></a>次の手順

ストレージ ターゲットの作成後は、次のいずれかのタスクを検討してください。

* [Azure HPC Cache をマウントする](hpc-cache-mount.md)
* [Azure Blob Storage にデータを移動する](hpc-cache-ingest.md)
