---
title: キー値に JSON コンテンツ タイプを使用する
titleSuffix: Azure App Configuration
description: キー値に JSON コンテンツ タイプを使用する方法を学習する
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: how-to
ms.date: 08/03/2020
ms.author: avgupta
ms.openlocfilehash: 19de46bc87b72ada221c63e36e87d0545304d344
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122155"
---
# <a name="leverage-content-type-to-store-json-key-values-in-app-configuration"></a>コンテンツ タイプを利用して App Configuration に JSON キー値を格納する

データはキー値として App Configuration に格納されます。その値は既定で文字列型として扱われます。 しかし、各キー値に関連付けられている content-type プロパティを利用してカスタム型を指定することにより、データの元の型を保持したり、コンテンツ タイプに基づいてアプリケーションの動作を変えたりすることができます。


## <a name="overview"></a>概要

App Configuration では、キー値のコンテンツ タイプとして JSON メディア タイプを使用して、次のような利点を活用することができます。
- **よりシンプルなデータ管理**: Azure portal での配列などのキー値の管理がはるかに簡単になります。
- **強化されたデータのエクスポート**: データのエクスポート中に、プリミティブ型、配列、および JSON オブジェクトが保持されます。
- **App Configuration プロバイダーでのネイティブ サポート**: アプリケーションで App Configuration プロバイダー ライブラリによって使用される場合、JSON コンテンツ タイプのキー値は正常に動作します。

#### <a name="valid-json-content-type"></a>有効な JSON コンテンツ タイプ

[ここ](https://www.iana.org/assignments/media-types/media-types.xhtml)に定義されているメディア タイプは、各キー値に関連付けられているコンテンツ タイプに割り当てることができます。
メディア タイプはタイプとサブタイプで構成されます。 タイプが `application` で、サブタイプ (またはサフィックス) が `json` の場合、メディア タイプは有効な JSON コンテンツ タイプと見なされます。
有効な JSON コンテンツ タイプの例を次に示します。

- application/json
- application/activity+json
- application/vnd.foobar+json;charset=utf-8

#### <a name="valid-json-values"></a>有効な JSON 値

キー値に JSON コンテンツ タイプがある場合、その値は、クライアントで正しく処理するために有効な JSON 形式である必要があります。 そうでない場合、クライアントで正しく処理できないか、あるいはフォール バックされ、文字列形式として扱われる可能性があります。
有効な JSON 値の例をいくつか以下に示します。

- "John Doe"
- 723
- False
- null
- "2020-01-01T12:34:56.789Z"
- [1, 2, 3, 4]
- {"ObjectSetting":{"Targeting":{"Default":true,"Level":"Information"}}}

> [!NOTE]
> この記事の残りの部分では、有効な JSON コンテンツ タイプと有効な JSON 値がある App Configuration のキー値はすべて、**JSON キー値** と呼ばれます。 

このチュートリアルでは、次の方法について説明します。
> [!div class="checklist"]
> * App Configuration に JSON キー値を作成する。
> * JSON ファイルから JSON キー値をインポートする。
> * JSON キー値を JSON ファイルにエクスポートする。
> * アプリケーションで JSON キー値を使用する。


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- このチュートリアルには、Azure CLI のバージョン 2.10.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-an-app-configuration-store"></a>App Configuration ストアを作成する

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]


## <a name="create-json-key-values-in-app-configuration"></a>App Configuration で JSON キー値を作成する

JSON キー値は、Azure portal または Azure CLI を使用するか、JSON ファイルからインポートすることによって作成できます。 このセクションでは、3 つの方法をすべて使用して、同じ JSON キー値を作成する手順について説明します。

### <a name="create-json-key-values-using-azure-portal"></a>Azure portal を使用して JSON キー値を作成する

App Configuration ストアに移動し、 **[構成エクスプローラー]**  >  **[作成]**  >  **[キー値]** の順に選択して、次のキーと値のペアを追加します。

| キー | 値 | コンテンツの種類 |
|---|---|---|
| Settings:BackgroundColor | "Green" | application/json |
| Settings:FontSize | 24 | application/json |
| Settings:UseDefaultRouting | false | application/json |
| Settings:BlockedUsers | null | application/json |
| Settings:ReleaseDate | "2020-08-04T12:34:56.789Z" | application/json |
| Settings:RolloutPercentage | [25,50,75,100] | application/json |
| Settings:Logging | {"Test":{"Level":"Debug"},"Prod":{"Level":"Warning"}} | application/json |

**[ラベル]** は空のままにして、 **[適用]** を選択します。

### <a name="create-json-key-values-using-azure-cli"></a>Azure CLI を使用して JSON キー値を作成する

次のコマンドにより、App Configuration ストアに JSON キー値が作成されます。 `<appconfig_name>` は、ご自分の App Configuration ストアの名前に置き換えてください。

```azurecli-interactive
appConfigName=<appconfig_name>
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BackgroundColor --value \"Green\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:FontSize --value 24
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:UseDefaultRouting --value false
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BlockedUsers --value null
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:ReleaseDate --value \"2020-08-04T12:34:56.789Z\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:RolloutPercentage --value [25,50,75,100]
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:Logging --value {\"Test\":{\"Level\":\"Debug\"},\"Prod\":{\"Level\":\"Warning\"}}
```

