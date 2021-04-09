---
title: Internet Analyzer クライアントの埋め込み | Microsoft Docs
description: この記事では、Internet Analyzer JavaScript クライアントをアプリケーションに埋め込む方法について説明します。
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: how-to
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 0d4b27b85ac7bc61e14a79f29e4e26ec4973ced1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84744053"
---
# <a name="embed-the-internet-analyzer-client"></a>Internet Analyzer クライアントの埋め込み

この記事では、JavaScript クライアントをアプリケーションに埋め込む方法について説明します。 テストを実行し、スコアカード分析を受けるには、このクライアントのインストールが必要です。 **プロファイル固有の JavaScript クライアントは、最初のテストが構成された後に提供されます。** そこから、新しいスクリプトを埋め込まなくても、そのプロファイルに対してテストを追加または削除することができます。 Internet Analyzer の詳細については、[概要](internet-analyzer-overview.md)について説明するページを参照してください。 

> [!IMPORTANT]
> このパブリック プレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあるか、Azure の場所によっては利用できない場合があります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。
>

## <a name="before-you-begin"></a>開始する前に

Internet Analyzer が正常に機能するには、Azure やその他 Microsoft サービスへのアクセスを必要とします。 クライアントを埋め込む前に、`fpc.msedge.net` と構成済みのエンドポイント URL ([CLI](internet-analyzer-cli.md) を通じて表示) へのネットワーク アクセスを許可してください。

## <a name="find-the-client-script-url"></a>クライアント スクリプトの URL を検索する

スクリプトの URL は、テストの構成後に Azure portal または Azure CLI を使用して見つけることができます。 詳細については、[Internet Analyzer リソースの作成](internet-analyzer-create-test-portal.md)に関するページを参照してください。

方法 1. Azure portal で、[このリンク](https://aka.ms/InternetAnalyzerPreviewPortal)を使用して、Azure Internet Analyzer のプレビュー ポータル ページを開きます。 **[設定] > [構成]** に移動して Internet Analyzer のプロファイルに移動し、スクリプトの URL を確認します。

方法 2. Azure CLI を使用して、`scriptFileUri` プロパティを確認します。
```azurecli-interactive
    az extension add --name internet-analyzer    
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
```

## <a name="client-details"></a>クライアントの詳細

このスクリプトは、プロファイルとテストで使用する目的でのみ生成されます。 スクリプトは読み込まれた後、2 秒の遅延で実行されます。 まず、Internet Analyzer サービスに接続して、テストで構成されているエンドポイントの一覧を取得します。 その後、測定を実行し、結果の時間数を Internet Analyzer サービスにアップロードします。

## <a name="client-examples"></a>クライアントの例

これらの例では、クライアントの JavaScript を Web ページまたはアプリケーションに埋め込むための基本的な方法をいくつか示します。 スクリプト URL のプロファイル ID の例として、`0bfcb32638b44927935b9df86dcfe397` を使用します。

### <a name="run-on-page-load"></a>ページ読み込み時に実行する
最も簡単な方法は、meta タグ ブロック内でスクリプト タグを使用することです。 このタグは、ページの読み込みごとにスクリプトを 1 回実行します。

```html
<html>
<meta>
    <script src="//fpc.msedge.net/client/v2/0bfcb32638b44927935b9df86dcfe397/ab.min.js"></script>
</meta>
<body></body>
</html>
```

## <a name="next-steps"></a>次のステップ

[Internet Analyzer の FAQ](internet-analyzer-faq.md) を読む
