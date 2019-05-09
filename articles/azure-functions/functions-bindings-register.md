---
title: Azure Functions バインド拡張機能を登録する
description: 環境に基づく Azure Functions バインド拡張機能の登録について説明します。
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 5534086d5754691f650370e465fa2c63210e0dc7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56740276"
---
# <a name="register-azure-functions-binding-extensions"></a>Azure Functions バインド拡張機能を登録する

Azure Functions は HTTP とすぐに使えるタイマーをサポートします。 その他のサービスを操作するには、[バインド](./functions-triggers-bindings.md)拡張機能をインストールまたは*登録*する必要があります。 バインド拡張機能は、Azure Core Tools または NuGet パッケージを使用して提供されます。 

次の表に、バインディングを登録するタイミングと方法を示します。

| 開発環境 |登録<br/> (Functions 1.x)  |登録<br/> (Functions 2.x)  |
|-------------------------|------------------------------------|------------------------------------|
|Azure ポータル|自動|[プロンプトで自動](#azure-portal-development)|
|非.NET 言語またはローカルの Azure Core Tools の開発|自動|[Core Tools CLI コマンドを使用](#local-development-azure-functions-core-tools)|
|Visual Studio 2017 を使用する C# クラス ライブラリ|[NuGet ツールを使用](#c-class-library-with-visual-studio-2017)|[NuGet ツールを使用](#c-class-library-with-visual-studio-2017)|
|Visual Studio Code を使用する C# クラス ライブラリ|該当なし|[.NET Core CLI を使用](#c-class-library-with-visual-studio-code)|

次のバインドの種類は、すべてのバージョンと環境に自動登録されているため、明示的な登録を必要としない例外です。HTTP とタイマー。

> [!IMPORTANT]
> この記事の残りの内容は、Functions 2.x にのみ適用されます。 バインド拡張機能は、[Visual Studio 2017 を使用して C# クラス ライブラリを作成する](#local-csharp)場合を除き、Functions 1.x では明示的に登録されません。

## <a name="azure-portal-development"></a>Azure Portal 開発

関数を作成するか、またはバインディングを追加する場合に、トリガーまたはバインディングの拡張機能が登録を必要とするときは、プロンプトが表示されます。 **[インストール]** をクリックして拡張機能を登録することで、プロンプトに応答します。 従量課金プランで、インストールには最大 10 分かかる可能性があります。

各拡張機能は、特定の関数アプリごとに 1 回だけインストールする必要があります。 ポータルで使用できないバインディングをサポートするか、またはインストールされている拡張機能を更新するために、[ポータルから Azure Functions バインディング拡張機能を手動でインストールまたは更新する](install-update-binding-extensions-manual.md)こともできます。  

## <a name="local-development-azure-functions-core-tools"></a>ローカル開発の Azure Functions Core Tools

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2017"></a>Visual Studio 2017 を使用する C# クラス ライブラリ

**Visual Studio 2017** では、次の例に示すように [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) コマンドを使用して、Package Manager Console からパッケージをインストールできます。

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <target_version>
```

特定のバインディングに使用するパッケージ名は、該当のバインディングのリファレンス記事に示されています。 たとえば、[Service Bus バインディングのリファレンス記事にある「パッケージ」セクション](functions-bindings-service-bus.md#packages---functions-1x)を参照してください。

例の中の `<target_version>` を `3.0.0-beta5` などの特定のバージョンのパッケージに置き換えます。 有効なバージョンは、[NuGet.org](https://nuget.org) の個々のパッケージ ページに記載されています。Functions ランタイム 1.x または 2.x に対応する主要なバージョンは、バインデイングのリファレンス記事に示されています。

## <a name="c-class-library-with-visual-studio-code"></a>Visual Studio Code を使用する C# クラス ライブラリ

**Visual Studio Code** では、次の例のように、.NET Core CLI の [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) コマンドを使用してコマンド プロンプトからパッケージをインストールできます。

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <target_version>
```

.NET Core CLI は、Azure Functions 2.x 開発のみに使用できます。

特定のバインディングに使用するパッケージ名は、該当のバインディングのリファレンス記事に示されています。 たとえば、[Service Bus バインディングのリファレンス記事にある「パッケージ」セクション](functions-bindings-service-bus.md#packages---functions-1x)を参照してください。

例の中の `<target_version>` を `3.0.0-beta5` などの特定のバージョンのパッケージに置き換えます。 有効なバージョンは、[NuGet.org](https://nuget.org) の個々のパッケージ ページに記載されています。Functions ランタイム 1.x または 2.x に対応する主要なバージョンは、バインデイングのリファレンス記事に示されています。

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバンドの例](./functions-bindings-example.md)

