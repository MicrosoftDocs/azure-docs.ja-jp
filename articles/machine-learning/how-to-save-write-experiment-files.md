---
title: 実験ファイルを保存する場所と書き込む場所
titleSuffix: Azure Machine Learning
description: ストレージ制限エラーと実験の待機時間を回避するために入力および出力のファイルを保存する場所について説明します。
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 03/10/2020
ms.openlocfilehash: ad641c2270f94b9d902a25e8d061fb1137a0cdb7
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518604"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Azure Machine Learning の実験でファイルを保存する場所と書き込む場所


この記事では、ストレージ制限エラーを回避し、待機時間を実験するために、実験で入力ファイルをどこに保存し、出力ファイルをどこに書き込むかについて説明します。

[コンピューティング ターゲット](concept-compute-target.md)でトレーニング実行を起動すると、外部環境から分離されます。 この設計の目的は、実行の再現性と移植性を確保することです。 同一または別のコンピューティング ターゲットで、同じスクリプトを 2 回実行すると、同じ結果が得られます。 この設計により、コンピューティング ターゲットを、完了後に実行されるジョブとのアフィニティがないステートレスな計算リソースとして扱うことができます。

## <a name="where-to-save-input-files"></a>入力ファイルを保存する場所

コンピューティング ターゲットまたはローカル マシンで実験を開始する前に、コードの実行に必要な依存関係ファイルやデータ ファイルなどの必要なファイルが、そのコンピューティング ターゲットに存在することを確認する必要があります。

Azure Machine Learning では、ソース ディレクトリ全体をコピーすることで、トレーニング スクリプトが実行されます。 アップロードしたくない機密データがある場合は、[.ignore ファイル](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots)を使用するか、ソース ディレクトリに含めないようにします。 代わりに、[データストア](/python/api/azureml-core/azureml.data)を使用してデータにアクセスしてください。

実験スナップショットのストレージ制限は、300 MB または 2000 ファイル (あるいはその両方) です。

このため、次のことをお勧めします。

* **お使いのファイルを Azure Machine Learning の [データストア](/python/api/azureml-core/azureml.data)に格納します。** これにより、実験の待機時間の問題を防止でき、リモート コンピューティング ターゲットからデータにアクセスする利点が得られます。つまり、認証とマウントは Azure Machine Learning によって管理されます。 データストアをソース ディレクトリとして指定し、データストアにファイルをアップロードする方法の詳細については、[データストアからデータにアクセスする](how-to-access-data.md)方法に関する記事を参照してください。

* **数個のデータ ファイルと依存関係スクリプトのみが必要であり、データストアを使用できない場合は、** トレーニング スクリプトと同じフォルダー ディレクトリにファイルを配置します。 このフォルダーを `source_directory` ディレクトリとして、トレーニング スクリプトで直接指定するか、またはトレーニング スクリプトを呼び出すコードで指定します。

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>実験スナップショットのストレージ制限

実験では、Azure Machine Learning により、実行を構成したときに提案したディレクトリに基づいてコードの実験スナップショットが自動的に作成されます。 これには、合計 300 MB または 2000 ファイルの制限があります。 この制限を超えると、次のエラーが表示されます。

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

このエラーを解決するには、実験ファイルをデータストアに格納します。 データストアを使用できない場合は、以下の表に可能な代替解決策を示します。

実験の説明&nbsp;|ストレージ制限の解決策
---|---
ファイル数は 2000 未満で、データストアを使用できない場合| 次を使用してスナップショットのサイズ制限を上書きします。 <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> ファイルの数とサイズによっては、これには数分間かかる場合があります。
特定のスクリプト ディレクトリを使用する必要がある場合| [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
パイプライン|ステップごとに異なるサブディレクトリを使用します。
Jupyter Notebook| `.amlignore` ファイルを作成するか、ノートブックを新しい空のサブディレクトリに移動して、コードをもう一度実行します。

## <a name="where-to-write-files"></a>ファイルを書き込む場所

トレーニング実験は分離されているため、実行時に発生したファイルへの変更が、必ずしも環境の外部で保持されるとは限りません。 スクリプトによって、ローカルのコンピューティング用のファイルが変更された場合、それらの変更は次回の実験の実行では保持されず、クライアント コンピューターに自動的に反映されることもありません。 したがって、最初の実験実行時に行われた変更が、2 回目のものに影響を与えることはなく、また与えてはなりません。

変更を書き込む場合は、Azure Machine Learning のデータストアにファイルを書き込むことをお勧めします。 [データストアからデータにアクセスする](how-to-access-data.md)方法に関する記事を参照してください。

データストアが必要ない場合は、`./outputs` フォルダーと `./logs` フォルダー、またはそのいずれかにファイルを書き込んでください。

>[!Important]
> *outputs* と *logs* の 2 つのフォルダーは、Azure Machine Learning によって特別に扱われます。 トレーニング中に `./outputs` フォルダーと `./logs` フォルダーにファイルを書き込んだ場合、それらのファイルは実行履歴に自動的にアップロードされて、実行が完了すると、それらのファイルにアクセスできるようになります。

* **状態メッセージやスコアリングの結果などの出力の場合は、** ファイルを `./outputs` フォルダーに書き込みます。それにより、それらは実行履歴に成果物として保持されます。 このフォルダーに書き込むファイルの数とサイズに注意してください。内容が実行履歴にアップロードされるときに待機時間が発生する可能性があります。 待機時間が心配な場合は、データストアにファイルを書き込むことをお勧めします。

* **書き込まれたファイルを実行履歴にログとして保存するには、** ファイルを `./logs` フォルダーに書き込みます。 この方法は、ログがリアルタイムでアップロードされるため、リモート実行からのライブ更新ストリーミングに適しています。

## <a name="next-steps"></a>次のステップ

* [データストアからデータにアクセスする](how-to-access-data.md)方法に関する詳細を確認します。

* 詳細については、[モデルのトレーニングとデプロイのためのコンピューティング先の作成](how-to-create-attach-compute-studio.md)に関するページを参照してください。