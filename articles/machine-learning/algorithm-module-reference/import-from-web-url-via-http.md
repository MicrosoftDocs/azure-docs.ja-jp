---
title: HTTP 経由の Web URL からのインポート:モジュール リファレンス
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service の HTTP 経由の Web URL からのインポート モジュールを使用して、機械学習の実験で使用するためにパブリック Web ページからデータを読み取る方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 59b8e2e73b9904a503c16d8891e5a5bd771fc87f
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128753"
---
# <a name="import-from-web-url-via-http-module"></a>HTTP 経由の Web URL からのインポート モジュール

この記事では、Azure Machine Learning service のビジュアル インターフェイス (プレビュー) のモジュールについて説明します。

機械学習の実験で使用するためにパブリック Web ページからデータを読み取るには、このモジュールを使用します。

Web ページで公開されているデータには次の制限が適用されます。

- データは、サポートされている次の形式のいずれかである必要があります。CSV、TSV、ARFF、または SvmLight。 その他のデータではエラーが発生します。
- 認証が必須でもサポートされてもいないこと。 データは公開されている必要があります。 

データを取得する 2 つの方法があります。 ウィザードを使用してデータ ソースを設定するか、または手動で構成します。

## <a name="use-the-data-import-wizard"></a>データ インポート ウィザードを使用する

1. **データのインポート** モジュールを実験に追加します。 このモジュールは、インターフェイスの **[Data Input and Output]\(データの入力と出力\)** カテゴリにあります。

2. **[Launch Import Data Wizard]\(データのインポート ウィザードを起動\)** をクリックし、[HTTP を使用する Web URL] を選択します。

3. URL を貼り付け、データ形式を選択します。

4. これで構成は完了です。

既存のデータ接続を編集するには、ウィザードを再び起動します。 ウィザードにより前の構成詳細がすべて読み込まれるため、一からやり直す必要はありません。

## <a name="manually-set-properties-in-the-import-data-module"></a>データのインポート モジュールのプロパティを手動で設定する

次の手順では、インポート元を手動で構成する方法について説明します。

1. [データのインポート](import-data.md) モジュールを実験に追加します。 このモジュールは、インターフェイスの **[Data Input and Output]\(データの入力と出力\)** カテゴリにあります。

2. **[データ ソース]** で、 **[HTTP を使用する Web URL]** を選択します。

3. **[URL]** に、読み込むデータが含まれるページの完全な URL を入力するか貼り付けます。

    URL には、読み込むデータを含むページのサイトの URL とファイル名および拡張子を含む完全なパスを含める必要があります。

    たとえば、次のページには、カリフォルニア大学アーバイン校の機械学習リポジトリの Iris データ セットが含まれています。

    `https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data`

4. **[データ形式]** で、サポートされているデータ形式のいずれかをリストから選択します。

    形式を判別するために常に事前にデータを確認することをお勧めします。 カリフォルニア大学アーバイン校のページでは、CSV 形式が使用されています。 他のサポートされているデータ形式は、TSV、ARFF、および SvmLight です。

5. データが CSV または TSV 形式である場合は、 **[File has header row]\(ファイルにヘッダー行が含まれる\)** オプションを使用して、ソース データにヘッダー行が含まれるかどうかを示します。 ヘッダー行は列名の割り当てに使用されます。

6. データが大きく変化しないと予測される場合、または実験を実行するたびにデータを再読み込みしたくない場合は、 **[Use cached results]\(キャッシュされた結果を使用する\)** オプションを選択します。

    このオプションを選択すると、実験ではモジュールが最初に実行されたときにデータが読み込まれ、その後はデータセットのキャッシュされたバージョンが使用されます。

    実験データセットの繰り返しごとにデータセットを再読み込みする場合は、 **[Use cached results]\(キャッシュされた結果を使用する\)** オプションの選択を解除します。 [データのインポート](import-data.md)のパラメーターが変更された場合も、結果が再読み込みされます。

7. 実験を実行します。

## <a name="results"></a>結果

完了したら、出力されたデータセットをクリックし、 **[Visualize]\(視覚化\)** を選択し、データが正常にインポートされたかどうかを確認します。


## <a name="next-steps"></a>次の手順

Azure Machine Learning service で[使用できる一連のモジュール](module-reference.md)を参照してください。 