---
title: インスタンスと認証を設定する (スクリプト化)
titleSuffix: Azure Digital Twins
description: 自動化されたデプロイ スクリプトを実行して Azure Digital Twins サービスのインスタンスを設定する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 86597521f762237b5c4bc9a7a5268d7dae1303b4
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587979"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Azure Digital Twins インスタンスと認証を設定する (スクリプト化)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

この記事では、**新しい Azure Digital Twins インスタンスを設定する**手順 (インスタンスの作成と認証の設定を含む) について説明します。 この記事を完了すると、Azure Digital Twins インスタンスのプログラミングを開始する準備が完了します。

この記事のこのバージョンでは、プロセスを効率化する[**自動化されたデプロイ スクリプト**のサンプル](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/)を実行することによってこれらの手順を完了します。 
* このスクリプトがバックグラウンドで実行する手動の CLI 手順を表示するには、この記事の CLI バージョンである[*方法: インスタンスと認証の設定 (CLI)* ](how-to-set-up-instance-cli.md) に関するページを参照してください。
* Azure portal に応じた手動の手順を表示するには、この記事のポータル バージョンである[*方法: インスタンスと認証の設定 (ポータル)* ](how-to-set-up-instance-portal.md) に関するページを参照してください。

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>展開スクリプトを実行する

この記事では、Azure Digital Twins のコード サンプルを使用して、Azure Digital Twins インスタンスと必要な認証を半自動でデプロイします。 これはまた、独自のスクリプト化された操作を記述するための開始点としても使用できます。

このサンプル スクリプトは PowerShell で記述されています。 これは、[Azure Digital Twins サンプル](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/)の一部です。これをコンピューターにダウンロードするには、そのサンプル リンクに移動し、タイトルの下の *[ZIP のダウンロード]* ボタンを選択します。

ダウンロードされたサンプル フォルダー内で、このデプロイ スクリプトは _Azure_Digital_Twins_samples.zip > scripts > **deploy.ps1**_ に配置されています。

