---
title: Azure Media Player のローカライズ
description: 英語以外のロケールのユーザーに対する複数言語のサポート。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 8a459d1fba4353b4b3e092e83a759314cc455ead
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87087290"
---
# <a name="azure-media-player-localization"></a>Azure Media Player のローカライズ #

複数言語のサポートにより、英語以外のロケールのユーザーがプレーヤーをネイティブに操作できるようになります。 Azure Media Player では、言語のグローバル辞書を使用してインスタンス化が行われます。これにより、ページの言語に基づいてエラー メッセージがローカライズされます。 開発者は、強制的に設定した言語でプレーヤー インスタンスを作成することもできます。 既定の言語は英語 (en) です。

> [!NOTE]
> この機能は現在もいくつかのテストが行われているため、バグが存在する可能性があります。

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Azure Media Player は現在、以下の言語と対応する言語コードをサポートしています。

| Language            | コード | Language                | コード   | Language                | コード         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| 英語 {既定}   | en   | クロアチア語                | hr     | ルーマニア語                | ro           |
| アラビア語              | ar   | ハンガリー語               | hu     | スロバキア語                  | sk           |
| ブルガリア語           | bg   | インドネシア語              | id     | スロヴェニア語                 | sl           |
| カタロニア語             | ca   | アイスランド語               | :     | セルビア語 - キリル      | sr-cyrl-cs   |
| チェコ語               | cs   | イタリア語                 | it     | セルビア語 - ラテン         | sr-latn-rs   |
| デンマーク語              | da   | 日本語                | ja     | ロシア語                 | ru           |
| ドイツ語              | de   | カザフ語                  | kk     | スウェーデン語                 | sv           |
| ギリシャ語               | el   | 韓国語                  | ko     | タイ語                    | th           |
| スペイン語             | es   | リトアニア語              | lt     | タガログ語                 | tl           |
| エストニア語            | et   | ラトビア語                 | lv     | トルコ語                 | tr           |
| バスク語              | eu   | マレーシア語               | ms     | ウクライナ語               | uk           |
| ペルシャ語               | fa   | ノルウェー語 - ブークモール     | nb     | ウルドゥ語                    | ur           |
| フィンランド語             | fi   | オランダ語                   | nl     | ベトナム語              | vi           |
| フランス語              | fr   | ノルウェー語 - ニーノシュク     | nn     | 簡体中国語    | zh-hans      |
| ガリシア語            | gl   | ポーランド語                  | pl     | 繁体中国語   | zh-hant      |
| ヘブライ語              | he   | ポルトガル語 - ブラジル     | pt-br  |                         |              |
| ヒンディー語               | hi   | ポルトガル語 - ポルトガル   | pt-pt  |                         |              |


> [!NOTE]
> ローカライズが行われないようにするには、言語を英語にする必要があります。

## <a name="next-steps"></a>次のステップ ##

- [Azure Media Player クイックスタート](azure-media-player-quickstart.md)
