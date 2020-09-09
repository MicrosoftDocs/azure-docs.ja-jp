---
title: Azure Front Door と Azure Web Application Firewall (WAF) を使用して、Web アプリケーションをすばやくスケーリングし、保護する | Microsoft Docs
description: この記事では、Azure Front Door サービスと Web Application Firewall を使用する方法について説明します
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/06/2020
ms.author: duau
ms.openlocfilehash: 7d2978b34d4c1e6cf85c65be2e9c3292ec704be4
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89398887"
---
# <a name="quickly-scale-and-protect-a-web-application-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Azure Front Door と Azure Web Application Firewall (WAF) を使用して、Web アプリケーションをすばやくスケーリングし、保護する

多くの Web アプリケーションでは、COVID-19 による影響で、この数週間にトラフィックが急増しています。 さらに、これらの Web アプリケーションでは、サービス拒否攻撃など、悪意のあるトラフィックも確認されています。 急増するトラフィックに対応できるようスケールを拡張し、攻撃からの保護を実現するには、Azure Front Door と Azure WAF を使用するのが効果的です。これにより、処理を高速化し、キャッシュを効果的に活用し、Web アプリケーションの前面にセキュリティ層を確保することができます。 この記事では、Azure の内部と外部で実行されているすべての Web アプリケーションを対象に、この Azure Front Door と Azure WAF を迅速に設定する方法について説明します。 

このチュートリアルでは、Azure CLI を使用して WAF を設定しますが、これらの手順はすべて、Azure portal、Azure PowerShell、Azure ARM、Azure REST API でも完全にサポートされています。 

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 

このブログの手順では、Azure コマンドライン インターフェイス (CLI) を使用します。 Azure CLI の使用方法については、[こちらのガイド](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)を参照してください。

*ヒント: [Bash in Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart) を使用すると、Azure CLI の使用方法をすばやく簡単に理解できます*

Azure CLI にフロントドア拡張機能が追加されていることを確認してください

```azurecli-interactive 
az extension add --name front-door
```

