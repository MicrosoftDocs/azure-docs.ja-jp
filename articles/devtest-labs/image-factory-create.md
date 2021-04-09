---
title: Azure DevTest Labs でイメージ ファクトリを作成する | Microsoft Docs
description: この記事では、Git リポジトリ (Azure DevTest Labs) にあるサンプル スクリプトを使用してカスタム イメージ ファクトリを設定する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 14c8338d6a5ae21847da3a9c774ea6dcdac33ae4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "85482073"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Azure DevTest Labs でカスタム イメージ ファクトリを作成する
この記事では、[Git リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)にあるサンプル スクリプトを使用してカスタム イメージ ファクトリを設定する方法について説明します。

## <a name="whats-an-image-factory"></a>イメージ ファクトリについて
イメージ ファクトリはコードとしての構成ソリューションです。イメージを定期的に、すべての必要な構成と共に、ビルドして配布します。 イメージ ファクトリ内のイメージは常に最新の状態であり、一度プロセス全体が自動化されると、継続的なメンテナンスはほぼゼロとなります。 また、必要な構成がすべてイメージ内に存在するため、ベース OS で VM が作成された後にシステムを手動で構成する時間を節約できます。

カスタム イメージを使用すると、より迅速に開発者のデスクトップを DevTest Labs で準備完了状態にすることができます。 カスタム イメージの欠点は、ラボで管理するものが増えるということです。 たとえば、製品の評価版の有効期限切れ、新しくリリースされたセキュリティ更新プログラムの未適用などに備えて、カスタム イメージを定期的に更新する必要があります。 イメージ ファクトリを使用すると、イメージの定義をソース コード管理にチェックインし、自動化されたプロセスでカスタム イメージをその定義に基づいて生成することができます。

このソリューションにより、カスタム イメージからの仮想マシンの迅速な作成が可能となり、同時に継続的なメンテナンス コストの追加発生を回避することができます。 このソリューションを使用すると、カスタム イメージの作成、他の DevTest Labs への配布、古いイメージの削除を自動的に行うことができます。 次のビデオでは、イメージ ファクトリについて説明し、また DevTest Labs を使用した実装方法についても説明します。  すべての Azure Powershell スクリプトは、[https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory)で無料で入手することができます。

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>ソリューションの概要
このソリューションにより、カスタム イメージからの仮想マシンの迅速な作成が可能となり、同時に継続的なメンテナンス コストの追加発生を回避することができます。 このソリューションを使用すると、自動的にカスタム イメージを作成し、他の DevTest Labs に配布することができます。 Azure DevOps (旧称 Visual Studio Team Services) は、DevTest Labs での全操作の自動化のためのオーケストレーション エンジンとして使用します。

![ソリューションの概要](./media/create-image-factory/high-level-view-of-solution.png)

[DevTest Labs 用の VSTS 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)を使用すると、次の各手順を行うことができます。

- カスタム イメージを作成する
- VM を作成する
- VM を削除する
- 環境を作成する
- 環境を削除する
- 環境を設定する

DevTest Labs の拡張機能を使用することは、DevTest Labs でのカスタム イメージの自動作成を開始するための簡単な方法です。

より複雑なシナリオの場合、PowerShell スクリプトを代わりに使用して実装することもできます。 PowerShell を使用すると、イメージ ファクトリを DevTest Labs に基づいて完全に自動化し、継続的インテグレーションと継続的デリバリー (CI/CD) ツールチェーンで使用することができます。 この代替ソリューションで使用されている原則は次のとおりです。

- 一般的な更新によりイメージ ファクトリの変更が必要になることはありません  (たとえば、新しい種類のカスタム イメージの追加、古いイメージの自動削除、カスタム イメージを受信するための新しい ‘エンドポイント’ DevTest Labs の追加など)
- 一般的な変更はソース コード管理 (コードとしてのインフラストラクチャ) でサポートされます
- DevTest Labs が受け取るカスタム イメージは、同じ Azure サブスクリプション (ラボにまたがるサブスクリプション) にない場合があります
- 必要に応じて追加のファクトリを作成できるよう、PowerShell スクリプトは再利用可能である必要があります

## <a name="next-steps"></a>次のステップ
このセクションの次の記事「[Azure DevOps からイメージ ファクトリを実行する](image-factory-set-up-devops-lab.md)」に進んでください
