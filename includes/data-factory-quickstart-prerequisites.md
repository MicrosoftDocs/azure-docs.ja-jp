---
title: インクルード ファイル
description: インクルード ファイル
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 06/27/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: dc12d77e9a57938051300277940b6e5107e45496
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234531"
---
## <a name="prerequisites"></a>前提条件

### <a name="azure-subscription"></a>Azure サブスクリプション
Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

### <a name="azure-roles"></a>Azure ロール
Data Factory インスタンスを作成するには、Azure へのサインインに使用するユーザー アカウントが、"*共同作成者*" ロールまたは "*所有者*" ロールのメンバーであるか、Azure サブスクリプションの "*管理者*" である必要があります。 サブスクリプションで自分が持っているアクセス許可を表示するには、[Azure portal](https://portal.azure.com) に移動し、右上にあるユーザー名を選択し、 **[その他のオプション]** (...) を選択し、 **[アクセス許可]** を選択します。 複数のサブスクリプションにアクセスできる場合は、適切なサブスクリプションを選択します。

データセット、リンクされたサービス、パイプライン、トリガー、および統合ランタイムを含む Data Factory の子リソースを作成および管理するには、次の要件が適用されます。

- Azure portal で子リソースを作成および管理するには、リソース グループ レベル以上で **Data Factory 共同作成者**ロールに属している必要があります。
- PowerShell または SDK を使用して子リソースを作成および管理する場合は、リソース レベル以上での**共同作成者**ロールで十分です。

ロールにユーザーを追加する方法に関するサンプル手順については、[ロールの追加](../articles/billing/billing-add-change-azure-subscription-administrator.md)に関する記事を参照してください。

詳細については、次の記事を参照してください。

- [Data Factory 共同作成者ロール](../articles/role-based-access-control/built-in-roles.md#data-factory-contributor)
- [Azure Data Factory のロールとアクセス許可](../articles/data-factory/concepts-roles-permissions.md)

### <a name="azure-storage-account"></a>Azure ストレージ アカウント
このクイックスタートでは、"*ソース*" データ ストアと "*コピー先*" データ ストアの両方に汎用の Azure ストレージ アカウント (具体的には BLOB ストレージ) を使用します。 汎用の Azure ストレージ アカウントがない場合、作成方法については、「[ストレージ アカウントの作成](../articles/storage/common/storage-quickstart-create-account.md)」を参照してください。 

#### <a name="get-the-storage-account-name-and-account-key"></a>ストレージ アカウント名とアカウント キーの取得
このクイック スタートには、Azure Storage アカウントの名前とキーが必要です。 以下の手順に従って、ご利用のストレージ アカウントの名前とキーを取得してください。 

1. Web ブラウザーで [Azure portal](https://portal.azure.com) にアクセスし、Azure のユーザー名とパスワードを使用してサインインします。
2. **[すべてのサービス]**  >  **[ストレージ]**  >  **[ストレージ アカウント]** の順に選択します。
3. **[ストレージ アカウント]** ページで、ご利用のストレージ アカウントを (必要に応じて) フィルターで抽出し、該当するストレージ アカウントを選択します。 
4. *[\<アカウント名>]*  -  **[ストレージ アカウント]** ページのサイドバーで、 **[設定]** ラベルに移動し、 **[アクセス キー]** を選択します。 *[\<アカウント名>]*  -  **[アクセス キー]** が表示されます。

   ![ストレージ アカウントの名前とキーを取得](media/data-factory-quickstart-prerequisites/storage-account-name-key.png)
5. **[ストレージ アカウント名]** ボックスと **[Key1]** ボックスの値をクリップボードにコピーします。 それらをメモ帳または他のエディターに貼り付けて保存します。 このクイックスタートの後の方で使用します。   

#### <a name="create-a-blob-container"></a>BLOB コンテナーを作成する
このセクションでは、**adftutorial** という名前の BLOB コンテナーを Azure Blob Storage に作成します。

1. *\<アカウント名>*  - **アクセス キー** ページのサイドバーで、**概要** > **BLOB** を選択します。
2. *\<アカウント名>*  - **BLOB** ページのツールバーで、**コンテナー** を選択します。
3. **[新しいコンテナー]** ダイアログ ボックスで、名前に「**adftutorial**」と入力し、 **[OK]** を選択します。 *\<アカウント名>*  - **BLOB** ページが更新され、コンテナーの一覧に **adftutorial** が含まれるようになります。

   ![コンテナーの一覧](media/data-factory-quickstart-prerequisites/list-of-containers.png)

#### <a name="add-an-input-folder-and-file-for-the-blob-container"></a>BLOB コンテナーの入力フォルダーとファイルを追加する
このセクションでは、先ほど作成したコンテナーに **input** という名前のフォルダーを作成し、入力フォルダーにサンプル ファイルをアップロードします。 開始する前に、**メモ帳**などのテキスト エディターを開き、次の内容を含む **emp.txt** という名前のファイルを作成します。

```emp.txt
John, Doe
Jane, Doe
```

**C:\ADFv2QuickStartPSH** フォルダーにファイルを保存します (フォルダーがまだ存在しない場合は作成します)。Azure portal に戻り、次の手順を実行します。

1. 中断した *[\<アカウント名>]*  -  **[BLOB]** ページで、コンテナーの更新された一覧から **[adftutorial]** を選択します。 (ウィンドウを閉じた場合、または別のページに移動した場合は、[Azure portal](https://portal.azure.com) にもう一度サインインし、 **[すべてのサービス]**  >  **[ストレージ]**  >  **[ストレージ アカウント]** を選択し、 **[BLOB]**  >  **[adftutorial]** を選択します)。
2. **adftutorial** コンテナー ページのツールバーで、 **[アップロード]** を選択します。
3. **[BLOB のアップロード]** ページで、 **[ファイル]** ボックスを選択し、**emp.txt** ファイルを参照して選択します。
4. **[詳細設定]** の見出しを展開します。 次のようにページが表示されます。

   ![[詳細設定] リンクの選択](media/data-factory-quickstart-prerequisites/upload-blob-advanced.png)
5. **[アップロード先のフォルダー]** ボックスに「**input**」と入力します。
6. **[アップロード]** ボタンを選択します。 一覧に **emp.txt** ファイルとアップロードの状態が表示されます。
7. **[閉じる]** アイコン (**X**) を選択して、 **[BLOB のアップロード]** ページを閉じます。

**adftutorial** コンテナーのページを開いたままにしておきます。 このクイックスタートの最後で、このページを使用して出力を確認します。