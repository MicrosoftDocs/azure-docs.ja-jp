---
title: Azure Automation で Change Tracking と Inventory を管理する
description: この記事では、Change Tracking と Inventory を使用して、お使いの環境内でソフトウェアと Microsoft サービスの変更を追跡する方法について説明します。
services: automation
ms.subservice: change-inventory-management
ms.date: 06/15/2020
ms.topic: conceptual
ms.openlocfilehash: eab509e389c074232526aa93fcebb72f3bc986c0
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185604"
---
# <a name="manage-change-tracking-and-inventory"></a>Change Tracking と Inventory の管理

追跡対象の新しいファイルまたはレジストリ キーを追加すると、Azure Automation によって、[Change Tracking とインベントリ](change-tracking.md)に対してそれが有効になります。 この記事では、トラッキングを構成し、トラッキングの結果を確認して、変更の検出時にアラートを処理する方法について説明します。

この記事の手順を使用する前に、これらのいずれかの方法を使用して、VM で Change Tracking とインベントリを有効にしておいてください。

* [Automation アカウントで Change Tracking とインベントリを有効にする](automation-enable-changes-from-auto-acct.md)
* [Azure portal を参照して Change Tracking とインベントリを有効にする](automation-enable-changes-from-browse.md)
* [Runbook で Change Tracking とインベントリを有効にする](automation-enable-changes-from-runbook.md)
* [Azure VM で Change Tracking とインベントリを有効にする](automation-enable-changes-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>デプロイのスコープを制限する

Change Tracking とインベントリでは、ワークスペース内のスコープ構成を使用して、変更の対象となるコンピューターを指定します。 詳細については、「[Change Tracking とインベントリのデプロイのスコープを制限する](automation-scope-configurations-change-tracking.md)」を参照してください。

## <a name="track-files"></a>ファイルを追跡する

Change Tracking とインベントリを使用して、ファイルとフォルダー (ディレクトリ) への変更を追跡できます。 このセクションでは、Windows および Linux でのファイル トラッキングを構成する方法について説明します。

### <a name="configure-file-tracking-on-windows"></a>Windows でファイル追跡を構成する

次の手順を使用して、Windows コンピューターでのファイル追跡を構成します。

1. Automation アカウントで、 **[構成管理]** の **[Change Tracking]\(変更の追跡\)** を選択します。 
2. **[設定の編集]** (歯車アイコン) をクリックします。
3. [ワークスペースの構成] ページで **[Windows ファイル]** を選択し、次に **[+ 追加]** をクリックして、追跡する新しいファイルを追加します。
4. [変更履歴用の Windows ファイルを追加する] ペインで、追跡対象のファイルまたはフォルダーの情報を入力して、 **[保存]** をクリックします。 次の表に、情報に使用できるプロパティを示します。

    |プロパティ  |説明  |
    |---------|---------|
    |Enabled     | 設定が適用される場合は True、それ以外の場合は False。        |
    |Item Name     | 追跡するファイルのフレンドリ名。        |
    |グループ     | ファイルを論理的にグループ化するためのグループ名。        |
    |パスの入力     | ファイルを確認するパス (例: **c:\temp\\\*.txt**)。 `%winDir%\System32\\\*.*` などの環境変数も使用できます。       |
    |パスの種類     | パスの種類。 指定できる値は [ファイル] と [フォルダー] です。        |    
    |再帰     | 追跡する項目を検索するときに、再帰を使用する場合は True、そうでない場合は False。        |    
    |ファイル コンテンツのアップロード | 追跡された変更についてのファイル内容をアップロードする場合は True、それ以外の場合は False。|

5. **[ファイル コンテンツのアップロード]** には、必ず True を指定してください。 この設定は、示されたファイル パスに対してファイル コンテンツの追跡を有効にします。

### <a name="configure-file-tracking-on-linux"></a>Linux でファイル追跡を構成する

次の手順を使用して、Linux コンピューターでのファイル追跡を構成します。

1. Automation アカウントで、 **[構成管理]** の **[Change Tracking]\(変更の追跡\)** を選択します。 
2. **[設定の編集]** (歯車アイコン) をクリックします。
3. [ワークスペースの構成] ページで **[Linux ファイル]** を選択し、次に **[+ 追加]** をクリックして、追跡する新しいファイルを追加します。
4. [Add Linux File for Change Tracking]\(変更履歴用の Linux ファイルを追加する\) ペインで、追跡するファイルまたはディレクトリの情報を入力し、 **[保存]** をクリックします。 次の表に、情報に使用できるプロパティを示します。

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

5. **[ファイル コンテンツのアップロード]** には、必ず True を指定してください。 この設定は、示されたファイル パスに対してファイル コンテンツの追跡を有効にします。

   ![Linux ファイルを追加する](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>ファイル コンテンツを追跡する

ファイル コンテンツの追跡により、追跡された変更の前後のファイルのコンテンツを表示できます。 この機能では、変更が発生するたびにファイル コンテンツを[ストレージ アカウント](../storage/common/storage-account-overview.md)に保存します。 ファイル コンテンツを追跡するためのいくつかの規則を次に示します。

* ファイル コンテンツの格納には、Resource Manager デプロイ モデルを使用している Standard ストレージ アカウントが必要です。 
* Premium およびクラシック デプロイ モデルのストレージ アカウントは使用しないでください。 [Azure Storage アカウントの概要](../storage/common/storage-account-create.md)に関する記事をご覧ください。
* ストレージ アカウントを接続できるのは、1 つの Automation アカウントだけです。
* ご自分の Automation アカウントで [Change Tracking とインベントリ](change-tracking.md)を有効にする必要があります。

### <a name="enable-tracking-for-file-content-changes"></a>ファイル コンテンツの変更の追跡を有効にする

ファイル コンテンツの変更の追跡を有効にするには、次の手順に従います。

1. Azure portal で、Automation アカウントを開き、次に **[構成管理]** で **[Change tracking]\(変更の追跡\)** を選択します。
2. **[設定の編集]** (歯車アイコン) をクリックします。
3. **[ファイル コンテンツ]** を選択し、 **[リンク]** をクリックします。 この選択により、[Add Content Location for Change Tracking]\(Change Tracking の対象となるコンテンツの場所を追加\) ペインが開きます。

   ![コンテンツの場所の追加](./media/change-tracking-file-contents/enable.png)

4. ファイル コンテンツを格納するために使用するサブスクリプションとストレージ アカウントを選択します。 

5. 既存の追跡対象ファイルすべてに対してファイル コンテンツの追跡を有効にする場合は、 **[すべての設定のファイル コンテンツをアップロードする]** で **[オン]** を選択します。 この設定は、後でファイル パスごとに変更できます。

   ![ストレージ アカウントを設定する](./media/change-tracking-file-contents/storage-account.png)

6. Change Tracking と Inventory は、ファイル コンテンツの変更追跡を有効にするときに、ストレージ アカウントと Shared Access Signature (SAS) の URI を表示します。 署名は 365 日後に期限切れになりますが、 **[再生成]** をクリックすると再作成できます。

   ![アカウント キーの一覧表示](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>追跡対象ファイルのコンテンツを表示する

Change Tracking と Inventory により追跡対象ファイルの変更が検出されると、[変更の詳細] ペインでファイル コンテンツを表示できます。  

![変更を一覧表示する](./media/change-tracking-file-contents/change-list.png)

1. Azure portal で、Automation アカウントを開き、次に **[構成管理]** で **[Change tracking]\(変更の追跡\)** を選択します。

2. 変更の一覧からファイルを選択し、 **[ファイル コンテンツの変更の表示]** を選択してファイルのコンテンツを表示します。 [変更の詳細] ペインには、変更前後の一般的なファイル情報がプロパティごとに表示されます。

   ![変更の詳細](./media/change-tracking-file-contents/change-details.png)

3. ファイル コンテンツは左右に並べて表示されています。 **[インライン]** を選択すると、変更はインライン ビューで表示されます。

## <a name="track-registry-keys"></a>レジストリ キーを追跡する

次の手順を使用して、Windows コンピューターでのレジストリ キー追跡を構成します。

1. Azure portal で、Automation アカウントを開き、次に **[構成管理]** で **[Change tracking]\(変更の追跡\)** を選択します。 
2. **[設定の編集]** (歯車アイコン) をクリックします。
3. [ワークスペースの構成] ページで、 **[Windows レジストリ]** を選択します。
4. **[+ 追加]** をクリックして、追跡する新しいレジストリ キーを追加します。
5. [Add Windows Registry for Change Tracking]\(変更履歴用の Windows レジストリを追加する\) ペインで、追跡するキーの情報を入力し、 **[保存]** をクリックします。 次の表に、情報に使用できるプロパティを示します。

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

## <a name="create-alerts-on-changes"></a>変更に関するアラートを作成する

次の例は、マシン上でファイル **c:\windows\system32\drivers\etc\hosts** が変更されたことを示しています。 このファイルは、Windows がホスト名を IP アドレスに解決するために使用するため、重要です。 この操作は DNS より優先されるため、接続の問題が発生する可能性があります。 また、悪意のある、またはその他の危険な Web サイトにトラフィックがリダイレクトされる可能性もあります。

![hosts ファイルの変更を示すグラフ](./media/change-tracking-file-contents/changes.png)

次の例を使用して、変更に関するアラートを作成する手順について説明します。

1. Automation アカウントで、 **[構成管理]** の **[Change tracking]\(変更の追跡\)** を選択し、次に **[Log Analytics]** を選択します。 
2. ログ検索で、クエリ `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` を使用して、**hosts** ファイルに対するコンテンツ変更を検索します。 このクエリでは、完全修飾パス名に `hosts` という単語が含まれているファイルのコンテンツ変更を検索します。 パスの部分を完全修飾の形式に変更する (たとえば、`FileSystemPath == "c:\windows\system32\drivers\etc\hosts"` を使用する) ことで、特定のファイルを要求することもできます。

3. クエリからその結果が返されたら、ログ検索で **[新しいアラート ルール]** をクリックして、アラート作成ページを開きます。 Azure portal で **Azure Monitor** を使用してこのページに移動することもできます。 

4. もう一度クエリを確認し、警告ロジックを変更してください。 この場合は、環境内のすべてのマシンで 1 つでも変更が検出されたら、アラートがトリガーされるようにします。

    ![hosts ファイルに対する変更を追跡するためのクエリへの変更](./media/change-tracking-file-contents/change-query.png)

5. アラート ロジックを設定したら、アラートのトリガーに応じてアクションを実行するアクション グループを割り当てます。 この場合は、送信する電子メールと、作成する IT サービスマネジメント (ITSM) チケットを設定します。 

    ![変更に関するアラートを通知するようにアクション グループを構成する](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>次のステップ

* スコープ構成の詳細については、「[Change Tracking とインベントリのデプロイのスコープを制限する](automation-scope-configurations-change-tracking.md)」を参照してください。
* Log Analytics ワークスペースに格納されているログを検索する必要がある場合は、[Azure Monitor ログでのログ検索](../azure-monitor/log-query/log-query-overview.md)に関する記事を参照してください。
* デプロイが完了したら、[Change Tracking とインベントリの Automation アカウントからワークスペースをリンク解除する方法](automation-unlink-workspace-change-tracking.md)についてのページを参照してください。
* Change Tracking とインベントリから VM を削除する方法については、「[Change Tracking とインベントリから VM を削除する](automation-remove-vms-from-change-tracking.md)」を参照してください。
* 機能のエラーのトラブルシューティングを行うには、「[Change Tracking と Inventory に関する問題のトラブルシューティング](troubleshoot/change-tracking.md)」を参照してください。
