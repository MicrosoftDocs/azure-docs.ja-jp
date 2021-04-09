---
title: 空間分析の概要
titleSuffix: Azure Cognitive Services
description: このドキュメントでは、Computer Vision 空間分析コンテナーの基本概念と機能について説明します。
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ad05dd59c925242baf5c2b0e36c1f51bc4fec5d4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575359"
---
# <a name="overview-of-computer-vision-spatial-analysis"></a>Computer Vision の空間分析の概要

Computer Vision 空間分析は、Azure Cognitive Services Computer Vision の新機能であり、組織はこの機能を使用して、特定の領域内でのユーザーの動きとプレゼンスを理解することで、物理的な空間の価値を最大限に高めることができます。 CCTV または監視カメラからビデオを取り込み、AI 操作を実行してビデオ ストリームから分析情報を抽出し、他のシステムで使用するイベントを生成することができます。 AI 操作では、カメラ ストリームからの入力を使用して、空間に入ってくるユーザーの人数を数えたり、マスクやソーシャル ディスタンスのガイドラインに対するコンプライアンスを測定したりする処理を行えます。

## <a name="the-basics-of-spatial-analysis"></a>空間分析の基本

現在、空間分析の中核となる操作はすべて、ビデオを取り込み、ビデオ内のユーザーを検出し、時間の経過に伴って動き回るユーザーを追跡し、ユーザーが関心領域を操作したときにイベントを生成するパイプライン上に構築されています。

## <a name="spatial-analysis-terms"></a>空間分析の用語

| 期間 | 定義 |
|------|------------|
| 人物検出 | このコンポーネントでは、"人物がこの画像のどこにいるか" という質問に答えます。 画像内の人間を見つけ、それぞれの人物の場所を示す境界ボックスが、人物追跡コンポーネントに渡されます。 |
| 人物追跡 | このコンポーネントでは、人物がカメラの前を動き回るときに時間の経過に伴って人物検出が接続されます。 これは、人が一般的にどのように動くかに関する通常の移動方法に関するテンポラル ロジックと、このように動いている人物の全体的な外観に関する基本情報を使用します。 複数のカメラにまたがる人物の追跡は行われません。 ある人物がカメラの視野に約 1 分を超えて存在し、その後カメラの視野に再び入ると、システムではこれが新しい人物として認識されます。 人物追跡では、複数のカメラ間で個人が一意に識別されることはありません。 顔認識や歩行追跡は使用されません。 |
| マスクの検出 | このコンポーネントでは、カメラの視野にある人物の顔の位置が検出され、マスクの存在が識別されます。 これを行うために、AI 操作ではビデオからイメージがスキャンされます。顔が検出されると、サービスによって顔の周りに境界ボックスが表示されます。 物体検出機能を使用して、境界ボックス内にマスクがあるかどうかが識別されます。 マスクの検出では、複数の顔を区別したり、顔の属性を予測または分類したり、顔認識を行ったりすることはありません。 |
| 関心領域 | これは、構成の一部として入力ビデオ内で定義されたゾーンまたは線です。 ある人物がビデオの領域を操作すると、システムによってイベントが生成されます。 たとえば、PersonCrossingLine 操作の場合、ビデオ内には線が定義されています。 人物がその線を越えると、イベントが生成されます。 |
| イベント | イベントは、空間分析の主要な出力です。 各操作からは、特定のイベントが定期的に (たとえば、 1 分あたり 1 回)、または特定のトリガーが発生したときに生成されます。 このイベントには、入力ビデオで起きたことに関する情報は含まれますが、画像やビデオは含まれません。 たとえば、PeopleCount 操作では、人々の数が変化するたびに (トリガー)、または 1 分ごとに (定期的に)、更新された数を含んだイベントを生成できます。 |

## <a name="responsible-use-of-spatial-analysis-technology"></a>責任を持って空間分析テクノロジを使用する

責任を持って空間分析テクノロジを使用する方法については、[透明性に関するメモ](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext)を参照してください。 Microsoft の透明性に関するメモは、AI テクノロジがどのように機能するか、システム所有者がシステムのパフォーマンスと動作に影響を与える可能性がある選択肢、およびテクノロジ、人、環境などのシステム全体について検討することの重要性を理解するためのものです。

## <a name="spatial-analysis-gating-for-public-preview"></a>空間分析のパブリック プレビューの制限

空間分析向けに設計されたシナリオで空間分析を使用してもらえるように、Microsoft では、お客様がアプリケーション プロセスを通じてこのテクノロジを利用できるようにしています。 空間分析にアクセスするには、最初にオンラインの受付フォームに入力する必要があります。 [アプリケーションはこちらから開始します](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)。

空間分析パブリック プレビューへのアクセスは、この制限プレビュー期間中に限られた数のお客様をサポートするための Microsoft の資格基準、審査プロセス、および可用性に基づき、Microsoft の独自の判断によって決定されます。 パブリック プレビューでは、Microsoft と重要な関係を持ち、推奨のユース ケースと、責任ある AI へのコミットメントに従った追加シナリオに関して Microsoft と協力することに関心があるお客様を求めています。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [空間分析コンテナーの使用開始](spatial-analysis-container.md)