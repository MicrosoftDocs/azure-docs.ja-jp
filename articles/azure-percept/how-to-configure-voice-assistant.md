---
title: Azure IoT Hub を使用した音声アシスタント アプリケーションの構成
description: Azure IoT Hub を使用した音声アシスタント アプリケーションの構成
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to
ms.openlocfilehash: b22ef4ee0a8b5978bb2ec1c02fadf368815f3014
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095784"
---
# <a name="configure-voice-assistant-application-using-azure-iot-hub"></a>Azure IoT Hub を使用した音声アシスタント アプリケーションの構成

この記事では、IoT Hub を使用して、音声アシスタント アプリケーションを構成する方法について説明します。 デモ テンプレートを使用して、音声アシスタントを作成するプロセスの手順を説明したチュートリアルについては、[Azure Percept Studio と Azure Percept Audio による、コーディングなしの音声アシスタントの作成](./tutorial-no-code-speech.md)に関するページを参照してください。

## <a name="update-your-voice-assistant-configuration"></a>音声アシスタント構成を更新する

1. [Azure portal](https://portal.azure.com) を開き、検索バーに「**IoT Hub**」と入力します。 アイコンをクリックして、[IoT Hub] ページを開きます。

1. [IoT Hub] ページで、デバイスがプロビジョニングされた IoT Hub を選択します。

1. 左側のナビゲーション メニューの **[自動デバイス管理]** で **[IoT Edge]** を選択すると、IoT Hub に接続されているすべてのデバイスが表示されます。

1. 音声アシスタント アプリケーションのデプロイ先となったデバイスを選択します。

1. **[モジュールの設定]** をクリックします。

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/set-modules.png" alt-text="[モジュールの設定] が強調表示されたデバイス ページのスクリーンショット。":::

1. **[コンテナー レジストリの資格情報]** セクションに、次のエントリが表示されていることを確認します。 必要に応じて資格情報を追加します。

    |名前|Address|ユーザー名|Password|
    |----|-------|--------|--------|
    |azureedgedevices|azureedgedevices.azurecr.io|devkitprivatepreviewpull|

1. **[IoT Edge モジュール]** セクションで、 **[azureearspeechclientmodule]** を選択します。

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/modules.png" alt-text="デバイス上のすべての IoT Edge モジュールの一覧を示すスクリーンショット。":::

1. **[モジュール設定]** タブをクリックします。次の構成を確認します。

    イメージの URI|再起動ポリシー|必要な状態
    ---------|--------------|--------------
    mcr.microsoft.com/azureedgedevices/azureearspeechclientmodule:preload-devkit|常時|実行中

    設定が一致していない場合は、それらを編集して **[更新]** をクリックします。

1. **[環境変数]** タブをクリックします。環境変数が定義されていないことを確認します。

1. **[モジュール ツイン設定]** タブをクリックします。 **[speechConfigs]** セクションを次のように更新します。

    ```
    "speechConfigs": {
        "appId": "<Application id for custom command project>",
        "key": "<Speech Resource key for custom command project>",
        "region": "<Region for the speech service>",
        "keywordModelUrl": "https://aedsamples.blob.core.windows.net/speech/keyword-tables/computer.table",
        "keyword": "computer"
    }
    ```

    > [!NOTE]
    > 上記で使用されているキーワードは、一般公開されている既定のキーワードです。 独自のものを使用する場合は、作成済みのテーブル ファイルを BLOB ストレージにアップロードすることで、独自のカスタム キーワードを追加できます。 BLOB ストレージは、匿名コンテナー アクセスまたは匿名 BLOB アクセスで構成する必要があります。

## <a name="how-to-find-out-appid-key-and-region"></a>appId、キー、リージョンを見つける方法

**appID**、**キー**、**リージョン** を見つけるには、「[Speech Studio](https://speech.microsoft.com/)」に移動します。

1. サインインして、適切な音声リソースを選択します。
1. **Speech Studio** ホーム ページで、 **[音声アシスタント]** の下にある **[Custom Commands]** をクリックします。
1. ターゲット プロジェクトを選択します。

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/project.png" alt-text="Speech Studio のプロジェクト ページのスクリーンショット。":::

1. 左側のメニュー パネルで **[設定]** をクリックします。
1. **appID** と **キー** は **[全般]** タブにあります。

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/general-settings.png" alt-text="Speech プロジェクトの全般設定のスクリーンショット。":::

1. **リージョン** を見つけるには、設定内の **[LUIS のリソース]** タブを開きます。 **[リソースの作成]** の選択に、リージョン情報が含まれます。

    :::image type="content" source="./media/manage-voice-assistant-using-iot-hub/luis-resources.png" alt-text="Speech プロジェクト LUIS リソースのスクリーンショット。":::

1. **speechConfigs** 情報を入力したら、 **[更新]** をクリックします。

1. **[モジュールの設定]** ページの上部の **[ルート]** タブをクリックします。 次の値を持つルートがあることを確認します。

    ```
    FROM /messages/modules/azureearspeechclientmodule/outputs/* INTO $upstream
    ```

    存在しない場合はルートを追加します。

1. **[Review + Create]\(レビュー + 作成\)** をクリックします。

1. **Create** をクリックしてください。


## <a name="next-steps"></a>次のステップ

音声アシスタントの構成を更新したら、Azure Percept Studio のデモに戻り、アプリケーションを操作します。

