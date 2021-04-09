---
title: Azure Automation で Change Tracking と Inventory を管理する
description: この記事では、Change Tracking と Inventory を使用して、お使いの環境内でソフトウェアと Microsoft サービスの変更を追跡する方法について説明します。
services: automation
ms.subservice: change-inventory-management
ms.date: 12/10/2020
ms.topic: conceptual
ms.openlocfilehash: dff314f3c9fb72c565a7c2d522694d533c487895
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572644"
---
# <a name="manage-change-tracking-and-inventory"></a>Change Tracking と Inventory の管理

追跡対象の新しいファイルまたはレジストリ キーを追加すると、Azure Automation によって、[Change Tracking とインベントリ](overview.md)に対してそれが有効になります。 この記事では、トラッキングを構成し、トラッキングの結果を確認して、変更の検出時にアラートを処理する方法について説明します。

この記事の手順を使用する前に、これらのいずれかの方法を使用して、VM で Change Tracking とインベントリを有効にしておいてください。

* [Automation アカウントで Change Tracking とインベントリを有効にする](enable-from-automation-account.md)
* [Azure portal を参照して Change Tracking とインベントリを有効にする](enable-from-portal.md)
* [Runbook で Change Tracking とインベントリを有効にする](enable-from-runbook.md)
* [Azure VM で Change Tracking とインベントリを有効にする](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>デプロイのスコープを制限する

Change Tracking とインベントリでは、ワークスペース内のスコープ構成を使用して、変更の対象となるコンピューターを指定します。 詳細については、「[Change Tracking とインベントリのデプロイのスコープを制限する](manage-scope-configurations.md)」を参照してください。

## <a name="track-files"></a>ファイルを追跡する

Change Tracking とインベントリを使用して、ファイルとフォルダー (ディレクトリ) への変更を追跡できます。 このセクションでは、Windows および Linux でのファイル トラッキングを構成する方法について説明します。

### <a name="configure-file-tracking-on-windows"></a>Windows でファイル追跡を構成する

次の手順を使用して、Windows コンピューターでのファイル追跡を構成します。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. Azure Portal で **[すべてのサービス]** を選択します。 リソースの一覧で、「**Automation**」と入力します。 入力を始めると、入力内容に基づいて一覧から候補が絞り込まれます。 **[Automation アカウント]** を選択します。

3. Automation アカウントの一覧で、変更履歴とインベントリを有効にしたときに選択したアカウントを選択します。

4. Automation アカウントで、 **[構成管理]** の **[Change Tracking]\(変更の追跡\)** を選択します。

5. **[設定の編集]** (歯車のシンボル) を選択します。

6. [ワークスペースの構成] ページで **[Windows ファイル]** を選択し、次に **[+ 追加]** をクリックして、追跡する新しいファイルを追加します。

7. [変更履歴用の Windows ファイルを追加する] ペインで、追跡対象のファイルまたはフォルダーの情報を入力して、 **[保存]** をクリックします。 次の表に、情報に使用できるプロパティを示します。

    |プロパティ  |説明  |
    |---------|---------|
    |Enabled     | 設定が適用される場合は True、それ以外の場合は False。        |
    |Item Name     | 追跡するファイルのフレンドリ名。        |
    |グループ     | ファイルを論理的にグループ化するためのグループ名。        |
    |パスの入力     | ファイルを確認するパス (例: **c:\temp\\\*.txt**)。 `%winDir%\System32\\\*.*` などの環境変数も使用できます。       |
    |パスの種類     | パスの種類。 指定できる値は [ファイル] と [フォルダー] です。        |    
    |再帰     | 追跡する項目を検索するときに、再帰を使用する場合は True、そうでない場合は False。        |    
    |ファイル コンテンツのアップロード | 追跡された変更についてのファイル内容をアップロードする場合は True、それ以外の場合は False。|

    ワイルドカードを使用してファイルとフォルダーの監視を構成する場合は、次の点を考慮してください。

    - 複数のファイルを追跡するにはワイルドカードが必要です。
    - ワイルドカードは、パスの最後のセグメントでのみ使用できます (*C:\folder\file* または */etc/* .conf* など)。
    - 環境変数に有効でないパスが含まれている場合、検証は成功しますが、インベントリの実行時にそのパスはエラーになります。
    - パスを設定するときは、漠然としたパス (*c:* .** など) は避けてください。走査の対象になるフォルダーが膨大な数になります。

8. **[ファイル コンテンツのアップロード]** には、必ず True を指定してください。 この設定は、示されたファイル パスに対してファイル コンテンツの追跡を有効にします。

### <a name="configure-file-tracking-on-linux"></a>Linux でファイル追跡を構成する

次の手順を使用して、Linux コンピューターでのファイル追跡を構成します。

1. **[設定の編集]** (歯車のシンボル) を選択します。

2. [ワークスペースの構成] ページで **[Linux ファイル]** を選択し、次に **[+ 追加]** を選択して、追跡する新しいファイルを追加します。

3. **[変更履歴用の Linux ファイルを追加する]** ページで、追跡するファイルまたはディレクトリの情報を入力し、 **[保存]** を選択します。 次の表に、情報に使用できるプロパティを示します。

    |プロパティ  |説明  |
    |---------|---------|
    |Enabled     | 設定が適用される場合は True、それ以外の場合は False。        |
    |Item Name     | 追跡するファイルのフレンドリ名。        |
    |グループ     | ファイルを論理的にグループ化するためのグループ名。        |
    |パスの入力     | ファイル確認のためのパス (例: **/etc/*.conf**)。       |
    |パスの種類     | パスの種類。 指定できる値は [ファイル] と [ディレクトリ] です。        |
    |再帰     | 追跡する項目を検索するときに、再帰を使用する場合は True、そうでない場合は False。        |
    |sudo の使用     | 項目を確認するときに sudo を使用する場合は True、そうでない場合は False。         |
    |リンク     | ディレクトリを走査するときにシンボリック リンクを処理する方法を決定する設定。 次のいずれかの値になります。<br> 無視 - シンボリック リンクを無視し、参照先のファイル/ディレクトリを含めません。<br>フォロー - 再帰中、シンボリック リンクに従います。また、参照先のファイルやディレクトリを含めます。<br>管理 - シンボリック リンクに従います。また、返された内容の変更を許可します。<br>**注:** ファイル コンテンツの取得がサポートされないため、[管理] オプションは推奨されません。    |
    |ファイル コンテンツのアップロード | 追跡された変更についてのファイル内容をアップロードする場合は True、それ以外の場合は False。 |

4. **[ファイル コンテンツのアップロード]** には、必ず **[True]** を指定してください。 この設定は、示されたファイル パスに対してファイル コンテンツの追跡を有効にします。

   ![Linux ファイルを追加する](./media/manage-change-tracking/add-linux-file.png)

## <a name="track-file-contents"></a>ファイル コンテンツを追跡する

ファイル コンテンツの追跡により、追跡された変更の前後のファイルのコンテンツを表示できます。 この機能では、変更が発生するたびにファイル コンテンツを[ストレージ アカウント](../../storage/common/storage-account-overview.md)に保存します。 ファイル コンテンツを追跡するためのいくつかの規則を次に示します。

* ファイル コンテンツの格納には、Resource Manager デプロイ モデルを使用している Standard ストレージ アカウントが必要です。
* 既定では、ストレージ アカウントは、任意のネットワーク上のクライアントからの接続を受け入れます。 特定のトラフィックのみを許可するようにストレージ アカウントをセキュリティで保護している場合は、Automation アカウントがそれに接続できるように構成規則を変更する必要があります。 「[Azure Storage ファイアウォールおよび仮想ネットワークを構成する](../../storage/common/storage-network-security.md)」を参照してください。
* Premium およびクラシック デプロイ モデルのストレージ アカウントは使用しないでください。 [Azure Storage アカウントの概要](../../storage/common/storage-account-create.md)に関する記事をご覧ください。
* ストレージ アカウントを接続できるのは、1 つの Automation アカウントだけです。
* ご自分の Automation アカウントで Change Tracking とインベントリを有効にする必要があります。

### <a name="enable-tracking-for-file-content-changes"></a>ファイル コンテンツの変更の追跡を有効にする

ファイル コンテンツの変更の追跡を有効にするには、次の手順に従います。

1. **[設定の編集]** (歯車のシンボル) を選択します。

2. **[ファイル コンテンツ]** を選択し、 **[リンク]** を選択します。 この選択により、 **[Add Content Location for Change Tracking]\(Change Tracking の対象となるコンテンツの場所を追加\)** ページが開きます。

   ![コンテンツの場所の追加](./media/manage-change-tracking/enable.png)

3. ファイル コンテンツを格納するために使用するサブスクリプションとストレージ アカウントを選択します。

5. 既存の追跡対象ファイルすべてに対してファイル コンテンツの追跡を有効にする場合は、 **[すべての設定のファイル コンテンツをアップロードする]** で **[オン]** を選択します。 この設定は、後でファイル パスごとに変更できます。

   ![ストレージ アカウントを設定する](./media/manage-change-tracking/storage-account.png)

6. Change Tracking と Inventory は、ファイル コンテンツの変更追跡を有効にするときに、ストレージ アカウントと Shared Access Signature (SAS) の URI を表示します。 署名は 365 日後に期限切れになりますが、 **[再生成]** を選択すると再作成できます。

   ![アカウント キーの一覧表示](./media/manage-change-tracking/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>追跡対象ファイルのコンテンツを表示する

Change Tracking と Inventory により追跡対象ファイルの変更が検出されると、[変更の詳細] ペインでファイル コンテンツを表示できます。  

![変更を一覧表示する](./media/manage-change-tracking/change-list.png)

1. Automation アカウントから **[変更履歴]** ページで、変更の一覧からファイルを選択し、 **[ファイル コンテンツの変更の表示]** を選択してファイルのコンテンツを表示します。 [変更の詳細] ペインには、変更前後の一般的なファイル情報がプロパティごとに表示されます。

   ![変更の詳細](./media/manage-change-tracking/change-details.png)

2. ファイル コンテンツは左右に並べて表示されています。 **[インライン]** を選択すると、変更はインライン ビューで表示されます。

## <a name="track-registry-keys"></a>レジストリ キーを追跡する

次の手順を使用して、Windows コンピューターでのレジストリ キー追跡を構成します。

1. Automation アカウントから **[変更履歴]** ページで、 **[設定の編集]** (歯車のシンボル) を選択します。

2. [ワークスペースの構成] ページで、 **[Windows レジストリ]** を選択します。

3. **[+ 追加]** を選択して、追跡する新しいレジストリ キーを追加します。

4. **[変更履歴用の Windows レジストリを追加する]** ページで、追跡するキーの情報を入力し、 **[保存]** を選択します。 次の表に、情報に使用できるプロパティを示します。 レジストリ パスを指定する場合は、値ではなくキーである必要があります。

    |プロパティ  |説明  |
    |---------|---------|
    |Enabled     | 設定が適用される場合は True、それ以外の場合は False。        |
    |Item Name     | 追跡するレジストリ キーのフレンドリ名。        |
    |グループ     | レジストリ キーを論理的にグループ化するためのグループ名。        |
    |Windows レジストリ キー   | パスが含まれたキー名 (例: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup`)。      |

## <a name="search-logs-for-change-records"></a>ログで変更レコードを検索する

Azure Monitor ログに対して、変更レコードのさまざまな検索を実行できます。 [Change tracking]\(変更の追跡\) ページを開いた状態で **[Log Analytics]** をクリックして、[ログ] ページを開きます。 次の表に、変更レコードのログ検索の例を示します。

|クエリ  |説明  |
|---------|---------|
|`ConfigurationData`<br>&#124; `where ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"`<br>&#124; `where SvcState == "Stopped"`<br>&#124; `summarize arg_max(TimeGenerated, *) by SoftwareName, Computer`         | Microsoft サービスの最新のインベントリ レコードで、Auto に設定されたが、Stopped として報告されたものを表示します。 結果は、指定されたソフトウェア名とコンピューターの最新のレコードに限定されます。    |
|`ConfigurationChange`<br>&#124; `where ConfigChangeType == "Software" and ChangeCategory == "Removed"`<br>&#124; `order by TimeGenerated desc`|削除されたソフトウェアの変更レコードを表示します。|

## <a name="next-steps"></a>次のステップ

* スコープ構成の詳細については、「[Change Tracking とインベントリのデプロイのスコープを制限する](manage-scope-configurations.md)」を参照してください。
* Azure Monitor ログに格納されているログを検索する必要がある場合は、[Azure Monitor ログでのログ検索](../../azure-monitor/logs/log-query-overview.md)に関する記事を参照してください。
* デプロイが終了したら、[変更履歴とインベントリの削除](remove-feature.md)に関する記事を参照してください。
* Change Tracking とインベントリから VM を削除する方法については、「[Change Tracking とインベントリから VM を削除する](remove-vms-from-change-tracking.md)」を参照してください。
* 機能のエラーのトラブルシューティングを行うには、「[Change Tracking と Inventory に関する問題のトラブルシューティング](../troubleshoot/change-tracking.md)」を参照してください。
