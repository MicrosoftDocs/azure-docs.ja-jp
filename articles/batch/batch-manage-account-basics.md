---
title: アカウントの管理
description: Azure Batch アカウントを構成するものについて説明します
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6ef87fab099c6ff7402b351664ee15a198d69a4b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "83722968"
---
# <a name="manage-your-batch-account"></a>Batch アカウントの管理

Batch アカウントは、Batch サービス内で一意に識別されるエンティティです。 すべての処理は、Batch アカウントに関連付けられています。

Azure Batch アカウントは、[Azure Portal](batch-account-create-portal.md) またはプログラム ([Batch Management .NET ライブラリ](batch-management-dotnet.md)など) を使用して作成できます。 アカウントを作成する際に、ジョブに関する入出力データまたはアプリケーションの格納に使用する、Azure ストレージ アカウントを関連付けることができます。

1 つの Batch アカウントで複数の Batch ワークロードを実行することも、同じサブスクリプションで異なる Azure リージョンの複数の Batch アカウントにワークロードを分散することもできます。

## <a name="components-of-the-batch-account"></a>Batch アカウントのコンポーネント

Batch アカウントを使用すると、大規模な並列コンピューティングやハイパフォーマンス コンピューティング (HPC) のバッチ ジョブを Azure で効率的に実行することができます。 アカウント内で、以下のものを管理します。

- 実行中のアプリケーション

- プールとプール内のノードの割り当て

- タスクの数と種類 

- データの入力と出力。 タスクを管理するために追加のソフトウェアをインストールする必要はありません。

- Batch アカウントを作成するとき、アカウントに名前を割り当てるよう求められます。 この名前はアカウントの ID であり、割り当てた後は変更できません。

- アカウントの名前を変更するには、アカウントを削除して新しい Batch アカウントを作成する必要があります。

- このアカウントは、使用するサブスクリプション内に作成されます。

- このアカウントを使用して、サブスクリプション内の任意の Batch アカウントから、プライマリおよびセカンダリのアカウント キーを識別および取得します。

- このアカウントは、プールの割り当ておよびコアのクォータに関する情報を保持します。  

- このアカウントには位置情報が含まれます。

- このアカウントは、お客様のストレージ アカウントを識別します。

## <a name="next-steps"></a>次のステップ

- [Azure portal](batch-account-create-portal.md) を使用して Batch アカウントを作成します。
- [Batch Management .NET ライブラリ](batch-management-dotnet.md)を使用するなどして、プログラムから Batch アカウントを作成します。
- [Azure Batch プールの計算ノードへのリモート アクセスを構成または無効化します](pool-endpoint-configuration.md)。
- [Batch 計算ノードでジョブ準備タスクとジョブ解放タスクを実行します](batch-job-prep-release.md)

