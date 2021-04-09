---
title: チュートリアル:Azure Front Door と Azure Web Application Firewall (WAF) を使用して、Web アプリケーションをスケーリングし、保護する
description: このチュートリアルでは、Azure Front Door サービスと共に Azure Web Application Firewall を使用する方法について説明します。
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: duau
ms.openlocfilehash: f894fd5ca7bb98c48b106c13b8fe6130b2c76c6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201058"
---
# <a name="tutorial-quickly-scale-and-protect-a-web-application-by-using-azure-front-door-and-azure-web-application-firewall-waf"></a>チュートリアル:Azure Front Door と Azure Web Application Firewall (WAF) を使用して、Web アプリケーションをすばやくスケーリングし、保護する

多くの Web アプリケーションで、新型コロナウイルス感染症による影響から、この数週間のトラフィックが急増しています。 また、これらの Web アプリケーションでは、サービス拒否攻撃など、悪意のあるトラフィックも確認されています。 トラフィックの急増への対応としてアプリケーションをスケールアウトし、かつ攻撃から保護するための効果的な方法があります。この方法では、Web アプリの前面に、高速化、キャッシュ、セキュリティのレイヤーとして、Azure Front Door と Azure WAF を構成します。 この記事では、Azure の内部または外部で実行されているすべての Web アプリに対して、Azure Front Door と Azure WAF を構成する方法について説明します。 

このチュートリアルでは、Azure CLI を使用して WAF を構成します。 Azure portal、Azure PowerShell、Azure Resource Manager、または Azure REST API を使用して、同じことを実現できます。 

このチュートリアルでは、次の作業を行う方法について説明します。
> [!div class="checklist"]
> - Front Door を作成する。
> - Azure WAF ポリシーを作成する。
> - WAF ポリシーに対するルール セットを構成する。
> - WAF ポリシーと Front Door を関連付ける。
> - カスタム ドメインを構成する。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

- このチュートリアルの手順では Azure CLI を使用します。 Azure CLI の使用方法については、[こちらのガイドを参照してください](/cli/azure/get-started-with-azure-cli)。

  > [!TIP] 
  > [Bash in Azure Cloud Shell](../cloud-shell/quickstart.md) を使用すると、Azure CLI をすばやく簡単に使い始めることができます。

- Azure CLI に `front-door` 拡張機能を確実に追加してください。

   ```azurecli-interactive 
   az extension add --name front-door
   ```

> [!NOTE] 
> このチュートリアルで使用されているコマンドの詳細については、[Front Door 用の Azure CLI のリファレンス](/cli/azure/ext/front-door)に関する記事を参照してください。

## <a name="create-an-azure-front-door-resource"></a>Azure Front Door リソースを作成する

```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

`--backend-address`: 保護対象のアプリケーションの完全修飾ドメイン名 (FQDN)。 たとえば、「 `myapplication.contoso.com` 」のように入力します。

`--accepted-protocols`: Azure Front Door で Web アプリケーションに対してサポートするプロトコルを指定します。 たとえば、「 `--accepted-protocols Http Https` 」のように入力します。

`--name`: Azure Front Door リソースの名前。

`--resource-group`: この Azure Front Door リソースを配置するリソース グループ。 リソース グループの詳細については、[Azure でのリソース グループの管理](../azure-resource-manager/management/manage-resource-groups-portal.md)に関する記事を参照してください。

このコマンドを実行すると取得される応答で、キー `hostName` を探します。 この値は、後のステップで必要になります。 `hostName` は、作成した Azure Front Door リソースの DNS 名です。

## <a name="create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Azure Front Door リソースで使用する Azure WAF プロファイルを作成する

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

`--name`: 新しい Azure WAF ポリシーの名前。

`--resource-group`: この WAF リソースを配置するリソース グループ。 

上の CLI コードを実行すると、WAF ポリシーが作成されて有効化され、保護モードになります。 

> [!NOTE] 
> 保護モードの使用を決定する前に、検出モードで WAF ポリシーを作成し、悪意のある要求がどのように検出されてログに記録されるかを、(それらをブロックしないで) 確認することができます。

このコマンドを実行すると取得される応答で、キー `ID` を探します。 この値は、後のステップで必要になります。 

`ID` フィールドの形式は次のとおりです。

/subscriptions/**subscription id**/resourcegroups/**resource group name**/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**WAF policy name**

## <a name="add-managed-rule-sets-to-the-waf-policy"></a>WAF ポリシーにマネージド ルール セットを追加する

WAF ポリシーにマネージド ルール セットを追加することができます。 マネージド ルール セットは、Microsoft によって構築されて管理される一連のルールであり、あるクラスの脅威を保護するのに役立ちます。 この例では、2 つのルール セットを追加します。
- 既定のルール セット。一般的な Web の脅威からユーザーを保護するのに役立ちます。 
- ボット保護ルール セット。悪意のあるボットから保護するのに役立ちます。

既定のルール セットを追加します。

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
   ```

