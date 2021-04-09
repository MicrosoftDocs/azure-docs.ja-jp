---
title: インスタンスと認証を設定する (スクリプト化)
titleSuffix: Azure Digital Twins
description: 自動化されたデプロイ スクリプトを実行して Azure Digital Twins サービスのインスタンスを設定する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q3
ms.openlocfilehash: 13f5db52917cd9659438c551ac2f01c477cc1c7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102201296"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Azure Digital Twins インスタンスと認証を設定する (スクリプト化)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

この記事では、**新しい Azure Digital Twins インスタンスを設定する** 手順 (インスタンスの作成と認証の設定を含む) について説明します。 この記事を完了すると、Azure Digital Twins インスタンスのプログラミングを開始する準備が完了します。

この記事のこのバージョンでは、プロセスを効率化する [**自動化されたデプロイ スクリプト** のサンプル](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)を実行することによってこれらの手順を完了します。 
* このスクリプトがバックグラウンドで実行する手動の CLI 手順を表示するには、この記事の CLI バージョンである [*方法: インスタンスと認証の設定 (CLI)*](how-to-set-up-instance-cli.md) に関するページを参照してください。
* Azure portal に応じた手動の手順を表示するには、この記事のポータル バージョンである [*方法: インスタンスと認証の設定 (ポータル)*](how-to-set-up-instance-portal.md) に関するページを参照してください。

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prerequisites-download-the-script"></a>前提条件:スクリプトのダウンロード

このサンプル スクリプトは PowerShell で記述されています。 これは、[**Azure Digital Twins のエンドツーエンド サンプル**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)の一部です。これをマシンにダウンロードするには、そのサンプル リンクに移動し、タイトルの下の *[コードの参照]* ボタンを選択します。 これにより、サンプル用の GitHub リポジトリに移動します。サンプルは、 *[Code]\(コード\)* ボタンと、 *[Download ZIP]\(ZIP のダウンロード\)* を選択することによって、 *.ZIP* 形式でダウンロードできます。

:::image type="content" source="media/includes/download-repo-zip.png" alt-text="GitHub の digital-twins-samples リポジトリのビュー。[Code]\(コード\) ボタンが選択されて、小さなダイアログ ボックスが生成されます。ここでは、[Download ZIP]\(ZIP のダウンロード\) ボタンが強調表示されています。" lightbox="media/includes/download-repo-zip.png":::

これにより、 *.ZIP* フォルダーが **digital-twins-samples-master.zip** としてお使いのマシンにダウンロードされます。 お使いのコンピューター上のフォルダーに移動してこれを解凍し、ファイルを抽出します。

解凍したフォルダー内で、このデプロイ スクリプトは _digital-twins-samples-master > scripts > **deploy.ps1**_ に配置されています。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>展開スクリプトを実行する

この記事では、Azure Digital Twins のコード サンプルを使用して、Azure Digital Twins インスタンスと必要な認証を半自動でデプロイします。 これはまた、独自のスクリプト化された操作を記述するための開始点としても使用できます。

