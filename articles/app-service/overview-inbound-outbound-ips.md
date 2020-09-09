---
title: 受信/送信 IP アドレス
description: Azure App Service で受信および送信 IP アドレスがどのように使用されるか、いつ変更されるかについて、およびアプリのアドレスを見つける方法について説明します。
ms.topic: article
ms.date: 06/06/2019
ms.custom: seodec18
ms.openlocfilehash: 8bcd80fde95e467513590f3ed09b1dadd2646aee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537629"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Azure App Service における受信 IP アドレスと送信 IP アドレス

[Azure App Service](overview.md) は、[App Service Environment](environment/intro.md) 以外はマルチテナント サービスです。 App Service Environment ([分離レベル](https://azure.microsoft.com/pricing/details/app-service/)) に含まれていないアプリは、他のアプリとネットワーク インフラストラクチャを共有します。 その結果、アプリの受信 IP アドレスと送信 IP アドレスが異なる可能性があり、特定の状況では変更される可能性もあります。 

[App Service Environment](environment/intro.md) は専用のネットワーク インフラストラクチャを使用するため、App Service Environment で実行されるアプリは、受信接続と送信接続の両方で、静的な専用 IP アドレスを取得します。

## <a name="when-inbound-ip-changes"></a>受信 IP はいつ変更されるか

スケールアウトされたインスタンスの数に関係なく、各アプリは、1 つの受信 IP アドレスを持ちます。 受信 IP アドレスは、次の操作のいずれかを実行したときに変更される可能性があります。

- アプリを削除した後、別のリソース グループ内に再作成する。
- リソース グループ _と_ リージョンの組み合わせに含まれる最後のアプリケーションを削除した後、再作成する。
- 証明書の更新中などに既存の TLS バインドを削除する (「[証明書の更新](configure-ssl-certificate.md#renew-certificate)」を参照してください)。

## <a name="find-the-inbound-ip"></a>受信 IP を検索する

ローカル ターミナルで次のコマンドを実行します。

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>静的な受信 IP を取得する

専用の静的 IP アドレスをアプリで使用したい場合があります。 静的な受信 IP アドレスを取得するには、[カスタム ドメインをセキュリティで保護する](configure-ssl-bindings.md#secure-a-custom-domain)必要があります。 アプリをセキュリティで保護するための TLS 機能を実質的に必要としない場合は、このバインドのために自己署名証明書をアップロードすることもできます。 IP ベースの TLS バインドでは、証明書は IP アドレス自体にバインドされるため、App Service は、静的 IP アドレスをプロビジョニングすることで、これを実現できます。 

## <a name="when-outbound-ips-change"></a>送信 IP はいつ変更されるか

スケールアウトされたインスタンスの数に関係なく、各アプリは、特定の時点で所定の数の送信 IP アドレスを持ちます。 App Service アプリからのすべての送信接続 (バックエンド データベースへの接続など) では、いずれかの送信 IP アドレスを送信元 IP アドレスとして使用します。 特定のアプリ インスタンスが送信接続を行うときに使用する IP アドレスを事前に知ることはできないため、バックエンド サービスは、アプリのすべての送信 IP アドレスに対してファイアウォールを開く必要があります。

アプリの送信 IP アドレス セットは、アプリを下位レベル (**Basic**、**Standard**、および **Premium**) と **Premium V2**レベルの間でスケーリングするときに変更されます。

アプリが使用できるすべての可能な送信 IP アドレスは、価格レベルに関係なく、`possibleOutboundIpAddresses` プロパティを調べるか、Azure portal の **[プロパティ]** ブレードの **[追加の送信 IP アドレス]** で確認できます。 「[IP アドレスを見つける](#find-outbound-ips)」を参照してください。

## <a name="find-outbound-ips"></a>IP アドレスを見つける

Azure ポータルで、現在アプリで使用されている送信 IP アドレスを見つけるには、アプリの左側のナビゲーションで **[プロパティ]** をクリックします。 IP アドアレスは **[送信 IP アドレス]** フィールドに表示されています。

[Cloud Shell](../cloud-shell/quickstart.md) で次のコマンドを実行することで、同じ情報を見つけることができます。

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

アプリで考えられる "_すべて_" の送信 IP アドレスを見つけるには、価格レベルに関係なく、アプリの左側にあるナビゲーションで **[プロパティ]** をクリックします。 IP アドアレスは **[追加の送信 IP アドレス]** フィールドに表示されています。

[Cloud Shell](../cloud-shell/quickstart.md) で次のコマンドを実行することで、同じ情報を見つけることができます。

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>次のステップ

ソース IP アドレスによって受信トラフィックを制限する方法を確認します。

> [!div class="nextstepaction"]
> [静的 IP の制限事項](app-service-ip-restrictions.md)
