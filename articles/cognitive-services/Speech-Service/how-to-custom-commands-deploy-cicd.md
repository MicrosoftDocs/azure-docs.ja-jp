---
title: Azure DevOps による継続的配置 (プレビュー)
titleSuffix: Azure Cognitive Services
description: この記事では、カスタム コマンド アプリケーションの継続的配置の設定方法について説明します。 継続的配置のワークフローをサポートするスクリプトを作成します。
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 686e342eb68e2e5c97bdbae9cd11bdff5819b5d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98927599"
---
# <a name="continuous-deployment-with-azure-devops"></a>Azure DevOps による継続的配置

この記事では、カスタム コマンド アプリケーションの継続的配置の設定方法について説明します。 CI/CD ワークフローをサポートするスクリプトが用意されています。

## <a name="prerequisite"></a>前提条件
> [!div class = "checklist"]
> * 開発用のカスタム コマンド アプリケーション (DEV)
> * 運用環境用のカスタム コマンド アプリケーション (PROD)
> * [Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-sign-up) へのサインアップ

## <a name="exportimportpublish"></a>エクスポート/インポート/発行

スクリプトは、[Cognitive Services 音声アシスタント - カスタム コマンド](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands)でホストされています。 bash ディレクトリ内のスクリプトをご自分のリポジトリに複製します。 必ず同じパスを保持してください。

### <a name="set-up-a-pipeline"></a>パイプラインの設定 

1. **Azure DevOps - パイプライン** にアクセスして、[新しいパイプライン] をクリックします。
1. **[接続]** セクションで、これらのスクリプトが配置されているリポジトリの場所を選択します。
1. **[選択]** セクションで、ご自分のリポジトリを選択します。
1. **[構成]** セクションで [Starter pipeline]\(スターター パイプライン\) を選択します。
1. 次に、YAML ファイルとエディターを取得し、"steps" セクションをこのスクリプトに置き換えます。

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Export source app'
      inputs:
        targetType: filePath
        filePath: ./bash/export.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(SourceAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Import to target app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish target app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```
    
1. これらのスクリプトでは、リージョン `westus2` を使用していることを前提としている点にご注意ください。そうでない場合は、それに応じてタスクの引数を更新します。

    > [!div class="mx-imgBorder"]
    > ![引数内のリージョン値を強調表示するスクリーンショット](media/custom-commands/cicd-new-pipeline-yaml.png)

1. [保存して実行] ボタンでドロップダウンを開き、[保存] をクリックします。

### <a name="hook-up-the-pipeline-with-your-application"></a>パイプラインをアプリケーションに接続する

1. パイプラインのメイン ページに移動します。
1. 右上隅のドロップダウンで、 **[パイプラインの編集]** を選択します。 YAML エディターが表示されます。 
1. 右上隅の [実行] ボタンの横にある **[変数]** を選択します。 **[新しい変数]** をクリックします。
1. 次の変数を追加します。
    
    | 変数 | 説明 |
    | ------- | --------------- | ----------- |
    | SourceAppId | DEV アプリケーションの ID |
    | TargetAppId | PROD アプリケーションの ID |
    | SubscriptionKey | 両方のアプリケーションで使用されるサブスクリプション キー |
    | カルチャ | アプリケーションのカルチャ (例: en-us) |

    > [!div class="mx-imgBorder"]
    > ![アクティビティの送信のペイロード](media/custom-commands/cicd-edit-pipeline-variables.png)

1. [実行] をクリックし、実行中の "ジョブ" をクリックします。 

    次のものを含む実行中のタスクの一覧が表示されます。"ソース アプリのエクスポート"、"ターゲット アプリへのインポート"、"ターゲット アプリのトレーニングと発行"

## <a name="deploy-from-source-code"></a>ソース コードからのデプロイ

アプリケーションの定義をリポジトリに保持する場合は、ソース コードからデプロイするためのスクリプトが用意されています。 このスクリプトは bash 内にあるため、Windows をお使いの場合は、[Linux サブシステム](/windows/wsl/install-win10)をインストールする必要があります。

スクリプトは、[Cognitive Services 音声アシスタント - カスタム コマンド](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands)でホストされています。 bash ディレクトリ内のスクリプトをご自分のリポジトリに複製します。 必ず同じパスを保持してください。

### <a name="prepare-your-repository"></a>リポジトリを準備する

1. アプリケーションのディレクトリを作成します。この例では、"apps" という名前のディレクトリを作成します。
1. 次の bash スクリプトの引数を更新して、実行します。 これによりアプリケーションのダイアログ モデルがファイル myapp.json にインポートされます。
    ```BASH
    bash/export.sh -r <region> -s <subscriptionkey> -c en-us -a <appid> -f apps/myapp.json
    ```
    | 引数 | 説明 |
    | ------- | --------------- | ----------- |
    | region | アプリケーションのリージョン (例: westus2)。 |
    | subscriptionkey | 音声リソースのサブスクリプション キー。 |
    | appid | エクスポートするカスタム コマンドのアプリケーション ID。 |

1. これらの変更をリポジトリにプッシュします。

### <a name="set-up-a-pipeline"></a>パイプラインの設定 

1. **Azure DevOps - パイプライン** にアクセスして、[新しいパイプライン] をクリックします。
1. **[接続]** セクションで、これらのスクリプトが配置されているリポジトリの場所を選択します。
1. **[選択]** セクションで、ご自分のリポジトリを選択します。
1. **[構成]** セクションで [Starter pipeline]\(スターター パイプライン\) を選択します。
1. 次に、YAML ファイルとエディターを取得し、"steps" セクションをこのスクリプトに置き換えます。

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Import app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ../apps/myapp.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```

    > [!NOTE]
    > これらのスクリプトでは、リージョン westus2 を使用していることを前提としています。そうでない場合は、それに応じてタスクの引数を更新します。

1. [保存して実行] ボタンでドロップダウンを開き、[保存] をクリックします。

### <a name="hook-up-the-pipeline-with-your-target-applications"></a>パイプラインをターゲット アプリケーションに接続する

1. パイプラインのメイン ページに移動します。
1. 右上隅のドロップダウンで、 **[パイプラインの編集]** を選択します。 YAML エディターが表示されます。 
1. 右上隅の [実行] ボタンの横にある **[変数]** を選択します。 **[新しい変数]** をクリックします。
1. 次の変数を追加します。

    | 変数 | 説明 |
    | ------- | --------------- | ----------- |
    | TargetAppId | PROD アプリケーションの ID |
    | SubscriptionKey | 両方のアプリケーションで使用されるサブスクリプション キー |
    | カルチャ | アプリケーションのカルチャ (例: en-us) |

1. [実行] をクリックし、実行中の "ジョブ" をクリックします。
    次のものを含む実行中のタスクの一覧が表示されます。"アプリのインポート" と "アプリのトレーニングと発行"

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [GitHub のサンプルを参照](https://aka.ms/speech/cc-samples)