> [!IMPORTANT]
> Azure CLI または Azure Cloud Shell を使用して JSON キー値を作成する場合、指定する値はエスケープされた JSON 文字列である必要があります。

### <a name="import-json-key-values-from-a-file"></a>ファイルから JSON キー値をインポートする

次の内容の `Import.json` という JSON ファイルを作成し、キー値として App Configuration にインポートします。

```json
{
  "Settings": {
    "BackgroundColor": "Green",
    "BlockedUsers": null,
    "FontSize": 24,
    "Logging": {"Test":{"Level":"Debug"},"Prod":{"Level":"Warning"}},
    "ReleaseDate": "2020-08-04T12:34:56.789Z",
    "RolloutPercentage": [25,50,75,100],
    "UseDefaultRouting": false
  }
}
```

```azurecli-interactive
az appconfig kv import -s file --format json --path "~/Import.json" --content-type "application/json" --separator : --depth 2
```

> [!Note]
> `--depth` 引数は、ファイルの階層データをキーと値のペアにフラット化するために使用されます。 このチュートリアルでは、App Configuration に値として JSON オブジェクトを格納することもできることを示すために、深さが指定されています。 深さが指定されていない場合、JSON オブジェクトは既定で最も深いレベルにフラット化されます。

作成した JSON キー値は、App Configuration では次のようになるはずです。

![JSON キー値を含む構成ストア](./media/create-json-settings.png)


## <a name="export-json-key-values-to-a-file"></a>JSON キー値をファイルにエクスポートする

JSON キー値を使用する主な利点の 1 つは、エクスポート中に値の元のデータ型を保持できることです。 App Configuration のキー値に JSON コンテンツ タイプがない場合、その値は文字列として扱われます。 

JSON コンテンツ タイプのないこれらのキー値があるとします。

| キー | 値 | コンテンツの種類 |
|---|---|---|
| Settings:FontSize | 24 | |
| Settings:UseDefaultRouting | false | |

これらのキー値を JSON ファイルにエクスポートすると、値は文字列としてエクスポートされます。
```json
{
  "Settings": {
    "FontSize": "24",
    "UseDefaultRouting": "false"
  }
}
```

しかし、JSON キー値をファイルにエクスポートすると、すべての値の元のデータ型が保持されます。 これを確認するには、キー値を App Configuration から JSON ファイルにエクスポートします。 エクスポートされたファイルの内容が、先ほどインポートした `Import.json` ファイルと同じであることがわかります。

```azurecli-interactive
az appconfig kv export -d file --format json --path "~/Export.json" --separator :
```

> [!NOTE]
> App Configuration ストアに JSON コンテンツ タイプのないキー値がいくつかある場合、文字列形式の同じファイルにもエクスポートされます。 JSON キー値のみをエクスポートする場合は、一意のラベルまたはプレフィックスを JSON キー値に割り当て、エクスポート時にラベルまたはプレフィックスのフィルター処理を使用します。


## <a name="consuming-json-key-values-in-applications"></a>アプリケーションでの JSON キー値の使用

アプリケーションで JSON キー値を使用する最も簡単な方法は、App Configuration プロバイダー ライブラリを使用することです。 プロバイダー ライブラリを使用する場合、アプリケーションで JSON キー値の特別な処理を実装する必要はありません。 これらは解析され、アプリケーションのネイティブ構成に一致するように変換されます。

たとえば、アプリ構成に次のキー値がある場合:

| Key | 値 | コンテンツの種類 |
|---|---|---|
| 設定 | {"FontSize":24,"UseDefaultRouting":false} | application/json |

.NET アプリケーション構成では、キー値が次のようになります。

| Key | 値 |
|---|---|
| Settings:FontSize | 24 |
| Settings:UseDefaultRouting | false |

新しいキーに直接アクセスするか、[構成値を .NET オブジェクトのインスタンスにバインドする](/aspnet/core/fundamentals/configuration/#bind-hierarchical-configuration-data-using-the-options-pattern)ことを選択できます。


> [!Important]
> JSON キー値のネイティブ サポートは、.NET 構成プロバイダー バージョン 4.0.0 (またはそれ以降) で利用できます。 詳細については、「[*次のステップ*](#next-steps)」セクションを参照してください。

SDK または REST API を使用して、コンテンツ タイプに基づいて App Configuration からキー値を読み取る場合、アプリケーションで JSON キー値の値を解析する必要があります。


## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ

これで、App Configuration ストアでの JSON キー値の操作方法がわかったので、これらのキー値を使用するためのアプリケーションを作成します。

* [ASP.NET Core のクイック スタート](./quickstart-aspnet-core-app.md)
    * 前提条件:[Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) パッケージ v4.0.0 以降。

* [.NET Core のクイック スタート](./quickstart-dotnet-core-app.md)
    * 前提条件:[Microsoft.Extensions.Configuration.AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration) パッケージ v4.0.0 以降。
