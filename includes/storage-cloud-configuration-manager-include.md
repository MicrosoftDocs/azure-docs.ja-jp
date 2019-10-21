---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 5102866cdda51ef545fd95b32946cb17c6e40a3c
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038175"
---
[.NET 用 Microsoft Azure Configuration Manager ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) には、構成ファイルの接続文字列を解析するためのクラスが用意されています。 [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) クラスで構成の設定を解析します。 デスクトップ、モバイル デバイス、Azure 仮想マシン、または Azure クラウド サービスで実行されるクライアント アプリケーションの設定を解析します。

`CloudConfigurationManager` パッケージを参照するには、次の `using` ディレクティブを追加します。

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage;
```

構成ファイルから接続文字列を取得する方法の例を次に示します。

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Azure Configuration Manager の使用はオプションです。 また、.NET Framework の [ConfigurationManager クラス](/dotnet/api/system.configuration.configurationmanager)などの API を使用することもできます。