このデプロイ スクリプトを Cloud Shell で実行する手順を次に示します。
1. ブラウザーで [[Azure Cloud Shell]](https://shell.azure.com/) ウィンドウに移動します。 次のコマンドを使用してサインインします。
    ```azurecli-interactive
    az login
    ```
    CLI で既定のブラウザーを開くことができる場合、開いたブラウザに Azure サインイン ページが読み込まれます。 それ以外の場合は、 *https://aka.ms/devicelogin* でブラウザー ページを開き、ターミナルに表示されている認証コードを入力します。
 
2. Cloud Shell アイコン バーで、PowerShell バージョンを実行するように設定されていることを確認します。

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="PowerShell バージョンが選択されていることを示す Cloud Shell ウィンドウ":::

1. [ファイルのアップロード/ダウンロード] アイコンを選択し、[アップロード] を選択します。

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="[アップロード] アイコンが選択されていることを示す Cloud Shell ウィンドウ":::

    お使いのマシン上の _**deploy.ps1**_ ファイルに移動し (_digital-twins-samples-master > scripts > **deploy.ps1**_)、[開く] をクリックします。 これにより、そのファイルが Cloud Shell にアップロードされ、Cloud Shell ウィンドウで実行できるようになります。

4. Cloud Shell ウィンドウで `./deploy.ps1` コマンドを送信することによってスクリプトを実行します。 次のコマンドをコピーできます (Cloud Shell に貼り付けるには、Windows と Linux では **Ctrl+Shift+V**、macOS では **Cmd+Shift+V** を使用できます。 右クリック メニューを使用することもできます)。

    ```azurecli-interactive
    ./deploy.ps1
    ```

    このスクリプトによって、Azure Digital Twins インスタンスが作成されて、Azure ユーザーにそのインスタンスの *Azure Digital Twins Data Owner (Azure Digital Twins データ所有者)* ロールが割り当てられます。

    スクリプトにより自動化された設定手順が実行されると、次の値を渡すよう求められます。
    * インスタンス用: 使用する Azure サブスクリプションの *サブスクリプション ID*
    * インスタンス用: インスタンスをデプロイする *場所*。 Azure Digital Twins がどのリージョンでサポートされているかを確認するには、[*リージョン別の利用可能な Azure 製品*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)に関するページを参照してください。
    * インスタンス用: *リソース グループ* の名前。 既存のリソース グループを使用するか、または作成するリソース グループの新しい名前を入力できます。
    * インスタンス用: Azure Digital Twins インスタンスの *名前*。 サブスクリプションのリージョンに、指定した名前がすでに使用されている別の Azure Digital Twins インスタンスがある場合は、別の名前を選択するように求められます。

このスクリプトからの出力ログの抜粋を次に示します。

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="デプロイ スクリプトの実行による入力と出力のログを示す Cloud Shell ウィンドウ" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

スクリプトが正常に完了した場合は、最終的な出力として `Deployment completed successfully` が表示されます。 それ以外の場合は、エラー メッセージに対処してから、スクリプトを再実行します。 それにより、既に完了した手順はバイパスされ、前に中断された時点から再び入力の要求が開始されます。

> [!NOTE]
> このスクリプトは現在、Azure Digital Twins 内の必要な管理ロール (*Azure Digital Twins Data Owner (Azure Digital Twins データ所有者)* ) を、Cloud Shell からスクリプトを実行している同じユーザーに割り当てています。 このロールを、このインスタンスを管理するだれか他のユーザーに割り当てる必要がある場合は、Azure portal ([手順](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) または CLI ([手順](how-to-set-up-instance-cli.md#set-up-user-access-permissions)) を使用してこれを実行できます。

>[!NOTE]
>現在のところ、スクリプト化されたセットアップには **既知の問題** があります。一部のユーザー (特に、個人の [Microsoft アカウント (MSA) のユーザー](https://account.microsoft.com/account)) に対して、**_Azure Digital Twins Data Owner (Azure Digital Twins データ所有者)_ へのロールの割り当てを作成できない** ことがあります。
>
>ロール割り当ての妥当性はこの記事の後半にある「[*ユーザー ロールの割り当てを確認する*](#verify-user-role-assignment)」セクションで確認できます。また、必要に応じて、[Azure portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions) または [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions) を使用して手動でロール割り当てを設定できます。
>
>この問題の詳細については、「[*トラブルシューティング: Azure Digital Twins の既知の問題*](troubleshoot-known-issues.md#missing-role-assignment-after-scripted-setup)」を参照してください。

## <a name="verify-success-and-collect-important-values"></a>正常に実行されたことを確認して重要な値を収集する

このスクリプトによって設定されるリソースやアクセス許可の作成を確認したい場合は、以下の指示に従って [Azure portal](https://portal.azure.com) でそれらを確認できます。 成功したかどうかを確認できない手順がある場合は、その手順を再試行してください。 [Azure portal](how-to-set-up-instance-portal.md) または [CLI](how-to-set-up-instance-cli.md) の指示に従って、手順を個別に実行できます。

このセクションでは、このスクリプトによって設定されるリソースから、Azure Digital Twins インスタンスを引き続き操作する場合に必要になる可能性がある重要な値を見つける方法も示します。 これらの値は安全な場所に保存するか、または後で必要になったときにこのセクションに戻って見つける必要があります。 他のユーザーがそのインスタンスに対してプログラミングする場合は、これらの値を彼らとも共有する必要があります。

### <a name="verify-instance"></a>インスタンスを確認する

インスタンスが作成されたことを確認するには、Azure portal の [[Azure Digital Twins] ページ](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances)に移動します。 ポータルの検索バーで *Azure Digital Twins* を検索すると、このページにアクセスできます。

このページには、すべての Azure Digital Twins インスタンスが一覧表示されます。 その一覧で新しく作成されたインスタンスの名前を探します。

確認に失敗した場合は、[ポータル](how-to-set-up-instance-portal.md#create-the-azure-digital-twins-instance) または [CLI](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance) を使用してインスタンスの作成を再試行できます。

### <a name="collect-instance-values"></a>インスタンス値を収集する

[[Azure Digital Twins] ページ](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances)からインスタンスの名前を選択して、インスタンスの *[概要]* ページを開きます。 その *[名前]* 、 *[リソース グループ]* 、 *[ホスト名]* をメモします。 これらは、後でそのインスタンスを識別して接続するために必要になる場合があります。

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="インスタンスの [概要] ページの重要な値の強調表示":::

### <a name="verify-user-role-assignment"></a>ユーザー ロールの割り当てを確認する

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> スクリプトが現在、この必要なロールを Cloud Shell からスクリプトを実行している同じユーザーに割り当てていることを思い出してください。 このロールを、このインスタンスを管理するだれか他のユーザーに割り当てる必要がある場合は、Azure portal ([手順](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) または CLI ([手順](how-to-set-up-instance-cli.md#set-up-user-access-permissions)) を使用してこれを実行できます。

確認に失敗した場合は、[ポータル](how-to-set-up-instance-portal.md#set-up-user-access-permissions) または [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions) を使用して、独自のロールの割り当てをやり直すこともできます。

## <a name="next-steps"></a>次のステップ

Azure Digital Twins CLI コマンドを使用して、インスタンスでの個別の REST API 呼び出しをテストします。 
* [az dt reference](/cli/azure/ext/azure-iot/dt)
* [*方法: Azure Digital Twins CLI を使用する*](how-to-use-cli.md)

または、認証コードを使用してクライアント アプリケーションをインスタンスに接続する方法を確認します。
* [*方法: アプリ認証コードを作成する*](how-to-authenticate-client.md)