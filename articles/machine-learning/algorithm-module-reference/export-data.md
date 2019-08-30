---
title: 'データのエクスポート: モジュール リファレンス'
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service でデータのエクスポート モジュールを使用して、実験の結果や中間データ、作業データを Azure Machine Learning の外部のクラウド ストレージに保存する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: a4fb539f4c86d27813b60964794fc1f398d3f2a4
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128777"
---
# <a name="export-data-module"></a>データのエクスポート モジュール

この記事では、Azure Machine Learning service のビジュアル インターフェイス (プレビュー) のモジュールについて説明します。

実験の結果や中間データ、作業データを Azure Machine Learning の外部のクラウド ストレージに保存するには、このモジュールを使用します。

このモジュールは、次のクラウド データ サービスにデータをエクスポートまたは保存する作業を支援します。


- **Azure Blob Storage へのエクスポート**: Azure の Blob service にデータを保存します。 Blob service 内のデータは、パブリックに共有したり、セキュリティで保護されたアプリケーション データ ストアに保存したりすることができます。

  
## <a name="how-to-configure-export-data"></a>データのエクスポートを構成する方法

1. インターフェイスで、**データのエクスポート** モジュールを実験に追加します。 このモジュールは、 **[Input and Output]\(入力と出力\)** カテゴリにあります。

2. エクスポートしたいデータがあるモジュールに **[Export Data]\(データのエクスポート\)** を接続します。

3. **[Export Data]\(データのエクスポート\)** をダブルクリックして **[Properties]\(プロパティ\)** ウィンドウを開きます。

4. **[Data destination]\(データのエクスポート先\)** で、データの保存先となるクラウド ストレージの種類を選択します。 このオプションに変更を加えた場合、他のプロパティはすべてリセットされます。 したがって、このオプションは必ず最初に選択してください。

5. 指定したストレージ アカウントへのアクセスに必要なアカウント名と認証方法を入力します。

    プライベート プレビューでは、 **[Export to Azure Blob Storage]\(Azure Blob Storage へのエクスポート\)** のみ選択できます。 以下に、このモジュールの設定方法を示します。
    1. Azure Blob service は、大量のデータ (バイナリ データを含む) を格納することを目的としています。 Blob Storage には 2 種類あります。パブリックな BLOB と、ログイン資格情報を必要とする BLOB です。

    2. 対象のストレージが SAS URL でのアクセスをサポートしているとわかっている場合は、 **[認証の種類]** で **[パブリック (SAS)]** を選択します。

          SAS URL は、Azure Storage ユーティリティを使用して生成でき、一定期間のみ利用できる特殊な種類の URL です。  認証とダウンロードに必要なすべての情報を含んでいます。

        **[URI]** に、アカウントとパブリック BLOB を定義する完全 URI を入力するか貼り付けます。

        ファイル形式は、CSV と TSV がサポートされます。

    3. プライベート アカウントの場合は、 **[アカウント]** を選択し、アカウント名とアカウント キーを指定することにより、実験でストレージ アカウントへの書き込みができるようになります。

         - **アカウント名**:データを保存するアカウントの名前を入力するか貼り付けます。 たとえば、ストレージ アカウントの完全 URL が `http://myshared.blob.core.windows.net` であれば、「`myshared`」と入力します。

        - **アカウント キー**: アカウントに関連付けられているストレージ アクセス キーを貼り付けます。

        -  **コンテナー、ディレクトリ、BLOB のパス**: エクスポートしたデータの格納先となる BLOB の名前を入力します。 たとえば、**mymldata** という名前のアカウントのコンテナー **predictions** に格納される **results01.csv** という名前の新しい BLOB に実験の結果を保存する場合、BLOB の完全 URL は `http://mymldata.blob.core.windows.net/predictions/results01.csv` になります。

            そのため、 **[コンテナー、ディレクトリ、BLOB のパス]** フィールドには、該当するコンテナーと BLOB の名前を「`predictions/results01.csv`」のように指定することになります。

        - まだ存在しない BLOB の名前を指定した場合、Azure によって自動的に BLOB が作成されます。

       -  既存の BLOB に書き込む場合は、 **[Azure blob storage write mode]\(Azure Blob Storage の書き込みモード\)** プロパティの設定で、BLOB の現在のコンテンツを上書きするように指定できます。 既定では、このプロパティが **[エラー]** に設定されています。この場合、同じ名前の既存のBLOB ファイルが見つかるとエラーが発生します。


    4. **[File format for blob file]\(BLOB ファイルのファイル形式\)** で、データの保存形式を選択します。

        - **CSV**: コンマ区切り値 (CSV) は既定の保存形式です。 データと一緒に列見出しをエクスポートする場合は、 **[Write blob header row]\(BLOB ヘッダー行を書き込む\)** を選択してください。  Azure Machine Learning で使用されるコンマ区切り形式の詳細については、「[Convert to CSV (CSV への変換)](./convert-to-csv.md)」を参照してください。

        - **TSV**: タブ区切り値 (TSV) は、多くの機械学習ツールと互換性がある形式です。 データと一緒に列見出しをエクスポートする場合は、 **[Write blob header row]\(BLOB ヘッダー行を書き込む\)** を選択してください。  

 
    5. **[Use cached results]\(キャッシュされた結果を使用する\)** : 実験を実行するたびに BLOB ファイルに結果が再書き込みされるのを防ぐには、このオプションをオンにします。 それ以外でモジュールのパラメーターに変更がなければ、実験結果が書き込まれるのは、モジュールの初回実行時と、データに変更が生じたときだけです。

    6. 実験を実行します。

## <a name="next-steps"></a>次の手順

Azure Machine Learning service で[使用できる一連のモジュール](module-reference.md)を参照してください。 