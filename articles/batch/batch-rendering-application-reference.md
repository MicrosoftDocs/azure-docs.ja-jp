---
title: レンダリング アプリケーションを使用する
description: Azure Batch でレンダリング アプリケーションを使用する方法。 この記事は各レンダリング アプリケーションを実行するための簡単な説明を提供します。
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: ace3fe7aee6b9ffc7226448b455bcfea1f931458
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85964874"
---
# <a name="rendering-applications"></a>アプリケーションをレンダリングする

レンダリング アプリケーションは、Batch の ジョブとタスクの作成で使用されます。 タスクのコマンド ライン プロパティが適切なコマンド ラインとパラメーターを指定します。  ジョブのタスクを作成する最も簡単な方法は、[この記事](./batch-rendering-using.md#using-batch-explorer)で説明するように Batch Explorer のテンプレートを使用する方法です。  テンプレートは必要に応じて表示および変更されたバージョンを作成できます。

この記事は各レンダリング アプリケーションを実行するための簡単な説明を提供します。

## <a name="rendering-with-autodesk-3ds-max"></a>Autodesk 3ds Max を使用したレンダリング

### <a name="renderer-support"></a>レンダラーのサポート

3ds Max に組み込まれているレンダラーに加えて、VM イメージのレンダリングには次のレンダラーを使用可能で、3ds Max シーン ファイルから参照できます。

* Autodesk Arnold
* Chaos Group V-Ray

### <a name="task-command-line"></a>タスクのコマンド ライン

`3dsmaxcmdio.exe` アプリケーションを呼び出し、プール ノード上でコマンド ライン レンダリングを実行します。  このアプリケーションはタスクが実行されるときにはパス上にあります。 `3dsmaxcmdio.exe` アプリケーションでは `3dsmaxcmd.exe` アプリケーションと同じパラメーターを使用できます。これは、[3ds Max のヘルプ ドキュメント](https://help.autodesk.com/view/3DSMAX/2018/ENU/) (Rendering | Command-Line Rendering セクション) に記載されています。

次に例を示します。

```
3dsmaxcmdio.exe -v:5 -rfw:0 -start:{0} -end:{0} -bitmapPath:"%AZ_BATCH_JOB_PREP_WORKING_DIR%\sceneassets\images" -outputName:dragon.jpg -w:1280 -h:720 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scenes\dragon.max"
```

メモ:

* アセット ファイルが見つかるように細心の注意を払う必要があります。  **[資産管理]** ウィンドウ、またはコマンド ラインで `-bitmapPath` パラメーターを使用して、パスが正しくかつ相対的であることを確認します。
* タスクが実行されるときに 3ds Max によって記述された `stdout.txt` ファイルをチェックすることで、アセットが見つからないなど、レンダーに問題がないかどうかを確認します。

### <a name="batch-explorer-templates"></a>Batch Explorer のテンプレート

プールやジョブのテンプレートは、Batch Explorer の **[ギャラリー]** からアクセスできます。  テンプレートのソース ファイルは、[GitHub の Batch Explorer データ リポジトリ](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/3dsmax)にあります。

## <a name="rendering-with-autodesk-maya"></a>Autodesk Maya を使用したレンダリング

### <a name="renderer-support"></a>レンダラーのサポート

Maya に組み込まれているレンダラーに加えて、VM イメージのレンダリングには次のレンダラーを使用可能で、3ds Max シーン ファイルから参照できます。

* Autodesk Arnold
* Chaos Group V-Ray

### <a name="task-command-line"></a>タスクのコマンド ライン

タスクのコマンド ラインでは、`renderer.exe` コマンド ライン レンダラーが使用されます。 コマンド ライン レンダラーは [Maya のヘルプ](https://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=GUID-EB558BC0-5C2B-439C-9B00-F97BCB9688E4)に記載されています。

次の例では、ジョブ準備タスクを使用してシーンのファイルとアセットをジョブ準備作業ディレクトリにコピーし、出力フォルダーを使用してレンダリング イメージを格納して、フレーム 10 がレンダリングされます。

```
render -renderer sw -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

V-Ray レンダリングでは、通常は Maya のシーン ファイルが V-Ray をレンダラーとして指定します。  コマンドラインで指定することもできます。

```
render -renderer vray -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Arnold レンダリングでは、通常は Maya のシーン ファイルが Arnold をレンダラーとして指定します。  コマンドラインで指定することもできます。

```
render -renderer arnold -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

### <a name="batch-explorer-templates"></a>Batch Explorer のテンプレート

プールやジョブのテンプレートは、Batch Explorer の **[ギャラリー]** からアクセスできます。  テンプレートのソース ファイルは、[GitHub の Batch Explorer データ リポジトリ](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/maya)にあります。

## <a name="next-steps"></a>次のステップ

Batch Explorer を使用して [GitHub のデータ リポジトリ](https://github.com/Azure/BatchExplorer-data/tree/master/ncj)からプールとジョブのテンプレートを使用します。  必要に応じて、新しいテンプレートするか、提供されたテンプレートの 1 つを変更します。
