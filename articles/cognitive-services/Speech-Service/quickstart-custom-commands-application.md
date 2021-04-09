---
title: クイック スタート:カスタム コマンドを使用して音声アシスタントを作成する
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、Speech Studio を使用して基本的なカスタム コマンド アプリケーションを作成してテストします。
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.custom: references_regions
ms.openlocfilehash: e046f8cbf6fa0418244f20e9a0c6f75f6da34136
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102434629"
---
# <a name="create-a-voice-assistant-using-custom-commands"></a>カスタム コマンドを使用して音声アシスタントを作成する

このクイックスタートでは、Speech Studio を使用して基本的なカスタム コマンド アプリケーションを作成してテストします。 また、Windows クライアント アプリからこのアプリケーションにアクセスすることもできます。

## <a name="region-availability"></a>利用可能なリージョン
現時点では、カスタム コマンドは、次のリージョンで作成された音声サブスクリプションをサポートしています。
* 米国西部
* 米国西部 2
* 米国東部
* 米国東部 2
* 米国中西部
* 北ヨーロッパ
* 西ヨーロッパ
* 東アジア
* 東南アジア
* インド中部

## <a name="prerequisites"></a>前提条件

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">カスタム コマンドをサポートするリージョンに Azure Speech リソースを作成する。</a> サポートされているリージョンの一覧については、上記の「**利用可能なリージョン**」の項を参照してください。
> * サンプルの [Smart Room Lite](https://aka.ms/speech/cc-quickstart) JSON ファイルをダウンロードする。
> * [Windows 音声アシスタント クライアント](https://aka.ms/speech/va-samples-wvac)の最新バージョンをダウンロードする。

## <a name="go-to-the-speech-studio-for-custom-commands"></a>カスタム コマンドを使用するため Speech Studio にアクセスする

1. Web ブラウザーで [Speech Studio](https://speech.microsoft.com/) にアクセスします。
1. 資格情報を入力してポータルにサインインします。

   既定のビューは、音声サブスクリプションの一覧です。
   > [!NOTE]
   > サブスクリプションの選択ページが表示されない場合は、上部のバーの [設定] メニューで [Speech resources]\(音声リソース\) を選択すると表示されます。

1. 音声サブスクリプションを選択し、 **[Go to Studio]\(Studio にアクセス\)** を選択します。
1. **[カスタム コマンド]** を選択します。

   既定のビューは、選択したサブスクリプションにあるカスタム コマンド アプリケーションの一覧です。

## <a name="import-an-existing-application-as-a-new-custom-commands-project"></a>既存のアプリケーションを新しいカスタム コマンド プロジェクトとしてインポートする

1. **[新しいプロジェクト]** を選択してプロジェクトを作成します。

1. **[名前]** ボックスに、プロジェクト名として「`Smart-Room-Lite`」 (またはご自分で選んだ名前) を入力します。
1. **[言語]** 一覧で、 **[英語 (米国)]** を選択します。
1. **[ファイルの参照]** を選択し、参照ウィンドウで **SmartRoomLite.json** ファイルを選択します。

    > [!div class="mx-imgBorder"]
    > ![プロジェクトの作成](media/custom-commands/import-project.png)

1.  **[LUIS authoring resource]\(LUIS オーサリング リソース\)** の一覧でオーサリング リソースを選択します。 有効なオーサリング リソースがない場合は、 **[Create new LUIS authoring resource]\(新しい LUIS オーサリング リソースの作成\)** を選択して作成します。

    > [!div class="mx-imgBorder"]
    > ![リソースの作成](media/custom-commands/create-new-luis-resource.png)
    
    
    1. **[リソース名]** ボックスに、リソースの名前を入力します。
    1. **[リソース グループ]** の一覧でリソース グループを選択します。
    1. **[Location]\(場所\)** の一覧で場所を選択します。
    1. **[価格レベル]** の一覧でレベルを選択します。
    
    
    > [!NOTE]
    > リソース グループを作成するには、[リソース グループ] フィールドに目的のリソース グループ名を入力します。 **[作成]** を選択すると、リソース グループが作成されます。


1. 次に、 **[作成]** を選択してプロジェクトを作成します。
1. プロジェクトが作成されたら、そのプロジェクトを選択します。
これで、新しいカスタム コマンド アプリケーションの概要が表示されます。

## <a name="try-out-some-voice-commands"></a>音声コマンドをいくつか試す
1. 右ペインの上部にある **[トレーニング]** を選択します。
1. トレーニングが完了したら、 **[テスト]** を選択し、以下の発話を試します。
    - Turn on the tv (テレビをつけて)
    - Set the temperature to 80 degrees (温度を 80 度に設定して)
    - Turn it off (消して)
    - The tv (テレビ)
    - Set an alarm for 5 PM (アラームを午後 5 時に設定して)

## <a name="integrate-custom-commands-application-in-an-assistant"></a>カスタム コマンド アプリケーションをアシスタントに統合する
このアプリケーションに外部の Speech Studio からアクセスするには、アプリケーションを発行する必要があります。 アプリケーションを発行するには、予測 LUIS リソースを構成する必要があります。  

### <a name="update-prediction-luis-resource"></a>予測 LUIS リソースを更新する


1. 左ペインで **[設定]** を選択して、中央のペインで **[LUIS resources]\(LUIS リソース\)** を選択します。
1. 予測リソースを選択するか、 **[新しいリソースの作成]** を選択して作成します。
1. **[保存]** を選択します。
    
    > [!div class="mx-imgBorder"]
    > ![LUIS のリソースをセットする](media/custom-commands/set-luis-resources.png)

> [!NOTE]
> オーサリング リソースでは 1 か月あたり 1,000 の予測エンドポイント要求しかサポートされないため、カスタム コマンド アプリケーションを発行する前に LUIS 予測リソースを設定する必要があります。

### <a name="publish-the-application"></a>アプリケーションの発行

右ペインの上部にある **[発行]** を選択します。 発行が完了すると、新しいウィンドウが表示されます。 そこにある **[アプリケーション ID]** と **[Speech resource key]\(音声リソース キー\)** の値をメモしておきます。 この 2 つの値は、外部の Speech Studio からアプリケーションにアクセスできるようにするために必要です。

または、 **[設定]**  >  **[全般]** セクションを選択することでこれらの値を取得することもできます。

### <a name="access-application-from-client"></a>クライアントからアプリケーションにアクセスする

この記事の範囲では、前提条件の一部としてダウンロードした Windows 音声アシスタント クライアントを使用します。 フォルダーを解凍します。
1. **VoiceAssistantClient.exe** を起動します。
1. 新しい発行プロファイルを作成し、 **[接続プロファイル]** の値を入力します。 **[全般設定]** セクションで、 **[サブスクリプション キー]** (これはアプリケーションの公開時に保存した **[Speech resource key]\(音声リソース キー\)** 値と同じ)、 **[Subscription key region]\(サブスクリプション キーのリージョン\)** 、 **[Custom commands app ID]\(カスタム コマンド アプリの ID\)** の値を入力します。
    > [!div class="mx-imgBorder"]
    > ![WVAC プロファイルを作成するための [全般設定] セクションが強調表示されているスクリーンショット。](media/custom-commands/create-profile.png)
1. **[Save and Apply Profile]\(プロファイルを保存して適用する\)** を選択します。
1. 音声またはテキストを通じて次の入力を試してみます。
    > [!div class="mx-imgBorder"]
    > ![WVAC プロファイルの作成](media/custom-commands/conversation.png)


> [!TIP]
> **アクティビティ ログ** のエントリをクリックすると、カスタム コマンド サービスから送信されている未加工の応答を調べることができます。

## <a name="next-steps"></a>次のステップ

この記事では、既存のアプリケーションを使用しました。 次に、[操作方法に関するセクション](./how-to-develop-custom-commands-application.md)で、カスタム コマンド アプリケーションを最初から設計、開発、デバッグ、テスト、統合する方法について学習します。