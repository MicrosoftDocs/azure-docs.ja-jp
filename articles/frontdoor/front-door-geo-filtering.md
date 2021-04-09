---
title: Azure Front Door のドメインに対する geo フィルタリング | Microsoft Docs
description: この記事では、Azure Front Door の geo フィルタリング ポリシーについて説明します
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2020
ms.author: duau
ms.reviewer: tyao
ms.openlocfilehash: 33ad17e3024011368c909a89e9164ca06d044bad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612084"
---
# <a name="geo-filtering-on-a-domain-for-azure-front-door"></a>Azure Front Door のドメインに対する geo フィルタリング

既定では、Azure Front Door は、要求が送信される場所に関係なくすべてのユーザーの要求に応答します。 一部のシナリオでは、国/地域ごとに Web アプリケーションへのアクセスを制限しなければならないことも考えられます。 Front Door の Web アプリケーション ファイアウォール (WAF) では、エンドポイントの特定のパスに対するカスタム アクセス ルールを使用してポリシーを定義することにより、指定した国/地域からのアクセスを許可したりブロックしたりすることができます。 

WAF ポリシーには一連のカスタム ルールが含まれています。 ルールは、一致条件、アクション、優先順位で構成されます。 一致条件には、一致の変数、演算子、一致の値を定義します。 geo フィルタリング ルールの場合、一致変数は REMOTE_ADDR、演算子は GeoMatch、値は対象となる 2 文字の国/地域コードです。 "ZZ" 国番号または "不明" 国は、データセット内の国にまだマップされていない IP アドレスを取り込みます。 擬陽性を回避するために、一致条件に ZZ を追加できます。 GeoMatch 条件と REQUEST_URI 文字列の一致条件を組み合わせて、パス ベースの geo フィルタリング ルールを作成することができます。 


Front Door の geo フィルタリング ポリシーは、[Azure PowerShell](front-door-tutorial-geo-filtering.md) を使用するか、または[クイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)を使用して構成できます。

## <a name="countryregion-code-reference"></a>国/地域コード リファレンス

|国/地域コード | 国/地域名 |
| ----- | ----- |
| AD | アンドラ |
| AE | アラブ首長国連邦|
| AF | アフガニスタン|
| AG | アンティグア・バーブーダ|
| AL | アルバニア|
| AM | アルメニア|
| AO | アンゴラ|
| AR | アルゼンチン|
| AS | アメリカ領サモア|
| AT | オーストリア|
| AU | オーストラリア|
| AZ | アゼルバイジャン|
| BA | ボスニア・ヘルツェゴビナ|
| BB | バルバドス|
| BD | バングラデシュ|
| BE | ベルギー|
| BF | ブルキナファソ|
| BG | ブルガリア|
| BH | バーレーン|
| BI | ブルンジ|
| BJ | ベナン|
| BL | サン バルテルミー島|
| BN | ブルネイ・ダルサラーム|
| BO | ボリビア|
| BR | ブラジル|
| BS | バハマ|
| BT | ブータン|
| BW | ボツワナ|
| BY | ベラルーシ|
| BZ | ベリーズ|
| CA | Canada|
| CD | コンゴ民主共和国|
| CF | 中央アフリカ共和国|
| CH | スイス|
| CI | コートジボワール|
| CL | チリ|
| CM | カメルーン|
| CN | 中国|
| CO | コロンビア|
| CR | コスタリカ|
| CU | キューバ|
| CV | カーボベルデ|
| CY | キプロス|
| CZ | チェコ共和国|
| DE | ドイツ|
| DK | デンマーク|
| DO | ドミニカ共和国|
| DZ | アルジェリア|
| EC | エクアドル|
| EE | エストニア|
| EG | エジプト|
| ES | スペイン|
| ET | エチオピア|
| FI | フィンランド|
| FJ | フィジー|
| FM | ミクロネシア連邦|
| FR | フランス|
| GB | イギリス|
| GE | ジョージア|
| GF | 仏領ギアナ|
| GH | ガーナ|
| GN | ギニア|
| GP | グアドループ|
| GR | ギリシャ|
| GT | グアテマラ|
| GY | ガイアナ|
| HK | 香港特別行政区|
| HN | ホンジュラス|
| HR | クロアチア|
| HT | ハイチ|
| HU | ハンガリー|
| id | インドネシア|
| IE | アイルランド|
| IL | イスラエル|
| IN | インド|
| IQ | イラク|
| IR | イラン・イスラム共和国|
| IS | アイスランド|
| IT | イタリア|
| JM | ジャマイカ|
| JO | ヨルダン|
| JP | 日本|
| KE | ケニア|
| KG | キルギス|
| KH | カンボジア|
| KI | キリバス|
| KN | セントクリストファー・ネイビス|
| KP | 朝鮮民主主義人民共和国|
| KR | 韓国|
| KW | クウェート|
| KY | ケイマン諸島|
| KZ | カザフスタン|
| LA | ラオス人民民主共和国|
| LB | レバノン|
| LI | リヒテンシュタイン|
| LK | スリランカ|
| LR | リベリア|
| LS | レソト|
| LT | リトアニア|
| LU | ルクセンブルク|
| LV | ラトビア|
| LY | リビア |
| MA | モロッコ|
| MD | モルドバ|
| MG | マダガスカル|
| MK | 北マケドニア|
| ML | マリ|
| mm | ミャンマー|
| MN | モンゴル|
| MO | 中華人民共和国マカオ特別行政区|
| MQ | マルティニーク|
| MR | モーリタニア|
| MT | マルタ|
| MV | モルディブ|
| MW | マラウイ|
| MX | メキシコ|
| MY | マレーシア|
| MZ | モザンビーク|
| NA | ナミビア|
| NE | ニジェール|
| NG | ナイジェリア|
| NI | ニカラグア|
| NL | オランダ|
| NO | ノルウェー|
| NP | ネパール|
| NR | ナウル|
| NZ | ニュージーランド|
| OM | オマーン|
| PA | パナマ|
| PE | ペルー|
| PH | フィリピン|
| PK | パキスタン|
| PL | ポーランド|
| PR | プエルトリコ|
| PT | ポルトガル|
| PW | パラオ|
| PY | パラグアイ|
| QA | カタール|
| RE | レユニオン|
| RO | ルーマニア|
| RS | セルビア|
| RU | ロシア|
| RW | ルワンダ|
| SA | サウジアラビア|
| SD | スーダン|
| SE | スウェーデン|
| SG | シンガポール|
| SI | スロベニア|
| SK | スロバキア|
| SN | セネガル|
| SO | ソマリア|
| SR | スリナム|
| SS | 南スーダン|
| SV | エルサルバドル|
| SY | シリア・アラブ共和国|
| SZ | スワジランド|
| TC | タークス・カイコス諸島|
| TG | トーゴ|
| TH | タイ|
| TN | チュニジア|
| TR | トルコ|
| TT | トリニダード・トバゴ|
| TW | 台湾|
| TZ | タンザニア|
| UA | ウクライナ|
| UG | ウガンダ|
| US | United States|
| UY | ウルグアイ|
| UZ | ウズベキスタン|
| VC | セントビンセント及びグレナディーン諸島|
| VE | ベネズエラ|
| VG | イギリス領ヴァージン諸島|
| VI | アメリカ領ヴァージン諸島|
| VN | ベトナム|
| ZA | 南アフリカ|
| ZM | ザンビア|
| ZW | ジンバブエ|

## <a name="next-steps"></a>次のステップ

- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
- [ジオフィルタリング WAF ポリシーを設定する](front-door-tutorial-geo-filtering.md)方法について学習します。
