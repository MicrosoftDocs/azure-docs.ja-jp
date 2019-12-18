---
title: StorSimple 10 GbE のハードウェアのインターフェイス | Microsoft Docs
description: StorSimple デバイス上の 10 GbE ネットワーク インターフェイスでサポートされている小型フォーム ファクターのプラグ可能な (SFP) トランシーバー、ケーブル、およびスイッチについて説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: df8d40c7-f5ad-4f84-93eb-779fbd5f7243
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: 7fafe177ea0c6c618dc4ab0727ba14c83cbb0102
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965015"
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>StorSimple デバイス上の 10 GbE ネットワーク インターフェイスでサポートされているハードウェア

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>概要
この記事では、Microsoft Azure StorSimple デバイスで動作する追加ハードウェアに関する情報を提供します。

## <a name="list-of-devices-tested-by-microsoft"></a>Microsoft によってテスト済みのデバイスの一覧
Microsoft では、テストの結果、次の小型フォーム ファクターのプラグ可能な (SFP) トランシーバー、ケーブル、およびスイッチがデバイスで最適に動作することを確認しています (次の表は、新しいハードウェアがテストされるたびに更新されます)。

### <a name="sfp-transceivers"></a>SFP+ トランシーバー
| 保存する | モデル |
| --- | --- |
| Cisco |SFP-10G-SR |

### <a name="cables"></a>ケーブル
| 連続 いいえ。 | Make | モデル |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB-CU1M |
| 2. |Cisco |SFP-H10GB-CU2M |
| 3. |Cisco |SFP-H10GB-CU3M |
| 4. |Tripp-Lite |N820-05M (OM3) |

### <a name="switches"></a>スイッチ
| 連続 いいえ。 | Make | モデル |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-FA |

## <a name="list-of-devices-tested-in-the-field"></a>実地テスト済みのデバイスの一覧
このセクションでは、StorSimple ユーザーによって現場で正常にデプロイされたデバイスを一覧で紹介します。 これらは Microsoft でテストされていませんが、StorSimple デバイスで動作することが見込まれています。

| パラメーター | 値 |
| --- | --- |
| スイッチ メーカー |Juniper |
| スイッチ モデル |ex4550-32F |
| スイッチのオペレーティング システム バージョン |JunOS 12.3R9.4 |
| ブレード モデル |オンボード ポート (PIC 0) |
| トランシーバー メーカー |Juniper |
| トランシーバー モデル |部品番号 740-021308 <br></br> 部品番号 740-030658 |
| トランシーバーのファームウェア バージョン |Rev 01 Version 0.0 (報告済み) |
| ケーブル モデル |デュプレックス ジャンパー LC/LC 50/125µ、   OM3、LSZH |
| StorSimple モデル |8600 |
| StorSimple ソフトウェアのバージョン |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>OEM プロバイダー (Mellanox) によってテスト済みのデバイスの一覧
Mellanox では、テストの結果、次の小型フォーム ファクターのプラグ可能な (SFP) トランシーバー、ケーブル、およびスイッチが Mellanox ネットワーク インターフェイス (StorSimple デバイス上の 10 GbE ネットワーク インターフェイスなど) で最適に動作することを確認しています。

### <a name="cables-and-modules-supported-by-mellanox"></a>Mellanox でサポートされているケーブルとモジュール
次の表に、Mellanox でサポートされているケーブルとモジュールを示します。 これらは Microsoft でテストされていませんが、StorSimple デバイスで動作することが見込まれています。

