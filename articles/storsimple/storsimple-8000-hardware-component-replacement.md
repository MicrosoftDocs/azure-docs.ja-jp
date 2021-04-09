---
title: StorSimple 8000 シリーズのハードウェア コンポーネントの交換 | Microsoft Docs
description: StorSimple デバイスの PCM、バッテリ、コントローラー モジュール、EBOD コントローラー、ディスク ドライブ、およびシャーシを安全に交換する方法について説明します。
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 12ab5a9598cc0222f5a3e64985be2e2ea9e7e2fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014858"
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>StorSimple 8000 シリーズ デバイスのハードウェア コンポーネントの交換

## <a name="overview"></a>概要
ハードウェア コンポーネントの交換に関するチュートリアルでは、Microsoft Azure StorSimple 8000 シリーズ デバイスのハードウェア コンポーネントと、それらのコンポーネントを取り外して交換するために必要な手順について説明します。 この記事は安全性アイコンについて説明し、詳細なチュートリアルへの参照情報や、交換可能なコンポーネントの一覧を掲載しています。

> [!IMPORTANT]
> StorSimple のコンポーネントを取り外して交換する前に、[安全性アイコンの表記規則](#safety-icon-conventions)とその他の[安全に関する注意事項](storsimple-8000-safety.md)を必ず確認してください。

### <a name="safety-icon-conventions"></a>安全性アイコンの表記規則

次の表で、これらのチュートリアルで使用される安全性アイコンについて説明します。 デバイスのコンポーネントを取り外して交換する手順を進めるときに、これらの安全性アイコンに細心の注意を払ってください。

| アイコン | テキスト | 追加情報 |
|:--- |:--- |:--- |
| ![警告アイコン](./media/storsimple-hardware-component-replacement/Warning.png) |**危険!** |回避しなければ、死亡事故または重大な負傷を招く危険な状況を示します。 この記号の文字は、最も重大な状況の場合にのみ使用されます。 |
| ![警告アイコン](./media/storsimple-hardware-component-replacement/Warning.png) |**警告!** |回避しなければ、死亡事故または重大な負傷を招く可能性のある危険な状況を示します。 |
| ![注意アイコン](./media/storsimple-hardware-component-replacement/Caution.png) |**注意!** |回避しなければ、軽度または中程度の負傷を招く可能性のある危険な状況を示します。 |
| ![Notice icon](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**注意事項:** |重要だが、危険に関連するものではない情報を示します。 |
| ![Electrical shock icon](./media/storsimple-hardware-component-replacement/Electric.png) |**感電の危険** |電圧が高いことを示します。 |
| ![ヘビー ウェイト アイコン](./media/storsimple-hardware-component-replacement/Weight.png) |**ヘビー ウェイト** | |
| ![ユーザーによる保守不可能な部品アイコン](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**ユーザーによる保守不可能な部品** |適切な訓練を受けている場合を除き、触らないでください。 |
| ![指示確認アイコン](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**最初にすべての手順をお読みください** | |
| ![危険情報アイコン](./media/storsimple-hardware-component-replacement/TipHazard.png) |**転倒注意** | |

### <a name="before-you-begin"></a>開始する前に

デバイスと、このチュートリアルで使用される安全性アイコンについての情報をよく理解してください。 詳細については、「 [StorSimple デバイスを安全にインストールして操作する](storsimple-8000-safety.md) 」を参照してください。 StorSimple デバイスに触る前に、「 [安全性に関する注意事項](storsimple-8000-safety.md#handling-precautions) 」を必ず確認してください。

コンポーネントを交換する前に、次の情報を考慮してください。

![警告アイコン](./media/storsimple-hardware-component-replacement/Warning.png) ![感電のアイコン](./media/storsimple-hardware-component-replacement/Electric.png) **警告!**

* StorSimple デバイスのコンポーネントに触るときは、静電気除去マットまたは帯電防止マットを使用して、身体の静電気を逃がしてください。
* 回路に触れないでください。 回路が露出してる可能性があるコンポーネントに触るときは、用意されているハンドルとガイドを使用してください。

![Warning Icon](./media/storsimple-hardware-component-replacement/Warning.png) ![Notice Icon](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **注意事項:**

モジュールを交換するときは、 **エンクロージャの後部に空のベイを絶対に残さないでください**。 問題のある部分を取り外す前に、交換用モジュールまたは空のモジュールを用意してください。

## <a name="hardware-component-replacement-procedures"></a>ハードウェア コンポーネントの交換手順

StorSimple 8000 シリーズ デバイスは、主エンクロージャまたは EBOD エンクロージャに収納されるさまざまなプラグイン モジュールで構成されています。 8100 は 1 つの主エンクロージャを持つデバイスであり、8600 は 1 つの主エンクロージャと 1 つの EBOD エンクロージャを持つデュアル エンクロージャ デバイスです。

デバイスの主なハードウェア コンポーネントを次の表にまとめて示します。 **交換手順** の欄に示されているリンクをクリックすると、関連付けられているチュートリアルに移動します。

| Components | 台数 | プラグイン モジュール? | 交換手順 |
|:--- |:--- |:--- |:--- |
| シャーシ |1 |いいえ |[StorSimple デバイスのシャーシを交換する](storsimple-8000-chassis-replacement.md) |
| プライマリ コントローラー |2 |はい |[StorSimple デバイスのコントローラー モジュールを交換する](storsimple-8000-controller-replacement.md) |
| 764W 電源冷却モジュール (PCM) |2 |はい |[StorSimple デバイスの電源冷却モジュールを交換する](storsimple-8000-power-cooling-module-replacement.md) |
| バックアップ バッテリ |2 |はい |[StorSimple デバイスのバックアップ バッテリ モジュールを交換する](storsimple-8000-battery-replacement.md) |
| ディスク ドライブ |12 |はい |[StorSimple デバイスのディスク ドライブを交換する](storsimple-8000-disk-drive-replacement.md) |

**表 1** 主エンクロージャ内のハードウェア コンポーネント

主エンクロージャと EBOD エンクロージャは、I/O モジュールが異なります。 また、PCM のワット数が異なります。 主エンクロージャの PCM は 764 W です。これに対し、EBOD エンクロージャの PCM は 580 W です。主エンクロージャの PCM には、バックアップ バッテリ モジュールも含まれます。

| Components | 台数 | プラグイン モジュール? | 交換手順 |
|:--- |:--- |:--- |:--- |
| シャーシ |1 |いいえ |[StorSimple デバイスのシャーシを交換する](storsimple-8000-chassis-replacement.md) |
| EBOD コントローラー |2 |はい |[StorSimple デバイスの EBOD コントローラーを交換する](storsimple-8000-ebod-controller-replacement.md) |
| 580W 電源冷却モジュール (PCM) |2 |はい |[StorSimple デバイスの電源冷却モジュールを交換する](storsimple-8000-power-cooling-module-replacement.md) |
| ディスク ドライブ |12 |はい |[StorSimple デバイスのディスク ドライブを交換する](storsimple-8000-disk-drive-replacement.md) |

**表 2** EBOD エンクロージャ内のハードウェア コンポーネント

デバイスのプラグイン モジュールは、次の前面図と背面図で強調表示されています。 これらの図を使用して、さまざまなプラグイン モジュールの交換が必要になった場合に、その場所を判別できます。 前面図はディスク ドライブを示し、EBOD エンクロージャと主エンクロージャの背面図はプラグイン モジュールを示しています。

![デバイスのフロント パネル (ディスク ドライブ付き)](./media/storsimple-hardware-component-replacement/IC741028.png)

**図 1** デバイスの前面

| Label | 説明 |
|:--- |:--- |
| 0 ～ 11 |ディスク ドライブ (合計 12 台) |

主エンクロージャと EBOD エンクロージャの両方に、ドライブ キャリア モジュールがあります。 シャーシには、3 x 4 の配列で 12 台の 3.5" ディスク ドライブが収納されます。

![デバイスの主エンクロージャ モジュールのバックプレーン](./media/storsimple-hardware-component-replacement/IC740994.png)

**図 2** 主エンクロージャの背面

| Label | 説明 |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |コントローラー 0 |
| 4 |コントローラー 1 |

![デバイスの EBOD エンクロージャ プラグイン モジュールのパックプレーン](./media/storsimple-hardware-component-replacement/IC769599.png)

**図 3** EBOD エンクロージャの背面

| Label | 説明 |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |EBOD コントローラー 0 |
| 4 |EBOD コントローラー 1 |

## <a name="field-replaceable-units"></a>現場交換可能ユニット

StorSimple デバイスでは、次の現場交換可能ユニット (FRU) を利用できます。

* シャーシ (統合操作パネルを含む)
* 764 W AC PCM
* 580 W AC PCM
* ドライブ キャリア モジュール付きハード ディスク ドライブ
* コントローラー モジュール
* EBOD コントローラー モジュール
* バックアップ バッテリ モジュール
* ラック取り付け用レール キット

これらの交換装置のいずれかの注文については、[Microsoft サポートにお問い合わせください](storsimple-8000-contact-microsoft-support.md)。

## <a name="next-steps"></a>次のステップ

StorSimple のハードウェア コンポーネントを交換する前に、すべての [安全に関する情報](storsimple-8000-safety.md) を確認します。
