---
author: baanders
description: Visual Studio から Azure 関数を発行するプロセス用のインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: 9f8ca9ab6dc184abb8d9dcd25ebe87cbd7761722
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "102630283"
---
プロジェクトを Azure の関数アプリに発行するには、"*ソリューション エクスプローラー*" でプロジェクトを右クリックし、 **[発行]** を選択します。

> [!IMPORTANT] 
> Azure の関数アプリに発行すると、Azure Digital Twins とは関係なく、サブスクリプションに追加料金が発生します。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="右クリックのソリューション メニューを示す Visual Studio のスクリーンショット。メニューで [発行] が強調表示されています。":::

表示された *[発行]* ページで、既定のターゲット (**Azure**) を選択したままにして、 *[次へ]* を選択します。 

特定のターゲットで **[Azure Function App (Windows)]** を選択し、 *[次へ]* を選択します。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Azure 関数の [発行] ダイアログの Visual Studio のスクリーンショット。[特定のターゲット] ページで [Azure Function App (Windows)] が選択されています。":::

*[Functions インスタンス]* ページで、該当するサブスクリプションを選択します。 次に、 *+* アイコンを選択して、新しい Azure 関数を作成します。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Azure 関数の [発行] ダイアログの Visual Studio のスクリーンショット。[Functions インスタンス] ページで、新しい関数を作成するための [+] ボタンが強調表示されています。":::

*[Function App (Windows) - 新規作成]* ウィンドウの各フィールドに次のように入力します。
* **[名前]** は、Azure Functions アプリをホストするために Azure で使用される従量課金プランの名前です。 実際の関数を収容する関数アプリの名前としても使用されます。 一意の値を独自に選択するか、候補として表示された既定値をそのまま使用します。
* **[サブスクリプション]** は、使用するサブスクリプションと一致させます。 
* **[リソース グループ]** は、使用するリソース グループと合わせます。
* **[プランの種類]** は "*従量課金プラン*" のままにします。
* リソース グループの場所と一致する **[場所]** を選択します。
* *[新規]* リンクを使用して、新しい **Azure Storage** リソースを作成します。 場所の設定はリソース グループに合わせてください。それ以外は既定値のままにし、[OK] を選択します。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Azure 関数の [発行] ダイアログの Visual Studio のスクリーンショット。名前、サブスクリプション、リソース グループ、プランの種類、場所、Azure Storage など、新しい関数アプリの詳細が入力されています。":::

そのうえで **[Create]\(作成\)** を選択します。

App Service が作成される間少し待機すると、ダイアログは *[Functions インスタンス]* ページに戻ります。リソース グループの下に入れ子になっている **[関数アプリ]** 領域に、新しい関数アプリが表示されます。 *[完了]* を選択します。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Visual Studio で Azure 関数を発行する: Functions インスタンス (関数アプリの後)":::

Visual Studio のメイン ウィンドウに再度 *[発行]* ペインが表示されるので、そこですべての情報が正しいことを確認し、 **[発行]** を選択します。

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Azure 関数の [発行] ダイアログの Visual Studio のスクリーンショット。新しい関数アプリが関数アプリの一覧に表示され、[終了] ボタンが表示されます。":::

> [!NOTE]
> 次のようなポップアップが表示される場合は、以下のようにします。:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="発行資格情報と呼ばれる Visual Studio ポップアップ ウィンドウのスクリーンショット。これには、ユーザー名とパスワードのフィールドと、[Attempt to retrieve credentials from Azure]\(Azure から資格情報の取得を試みる\) ためのボタンが含まれています。" border="false":::
> **[Attempt to retrieve credentials from Azure]\(Azure から資格情報の取得を試みる\)** 、 **[保存]** の順に選択します。
>
> "*Azure で関数のバージョンをアップグレード*" するようにという警告や、"*関数ランタイムのバージョンが Azure で実行されているバージョンと一致しない*" という警告が表示される場合は、次のようにしてください。
>
> 画面の指示に従って、最新の Azure Functions ランタイム バージョンにアップグレードします。 この問題は、古いバージョンの Visual Studio を使用している場合に発生する可能性があります。

これで、関数アプリが Azure に発行されました。