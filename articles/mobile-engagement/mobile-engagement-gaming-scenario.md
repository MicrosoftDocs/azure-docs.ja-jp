---
title: "ゲーム アプリでの Azure Mobile Engagement の実装"
description: "Azure Mobile Engagement を実装するためのゲーム アプリのシナリオ"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2cafc044-4902-4058-8037-49399bf6bf7f
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 06e16033435ed0a37d5688055743875827d3aec2
ms.openlocfilehash: 0ca35a3d634db8eb5c63afacba046a35b8a3e7ed
ms.lasthandoff: 03/01/2017


---
# <a name="implement-mobile-engagement-with-gaming-app"></a>ゲーム アプリで Mobile Engagement を実装する
## <a name="overview"></a>Overview
ゲームのスタートアップが、新しい釣りベースのロールプレイング/戦略ゲーム アプリを立ち上げました。 このゲームは発売から 6 か月が経過しています。 ゲームは大成功し、数百万ものダウンロードがあり、他のスタートアップのゲーム アプリと比べ、リテンション期間が非常に長くなっています。 四半期ごとのレビュー会議で、関係者はユーザー&1; 人あたりの平均収益 (ARPU) を向上させる必要があることに同意しました。 特別プランとしてゲーム内のプレミアム パッケージが提供されています。 このゲーム パックにより、ユーザーはゲームで使用する釣り糸やルアーなどの道具の外観や性能をアップグレードできるようになります。 しかし、パッケージの売上は非常に低いです。 そこで、分析ツールを使用してカスタマー エクスペリエンスを分析し、セグメント化を細分化して売上を向上させるエンゲージメント プログラムを開発することにしました。

「 [Azure Mobile Engagement - 概要とベスト プラクティス](mobile-engagement-getting-started-best-practices.md) 」に基づき、エンゲージメント戦略を構築します。

## <a name="objectives-and-kpis"></a>目標と KPI
ゲームの主要な関係者で顔合わせをします。 全員が、プレミアム パッケージの売上を 15% 上げるという 1 つの大きな目標に合意しています。 この目標の進捗を測定して推進するため、次のようなビジネスの主要業績評価指標 (KPI) を作成します。

* このパッケージが購入されているのは、ゲームのどのレベルか。
* ユーザーごと、セッションごと、週ごと、月ごとの収益はどれくらいか。
* 人気の購入タイプは何か。

[概要](mobile-engagement-getting-started-best-practices.md) のパート 1 で目標と KPI の定義方法を説明しています。 

ビジネスの KPI が定義されたことで、モバイル製品マネージャーがエンゲージメント KPI を作成し、新しいユーザーの傾向とリテンション期間を決定します。

* 毎日、2 日ごと、週ごと、月ごと、3 か月ごとの間隔でリテンション期間と使用状況を監視
* アクティブなユーザー数
* ストアでのアプリの評価

IT チームからの推奨事項に基づき、次の質問に答えるためのテクニカル KPI が追加されました。

* ユーザー パス (どのページにアクセスされているか、そのページにユーザーがどれくらい滞在したか)
* セッションごとに発生したクラッシュまたはバグの数
* ユーザーが実行している OS のバージョン
* ユーザーの画面の平均サイズ
* ユーザーが使用するインターネット接続の種類

モバイル製品マネージャーが、必要なデータと、それがプレイブックのどこにあるかを KPI ごとに指定します。

## <a name="engagement-program-and-integration"></a>エンゲージメント プログラムおよび統合
プログラムの責任者であるモバイル プロジェクト ディレクターは、高度なエンゲージメント プログラムを構築する前に、ユーザーがいつどのように製品を消費するかについて深く理解しておく必要があります。

3 か月後、モバイル プロジェクト ディレクターは、アプリ内のプッシュ通知による売上を強化するための十分なデータを収集しました。 次のことがわかりました。

* 最初の購入は、一般的にレベル 14 で行われる。 そのうち 90% が、$3 の新しい伝説の武器の購入である。
* そのうち 80% で、購入を行ったユーザーが引き続き製品を使い続け、購入も行っている。
* レベル 20 をクリアしたユーザーは週に $10 以上費やすようになる。
* ユーザーは、レベル 16、24、および 32 でプレミアム パッケージを購入する傾向がある。

この分析により、モバイル プロジェクト ディレクターは、アプリの売上を強化するための特定のプッシュ通知シーケンスを作成することにしました。 彼は、スタート時プログラム、セールス プログラムおよび非アクティブ プログラムを呼び出す&3; つのプッシュ シーケンスを作成します。 詳しくは、[プレイブック](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks)
    ![][1]を参照してください。

<!--Image references-->

[1]: ./media/mobile-engagement-game-scenario/notification-scenario.png

<!--Link references-->

