---
title: Azure Stack Edge Mini R の安全性ガイド | Microsoft Docs
description: Azure Data Box Heavy の安全性についての規定事項、ガイドライン、考慮事項について説明し、さらに Azure Stack Edge Mini R デバイスを安全にインストールして操作する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: alkohli
ms.openlocfilehash: eb42a9a77927d8577dfec3c9167294eb8f809fec
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382628"
---
# <a name="azure-stack-edge-mini-r-safety-instructions"></a>Azure Stack Edge Mini R の安全性のための指示

![警告アイコン 1](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
![安全上の注意の確認を促すアイコン](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png)
**安全衛生に関する情報をお読みください**

バッテリー パック (1 個)、AC/DC コンセント付き電源 (1 個)、モジュール電源アダプタ (1 個)、およびサーバー モジュール (1 個) で構成される Azure Stack Edge Mini R デバイスを使用する前に、この記事に記載されているすべての安全性に関する情報をお読みください。 指示に従わないと、火災、感電、負傷、または資産の破損が発生する可能性があります。 Azure Stack Edge Mini R を使用する前に、以下のすべての安全性に関する情報をお読みください。

## <a name="safety-icon-conventions"></a>安全性アイコンの表記規則

危険警告表示に続くシグナル ワードには、以下のようなものがあります。

| アイコン | 説明 |
|:--- |:--- |
| ![危険シンボル](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)| **危険:** 回避しなければ、死亡事故または重大な負傷を招く危険な状況を示します。 <br> **警告:** 回避しなければ、死亡事故または重大な負傷を招く可能性のある危険な状況を示します。 <br> **注意:** 回避しなければ、軽度または中程度の負傷を招く可能性のある危険な状況を示します。|
|

Azure Stack Edge Mini R デバイスを設定して実行する際に表示される注意喚起のアイコンには、以下のようなものがあります。

| アイコン | 説明 |
|:--- |:--- |
| ![最初にすべての手順をお読みください](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png) | 最初にすべての手順をお読みください |
| ![注意アイコン](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **注意:** | 重要だが、危険に関連するものではない情報を示します。 |
| ![危険シンボル](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) | 危険シンボル |
| ![感電を示すアイコン](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) | 感電の危険 |
| ![室内使用のみ](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) | 室内使用のみ |
| ![ユーザーが使用できる部品がないことを示すアイコン](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) | ユーザーによる保守不可能な部品。 適切な訓練を受けている場合を除き、触らないでください。 |
|

## <a name="handling-precautions-and-site-selection"></a>使用上の注意事項およびサイト選択

Azure Stack Edge Mini R デバイスには、次の使用上の注意事項およびサイト選択条件があります。

![警告アイコン 2](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
![感電のアイコン](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
![ユーザーによる保守不可能な部品アイコン](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **注意:**

* "*受け取った状態*" のデバイスで損傷を検査してください。 デバイスの筐体が損傷している場合は、[Microsoft サポートに連絡](azure-stack-edge-placeholder.md)して代わりの製品を受け取ってください。 デバイスを操作しようとしないでください。
* デバイスが正常に動作していないと思われる場合は、[Microsoft サポートに連絡](azure-stack-edge-placeholder.md)して代わりの製品を受け取ってください。 デバイスを修理しようとしないでください。
* デバイスには、ユーザーでは修理できない部品が含まれています。 危険な電圧、電流、エネルギー レベルが内部に存在します。 開かないでください。 修理のために Microsoft にデバイスを返却してください。

![警告アイコン 3](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **注意:**

システムは、以下の条件下で運用することをお勧めします。

* 直射日光や暖房などの熱源から離す。
* 湿気や雨にさらされていない場所。
* 振動および物理的な衝撃が最小限に抑えられた場所に配置。  このシステムは、MIL-STD-810G に準拠した衝撃および振動を想定して設計されています。
* 電気機器によって生成される強力な電磁場の影響がない。
* 液体や異物がシステム内に入らないようにしてください。 システムの上や近くに飲料やその他の液体容器を置かないでください。

![警告アイコン 4](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
![ユーザーによる保守不可能な部品アイコン](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **注意:**

* この機器には、リチウム電池が搭載されています。 バッテリ パックの保守は行わないでください。 この機器のバッテリをユーザーは保守できません。 バッテリを不適切なタイプに交換すると爆発する危険があります。

![警告アイコン 5](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **注意:**

バッテリ パックは、別々のデバイスとしては充電せず、Azure Stack Edge Mini R デバイスに組み込まれている時にのみ充電してください。

![警告アイコン 6](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **注意:**

* バッテリ パックのオン/オフ スイッチは、バッテリをサーバー モジュールのみに放電するためのものです。 電源アダプターがバッテリ パックに接続されている場合、スイッチがオフの位置にある場合でも、サーバー モジュールには電力が流れます。

![警告アイコン 7](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **注意:**

* バッテリ パックを焼いたり、ショートさせたりしないでください。 リサイクルするか、適切に破棄する必要があります。

![警告アイコン 8](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **注意:**

* 付属の AC/DC 電源の代わりに、このシステムには、フィールドで提供される 2590 型のバッテリを使用するオプションもあります。 この場合、エンド ユーザーは、適用されるすべての安全、輸送、環境、およびその他の国または地域の規制が満たされていることを確認しなければなりません。
* 2590 型のバッテリを使用してシステムを運用する場合は、バッテリ メーカーが指定する使用条件の範囲内で運用してください。

![警告アイコン 9](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **注意:**

このデバイスには、光トランシーバーに使用できる 2 つの SFP+ ポートがあります。 危険なレーザー放射を避けるため、クラス 1 のトランシーバーのみを使用してください。

## <a name="electrical-precautions"></a>電気に関する注意事項

Azure Stack Edge Mini R デバイスには、次のような電気に関する注意事項があります。

![警告アイコン 10](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) ![感電のアイコン](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **警告:**

電源アダプターと一緒に使用する場合:

* 電源コードには安全な接地を行ってください。 交互電流 (AC) 電源ケーブルには、3 線接地プラグ (接地ピンを持つプラグ) が付いています。 このプラグは、接地されている AC コンセントにのみ適合します。 接地ピンを無意味にしないでください。
* 電源コードのプラグが主な切断装置である場合、壁コンセントがデバイスの近くにあり、簡単に使用できることを確認してください。
* 次の状態が存在する場合は、電源コードを抜き (コードではなくプラグを持つこと)、すべてのケーブルを外します。

  * 電源コードまたはプラグにほつれや、他の損傷がある。
  * デバイスが雨、過度の湿気、またはその他の液体に曝された。
  * デバイスを落とし、デバイスの筐体が破損している。
  * デバイスのサービスまたは修理が必要であると思われる。
* ユニットを移動する前、または何らかの理由で破損すると思われる場合は、ユニットからプラグを完全に抜いてください。

* 以下の電力仕様を満たす、過負荷保護が装備された適切な電源を用意してください。

* 電圧: 100 ～ 240 ボルト AC
* 電流: 1.7 アンペア
* 頻度: 50 ～ 60 Hz

![警告アイコン 11](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
![感電のアイコン](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **警告:**

* AC 電源コードを改造したり、機器に付属しているもの以外の AC 電源コードを使用したりしないでください。

![警告アイコン 12](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
![感電のアイコン](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
![室内使用のみ](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) **警告:**

* この記号が付いている電源は、室内使用のみの場合の定格です。

## <a name="regulatory-information"></a>規制情報

以下には、Azure Stack Edge Mini R デバイスの規制情報 (規制モデル番号: TMA01) が記載されています。

Azure Stack Edge Mini R デバイスは、NRTL にリストされている (UL、CSA、ETL など) および IEC/EN 60950-1 または IEC/EN 62368-1 準拠 (CE マーク) の情報テクノロジ機器で使用するように設計されています。

米国およびカナダ以外の国では、ネットワーク ケーブル (機器に付属されていません) の長さが 3 メートルを超える場合、本機器と一緒に設置しないでください。

本機器は、次の環境で動作するように設計されています。

| 環境 | 仕様 |
|:---  |:--- |
| システム温度の仕様 | <ul><li>ストレージの温度: –20&deg;C ～ 50&deg;C (–4&deg;F ～ 122&deg;F)</li><li>連続運転:0&deg;C – 40&deg;C (32&deg;F – 104&deg;F)</li></ul> |
| 相対湿度 (RH) の仕様 | <ul><li>ストレージ:5% から 95% の相対湿度</li><li>運転中:10% から 90% の相対湿度</li></ul>|
| 最大高度の仕様 | <ul><li>運転中:15,000 フィート (4,572 メートル)</li><li>非運用時:40,000 フィート (12,192 メートル)</li></ul>|

> ![通知のアイコン - 2](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **通知:** &nbsp;Microsoft から明示的な承認を受けずに機器を変更すると、機器を操作するユーザーの権限が無効になる可能性があります。

#### <a name="canada-and-usa"></a>カナダと米国の場合:

> ![通知のアイコン - 3](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **通知:** &nbsp; この装置は、FCC 規則のパート 15 に従ってテストされ、クラス A デジタル デバイスの制限に準拠することが確認されています。 これらの制限は、装置が商用環境で使用されるときに、有害な干渉に対して適切に保護されるように設計されています。 この装置は、無線周波数エネルギーを生成および使用し、また放射する場合があり、取扱説明書に従って設置および使用しないと、無線通信に有害な干渉を及ぼす可能性があります。 住宅地域でこの装置を動作させると、有害な干渉が発生する可能性があり、その場合はユーザーが自費で干渉を是正する必要があります。

この装置に付属している Netgear A6150 WiFi USB アダプターは、身体の近くで動作させることが想定されており、装着時の比吸収率 (SAR) への準拠がテストされています。 FCC で定められている SAR の許容値は、組織 1 g あたりで平均した場合、1.6 W/kg です。 身体に装着した状態での携帯や使用時には、RF 曝露要件に準拠するために、身体から 10 mm 離してください。

Netgear A6150 WiFi USB アダプターは、ANSI/IEEE C95.1-1999 に準拠しており、OET Bulletin 65 Supplement C にて規定されている測定方法と手順に基づいてテストされています。

Netgear A6150 の比吸収率 (SAR):組織 1 g あたり平均 1.18 W/kg

Netgear A6150 WiFi USB アダプターは、承認されたアンテナでのみ使用してください。 このデバイスとそのアンテナは、FCC マルチトランスミッター製品の手順に準拠している場合を除き、他のアンテナやトランスミッターと一緒に設置したり、動作させたりしないでください。 米国市場で利用可能な製品の場合は、チャンネル 1 から 11 のみ運用することができます。 他のチャンネルを選択することはできません。

5150 から 5250 MHz 帯での運用は、同一チャンネルのモバイル サテライト システムへの有害な干渉の可能性を減らすために、屋内での使用に限定されます。

![規制情報に関する警告 - 室内使用](./media/azure-stack-edge-mini-r-safety/regulatory-information-indoor-use-only.png)

高出力レーダーは、5250 から 5350 MHz 帯および 5650 から 5850 MHz 帯のプライマリ ユーザー (優先ユーザー) として割り当てられており、これらのレーダーが LE-LAN デバイスに干渉したり損傷を与える可能性があることをご承知おき下さい。

この装置は、無線周波数エネルギーを生成および使用し、また放射する場合があり、手順に従って設置および使用しないと、無線通信に有害な干渉を及ぼす可能性があります。 ただし、特定の設置により干渉が発生しないことを保証するものではありません。

本装置によってラジオまたはテレビの受信に有害な干渉が引き起こされた場合 (これは装置の電源のオンとオフを切り替えることで判断できます)、ユーザーには、以下のいずれかの方法で干渉を修正することを試みることをお勧めします。

- 受信アンテナの向きを変えるか、再配置します。
- 装置と受信機を遠ざけます。
- 機器を、受信機が接続されている回路とは異なる回路のコンセントに接続します。
- 販売店または経験豊富なラジオまたは TV 技術者に相談します。

干渉の問題の詳細については、[fcc.gov/cgb/consumerfacts/interference.html](https://www.fcc.gov/consumers/guides/interference-radio-tv-and-telephone-signals) にある FCC の Web サイトを参照してください。 また、1-888-CALL FCC から FCC に電話して、干渉や電話の干渉についてのファクトシートを要求することもできます。

無線周波数の安全性に関する追加情報については、FCC の Web サイト ([https://www.fcc.gov/general/radio-frequency-safety-0](https://www.fcc.gov/general/radio-frequency-safety-0)) および Industry Canada の Web サイト ([http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html](http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html)) を参照してください。

本製品は、準拠した周辺機器とシステム コンポーネント間のシールド ケーブルの使用に関する条件に基づいて、EMC への適合性が実証されています。 ラジオ、テレビ セット、およびその他の電子機器への干渉の可能性を低減するため、準拠した周辺機器、およびシステム コンポーネント間のシールド ケーブルを使用することが重要です。

このデバイスは、FCC 規則のパート 15 およびカナダ産業省ライセンス適用外 RSS 標準に準拠しています。 動作は次の 2 つの条件に従います:(1) このデバイスは有害な干渉の原因にならない。(2) このデバイスは、デバイスの望ましくない動作を引き起こす可能性のある障害を含む、受信した任意の干渉を受け入れる必要がある。

![規制情報に関する警告 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)

CAN ICES-3(A)/NMB-3(A)

Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, USA

米国:(800) 426-9400

カナダ:(800) 933-4750

Netgear A6150 WiFi USB アダプター FCC ID:PY318300429
 
Netgear A6150 WiFi USB アダプター IC ID:4054A-18300429

この装置に付属している Netgear A6150 WiFi USB アダプターは、IC RSS-102 の一般公衆/非管理環境暴露の SAR 制限に準拠しており、IEEE 1528 に規定されている測定方法および手順に準拠してテストされています。 身体に装着した状態では、10 mm 以上の距離を保ってください。

Netgear A6150 WiFi USB アダプターは、管理されていない環境のために規定されているカナダ ポータブル RF の暴露限度に準拠しており、マニュアルで説明されている所定の操作を行っても安全です。 本製品を身体からできるだけ遠ざける、または本装置の出力を下げる (そのような機能がある場合) ことによって、RF 暴露をさらに低減させることができます。

1 g あたりで平均した各製品の比吸収率 (SAR) の表は、上記の米国のセクションに記載されています。

![規制情報に関する警告 2](./media/azure-stack-edge-mini-r-safety/regulatory-information-2.png)

#### <a name="european-union"></a>欧州連合:

この装置の EU 適合宣言のコピーを要求します。 [CSI_Compliance@microsoft.com](mailto:CSI_Compliance@microsoft.com) に電子メールを送信します。

この装置に付属している Netgear A6150 WiFi USB アダプターは、指令 2014/53/EU に準拠しており、要求に応じて提供することも可能です。

![警告アイコン 13](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **警告:**

これは、クラス A 製品です。 国内環境では、この製品は無線干渉を起こす可能性があり、その場合はユーザーが適切な対策を講じることが必要な場合があります。

廃棄バッテリと電気電子機器の廃棄:

![警告アイコン 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

製品、そのバッテリ、またはそのパッケージに記載されているこのシンボルは、この製品およびそれに含まれるすべてのバッテリを、家庭ごみとして廃棄してはならないことを意味します。 代わりに、ユーザーは責任を持って、バッテリおよび電気電子機器のリサイクル用の適用な収集場所にこれを渡す必要があります。 この個別回収とリサイクルは、天然資源の節約に役立ち、不適切な廃棄によって生じる可能性のある、バッテリおよび電気電子機器内に存在する可能性がある有害物質による、人の健康および自然への潜在的な悪影響を防ぎます。 バッテリおよび電気電子機器を廃棄する場所について詳しくは、最寄りの都市/自治体の窓口、家庭内廃棄物処理サービス、またはこの製品を購入した店にお問い合わせください。 WEEE での追加情報については、erecycle@microsoft.com にお問い合わせください。

この製品には、コイン電池バッテリが含まれています。

この装置に付属している Netgear A6150 WiFi USB アダプターは、身体の近くで動作させることが想定されており、装着時の比吸収率 (SAR) への準拠がテストされています (以下の値を参照してください)。 身体に装着した状態での携帯や使用時には、RF 曝露要件に準拠するために、身体から 10 mm 離してください。

**Netgear A6150 の比吸収率 (SAR):** 組織 10 g あたり平均 0.54 W/kg

 
このデバイスは、EU のすべての加盟国で動作する可能性があります。 デバイスが使用される国と地域の規制を順守してください。 このデバイスは、次の国で 5150 から 5350 MHz の周波数の範囲内で動作している場合、屋内のみの使用に制限されます。  

![室内のみの使用が義務付けられている EU 諸国](./media/azure-stack-edge-mini-r-safety/mini-r-safety-eu-indoor-use-only.png)

次の表に、RED の第 10.8(a) 項および第 10.8(b) 項に従って、EU 内で販売される Netgear ワイヤレス製品の周波数帯域と最大高周波伝送電力を示します。

**WiFi**

| 周波数範囲 (MHz) | 使用チャンネル | 最大伝送電力 (dBm/mW) |
| --------------------- | ------------- | --------------------------- |
| 2400 から 2483.5 | 1 から 13    | ODFM:19.9 dBm (97.7 mW) <br> CCK:17.9 dBm (61.7 mW) |
| 5150 から 5320   | 36 から 48   | 22.9 dBm (195 mW) |
| 5250 から 5350   | 52 から 64   | 22.9 dBm (195 mW) TPC あり <br> 19.9 dBm (97.7 mW) TPC なし |
| 5470 から 5725   | 100 から 140 | 29.9 dBm (977 mW) TPC あり <br> 29.6 dBm (490 mW) TPC なし |

Microsoft Ireland Sandyford Ind Est Dublin D18 KX32 IRL

電話番号: +353 1 295 3826

FAX 番号: +353 1 706 4110

#### <a name="singapore"></a>シンガポール:

この装置に付属している Netgear A6150 WiFi USB アダプターは、IMDA 標準に準拠しています。


## <a name="next-steps"></a>次の手順

- [Azure Stack Edge Mini R の配置を準備する](azure-stack-edge-mini-r-deploy-prep.md)