このデプロイ スクリプトを Cloud Shell で実行する手順を次に示します。
1. ブラウザーで [[Azure Cloud Shell]](https://shell.azure.com/) ウィンドウに移動します。 次のコマンドを使用してサインインします。
    ```azurecli-interactive
    az login
    ```
    CLI で既定のブラウザーを開くことができる場合、開いたブラウザに Azure サインイン ページが読み込まれます。 それ以外の場合は、 *https://aka.ms/devicelogin* でブラウザー ページを開き、ターミナルに表示されている認証コードを入力します。
 
2. サインインしたら、Cloud Shell ウィンドウのアイコン バーに注目します。 [ファイルのアップロード/ダウンロード] アイコンを選択し、[アップロード] を選択します。

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="[アップロード] オプションが選択されていることを示す Cloud Shell ウィンドウ":::

    コンピューター上の _**deploy.ps1**_ ファイルに移動し、[開く] をクリックします。 これにより、そのファイルが Cloud Shell にアップロードされ、Cloud Shell ウィンドウで実行できるようになります。

3. Cloud Shell ウィンドウで `./deploy.ps1` コマンドを送信することによってスクリプトを実行します。 スクリプトにより自動化された設定手順が実行されると、次の値を渡すよう求められます。
    * インスタンス用: 使用する Azure サブスクリプションの*サブスクリプション ID*
    * インスタンス用: インスタンスをデプロイする*場所*。 Azure Digital Twins がどのリージョンでサポートされているかを確認するには、[*リージョン別の利用可能な Azure 製品*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)に関するページを参照してください。
    * インスタンス用: *リソース グループ*の名前。 既存のリソース グループを使用するか、または作成するリソース グループの新しい名前を入力できます。
    * インスタンス用: Azure Digital Twins インスタンスの*名前*。 新しいインスタンスの名前は、サブスクリプションのリージョン内で一意である必要があります (つまり、サブスクリプションのリージョン内に、選択した名前を既に使用している別の Azure Digital Twins インスタンスが存在する場合は、異なる名前を選択するよう求められます)。
    * アプリ登録用: 登録に関連付ける *Azure AD アプリケーション表示名*。 このアプリの登録では、[Azure Digital Twins API](how-to-use-apis-sdks.md) に対するアクセス許可を構成します。 後で、クライアント アプリはこのアプリ登録に対して認証し、結果として API への構成済みのアクセス許可が付与されます。
    * アプリ登録用: Azure AD アプリケーションの *Azure AD アプリケーション 応答 URL*。 `http://localhost` を使用できます。

このスクリプトは Azure Digital Twins インスタンスを作成し、Azure ユーザーにそのインスタンスの *[Azure Digital Twins 所有者 (プレビュー)]* ロールを割り当てた後、使用するクライアント アプリに対して Azure AD アプリ登録を設定します。

このスクリプトからの出力ログの抜粋を次に示します。

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="デプロイ スクリプトの実行による入力と出力のログを示す Cloud Shell ウィンドウ" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

スクリプトが正常に完了した場合は、最終的な出力として `Deployment completed successfully` が表示されます。 それ以外の場合は、エラー メッセージに対処してから、スクリプトを再実行します。 それにより、既に完了した手順はバイパスされ、前に中断された時点から再び入力の要求が開始されます。

スクリプトが完了すると、Azure Digital Twins インスタンスの準備が完了し、それを管理するためのアクセス許可が割り当てられ、それにアクセスするためのクライアント アプリのアクセス許可が設定されました。

> [!NOTE]
> このスクリプトは現在、Azure Digital Twins 内の必要な管理ロール ( *[Azure Digital Twins 所有者 (プレビュー)]* ) を、Cloud Shell からスクリプトを実行している同じユーザーに割り当てています。 このロールを、このインスタンスを管理するだれか他のユーザーに割り当てる必要がある場合は、Azure portal ([手順](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) または CLI ([手順](how-to-set-up-instance-cli.md#set-up-user-access-permissions)) を使用してこれを実行できます。

## <a name="collect-important-values"></a>重要な値を収集する

このスクリプトによって設定されるリソースには、Azure Digital Twins インスタンスを引き続き操作する場合に必要になる可能性がある重要な値がいくつかあります。 このセクションでは、[Azure portal](https://portal.azure.com) を使用して、これらの値を収集します。 これらは安全な場所に保存するか、または後で必要になったときにこのセクションに戻って見つける必要があります。

他のユーザーがこのインスタンスに対してプログラミングする場合は、これらの値をそのユーザーとも共有する必要があります。

### <a name="collect-instance-values"></a>インスタンス値を収集する

[Azure portal](https://portal.azure.com) で、ポータルの検索バーでインスタンスの名前を検索することによって Azure Digital Twins インスタンスを見つけます。

それを選択すると、そのインスタンスの *[概要]* ページが開きます。 その *[名前]* 、 *[リソース グループ]* 、 *[ホスト名]* をメモします。 これらは、後でそのインスタンスを識別して接続するために必要になる場合があります。

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="インスタンスの [概要] ページの重要な値の強調表示":::

### <a name="collect-app-registration-values"></a>アプリ登録の値を収集する 

アプリ登録には、後で [Azure Digital Twins API のクライアント アプリ認証コードを作成する](how-to-authenticate-client.md)ために必要になる重要な値が 2 つあります。 

それらを見つけるには、[このリンク](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)に従って、Azure portal の Azure AD アプリ登録の概要ページに移動します。 このページには、サブスクリプションで作成されたすべてのアプリ登録が表示されます。

今作成したアプリ登録がこの一覧に表示されます。 それを選択すると、その詳細が開かれます。

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="アプリ登録の重要な値のポータル ビュー":::

**実際の**ページに表示される、"*アプリケーション (クライアント) ID*" と "*ディレクトリ (テナント) ID*" をメモしておきます。 自分がクライアント アプリケーションのコードを作成するユーザーでない場合は、これらの値をそのユーザーと共有する必要があります。

## <a name="verify-success"></a>成功を確認する

このスクリプトによって設定されるリソースやアクセス許可の作成を確認したい場合は、[Azure portal](https://portal.azure.com) でそれらを確認できます。

### <a name="verify-instance"></a>インスタンスを確認する

インスタンスが作成されたことを確認するには、Azure portal の [[Azure Digital Twins] ページ](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances)に移動します。 このページには、すべての Azure Digital Twins インスタンスが一覧表示されます。 その一覧で新しく作成されたインスタンスの名前を探します。

### <a name="verify-user-role-assignment"></a>ユーザー ロールの割り当てを確認する

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> スクリプトが現在、この必要なロールを Cloud Shell からスクリプトを実行している同じユーザーに割り当てていることを思い出してください。 このロールを、このインスタンスを管理するだれか他のユーザーに割り当てる必要がある場合は、Azure portal ([手順](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) または CLI ([手順](how-to-set-up-instance-cli.md#set-up-user-access-permissions)) を使用してこれを実行できます。
>
> また、スクリプトを使用した設定で問題がある場合は、ポータルまたは CLI を使用して自分独自のロール割り当てをやり直すこともできます。

### <a name="verify-app-registration"></a>アプリ登録を確認する

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

最初に、Azure Digital Twins のアクセス許可の設定が登録時に正しく設定されたことを確認します。 これを行うには、メニュー バーから *[マニフェスト]* を選択して、アプリ登録のマニフェスト コードを表示します。 コード ウィンドウの一番下までスクロールし、`requiredResourceAccess` の下のこれらのフィールドを探します。 これらの値は、次のスクリーンショットの値と一致している必要があります。

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

## <a name="other-possible-steps-for-your-organization"></a>組織でのその他の考えられる手順

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>次のステップ

クライアント アプリの認証コードを作成してクライアント アプリケーションをインスタンスに接続する方法について説明します。
* [*方法: アプリ認証コードを作成する*](how-to-authenticate-client.md)
