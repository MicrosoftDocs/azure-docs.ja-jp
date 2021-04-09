---
title: Azure Media Player 再生テクノロジ
description: ビデオまたはオーディオを再生するために使用される再生テクノロジについて説明します。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 24a38fde3d1b6bbe1efef9241458da846c186112
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97916339"
---
# <a name="playback-technology-tech"></a>再生テクノロジ ("テクノロジ") #

再生テクノロジとは、ビデオまたはオーディオを再生するために使用される特定のブラウザーまたはプラグイン テクノロジを指します。

- **azureHtml5JS**: ビデオ要素と共に MSE および EME 標準を利用し、DASH コンテンツをプラグインなしで再生します。Azure Media Services によって、AES-128 ビット エンベロープ暗号化コンテンツまたは DRM の一般的な暗号化コンテンツ (ブラウザーでサポートされている場合に PlayReady および Widevine を使用) がサポートされます。
- **flashSS**: Flash Player テクノロジを利用して Smooth コンテンツを再生します。Azure Media Services によって AES-128 ビット エンベロープ暗号化がサポートされており、Flash バージョン 11.4 以上が必要です。
- **html5FairPlayHLS**: ビデオ要素を使用して、HLS 経由で Safari 固有のインブラウザーベースの再生テクノロジを利用します。 このテクノロジでは、FairPlay で保護されたコンテンツを Azure Media Services から再生する必要があります。2016 年 10 月 19 日に techOrder に追加されました。
- **silverlightSS**: Silverlight テクノロジを利用して Smooth コンテンツを再生します。PlayReady で保護されたコンテンツが Azure Media Services によってサポートされます。
- **html5**: ビデオ要素を使用して、インブラウザーベースの再生テクノロジを利用します。  Apple iOS または Android デバイスの場合、このテクノロジで HLS ストリームの再生が可能です。基本的な AES-128 ビット エンベロープ暗号化または DRM コンテンツ (ブラウザーでサポートされている場合に FairPlay を使用) が一部サポートされます。

## <a name="tech-order"></a>テクノロジの順序 ##

幅広いデバイスで資産を再生できるように、次のテクノロジの順序が推奨されます。これは `techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"]` の場合に既定値で、`<video>` で直接設定できるほか、オプションでプログラムによって設定できます。

`<video data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]}`

or

```javascript
    amp("vid1", {
          techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]
    });
```

## <a name="compatibility-matrix"></a>互換性マトリックス ##

Azure Media Services のストリーミング コンテンツでテクノロジの順序が推奨されるものである場合、次の互換性再生マトリックスが想定されます

| Browser        | OS                                                       | 想定されるテクノロジ (クリア)  | 想定されるテクノロジ (AES)  | 想定されるテクノロジ (DRM)          |
|----------------|----------------------------------------------------------|------------------------|----------------------|------------------------------|
| EdgeIE 11      | Windows 10、Windows 8.1、Windows Phone 10<sup>1</sup>               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (PlayReady)     |
| IE 11          | Windows 7、Windows Vista<sup>1</sup>                     | flashSS                | flashSS              | silverlightSS (PlayReady)    |
| IE 11          | Windows Phone 8.1                                        | azureHtml5JS           | azureHtml5JS         | サポート対象外                |
| Edge           | Xbox One<sup>1</sup> (2015 年 11 月の更新)                   | azureHtml5JS           | azureHtml5JS         | サポート対象外                |
| Chrome 37 以降     | Windows 10、Windows 8.1、macOS X Yosemite<sup>1</sup>   | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 47 以降    | Windows 10、Windows 8.1、macOS X Yosemite 以降<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 42 ～ 46  | Windows 10、Windows 8.1、macOS X Yosemite 以降<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | silverlightSS (PlayReady)    |
| Firefox 35 ～ 41  | Windows 10、Windows 8.1                                  | flashSS                | flashSS              | silverlightSS (PlayReady)    |
| Safari         | iOS 6 以降                                                   | html5                  | html5 (トークンなし) 3    | サポート対象外                |
| Safari 8 以降      | OS X Yosemite 以降                                           | azureHtml5JS           | azureHtml5JS         | html5FairPlayHLS (FairPlay)  |
| Safari 6       | OS X Mountain Lion<sup>1</sup>                           | flashSS                | flashSS              | silverlightSS (PlayReady)    |
| Chrome 37 以降     | Android 4.4.4 以降<sup>2</sup>                               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Chrome 37 以降     | Android 4.02                                             | html5                  | html5 (トークンなし)<sup>3</sup>    | サポート対象外                |
| Firefox 42 以降    | Android 5.0 以降<sup>2</sup>                                 | azureHtml5JS           | azureHtml5JS         | サポート対象外                |
| IE 8、IE 9、IE 10  | Windows                                                  | サポート対象外          | サポート対象外        | サポート対象外                |

<sup>1</sup> 構成がサポートされていないか、テストされていません。完了の参照として表示されます。

<sup>2</sup> Android デバイスでの正常な再生には、デバイスの機能、グラフィックスのサポート、コーデックのレンダリング、OS のサポートなどの組み合わせが必要になります。 Android はオープンソース プラットフォームであり、スマートフォンの製造元は、Google から提供されている Vanilla Android OS を変更することができます。これにより、Android の領域で断片化が発生し、機能不足のために一部のデバイスがサポートされないことがあります。 また、一部の Android デバイスは、すべてのコーデックには対応していません。  

<sup>3</sup> トークンがサポートされていない場合は、プロキシを使用してこの機能を追加できます。 このソリューションの詳細については、こちらの[ブログ](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)をご覧ください。

> [!NOTE]
> 選択される想定テクノロジでプラグイン (Flash など) のインストールが必要で、ユーザーのマシンにそれがインストールされていない場合は、引き続き AMP によって、テクノロジ リストの次のテクノロジの機能がソースの種類および保護情報と併せて確認されます。 たとえば、OS X Yosemite の Safari 8 で保護されていないオンデマンド ストリームを表示しようとしたときに、Flash も Silverlight もインストールされていない場合、AMP ではネイティブの Html5 テクノロジを使って再生するよう選択されます。<br/><br/>新しいブラウザー テクノロジが日々登場しているため、このマトリックスは変更される可能性があります。

## <a name="next-steps"></a>次のステップ ##

- [Azure Media Player クイックスタート](azure-media-player-quickstart.md)