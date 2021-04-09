---
title: Azure Stack Edge Pro R の安全性ガイド |Microsoft Docs
description: 安全性についての規定事項、ガイドライン、考慮事項について説明し、さらに Azure Stack Edge Pro R デバイスを安全にインストールして操作する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: alkohli
ms.openlocfilehash: dacc9ecc28ffa482b60d1e48735fe3620b5b7558
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100363068"
---
# <a name="azure-stack-edge-pro-r-safety-instructions"></a>Azure Stack Edge Pro R の安全性のための指示

![警告のアイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![安全上の注意の確認を促すアイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png)
**安全衛生に関する情報を確認してください**

Azure Stack Edge Pro R デバイスを使用する前に、この記事の安全性に関する情報をすべてお読みください。 指示に従わないと、火災、感電、負傷、または資産の破損が発生する可能性があります。 Azure Stack Edge Pro R を使用する前に、以下のすべての安全性に関する情報をお読みください。

## <a name="safety-icon-conventions"></a>安全性アイコンの表記規則

危険警告表示に続くシグナル ワードには、以下のようなものがあります。

| アイコン | 説明 |
|:--- |:--- |
| ![危険シンボル](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)| **危険:** 回避しなければ、死亡事故または重大な負傷を招く危険な状況を示します。 <br> **警告:** 回避しなければ、死亡事故または重大な負傷を招く可能性のある危険な状況を示します。 <br> **注意:** 回避しなければ、軽度または中程度の負傷を招く可能性のある危険な状況を示します。|
|

Azure Stack Edge Pro R Edge デバイスを設定して実行する際に表示される注意喚起のアイコンには、以下のようなものがあります。

| アイコン | 説明 |
|:--- |:--- |
| ![最初にすべての手順をお読みください](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png) | 最初にすべての手順をお読みください |
| ![注意アイコン](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **注意:** | 重要だが、危険に関連するものではない情報を示します。 || ![危険シンボル](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) | 危険シンボル |
| ![傾け注意を示すアイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png)  | 転倒注意|
| ![大きな重量を示すアイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png)  | 重量物の危険|
| ![感電を示すアイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) | 感電の危険 |
| ![ユーザーが使用できる部品がないことを示すアイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) | ユーザーによる保守不可能な部品。 適切な訓練を受けている場合を除き、触らないでください。 |
| ![複数の電源のアイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png)  | 複数の電源。 機器への電力供給をすべて止めるには、すべての電源コードを外します。 |
| ![ピンチ ポイントのアイコン](./media/azure-stack-edge-pro-r-safety/icon-pinching-points.png)  | ピンチ ポイントが存在します。 |
| ![高温の内容物または表面のアイコン](./media/azure-stack-edge-pro-r-safety/icon-hot-component-surface.png)  | 高温の内容物または表面を示します。 |
|

## <a name="handling-precautions-and-site-selection"></a>使用上の注意事項およびサイト選択

![警告アイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **警告:**

* 出荷時の状態のデバイスの移動や取り扱いには、適切な機器 (たとえば、ハンドリフト) および保護具 (PPE) を使用する必要があります。

![警告のアイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)![危険情報のアイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png) **警告:**

* 転倒や破損を回避するために、機器を平らで固く安定した場所に置いてください。
* デバイスを 2 台以上積み重ねないでください。
* 積み重ねる前にシステムが安定していることを確認します。
* 積み重ねられたデバイスを再配置したり、移動したりしないでください。
* 外部ケーブルから通電されているデバイスは、積み重ねないでください。
* 積み重ね作業中に電源コードが押しつぶされたり、破損したりしないようにしてください。
* デバイスをテーブルやワークスペースとして使用しないでください。

![警告アイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![感電のアイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
![ユーザーによる保守不可能な部品アイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **注意:**

* "*受け取った状態*" のデバイスで損傷を検査してください。 デバイスの筐体が損傷している場合は、[Microsoft サポートに連絡](azure-stack-edge-contact-microsoft-support.md)して代わりの製品を受け取ってください。 デバイスを操作しようとしないでください。
* デバイスが正常に動作していないと思われる場合は、[Microsoft サポートに連絡](azure-stack-edge-contact-microsoft-support.md)して代わりの製品を受け取ってください。 デバイスを修理しようとしないでください。
* デバイスには、ユーザーでは修理できない部品が含まれています。 危険な電圧、電流、エネルギー レベルが内部に存在します。 開かないでください。 修理のために Microsoft にデバイスを返却してください。

![警告アイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![重量物のアイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png) **警告:**

* UPS の電源モジュールを取り外すと、UPS 内の通電部品が露出します。 電源モジュールのコンパートメント内に異物を挿入しないでください。
* Azure Stack Edge Pro R Edge デバイスを 1 人で持ち上げようとしないでください。 エンクロージャの重量は 52 kg から 93 kg (115 lbs から 205 lbs) です。機器を移動する場合や持ち上げる場合は、機械的補助やその他の適切な補助を使用してください。 機器を移動する場合や持ち上げる場合は、特定の職業安全衛生の要件に準拠してください。
* 適切な機械的補助なしで機器を持ち上げようとしないでください。 この重量を持ち上げようとすると、大きなけがをする可能性があるので注意してください。

![警告アイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **警告:**

* このシステムは管理された環境で運用されるように設計されています。 次のような場所を選択してください。
  * 直射日光や暖房などの熱源から離れた風通しがよい。
  * 湿気や雨にさらされていない。
  * 振動および物理的な衝撃が最小限に抑えられた場所に配置。  このシステムは、MIL-STD-810G に準拠した衝撃および振動を想定して設計されています。
  * 電気機器によって生成される強力な電磁場の影響がない。
  * 適切にアースされたコンセントがある。
  * 電源コードにアクセスするための十分な空間がある。これは、電源コードを製品の主電源を切断するのに使用するためです。
* この製品には、イーサネット ケーブルは付属していません。 電磁干渉を減らすため、Cat 6 シールド付きツイストペア (STP) ケーブルを使用することをお勧めします。
* 機器の周囲の空気が十分に循環するような作業場に機器を設置してください。機器が動作している間は、フロント カバーとバック カバーが完全に取り外されていることを確認してください。
* この製品には、イーサネット ケーブルは付属していません。 電磁干渉を減らすため、Cat 6 シールド付き (STP) ケーブルを使用することが推奨されます。
* 導電性の汚染物質がない温度管理された場所に機器を設置し、機器の周囲の空気が適切に循環できるようにします。
* 機器を液体の近くや湿度の高い環境に置かないでください。
* 液体や異物がシステム内に入らないようにしてください。 システムの上や近くに飲料やその他の液体容器を置かないでください。

## <a name="heater-precautions"></a>ヒーターに関する注意事項

![警告アイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![高温の内容物または表面のアイコン 1](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **警告:** 

* システムの電源がオンになっている間は自動ヒーター運転によりヒーターのアセンブリ カバーの表面温度が上がるため、触れると危険な場合があります。 システムの電源がオンになっている間は、この表面に触れないでください。 システムの電源をオフにした後は、クール ダウン時間を 10 分間設けてください。

![警告アイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ピンチ ポイントのアイコン 1](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **警告:** 

* システムの電源がオンになっている場合、背面プレナム ドアの自動作動により、ピンチ ポイントの危険が生じる可能性があります。 システムの電源がオンになっているときは、この領域に触れないようにしてください。

## <a name="electrical-precautions"></a>電気に関する注意事項

![警告アイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![感電のアイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **警告:**

* 電源コードには安全な接地を行ってください。 交互電流 (AC) 電源ケーブルには、3 線接地プラグ (接地ピンを持つプラグ) が付いています。 このプラグは、接地されている AC コンセントにのみ適合します。 接地ピンを無意味にしないでください。
* 電源コードのプラグが主な切断装置である場合、壁コンセントがデバイスの近くにあり、簡単に使用できることを確認してください。
* 次の状態が存在する場合は、電源コードを抜き (コードではなくプラグを持つこと)、すべてのケーブルを外します。

  * 電源コードまたはプラグにほつれや、他の損傷がある。
  * デバイスのケースの内部に何かをこぼした。
  * デバイスが雨または過度の湿気に曝された。
  * デバイスを落としてデバイスのケースが壊れている。
  * デバイスのサービスまたは修理が必要であると思われる。
* ユニットを移動する前、または何らかの理由で破損すると思われる場合は、ユニットからプラグを完全に抜いてください。
* 大量の漏れ電流を防ぐため、1 つのトランジット ケースに複数の無停電電源装置 (UPS) がある場合は、各 UPS を独立したブランチ回線に接続することをお勧めします。 ただし、各配電ユニット (UPS) の安全用接地が PDU の 1 つのフィーダー接地線に依存しているときに、PDU または他のデバイスが使用されている場合は、各 UPS の外部にある接地端子を補助用の建物の接地線と一緒に使用する必要もあります。

  > [!NOTE]
  > 既に補助接地線がある PDU が使用されている場合は、UPS で追加の接地端子を使用する必要はありません。

* 以下の電力仕様を満たす、過負荷保護が装備された適切な電源を用意してください。

  * 電圧: 100 から 240 ボルト AC
  * 電流: 20 A、電源コードあたりの最大値。 電源コードは付属しています。
  * 頻度: 50 ～ 60 Hz

![警告のアイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![感電のアイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
![複数の電源のアイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png) **警告:**

* 無停電電源装置 (UPS) のないシステムの場合は、すべての AC 電源コードを抜いて、機器から AC 電源を完全に取り外します。
* UPS が搭載されているシステムの場合は、すべての AC 電源コードを抜き、UPS 電源スイッチを使用してシステムの電源を切ります。 UPS には、危険なレベルの AC および DC 電圧が含まれています。
* システムに UPS が含まれている場合、UPS にはシールドされた入力電源ケーブルが付属しています。 シールドされた入力電源ケーブルを使用する必要があります。コードを交換したり、変更したりしないでください。

![警告のアイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![感電のアイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **警告:**

* UPS が搭載されているシステムの場合、AC や DC 電圧には、常にバッテリまたはユーティリティのいずれかから発生した AC 電圧が UPS 出力にかかることによる潜在的なリスクが伴います。 機器の損傷や人身傷害を避けるために、一次電源から切り離されている場合でも、UPS の出力に電圧がかかっている可能性があることを常に想定してください。
* UPS が搭載されているシステムでは、UPS に通電した外部接続はすべてメスです。 ケースを取り外したり、UPS のこれらまたはコネクタに何かを挿入したりしないでください。

![警告のアイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![感電のアイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **警告:**

* AC 電源コードを改造したり、機器に付属しているもの以外の AC 電源コードを使用したりしないでください。

![警告アイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![ユーザーによる保守不可能な部品アイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **注意:**

* この機器には、ボタン型リチウム電池またはリン酸鉄リチウム イオン バッテリが含まれています。 機器の保守は行わないでください。 この機器のバッテリをユーザーは保守できません。 バッテリを不適切なタイプに交換すると爆発する危険があります。
* UPS のバッテリ モジュールを取り外すと、UPS 内の通電部品が露出します。 バッテリ モジュールのコンパートメント内に異物を挿入しないでください。

## <a name="regulatory-information"></a>規制情報

このセクションには、Azure Stack Edge Pro R デバイスに関する規制情報 (規制モデル番号: Azure Stack Edge Pro R ) が記載されています。

Azure Stack Edge Pro R Edge デバイスは、NRTL にリストされている (UL、CSA、ETL など) および IEC/EN 60950-1 または IEC/EN 62368-1 準拠 (CE マーク) の情報テクノロジ機器で使用するように設計されています。

本デバイスは、次の環境で動作するように設計されています。

| 環境 | 仕様 |
|:--- |:--- |
|温度の仕様 | <ul><li>ストレージの温度: –33&deg;C ～ 63&deg;C (–28&deg;F ～ 145&deg;F) </li><li>連続運転:5&deg;C ～ 43&deg;C (41&deg;F ～ 110&deg;F)</li><li>最大温度勾配 (操作時およびストレージ):20&deg;C/時 (68&deg;F/時)</li></ul> |
|相対湿度の仕様 | <ul><li>ストレージ:5% から 95% RH (最大露点 33&deg;C (91&deg;F) の場合)。 大気は常に結露しないよう保ってください。</li><li>運転中:5% から 85% の相対湿度 (最高露点 29&deg;C (84.2&deg;F) の場合)</li></ul> |
| 最大高度の仕様 | <ul><li>運用時 (UPS なし):15,000 ft (4,572 メートル)</li><li>運用時 (UPS あり):10,000 ft (3,048 メートル)</li><li>ストレージ:40,000 ft (12,192 メートル)</li></ul> |

<!--|Standard operating temperature specifications | <ul>Continuous operation (for altitude less than 950 m or 3117 ft): +5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</ui>
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li></= 1% of annual operating hours: –5&deg;C to 55&deg;C at 5% to 90% RH with 29&deg;C dew point. |
| Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15&deg;C according to IEC 60945.</li><li>The operating temperature specified is for a maximum altitude of 950 meters.</li></ul> |
| Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.</li></ul>|

> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to –5&deg;C or up to 55&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
>
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.     
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. 


A device that has a UPS installed is designed to operate in the following environments:

| Environment | Specifications |
|:---  |:--- |
|Temperature specifications | <ul><li>Storage temperature: –40&deg;C–70&deg;C (–40&deg;F-158&deg;F) per Mil-Std 810G Method 501.5, Proc 1</li><li>Continuous operation (for altitude less than 950 m or 3117 ft): 5&deg;C–45&deg;C (41&deg;F–113&deg;F), with no direct sunlight on the equipment</li><li>Excursion temperature: 50&deg;C per Mil-Std 810G</li><li>Maximum temperature gradient (operating and storage): 20&deg;C/h (68&deg;F/h)</li></ul> |
|Relative humidity specifications | <ul><li>Storage: 5% to 95% RH with 33&deg;C (91&deg;F) maximum dew point. Atmosphere must be non-condensing at all times.</li><li>Operating: 5% to 85% relative humidity with 29&deg;C (84.2&deg;F) maximum dew point</li></ul>|
|Maximum altitude specifications | <ul><li>Storage: Mil-Std 810G method 500.5, Proc. I, 40,000 ft for 1 hour after stabilization </li><li>Operating: Mil-Std 810G method 500.5, Proc. II, air carriage, 15,000 ft for 1 hour after stabilization</li></ul>|
|Standard operating temperature specifications |<ul><li>Continuous operation (for altitude less than 950 m or 3117 ft)</li><li>+5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</li></ul>|
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li>&lt;/= 1% of annual operating hours: 0&deg;C to 50&deg;C at 5% to 90% RH with 29&deg;C dew point.</li></ul>|
|Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15C Per IEC 60945</li><li>The operating temperature specified is for a maximum altitude of 950 meters</li></ul> |
|Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.|-->

<!--
> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to 0&deg;C or up to 50&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
> 
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. --> 


> ![通知のアイコン - 2](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **通知:** &nbsp;Microsoft から明示的な承認を受けずに機器を変更すると、機器を操作するユーザーの権限が無効になる可能性があります。

#### <a name="canada-and-usa"></a>カナダと米国の場合:

> ![通知のアイコン - 2](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **通知:** &nbsp;この装置は、FCC 規則のパート 15 に従ってテストされ、クラス A デジタル デバイスの制限に準拠することが確認されています。 これらの制限は、装置が商用環境で使用されるときに、有害な干渉に対して適切に保護されるように設計されています。 この装置は、無線周波数エネルギーを生成および使用し、また放射する場合があり、取扱説明書に従って設置および使用しないと、無線通信に有害な干渉を及ぼす可能性があります。 住宅地域でこの装置を動作させると、有害な干渉が発生する可能性があり、その場合はユーザーが自費で干渉を是正する必要があります。

このデバイスは、FCC 規則のパート 15 およびカナダ産業省ライセンス適用外 RSS 標準に準拠しています。 動作は次の 2 つの条件に従います:(1) このデバイスは有害な干渉の原因にならない。(2) このデバイスは、デバイスの望ましくない動作を引き起こす可能性のある障害を含む、受信した任意の干渉を受け入れる必要がある。

![規制情報に関する警告 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)


CAN ICES-3(A)/NMB-3(A) Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, USA United States:(800) 426-9400 カナダ:(800) 933-4750

#### <a name="european-union"></a>欧州連合:

EU 適合宣言のコピーを要求します。 [CSI_Compliance@microsoft.com](mailto:CSI_Compliance@microsoft.com) に電子メールを送信します。

![警告アイコン](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **警告!**

これは、クラス A 製品です。 国内環境では、この製品は無線干渉を起こす可能性があり、その場合はユーザーが適切な対策を講じることが必要な場合があります。

廃棄バッテリと電気電子機器の廃棄:

![警告アイコン 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

製品、そのバッテリ、またはそのパッケージに記載されているこのシンボルは、この製品およびそれに含まれるすべてのバッテリを、家庭ごみとして廃棄してはならないことを意味します。 代わりに、ユーザーは責任を持って、バッテリおよび電気電子機器のリサイクル用の適用な収集場所にこれを渡す必要があります。 この個別回収とリサイクルは、天然資源の節約に役立ち、不適切な廃棄によって生じる可能性のある、バッテリおよび電気電子機器内に存在する可能性がある有害物質による、人の健康および自然への潜在的な悪影響を防ぎます。 バッテリおよび電気電子機器を廃棄する場所について詳しくは、最寄りの都市/自治体の窓口、家庭内廃棄物処理サービス、またはこの製品を購入した店にお問い合わせください。 WEEE での追加情報については、erecycle@microsoft.com にお問い合わせください。

この製品には、コイン電池バッテリが含まれています。

Microsoft Ireland Sandyford Ind Est Dublin D18 KX32 IRL 電話番号: +353 1 295 3826 ファックス番号: +353 1 706 4110


## <a name="next-steps"></a>次の手順

- [Azure Stack Edge Pro R Edge のデプロイを準備する](azure-stack-edge-pro-r-deploy-prep.md)
