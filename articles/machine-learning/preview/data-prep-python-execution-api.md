---
title: "Azure Machine Learning データ準備実行 API の使用方法に関する詳細ガイド |Microsoft Docs"
description: "このドキュメントでは、以前に設計されたデータ ソースおよびデータ準備パッケージの実行について詳しく説明しています"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: ceecc718f8198e9a88cae4acbe97e7f26d95f984
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="executing-data-sources-and-data-preparation-packages-from-python"></a>Python からのデータ ソースおよびデータ準備パッケージの実行

Python 内でデータ ソースまたはデータ準備パッケージを使用するには:
- プロジェクトの [Data] (データ) タブに移動します。
- 適切なソースを右クリックします。
- [Generate Data Access Code File] (データ アクセス コード ファイルの生成) を選択します。

このアクションは、パッケージを実行して、データフレームを返す短い Python スクリプトを作成します。

## <a name="data-sources"></a>データ ソース

`azureml.dataprep.datasource` モジュールには、データ ソースを実行し、データフレームを返す 1 つの関数が含まれています: `load_datasource(path, secrets=None, spark=None)`。
- `path` はデータ ソース (.dsource ファイル) へのパスです。
- `secrets` はシークレットに対する省略可能な dict マッピング キーです。
- `spark` は Spark データフレームまたは Pandas データフレームを返すかどうかを指定する省略可能なブール値です。 既定では、Azure ML ワークベンチは、コンテキストに基づいてランタイムに返されるデータフレームの種類を決定します。

## <a name="data-preparation-packages"></a>データ準備パッケージ

`azureml.dataprep.package` モジュールには、データ準備パッケージからデータフローを実行する 3 つの関数が含まれています。

### <a name="execution-functions"></a>実行関数

- `submit(package_path, dataflow_idx=0, secrets=None, spark=None)` - 指定された実行用データフローを送信しますが、データフレームは返しません。
- `run(package_path, dataflow_idx=0, secrets=None, spark=None)` - 指定されたデータフローを実行し、データフレームとして結果を返します。
- `run_on_data(user_config, package_path, dataflow_idx=0, secrets=None, spark=None)` - メモリ内データ ソースに基づいて指定されたデータフローを実行し、データフレームとして結果を返します。 `user_config` 引数は、リストの一覧として表されるメモリ内データ ソースに、データ ソース (.dsource ファイル) の絶対パスをマップする dict です。

### <a name="common-arguments"></a>共通の引数

- `package_path` は、データ準備パッケージ (.dprep ファイル) へのパスです。
- `dataflow_idx` は、パッケージのどのデータフローを実行するかを示す、0 から始まるインデックスです。 指定したデータフローが他のデータフローまたはデータ ソースを参照する場合、これらも実行されます。
- `secrets` はシークレットに対する省略可能な dict マッピング キーです。
- `spark` は Spark データフレームまたは Pandas データフレームを返すかどうかを指定する省略可能なブール値です。 既定では、Azure ML ワークベンチは、コンテキストに基づいてランタイムに返されるデータフレームの種類を決定します。
