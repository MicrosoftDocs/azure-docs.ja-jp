---
title: Azure Media Player のアクセシビリティ
description: Azure Media Player のアクセシビリティ設定について詳しく説明します。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 2231c2969bbfce1668002ad4f5f719e0b8e13de5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "81727609"
---
# <a name="accessibility"></a>アクセシビリティ #

Azure Media Player は、Windows Narrator や Apple OSX/iOS VoiceOver などのスクリーン リーダー機能と連携します。 UI ボタンでは代替タグを利用できます。ユーザーが UI に移動すると、スクリーン リーダーでこれらの代替タグを読み上げることができます。 追加の構成は、オペレーティング システム レベルで設定できます。

## <a name="captions-and-subtitles"></a>キャプションと字幕 ##

現在、Azure Media Player でキャプションがサポートされるのは、WebVTT 形式のオンデマンド イベントと CEA-708 使用するライブ イベントのみです。 TTML 形式は、現在サポートされていません。 字幕やキャプションにより、より広範な視聴者 (たとえば、聴覚障碍を持つ人や、別の言語で読みたい人など) に対応して、利用を拡大できます。 キャプションにより、ビデオの視聴率を高め、理解を深められるようにし、職場などの音を出せない環境でもビデオを再生できるようになります。  

## <a name="high-contrast-mode"></a>ハイ コントラスト モード ##

Azure Media Player の既定の UI は、ほとんどのデバイスまたはブラウザーのハイ コントラスト表示モードに準拠しています。 構成は、オペレーティング システム レベルで設定できます。

## <a name="mobility-options"></a>移動オプション ##

### <a name="tabbing-focus"></a>Tab キーを使用したフォーカス ###

一般的な HTML 標準で提供されている Tab キーを使用したフォーカスは、Azure Media Player で利用できます。 Tab キーを使用したフォーカスを有効にするには、`tabindex=0` (または、タブ オーダーが HTML でどのような影響を受けるかを理解している場合は別の値) を、`<video ... tabindex=0>...</video>` のように HTML の `<video>` に追加する必要があります。 一部のプラットフォームでは、コントロールが表示されている場合や、プラットフォームがこれらの機能をサポートしている場合にのみ、コントロールのフォーカスが表示されます。

Tab キーを使用したフォーカスを有効にすると、エンド ユーザーはマウスに頼らずに、ビデオ プレーヤーを効果的に移動したり制御したりできるようになります。 Tab キーを押して各コンテキスト メニューまたは制御可能な要素に移動し、Enter キーまたは Space キーで選択できます。 エンド ユーザーがショートカット メニューで Enter キーまたは Space キーを押すとメニューが展開し、Tab キーを押しながら移動してメニュー項目を選択できるようになります。 選択したい項目のコンテキストが表示されたら、Enter キーまたは Space キーをもう一度押して選択を完了します。

### <a name="hotkeys"></a>ホット キー ###

Azure Media Player は、キーボードのホット キーを使用した制御をサポートしています。 Web ブラウザーで基になるビデオ要素を制御する唯一の方法は、プレーヤーにフォーカスを移すことです。 プレーヤーにフォーカスがあると、ホット キーでプレーヤーの機能を制御できます。  次の表では、さまざまなホット キーとそれに関連する動作について説明します。

| ホット キー              | 動作                                                                |
|----------------------|-------------------------------------------------------------------------|
| F または f                  | プレーヤーの全画面表示モードを開始または終了します                                  |
| M または m                  | プレーヤーの音量をミュートまたはミュート解除します                                          |
| 上方向キーと下方向キー    | プレーヤーの音量を増減します                                    |
| 左方向キーと右方向キー | ビデオの進行度を増減します                                  |
| 0、1、2、3、4、5、6、7、8、9  | 押したキーに応じて、ビデオの進行度が 0% から 90% の間で変更されます |
| [アクション] をクリックする         | ビデオが再生または一時停止されます                                                   |

## <a name="next-steps"></a>次のステップ

<!---Some context for the following links goes here--->
- [Azure Media Player クイックスタート](azure-media-player-quickstart.md)