---
title: リリース ノート - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Custom Vision チームから新しいリリースに関する最新情報を入手します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: pafarley
ms.openlocfilehash: 3724a7d515197c1f969bb051fc201b82bee64c42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "97813498"
---
# <a name="custom-vision-service-release-notes"></a>Custom Vision Service リリース ノート

## <a name="may-2-2019-and-may-10-2019"></a>2019 年 5 月 2 日と 2019 年 5 月 10 日

- バグ修正とバックエンドの機能強化

## <a name="may-23-2019"></a>2019 年 5 月 23 日

- Azure サブスクリプションに関連するポータル UX のエクスペリエンスを向上し、Azure ディレクトリを簡単に選択できるようにしました。

## <a name="april-18-2019"></a>2019 年 4 月 18 日 

- Vision AI Dev Kit 用のオブジェクトの検出のエクスポートを追加しました。
- プロジェクト検索などの UI の調整。

## <a name="april-3-2019"></a>2019 年 4 月 3 日

- イメージごとの境界ボックス数の制限が 200 に増えました。 
- TensorFlow にエクスポートされたモデルの大幅なパフォーマンスの更新を含むバグ修正。 

## <a name="march-26-2019"></a>2019 年 3 月 26 日

- Custom Vision Service が Azure で一般提供されました。
- 特に高度な処理が必要になるデータセットや詳細に設定された分類でのパフォーマンスを高めるための新しい機械学習バックエンドを備えた、高度なトレーニング機能が追加されました。 高度なトレーニングにより、トレーニングのコンピューティング時間予算を指定でき、Custom Vision によって、経験に基づいて最適なトレーニングと拡張設定が特定されます。 クイック イテレーションについては、引き続き既存の高速トレーニングを使用することができます。
- 3\.0 API が導入されました。 2019 年 10 月 1 日の 3.0 より前の API の廃止予定を発表しました。 作業の開始方法の例については、ドキュメントの[クイックスタート](./quickstarts/image-classification.md)を参照してください。
- "既定のイテレーション" が 3.0 API の発行/発行取り消しで置き換えられました。
- 新しいモデルのエクスポート ターゲットが追加されました。 Raspberry Pi 3 の ARM をサポートするために、Dockerfile のエクスポートがアップグレードされました。 [Vision AI Dev Kit](https://visionaidevkit.com/) にエクスポートのサポートが追加されました。
- S0 レベルのプロジェクトあたりのタグの制限を 500 に増やしました。 S0 レベルのプロジェクトあたりのイメージの制限を 100,000 に増やしました。
- 成人ドメインが削除されました。 代わりに汎用ドメインが推奨されます。
- 一般提供の[価格](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)を発表しました。  

## <a name="february-25-2019"></a>2019 年 2 月 25 日

- Custom Vision の Azure パブリック プレビューへの移行の完了が近いため、期間限定試用版プロジェクト (Azure リソースに関連付けられていないプロジェクト) の終了を発表しました。 2019 年 3 月 25 日 以降、CustomVision.ai サイトでは、無料の Custom Vision リソースなどの Azure リソースに関連付けられているプロジェクトの表示のみがサポートされます。 2019 年 10 月 1 日まで引き続き、Custom Vision API を使用して、既存の期間限定試用版プロジェクトにアクセスできます。 これにより、Custom Vision で記述したアプリの API キーを更新する時間が得られます。 2019 年 10 月 1日 以降、Azure に移動していない期間限定試用版プロジェクトは削除されます。

## <a name="january-22-2019"></a>2019 年 1 月 22 日

- 新しい Azure リージョンのサポートが追加されました。米国西部 2、米国東部、米国東部 2、西ヨーロッパ、北ヨーロッパ、東南アジア、オーストラリア東部、インド中部、英国南部、東日本、および米国中北部です。 米国中南部のサポートは継続されます。

## <a name="december-12-2018"></a>2018 年 12 月 12 日

- オブジェクトの検出モデル用のエクスポートがサポートされるようになりました (オブジェクトの検出コンパクト ドメインを導入)。
- 多くのアクセシビリティの問題を修正して、スクリーン リーダーとキーボード ナビゲーションのサポートを改善しました。
- 画像ビューアーの UX が更新され、より高速なタグ付けのためのオブジェクト検出タグ付けエクスペリエンスが改善されました。  
- より高品質のオブジェクト検出のために、オブジェクトの検出ドメインの基本モデルが更新されました。
- バグが修正されました。

## <a name="november-6-2018"></a>2018 年 11 月 6 日

- オブジェクトの検出におけるロゴ ドメインのサポートを追加しました。

## <a name="october-9-2018"></a>2018 年 10 月 9 日

- Object Detection は有料のプレビューになります。 Azure のリソースを使用して､Object Detection プロジェクトを作成できるようになりました。
- Web サイトに｢Move to Azure｣機能を追加｡Limited Trial プロジェクトをアップグレードして､Azure に簡単にリンクできるようになっています｡ リソース リンク先のプロジェクト (F0 または S0)ご利用の製品の [設定] ページにあります｡  
- ONNX 1.2 へのエクスポートを追加｡Windows ML の Windows 2018 October Update バージョンに対応｡
特殊文字に関するバグ修正 (ONNX エクスポートのバグ修正を含む) が行われました。

## <a name="august-14-2018"></a>2018 年 8 月 14 日

- ユーザーにプロジェクトのトレーニングを案内する "作業の開始" ウィジェットが customvision.ai サイトに追加されました。
- マルチラベル プロジェクト (新しい損失層) で役立つように機械学習パイプラインがさらに機能強化されました。

## <a name="june-28-2018"></a>2018 年 6 月 28 日

- バグ修正とバックエンドの機能強化。
- 複数のイメージに完全に同じ 1 つのラベルがあるプロジェクトに対する多クラス分類のサポート。 多クラス モードの予測では、確率が 1 つに合計されます (すべてのイメージが指定されたタグの間で分類されます)。

## <a name="june-13-2018"></a>2018 年 6 月 13 日

- 使いやすさとアクセシビリティに重点を置いて、UX が更新されました。
- 多数のタグがあるマルチラベル プロジェクト で役立つように機械学習パイプラインが機能強化されました。
- TensorFlow のエクスポートのバグが修正されました。 エクスポートされたモデルのバージョン管理のサポート｡イテレーションを複数回エクスポートできるようになっています｡

## <a name="may-7-2018"></a>2018 年 5 月 7 日

- 制限付き試用版プロジェクトに関してオブジェクト検出機能 (プレビュー) が導入されました。
- API が 2.0 にアップグレードされました。
- S0 レベルが最大 250 タグおよび 50,000 画像に拡張されました。
- 画像分類プロジェクトの機械学習パイプラインに対し、重要なバックエンドの改良が行われました。 この更新の効果は、2018 年 4 月 27 日より後にトレーニングされたプロジェクトで得られます。
- Windows ML で使用できる ONNX へのモデルのエクスポートが追加されました。
- Dockerfile へのモデルのエクスポートが追加されました。 これにより、成果物 (DockerFile、TensorFlow モデル、サービス コードなど) をダウンロードして独自の Windows コンテナまたは Linux コンテナを構築することができます。
- 全プロジェクトの間で整合性を維持するため、新しくトレーイングされて､General (Compact) ドメインと Landmark (Compact) ドメインの TensorFlow にエクスポートされたモデルについては､[平均値を (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample) にしました。

## <a name="march-1-2018"></a>2018 年 3 月 1 日

- 有料プレビューとなり、Azure portal への配布準備が行われました。 F0 (Free) レベルまたは S0 (Standard) レベルで Azure リソースにプロジェクトをアタッチできるようになりました。 S0 レベルのプロジェクトを導入しました。最大 100 タグおよび 25,000 画像が利用できます。
- 機械学習パイプライン/正規化パラメーターに対するバックエンド変更。 これによりユーザーが確率のしきい値を調整する際に、精度とリコールのトレードオフを制御しやすくなります。 この変更の一環として、CustomVision.ai ポータルでは既定の確率のしきい値が 50% に設定されました。

## <a name="december-19-2017"></a>2017 年 12 月 19 日

- 既にリリースされている iOS へのエクスポート (CoreML) に加え、Android へのエクスポート (TensorFlow) が追加されました。これにより、トレーニング済みのコンパクト モデルをエクスポートして、アプリケーションでオフライン実行することができます。
- Retail および Landmark の "コンパクト" ドメインを追加し、これらのドメインのモデルのエクスポートに対応しました。
- [1.2 Training API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) および [1.1 Prediction API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164) バージョンをリリースしました。 更新された API は、モデルのエクスポート、"Predictions" に画像を保存しない新しい予測操作をサポートします。また、Training API にバッチ操作が導入されています。
- UX が調整されています (イテレーションのトレーニングに使用されたドメインを確認する機能など)。
- [C# SDK およびサンプル](https://github.com/Microsoft/Cognitive-CustomVision-Windows)を更新しました。