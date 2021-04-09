---
title: カスタム エンドポイントを作成する | Microsoft Docs
description: この記事では、Internet Analyzer リソースを使用して測定するカスタム エンドポイントを構成する方法について説明します。
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: how-to
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 6ca71bce726f16eeacc96e10eb654bb3e21c5924
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84744087"
---
# <a name="measure-custom-endpoints-to-evaluate-in-your-internet-analyzer-tests"></a>Internet Analyzer テストで評価するカスタム エンドポイントを測定する 

この記事では、Internet Analyzer テストの一部として測定するカスタム エンドポイントを設定する方法を示します。 カスタム エンドポイントは、オンプレミスのワークロード、他のクラウド プロバイダーで実行されているワークロード、Azure のカスタム構成を評価するのに役立ちます。  1 つのエンドポイントが Azure リソースの場合、1 つのテストで 2 つのカスタム エンドポイントを比較できます。 Internet Analyzer の詳細については、[概要](internet-analyzer-overview.md)について説明するページを参照してください。 

> [!IMPORTANT]
> このパブリック プレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあるか、Azure の場所によっては利用できない場合があります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。
>

## <a name="before-you-begin"></a>開始する前に

Internet Analyzer リソースを設定し、[カスタム エンドポイント] オプションを選択してください。 Internet Analyzer は、カスタム エンドポイントがインターネットにアクセス可能であることを前提としています。 詳細については、[Internet Analyzer リソースの作成](internet-analyzer-create-test-portal.md)に関するページを参照してください。


## <a name="create-custom-endpoint"></a>カスタム エンドポイントを作成する

1. [ここ](https://fpc.msedge.net/apc/trans.gif)から透明な 1 ピクセルのテスト画像をダウンロードします。 この 1 ピクセルの画像は、クライアントの JavaScript がパフォーマンスを測定するためにフェッチする資産です。
2. お使いのカスタム Web アプリケーションで、パブリックにアクセス可能なパスにテスト画像を配置します。 パスは HTTPS 経由で機能する必要があります。 
3. テスト作成時に、カスタム エンド ポイントの完全な URL (例: `https://contoso.com/test/trans.gif`) をカスタム エンドポイントのフィールドにコピーします。

## <a name="next-steps"></a>次のステップ

[Internet Analyzer の FAQ](internet-analyzer-faq.md) を読む

