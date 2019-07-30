---
title: 制限とクォータ - Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Custom Vision Service の制限とクォータについて説明します。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 9cff5fdac39be2338305cd37a4b2328a28a48255
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269267"
---
# <a name="limits-and-quotas"></a>制限とクォータ

Custom Vision Service にはキーの層が 2 つあります。 Azure portal を通じて F0 (Free) または S0 (Standard) サブスクリプションにサインアップすることができます。 価格とトランザクションについて詳しくは、対応する [Cognitive Services の価格ページ](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)をご覧ください。

プロジェクトあたりトレーニング画像数とプロジェクトあたりタグ数は、S0 プロジェクトの場合、時間の経過と共に増えることが予想されます。

||**F0**|**S0**|
|-----|-----|-----|
|プロジェクト|2|100|
|プロジェクトあたりのトレーニング画像 |5,000|100,000|
|予測/月|10,000 |無制限|
|タグ/プロジェクト|50|500|
|反復回数 |10|10|
|タグあたりの最小ラベル付き画像、分類 (50+ 推奨) |5|5|
|タグあたりの最小ラベル付き画像、物体検出 (50+ 推奨)|15|15|
|予測画像の保存期間|30 日|30 日|
|[予測](https://go.microsoft.com/fwlink/?linkid=865445)操作、ストレージあり (秒あたりトランザクション)|2|10|
|[予測](https://go.microsoft.com/fwlink/?linkid=865445)操作、ストレージなし (秒あたりトランザクション)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (秒あたり API 呼び出し)|2|10|
|[その他の API 呼び出し](https://go.microsoft.com/fwlink/?linkid=865446) (秒あたりトランザクション)|10|10|
|最大画像サイズ (トレーニング画像アップロード) |6 MB|6 MB|
|最大画像サイズ (予測)|4 MB|4 MB|
|物体検出のトレーニング画像あたりの最大領域|200|200|
|分類画像あたりの最大タグ数|30|30|
