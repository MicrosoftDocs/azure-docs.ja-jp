---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2019
ms.openlocfilehash: d1c880ddc90ae3ce18dfde7e1983b45ac239de85
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58886839"
---
`Logging` の設定では、お客様のコンテナーの ASP.NET Core ログ サポートを管理します。 お客様が ASP.NET Core アプリケーションに対して使用するのと同じ構成設定と値をお客様のコンテナーに使用できます。 

コンテナーでは、次のログ プロバイダーがサポートされています。

|プロバイダー|目的|
|--|--|
|[コンソール](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|ASP.NET Core `Console` ログ プロバイダー。 このログ プロバイダーのすべての ASP.NET Core 構成設定と既定値がサポートされています。|
|[デバッグ](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|ASP.NET Core `Debug` ログ プロバイダー。 このログ プロバイダーのすべての ASP.NET Core 構成設定と既定値がサポートされています。|
|[ディスク](#disk-logging)|JSON ログ プロバイダー。 このログ プロバイダーは、ログ データを出力マウントに書き込みます。|

このコンテナー コマンドは、ログ情報を JSON 形式で出力マウントに格納します。

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

このコンテナー コマンドは、コンテナーの実行中に、`dbug` で始まるデバッグ情報を表示します。

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Disk ログ

`Disk` ログ プロバイダーでは、次の構成設定がサポートされます。  

| 名前 | データ型 | 説明 |
|------|-----------|-------------|
| `Format` | String | ログ ファイルの出力形式。<br/> **注:** ログ プロバイダーを有効にするためにこの値を `json` に設定する必要があります。 コンテナーのインスタンス化中に、出力マウントを指定せずに、この値を指定した場合、エラーが発生します。 |
| `MaxFileSize` | 整数 | ログ ファイルの最大サイズ (メガバイト (MB))。 現在のログ ファイルのサイズがこの値を満たしているか、または超えている場合、ログ プロバイダーによって新しいログ ファイルが開始されます。 -1 が指定されている場合、ログ ファイルのサイズは、出力マウントの最大ファイル サイズ (存在する場合) によってのみ制限されます。 既定値は 1 です。 |

ASP.NET Core ログのサポートを構成する方法の詳細については、[設定ファイル構成](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1)に関するページを参照してください。

