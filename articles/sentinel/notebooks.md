---
title: Azure Sentinel プレビューのノートブックを使用した検出機能 | Microsoft Docs
description: この記事では、Azure Sentinel 検出機能でノートブックを使用する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 63ce2be847017ed7e80fe5e573d5553311f6af2f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58107680"
---
# <a name="use-notebooks-to-hunt-for-anomalies"></a>ノートブックを使用した異常検出

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Sentinel では、ご利用の環境内のセキュリティ異常を調査または検出するために、Jupyter の対話型ノートブックの能力を活用して、分析情報とアクションを提供します。 Azure Sentinel には、Microsoft のセキュリティ アナリストによって開発されたノートブックがプリロードされています。 各ノートブックは、特定のユース ケース向けに自己完結型のワークフローの目的で作成されたものです。 ノートブックにはそれぞれ、より高速なデータ探索と脅威検出のために、視覚化が含まれています。 必要に応じて組み込みのノートブックをカスタマイズしたり、最初から新しいノートブックを作成したり、Azure Sentinel の GitHub コミュニティからノートブックをインポートしたりします。 Jupyter ノートブックは Azure Notebooks ページにプロジェクトとしてインポートされます。これにより、ユーザーは 1 か所からすべての Azure Sentinel ノートブックにアクセスできます。 ノートブックは、ボタンをクリックして実行することも、環境に合わせてユーザーが構成することもできます。

完全に統合されたエクスペリエンスによって、基になるメンテナンスのオーバーヘッドなしで、ノートブックによってクラウド コンピューティングとストレージ上で実行できます。 既存の Jupyter Notebook エコシステムを活用する機能では、顧客データを共有することなく、ソース モデルを集めることができます。 


各ノートブックは、Azure クラウドの対話型の開発環境である Azure Notebooks (現在、プレビュー段階) でホストされます。 ノートブックには常にアクセスすることができ、世界中のどこからでも任意のブラウザーからアクセスできます。  調査と検出用の Azure Sentinel の組み込みノートブックは、属しているプロジェクトに複製され、これはご利用の環境に合わせて変更し、調整することができます。 最も人気のある組み込みのノートブックには、次のようなものがあります。

- **アラートの調査と検出**: この対話型ノートブックによって、関連するアクティビティを取得し、生成されたアラートから関連付けられたアクティビティとデータでアラートを改良することで、異なるクラスのアラートの迅速な優先順位付けを可能にします。

- **エンドポイント ホストのガイド付き検出**: エンドポイント ホストと関連付けられているセキュリティ関連アクティビティにドリルダウンすることで、セキュリティ侵害の兆候を検出できます。  

- **Office サインインのガイド付き検出**: 疑いがあるログの地域を視覚化し、Office 365 データから派生した異常なサインイン パターンを表示することで、不審なサインインを検出できます。

## <a name="run-a-notebook"></a>ノートブックの実行
次の例では、場所の異常を深く探求するために組み込みのノートブックを実行して、予期しない場所にいる任意のユーザーがご利用のネットワーク上で操作を行っていないかどうかを確認します。

1. Azure Sentinel ポータルで **[Notebooks]** をクリックして、組み込みのノートブックのいずれかを選択します。
  
   ![ノートブックを選択する](./media/notebooks/select-notebook.png)

3. **[インポート]** をクリックして、GitHub リポジトリの複製を自分の Azure Notebooks プロジェクトに作成します。
   ![ノートブックをインポートする](./media/notebooks/import1.png)
4. 各ノートブックでは、検出または調査を実行するための手順を紹介します。 モデル、ライブラリ、Azure Sentinel の接続用のその他の依存関係と構成は、ワンクリック実行を有効にするように自動的にインポートされます。 ノートブックの実行に必要なコードとライブラリはすべてプリロードされています。 構成することなく、ご利用の Log Analytics ワークスペースに対してすぐにノートブックの実行を開始できます。

   ![リポジトリをインポートする](./media/notebooks/import2.png)

5. 提供されているサンプル ノートブックをすべて探索、変更、実行します。 これらは、多くの異なるシナリオ用にブロックを作成するときに使用できます。

   ![ノートブックを選択する](./media/notebooks/import3.png)



## <a name="next-steps"></a>次の手順
この記事では、Azure Sentinel でノートブックを使用して検出調査を実行する方法を学習しました。 Azure Sentinel の詳細については、以下の記事を参照してください。

- [脅威を事前に検出する](hunting.md)
- [ブックマークを使用して検出中に関心のある情報を保存する](bookmarks.md)