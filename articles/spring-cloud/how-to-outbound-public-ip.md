---
title: Azure Spring Cloud で送信パブリック IP アドレスを識別する方法
description: データベース、ストレージ、キー コンテナーなど、外部リソースと通信するために静的送信パブリック IP アドレスを表示する方法。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 04174b9cffb7e853dee235a4141ccda74a7847c6
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877416"
---
# <a name="how-to-identify-outbound-public-ip-addresses-in-azure-spring-cloud"></a>Azure Spring Cloud で送信パブリック IP アドレスを識別する方法

このページでは、Azure Spring Cloud アプリケーションの静的な送信パブリック IP アドレスを表示する方法について説明します。  パブリック IP は、データベース、ストレージ、キー コンテナーなど、外部リソースとの通信に使用されます。

## <a name="how-ip-addresses-work-in-azure-spring-cloud"></a>Azure Spring Cloud で IP アドレスが動作するしくみ

Azure Spring Cloud サービスには、1 つまたは複数の送信パブリック IP アドレスが与えられます。 送信パブリック IP アドレスの数は、レベルやその他の要因によって異なる場合があります。 

通常、送信パブリック IP アドレスは不変であり、同じままですが例外があります。

## <a name="when-outbound-ips-change"></a>送信 IP はいつ変更されるか

各 Azure Spring Cloud インスタンスには常に、決まった数の送信パブリック IP アドレスが与えられます。 アプリケーションからのあらゆる送信接続 (バックエンド データベースへの接続など) で、いずれかの送信パブリック IP アドレスを送信元 IP アドレスとして使用します。 IP アドレスは実行時にランダムに選択されるため、バックエンド サービスでは、すべての送信 IP アドレスに対してファイアウォールを開いておく必要があります。

次のいずれかの操作を実行すると、送信 IP アドレスの数が変わります。

- Azure Spring Cloud インスタンスの階層をアップグレードする。
- ビジネス ニーズに合わせて送信パブリック IP を増やすようにサポート チケットを作成する。

## <a name="find-outbound-ips"></a>IP アドレスを見つける

Azure portal でサービス インスタンスによって現在使用されている送信パブリック IP アドレスを見つけるには、インスタンスの左側ナビゲーション ウィンドウで **[ネットワーク]** をクリックします。 **[送信 IP アドレス]** フィールドに表示されます。

Cloud Shell で次のコマンドを実行することで、同じ情報を見つけることができます。

```Azure CLI
az spring-cloud show --resource-group <group_name> --name <service_name> --query properties.networkProfile.outboundIPs.publicIPs --output tsv
```

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
* [Azure リソース用マネージド ID の詳細](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Azure Spring Cloud のキー コンテナーの詳細](spring-cloud-tutorial-managed-identities-key-vault.md)
