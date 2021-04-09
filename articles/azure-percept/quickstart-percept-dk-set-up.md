---
title: Azure Percept DK を設定する
description: 開発キットを Azure に接続して、最初の AI モデルをデプロイする
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/17/2021
ms.custom: template-quickstart
ms.openlocfilehash: 8cecd1b57395fa51fd95c824e88885d9c3ae3f09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023301"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>Azure Percept DK を設定し、最初の AI モデルをデプロイする

Azure Percept DK のセットアップ エクスペリエンスを完了し、開発キットを構成して初めての AI モデルをデプロイします。 ご使用の Azure アカウントが Azure Percept に適合していることを確認したら、次の作業を行います。

- 開発キットを Wi-Fi ネットワークに接続する
- 開発キットへのリモート アクセス用の SSH ログインを設定する
- Azure Percept で使用する新しい IoT ハブを作成する
- 開発キットを IoT ハブと Azure アカウントに接続する

このプロセスの過程で問題が発生した場合に実行できる解決策については、[セットアップのトラブルシューティング ガイド](./how-to-troubleshoot-setup.md)を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure Percept DK (開発キット)。
- Wi-Fi 機能と Web ブラウザーを備えた Windows、Linux、OS X ベースのホスト コンピューター。
- アクティブなサブスクリプションが含まれる Azure アカウント。 [アカウントは無料で作成できます。](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure アカウントには、サブスクリプションにおける **所有者** または **共同作成者** ロールが必要です。 Azure アカウントのロールを確認するには、以下の手順に従います。 Azure ロールの定義の詳細については、[Azure ロールベースのアクセス制御に関するドキュメント](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)を参照してください。

    > [!CAUTION]
    > 複数の Azure アカウントがある場合、ブラウザーでは別のアカウントの資格情報がキャッシュされることがあります。 混乱を防ぐために、使用していないブラウザー ウィンドウはすべて閉じ、[Azure portal](https://portal.azure.com/) にログインしてからセットアップ エクスペリエンスを開始するようお勧めします。 正しいアカウントでサインインしていることを確認する方法の詳細については、[セットアップのトラブルシューティング ガイド](./how-to-troubleshoot-setup.md)を参照してください。

### <a name="check-your-azure-account-role"></a>Azure アカウント ロールを確認する

Azure アカウントがサブスクリプションにおける "所有者" または "共同作成者" であるかどうかを確認するには、これらの手順に従ってください。

1. [Azure portal](https://portal.azure.com/) にアクセスし、Azure Percept で使用するのと同じ Azure アカウントでログインします。

1. **[サブスクリプション]** アイコン (黄色の鍵の形のアイコン) をクリックします。

1. 一覧からご利用のサブスクリプションを選択します。 ご自分のサブスクリプションが表示されない場合は、正しい Azure アカウントでサインインしているかどうかを確認してください。 新しいサブスクリプションを作成するには、[これらの手順](../cost-management-billing/manage/create-subscription.md)に従います。

1. サブスクリプション メニューで、 **[アクセス制御 (IAM)]** を選択します。
1. **[View my access]\(アクセス権の表示\)** をクリックします。
1. ロールをチェックします。
    - 自分のロールが **閲覧者** として表示されている場合、またはロールを表示するアクセス許可がないというメッセージが表示される場合は、組織で必要なプロセスに従ってアカウント ロールを昇格させる必要があります。
    - 自分のロールが **所有者** または **共同作成者** として表示される場合は、ご使用のアカウントが Azure Percept に対応しており、セットアップ エクスペリエンスに進むことができます。

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Azure Percept DK セットアップ エクスペリエンスを開始する

1. ホスト コンピューターを開発キットの Wi-Fi アクセス ポイントに直接接続します。 他の Wi-Fi ネットワークへの接続と同様に、お使いのコンピューターでネットワークとインターネットの設定を開き、次のネットワークをクリックして、プロンプトが表示されたらネットワーク パスワードを入力します。

    - **ネットワーク名**: 開発キットのオペレーティング システムのバージョンによりますが、Wi-Fi アクセス ポイントの名前は **scz-xxxx** または **apd-xxxx** のいずれかです ("xxxx" は開発キットの MAC アドレスの最後の 4 桁です)
    - **パスワード**: 開発キットに付属のウェルカム カードに記載されています

    > [!WARNING]
    > Azure Percept DK Wi-Fi のアクセスポイントに接続している間は、ホスト コンピューターのインターネット接続が一時的に失われます。 アクティブなビデオ会議通話や Web ストリーミングといったネットワークベースのエクスペリエンスは中断されます。

1. 開発キットの Wi-Fi アクセス ポイントに接続すると、ホスト コンピューターで自動的に新しいブラウザー ウィンドウのアドレス バーに「**your.new.device/** 」が入力され、セットアップ エクスペリエンスが起動します。 タブが自動的に開かない場合は、[http://10.1.1.1](http://10.1.1.1) に移動してセットアップ エクスペリエンスを起動してください。 Azure Percept で使用する予定の Azure アカウント資格情報でブラウザーがサインインしていることを確認してください。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="ウェルカム ページ。":::

    > [!CAUTION]
    > セットアップ エクスペリエンスの Web サービスは、無使用状態が 30 分間続くとシャットダウンされます。 このような場合は、開発キットの Wi-Fi アクセス ポイントに関する接続の問題を回避するために、開発キットを再起動してください。

## <a name="complete-the-azure-percept-dk-setup-experience"></a>Azure Percept DK セットアップ エクスペリエンスを完了する

1. **ようこそ** 画面で、 **[次へ]** をクリックします。

1. **[ネットワーク接続]** ページで **[新しい Wi-Fi ネットワークに接続する]** をクリックします。

    既に開発キットを Wi-Fi ネットワークに接続している場合は、 **[スキップ]** をクリックします。

1. 使用できるネットワークの一覧から自分の Wi-Fi ネットワークを選択し、 **[接続]** をクリックします。 確認を求められたらネットワーク パスワードを入力します。

1. 開発キットが自分の選択したネットワークに正しく接続されると、開発キットに割り当てられた IPv4 アドレスがページに表示されます。 **ページに表示される IPv4 アドレスを書き留めてください。** トラブルシューティングやデバイスの更新を目的として開発キットに SSH で接続する際、この IP アドレスが必要になります。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="IP アドレスをコピーします。":::

    > [!NOTE]
    > この IP アドレスは、デバイスを起動するたびに変わる可能性があります。

1. 使用許諾契約書を読み、 **[使用許諾契約書を読んで同意しました]** を選択し (契約の一番下までスクロールする必要があります)、 **[次へ]** をクリックします。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="EULA に同意します。":::

1. SSH アカウント名とパスワードを入力し、**後で使用するためにログイン情報を書き留め** ておきます。

    > [!NOTE]
    > SSH (Secure Shell) は、ホスト コンピューターを介してリモートで開発キットに接続することを可能にするネットワーク プロトコルです。

1. 次のページで、 **[新しいデバイスとしてセットアップ]** をクリックして、自分の Azure アカウント内に新しいデバイスを作成します。

1. **[コピー]** をクリックしてデバイス コードをコピーします。 その後、 **[Azure にログイン]** をクリックします。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="デバイス コードをコピーします。":::

1. 新しいブラウザー タブが開いて、 **[コードの入力]** というウィンドウが表示されます。 ウィンドウにコードを貼り付け、 **[次へ]** をクリックします。 セットアップ エクスペリエンスがある **[ようこそ]** タブは閉じないでください。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="デバイス コードを入力します。":::

1. 開発キットで使用する Azure アカウントの資格情報を使用して Azure Percept にサインインします。 完了してもブラウザー タブは開いたままにしておいてください。

    > [!CAUTION]
    > ブラウザーによって、他の資格情報が自動的にキャッシュされる場合もあります。 正しいアカウントでサインインしていることを再確認してください。

    デバイスで Azure Percent に正常にサインインしたら、 **[ようこそ]** タブに戻ってセットアップ エクスペリエンスを続行します。

1. **[ようこそ]** タブに **[デバイスを Azure IoT ハブに割り当てる]** ページが表示されたら、次のいずれかの操作を実行します。

    - Azure Percept で使用したい IoT ハブが既にあり、このページに表示されている場合は、それを選択して手順 15. に進みます。
    - IoT ハブがない場合、または新しいものを作成する場合は、 **[新しい Azure IoT ハブを作成する]** をクリックします。

    > [!IMPORTANT]
    > IoT ハブはあるものの、一覧に表示されていない場合、間違った資格情報で Azure Percept にサインインしている可能性があります。 ヘルプについては、[セットアップのトラブルシューティング ガイド](./how-to-troubleshoot-setup.md)を参照してください。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="IoT Hub の選択を選択します。":::

1. 新しい IoT ハブを作成するために、次のフィールドに入力します。

    - Azure Percept で使用する Azure サブスクリプションを選択します。
    - 既存のリソース グループを選択します。 存在しない場合は、 **[新規作成]** をクリックし、画面の指示に従います。
    - 物理的に最も近い Azure リージョンを選択します。
    - 新しい IoT ハブに名前を付けます。
    - S1 (Standard) 価格レベルを選択します。

    > [!NOTE]
    > 最終的に、エッジ AI アプリケーションの[メッセージ スループット](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling#message-throughput)を引き上げる必要が生じた場合は、Azure portal でいつでも [IoT ハブを上位の Standard レベルにアップグレード](https://docs.microsoft.com/azure/iot-hub/iot-hub-upgrade)できます。 B および F レベルでは、Azure Percept はサポートされません。

1. IoT ハブのデプロイには数分かかる場合があります。 デプロイが完了したら、 **[登録]** をクリックします。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="IoT Hub が正常にデプロイされました。":::

1. 開発キットのデバイス名を入力し、 **[次へ]** をクリックします。

1. デバイス モジュールがダウンロードされるまで待ちます。これには数分かかります。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="セットアップの終了処理を行います。":::

1. **[デバイスのセットアップが完了しました]** ページが表示されたら、開発キットは正常に IoT ハブにリンクされ、必要なソフトウェアがダウンロードされています。 page, your dev kit has successfully linked to your IoT Hub and downloaded the necessary software. 開発キットが Wi-Fi アクセス ポイントから自動的に切断されて、これら 2 つの通知が表示されます。

    <!---
    > [!NOTE]
    > The onboarding process and connection to the device Wifi access to your host computer shuts down at this point, but your dev kit will stay connected to the internet.   You can restart the onboarding experience with a dev kit reboot, which will allow you to go back through the onboarding and reconnect the device to a different IOT hub associated with the same or a different Azure Subscription..
    --->

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="セットアップ エクスペリエンスの切断に関する警告。":::

1. 手順 2. で開発キットを接続した Wi-Fi ネットワークにホスト コンピューターを接続します。

1. **[Azure portal に進む]** をクリックします。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-azure-portal-continue.png" alt-text="Azure Percept Studio に移動します。":::

## <a name="view-your-dev-kit-video-stream-and-deploy-a-sample-model"></a>開発キットのビデオ ストリームを表示してサンプル モデルをデプロイする

1. [Azure Percept Studio の [概要] ページ](https://go.microsoft.com/fwlink/?linkid=2135819)は、初歩および高度の両方のエッジ AI ソリューション開発のための、多くのさまざまなワークフローにアクセスするための開始点となります 開始するには、左側のメニューの **[デバイス]** をクリックします。

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="デバイス一覧を表示します。":::

1. 開発キットが **[接続済み]** として表示されていることを確認し、それをクリックしてデバイス ページを表示します。

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="デバイスを選択します。":::

1. **[View your device stream]\(デバイス ストリームの表示\)** をクリックします。 デバイスのビデオ ストリームを表示するのが初めてである場合、新しいモデルがデプロイされているという通知が右上隅に表示されます。 これには数分かかることがあります。

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-1-start-video-stream.png" alt-text="ビデオ ストリームを表示します。":::

    モデルがデプロイされると、 **[ストリームを表示します]** リンクを含んだもう 1 つの通知が表示されます。 リンクをクリックすると、Azure Percept Vision カメラからのビデオ ストリームが新しいブラウザー ウィンドウに表示されます。 開発キットには、多くの一般的な物体の物体検出を自動的に実行する AI モデルが事前に読み込まれています。

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="物体検出を確認します。":::

1. Azure Percept Studio には、サンプル AI モデルも数多く備わっています。 開発キットにサンプル モデルをデプロイするためには、デバイス ページに戻って **[サンプル モデルのデプロイ]** をクリックします。

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-04-explore-prebuilt.png" alt-text="事前構築済みモデルについて説明します。":::

1. ライブラリからサンプル モデルを選択し、 **[デバイスへのデプロイ]** をクリックします。

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="動作中の物体検出を確認します。":::

1. モデルが正常にデプロイされると、 **[ストリームを表示します]** リンクを含んだ通知が画面の右上隅に表示されます。 モデル推論の動作を確認するには、通知のリンクをクリックするかデバイス ページに戻り、 **[デバイス ストリームの表示]** をクリックします。 これまで開発キットで実行されていたモデルはすべて、新しいモデルに置き換えられます。

## <a name="video-walkthrough"></a>ビデオ チュートリアル

上記の手順を視覚的に説明するチュートリアルについては、次の動画をご覧ください (セットアップ エクスペリエンスは 0:50 から始まります)。

</br>

> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [コードなしのビジョン ソリューションを作成する](./tutorial-nocode-vision.md)