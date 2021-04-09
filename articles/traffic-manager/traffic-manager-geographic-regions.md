---
title: 地理的ルーティングで使用される国/リージョン階層 - Azure Traffic Manager
description: この記事では、Azure Traffic Manager の地理的ルーティングの種類で使用される国/リージョンの階層を一覧表示します。
services: traffic-manager
documentationcenter: ''
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: duau
ms.openlocfilehash: 9d4b4ba6830fd573a545eb6308fe15a4781873c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98184324"
---
# <a name="countryregion-hierarchy-used-by-azure-traffic-manager-for-geographic-traffic-routing-method"></a>地理的トラフィック ルーティング方法で Azure Traffic Manager によって使用される国/リージョン階層

この記事では、Azure Traffic Manager の **地理的** トラフィック ルーティング方法で使用される国および地域の一覧を示します。 この情報は、[Azure Traffic Manager の REST API](/rest/api/trafficmanager/) を呼び出すことによってプログラムからも取得することができます。 

- WORLD(世界)

    - GEO-EU(ヨーロッパ)

        - AD(アンドラ)

        - AL(アルバニア)

        - AT(オーストリア)

        - AX(オーランド諸島)

        - BA(ボスニア・ヘルツェゴビナ)

        - BE(ベルギー)

        - BG(ブルガリア)

        - BY(ベラルーシ)

        - CH(スイス)

        - CY(キプロス)

        - CZ(チェコ共和国)

        - DE(ドイツ)

        - DK(デンマーク)

        - EE(エストニア)

        - ES(スペイン)

        - FI(フィンランド)

        - FO(フェロー諸島)

        - FR(フランス)

        - GB(イギリス)

        - GG(ガーンジー)

        - GI(ジブラルタル)

        - GR(ギリシャ)

        - HR(クロアチア)

        - HU(ハンガリー)

        - IE(アイルランド)

        - IM(マン島)

        - IS(アイスランド)

        - IT(イタリア)

        - JE(ジャージー)

        - LI(リヒテンシュタイン)

        - LT(リトアニア)

        - LU(ルクセンブルク)

        - LV(ラトビア)

        - MC(モナコ)

        - MD(モルドバ)

        - ME(モンテネグロ)

        - MK(北マケドニア)

        - MT(マルタ)

        - NL(オランダ)

        - NO(ノルウェー)

        - PL(ポーランド)

        - PT(ポルトガル)

        - RO(ルーマニア)

        - RS(セルビア)

        - RU(ロシア)

        - SE(スウェーデン)

        - SI(スロベニア)

        - SJ(スバールバル諸島)

        - SK(スロバキア)

        - SM(サンマリノ)

        - UA(ウクライナ)
            - クリミア地域

        - VA(バチカン市国)

        - XJ(ヤンマイエン島)

        - XK(コソボ)

    - GEO-ME(中東)

        - AE(アラブ首長国連邦)

        - BH(バーレーン)

        - IL(イスラエル)

        - IQ(イラク)

        - IR(イラン)

        - JO(ヨルダン)

        - KW(クウェート)

        - LB(レバノン)

        - OM(オマーン)

        - PS(パレスチナ自治政府)

        - QA(カタール)

        - SY(シリア)

        - SA(サウジアラビア)

        - TR(トルコ)

        - YE(イエメン)

    - GEO-NA(北米/中央アメリカ/カリブ)

        - AG(アンティグア・バーブーダ)

        - AI(アンギラ)

        - AW(アルバ)

        - BB(バルバドス)

        - BL(サン・バルテルミー)

        - BM(バミューダ)

        - BQ(ボネール島)

        - BS(バハマ)

        - BZ(ベリーズ)

        - CA(カナダ)

            - CA-AB(アルバータ)

            - CA-BC(ブリティッシュコロンビア)

            - CA-MB(マニトバ)

            - CA-NB(ニューブランズウィック)

            - CA-NL(ニューファンドランド・ラブラドル)

            - CA-NS(ノバスコシア)

            - CA-NT(ノースウェスト テリトリーズ)

            - CA-NU(ヌナブット)

            - CA-ON(オンタリオ)

            - CA-PE(プリンス エドワード島)

            - CA-QC(ケベック)

            - CA-SK(サスカチェワン)

            - CA-YT(ユーコン準州)

        - CR(コスタリカ)

        - CU(キューバ)

        - CW(キュラソー島)

        - DM(ドミニカ)

        - DO(ドミニカ共和国)

        - GD(グレナダ)

        - GL(グリーンランド)

        - GP(グアドループ)

        - GT(グアテマラ)

        - HN(ホンジュラス)

        - HT(ハイチ)

        - JM(ジャマイカ)

        - KN(セントクリストファー・ネイビス)

        - KY(ケイマン諸島)

        - LC(セントルシア)

        - MF(サン・マルタン)

        - MQ(マルティニーク)

        - MS(モントセラト)

        - MX(メキシコ)

        - NI(ニカラグア)

        - PA(パナマ)

        - PM(サンピエール島・ミクロン島)

        - PR(プエルトリコ)

        - SV(エルサルバドル)

        - SX(シント・マールテン島)

        - TC(タークス・カイコス諸島)

        - TT(トリニダード・トバゴ)

        - UM(合衆国領有小離島)

        - US(米国)

            - US-AK(アラスカ)

            - US-AL(アラバマ)

            - US-AR(アーカンソー)

            - US-AZ(アリゾナ)

            - US-CA(カリフォルニア)

            - US-CO(コロラド)

            - US-CT(コネチカット)

            - US-DC(ワシントン D.C.)

            - US-DE(デラウェア)

            - US-FL(フロリダ)

            - US-GA(ジョージア)

            - US-HI(ハワイ)

            - US-IA(アイオワ)

            - US-ID(アイダホ)

            - US-IL(イリノイ)

            - US-IN(インディアナ)

            - US-KS(カンザス)

            - US-KY(ケンタッキー)

            - US-LA(ルイジアナ)

            - US-MA(マサチューセッツ)

            - US-MD(メリーランド)

            - US-ME(メイン)

            - US-MI(ミシガン)

            - US-MN(ミネソタ)

            - US-MO(ミズーリ)

            - US-MS(ミシシッピ)

            - US-MT(モンタナ)

            - US-NC(ノースカロライナ)

            - US-ND(ノースダコタ)

            - US-NE(ネブラスカ)

            - US-NH(ニューハンプシャー)

            - US-NJ(ニュージャージー)

            - US-NM(ニューメキシコ)

            - US-NV(ネバダ)

            - US-NY(ニューヨーク)

            - US-OH(オハイオ)

            - US-OK(オクラホマ)

            - US-OR(オレゴン)

            - US-PA(ペンシルベニア)

            - US-RI(ロードアイランド)

            - US-SC(サウスカロライナ)

            - US-SD(サウスダコタ)

            - US-TN(テネシー)

            - US-TX(テキサス)

            - US-UT(ユタ)

            - US-VA(バージニア)

            - US-VT(バーモント)

            - US-WA(ワシントン)

            - US-WI(ウィスコンシン)

            - US-WV(ウエストバージニア)

            - US-WY(ワイオミング)

        - VC(セントビンセント及びグレナディーン諸島)

        - VG(英領ヴァージン諸島)

        - VI(米領バージン諸島)

        - XE(シント・ユースタティウス島)

        - XS(サバ島)

    - GEO-AS(アジア)

        - AF(アフガニスタン)

        - AM(アルメニア)

        - AZ(アゼルバイジャン)

        - BD(バングラデシュ)

        - BN(ブルネイ)

        - BT(ブータン)

        - CC(ココス諸島)

        - CN(中国)

        - CX(クリスマス島)

        - GE(ジョージア)

        - HK(香港特別行政区)

        - ID(インドネシア)

        - IN(インド)

        - IO(英領インド洋地域)

        - JP(日本)

        - KG(キルギス)

        - KH(カンボジア)

        - KP(北朝鮮)

        - KR(韓国)

        - KZ(カザフスタン)

        - LA(ラオス)

        - LK(スリランカ)

        - MM(ミャンマー)

        - MN(モンゴル)

        - MO(マカオ)

        - MV(モルディブ)

        - MY(マレーシア)

        - NP(ネパール)

        - PH(フィリピン)

        - PK(パキスタン)

        - SG(シンガポール)

        - TH(タイ)

        - TJ(タジキスタン)

        - TL(ティモール・レステ)

        - TM(トルクメニスタン)

        - TW(台湾)

        - UZ(ウズベキスタン)

        - VN(ベトナム)

    - GEO-AF(アフリカ)

        - AO(アンゴラ)

        - BF(ブルキナファソ)

        - BI(ブルンジ)

        - BJ(ベナン)

        - BV(ブーベ島)

        - BW(ボツワナ)

        - CD(コンゴ民主共和国)

        - CF(中央アフリカ共和国)

        - CI(コートジボワール)

        - CM(カメルーン)

        - CV(カーボベルデ)

        - DJ(ジブチ)

        - DZ(アルジェリア)

        - EG(エジプト)

        - ER(エリトリア)

        - ET(エチオピア)

        - GA(ガボン)

        - GH(ガーナ)

        - GM(ガンビア)

        - GN(ギニア)

        - GQ(赤道ギニア)

        - GW(ギニアビサウ)

        - KE(ケニア)

        - KM(コモロ)

        - LR(リベリア)

        - LS(レソト)

        - LY(リビア)

        - MA(モロッコ)

        - MG(マダガスカル)

        - ML(マリ)

        - MR(モーリタニア)

        - MU(モーリシャス)

        - MW(マラウイ)

        - MZ(モザンビーク)

        - NA(ナミビア)

        - NE(ニジェール)

        - NG(ナイジェリア)

        - RE(レユニオン)

        - RW(ルワンダ)

        - SC(セーシェル)

        - SD(スーダン)

        - SH(セントヘレナ、アセンションおよびトリスタンダクーニャ)

        - SL(シエラレオネ)

        - SN(セネガル)

        - SO(ソマリア)

        - SS(南スーダン)

        - ST(サントメ・プリンシペ)

        - SZ(スワジランド)

        - TD(チャド)

        - TF(仏領極南諸島)

        - TG(トーゴ)

        - TN(チュニジア)

        - TZ(タンザニア)

        - UG(ウガンダ)

        - YT(マイヨット島)

        - ZA(南アフリカ)

        - ZM(ザンビア)

        - ZW(ジンバブエ)

    - GEO-AN(南極)

        - AQ(南極)

    - GEO-SA(南アメリカ)

        - AR(アルゼンチン)

        - BO(ボリビア)

        - BR(ブラジル)

        - CL(チリ)

        - CO(コロンビア)

        - EC(エクアドル)

        - FK(フォークランド諸島)

        - GF(仏領ギアナ)

        - GS(サウスジョージア・サウスサンドウィッチ諸島)

        - GY(ガイアナ)

        - PE(ペルー)

        - PY(パラグアイ)

        - SR(スリナム)

        - UY(ウルグアイ)

        - VE(ベネズエラ)

    - GEO-AP(オーストラリア/太平洋)

        - AS(米領サモア)

        - AU(オーストラリア)

            - AU-ACT(オーストラリア首都特別地域)

            - AU-NSW(ニューサウスウェールズ)

            - AU-NT(ノーザンテリトリー)

            - AU-QLD(クイーンズランド)

            - AU-SA(サウスオーストラリア)

            - AU-TAS(タスマニア)

            - AU-VIC(ビクトリア)

            - AU-WA(ウェスタンオーストラリア)

        - CK(クック諸島)

        - FJ(フィジー)

        - FM(ミクロネシア)

        - GU(グアム)

        - HM(ハード・マクドナルド諸島)

        - KI(キリバス)

        - MH(マーシャル諸島)

        - MP(北マリアナ諸島)

        - NC(ニューカレドニア)

        - NF(ノーフォーク島)

        - NR(ナウル)

        - NU(ニウエ)

        - NZ(ニュージーランド)

        - PF(仏領ポリネシア)

        - PG(パプアニューギニア)

        - PN(ピトケアン)

        - PW(パラオ)

        - SB(ソロモン諸島)

        - TK(トケラウ諸島)

        - TO(トンガ)

        - TV(ツバル)

        - VU(バヌアツ)

        - WF(ウォリス・フツナ)

        - WS(サモア)

## <a name="next-steps"></a>次のステップ

- [Azure Traffic Manager の地理的トラフィック ルーティング方法](traffic-manager-routing-methods.md#geographic)の詳細について学習します。