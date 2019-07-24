---
title: Project Acoustics の概要
titlesuffix: Azure Cognitive Services
description: Project Acoustics は、3D 対話型エクスペリエンス用の音響エンジンです。ベイクされた波動物理学シミュレーションを対話型設計コントロールに統合します。
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 1fc125322b83a0eb51095fac21dee05c7ffb39c1
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313619"
---
# <a name="what-is-project-acoustics"></a>Project Acoustics とは
Project Acoustics は、3D 対話型エクスペリエンス用の波動音響エンジンです。 複雑なシーンの回折などの波動効果、ポータリングおよびリバーブ効果をモデル化します。手動のゾーン マークアップは必要ありません。 ゲーム エンジンとオーディオ ミドルウェアの統合も含まれます。 Project Acoustics の理念は、静的ライティングに似ています。詳細な物理学をオフラインでベイクし、物理的なベースラインを提供し、表現豊かな設計コントロールで軽量ランタイムを使用して、芸術的目標を達成します。

![Acoustics のボクセルを示している "Gears of War 4" のスクリーンショット](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>対話型音響での波動物理学の使用
レイベースの音響手法では、単一のソースからリスナーへのレイ キャストを使用して、オクルージョンを確認したり、いくつかのレイでローカル シーンのボリュームを予測することでリバーブを発生させたりすることができます。 しかし、これらの手法は信頼できない場合があります。小さな石で、大きな石と同じ程度に遮られるためです。 レイでは、音がオブジェクトの周りで屈折する方法 (回析として知られる現象) が考慮されません。 Project Acoustics のシミュレーションでは波動ベースのシミュレーションを使用して、これらの効果がキャプチャされます。 結果はより予測可能な信頼性の高いものとなります。

Project Acoustics の重要な革新は、音響シミュレーションを従来のサウンド設計の概念と統合することです。 シミュレーション結果は、オクルージョン、ポータリング、リバーブの従来のオーディオ DSP パラメーターに変換されます。 デザイナーは、この変換プロセスの制御を使用します。 Project Acoustics の背後にあるコア テクノロジの詳細については、[研究プロジェクトのページ](https://www.microsoft.com/en-us/research/project/project-triton/)を参照してください。

![シーンを通る波動伝搬の水平方向の 2D スライスを示すアニメーション](media/wave-simulation.gif)

## <a name="setup"></a>セットアップ
[Project Acoustics Unity の統合](unity-integration.md)はドラッグ アンド ドロップで行われ、Unity オーディオ エンジン プラグインが含まれます。 Project Acoustics C# コントロール コンポーネントを各オーディオ オブジェクトにアタッチして、Unity オーディオ ソース コントロールを補強します。

[Project Acoustics Unreal 統合](unreal-integration.md)には、Unreal 用のエディターおよびゲーム プラグインと、Wwise ミキサー プラグインが含まれます。 カスタム オーディオ コンポーネントでは、ライブ音響設計コントロールで Unreal 内に使い慣れた Wwise 機能が拡張されます。 設計コントロールは、Wwise のミキサー プラグインでも公開されます。

## <a name="workflow"></a>ワークフロー
* **事前ベイク:** まず、ベイクを設定します。その場合、光軸を無視するなどして、音響に対応するジオメトリを選択します。 その後、自動マテリアル割り当てを編集し、ナビゲーション領域を選択して、リスナー サンプリングをガイドします。 リバーブ/ポータル/ルーム ゾーンの手動のマークアップはありません。
* **ベイク:** 分析の手順はローカルで行われます。その際に、前述の選択内容に基づいて、シーンでボクセル化とその他の幾何解析を行います。 シーンのセットアップを確認するために、エディターで結果が視覚化されます。 ベイク送信時に、ボクセル データが Azure に送信され、音響ゲーム資産が返されます。
* **ランタイム:** 資産をご自分のレベルに読み込むと、そのレベルで音響を聴けるようになります。 エディターで詳細なソースごとのコントロールを使用して、音響ライブを設計します。 コントロールはレベル スクリプトから実行することもできます。

## <a name="platforms"></a>プラットフォーム
Project Acoustics ランタイム プラグインは現在、以下のプラットフォームにデプロイできます。
*  Windows
* Android
* Xbox One

## <a name="download"></a>ダウンロード
* [Project Acoustics Unity プラグインとサンプル](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
* [Project Acoustics Unreal と Wwise のプラグインとサンプル](https://www.microsoft.com/download/details.aspx?id=58090)
  * Xbox のバイナリとサポートについては、以下のサインアップ フォームを使用してお問い合わせください

## <a name="contact-us"></a>お問い合わせ
* [Project Acoustics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics)
* [サインアップして Project Acoustics の更新プログラムを受け取る](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u)

## <a name="next-steps"></a>次の手順
* [Project Acoustics のクイック スタート (Unity 用](unity-quickstart.md) または [Unreal 用)](unreal-quickstart.md) を試す
* [Project Acoustics のサウンド設計理念](design-process.md)を確認する

