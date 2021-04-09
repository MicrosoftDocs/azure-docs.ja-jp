---
title: チュートリアル - Azure Service Fabric Mesh アプリケーションをアップグレードする
description: このチュートリアルはシリーズの第 4 部です。ここでは、Azure Service Fabric Mesh アプリケーションを Visual Studio から直接アップグレードする方法について説明します。
author: georgewallace
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-csharp
ms.openlocfilehash: 1020613eb43177ba159601f253848f8d03f385a8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625434"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>チュートリアル:Visual Studio を使用して Service Fabric アプリケーションをアップグレードする方法を学習します

> [!IMPORTANT]
> Azure Service Fabric Mesh のプレビューは廃止されました。 Service Fabric Mesh API を介した新しいデプロイは許可されなくなります。 既存のデプロイのサポートは、2021 年 4 月 28 日まで継続されます。
> 
> 詳細については、[Azure Service Fabric Mesh のプレビューの廃止](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)に関する記事を参照してください。

このチュートリアルはシリーズの第 4 部です。ここでは、Azure Service Fabric Mesh アプリケーションを Visual Studio から直接アップグレードする方法について説明します。 アップグレードには、コードの更新と構成の更新の両方が含まれます。 Visual Studio 内からアップグレードする手順と発行する手順は同じであることがわかります。

