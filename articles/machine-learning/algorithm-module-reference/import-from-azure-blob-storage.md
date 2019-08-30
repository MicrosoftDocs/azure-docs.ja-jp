---
title: 'Azure Blob Storage からのインポート: モジュール リファレンス'
titleSuffix: Azure Machine Learning service
description: このトピックでは、Azure Machine Learning service の "Azure Blob Storage からのインポート" モジュールを使用して、機械学習の実験に使用するために Azure Blob Storage からデータを読み取る方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: fea64070c496379351bb75f2a38aba9b4db70dcd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128720"
---
# <a name="import-from-azure-blob-storage-module"></a>"Azure Blob Storage からのインポート" モジュール

この記事では、Azure Machine Learning service のビジュアル インターフェイス (プレビュー) のモジュールについて説明します。

機械学習の実験にデータを使用できるように Azure Blob Storage からデータを読み取るには、このモジュールを使用します。  

Azure Blob service は、大量のデータ (バイナリ データを含む) を格納することを目的としています。 Azure BLOB には、HTTP または HTTPS を使用して、どこからでもアクセスすることができます。 Blob Storage の種類によっては、認証が要求される場合があります。 

- パブリック BLOB は、だれでも (つまり、SAS URL を持つユーザーが) アクセスできます。
- プライベート BLOB では、ログインと資格情報が要求されます。

Blob Storage からのインポートでは、**ブロック BLOB** 形式が使用された BLOB にデータが格納されている必要があります。 BLOB に格納されたファイルには、コンマ区切り (CSV) またはタブ区切り (TSV) 形式が使用されている必要があります。 ファイルを読み取る際、レコードおよび適切な属性の見出しが、データセットとして行単位でメモリに読み込まれます。


スキーマが正しいことを確認するために、データはインポート前にプロファイルしておくことを強くお勧めします。 スキーマは、インポート プロセスでいくつかの見出し行をスキャンすることによって特定されますが、後続の行に余分な列やエラーの原因となるデータが含まれている可能性もあります。



## <a name="manually-set-properties-in-the-import-data-module"></a>"データのインポート" モジュールのプロパティを手動で設定する

次の手順では、インポート元を手動で構成する方法について説明します。

1. **データのインポート** モジュールを実験に追加します。 このモジュールは、インターフェイスの **[Data Input and Output]\(データの入力と出力\)** にあります。

2. **[データ ソース]** に **[Azure Blob Storage]** を選択します。

3. 情報がパブリック データ ソースとして提供されていることがわかっている場合は、 **[認証の種類]** で **[パブリック (SAS URL)]** を選択します。 SAS URL は、パブリック アクセス用の期限付きの URL で、Azure ストレージ ユーティリティを使用して生成できます。

    それ以外の場合は、 **[アカウント]** を選択します。

4. SAS URL を使用してアクセスできる**パブリック** BLOB にデータが存在する場合、ダウンロードと認証に必要な情報はすべて URL 文字列に含まれているため、それ以外の資格情報は必要ありません。

    **[URI]** フィールドに、アカウントとパブリック BLOB を定義する完全 URI を入力するか貼り付けます。



5. データが**プライベート** アカウントに存在する場合は、アカウント名とキーを含んだ資格情報を指定する必要があります。

    - **[アカウント名]** に、アクセスしたい BLOB が含まれているアカウントの名前を入力するか貼り付けます。

        たとえば、ストレージ アカウントの完全 URL が `http://myshared.blob.core.windows.net` であれば、「`myshared`」と入力します。

    - **[アカウント キー]** には、アカウントに関連付けられているストレージ アクセス キーを貼り付けます。

        アクセス キーがわからない場合、次の記事で Azure ストレージ アカウントの管理に関するセクションを参照してください:[Azure ストレージ アカウントについて](https://docs.microsoft.com/azure/storage/storage-create-storage-account)。

6. **[コンテナー、ディレクトリ、BLOB のパス]** に、取得したい特定の BLOB の名前を入力します。

    たとえば、**mymldata** という名前のアカウントのコンテナー **trainingdata** に **data01.csv** という名前のファイルをアップロードした場合、ファイルの完全 URL は `http://mymldata.blob.core.windows.net/trainingdata/data01.txt` になります。

    そのため、 **[コンテナー、ディレクトリ、BLOB のパス]** フィールドには、「`trainingdata/data01.csv`」と入力することになります。

    複数のファイルをインポートする場合、ワイルドカード文字 `*` (アスタリスク) または `?` (疑問符) を使用できます。

    たとえば、互換性がある形式の複数のファイルが `trainingdata` コンテナーに含まれている場合、次のように指定することで、`data` で始まるすべてのファイルを読み取って、単一のデータセットに連結することができます。

    `trainingdata/data*.csv`

    コンテナー名にワイルドカードを使用することはできません。 複数のコンテナーからファイルをインポートする必要がある場合、コンテナーごとに**データのインポート** モジュールの個別のインスタンスを使用し、[行の追加](./add-rows.md)モジュールを使ってデータセットをマージしてください。

    > [!NOTE]
    > **[Use cached results]\(キャッシュされた結果を使用する\)** オプションを選択した場合、コンテナー内のファイルに変更を加えても、実験のデータは更新されません。

7. Azure Machine Learning がデータを適切に処理できるよう、BLOB に格納されているデータの形式を指定するオプションを **[BLOB ファイル形式]** で選択します。 次の形式がサポートされています。

    - **CSV**: コンマ区切り値 (CSV) は、Azure Machine Learning でファイルをエクスポートおよびインポートする際の既定の保存形式です。 あらかじめデータにヘッダー行が含まれている場合は、必ず **[ファイルにヘッダー行が含まれています]** オプションを選択してください。そうしないと、ヘッダーがデータ行として扱われます。

       

    - **TSV**: タブ区切り値 (TSV) は、多くの機械学習ツールで使用される形式です。 あらかじめデータにヘッダー行が含まれている場合は、必ず **[ファイルにヘッダー行が含まれています]** オプションを選択してください。そうしないと、ヘッダーがデータ行として扱われます。

       

    - **ARFF**: この形式は、Weka ツールセットで使用される形式でのファイルのインポートをサポートします。 

   

8. 実験を実行します。


## <a name="next-steps"></a>次の手順

Azure Machine Learning service で[使用できる一連のモジュール](module-reference.md)を参照してください。 