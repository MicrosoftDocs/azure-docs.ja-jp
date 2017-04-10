---
title: "Azure Functions の開発に関するガイダンス | Microsoft Docs"
description: "プログラミング言語とバインドを問わず、Azure での関数開発に必要な Azure Functions の概念とテクニックについて説明します。"
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "開発者向けガイド, Azure Functions, 関数, イベント処理, webhook, 動的コンピューティング, サーバーレス アーキテクチャ"
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/23/2017
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: bc96edb44dc8bbbbe4687806117990c9d3470fdc
ms.openlocfilehash: adb70fc58321c11c0b57efc9810a44d0ab2c8a20
ms.lasthandoff: 03/01/2017


---
# <a name="azure-functions-developers-guide"></a>Azure Functions の開発者向けガイド
Azure Functions の特定の関数は、使用する言語またはバインドに関係なく、いくつかの中核となる技術的な概念とコンポーネントを共有します。 特定の言語またはバインドに固有の詳細を学習する前に、それらすべてに適用されるこの概要をお読みください。

この記事は、「[Azure Functions の概要](functions-overview.md)」を既に読んでいて、[トリガー、バインド、JobHost ランタイムなどの WebJobs SDK の概念](../app-service-web/websites-dotnet-webjobs-sdk.md)を熟知していることを前提として書かれています。 Azure Functions は WebJobs SDK が基になっています。 

## <a name="function-code"></a>関数のコード
*関数* は Azure Functions の主要な概念です。 好みの言語を選択して関数のコードを記述し、コードと構成ファイルを同じフォルダーに保存します。 構成の名前は `function.json` で、JSON 構成データを格納します。 さまざまな言語がサポートされており、各言語はその言語での作業向けに最適化された少しずつ異なるエクスペリエンスを備えています。 

function.json ファイルは、関数バインドとその他の構成設定を定義します。 ランタイムはこのファイルを使用して、監視対象のイベントを特定し、関数の実行との間でデータを渡したりデータを受け取ったりする方法を判断します。 以下に function.json ファイルの例を示します。

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

関数が実行されることを防ぐために、`disabled` プロパティを `true` に設定します。

`bindings` プロパティで、トリガーとバインドの両方を構成します。 各バインドは、いくつかの一般的な設定と、バインドの特定の種類に固有の設定を共有します。 すべてのバインドには次の設定が必要です。

| プロパティ | 値/型 | 説明 |
| --- | --- | --- |
| `type` |string |バインドの種類。 たとえば、「 `queueTrigger`」のように入力します。 |
| `direction` |"in"、"'out" |バインドが関数への受信データか、関数からの送信データかを示します。 |
| `name` |string |関数のバインドされたデータに使用される名前。 C# の場合は引数の名前です。JavaScript の場合はキー/値リストのキーです。 |

## <a name="function-app"></a>関数アプリ
関数アプリは、Azure App Service でまとめて管理される&1; つまたは複数の個々の関数で構成されます。 関数アプリ内のすべての関数は、同じ料金プラン、継続的なデプロイ、およびランタイムのバージョンを共有します。 複数の言語で記述された関数は、同じ関数アプリをすべて共有できます。 関数を整理し、まとめて管理する方法として関数アプリを考えてください。 

## <a name="runtime-script-host-and-web-host"></a>ランタイム (スクリプト ホストおよび Web ホスト)
ランタイム (つまりスクリプト ホスト) は基になる WebJobs SDK ホストであり、イベントをリッスンし、データを収集して送信して、最終的にはコードを実行します。 

HTTP トリガーを容易にするため、運用環境シナリオでスクリプト ホストの前に配置されるように設計されている Web ホストもあります。 ホストを&2; つ持つことで、Web ホストによって管理されるフロントエンド トラフィックからスクリプト ホストを分離できます。

## <a name="folder-structure"></a>フォルダー構造
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Azure App Service で関数アプリに関数をデプロイするためにプロジェクトをセットアップするときは、このフォルダー構造をサイト コードとして扱うことができます。 継続的インテグレーションやデプロイなどの既存のツール、またはカスタム デプロイ スクリプトを使用して、デプロイ時のパッケージのインストールまたはコードの変換を行うことができます。

> [!NOTE]
> `host.json` ファイルと関数フォルダーを直接 `wwwroot` フォルダーにデプロイします。 デプロイに `wwwroot` フォルダーを含めないでください。 そうしないと、`wwwroot\wwwroot` フォルダーができてしまいます。 
> 
> 

## <a name="a-idfileupdatea-how-to-update-function-app-files"></a><a id="fileupdate"></a> 関数アプリ ファイルを更新する方法
Azure ポータルに組み込まれている関数エディターでは、 *function.json* ファイルと関数のコード ファイルを更新できます。 *package.json* や *project.json* などのその他のファイルや依存関係をアップロードまたは更新するには、その他のデプロイ方法を使用する必要があります。