このチュートリアルで学習する内容は次のとおりです。
> [!div class="checklist"]
> * Visual Studio を使用して Service Fabric Mesh サービスをアップグレードする

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [Visual Studio で Service Fabric Mesh アプリを作成する](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [ローカル開発クラスター内で実行されている Service Fabric Mesh アプリをデバッグする](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Service Fabric Mesh アプリをデプロイする](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Service Fabric Mesh アプリをアップグレードする
> * [Service Fabric Mesh リソースをクリーンアップする](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* To Do アプリをデプロイしていない場合は、[Service Fabric Mesh Web アプリケーションの発行](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)に関するページの指示に従ってください。

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Visual Studio を使用して Service Fabric Mesh サービスをアップグレードする

この記事では、アプリケーション内でマイクロサービスをアップグレードする方法を示します。 この例では、`WebFrontEnd` サービスを変更し、タスク カテゴリを表示して、指定されている CPU の量を増やします。 その後、デプロイされているサービスをアップグレードします。

## <a name="modify-the-config"></a>構成を変更する

Service Fabric Mesh アプリを作成するときに、Visual Studio で各デプロイ環境 (クラウドとローカル) の **parameters.yaml** ファイルが追加されます。 これらのファイルでは、パラメーターとその値を定義できます。これらは service.yaml または network.yaml などの Mesh *.yaml ファイルから参照できます。  Visual Studio では、サービスで使用できる CPU の量など、いくつかの変数が提供されます。

`WebFrontEnd_cpu` パラメーターを更新し、**WebFrontEnd** サービスの使用頻度がより高くなるアプリケーションで cpu リソースを `1.5` に更新します。

1. **todolistapp** プロジェクトの **[環境]**  >  **[クラウド]** で、**parameters.yaml** ファイルを開きます。 `WebFrontEnd_cpu` の値を `1.5` に変更します。 さまざまなサービスに適用される、同じ名前のパラメーターと区別するためのベスト プラクティスとして、パラメーター名の前には `WebFrontEnd_` というサービス名が付けられます。

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. **[WebFrontEnd]**  >  **[サービスのリソース]** で、**WebFrontEnd** プロジェクトの **service.yaml** ファイルを開きます。

    `resources:` セクションでは、`cpu:` が `"[parameters('WebFrontEnd_cpu')]"` に設定されていることに注意してください。 クラウド用にプロジェクトが構築されている場合、`'WebFrontEnd_cpu` の値は **[環境]**  >  **[クラウド]**  > **parameters.yaml** ファイルから取得され、`1.5` になります。 ローカルで実行するためにプロジェクトが構築されている場合、値は **[環境]**  >  **[ローカル]**  > **parameters.yaml** ファイルから取得され、'0.5' となります。

> [!Tip]
> 既定では、profile.yaml ファイルのピアであるパラメーター ファイルが、その profile.yaml ファイルの値を指定するために使用されます。
> たとえば、[環境] > [クラウド] > parameters.yaml では、[環境] > [クラウド] > profile.yaml のパラメーター値が指定されます。
>
> profile.yaml ファイルに `parametersFilePath=”relative or full path to the parameters file”` を追加して、これをオーバーライドすることができます。たとえば、`parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` や `parametersFilePath=”..\CommonParameters.yaml”` のようにします。

## <a name="modify-the-model"></a>モデルを変更する

コードの変更を導入するため、`ToDoItem.cs` ファイルで `ToDoItem` クラスに `Category` プロパティを追加します。

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

そして、同じファイル内の `Load()` メソッドを更新し、カテゴリを既定の文字列に設定します。

```csharp
public static ToDoItem Load(string description, int index, bool completed)
{
    ToDoItem newItem = new ToDoItem(description)
    {
        Index = index,
        Completed = completed, 
        Category = "none"    // <-- add this line
    };

    return newItem;
}
```

## <a name="modify-the-service"></a>サービスを変更する

`WebFrontEnd` プロジェクトは、To-Do リスト項目を表示する Web ページを含む ASP.NET Core アプリケーションです。 `WebFrontEnd` プロジェクトで `Index.cshtml` を開き、タスクのカテゴリを表示するための次に示す 2 行を追加します。

```HTML
<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Category</th>           @*add this line*@
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Category</td>           @*add this line*@
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

アプリをビルドして実行し、タスクの一覧を示す新しいカテゴリ列が Web ページに表示されることを確認します。

## <a name="upgrade-the-app-from-visual-studio"></a>Visual Studio からアプリをアップグレードする

コードのアップグレードと構成のアップグレードのどちらを行う場合でも (この例では両方を行う)、Azure 上の Service Fabric Mesh アプリをアップグレードするには、Visual Studio で **todolistapp** を右クリックして、 **[発行]** を選択します。

**[Service Fabric アプリケーションの発行]** ダイアログが表示されます。

**[ターゲット プロファイル]** ドロップダウンを使用して、このデプロイで使用する profile.yaml ファイルを選びます。 クラウド内のアプリをアップグレードするため、ドロップダウンでは **cloud.yaml** を選びます。そのファイルで定義されている 1.0 の `WebFrontEnd_cpu` 値が使用されます。

![Visual Studio: Service Fabric mesh の発行ダイアログ](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Azure アカウントとサブスクリプションを選択します。 **[場所]** は、最初に To-Do アプリを Azure に発行したときに使用した場所に設定します。 この記事では、 **[米国東部]** を使用しました。

**[リソース グループ]** は、最初に To-Do アプリを Azure に発行したときに使用したリソース グループに設定します。

**[Azure Container Registry]** は、最初に To-Do アプリを Azure に発行したときに作成した Azure コンテナー レジストリの名前に設定します。

発行ダイアログで、 **[発行]** ボタンをクリックして Azure 上の To-Do アプリをアップグレードします。

Visual Studio の **[出力]** ウィンドウで **[Service Fabric Tools]** ウィンドウを選択して、アップグレードの進行状況を監視します。 

イメージがビルドされ、Azure Container Registry にプッシュされた後、出力に **[For status]\(状態について\)** リンクが表示されます。これをクリックすれば、Azure portal でデプロイを監視することができます。

アップグレードが完了すると、**Service Fabric Tools** の出力に、アプリケーションの IP アドレスとポートが URL の形式で表示されます。

```json
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Web ブラウザーを開き、その URL に移動して、Azure で実行されている Web サイトを表示します。 カテゴリ列が含まれる Web ページが表示されるはずです。

## <a name="next-steps"></a>次のステップ

チュートリアルのこの部分で学習した内容は次のとおりです。
> [!div class="checklist"]
> * Visual Studio を使用して Service Fabric Mesh アプリをアップグレードする方法

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [Service Fabric Mesh リソースをクリーンアップする](service-fabric-mesh-tutorial-cleanup-resources.md)