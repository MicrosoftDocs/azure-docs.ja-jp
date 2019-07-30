---
title: Azure Functions バインド拡張機能を登録する
description: 環境に基づく Azure Functions バインド拡張機能の登録について説明します。
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 5969c3e0d270b45347f8132b2d655ba2e56cb2c0
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625896"
---
# <a name="register-azure-functions-binding-extensions"></a>Azure Functions バインド拡張機能を登録する

Azure Functions バージョン 2.x では、[バインド](./functions-triggers-bindings.md)を関数ランタイムとは別のパッケージとして利用できます。 .NET 関数は NuGet パッケージを介してバインドにアクセスしますが、拡張機能バンドルを使用すると、他の関数から構成設定を介してすべてのバインドにアクセスできるようになります。

バインド拡張機能に関連する以下の項目を考慮してください。

- バインド拡張機能は、[Visual Studio を使用して C# クラス ライブラリを作成する](#local-csharp)場合を除き、Functions 1.x では明示的に登録されません。

- HTTP とタイマーのトリガーは既定でサポートされており、拡張機能は必要ありません。

次の表に、バインディングを登録するタイミングと方法を示します。

| 開発環境 |登録<br/> (Functions 1.x)  |登録<br/> (Functions 2.x)  |
|-------------------------|------------------------------------|------------------------------------|
|Azure ポータル|自動|自動|
|非.NET 言語またはローカルの Azure Core Tools の開発|自動|[Azure Functions Core Tools と拡張機能バンドルを使用する](#extension-bundles)|
|Visual Studio を使用する C# クラス ライブラリ|[NuGet ツールを使用](#vs)|[NuGet ツールを使用](#vs)|
|Visual Studio Code を使用する C# クラス ライブラリ|該当なし|[.NET Core CLI を使用](#vs-code)|

## <a name="extension-bundles"></a>ローカル開発用の拡張機能バンドル

拡張機能バンドルは、バージョン 2.x ランタイム用のローカル開発テクノロジであり、互換性のある一連の Functions バインディング拡張機能を関数アプリ プロジェクトに追加できます。 これらの拡張機能パッケージは、Azure にデプロイするときにデプロイ パッケージに含まれます。 バンドルによって、Microsoft が公開しているすべてのバインディングを *host.json* ファイルの設定を介して利用できるようになります。 バンドルで定義されている拡張機能パッケージは相互に互換性があるため、パッケージ間の競合を回避できます。 ローカルで開発する場合は、必ず最新バージョンの [Azure Functions Core Tools](functions-run-local.md#v2) を使用します。

Azure Functions Core Tools または Visual Studio Code を使用するすべてのローカル開発には、拡張機能バンドルを使用します。

拡張機能バンドルを使用しない場合は、バインディング拡張機能をインストールする前に、.NET Core 2.x SDK をローカル コンピューターにインストールする必要があります。 バンドルによって、ローカル開発のこの要件が取り除かれます。 

拡張機能のバンドルを使用するには、*host.json* ファイルを更新して、`extensionBundle` の次のエントリを含めます。

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

`extensionBundle` では次のプロパティを使用できます。

| プロパティ | 説明 |
| -------- | ----------- |
| **`id`** | Microsoft Azure Functions 拡張機能バンドルの名前空間。 |
| **`version`** | インストールするバンドルのバージョン。 Functions ランタイムは常に、バージョン範囲 (間隔) で定義された最大許容バージョンを選択します。 上記のバージョン値には、1.0.0 から 2.0.0 未満までのすべてのバンドル バージョンを指定できます。 詳細については、[バージョン範囲を指定する間隔の表記](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards)に関する説明を参照してください。 |

バンドルのバージョンは、バンドル内のパッケージの変更に応じて増加します。 メジャー バージョンの変更は、バンドル内のパッケージがメジャー バージョンによって増加するときに発生します。通常、これは、Functions ランタイムのメジャー バージョンの変更と一致しています。  

既定のバンドルによってインストールされる最新の拡張機能のセットは、この [extensions.json ファイル](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)に列挙されています。

<a name="local-csharp"></a>

## <a name="vs"></a> Visual Studio を使用する C\# クラス ライブラリ

**Visual Studio** では、次の例に示すように [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) コマンドを使用して、Package Manager Console からパッケージをインストールできます。

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

特定のバインディングに使用するパッケージ名は、該当のバインディングのリファレンス記事に示されています。 たとえば、[Service Bus バインディングのリファレンス記事にある「パッケージ」セクション](functions-bindings-service-bus.md#packages---functions-1x)を参照してください。

例の中の `<TARGET_VERSION>` を `3.0.0-beta5` などの特定のバージョンのパッケージに置き換えます。 有効なバージョンは、[NuGet.org](https://nuget.org) の個々のパッケージ ページに記載されています。Functions ランタイム 1.x または 2.x に対応する主要なバージョンは、バインデイングのリファレンス記事に示されています。

`Install-Package` を使用してバインディングの参照を行う場合、[拡張機能バンドル](#extension-bundles)を使用する必要はありません。 この方法は、Visual Studio でビルドされたクラス ライブラリに固有のものです。

## <a name="vs-code"></a>Visual Studio Code を使用する C# クラス ライブラリ

> [!NOTE]
> Functions で互換性のある一連のバインディング拡張機能パッケージが自動的にインストールされるようにするには、[拡張機能バンドル](#extension-bundles)を使用することをお勧めします。

**Visual Studio Code** では、コマンド プロンプトから .NET Core CLI の [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) コマンドを使用して C# クラス ライブラリ プロジェクトのパッケージをインストールします。 次の例は、バインディングの追加方法を示しています。

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI は、Azure Functions 2.x 開発のみに使用できます。

`<BINDING_TYPE_NAME>` を対象のバインディングのリファレンス記事に示されているパッケージの名前に置き換えます。 対象のバインディングのリファレンス記事は、[サポートされるバインディングの一覧](./functions-triggers-bindings.md#supported-bindings)で見つけることができます。

例の中の `<TARGET_VERSION>` を `3.0.0-beta5` などの特定のバージョンのパッケージに置き換えます。 有効なバージョンは、[NuGet.org](https://nuget.org) の個々のパッケージ ページに記載されています。Functions ランタイム 1.x または 2.x に対応する主要なバージョンは、バインデイングのリファレンス記事に示されています。

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバンドの例](./functions-bindings-example.md)
