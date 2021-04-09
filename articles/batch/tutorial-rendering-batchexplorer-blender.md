---
title: チュートリアル - Azure Batch と Batch Explorer を使って Blender のシーンをレンダリングする
description: チュートリアル - Azure Batch と Batch Explorer のクライアント アプリケーションを使って Blender のシーンから複数のフレームをレンダリングする方法
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: tutorial
ROBOTS: NOINDEX
ms.openlocfilehash: 11805d2a15e7f369ad38ab7e92409c1b357d2d8e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103490842"
---
# <a name="tutorial-render-a-blender-scene-using-batch-explorer"></a>チュートリアル: Batch Explorer を使って Blender のシーンをレンダリングする

このチュートリアルでは、Blender のデモ シーンから複数のフレームをレンダリングする方法について説明します。 このチュートリアルでは、クライアント VM とレンダリング VM のどちらにも無料で利用できる Blender を使っていますが、仮に他のアプリケーション (Maya、3ds Max など) を使った場合でも手順はほぼ同じです。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Azure Storage に Blender のシーンをアップロードする
> * レンダリングを実行するための複数のノードを含んだ Batch プールを作成する
> * 複数のフレームをレンダリングする
> * レンダリングされたフレーム ファイルを確認してダウンロードする

## <a name="prerequisites"></a>前提条件

Batch で従量課金制のレンダリング アプリケーションを使用するには、従量課金制サブスクリプションまたはその他の Azure 購入オプションが必要です。 金融クレジットを提供する無料の Azure オファーを使用する場合、従量課金制ライセンスはサポートされません。

ストレージ アカウントが関連付けられている Azure Batch アカウントが必要です。  Batch アカウントの作成については、Batch のいずれかのクイック スタート記事 ([CLI に関する記事](./quick-create-cli.md) など) を参照してください。

このチュートリアルに記載されている VM サイズと VM 数には、少なくとも 50 コアから成る低優先度コア クォータが必要です。既定のクォータを使用することはできますが、より小さい VM サイズを使用しなければならなくなります。つまり、画像のレンダリングにかかる時間が長くなります。 コア クォータの引き上げを要求する手順については、[こちらの記事](./batch-quota-limit.md)を参照してください。

