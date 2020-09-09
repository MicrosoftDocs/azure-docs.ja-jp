---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 6dfc96df3623e44a4ba513c238f21482c2a2989d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87013118"
---
アプリケーションの DefaultAzureCredential メソッドは、3 つの環境変数 (`AZURE_CLIENT_ID`、`AZURE_CLIENT_SECRET`、`AZURE_TENANT_ID`) に依存しています。 これらの変数を、`export VARNAME=VALUE` 形式を使用して「サービス プリンシパルの作成」手順で返された clientId、clientSecret、および tenantId の各値に設定します。 (この方法では、現在のシェルの変数とシェルから作成されたプロセスのみが設定されます。これらの変数を環境に永続的に追加するには、`/etc/environment ` ファイルを編集します。) 

また、キー コンテナー名を `KEY_VAULT_NAME` という環境変数として保存する必要もあります。

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````
