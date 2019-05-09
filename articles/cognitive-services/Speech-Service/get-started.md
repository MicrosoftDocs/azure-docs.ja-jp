---
title: Speech Services を無料で試す
titleSuffix: Azure Cognitive Services
description: Speech Services は、低コストで簡単に使い始めることができます。 30 日間の無料試用版でサービスの機能を確認し、アプリケーションのニーズに適しているかどうかを判断できます。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 09cc38cd5343e8b01b3e704191ea40c133d724f8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58103725"
---
# <a name="try-speech-services-for-free"></a>Speech Services を無料で試す

Speech Services は、低コストで簡単に使い始めることができます。 30 日間の無料試用版でサービスの機能を確認し、アプリケーションのニーズに適しているかどうかを判断できます。

さらに多くの時間が必要な場合は、Microsoft Azure アカウントにサインアップします。Azure アカウントには、最大 30 日間、有料の Speech Services サブスクリプションに充当できる 200 ドルのサービス クレジットが付属しています。

最後に、Speech Services では、アプリケーションの開発に適した無料の低ボリューム レベルが提供されます。 サービス クレジットの有効期限が切れた後も、この無料サブスクリプションを保持できます。

## <a name="free-trial"></a>無料試用版

30 日間の無料試用版では、限られた期間、Standard 価格レベルにアクセスできます。

30 日間の無料試用版にサインアップするには:

1. 「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/)」に移動します。

1. **[Speech API]** タブを選択します。

   ![[Speech Services] タブ](media/index/try-speech-api-free-trial1.png)

1. **[Speech Services]** で、**[API キーの取得]** ボタンをクリックします。

   ![API キー](media/index/try-speech-api-free-trial2.png)

1. 使用条件に同意し、ドロップダウン メニューからロケールを選択します。

   ![使用条件に同意する](media/index/try-speech-api-free-trial3.png)

