---
title: Azure Functions の IP アドレス
description: 関数アプリの着信 IP アドレスと送信 IP アドレスを確認する方法、およびこれらのアドレスが変更される理由について説明します。
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: glenga
ms.openlocfilehash: 83e5a15d8a7f9c01f6a180ebceb715600b8a39db
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849481"
---
# <a name="ip-addresses-in-azure-functions"></a>Azure Functions の IP アドレス

この記事では、関数アプリの IP アドレスに関連する次のトピックについて説明します。

* 関数アプリが現在使用している IP アドレスを確認する方法。
* 関数アプリの IP アドレスが変更される理由。
* 関数アプリにアクセスできる IP アドレスを制限する方法。
* 関数アプリの専用の IP アドレスを取得する方法。

IP アドレスは、個々の関数ではなく、関数アプリに関連付けられています。 着信 HTTP 要求は、着信 IP アドレスを使用して個々の関数を呼び出すことはできません。既定のドメイン名 (functionappname.azurewebsites.net) またはカスタム ドメイン名を使用する必要があります。

## <a name="function-app-inbound-ip-address"></a>関数アプリの着信 IP アドレス

各関数アプリには、1 つの着信 IP アドレスがあります。 この IP アドレスを確認するには、次のようにします。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 関数アプリに移動します。
3. **[プラットフォーム機能]** を選択します。
4. **[プロパティ]** を選択すると、**[仮想 IP アドレス]** の下に着信 IP アドレスが表示されます。

## <a name="find-outbound-ip-addresses"></a>関数アプリの送信 IP アドレス

各関数アプリには、使用可能な一連の送信 IP アドレスがあります。 関数からバックエンド データベースなどへの送信接続では、使用可能な送信 IP アドレスの 1 つが送信元 IP アドレスとして使用されます。 指定された接続でどの IP アドレスが使用されるかを事前に知ることはできません。 このため、バックエンド サービスでは、関数アプリのすべての送信 IP アドレスに対してファイアウォールを開く必要があります。

関数アプリで使用可能な送信 IP アドレスを確認するには、次のようにします。

1. [Azure Resource Explorer](https://resources.azure.com) にサインインします。
2. **[サブスクリプション] > {自分のサブスクリプション} > [プロバイダー] > [Microsoft.Web] > [サイト]** を選択します。
3. JSON パネルで、末尾が関数アプリ名の `id` プロパティを持つサイトを探します。
4. `outboundIpAddresses` と `possibleOutboundIpAddresses` を参照してください。 

現在、関数アプリでは、`outboundIpAddresses` のセットを使用できます。 `possibleOutboundIpAddresses` のセットには、関数アプリが[他の価格レベルにスケーリングする](#outbound-ip-address-changes)場合にのみ使用できる IP アドレスが含まれています。

次に示すように、[Cloud Shell](../cloud-shell/quickstart.md) を使用して、使用可能な送信 IP アドレスを確認する方法もあります。

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```
> [!NOTE]
> [従量課金プラン](functions-scale.md#consumption-plan)で実行されている関数アプリをスケーリングすると、新しい送信 IP アドレスの範囲が割り当てられる場合があります。 従量課金プランで実行する場合は、データセンター全体のホワイトリストへの登録が必要になる可能性があります。

## <a name="data-center-outbound-ip-addresses"></a>データ センターの送信 IP アドレス

関数アプリが使用する送信 IP アドレスをホワイトリストに登録する必要がある場合、別の選択肢として、関数アプリのデータ センター (Azure リージョン) をホワイトリストに登録するという方法があります。 [すべての Azure データ センターの IP アドレスが記述された JSON ファイルをダウンロード](https://www.microsoft.com/en-us/download/details.aspx?id=56519)してください。 次に、関数アプリを実行するリージョンに適用される JSON 要素を検索します。

たとえば、西ヨーロッパの JSON 要素は、次のようになっています。

```
{
  "name": "AzureCloud.westeurope",
  "id": "AzureCloud.westeurope",
  "properties": {
    "changeNumber": 9,
    "region": "westeurope",
    "platform": "Azure",
    "systemService": "",
    "addressPrefixes": [
      "13.69.0.0/17",
      "13.73.128.0/18",
      ... Some IP addresses not shown here
     "213.199.180.192/27",
     "213.199.183.0/24"
    ]
  }
}
```

 このファイルがいつ更新されるかや IP アドレスがいつ変更されるかの詳細については、[[ダウンロード センター] ページ](https://www.microsoft.com/en-us/download/details.aspx?id=56519)の **[詳細]** セクションを展開してください。

## <a name="inbound-ip-address-changes"></a>着信 IP アドレスの変更

次の操作を行うと、着信 IP アドレスが変更される**ことがあります**。

- 関数アプリを削除した後、別のリソース グループ内で再作成する。
- リソース グループとリージョンの組み合わせに含まれる最後の関数アプリを削除した後、再作成する。
- [証明書の更新](../app-service/app-service-web-tutorial-custom-ssl.md#renew-certificates)時などに SSL バインドを削除する。

関数アプリが[従量課金プラン](functions-scale.md#consumption-plan)で実行される場合、着信 IP アドレスは、上記のようなアクションを実行しなくても、変更される場合があります。

## <a name="outbound-ip-address-changes"></a>送信 IP アドレスの変更

次の操作を行うと、関数アプリの一連の使用可能な送信 IP アドレスが変更される場合があります。

* 着信 IP アドレスが変更される可能性のあるアクションを実行する。
* App Service プランの価格レベルを変更する。 すべての価格レベルについて、アプリで使用可能なすべての送信 IP アドレスのリストが `possibleOutboundIPAddresses` プロパティに含まれています。 「[IP アドレスを見つける](#find-outbound-ip-addresses)」を参照してください。

関数アプリが[従量課金プラン](functions-scale.md#consumption-plan)で実行される場合、送信 IP アドレスは、上記のようなアクションを実行しなくても、変更される場合があります。

送信 IP アドレスを意図的に変更するには、次のようにします。

1. App Service プランの価格レベルを Standard から上げるか、または Premium v2 から下げます。
2. 10 分間待ちます。
3. 最初の価格レベルに戻します。

## <a name="ip-address-restrictions"></a>IP アドレスの制限

関数アプリへのアクセス許可または拒否する IP アドレスの一覧を構成できます。 詳細については、「[Azure App Service 静的 IP 制限](../app-service/app-service-ip-restrictions.md)」を参照してください。

## <a name="dedicated-ip-addresses"></a>専用の IP アドレス

静的な専用の IP アドレスが必要な場合は、[App Service 環境](../app-service/environment/intro.md) (App Service プランの [Isolated 階層](https://azure.microsoft.com/pricing/details/app-service/)) をお勧めします。 詳細については、次を参照してください。 [App Service 環境の IP アドレス](../app-service/environment/network-info.md#ase-ip-addresses)と[App Service Environment への受信トラフィックを制御する方法](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md)します。

関数アプリが App Service 環境内で実行されるかどうかを確認するには、次のようにします。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 関数アプリに移動します。
3. **[概要]** タブを選択します。
4. App Service プランの階層は、**[App Service プラン/価格レベル]** の下に表示されます。 App Service 環境の価格レベルは、**[Isolated]** です。
 
別の方法として、[Cloud Shell](../cloud-shell/quickstart.md) を使用することもできます。

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

App Service 環境 `sku` は `Isolated` です。

## <a name="next-steps"></a>次の手順

多くの場合、IP が変更されるのは、関数アプリのスケールが変更されるためです。 [関数アプリのスケーリングの詳細](functions-scale.md)を参照してください。
