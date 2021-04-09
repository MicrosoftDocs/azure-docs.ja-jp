---
title: Azure Blob Storage と Azure Data Lake Storage からデータを共有および受信する
description: Azure Blob Storage と Azure Data Lake Storage からデータを共有および受信する方法について説明します。
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 02/23/2021
ms.openlocfilehash: dc309e85373193e4f5d431f543ff3e59ea5bebc7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739264"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Azure Blob Storage と Azure Data Lake Storage からデータを共有および受信する

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

Azure Data Share では、ストレージ アカウントからのスナップショットベースの共有がサポートされています。 この記事では、Azure Blob Storage、Azure Data Lake Storage Gen1、および Azure Data Lake Storage Gen2 からデータを共有および受信する方法について説明します。

Azure Data Share では、Azure Data Lake Gen1 および Azure Data Lake Gen2 からのファイル、フォルダー、ファイル システムの共有がサポートされています。 また、Azure Blob Storage からの BLOB、フォルダー、コンテナーの共有もサポートされています。 現時点では、ブロック BLOB のみがサポートされています。 これらのソースから共有されているデータは、Azure Data Lake Gen2 または Azure Blob Storage で受信できます。

ファイル システム、コンテナー、またはフォルダーがスナップショットベースの共有で共有されている場合、データ コンシューマーは共有データの完全なコピーを作成することを選択できます。 または、増分スナップショット機能を使用して、新しいファイルまたは更新されたファイルのみをコピーすることもできます。 増分スナップショットの機能は、ファイルの最終更新時刻に基づいています。 

同じ名前の既存のファイルは、スナップショットの作成中に上書きされます。 ソースからファイルが削除されても、ターゲットでは削除されません。 ソースにある空のサブフォルダーは、ターゲットにはコピーされません。 

## <a name="share-data"></a>データの共有

