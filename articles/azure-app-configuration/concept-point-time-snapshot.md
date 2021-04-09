---
title: ポイントインタイムからキーと値のペアを取得する
titleSuffix: Azure App Configuration
description: キーと値に対する変更のレコードを保持している Azure App Configuration で、ポイントインタイム スナップショットを使用して古いキーと値のペアを取得します。
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: fa2dbb11b3b8b9afd90c7f6fe3ffe77e2e57c4e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96929899"
---
# <a name="point-in-time-snapshot"></a>ポイントインタイム スナップショット

Azure App Configuration では、キー値に対する変更のレコードが保持されます。 このレコードからは、キーと値の変更のタイムラインが得られます。 任意のキー値の履歴を再構築し、キーの履歴期間 (Free レベル ストアの場合は 7 日、Standard レベル ストアの場合は 30 日) 内の任意の時点での過去の値を指定できます。 この機能を使用すると、"過去にさかのぼって" 以前のキーと値を取得することができます。 たとえば、アプリケーションを以前の構成にロールバックするために、直近のデプロイの前に使用されていた構成設定を回復することができます。

## <a name="key-value-retrieval"></a>キー/値の取得

Azure portal または CLI を使用して、過去のキー値を取得できます。 Azure CLI では `az appconfig revision list` を使用して、適切なパラメーターを追加して必要な値を取得します。  ストア名 (`--name <app-config-store-name>`) を指定するか、接続文字列 (`--connection-string <your-connection-string>`) を使用するかして、Azure App Configuration インスタンスを指定します。 特定のポイントインタイム (`--datetime`) を指定し、返される項目の最大数 (`--top`) を指定して、出力を制限します。

Azure CLI がローカルにインストールされていない場合は、必要に応じて、[Azure Cloud Shell](../cloud-shell/overview.md) を使用することもできます。

キーと値に対する変更のレコードをすべて取得します。

```azurecli
az appconfig revision list --name <your-app-config-store-name>.
```

キーが `environment` でラベルが `test` と `prod` の変更のレコードをすべて取得します。

```azurecli
az appconfig revision list --name <your-app-config-store-name> --key environment --label test,prod
```

階層的キー スペース `environment:prod` 内の変更のレコードをすべて取得します。

```azurecli
az appconfig revision list --name <your-app-config-store-name> --key environment:prod:* 
```

特定のポイントインタイムのキー `color` に対する変更のレコードをすべて取得します。

```azurecli
az appconfig revision list --connection-string <your-app-config-connection-string> --key color --datetime "2019-05-01T11:24:12Z" 
```

自分のキーと値に対する最近 10 件の変更のレコードを取得し、`key`、`label`、`last_modified` タイム スタンプの値だけを返します。

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --top 10 --fields key label last_modified
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ASP.NET Core Web アプリケーションの作成](./quickstart-aspnet-core-app.md)