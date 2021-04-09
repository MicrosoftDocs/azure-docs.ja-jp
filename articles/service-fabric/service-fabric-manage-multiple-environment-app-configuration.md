---
title: 複数の環境向けのアプリの管理
description: Azure Service Fabric アプリケーションは、1 台から数千台のコンピューターで構成されたさまざまな規模のクラスターで実行できます。 場合によっては、このようなさまざまな環境に合わせて異なる方法でアプリケーションを構成したい場合があります。 この記事では、環境ごとに異なるアプリケーション パラメーターを定義する方法について説明します。
ms.topic: conceptual
ms.date: 02/23/2018
ms.openlocfilehash: c907540c03788ab5f4087a96e301f18ab7ced4ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98787979"
---
# <a name="manage-applications-for-multiple-environments"></a>複数の環境向けのアプリケーションの管理

Azure Service Fabric クラスターは、1 台から数千台のコンピューターを使って作成することができます。 ほとんどの場合、ローカル開発クラスター、共有開発クラスター、運用クラスターなど、複数のクラスター構成にアプリケーションを展開する必要があります。 これらのクラスターはすべて、その中でコードを実行する必要がある異なる環境と見なされます。 アプリケーション バイナリは変更しなくてもこのようなさまざまな環境で実行できますが、通常は、アプリケーションの構成は変える必要があります。

2 つの簡単な例を次に示します。
  - サービスは定義されたポートでリッスンしますが、環境によってそのポートを変える必要があります
  - 環境ごとにデータベースに対して異なるバインド資格情報を指定する必要があります

## <a name="specifying-configuration"></a>構成の指定

指定する構成は、2 つのカテゴリに分類できます。

- サービスの実行方法に適用される構成
  - たとえば、エンドポイントのポート番号や、サービスのインスタンスの数など
  - この構成は、アプリケーションまたはサービスのマニフェスト ファイルで指定します
- アプリケーションのコードに適用される構成
  - たとえば、データベースのバインド情報
  - この構成は、構成ファイルまたは環境変数で指定できます

> [!NOTE]
> アプリケーションおよびサービスのマニフェスト ファイルの属性の中には、パラメーターをサポートしていないものがあります。
> そのような場合は、展開ワークフローの一部として置換文字列に依存する必要があります。 Azure DevOps では、置換トークン (https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens ) のような拡張機能を使用することができます。また、Jenkins では、スクリプト タスクを実行して値を置き換えることができます。
>

## <a name="specifying-parameters-during-application-creation"></a>アプリケーション作成時のパラメーターの指定

Service Fabric で名前付きアプリケーション インスタンスを作成するときは、パラメーターで渡すことができます。 その方法は、アプリケーション インスタンスの作成方法によって異なります。

  - PowerShell では、[`New-ServiceFabricApplication`](/powershell/module/servicefabric/new-servicefabricapplication) コマンドレットはハッシュテーブルとしてアプリケーション パラメーターを受け取ります。
  - sfctl では、[`sfctl application create`](./service-fabric-sfctl-application.md#sfctl-application-create) コマンドは JSON 文字列としてパラメーターを受け取ります。 install.sh スクリプトは sfctl を使います。
  - Visual Studio では、アプリケーション プロジェクトの Parameters フォルダーにパラメーター ファイルのセットが用意されています。 これらのパラメーター ファイルは、Azure DevOps Services または Azure DevOps Server を使って、Visual Studio から発行するときに使われます。 Visual Studio では、パラメーター ファイルは Deploy-FabricApplication.ps1 スクリプトに渡されます。

## <a name="next-steps"></a>次のステップ
以下の記事では、ここで説明したいくつかの概念の使い方が説明されています。

- [Service Fabric のサービス用に環境変数を指定する方法](service-fabric-how-to-specify-environment-variables.md)
- [Service Fabric でパラメーターを使用してサービスのポート番号を指定する方法](service-fabric-how-to-specify-port-number-using-parameters.md)
- [構成ファイルをパラメーター化する方法](service-fabric-how-to-parameterize-configuration-files.md)

- [環境変数リファレンス](service-fabric-environment-variables-reference.md)