1. Microsoft、Facebook、LinkedIn、または GitHub アカウントを使用してサインインします。

    [Microsoft アカウント ポータル](https://account.microsoft.com/account)から無料の Microsoft アカウントにサインアップできます。 始めるには、**[Microsoft アカウントでサインイン]** をクリックし、サインインを求められたら **[Create one]\(アカウントの作成\)** をクリックします。 新しい Microsoft アカウントを作成して確認する手順に従います。

[Try Cognitive Services]\(Cognitive Services を試す\) にサインインすると、無料試用が開始します。 表示される Web ページには、現在の試用版サブスクリプションの対象となるすべての Azure Cognitive Services のサービスが一覧表示されます。 **[Speech Services]** の横に、2 つのサブスクリプション キーが表示されます。 アプリケーションでどちらのキーを使用しても構いません。

> [!NOTE]
> 無料試用版サブスクリプションはすべて米国西部リージョンにあります。 要求を行うときは、必ず `westus` エンドポイントを使用してください。

## <a name="new-azure-account"></a>新しい Azure アカウント

新しい Azure アカウントでは、最大 30 日間利用可能な 200 ドルのサービス クレジットが提供されます。 このクレジットを使用して、Speech Services をさらに探索したり、アプリケーション開発を開始したりできます。

新しい Azure アカウントにサインアップするには、[Azure サインアップ ページ](https://azure.microsoft.com/free/ai/)に移動し、**[無料で始める]** をクリックし、お使いの Microsoft アカウントを使用して新しい Azure アカウントを作成します。

[Microsoft アカウント ポータル](https://account.microsoft.com/account)から無料の Microsoft アカウントにサインアップできます。 始めるには、**[Microsoft アカウントでサインイン]** をクリックし、サインインを求められたら **[Create one]\(アカウントの作成\)** をクリックします。 新しい Microsoft アカウントを作成して確認する手順に従います。

Azure アカウントを作成したら、次のセクションの手順に従って、Speech Services のサブスクリプションを開始します。

## <a name="create-a-speech-resource-in-azure"></a>Azure で Speech リソースを作成する

Speech Services リソース (無料または有料レベル) を Azure アカウントに追加するには:

1. お使いの Microsoft アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。

1. ポータルの左上にある **[Create a resource]\(リソースの作成\)** を選択します。

    ![リソースの作成](media/index/try-speech-api-create-speech1.png)

1. **[New]\(新規\)** ウィンドウで、**[speech]** を検索します。

1. 検索結果で、**[Speech]** を選択します。

    ![[Speech] を選択](media/index/try-speech-api-create-speech2.png)

1. **[Speech]** で、**[作成]** ボタンを選択します。

    ![[作成] ボタンを選択](media/index/try-speech-api-create-speech3.png)

1. **[作成]** で、次のものを入力します。

   * 新しいリソースの名前。 この名前は、同じサービスの複数のサブスクリプションを区別するのに役立ちます。
   * 新しいリソースが関連付けられている Azure サブスクリプションを選択して、料金の課金方法を決定します。
   * リソースを使用するリージョンを選択します。 現時点では、Speech Services は、東アジア、北ヨーロッパ、米国西部の各リージョンで利用できます。
   * 無料または有料の価格レベルのどちらかを選択します。 **[価格の詳細を表示]** をクリックすると、各レベルの価格と使用量クォータについて詳細を確認できます。
   * この Speech サブスクリプションの新しいリソース グループを作成するか、既存のリソース グループにサブスクリプションを割り当てます。 リソース グループは、さまざまな Azure サブスクリプションを整理しておくのに役立ちます。
   * 今後、サブスクリプションに簡単にアクセスできるように、**[ダッシュボードにピン留めする]** チェック ボックスをオンにします。
   * **[作成]** を選択します。

     ![[作成] ボタンを選択](media/index/try-speech-api-create-speech4.png)

     新しい Speech リソースを作成して展開するまでに少し時間がかかります。 **[クイック スタート]** を選択すると、新しいリソースについての情報が表示されます。

     ![クイック スタート パネル](media/index/try-speech-api-create-speech5.png)

1. **[クイック スタート]** で、手順 1 の下の **[キー]** リンクをクリックして、サブスクリプション キーを表示します。 各サブスクリプションには 2 つのキーがあります。アプリケーションでどちらのキーを使用しても構いません。 各キーの横のボタンを選択して、クリップボードにコピーし、コードに貼り付けます。

> [!NOTE]
> Standard レベルのサブスクリプションは、1 つまたは複数のリージョンにいくつでも作成できます。 ただし、Free レベルのサブスクリプションは 1 つしか作成できません。 7 日間使用されなかった無料レベルのモデル デプロイは自動的に破棄されます。

## <a name="switch-to-a-new-subscription"></a>新しいサブスクリプションに切り替える

無料試用版の期限が切れたときやアプリケーションを発行するときにサブスクリプションを切り替えるには、コード内のリージョンとサブスクリプション キーを新しい Azure リソースのリージョンとサブスクリプション キーに置き換えます。

> [!NOTE]
> 無料試用版のキーは米国西部 (`westus`) リージョンに作成されます。 Azure ダッシュボードから作成したサブスクリプションは、お客様の選択によっては別のリージョンに存在している場合があります。

* アプリケーションで [Speech SDK](speech-sdk.md) を使用する場合、Speech の構成を作成するときに `westus` などのリージョン コードを指定します。
* アプリケーションで Speech Services の [REST API](rest-apis.md) のいずれかを使用する場合、要求を行うときに使用するエンドポイント URI にリージョンが含まれます。

あるリージョン用に作成されたキーはそのリージョンでのみ有効です。 別のリージョンで使用すると認証エラーが発生します。

## <a name="next-steps"></a>次の手順

10 分間のクイック スタートのいずれかを完了するか、SDK のサンプルを確認します:

> [!div class="nextstepaction"]
> [クイック スタート: C# で音声を認識する](quickstart-csharp-dotnet-windows.md)
> [Speech SDK サンプル](speech-sdk.md#get-the-samples)
