---
title: Azure Percept DK と Azure Percept Audio を使用して音声アシスタントを作成する
description: コーディングなしで音声ソリューションを作成し、Azure Percept DK にデプロイする方法について説明します。
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 76333e11916641be71c72ce6142cd59b496a17e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023165"
---
# <a name="create-a-voice-assistant-with-azure-percept-dk-and-azure-percept-audio"></a>Azure Percept DK と Azure Percept Audio を使用して音声アシスタントを作成する

このチュートリアルでは、Azure Percept DK と Azure Percept Audio で使用する音声アシスタントをテンプレートから作成します。 音声アシスタントのデモは [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) 内で動作し、音声で制御される選りすぐりの仮想オブジェクトを含んでいます。 オブジェクトを制御するには、まずキーワード (デバイスを目覚めさせる単語または短いフレーズ) を発話し、続けてコマンドを発話します。 それぞれのテンプレートは、具体的な一連のコマンドに応答します。

このガイドでは、デバイスのセットアップから、音声アシスタントと必要な [Speech Services](../cognitive-services/speech-service/overview.md) リソースの作成、音声アシスタントのテスト、キーワードの構成、カスタム キーワードの作成までのプロセスを紹介します。

## <a name="prerequisites"></a>前提条件

- Azure Percept DK (開発キット)
- Azure Percept Audio
- 3\.5 mm オーディオ ジャックに接続できるスピーカーまたはヘッドホン (オプション)
- [Azure サブスクリプション](https://azure.microsoft.com/free/)
- [Azure Percept DK セットアップ エクスペリエンス](./quickstart-percept-dk-set-up.md): 開発キットを Wi-Fi ネットワークに接続し、IoT ハブを作成して、開発キットを IoT ハブに接続済みであること
- [Azure Percept Audio の設定](./quickstart-percept-audio-setup.md)


## <a name="create-a-voice-assistant-using-an-available-template"></a>提供されているテンプレートを使用して音声アシスタントを作成する

1. [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) に移動します。

1. **[Demos & tutorials]\(デモとチュートリアル\)** タブを開きます。

    :::image type="content" source="./media/tutorial-no-code-speech/portal-overview.png" alt-text="Azure portal ホームページのスクリーンショット。":::

1. **[Speech tutorials and demos]\(音声のチュートリアルとデモ\)** の **[Try out voice assistant templates]\(音声アシスタント テンプレートを試す\)** をクリックします。 画面の右側にウィンドウが開きます。

1. このウィンドウで次の操作を行います。

    1. **[IoT Hub]** ドロップダウン メニューで、開発キットの接続先となる IoT ハブを選択します。

    1. **[Device]\(デバイス\)** ドロップダウン メニューから自分の開発キットを選択します。

    1. 提供されているいずれかの音声アシスタント テンプレートを選択します。

    1. **[I agree to terms & conditions for this project]\(このプロジェクトの使用条件に同意する\)** チェック ボックスをオンにします。

    1. **Create** をクリックしてください。

    :::image type="content" source="./media/tutorial-no-code-speech/template-creation.png" alt-text="音声アシスタント テンプレートの作成のスクリーンショット。":::

1. **[作成]** をクリックすると、音声のテーマ リソースを作成するための別のウィンドウが開きます。 このウィンドウで次の操作を行います。

    1. **[サブスクリプション]** ボックスで Azure サブスクリプションを選択します。

    1. **[リソース グループ]** ドロップダウン メニューから任意のリソース グループを選択します。 音声アシスタントで使用する新しいリソース グループを作成する場合は、ドロップダウン メニューの下にある **[作成]** をクリックしてプロンプトに従います。

    1. **[Application prefix]\(アプリケーションのプレフィックス\)** に名前を入力します。 これが、プロジェクトとカスタム コマンド名のプレフィックスになります。

    1. **[リージョン]** で、リソースのデプロイ先となるリージョンを選択します。

    1. **[LUIS prediction pricing tier]\(LUIS 予測価格レベル\)** で **[Standard]** を選択します (Free レベルでは音声要求はサポートされません)。

    1. **[作成]** ボタンをクリックします。 音声アシスタント アプリケーションのリソースは、ご利用のサブスクリプションにはデプロイされません。

        > [!WARNING]
        > ポータルによってリソースのデプロイが完了するまで、ウィンドウは閉じ **ない** でください。 ウィンドウを途中で閉じると、音声アシスタントに予期しない動作が生じる可能性があります。 リソースがデプロイされると、デモが表示されます。

    :::image type="content" source="./media/tutorial-no-code-speech/resource-group.png" alt-text="サブスクリプションとリソース グループの選択ウィンドウのスクリーンショット。":::

## <a name="test-out-your-voice-assistant"></a>音声アシスタントをテストする

音声アシスタントと対話するには、キーワードに続けてコマンドを発話します。 そのキーワードを Ear SoM が認識すると、デバイスはチャイム (スピーカーまたはヘッドホンが接続されている場合に聞こえます) を出力し、LED が青色で点滅します。 コマンドが処理されている間は LED が濃い青色に変わります。 コマンドに対する音声アシスタントの応答は、デモ ウィンドウにテキストで出力されるほか、スピーカーまたはヘッドホンから音声で出力されます。 既定のキーワード ( **[Custom Keyword]\(カスタム キーワード\)** の横に表示されます) は "Computer" に設定され、各テンプレートには、対応している一連のコマンドが含まれています。これらのコマンドを使用することで、デモ ウィンドウの仮想オブジェクトと対話することができます。 たとえば、Hospitality (接客) デモまたは Healthcare (医療) デモを使用している場合、"Computer, turn on TV (コンピューター、テレビを付けて)" と発話すると、仮想テレビがオンになります。

:::image type="content" source="./media/tutorial-no-code-speech/hospitality-demo.png" alt-text="Hospitality (接客) デモ ウィンドウのスクリーンショット。":::

### <a name="hospitality-and-healthcare-demo-commands"></a>Hospitality (接客) デモと Healthcare (医療) デモのコマンド

Healthcare (医療) と Hospitality (接客) のデモにはどちらも、対話できる仮想テレビ、ライト、ブラインド、サーモスタットがあります。 次のコマンド (およびその他のバリエーション) がサポートされています。

* "Turn on/off the lights. (照明を付けて、照明を消して)"
* "Turn on/off the TV. (テレビを付けて、テレビを消して)"
* "Turn on/off the AC. (エアコンを付けて、エアコンを消して)"
* "Open/close the blinds. (ブラインドを開いて、ブラインドを閉じて)"
* "Set temperature to X degrees. (温度を X 度に設定して)" (X は 75 など、目的の温度です。)

:::image type="content" source="./media/tutorial-no-code-speech/healthcare-demo.png" alt-text="Healthcare (医療) デモ ウィンドウのスクリーンショット。":::

### <a name="automotive-demo-commands"></a>Automotive (自動車) デモのコマンド

Automotive (自動車) デモには、対話的に操作可能な仮想シート ヒーター、デフロスター、サーモスタットが備わっています。 次のコマンド (およびその他のバリエーション) がサポートされています。

* "Turn on/off the defroster. (デフロスターをオンにして、デフロスターをオフにして)"
* "Turn on/off the seat warmer. (シート ヒーターをオンにして、シート ヒーターをオフにして)"
* "Set temperature to X degrees. (温度を X 度に設定して)" (X は 75 など、目的の温度です。)
* "Increase/decrease the temperature by Y degrees. (温度を Y 度上げて、温度を Y 度下げて)"


:::image type="content" source="./media/tutorial-no-code-speech/auto-demo.png" alt-text="Automotive (自動車) デモ ウィンドウのスクリーンショット。":::

### <a name="inventory-demo-commands"></a>Inventory (在庫) デモのコマンド

Inventory (在庫) デモには、仮想在庫アプリと共に、対話的に操作することができる青、黄、緑の各仮想ボックスが含まれています。 次のコマンド (およびその他のバリエーション) がサポートされています。

* "Add/remove X boxes. (X 箱追加して、X 箱移動して)" (X は 4 など、箱の数です。)
* "Order/ship X boxes. (X 箱注文して、X 箱出荷して)"
* "How many boxes are in stock? (在庫に残っている箱はいくつ?)"
* "Count Y boxes. (Y 色の箱を数えて)" (Y は黄など、箱の色です。)
* "Ship everything in stock. (在庫に残っているものをすべて出荷して)"


:::image type="content" source="./media/tutorial-no-code-speech/inventory-demo.png" alt-text="Inventory (在庫) デモ ウィンドウのスクリーンショット。":::

## <a name="configure-your-keyword"></a>キーワードを構成する

音声アシスタント アプリケーション用にキーワードをカスタマイズできます。

1. デモ ウィンドウの **[カスタム キーワード]** の横にある **[変更]** をクリックします。

1. 使用可能なキーワードの 1 つを選択します。 一連のサンプル キーワードと、自分で作成したカスタム キーワードの中から選択できます。

1. **[保存]** をクリックします。

### <a name="create-a-custom-keyword"></a>カスタム キーワードを作成する

音声アプリケーション用に独自のキーワードを作成できます。 カスタム キーワードのトレーニングは、わずか数分で完了します。

1. デモ ウィンドウの上部の近くの **[カスタム キーワードの作成]** をクリックします。 

1. 目的のキーワードを入力します。1 つの単語または短い語句を指定できます。

1. **[音声リソース]** (これは、デモ ウィンドウの **[カスタム コマンド]** の横に一覧表示され、アプリケーションのプレフィックスを含みます) を選択します。

1. **[保存]** をクリックします。 

## <a name="create-a-custom-command"></a>カスタム コマンドを作成する

ポータルには、既存の音声リソースを使用してカスタム コマンドを作成するための機能も用意されています。 "カスタム コマンド" とは、既存のアプリケーション内の特定のコマンドではなく、音声アシスタント アプリケーション自体を指します。 カスタム コマンドを作成すると、新しい音声プロジェクトが作成されます。このプロジェクトは、[Speech Studio](https://speech.microsoft.com/) でさらに開発を行う必要があります。

デモ ウィンドウ内から新しいカスタム コマンドを作成するには、ページの上部にある **[+ Create Custom Command]\(+ カスタム コマンドの作成\)** をクリックし、次の手順を実行します。

1. カスタム コマンドの名前を入力します。

1. プロジェクトの説明を入力します (省略可)。

1. 優先する言語を選択します。

1. 音声リソースを選択します。

1. LUIS リソースを選択します。

1. LUIS 作成リソースを選択するか、新たに作成します。

1. **Create** をクリックしてください。

:::image type="content" source="./media/tutorial-no-code-speech/custom-commands.png" alt-text="カスタム コマンドの作成ウィンドウのスクリーンショット。":::

カスタム コマンドを作成したら、[Speech Studio](https://speech.microsoft.com/) に移動して、さらに開発を行う必要があります。 Speech Studio を開いてもカスタム コマンドが表示されない場合は、次の手順を実行します。

1. Azure Percept Studio の左側のメニュー パネルで、 **[AI プロジェクト]** の **[Speech]\(音声\)** をクリックします。

1. **[Commands]\(コマンド\)** タブを選択します。

    :::image type="content" source="./media/tutorial-no-code-speech/ai-projects.png" alt-text="編集可能なカスタム コマンドの一覧のスクリーンショット。":::

1. 開発するカスタム コマンドを選択します。 Speech Studio でプロジェクトが開きます。

    :::image type="content" source="./media/tutorial-no-code-speech/speech-studio.png" alt-text="Speech Studio のホーム画面のスクリーンショット。":::

カスタム コマンドの開発について詳しくは、[Speech Service のドキュメント](../cognitive-services/speech-service/custom-commands.md)を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="voice-assistant-was-created-but-does-not-respond-to-commands"></a>音声アシスタントは作成されましたが、コマンドに応答しません

インターポーザー ボードの LED ライトを確認してください。

* 青色の 3 つのライトが点灯状態である場合、音声アシスタントの準備が完了し、キーワードを待機していることを示します。
* 中心の LED (L02) が白色である場合、開発キットの初期化は完了していますが、キーワードを使用した構成が必要です。
* 中央の LED (L02) が白色で点滅している場合、Audio SoM はまだ初期化を完了していません。 初期化は、完了までに数分かかる場合があります。

LED インジケーターの詳細については、[LED に関する記事](./audio-button-led-behavior.md)を参照してください。

### <a name="voice-assistant-does-not-respond-to-a-custom-keyword-created-in-speech-studio"></a>Speech Studio で作成したカスタムキーワードに音声アシスタントが応答しません

音声モジュールが古いと、この症状が発生することがあります。 次の手順に従って、音声モジュールを最新バージョンに更新してください。

1. Azure Percept Studio ホームページの左側のメニュー パネルにある **[Devices]\(デバイス\)** をクリックします。

1. デバイスを探して選択します。

    :::image type="content" source="./media/tutorial-no-code-speech/devices.png" alt-text="Azure Percept Studio のデバイス一覧のスクリーンショット。":::

1. デバイス ウィンドウで **[Speech]\(音声\)** タブを選択します。

1. 音声モジュールのバージョンを確認します。 更新プログラムが利用可能な場合は、バージョン番号の横に **[Update]\(更新\)** ボタンが表示されます。

1. **[Update]\(更新\)** をクリックして、音声モジュールの更新プログラムをデプロイします。 通常、更新プロセスは、完了までに 2 分から 3 分かかります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

音声アシスタント アプリケーションの作業が完了したら、次の手順に従って、このチュートリアルでデプロイした音声リソースをクリーンアップしてください。

1. [Azure portal](https://portal.azure.com) の左側のメニュー パネルから **[リソース グループ]** を選択するか、検索バーにそのように入力します。

    :::image type="content" source="./media/tutorial-no-code-speech/azure-portal.png" alt-text="左側のメニュー パネルとリソース グループが表示されている Azure portal ホームページのスクリーンショット。":::

1. リソース グループを選択します。

1. アプリケーションのプレフィックスが含まれている 6 つのリソースをすべて選択し、上部のメニュー パネルにある **[削除]** アイコンをクリックします。
\
    :::image type="content" source="./media/tutorial-no-code-speech/select-resources.png" alt-text="削除対象として選択された音声リソースのスクリーンショット。":::

1. 削除を確定するには、確認ボックスに「**yes**」と入力し、正しいリソースが選択されていることを確認して、 **[削除]** をクリックします。

    :::image type="content" source="./media/tutorial-no-code-speech/delete-confirmation.png" alt-text="削除の確認ウィンドウのスクリーンショット。":::

> [!WARNING]
> 削除する音声リソースと共に作成されたカスタム キーワードがすべて削除され、音声アシスタントのデモは機能しなくなります。

## <a name="next-steps"></a>次の手順

コーディングなしで音声ソリューションを作成したら、Azure Percept DK 向けに、[コーディングなしでビジョン ソリューション](./tutorial-nocode-vision.md)を作成してみましょう。