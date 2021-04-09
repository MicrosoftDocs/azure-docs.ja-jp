---
title: Azure Pipelines を使用して App Configuration に設定をプッシュする
description: Azure Pipelines を使用して App Configuration ストアにキー値をプッシュする方法について説明します
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 02/23/2021
ms.author: alkemper
ms.openlocfilehash: 7d343e07414dd1c3f9786c1684eb6f14d5f45e51
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101718184"
---
# <a name="push-settings-to-app-configuration-with-azure-pipelines"></a>Azure Pipelines を使用して App Configuration に設定をプッシュする

[Azure App Configuration Push](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) タスクは、キー値を構成ファイルから App Configuration ストアにプッシュします。 このタスクによって、App Configuration ストアから設定をプルしたり、App Configuration ストアに設定をプッシュしたりできるようになるため、パイプライン内で完全なサークル機能が有効になります。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- App Configuration リソース - [Azure portal](https://portal.azure.com)で無料リソースを作成する。
- Azure DevOps プロジェクト - [無料プロジェクトを作成する](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Azure App Configuration Push タスク - [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) から無料でダウンロードする。

## <a name="create-a-service-connection"></a>サービス接続を作成する

[サービス接続](/azure/devops/pipelines/library/service-endpoints)により、Azure DevOps プロジェクトから Azure サブスクリプション内のリソースにアクセスできます。

1. Azure DevOps で、ターゲットのパイプラインを含むプロジェクトに移動し、左下にある **[プロジェクトの設定]** を開きます。
1. **[パイプライン]** で、 **[サービス接続]** を選択し、右上にある **[新しいサービス接続]** を選択します。
1. **[Azure Resource Manager]** を選択します。
1. **[サービス プリンシパル (自動)]** を選択します。
1. サブスクリプションとリソースを入力します。 サービス接続に名前を付けます。

サービス接続が作成されたので、それに割り当てるサービス プリンシパルの名前を見つけます。 次の手順では、このサービス プリンシパルに新しいロールの割り当てを追加します。

1. **[プロジェクトの設定]**  >  **[サービス接続]** の順に移動します。
1. 前のセクションで作成したサービス接続を選択します。
1. **[サービス プリンシパルの管理]** を選択します。
1. 一覧表示された **[表示名]** をメモします。

## <a name="add-role-assignment"></a>ロールの割り当ての追加

タスクで App Configuration ストアにアクセスできるように、タスク内で使用する資格情報に、適切な App Configuration のロールの割り当てを割り当てます。

1. ターゲットの App Configuration ストアに移動します。 
1. 左側で、 **[アクセス制御 (IAM)]** を選択します。
1. 上部にある **[+ 追加]** を選択し、 **[ロールの割り当ての追加]** を選択します。
1. **[ロール]** で、 **[App Configuration データ所有者]** を選択します。 このロールを使用すると、タスクで App Configuration ストアに対して読み取りや書き込みを行うことができます。 
1. 前のセクションで作成したサービス接続に関連付けるサービス プリンシパルを選択します。
  
## <a name="use-in-builds"></a>ビルドでの使用

このセクションでは、Azure DevOps ビルド パイプラインで Azure App Configuration Push タスクを使用する方法について説明します。

1. **[パイプライン]**  >  **[パイプライン]** の順にクリックして、ビルド パイプラインのページに移動します。 ビルド パイプラインについては、[こちらのドキュメント](/azure/devops/pipelines/create-first-pipeline?tabs=tfs-2018-2)を参照してください。
      - 新しいビルド パイプラインを作成する場合は、パイプラインの右側にある **[アシスタントを表示する]** を選択し、 **[Azure App Configuration Push]** タスクを検索します。
      - 既存のビルド パイプラインを使用する場合は、パイプラインを編集するときに **[タスク]** タブに移動し、 **[Azure App Configuration Push]** タスクを検索します。
2. キー値を構成ファイルから App Configuration ストアにプッシュするために必要なタスクのパラメーターを構成します。 **[構成ファイルのパス]** パラメーターは、ファイル リポジトリのルートから始まります。
3. ビルドを保存してキューに登録します。 ビルド ログには、タスクの実行中に発生したすべてのエラーが表示されます。

## <a name="use-in-releases"></a>リリースでの使用

このセクションでは、Azure DevOps リリース パイプラインで Azure App Configuration Push タスクを使用する方法について説明します。

1. **[パイプライン]**  >  **[リリース]** の順に選択して、リリース パイプラインのページに移動します。 リリース パイプラインについては、[こちらのドキュメント](/azure/devops/pipelines/release)を参照してください。
1. 既存のリリース パイプラインを選択します。 ない場合は、 **[+ 新規]** を選択して、新規作成します。
1. 右上隅にある **[編集]** ボタンを選択して、リリース パイプラインを編集します。
1. **[ステージ]** を選択して、タスクを追加します。 ステージの詳細については、[こちら](/azure/devops/pipelines/release/environments)を参照してください。
1. そのジョブの **+** を選択し、 **[デプロイ]** タブで **[Azure App Configuration Push]** タスクを追加します。
1. キー値を構成ファイルから App Configuration ストアにプッシュするために必要なタスク内のパラメーターを構成します。 パラメーターの説明は、以下の「**パラメーター**」セクション、および各パラメーターの横にあるヒントを参照してください。
1. リリースを保存してキューに登録します。 リリース ログに、タスクの実行中に発生したすべてのエラーが表示されます。

## <a name="parameters"></a>パラメーター

App Configuration Push タスクによって、次のパラメーターが使用されます。

- **Azure サブスクリプション**:利用可能な Azure サービス接続を含むドロップダウン。 利用可能な Azure サービス接続の一覧を最新の情報に更新するには、テキストボックスの右側にある **[Azure サブスクリプションの更新]** ボタンを押します。
- **App Configuration の名前**: 選択したサブスクリプションで利用可能な Configuration ストアが読み込まれるドロップダウン。 利用可能な Configuration ストアの一覧を最新の情報に更新するには、テキストボックスの右側にある **[App Configuration 名の更新]** ボタンを押します。
- **構成ファイルのパス**: 構成ファイルへのパス。 ビルド成果物を参照して、構成ファイルを選択することができます (テキストボックスの右側にある `...` ボタン)。 サポートされているファイル形式は、yaml、json、properties です。
- **区切り記号**: .json および .yml ファイルをフラット化するために使用する区切り記号。
- **深さ**: .json および .yml ファイルをフラット化する深さ。
- **プレフィックス**: App Configuration ストアにプッシュされる各キーの先頭に追加される文字列。
- **ラベル**:App Configuration ストア内で、各キー値にラベルとして追加される文字列。
- **コンテンツの種類**: App Configuration ストア内で、各キー値にコンテンツの種類として追加される文字列。
- **タグ**:`{"tag1":"val1", "tag2":"val2"}` 形式の JSON オブジェクト。これは、App Configuration ストアにプッシュされる各キー値に追加されるタグを定義します。
- **指定されたプレフィックスとラベルを含むストア内の他のすべてのキー値を削除する**: 既定値は、**オフ** です。
  - **オン**: 指定されたプレフィックスとラベルの両方と一致する App Configuration ストア内のすべてのキー値を削除した後、構成ファイルの新しいキー値をプッシュします。
  - **オフ**: すべてのキー値を構成ファイルから App Configuration ストアにプッシュし、App Configuration ストア内の他のすべてのものをそのまま残します。

必要なパラメーターを入力した後、パイプラインを実行します。 指定された構成ファイル内のすべてのキー値が App Configuration にアップロードされます。

## <a name="troubleshooting"></a>トラブルシューティング

予期しないエラーが発生した場合、パイプライン変数 `system.debug` を `true`に設置すると、デバッグ ログを有効にすることができます。

## <a name="faq"></a>よく寄せられる質問

**複数の構成ファイルをアップロードするには、どうすればよいですか?**

同じパイプライン内で Azure App Configuration Push タスクの複数のインスタンスを作成して、複数の構成ファイルを App Configuration ストアにプッシュします。

**キー値を Configuration ストアにプッシュしようとすると、409 エラーが発生するのはなぜですか?**

409 競合エラー メッセージは、タスクで、App Configuration ストア内のロックされたキー値を削除または上書きしようとしたときに発生します。
