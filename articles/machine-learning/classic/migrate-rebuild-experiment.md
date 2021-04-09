---
title: 'ML スタジオ (クラシック): Azure Machine Learning に移行する - 実験の再構築'
description: スタジオ (クラシック) の実験を Azure Machine Learning デザイナーで再構築します。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: bb944cb034fdd7cc51648314154a654bc1265533
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565643"
---
# <a name="rebuild-a-studio-classic-experiment-in-azure-machine-learning"></a>スタジオ (クラシック) の実験を Azure Machine Learning で再構築します。

この記事では、スタジオ (クラシック) の実験を Azure Machine Learning で再構築する方法について説明します。 スタジオ (クラシック) からの移行の詳細については、[移行の概要に関する記事](migrate-overview.md)を参照してください。

スタジオ (クラシック) の **実験** は、Azure Machine Learning の **パイプライン** に似ています。 ただし、Azure Machine Learning では、パイプラインは SDK を作動させるそのバックエンド上で構築されます。 つまり、機械学習の開発には、ドラッグ アンド ドロップ デザイナーとコードファースト SDK の 2 つのオプションが用意されていることになります。

SDK を使用したパイプラインの構築の詳細については、「[Azure Machine Learning パイプラインとは](../concept-ml-pipelines.md#building-pipelines-with-the-python-sdk)」を参照してください。


## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Azure Machine Learning ワークスペース。 [Azure Machine Learning ワークスペースを作成](../how-to-manage-workspace.md#create-a-workspace)します。
- 移行するスタジオ (クラシック) の実験。
- Azure Machine Learning に[データセットをアップロードする](migrate-register-dataset.md)

## <a name="rebuild-the-pipeline"></a>パイプラインを再構築する

[データセットを Azure Machine Learning に移行](migrate-register-dataset.md)したら、実験を再作成する準備は完了です。

Azure Machine Learning では、ビジュアル グラフは **パイプライン ドラフト** と呼ばれます。 このセクションでは、クラシックの実験をパイプライン ドラフトとして再作成します。

1. Azure Machine Learning スタジオ ([ml.azure.com](https://ml.azure.com)) に移動します。
1. 左側のナビゲーション ウィンドウで、 **[デザイナー]** > **[Easy-to-use prebuilt modules]\(使いやすい事前構築済みモジュール\)** の順に選択します。 ![新しいパイプライン ドラフトの作成方法を示すスクリーンショット。](../media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. デザイナー モジュールを使用して実験を手動で再構築します。
    
    後継モジュールを見つけるには、[モジュール マッピング テーブル](migrate-overview.md#studio-classic-and-designer-module-mapping)を参照してください。 スタジオ (クラシック) の最も人気のあるモジュールの多くは、デザイナーにも同じバージョンがあります。

    > [!Important]
    > 実験で "R スクリプトの実行" モジュールが使用されている場合、ご自分の実験を移行するには追加の手順を行う必要があります。 詳細については、[R スクリプト モジュールの移行](migrate-execute-r-script.md)に関するページを参照してください。

1. パラメーターを調整します。
    
    各モジュールを選択し、右側にある [モジュールの設定] パネルでパラメーターを調整します。 そのパラメーターを使用して、スタジオ (クラシック) の実験の機能を再作成します。 各モジュールの詳細については、[モジュールのリファレンス](../algorithm-module-reference/module-reference.md)に関するページを参照してください。

## <a name="submit-a-run-and-check-results"></a>実行および確認の結果を送信する

スタジオ (クラシック) の実験を再作成したら、**パイプライン実行** を送信します。

パイプライン実行は、ご利用のワークスペースに接続された **コンピューティング先** で実行されます。 パイプライン全体に対して既定のコンピューティング先を設定することも、モジュールごとにコンピューティング先を指定することもできます。

パイプライン ドラフトから実行を送信すると、それは **パイプライン実行** になります。 各パイプライン実行は、Azure Machine Learning で記録およびログされます。

パイプライン全体に対する既定のコンピューティング先を設定するには、次のようにします。
1. パイプライン名の横にある **歯車アイコン** ![デザイナー内の歯車アイコン](../media/tutorial-designer-automobile-price-train-score/gear-icon.png) を選択します。
1. **[コンピューティング先を選択]** を選択します。
1. 既存のコンピューティングを選択するか、新しいコンピューティングを画面の指示に従って作成します。

これでコンピューティング先が設定されたので、パイプライン実行を送信することができます。

1. キャンバス上部の **[Submit]\(送信\)** を選択します。
1. 新しい実験を作成するには、 **[新規作成]** を選択します。
    
    実験では、類似したパイプライン実行が一緒にまとめられます。 パイプラインを複数回実行する場合は、一連の実行に対して同じ実験を選択できます。 これは、ログと追跡を行う場合に役立ちます。
1. 実験の名前を入力します。 次に、 **[送信]** をクリックします。

    最初の実行には最長で 20 分かかる場合があります。 既定のコンピューティング設定の最小ノード サイズは 0 であるため、アイドル状態になった後に、デザイナーによってリソースが割り当てられる必要があります。 ノードは既に割り当てられているため、連続実行にかかる時間は短くなります。 実行時間を短縮するには、最小ノード サイズが 1 以上のコンピューティング リソースを作成します。

実行が完了したら、各モジュールの結果を確認できます。

1. 出力を確認したいモジュールを選択します。
1. **[可視化]** 、 **[出力の表示]** 、または **[ログの表示]** のいずれかを選択します。

    - **可視化**: 結果のデータセットをプレビューします。
    - **View Output (出力の表示)** : 出力の格納場所へのリンクを開きます。 これを使用して、出力を探索またはダウンロードします。 
    - **ログの表示**: ドライバーとシステム ログを表示します。 エラーや例外など、ユーザーが送信したスクリプトに関連する情報を表示するには、**70_driver_log** を使用します。

> [!IMPORTANT]
> スタジオ (クラシック) では C# パッケージが使用されているのに対して、デザイナー モジュールではオープン ソースの Python パッケージが使用されます。 その結果、デザイナーとスタジオ (クラシック) ではモジュールの出力が多少異なる場合があります。 


## <a name="next-steps"></a>次のステップ

この記事では、スタジオ (クラシック) の実験を Azure Machine Learning で再構築する方法について説明しました。 次の手順では、[Azure Machine Learning で Web サービスを再構築します](migrate-rebuild-web-service.md)。


スタジオ (クラシック) 移行シリーズの他の記事を参照してください。

1. [移行の概要](migrate-overview.md)に関するドキュメントを参照してください。
1. [データセットを移行します](migrate-register-dataset.md)。
1. **スタジオ (クラシック) のトレーニング パイプラインを再構築します**。
1. [スタジオ (クラシック) の Web サービスを再構築します](migrate-rebuild-web-service.md)。
1. [Azure Machine Learning の Web サービスをクライアント アプリと統合します](migrate-rebuild-integrate-with-client-app.md)。
1. [R スクリプトの実行を移行します](migrate-execute-r-script.md)。
