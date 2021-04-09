---
title: 安全なコードのベスト プラクティス
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 向けの開発時に遭遇する可能性のあるセキュリティ上の脅威、軽減策、ベスト プラクティスについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.date: 11/12/2019
ms.openlocfilehash: 37cb70bdbd1e3c87eeb994e0959c6214822d22ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93322973"
---
# <a name="secure-code-best-practices-with-azure-machine-learning"></a>Azure Machine Learning での安全なコードのベスト プラクティス

Azure Machine Learning では、あらゆるソースのファイルとコンテンツを Azure にアップロードすることができます。 読み込んだ Jupyter ノートブックやスクリプトの内容によって、セッションのデータが読み取られたり、Azure にある組織内のデータがアクセスされたり、ユーザーに代わって悪質なプロセスが実行されたりする可能性があります。

> [!IMPORTANT]
> 実行するノートブックやスクリプトは、信頼できるソースのものに限定してください。 たとえば、皆さんやそのセキュリティ チームによって確認されたノートブックまたはスクリプトが該当します。

## <a name="potential-threats"></a>潜在的な脅威

Azure Machine Learning を使用した開発は、多くの場合、Web ベースの開発環境 (Notebooks や Azure ML Studio) を伴います。 Web ベースの開発環境を使用する際に考えられる脅威として、次のようなものがあります。

* [クロスサイト スクリプティング (XSS)](https://owasp.org/www-community/attacks/xss/)

    * __DOM インジェクション__: このタイプの攻撃では、ブラウザーに表示される UI が改変される可能性があります。 たとえば、Jupyter Notebook で実行ボタンを押したときの動作が変更されます。
    * __アクセス トークンと Cookie__: XSS 攻撃は、ローカル記憶域やブラウザーの Cookie にアクセスすることもできます。 Azure Active Directory (AAD) の認証トークンはローカル記憶域に格納されます。 XSS 攻撃は、このトークンを使用することにより、皆さんに代わって API 呼び出しを実行した後、外部システムや API にデータを送信します。

* [クロスサイト リクエスト フォージェリ (CSRF)](https://owasp.org/www-community/attacks/csrf): 画像やリンクの URL が、この攻撃によって、悪意のあるスクリプトや API の URL に置き換えられることがあります。 画像が読み込まれるか、リンクがクリックされると、その URL に対する呼び出しが実行されます。

## <a name="azure-ml-studio-notebooks"></a>Azure ML Studio ノートブック

Azure Machine Learning Studio は、ブラウザーでホストされるノートブック環境を備えています。 ノートブックのセルから、悪質なコードを含んだ HTML ドキュメントや HTML フラグメントが出力される可能性があります。  出力がレンダリングされたときにそのコードが実行されるおそれがあります。

__考えられる脅威__:
* クロスサイト スクリプティング (XSS)
* クロスサイト リクエスト フォージェリ (CSRF)

__Azure Machine Learning によって提供されている軽減策__:
* __コード セルの出力__ は、iframe 内にサンドボックス化されています。 iframe によってスクリプトは、親 DOM や Cookie、セッション ストレージにはアクセスできないようになっています。
* __Markdown セル__ の内容は、dompurify ライブラリを使用して消去されます。 そうすることで、マークダウン セルがレンダリングされて悪質なスクリプトが実行されるのを防止しています。
* __画像の URL__ と __Markdown リンク__ は、Microsoft が所有するエンドポイントに送信され、そこで悪質な値がないかチェックされます。 悪質な値が検出された場合、その要求はエンドポイントによって拒否されます。

__推奨アクション__:
* Studio にアップロードするファイルは、事前にその内容が信頼できることを確認します。 アップロードしようとしているのが信頼のおけるファイルであることを確認する必要があります。
* 外部アプリケーションを開くリンクを選択すると、そのアプリケーションを信頼するように求められます。

## <a name="azure-ml-compute-instance"></a>Azure ML のコンピューティング インスタンス

Azure Machine Learning コンピューティング インスタンスは、__Jupyter__ と __Jupyter Lab__ をホストしています。 そのどちらかを使用しているとき、ノートブックのセルまたはコードから、悪質なコードを含んだ HTML ドキュメントや HTML フラグメントが出力される可能性があります。 出力がレンダリングされたときにそのコードが実行されるおそれがあります。 コンピューティング インスタンスでホストされている __RStudio__ を使用している場合にも、同じ脅威が当てはまります。

__考えられる脅威__:
* クロスサイト スクリプティング (XSS)
* クロスサイト リクエスト フォージェリ (CSRF)

__Azure Machine Learning によって提供されている軽減策__:
* [なし] : Jupyter と Jupyter Lab は、Azure Machine Learning コンピューティング インスタンスをホストとするオープンソース アプリケーションです。

__推奨アクション__:
* Studio にアップロードするファイルは、事前にその内容が信頼できることを確認します。 アップロードしようとしているのが信頼のおけるファイルであることを確認する必要があります。

## <a name="report-security-issues-or-concerns"></a>セキュリティの問題や懸念事項をレポートする 

Azure Machine Learning は、Microsoft Azure 報奨金プログラムの対象となっています。 詳細については、 [https://www.microsoft.com/msrc/bounty-microsoft-azure](https://www.microsoft.com/msrc/bounty-microsoft-azure) を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Machine Learning のエンタープライズ セキュリティ](concept-enterprise-security.md)