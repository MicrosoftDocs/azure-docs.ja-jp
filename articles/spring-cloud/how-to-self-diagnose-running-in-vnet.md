---
title: VNET の Azure Spring Cloud で自己診断する方法
description: VNET で実行中の Azure Spring Cloud で問題を自己診断して解決する方法について説明します。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: 5407213b62902326d53b73e42ee3af1ba9b11524
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877535"
---
# <a name="self-diagnose-running-azure-spring-cloud-in-vnet"></a>VNET での Azure Spring Cloud の自己診断
Azure Spring Cloud 診断では、仮想ネットワークで実行される構成なしの対話型トラブルシューティング アプリがサポートされています。 Azure Spring Cloud 診断は、問題点を特定し、問題のトラブルシューティングと解決に役立つ情報を示します。

## <a name="navigate-to-the-diagnostics-page"></a>診断ページに移動する
次の手順を実行すると、ネットワーク アプリケーションの診断を開始できます。
1. Azure portal にサインインします。
1. Azure Spring Cloud の [概要] ページに移動します。
1. 左側のナビゲーション ウィンドウのメニューで **[問題の診断と解決]** を選択します。
1. 3 番目のカテゴリである **[ネットワーク]** を選択します。

   ![自己診断のタイトル](media/spring-cloud-self-diagnose-vnet/self-diagostic-title.png)

## <a name="view-a-diagnostic-report"></a>診断レポートの表示
**[ネットワーク]** カテゴリをクリックすると、VNet が挿入された Azure Spring Cloud に関するネットワーク固有の問題として、**DNS 解決** と **必要なアウトバウンド トラフィック** に関する問題が 2 件表示されます。

   ![自己診断オプション](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-req-outbound-options.png)

目的の問題を選択して、診断レポートを表示します。 診断の概要が次のように表示されます。 

* *リソースが削除されました。*
* *リソースが仮想ネットワークに展開されていません*。

一部の結果には、関連ドキュメントが含まれています。 結果は、サブネットごとに分けて表示されます。

## <a name="dns-resolution"></a>DNS の解決 
**[DNS 解決]** を選択すると、アプリケーションで DNS の問題があるかどうかが結果に示されます。  正常なアプリについては、次のように表示されます。

* *DNS の問題は、サブネット 'subnet01' で問題なく解決されました*。
* *DNS の問題は、サブネット ' subnet02' で問題なく解決されました*。

次の診断レポートの例は、アプリケーションの正常性が不明であることを示しています。 レポート期間には、正常性状態がレポートされた時刻は含まれません。  コンテキストの終了時刻が *2021-03-03T04:20:00Z* であるとします。 **[DNS Resolution Table Renderings]\(DNS 解決テーブル レンダリング\)** の最新の TIMESTAMP は前日の *2021-03-03T03:39:00Z* です。 ネットワークがブロックされているため、正常性チェック ログが送信されていない可能性があります。 

正常性状態が不明の場合の結果には、関連するドキュメントが含まれています。  左山かっこをクリックすると、ドロップダウン画面が表示されます。

   ![DNS 不明](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-unknown.png)

プライベート DNS ゾーンのレコード セットが正しく構成されていない場合は、`Failed to resolve the Private DNS in subnet xxx` のような深刻な結果が返されます。 

**[DNS Resolution Table Renderings]\(DNS 解決テーブル レンダリング\)** のドロップダウンに、詳細なメッセージの情報が表示され、ここから構成を確認できます。

## <a name="required-outbound-traffic"></a>必要なアウトバウンド トラフィック 

**[Required Outbound Traffic]\(必要なアウトバウンド トラフィック\)** を選択すると、アプリケーションにアウトバウンド トラフィックの問題があるかどうかが結果に示されます。  正常なアプリについては、次のように表示されます。

* *必要なアウトバウンド トラフィックは、サブネット 'subnet01' で問題なく解決されました。
* *必要なアウトバウンド トラフィックは、サブネット 'subnet02' で問題なく解決されました。

いずれかのサブネットが NSG またはファイアウォール規則によってブロックされ、自分ではログをブロックしていない場合は、次のようなエラーが発生します。 [お客様の責任](spring-cloud-vnet-customer-responsibilities.md)を見逃していないかを確認します。
    
   ![エンドポイントのエラー](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-failed.png)

`Required Outbound Traffic Table Renderings` に 30 分間データがない場合、結果は `health status unknown` になります。 ネットワークがブロックされているか、ログ サービスが停止している可能性があります。

   ![診断対象のエンドポイントが不明](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-unknown.png)

## <a name="see-also"></a>関連項目
* [Azure Spring Cloud で自己診断する方法](spring-cloud-howto-self-diagnose-solve.md)