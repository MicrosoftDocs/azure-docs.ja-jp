---
title: Azure Percept Audio の使用を開始する
description: Azure Percept Audio デバイスを Azure Percept DK に接続する方法について説明します。
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: 660f03ce248a27a00fdd443964fbdba2fe3adeb0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179274"
---
# <a name="azure-percept-audio-setup"></a>Azure Percept Audio の設定

Azure Percept Audio は、Azure Percept DK ですぐに使用できます。 特別な設定は必要ありません。

## <a name="prerequisites"></a>前提条件

- Azure Percept DK (開発キット)
- Azure Percept Audio
- [Azure サブスクリプション](https://azure.microsoft.com/free/)
- [Azure Percept DK セットアップ エクスペリエンス](./quickstart-percept-dk-set-up.md): 開発キットを Wi-Fi ネットワークに接続し、IoT ハブを作成して、開発キットを IoT ハブに接続済みであること
- 3\.5 mm オーディオ ジャックに接続できるスピーカーまたはヘッドホン (オプション)

## <a name="connecting-your-devices"></a>デバイスを接続する

1. 同梱されている Micro USB to USB Type-A ケーブルを使用して、Azure Percept Audio デバイスを Azure Percept DK キャリア ボードに接続します。 ケーブルの Micro USB 側をインターポーザー (開発者) ボードに、Type-A 側を Percept DK キャリア ボードに接続します。
1. (省略可能) "Line Out" というラベルの付いたオーディオ ジャックを介して、スピーカーまたはヘッドホンを Azure Percept Audio に接続します。 これによって音声アシスタントの音声応答を聴くことができます。 スピーカーやヘッドホンを接続していなくても、応答はテキストとしてデモ ウィンドウに表示されます。 

1. 開発キットの電源をオンにします。 インターポーザー ボードの LED L02 が白の点滅に変わり、デバイスの電源が入っており、Audio SoM で認証が行われていることを示します。

1. 認証プロセスが完了するまで待機します。これには最大 3 分かかることがあります。

1. 次のいずれかが表示されたら、プロトタイプ作成を開始できます。

    - LED L02 が白の点灯に変わります。 認証は完了しましたが、キーワードを使用した開発キットの構成はまだ済んでいません。
    - 3 つの LED がすべて青色に変わった。 認証が完了し、キーワードを使用した開発キットの構成も済んでいます。

## <a name="next-steps"></a>次のステップ

[Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) で[コードなしのスピーチ ソリューション](./tutorial-no-code-speech.md)を作成する。