関数アプリは App Service 上で構築されるため、[標準 Web アプリで利用できるデプロイ オプション](../app-service-web/web-sites-deploy.md) はすべて、関数アプリでも利用できます。 ここでは、関数アプリ ファイルをアップロードまたは更新するための方法をいくつか紹介します。 

#### <a name="to-use-app-service-editor"></a>App Service Editor を使用するには
1. Azure Functions ポータルで、 **[関数アプリの設定]**をクリックします。
2. **[詳細設定]** セクションで、**[App Service の設定に移動]** をクリックします。
3. **[開発ツール]** の App Menu ナビゲーション バーの **[App Service Editor]** をクリックします。
4. **[移動]**をクリックします。
   
   App Service Editor の読み込み後、*host.json* ファイルと関数フォルダーが *wwwroot* の下に表示されます。 
5. ファイルを開いて編集するか、開発コンピューターからドラッグアンドドロップしてファイルをアップロードします。

#### <a name="to-use-the-function-apps-scm-kudu-endpoint"></a>関数アプリの SCM (Kudu) エンドポイントを使用するには
1. `https://<function_app_name>.scm.azurewebsites.net`に移動します。
2. **[デバッグ コンソール]、[CMD]** の順にクリックします。
3. `D:\home\site\wwwroot\` に移動し、*host.json* または `D:\home\site\wwwroot\<function_name>` を更新し、関数のファイルを更新します。
4. アップロードするファイルをファイル グリッドのフォルダーにドラッグアンドドロップします。 ファイルをドロップできるファイル グリッドには&2; つの領域があります。 *.zip* ファイルの場合は、[Drag here to upload and unzip (アップロードして解凍するにはここにドラッグしてください)] というラベルの付いたボックスが表示されます。 他の種類のファイルの場合は、ファイル グリッド内の [unzip (ファイルの解凍)] ボックスの外側にドロップします。

#### <a name="to-use-ftp"></a>FTP を使用するには
1. [こちら](../app-service-web/web-sites-deploy.md#ftp) の指示に従って、FTP を構成します。
2. 関数アプリのサイトに接続されたら、更新された *host.json* ファイルを `/site/wwwroot` にコピーするか、関数ファイルを `/site/wwwroot/<function_name>` にコピーします。

#### <a name="to-use-continuous-deployment"></a>継続的なデプロイを使用するには
「 [Azure Functions の継続的なデプロイ](functions-continuous-deployment.md)」のトピックの手順に従ってください。

## <a name="parallel-execution"></a>並列実行
シングル スレッドの関数ランタイムが処理できるより速く複数のトリガー イベントが発生する場合、ランタイムは関数を並列で複数回呼び出す場合があります。  関数アプリが[従量課金ホスティング プラン](functions-scale.md#how-the-consumption-plan-works)を使用している場合、関数アプリは自動的にスケールアウトできます。  アプリが従量課金ホスティング プランと標準の [App Service ホスティング プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)のどちらで実行されていても、関数アプリの各インスタンスは、複数の同時関数呼び出しを、複数のスレッドを使用して並列に処理します。  各関数アプリ インスタンスでの同時関数呼び出しの最大数は、使用されるトリガーの種類と、関数アプリ内の他の関数によって使用されるリソースに応じて異なります。

## <a name="azure-functions-pulse"></a>Azure Functions パルス
パルスは、関数の実行頻度と成功または失敗を示すライブ イベント ストリームです。 また、平均実行時間を監視することができます。 今後、さらに機能とカスタマイズが追加される予定です。 **[パルス]** ページには **[監視]** タブからアクセスできます。

## <a name="repositories"></a>リポジトリ
Azure Functions のコードはオープン ソースであり、GitHub リポジトリに保存されています。

* [Azure Functions ランタイム](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Azure Functions ポータル](https://github.com/projectkudu/AzureFunctionsPortal)
* [Azure Functions テンプレート](https://github.com/Azure/azure-webjobs-sdk-templates/)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK 拡張機能](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>バインド
サポートされるすべてのバインドを次の表に示します。

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

## <a name="reporting-issues"></a>問題の報告
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>次のステップ
詳細については、次のリソースを参照してください。

* [Azure Functions のベスト プラクティス](functions-best-practices.md)
* [Azure Functions C# developer reference (Azure Functions C# 開発者向けリファレンス)](functions-reference-csharp.md)
* [Azure Functions F# 開発者向けリファレンス](functions-reference-fsharp.md)
* [Azure Functions NodeJS 開発者向けリファレンス](functions-reference-node.md)
* [Azure Functions triggers and bindings (Azure Functions のトリガーとバインド)](functions-triggers-bindings.md)
* [Azure Functions: The Journey (Azure Functions への道のり) (Azure Functions への道のり)](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/) 」。 Azure Functions の開発の歴史。