| 連続 いいえ。 | 速度 | モデル | 説明 | Make |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |CAB-SFP-SFP-1M |パッシブ銅線ケーブル SFP+ 10 Gb/秒、1 m |Arista |
| 2. |10 GbE |CAB-SFP-SFP-2M |パッシブ銅線ケーブル SFP+ 10 Gb/秒、2 m |Arista |
| 3. |10 GbE |CAB-SFP-SFP-3M |パッシブ銅線ケーブル SFP+ 10 Gb/秒、3 m |Arista |
| 4. |10 GbE |CAB-SFP-SFP-5M |パッシブ銅線ケーブル SFP+ 10 Gb/秒、5 m |Arista |
| 5. |10 GbE |Cisco SFP-H10GBCU1M |Cisco SFP+ ケーブル |Cisco |
| 6. |10 GbE |Cisco SFP-H10GBCU3M |Cisco SFP+ ケーブル |Cisco |
| 7. |10 GbE |Cisco SFP-H10GBCU5M |Cisco SFP+ ケーブル |Cisco |
| 8. |10 GbE |J9281B HP X242 10G |SFP+ 対 SFP+、1 m、直接接続銅線ケーブル |HP |
| 9. |10 GbE |455883-B21 HP BLc |10Gb SR SFP+ Opt |HP |
| 10. |10 GbE |455886-B21 HP BLc |10Gb LR SFP+ Opt |HP |
| 11. |10 GbE |487649-B21 HP BLc |SFP+ 0.5 m、10GbE 銅線ケーブル |HP |
| 12. |10 GbE |487652-B21 HP BLc |SFP+ 1 m、10GbE 銅線ケーブル |HP |
| 13. |10 GbE |487655-B21 HP BLc |SFP+ 3 m、10GbE 銅線ケーブル |HP |
| 14. |10 GbE |487658-B21 HP BLc |SFP+ 7 m、10GbE 銅線ケーブル |HP |
| 15. |10 GbE |537963-B21 HP BLc |SFP+ 5 m、10GbE 銅線ケーブル |HP |
| 16. |10 GbE |AP784A HP |3 m C シリーズ パッシブ銅線 SFP+ ケーブル |HP |
| 17. |10 GbE |AP785A HP |5 m C シリーズ パッシブ銅線 SFP+ ケーブル |HP |
| 18. |10 GbE |AP818A HP |1 m B シリーズ アクティブ銅線 SFP+ ケーブル |HP |
| 19. |10 GbE |AP819A HP |3 m B シリーズ アクティブ銅線 SFP+ ケーブル |HP |
| 20. |10 GbE |J9150A HP |X132 10G SFP+ LC SR トランシーバー |HP |
| 21. |10 GbE |J9151A HP |X132 10G SFP+ LC LR トランシーバー |HP |
| 22. |10 GbE |J9283B HP |X242 10G SFP+ SFP+ 3 m DAC ケーブル |HP |
| 23. |10 GbE |J9285B HP |X242 10G SFP+ SFP+ 7 m DAC ケーブル |HP |
| 24. |10 GbE |JD095B HP |X240 10G SFP+ SFP+ 0.65 m DAC ケーブル |HP |
| 25. |10 GbE |JD096B HP |X240 10G SFP+ SFP+ 1.2 m DAC ケーブル |HP |
| 26. |10 GbE |JD097B HP |X240 10G SFP+ SFP+ 3 m DAD ケーブル |HP |
| 27. |10 GbE |MAM1Q00A-QSA Mellanox |QSFP 対 SFP+ アダプター |Mellanox Technologies |
| 28. |10 GbE |MC2309124-006 Mt |パッシブ銅線ケーブル 1x SFP+ 対 QSFP 10Gb/秒 24awg 7 m |Mellanox Technologies |
| 29. |10 GbE |MC2309124-007 Mt |パッシブ銅線ケーブル 1x SFP+ 対 QSFP 10Gb/秒 24awg 7 m |Mellanox Technologies |
| 30. |10 GbE |MC2309130-003 Mt |パッシブ銅線ケーブル 1x SFP+ 対 QSFP 10Gb/秒 30awg 3 m |Mellanox Technologies |
| 31. |10 GbE |MC2309130-00A Mt |パッシブ銅線ケーブル 1x SFP+ 対 QSFP 10Gb/秒 30awg 0.5 m |Mellanox Technologies |
| 32. |10 GbE |MC3309124-005 Mt |パッシブ銅線ケーブル 1x SFP+ 10Gb/秒 24awg 5 m |Mellanox Technologies |
| 33. |10 GbE |MC3309124-007 Mt |パッシブ銅線ケーブル 1x SFP+ 10Gb/秒 24awg 7 m |Mellanox Technologies |
| 34. |10 GbE |MC3309130-003 Mt |パッシブ銅線ケーブル 1x SFP+ 10Gb/秒 30awg 3 m |Mellanox Technologies |
| 35. |10 GbE |MC3309130-00A Mt |パッシブ銅線ケーブル 1x SFP+ 10Gb/秒 30awg 0.5 m |Mellanox Technologies |

### <a name="switches-supported-by-mellanox"></a>Mellanox でサポートされているスイッチ
次の表に、Mellanox でサポートされているスイッチの一覧を示します。 これらは Microsoft でテストされていませんが、StorSimple デバイスで動作することが見込まれています。

| 連続 いいえ。 | 速度 | モデル | 説明 | Make |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |516733-B21 |HP ProCurve 6120XG 10 GbE イーサネット ブレード スイッチ |HP |
| 2. |10 GbE |538113-B21 |HP 10 GbE パススルー モジュール (PTM) |HP |
| 3. |10 GbE |EN4093 |IBM PureFlex システム ファブリック EN4093 10 ギガビット スケーラブル スイッチ モジュール |IBM |
| 4. |1 GbE |3020 |Cisco Catalyst 3020 1 GbE スイッチ ブレード |Cisco |
| 5. |1 GbE |3020X |Cisco Catalyst 3020X 1 GbE スイッチ ブレード |Cisco |
| 6. |1 GbE |438030-B21 |HP 1 GbE スイッチ モジュール - GbE2c レイヤー 2/3 イーサネット ブレード スイッチ |HP |
| 7. |1 GbE |6120G |HP ProCurve 6120G/XG 1 GbE スイッチ ブレード |HP |

## <a name="next-steps"></a>次の手順
[StorSimple ハードウェア コンポーネントと状態の詳細](storsimple-monitor-hardware-status.md)