最後に、[Batch Explorer](https://azure.github.io/BatchExplorer/) をインストールする必要があります。Batch Explorer には、Windows 用、OSX 用、Linux 用があります。 必須ではありませんが、[Blender](https://www.blender.org/download/) がインストールされていれば、サンプル モデル ファイルを表示することができます。

## <a name="download-the-demo-scene"></a>デモ シーンをダウンロードする

[Blender のデモ ファイル Web ページ](https://www.blender.org/download/demo-files/)から Blender の "Class room" デモ ZIP ファイルをダウンロードし、ローカル ドライブにファイルを解凍します。

## <a name="upload-a-scene-to-azure-storage"></a>Azure Storage にシーンをアップロードする

デモのシーン ファイルに使用するストレージ アカウントのコンテナーを作成します。

* Batch Explorer を起動します。
* 左側にあるメイン メニューから [データ] メニュー項目を選択します。
* プルダウンで [ファイル グループ] が選択されていることを確認します。
* [+] ボタンを選択して、"blender-classroom" という "空のファイル グループ" を新たに作成します。
  * ファイル グループは単に "fgrp-" というプレフィックスが付いた Azure Storage BLOB コンテナーです。ストレージ アカウント内の他のコンテナーをフィルターで除外するために、このプレフィックスが慣例的に使用されます。

![シーン ファイル用のファイル グループ](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_scene_filegroup.png)

シーン ファイルをアップロードします。

* 新しいコンテナーを選択し、Batch Explorer で "classroom" フォルダーの内容をコンテナーにドラッグ アンド ドロップします。

![アップロードされたシーン ファイル](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_scene_filegroup_uploaded.png)

## <a name="create-azure-storage-container-for-output-images"></a>出力画像用の Azure Storage コンテナーを作成する

デモのシーン出力ファイルに使用するストレージ アカウントのコンテナーを作成します。

* 左側にあるメイン メニューから [データ] メニュー項目を選択します。
* [+] ボタンを選択して、"render-output" という "空のファイル グループ" を新たに作成します。

![出力ファイル用のファイル グループ](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_filegroup.png)

## <a name="create-a-pool-of-vms-for-rendering"></a>レンダリングに使用する VM のプールを作成する

Blender アプリケーションを含んだレンダリング用 Azure Marketplace VM イメージを使って Batch プールを作成します。

* 左側にあるメイン メニューから [ギャラリー] メニュー項目を選択します。
* アプリケーション項目一覧の [Blender] 項目を選択します。
* Windows Server 上でフレームのレンダリングに使用する項目を選択します。
* 必要に応じて項目の右側にあるリンク アイコンを選択することにより、プールとジョブの作成に使用されるテンプレート ファイルを表示できます。

![Blender のギャラリー アイテム](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_gallery_item.png)

* [Create pool for later use]\(後で使用するためにプールを作成する\) を選択します。
  * プール名は "blender-windows" のままにします。
  * [Dedicated vm count]\(専用 VM 数\) を "0" に設定します。
  * [Low priority vm count]\(低優先度 VM 数\) を "3" に設定します。
  * [ノード サイズ] を "Standard_F16" に設定します。別の VM サイズを選択することはできますが、フレームのレンダリングにかかる時間は主にコア数に依存します。
* 緑色のボタンを選択してプールを作成します。
  * プールはほぼすぐに作成されますが、VM が割り当てられて起動されるまでには数分かかります。

![Blender のプール テンプレート](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_template.png)

> [!WARNING]
> プールに VM が存在すると、ご利用のAzure サブスクリプションに、それらの VM のコストが請求されるので注意してください。 請求を停止するには、プールを削除するか、VM を削除する必要があります。 継続して請求されることのないよう、このチュートリアルの最後にプールを削除してください。

プールと VM の状態は、[プール] ビューで監視できます。次の例を見ると、全部で 3 つの VM が割り当てられ、うち 2 つは起動済みでアイドル状態、1 つは起動中であることがわかります。![プールのヒートマップ](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_heatmap.png)

## <a name="create-a-rendering-job"></a>レンダリング ジョブを作成する

作成したプールを使っていくつかのフレームをレンダリングするためにレンダリング ジョブを作成します。

* 左側にあるメイン メニューから [ギャラリー] メニュー項目を選択します。
* アプリケーション項目一覧の [Blender] 項目を選択します。
* Windows Server 上でフレームのレンダリングに使用する項目を選択します。
* [Run job with existing pool]\(既存のプールでジョブを実行\) ボタンを選択します。
* [blender-windows] プールを選択します。
* [ジョブ名] を「blender-render-tutorial1」に設定します。
* [入力データ] には「fgrp-blender-classroom」を選択します。
* [Blend file]\(Blend ファイル\) に使用するファイル アイコンを選択し、[classroom.blend] を選択します。
* [Frame start]\(フレームの先頭\) は「1」のままにし、[Frame end]\(フレームの終了\) を「5」に設定します。
* [出力] は「fgrp-render-output」に設定します。
* 緑色のボタンを選択してジョブを作成します。ジョブは、5 つのタスク (各フレームにつき 1 つ) で作成されます。

![Blender のジョブ テンプレート](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_job_template.png)

ジョブとすべてのタスクが作成されると、ジョブがそのタスクと共に表示されます。![ジョブ タスク一覧](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_task_list.png)

プールの VM 上でタスクの初回実行が開始されるとき、バッチ ジョブの準備タスクが実行されます。この準備タスクは、Blender でシーン ファイルにアクセスできるようストレージ ファイル グループから VM にシーン ファイルをコピーするものです。
レンダーの状態は、Blender によって生成される stdout.txt ログ ファイルを見て確認できます。  いずれかのタスクを選択すると、"Task Outputs (タスクの出力)" が既定で表示され、"stdout.txt" ファイルを選択して表示することができます。
![stdout ファイル](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_stdout.png)

"blender-windows" プールが選択されている場合、プールの VM が実行状態で表示されます。![プールのヒートマップと実行状態のノード](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_heatmap_running.png)

選択した VM サイズによっては、レンダリングされた画像が生成されるまでに数分かかります。  先ほど指定した F16 VM の場合、フレームのレンダリングに 16 分かかりました。

## <a name="view-the-rendering-output"></a>レンダリング出力を表示する

フレームのレンダリングが完了すると、そのタスクが完了済みとして表示されます。![タスクの完了](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_tasks_complete.png)

レンダリングされた画像はまず VM に書き込まれ、"wd" フォルダーを選択することによって表示できます。![プールのノードでレンダリングされた画像](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_image.png)

出力フレームとログ ファイルについても、ジョブの作成時に指定した Azure Storage アカウントのファイル グループに書き戻すように、ジョブ テンプレートで指定されています。  出力ファイルとログは "データ" という UI を使用して表示できます。また、それらのファイルをダウンロードすることもできます。![ストレージ ファイル グループ内のレンダリング済み画像](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_image_storage.png)

すべてのタスクが完了すると、ジョブが完了済みとしてマークされます。![ジョブとすべてのタスクが完了](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_job_alltasks_complete.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

> [!WARNING]
> Azure サブスクリプションに課される VM の料金請求を停止するには、プールを削除する必要があります (サイズ変更でノード数をゼロにしてもかまいません)。

* [プール] を選択します。
* [blender-windows] プールを選択します。
* 右クリックして [削除] を選択するか、プールの上に表示されるごみ箱アイコンを選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次の作業を行う方法を学びました。

> [!div class="checklist"]
> * Azure Storage に Blender のシーンをアップロードする
> * レンダリングを実行するための複数のノードを含んだ Batch プールを作成する
> * 複数のフレームをレンダリングする
> * レンダリングされたフレーム ファイルを確認してダウンロードする

Batch Explorer で利用できるレンダリング アプリケーションを **[ギャラリー]** セクションで探して次に進みます。 アプリケーションごとにいくつかのテンプレートが用意され、今後も拡充されていく予定です。 たとえば Blender については、単一の画像を複数のタイルに分割することで 1 つの画像を構成する各部分を並列にレンダリングできるテンプレートが存在します。

クラウド規模のレンダリングの詳細については、Batch Rendering サービスのオプションを確認してください。

> [!div class="nextstepaction"]
> [Batch Rendering サービス](batch-rendering-service.md)