Azure Data Share を使用してデータを共有するには、次のセクションの情報を参照してください。 
### <a name="prerequisites-to-share-data"></a>データを共有するための前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* 受信者の Azure サインイン メール アドレスを検索します。 受信者のメール アドレスの別名は、この目的には使用できません。
* Data Share リソースの作成用とは異なる Azure サブスクリプションにソース Azure データ ストアがある場合は、Azure データ ストアがあるサブスクリプションで [Microsoft.DataShare リソース プロバイダー](concepts-roles-permissions.md#resource-provider-registration)を登録してください。 

### <a name="prerequisites-for-the-source-storage-account"></a>ソース ストレージ アカウントの前提条件

* Azure Storage のアカウント まだアカウントを持っていない場合は[作成](../storage/common/storage-account-create.md)します。
* ストレージ アカウントに書き込むためのアクセス許可。 書き込みアクセス許可は、*Microsoft.Storage/storageAccounts/write* にあります。 これは共同作成者ロールの一部です。
* ロールの割り当てをストレージ アカウントに追加するアクセス許可。 このアクセス許可は *Microsoft.Authorization/role assignments/write* にあります。 これは所有者ロールの一部です。 

### <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

### <a name="create-a-data-share-account"></a>Data Share アカウントを作成する

Azure リソース グループに Azure Data Share リソースを作成します。

1. ポータルの左上隅でメニューを開き、 **[リソースの作成]** (+) を選択します。

1. *Data Share* を検索します。

1. **[Data Share]** を選択し、 **[作成]** を選択します。

1. Azure Data Share リソースの基本情報を指定します。 

     **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | サブスクリプション | 該当するサブスクリプション | Data Share アカウントの Azure サブスクリプションを選択します。|
    | リソース グループ | *test-resource-group* | 既存のリソース グループを使用するか、リソース グループを作成します。 |
    | 場所 | *米国東部 2* | Data Share アカウントのリージョンを選択します。
    | 名前 | *datashareaccount* | Data Share アカウントに名前を付けます。 |
    | | |

1. **[レビュー + 作成]**  >  **[作成]** を選択して Data Share アカウントをプロビジョニングします。 通常、新しい Data Share アカウントのプロビジョニングにかかる時間は約 2 分です。 

1. デプロイが完了したら、**[リソースに移動]** を選択します。

### <a name="create-a-share"></a>共有を作成する

1. データ共有の **[概要]** ページに移動します。

   :::image type="content" source="./media/share-receive-data.png" alt-text="データ共有の概要を示すスクリーンショット。":::

1. **[Start sharing your data]\(データの共有の開始する\)** を選択します。

1. **［作成］** を選択します   

1. 共有の詳細を入力します。 名前、共有の種類、共有の内容の説明、利用規約 (省略可能) を指定します。 

    ![データ共有の詳細を示すスクリーンショット。](./media/enter-share-details.png "データ共有の詳細を入力します。") 

1. **[続行]** をクリックします。

1. 共有にデータセットを追加するには、 **[データセットの追加]** を選択します。 

    ![共有にデータセットを追加する方法を示すスクリーンショット。](./media/datasets.png "データセット:")

1. 追加するデータセットの種類を選択します。 データセットの種類の一覧に表示される内容は、前の手順でスナップショットベースの共有とインプレース共有のどちらを選択したかに応じて変わります。 

    ![データセットの種類を選択する場所を示すスクリーンショット。](./media/add-datasets.png "データセットを追加します。")    

1. 共有するオブジェクトに移動します。 **[データセットの追加]** を選択します。 

    ![共有するオブジェクトを選択する方法を示すスクリーンショット。](./media/select-datasets.png "データセットを選択します。")    

1. **[受信者]** タブで、 **[受信者の追加]** を選択して、データ コンシューマーのメール アドレスを追加します。 

    ![受信者の電子メール アドレスを追加する方法を示すスクリーンショット。](./media/add-recipient.png "受信者を追加します。") 

1. **[続行]** をクリックします。

1. スナップショットの共有の種類を選択した場合は、データ コンシューマー用にデータを更新するようにスナップショット スケジュールを設定できます。 

    ![スナップショット スケジュールの設定を示すスクリーンショット。](./media/enable-snapshots.png "スナップショットを有効にします。") 

1. 開始時刻と繰り返しの間隔を選択します。 

1. **[続行]** をクリックします。

1. **[レビュー + 作成]** タブで、パッケージの内容、設定、受信者、および同期の設定を確認します。 **[作成]** を選択します。

これで、Azure のデータ共有が作成されました。 データ共有の受信者は、招待を受け付けることができます。 

## <a name="receive-data"></a>データの受信

以下のセクションでは、共有データを受信する方法について説明します。
### <a name="prerequisites-to-receive-data"></a>データを受信するための前提条件
データ共有の招待を受け付ける前に、以下の前提条件を確認してください。 

* Azure サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成します。
* Azure からの招待。 メールの件名は、" *\<yourdataprovider\@domain.com>* からの Azure Data Share の招待" となっているはずです。
* 次に含まれる、登録済みの [Microsoft.DataShare リソース プロバイダー](concepts-roles-permissions.md#resource-provider-registration)。
    * Data Share リソースを作成する Azure サブスクリプション。
    * ターゲットの Azure データストアが配置されている Azure サブスクリプション。

### <a name="prerequisites-for-a-target-storage-account"></a>ターゲット ストレージ アカウントの前提条件

* Azure Storage のアカウント まだない場合は、[アカウントを作成](../storage/common/storage-account-create.md)します。 
* ストレージ アカウントに書き込むためのアクセス許可。 このアクセス許可は *Microsoft.Storage/storageAccounts/write* にあります。 これは共同作成者ロールの一部です。 
* ロールの割り当てをストレージ アカウントに追加するためのアクセス許可。 この割り当ては *Microsoft.Authorization/role assignments/write* にあります。 これは所有者ロールの一部です。  

### <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

### <a name="open-an-invitation"></a>招待を開く

招待は、メールから開くことができるほか、Azure portal から直接開くこともできます。

1. 招待をメールから開くには、受信トレイでデータ プロバイダーからの招待を確認します。 招待は Microsoft Azure からで、件名は " *\<yourdataprovider\@domain.com>* からの Azure Data Share の招待" になっています。 **[招待を表示]** を選択して、Azure で招待を確認します。 

   Azure portal から招待を開くには、 *[データ共有への招待]* を検索します。 データ共有の招待の一覧が表示されます。

   ![Azure portal の招待状の一覧を示すスクリーンショット。](./media/invitations.png "招待の一覧。") 

1. 表示する共有を選択します。 

### <a name="accept-an-invitation"></a>招待を受け入れる
1. **[使用条件]** を含むすべてのフィールドを確認してください。 条件に同意したら、チェック ボックスをオンにします。 

   ![使用条件の領域を示すスクリーンショット。](./media/terms-of-use.png "Terms of use.") 

1. **[ターゲット データ共有アカウント]** で、自分の Data Share のデプロイ先とするサブスクリプションとリソース グループを選択します。 次に、以下のフィールドを入力します。

   * **[データ共有アカウント]** フィールドで **[新規作成]** を選択します (Data Share アカウントがない場合)。 または、自分のデータ共有を受け入れる既存の Data Share アカウントを選択します。 

   * **[受信した共有名]** フィールドで、データ プロバイダーが指定した既定値をそのままにするか、受信した共有の新しい名前を指定します。 

1. **[同意して構成する]** を選択します。 共有サブスクリプションが作成されます。 

   ![構成オプションを受け入れる場所を示すスクリーンショット。](./media/accept-options.png "受け入れオプション") 

    Data Share アカウントに、受信した共有が表示されます。 

    招待を受け入れたくない場合は、 **[拒否]** を選択します。 

### <a name="configure-a-received-share"></a>受信した共有を構成する
データを受信する場所を構成するには、このセクションの手順に従います。

1. **[データセット]** タブで、ターゲットを割り当てるデータセットの横にあるチェック ボックスをオンにします。 **[ターゲットへのマップ]** を選択し、ターゲット データ ストアを選択します。 

   ![ターゲットへのマップ方法を示すスクリーンショット。](./media/dataset-map-target.png "ターゲットへのマップ。") 

1. データのターゲット データ ストアを選択します。 ターゲット データ ストア内のファイルのうち、パスと名前が受信データのファイルと同じものは上書きされます。 

   ![ターゲット ストレージ アカウントを選択する場所を示すスクリーンショット。](./media/map-target.png "ターゲット ストレージ。") 

1. スナップショットベースの共有では、データ プロバイダーがスナップショット スケジュールを使用して定期的にデータを更新する場合、 **[スナップショット スケジュール]** タブからスケジュールを有効にすることができます。スナップショット スケジュールの横にあるチェック ボックスをオンにします。 その後、**[有効化]** を選択します。

   ![スナップショット スケジュールを有効にする方法を示すスクリーンショット。](./media/enable-snapshot-schedule.png "スナップショット スケジュールを有効にします。")

### <a name="trigger-a-snapshot"></a>スナップショットをトリガーする
このセクションの手順は、スナップショットベースの共有にのみ適用されます。

1. スナップショットは **[詳細]** タブからトリガーできます。このタブで **[スナップショットのトリガー]** を選択します。 データの完全なスナップショットまたは増分スナップショットがトリガーされるように選択することができます。 データ プロバイダーから初めてデータを受信する場合は、 **[完全なコピー]** を選択します。 1 つのスナップショットが実行されている場合、後続のスナップショットは前のものが完了するまで開始されません。

   ![スナップショットがトリガーされるようにすることを選択したスクリーンショット。](./media/trigger-snapshot.png "スナップショットをトリガーします。") 

1. 最終実行状態が "*成功*" の場合、ターゲット データ ストアに移動して、受信したデータを表示します。 **[データセット]** を選択し、ターゲット パスのリンクを選択します。 

   ![コンシューマー データセットのマッピングを示すスクリーンショット。](./media/consumer-datasets.png "コンシューマー データセットのマッピング。") 

### <a name="view-history"></a>履歴を表示する
スナップショットの履歴は、スナップショットベースの共有でのみ表示できます。 履歴を表示するには、 **[履歴]** タブを開きます。ここには、過去 30 日間に生成されたすべてのスナップショットの履歴があります。 

## <a name="storage-snapshot-performance"></a>ストレージ スナップショットのパフォーマンス
ストレージ スナップショットのパフォーマンスは、ファイル数と共有データのサイズに加え、さまざまな要因によって影響を受けます。 常に、独自のパフォーマンス テストを実施することをお勧めします。 パフォーマンスに影響するいくつかの要因の例を下に示します。

* ソースとターゲットのデータ ストアへの同時アクセス。  
* ソースとターゲットのデータ ストアの場所。 
* 増分スナップショットの場合、共有データセット内のファイル数が、最後に成功したスナップショットの後の最終変更時刻によってファイルの一覧を検索するのにかかる時間に影響する可能性があります。 


## <a name="next-steps"></a>次のステップ
Azure Data Share サービスを使用してストレージ アカウントからデータを共有および受信する方法について学習しました。 他のデータ ソースからの共有の詳細については、[サポートされているデータ ストア](supported-data-stores.md)に関するページを参照してください。