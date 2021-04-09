---
title: チュートリアル:Azure Purview (プレビュー) でデータをスキャンする
description: このチュートリアルでは、スタート キットを実行してデータ資産をセットアップした後、データ ソースのデータをスキャンして Azure Purview カタログに取り込みます。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 16692ac75f0ab6df0c8ee1bebef393848ca066b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676545"
---
# <a name="tutorial-scan-data-with-azure-purview-preview"></a>チュートリアル:Azure Purview (プレビュー) でデータをスキャンする

> [!IMPORTANT]
> Azure Purview は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この "*5 部構成のチュートリアル シリーズ*" では、Azure Purview (プレビュー) を使用して、データ資産全体にわたってデータ ガバナンスを管理する方法の基礎について説明します。 このチュートリアル パートで作成するデータ資産は、以降のシリーズでも使用します。

このチュートリアル シリーズのパート 1 では、次のことを行います。

> [!div class="checklist"]
>
> * 各種の Azure データ リソースを使用してデータ資産を作成する。
> * データをスキャンしてカタログに取り込む。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)してください。
* [Azure Purview アカウント](create-catalog-portal.md)。
* 自分のデータ資産をデプロイする[スタート キット](https://github.com/Azure/Purview-Samples/blob/master/PurviewStarterKitV4.zip)。

> [!NOTE]
> スタート キットは Windows でのみ使用できます。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-a-data-estate"></a>データ資産を作成する

このセクションでは、スタート キットのスクリプトを実行して、シミュレートされたデータ資産を作成します。 データ資産は、企業が所有するあらゆるデータのポートフォリオです。 スタート キット スクリプトによって、次のアクションが実行されます。

* Azure Blob Storage アカウントを作成し、そのアカウントにデータを投入します。
* Azure Data Lake Storage Gen2 アカウントを作成します。
* Azure Data Factory インスタンスを作成し、そのインスタンスを Azure Purview に関連付けます。
* Azure Blob Storage と Azure Data Lake Storage Gen2 のアカウントの間にコピー アクティビティ パイプラインを設定してトリガーします。
* 関連付けられている系列を Azure Data Factory から Azure Purview にプッシュします。

### <a name="prepare-to-run-the-starter-kit"></a>スタート キットを実行するための準備をする

自分の Windows マシンにスタート キット クライアント ソフトウェアを設定するには、次の手順に従います。

1. [スタート キットをダウンロード](https://github.com/Azure/Purview-Samples/blob/master/PurviewStarterKitV4.zip)し、その内容を任意の場所に抽出します。


1. ご使用のコンピューターで、Windows タスク バーの検索ボックスに「**PowerShell**」と入力します。 検索リストで **[Windows PowerShell]** を右クリックし、 **[管理者として実行]** を選択します。

1. PowerShell ウィンドウで次のコマンドを入力します。`<path-to-starter-kit>` は、抽出したスタート キット ファイルがあるフォルダーのパスに置き換えてください。

   ```powershell
   dir -Path <path-to-starter-kit> -Recurse | Unblock-File
   ```

1. 次のコマンドを入力して Azure コマンドレットをインストールします。

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```

1. "*NuGet provider is required to continue (続行するには NuGet プロバイダーが必要です)* " という警告プロンプトが表示された場合は、「**Y**」と入力して Enter キーを押します。

1. "*信頼されていないリポジトリ*" という警告プロンプトが表示された場合は、「**A**」と入力して Enter キーを押します。

PowerShell によって必要なモジュールがインストールされるまでに 1 分ほどかかる場合があります。

### <a name="collect-data-needed-to-run-the-scripts"></a>スクリプトの実行に必要なデータを収集する

PowerShell スクリプトを実行してカタログをブートストラップする前に、スクリプトで使用する次の引数の値を取得します。

* TenantId
   1. [Azure Portal](https://portal.azure.com) で、 **[Azure Active Directory]** を選択します。
   1. 左側のナビゲーション ペインの **[管理]** セクションで、 **[プロパティ]** を選択します。 次に、 **[テナント ID]** のコピー アイコンを選択して、クリップボードに値を保存します。 後で使うためにその値をテキスト エディターに貼り付けます。

* SubscriptionID
   1. 前提条件として作成した Azure Purview インスタンスの名前を Azure portal で検索し、選択します。
   1. **[概要]** セクションを選択して、 **[サブスクリプション ID]** の GUID を保存します。

   > [!NOTE]
   > - 必ず、Azure Purview アカウントを作成したときと同じサブスクリプションを使用してください。 これは、許可リストに追加したのと同じサブスクリプションになります。
   > - スターター キットを実行した後、Azure Purview で系列が欠落することがあります。 これは、スターター キットによって作成されたデータ ファクトリに Purview のアクセス許可がないためです。 [**このドキュメント リンク**](how-to-link-azure-data-factory.md#view-existing-data-factory-connections)を選択して、Data Factory が正しく構成され、Purview で適切なロールが割り当てられていることを確認します


* CatalogName: [Azure Purview アカウントの作成](create-catalog-portal.md)に関するページで作成した Azure Purview アカウントの名前。

* CatalogResourceGroupName: Azure Purview アカウントを作成したリソース グループの名前。

### <a name="verify-permissions"></a>アクセス許可の確認

前提条件として作成した Azure Purview アカウントにスクリプトの実行ユーザーを追加するには、以下の手順に従います。 ユーザーには、"*Purview データ キュレーター*" と "*Purview データ ソース管理者*" の両方のロールが必要です。 

Azure Purview アカウントをご自身で作成した場合は、アクセス権が自動的に割り当てられるので、このセクションはスキップしてかまいません。

1. Azure portal の [Purview アカウント] ページに移動し、変更したい Azure Purview アカウントを選択します。

1. アカウントのページで **[アクセス制御 (IAM)]** タブを選択し、 **[+ 追加]** を選択します。

1. **[ロールの割り当ての追加]** を選択します。

1. *[ロール]* に「**Purview データ キュレーター ロール**」と入力します。
 
1. *[アクセスの割り当て先]* には既定値を使用します。 **[User, group, or service principal]\(ユーザー、グループ、またはサービス プリンシパル\)** が既定値となっているはずです。

1. スクリプトの実行ユーザーの名前を **[選択]** に入力します。

1. **[保存]** を選択します。

1. *[ロール]* を **[Purview データ ソース管理者ロール]** に設定して上記の手順を繰り返します。

詳細については、[カタログのアクセス許可](catalog-permissions.md)に関するページを参照してください。

### <a name="run-the-client-side-setup-scripts"></a>クライアント側の設定スクリプトを実行する

カタログの構成が完了したら、PowerShell ウィンドウで以下のスクリプトを実行して資産を作成します。プレースホルダーは、前もって収集しておいた値に置き換えてください。

1. 次のコマンドを使用して、スタート キットのディレクトリに移動します。 `path-to-starter-kit` は、抽出したファイルがあるフォルダーのパスに置き換えてください。

   ```powershell
   cd <path-to-starter-kit>
   ```

1. 次のコマンドで、ローカル コンピューターの実行ポリシーを設定します。 実行ポリシーを変更するように求められたら、 *[Yes to All]\(すべてはい\)* で「**A**」を入力します。

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

1. 次のコマンドを使用して Azure に接続します。 `TenantID` および `SubscriptionID` のプレースホルダーを置き換えます。

   ```powershell
   .\RunStarterKit.ps1 -ConnectToAzure -TenantId <TenantID> `
   -SubscriptionId <SubscriptionID>
   ```

   このコマンドを実行すると、Azure Active Directory の資格情報を使用してサインインするためのポップアップ ウィンドウが表示されることがあります。


1. 次のコマンドを使用してスタート キットを実行します。 `CatalogName`、`TenantID`、`SubscriptionID`、`NewResourceGroupName`、`CatalogResourceGroupName` の各プレースホルダーを置き換えてください。 `NewResourceGroupName` には、データ資産の追加先となるリソース グループの一意の名前 (小文字の英数字のみ) を使用します。

   > [!IMPORTANT]
   > **newresourcegroupname** には、数字と小文字のみを使用できます。また、文字数は 17 文字未満にする必要があります。 **大文字のアルファベットや特殊文字は使用できません。** この制約は、ストレージ アカウントの名前付けルールによるものです。

   ```powershell
   .\RunStarterKit.ps1 -CatalogName <CatalogName> -TenantId <TenantID>`
   -ResourceGroup <newresourcegroupname> `
   -SubscriptionId <SubscriptionID> `
   -CatalogResourceGroup <CatalogResourceGroupName>
   ```

環境が設定されるまでに最大 10 分かかることがあります。 この間にさまざまなポップアップ ウィンドウが表示されるかもしれませんが、無視してかまいません。 **BlobDataCreator.exe** ウィンドウは閉じないでください。完了したときに自動的に閉じます。

"`Executing Copy pipeline xxxxxxxxxx-487e-4fc4-9628-92dd8c2c732b`" というメッセージが表示されたときは、**BlobDataCreator.exe** の別のインスタンスの実行が開始されて終了するまで待ってください。

> [!IMPORTANT]
> "アクティブなタスクの数" が減少しなくなった場合は、BLOB 作成ウィンドウを終了し、PowerShell ウィンドウで Enter キーを押してください。

このプロセスが完了すると、指定した名前のリソース グループが作成されます。 Azure Data Factory、Azure Blob Storage、Azure Data Lake Storage Gen2 のすべてのアカウントがこのリソース グループに存在します。 このリソース グループは、自分が指定したサブスクリプションに属しています。

## <a name="scan-data-into-the-catalog"></a>データをスキャンしてカタログに取り込む

スキャンは、ユーザー指定のスケジュールに従ってカタログが直接データ ソースに接続するプロセスです。 カタログには、スキャン、系列、ポータル、API を通じて、会社のデータ資産が反映されます。 中身を観察することや、スキーマを抽出すること、セマンティクスを把握することがその目的です。 このセクションでは、スタート キットを使用して生成したデータ ソースのスキャンを設定します。

スタート キット スクリプトの実行により、Azure Blob Storage と Azure Data Lake Storage Gen2 の 2 つのデータ ソースが作成されています。 これらのデータ ソースを 1 つずつスキャンしてカタログに取り込むことができます。

### <a name="authenticate-to-your-storage-with-managed-identity"></a>マネージド ID を使用してストレージに対する認証を行う

Azure Purview アカウントを作成すると、そのアカウントと同じ名前のマネージド ID が自動的に作成されます。 データをスキャンする前に、自分のストレージ アカウントに対するアクセス許可を Azure Purview ロールに付与する必要があります。

1. スタート キットによって作成されたリソース グループに移動して、自分の Blob Storage アカウントを選択します。

1. 左側のナビゲーション メニューで **[アクセス制御 (IAM)]** を選択します。 次に、 **[+ 追加]** を選択します。

1. [ロール] を **[ストレージ BLOB データ閲覧者]** に設定し、 **[選択]** に自分の Azure Purview アカウント名を入力します。 次に、 **[保存]** を選択して、このロールの割り当てを自分の Purview アカウントに付与します。

   :::image type="content" source="media/tutorial-scan-data/add-role-assignment.png" alt-text="ロールの割り当てを追加する":::

1. 以上の手順を Azure Data Lake Storage Gen2 についても行います。

### <a name="scan-your-data-sources"></a>データ ソースをスキャンする

1. [Azure portal](https://portal.azure.com) で Azure Purview リソースに移動し、 *[Open Purview Studio]\(Purview Studio を開く\)* を選択します。 Purview Studio のホーム ページに自動的に移動します。

1. カタログの Web ページで **[Sources]\(ソース\)** を選択し、 **[登録]** を選択します。 次に、 **[Azure Blob Storage]** 、 **[続行]** の順に選択します。

   :::image type="content" source="media/tutorial-scan-data/add-blob-storage.png" alt-text="Blob Storage のリソースを登録する":::

1. **[Register sources]\(ソースの登録\)** ページで **名前** を入力します。 先ほどスタート キットで作成した Azure Blob Storage アカウントの **ストレージ アカウント名** を選択します。 このアカウントには、`<YourResourceGroupName>adcblob` という名前が付きます。 **[完了]** を選択します。

   :::image type="content" source="./media/tutorial-scan-data/register-azure-blob-storage.png" alt-text="Azure Blob Storage データ ソースを登録するための設定を示すスクリーンショット。" border="true":::

1. **[データ ソース]** マップ ビューの [Azure Blob Storage] タイルにある **[新しいスキャン]** を選択します。

   :::image type="content" source="./media/tutorial-scan-data/select-setup-scan.png" alt-text="データ ソースからスキャン設定を選択する方法を示すスクリーンショット。" border="true":::

1. **[スキャン]** ページでスキャン名を入力し、 **[資格情報]** ボックスの一覧から自分のマネージド ID を選択して、 **[続行]** を選択します。

   :::image type="content" source="media/tutorial-scan-data/scan-blob.png" alt-text="Azure Purview で BLOB ストレージをスキャンする":::

1. スキャンの範囲を個々の BLOB に設定することができます。 このチュートリアルでは、すべての資産のチェック ボックスをオンにしてスキャン対象とし、 **[続行]** を選択します。

   :::image type="content" source="media/tutorial-scan-data/scope-your-scan.png" alt-text="ストレージのスキャン範囲を設定する":::

1. 既定のスキャン ルール セットを選択し、 **[続行]** を選択します。

1. スキャン トリガーを設定することで、スキャンを定期的に実行することができます。 このチュートリアルでは、 **[Once]\(一回のみ\)** を選択し、 **[続行]** を選択します。

1. **[保存および実行]** を選択してスキャンを完了します。

1. 以上の手順を繰り返して、Azure Data Lake Storage Gen2 アカウントをスキャンします。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次の作業を行う方法を学びました。
> [!div class="checklist"]
>
> * スタート キット スクリプトを実行して、データ資産環境を設定する。
> * データをスキャンして Azure Purview カタログに取り込む。

次のチュートリアルに進んで、ホーム ページを閲覧し、資産を検索する方法について学習してください。

> [!div class="nextstepaction"]
> [ホーム ページを閲覧して資産を検索する](tutorial-asset-search.md)
