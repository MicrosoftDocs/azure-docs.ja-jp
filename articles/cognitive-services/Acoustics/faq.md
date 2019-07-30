---
title: Project Acoustics についてよく寄せられる質問
titlesuffix: Azure Cognitive Services
description: このページでは、ダウンロード手順やベイク プロセスなど、Project Acoustics についてよく寄せられる質問に回答します。
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 6e979db29f4a223b61580c48101c0d242fdbebbf
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616328"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Project Acoustics についてよく寄せられる質問

## <a name="what-is-project-acoustics"></a>Project Acoustics とは

Project Acoustics のプラグイン スイートは、実行時よりも前に音波の挙動を計算する音響システムであり、静的ライティングに似ています。 複雑な波動物理学計算がクラウドで実行されるため、実行時の CPU コストを低減します。  

## <a name="where-can-i-download-the-plugin"></a>どこでプラグインをダウンロードできますか?

[Project Acoustics Unity プラグイン](https://www.microsoft.com/download/details.aspx?id=57346)または [Project Acoustics Unreal プラグイン](https://www.microsoft.com/download/details.aspx?id=58090)をダウンロードできます。

## <a name="does-project-acoustics-support-ltxgt-platform"></a>Project Acoustics は &lt;x&gt; プラットフォームをサポートしますか?

Project Acoustics プラットフォームのサポートは、お客様のニーズに基づいて進化します。 追加のプラットフォームのサポートについては、[Project Acoustics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics)にお問い合わせください。

## <a name="is-azure-used-at-runtime"></a>Azure は実行時に使用されますか?

いいえ、クラウド統合はシーン設定の一部としての事前計算段階の間にのみ使用されます。
 
## <a name="what-is-simulation-input"></a>シミュレーション入力とは何ですか? 

シミュレーション入力は 3D シーン、仮想環境、またはゲーム レベルです。 Project Acoustics は、滑らかな閉鎖や散乱など、音の物理的特性を厳密にモデル化した 3D 容積測定波動シミュレーションを実行します。
 
## <a name="what-is-the-runtime-cost"></a>実行時コストはどのくらいですか?

Acoustics は 1 ソース、1 フレームあたり CPU の約 0.01% を占有します。 RAM の使用量はシーンのサイズに依存し、10 ～ 100 MB の範囲です。
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>レベル ジオメトリを簡略化する必要はありますか? 三角形の数を制御しますか? メッシュを水密にしますか?

いいえ。 システムは詳細レベルのジオメトリを直接取り込みます。 内部処理のためにボクセル化されます。
 
## <a name="whats-in-the-runtime-lookup-table"></a>実行時ルックアップ テーブルの内容は何ですか?

ACE ファイルには、無数のソース/リスナー位置ペア間の音響パラメーターのテーブルと、パラメーターの補間に使用されるボクセル化されたシーンのジオメトリが含まれます。
 
## <a name="can-project-acoustics-handle-moving-sources"></a>Project Acoustics は移動するソースを扱えますか?

はい。Project Acoustics はルックアップ テーブルを調べ、ティックごとにオーディオ DSP を更新するので、移動するソースとリスナーを処理できます。
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Project Acoustics は動的なジオメトリを扱えますか? 閉じるドアは? 吹き飛ばされる壁は?

いいえ。 音響パラメーターはゲーム レベルの静的状態に基づいて事前計算されます。 ドアのジオメトリは音響から除外し、確立された技術を使用して、破壊と移動が可能なゲーム オブジェクトの状態に基づいて閉鎖を適用することをお勧めします。
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>Project Acoustics は音響マテリアルを使用しますか?

はい。 レベル内の物理マテリアル名からマテリアルが選ばれ、吸音性を決定します。
 
## <a name="what-do-the-probes-represent"></a>"プローブ" とは何のことですか?

プローブは、可能性のあるプレイヤー位置のサンプリングです。 各プローブは、プローブ位置を起点とした、シーンの個別の波動シミュレーションを表します。 実行時に、リスナー位置の音響パラメーターが近くのプローブ位置から補間されます。
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>なぜクラウドでそんなに多くの計算を消費するのですか? どのようなメリットがあるのですか?

Project Acoustics は、アーキテクチャのすべての側面を考慮に入れ、きわめて複雑な仮想環境の場合でも正確で信頼できる音響パラメーターを提供します。 手動でボリュームを描画することなく、滑らかな閉鎖/障害、動的リバーブ変動を提供します。 実行中ずっと CPU のライトが点いたままです。

## <a name="what-exactly-happens-during-baking"></a>"ベイク" の間、正確には何が起きているのですか?

ベイクは、各リスナー プローブを中心とした立方体シミュレーション領域の音波シミュレーションで構成されます。

## <a name="next-steps"></a>次の手順
* [Project Acoustics Unity サンプル コンテンツ](unity-quickstart.md)または [Unreal サンプル コンテンツ](unreal-quickstart.md)を試してください

