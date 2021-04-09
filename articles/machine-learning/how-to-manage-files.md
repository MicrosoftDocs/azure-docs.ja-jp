---
title: ワークスペース内のファイルの作成と管理
titleSuffix: Azure Machine Learning
description: Azure Machine Learning スタジオで自分のワークスペース内のファイルを作成および管理する方法について説明します。
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: 474b3123513e4b8acf19ba9cdb42c3384ea3ced2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100101051"
---
# <a name="how-to-create-and-manage-files-in-your-workspace"></a>ワークスペース内のファイルを作成および管理する方法

Azure Machine Learning ワークスペース内のファイルを作成および管理する方法について説明します。  これらのファイルは、既定のワークスペース ストレージに格納されます。 ファイルとフォルダーは、ワークスペースに対する読み取りアクセス権を持つ任意のユーザーと共有でき、ワークスペース内の任意のコンピューティング インスタンスから使用できます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://aka.ms/AMLFree) を作成してください。
* Machine Learning ワークスペース。 [Azure Machine Learning ワークスペースを作成する](how-to-manage-workspace.md)方法に関するページを参照してください。

## <a name="create-files"></a><a name="create"></a>ファイルの作成

既定のフォルダー (`Users > yourname`) に新しいファイルを作成するには、次のようにします。

1. [Azure Machine Learning Studio](https://ml.azure.com) で、ワークスペースを開きます。
1. 左側にある **[Notebooks]\(ノートブック\)** を選択します。
1. [ **+** ] イメージを選択します。
1. **[Create new file]\(新しいファイルの作成\)** イメージを選択します。

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="[新しいファイルの作成]":::

1. ファイルに名前を付けます。
1. ファイルの種類を選択します。
1. **［作成］** を選択します

ノートブックとほとんどのテキスト ファイルの種類はプレビュー セクションに表示されます。  その他のほとんどのファイルの種類にはプレビューがありません。

別のフォルダーに新しいファイルを作成するには、次のようにします。
1. フォルダーの末尾にある "..." を選択します。
1. **[Create new file]\(新しいファイルの作成\)** を選択します。

> [!IMPORTANT]
> ノートブックやスクリプトの内容によっては、Azure の組織なしでセッションからデータを読み取ったり、データにアクセスしたりできる可能性があります。  信頼できるソースのファイル以外は読み込まないでください。 詳細については、[ソース コードのベスト プラクティス](concept-secure-code-best-practice.md#azure-ml-studio-notebooks)に関する記事をご覧ください。

## <a name="manage-files-with-git"></a>Git を使用したファイルの管理

[コンピューティング インスタンス ターミナルを使用](how-to-access-terminal.md#git)して、Git リポジトリの複製と管理を行います。

## <a name="clone-samples"></a>サンプルを複製する

ワークスペースには **[サンプル ノートブック]** フォルダーがあり、SDK を探索するために役立ち、独自の機械学習プロジェクトの例として利用できるように設計されたノートブックが格納されています。   これらのノートブックを独自のフォルダーに複製し、実行したり編集したりできます。  

例については、「[Tutorial:初めての ML 実験を作成する](tutorial-1st-experiment-sdk-setup.md#azure)」を参照してください。

## <a name="share-files"></a>ファイル共有

ファイルを共有するには、URL をコピーして貼り付けます。  この URL にアクセスできるのは、ワークスペースの他のユーザーだけです。  詳細については、[ワークスペースへのアクセスの許可](how-to-assign-roles.md)に関する記事を参照してください。

## <a name="delete-a-file"></a>ファイルを削除する

**サンプル ノートブック** ファイルは削除 "*できません*"。  このファイルはスタジオの一部であり、新しい SDK が発行されるたびに更新されます。  

**[ファイル]** セクションにあるファイルは、次のいずれかの方法で "*削除*" できます。

* Studio で、フォルダーまたはファイルの末尾にある **[...]** を選択します。  必ずサポートされているブラウザー (Microsoft Edge、Chrome、または Firefox) を使用してください。
* ワークスペース内の任意のコンピューティング インスタンスから[ターミナルを使用](how-to-access-terminal.md)します。 フォルダー **~/cloudfiles** は、ワークスペース ストレージ アカウントのストレージにマップされています。
* Jupyter または JupyterLab で付属のツールを使用します。
