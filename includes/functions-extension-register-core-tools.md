---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: a4f03223b5067d18f5d6e747b3bb630a86031b8f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689562"
---
拡張機能バンドルを使用できない場合、Azure Functions Core Tools をローカルで使用し、プロジェクトで必要とされる特定の拡張機能パッケージをインストールできます。

> [!IMPORTANT]
> 拡張機能バンドルを使用している関数アプリには、拡張機能を明示的にインストールできません。 拡張機能を明示的にインストールする前に、*host.json* の `extensionBundle` セクションを削除します。

次の項目では、場合によっては拡張機能の手動インストールが必要になるいくつかの理由を説明します。

* バンドルで利用できない特定のバージョンの拡張機能にアクセスする必要があります。
* バンドルで利用できないカスタム拡張機能にアクセスする必要があります。
* 1 つのバンドルで利用できない特定の組み合わせの拡張機能にアクセスする必要があります。

> [!NOTE]
> Core Tools を使用して拡張機能を手動でインストールするには、[.NET Core 2.x SDK](https://dotnet.microsoft.com/download) がインストールされている必要があります。 .NET Core SDK は NuGet から拡張機能をインストールする目的で Azure Functions Core Tools によって使用されます。 Azure Functions 拡張機能を使用するために .NET の知識は必要ありません。

拡張機能を明示的にインストールすると、extensions.csproj という名前の .NET プロジェクト ファイルがプロジェクトのルートに追加されます。 このファイルによって、関数に必要とされる NuGet パッケージのセットが定義されます。 このファイルで [NuGet パッケージ参照](/nuget/consume-packages/package-references-in-project-files)を使用することができますが、Core Tools では、ファイルを手動編集しなくても拡張機能をインストールできます。

Core Tools を使用し、ローカル プロジェクトで必要な拡張機能をインストールする方法がいくつかあります。 

#### <a name="install-all-extensions"></a>すべての拡張機能をインストールする 

次のコマンドを使用し、ローカル プロジェクトのバインドで使用されるすべての拡張機能パッケージを自動追加します。

```dotnetcli
func extensions install
```
このコマンドでは、*function.json* ファイルを読み取って必要なパッケージを確認し、パッケージをインストールして、拡張プロジェクト (extensions.csproj) を再構築します。 現在のバージョンで新しいバインドが追加されますが、既存のバインドは更新されません。 新しいバージョンをインストールするときに、`--force` オプションを使用して既存のバインドを最新バージョンに更新します。

Core Tools で認識されないバインドが関数アプリで使用されている場合、特定の拡張機能を手動インストールする必要があります。

#### <a name="install-a-specific-extension"></a>特定の拡張機能をインストールする

次のコマンドを使用し、特定のバージョンの特定の拡張機能パッケージをインストールします。今回は、Storage 拡張機能です。

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```
