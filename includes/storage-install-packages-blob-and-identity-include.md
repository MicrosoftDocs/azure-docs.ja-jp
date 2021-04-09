---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: de79ea50d12ab322d1e28d0ad650df30ecc0c222
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "74806586"
---
## <a name="install-client-library-packages"></a>クライアント ライブラリ パッケージをインストールする

> [!NOTE]
> ここに示す例では、Azure Storage クライアント ライブラリバージョン 12 を使用します。 バージョン 12 クライアント ライブラリは、Azure SDK に含まれています。 Azure SDK の詳細については、[GitHub](https://github.com/Azure/azure-sdk) で Azure SDK リポジトリを参照してください。

BLOB ストレージ パッケージをインストールするには、NuGet パッケージ マネージャー コンソールから次のコマンドを実行します。

```powershell
Install-Package Azure.Storage.Blobs
```

ここに示す例では、Azure AD の資格情報で認証するために、[.NET 用 Azure ID クライアント ライブラリ](https://www.nuget.org/packages/Azure.Identity/)の最新バージョンも使用されます。 パッケージをインストールするには、NuGet パッケージ マネージャー コンソールから次のコマンドを実行します。

```powershell
Install-Package Azure.Identity
```
