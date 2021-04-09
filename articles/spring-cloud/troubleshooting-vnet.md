---
title: 仮想ネットワークでの Azure Spring Cloud のトラブルシューティング
description: Azure Spring Cloud 仮想ネットワークのトラブルシューティング ガイド。
author: mikedodaro
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b2369a6380c7b74302d32366d0604fca616fc3ed
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877609"
---
# <a name="troubleshooting-azure-spring-cloud-in-virtual-networks"></a>仮想ネットワークでの Azure Spring Cloud のトラブルシューティング

このドキュメントは、仮想ネットワークで Azure Spring Cloud を使用する際に発生する可能性のあるさまざまな問題を解決するのに役立ちます。

## <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Azure Spring Cloud サービス インスタンスの作成で問題が発生した

Azure Spring Cloud のインスタンスを作成するには、仮想ネットワークにインスタンスをデプロイするための十分なアクセス許可が必要です。  Spring Cloud サービス インスタンス自体は、[仮想ネットワークに Azure Spring Cloud サービスのアクセス許可を付与する](spring-cloud-tutorial-deploy-in-azure-virtual-network.md#grant-service-permission-to-the-virtual-network)必要があります。

Azure portal を使用して Azure Spring Cloud サービス インスタンスを設定すると、Azure portal によってアクセス許可が検証されます。

[Azure CLI](/cli/azure/get-started-with-azure-cli) を使用して Azure Spring Cloud サービス インスタンスを設定するには、次のことを確認します。

- サブスクリプションがアクティブである。
- 場所が Azure Spring Cloud でサポートされている。
- インスタンスのリソース グループが既に作成されている。
- リソース名が、名前付け規則に準拠している 使用できる文字は小文字、数字、およびハイフンのみです。 先頭の文字は英字にする必要があります。 末尾の文字は、文字または数字にする必要があります。 値は 2 文字以上 32 文字以下にする必要があります。

Resource Manager テンプレートを使用して Azure Spring Cloud サービス インスタンスを設定するには、[Azure Resource Manager テンプレートの構造と構文の詳細](../azure-resource-manager/templates/template-syntax.md)に関する記事を参照してください。

### <a name="common-creation-issues"></a>作成に関する一般的な問題

| エラー メッセージ | 修正方法 |
|------|------|
| Azure Spring Cloud によって作成されたリソースは、ポリシーによって許可されていません。 | ネットワーク リソースは、Azure Spring Cloud を独自の仮想ネットワークにデプロイするときに作成されます。 これらの作成をブロックする [Azure Policy](../governance/policy/overview.md) が定義されているかどうかを確認してください。 作成できなかったリソースがエラー メッセージに表示されます。 |
| サブネットにルート テーブルが関連付けられている場合は、関連付けを解除してください。 | 現在、既存のルート テーブルに関連付けられているサブネットに Azure Spring Cloud をデプロイすることはサポートされていません。関連付けを解除してから、もう一度お試しください。 |
| 必要なトラフィックが許可リストに登録されていません。 | 必要なトラフィックを許可リストに登録するには、「[VNET での Azure Spring Cloud の実行に関するお客様の責任](spring-cloud-vnet-customer-responsibilities.md)」を参照してください。 |

## <a name="my-application-cant-be-registered"></a>アプリケーションを登録できない

この問題は、仮想ネットワークにカスタム DNS 設定が構成されている場合に発生します。 この場合、Azure Spring Cloud で使用されるプライベート DNS ゾーンが無効になります。 カスタム DNS サーバーにアップストリーム DNS サーバーとして Azure DNS IP 168.63.129.16 を追加します。

## <a name="other-issues"></a>その他の問題

[Azure Spring Cloud に関する一般的な問題のトラブルシューティング](./spring-cloud-troubleshoot.md)。