注:以下に示すコマンドの詳細については、[フロント ドアの Azure CLI リファレンス](https://docs.microsoft.com/cli/azure/ext/front-door/?view=azure-cli-latest)を参照してください。

## <a name="step-1-create-an-azure-front-door-afd-resource"></a>手順 1:Azure Front Door (AFD) リソースを作成する


```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

**--backend-address**:保護しようとしているアプリケーションの完全修飾ドメイン名 (FQDN) 名を指定します。 例: myapplication.contoso.com

**--accepted-protocols**:Web アプリケーションに使用するプロトコルとして、AFD でサポートするプロトコルをすべて指定します。 例: --accepted-protocols Http Https

**--name**: AFD リソースの名前を指定します

**--resource-group**:この AFD リソースを配置するリソース グループを指定します。  リソース グループの詳細については、Azure でのリソース グループの管理に関する記事を参照してください

このコマンドが正常に実行されたら、"hostName" というキーを探し、その値をメモしてください。これは後の手順で使用します。 hostName は、作成した AFD リソースの DNS 名です

## <a name="step-2-create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>手順 2:Azure Front Door リソースで使用する Azure WAF プロファイルを作成する

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

--name Azure WAF ポリシーの名前を指定します

--resource-group この WAF リソースを配置するリソース グループを指定します。 

上記の CLI コードを実行すると、WAF ポリシーが作成されて有効化され、保護モードになります。 

注: WAF を検出モードで作成し、悪意のある要求を (ブロックせずに) ログに記録した後、その結果を見たうえで、保護モードに変更するかどうかを判断することもできます。

このコマンドが正常に実行されたら、"ID" というキーを探し、その値をメモしてください。これは後の手順で使用します。 ID フィールドは次のような形式になっています

/subscriptions/**subscription id**/resourcegroups/**resource group name**/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**WAF policy name**

## <a name="step-3-add-managed-rulesets-to-this-waf-policy"></a>手順 3:この WAF ポリシーにマネージド ルールセットを追加する

WAF ポリシーでは、マネージド ルールセットを追加することができます。これは、Microsoft によって作成および管理される一連の規則です。これを使用すると、各種の脅威に対して、すぐに保護を提供できます。 この例では、2 つのルールセットを追加します。(1) 一般的な Web の脅威から保護する既定のルールセットと、(2) 悪意のあるボットから保護する ボット保護ルールセットです

(1) 既定のルールセットを追加する

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
```

(2) ボット マネージャー ルールセットを追加する

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
```

--policy-name Azure WAF リソースに付けた名前を指定します

--resource-group この WAF リソースを配置したリソース グループを指定します。

## <a name="step-4-associate-the-waf-policy-with-the-afd-resource"></a>手順 4:WAF ポリシーを AFD リソースに関連付ける

この手順では、作成した WAF ポリシーを、Web アプリケーションの前面にある AFD リソースに関連付けます。

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

--name AFD リソースに付けた名前を指定します

--resource-group Azure Front Door リソースを配置したリソース グループを指定します。

--set AFD リソースに関連付けられている frontendEndpoint の WebApplicationFirewallPolicyLink 属性を、新規作成した WAF ポリシーで更新します。 WAF ポリシーの ID には、上記の手順 2 でメモしたものを使用します

注: 上の例は、カスタム ドメインを使用しない場合の例です

Web アプリケーションへのアクセスにカスタム ドメインを使用しない場合、手順 5 は省略できます。 その場合は、手順 1 で取得したホスト名をエンド ユーザーに提供することで、Web アプリケーションにアクセスしてもらうことができます

## <a name="step-5-configure-custom-domain-for-your-web-application"></a>手順 5:Web アプリケーションのカスタム ドメインを構成する

Web アプリケーションのカスタム ドメイン名 (顧客がアプリケーションを参照するのに使うドメイン名。たとえば、 www.contoso.com) は、当初、AFD が導入される前の実行場所を指していました。 ここでのアーキテクチャ変更によってアプリケーションの前面に AFD と WAF が追加された後は、そのカスタム ドメインに対応する DNS エントリが、この AFD リソースを指すようにする必要があります。 これを行うには、DNS サーバーのこのエントリを、手順 1 でメモした AFD ホスト名にマップし直します。

DNS レコードを更新するための具体的な手順は、DNS サービス プロバイダーによって異なりますが、Azure DNS を使用して DNS 名をホストしている場合は、[DNS レコードを更新する手順](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-cli)に関するドキュメントを参照して、AFD hostName をポイントできます。 

ここで注意すべき重要な点として、ユーザーがゾーンの頂点 (たとえば、contoso.com) を使用して Web サイトにアクセスするようにしたい場合は、Azure DNS とその [ALIAS レコード タイプ](https://docs.microsoft.com/azure/dns/dns-alias)を使用して DNS 名をホストする必要があります。 

また、AFD 構成を更新して[このカスタム ドメインを追加](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain)し、AFD でこのマッピングが認識されるようにする必要があります。

最後に、カスタム ドメインを使用して Web アプリケーションにアクセスする場合で、HTTPS プロトコルを有効にする場合は、[AFD でのカスタム ドメイン設定に対応した証明書](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain-https)を用意する必要があります。 

## <a name="step-6-lock-down-your-web-application"></a>手順 6:Web アプリケーションをロック ダウンする

実施するべきベスト プラクティスの 1 つとして、AFD のエッジのみが Web アプリケーションと通信できるようにすることをお勧めします。 この措置を講じれば、AFD 保護をバイパスしてアプリケーションに直接アクセスすることはできなくなります。 このロック ダウンを行うには、[AFD の FAQ セクション](https://docs.microsoft.com/azure/frontdoor/front-door-faq)にアクセスし、バックエンドのロックダウン (AFD のみによるアクセス) に関する質問を参照してください。