ボット保護ルール セットを追加します。

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
   ```

`--policy-name`: Azure WAF リソースに指定した名前。

`--resource-group`: WAF リソースを配置したリソース グループ。

## <a name="associate-the-waf-policy-with-the-azure-front-door-resource"></a>WAF ポリシーと Azure Front Door リソースを関連付ける

このステップでは、作成した WAF ポリシーを、Web アプリケーションの前面にある Azure Front Door リソースと関連付けます。

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

`--name`: Azure Front Door リソースに指定した名前。

`--resource-group`: Azure Front Door リソースを配置したリソース グループ。

`--set`: ここでは、Azure Front Door リソースに関連付けられている `frontendEndpoint` の `WebApplicationFirewallPolicyLink` 属性を、新しい WAF ポリシーに更新します。 このチュートリアルで前に WAF プロファイルを作成したときに取得した応答から、WAF ポリシーの ID を取得する必要があります。

 > [!NOTE] 
> 前の例は、カスタム ドメインを使用していない場合に適用されます。 Web アプリケーションへのアクセスにカスタム ドメインを使用していない場合は、次のセクションを省略できます。 その場合は、Azure Front Door リソースを作成したときに取得した `hostName` を顧客に提供します。 この `hostName` を使用して、Web アプリケーションにアクセスします。

## <a name="configure-the-custom-domain-for-your-web-application"></a>Web アプリケーションのカスタム ドメインを構成する

Web アプリケーションのカスタム ドメイン名は、顧客がアプリケーションを参照するために使用するものです。 例: www.contoso.com。 最初、このカスタム ドメイン名は、Azure Front Door を導入する前に実行されていた場所を指していました。 Azure Front Door と WAF をアプリケーションの前面に追加した後、そのカスタム ドメインに対応する DNS エントリによって、Azure Front Door リソースが指し示されている必要があります。 この変更を行うには、Azure Front Door リソースを作成したときに記録した Azure Front Door の `hostName` に、DNS サーバーのエントリを再マップします。

DNS レコードを更新するための具体的な手順は、DNS サービス プロバイダーによって異なります。 Azure DNS を使用して DNS 名をホストしている場合は、[DNS レコードの更新手順](../dns/dns-operations-recordsets-cli.md)に関するドキュメントを参照して、Azure Front Door の `hostName` を指すようにすることができます。 

ゾーン頂点 (contoso.com など) を使用した Web サイトへのアクセスを顧客に行ってもらう必要がある場合には、注意すべき重要な点が 1 つあります。 この場合は、Azure DNS とその[別名レコードの種類](../dns/dns-alias.md)を使用して、DNS 名をホストする必要があります。 

また、このマッピングが認識されるように、Azure Front Door の構成を更新して、それに[カスタム ドメインを追加する](./front-door-custom-domain.md)必要があります。

最後に、カスタム ドメインを使用して Web アプリケーションにアクセスし、HTTPS プロトコルを有効にする場合についてです。 この場合は、[Azure Front Door でカスタム ドメインに対して証明書を設定する](./front-door-custom-domain-https.md)必要があります。 

## <a name="lock-down-your-web-application"></a>Web アプリケーションをロック ダウンする

Azure Front Door のエッジのみが確実に Web アプリケーションと通信できるようにすることをお勧めします。 そうすることで、誰も Azure Front Door の保護をバイパスしてアプリケーションに直接アクセスすることができなくなります。 このロックダウンを実現するには、「[バックエンドへのアクセスを Azure Front Door のみにロックダウンするにはどうしたらよいですか?](./front-door-faq.md#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで使用したリソースが不要になったら、[az group delete](/cli/azure/group#az-group-delete) コマンドを使用して、リソース グループ、Front Door、WAF ポリシーを削除してください。

```azurecli-interactive
  az group delete \
    --name <>
```
`--name`: このチュートリアルで使用したすべてのリソースのリソース グループの名前。

## <a name="next-steps"></a>次の手順

Front Door のトラブルシューティングを行う方法については、トラブルシューティング ガイドを参照してください。

> [!div class="nextstepaction"]
> [ルーティングの一般的な問題のトラブルシューティング](front-door-troubleshoot-routing.md)