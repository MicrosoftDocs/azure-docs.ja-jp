---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: 2936d22eacef73daef4433b3fd296dd4757fa410
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031127"
---
拡張機能バンドルを使用できない場合、Azure Functions Core Tools をローカルで使用し、プロジェクトで必要とされる特定の拡張機能パッケージをインストールできます。 

> [!NOTE]
> Core Tools を使用して拡張機能を手動でインストールするには、.NET Core 2.x SDK がインストールされている必要があります。

拡張機能を明示的にインストールすると、extensions.csproj という名前の .NET プロジェクト ファイルがプロジェクトのルートに追加されます。 このファイルによって、関数に必要とされる NuGet パッケージのセットが定義されます。 このファイルで [NuGet パッケージ参照](/nuget/consume-packages/package-references-in-project-files)を使用することができますが、Core Tools では、ファイルを手動編集しなくても拡張機能をインストールできます。

Core Tools を使用し、ローカル プロジェクトで必要な拡張機能をインストールする方法がいくつかあります。 

#### <a name="install-all-extensions"></a>すべての拡張機能をインストールする 

次のコマンドを使用し、ローカル プロジェクトのバインドで使用されるすべての拡張機能パッケージを自動追加します。

```dotnetcli
func extensions install
```
このコマンドでは、*function.json* ファイルを読み取って必要なパッケージを確認し、パッケージをインストールして、拡張プロジェクト (extensions.csproj) を再構築します。 現在のバージョンで新しいバインドが追加されますが、既存のバインドは更新されません。 新しいバージョンをインストールするときに、`--force` オプションを使用して既存のバインドを最新バージョンに更新します。

#### <a name="install-a-specific-extension"></a>特定の拡張機能をインストールする

次のコマンドを使用し、特定のバージョンの特定の拡張機能パッケージをインストールします。今回は、Storage 拡張機能です。

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```