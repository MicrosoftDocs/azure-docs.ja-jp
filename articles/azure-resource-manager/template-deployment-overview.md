---
title: Azure Resource Manager のテンプレート
description: リソースのデプロイに Azure Resource Manager テンプレートを使用する方法を説明します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: tomfitz
ms.openlocfilehash: 6d0d162f0f6f3024f6b4b63b8df1df9fd413afc8
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965311"
---
# <a name="azure-resource-manager-templates"></a>Azure Resource Manager のテンプレート

クラウドに移行するとき、多くのチームでアジャイル開発方法が採用されてきました。 このようなチームでは反復が迅速に行われます。 ソリューションをクラウドに繰り返しデプロイし、インフラストラクチャが信頼できる状態であることを把握する必要があります。 インフラストラクチャが反復的なプロセスの一部になると、運用と開発の間の区切りはなくなります。 チームは、統合されたプロセスを通してインフラストラクチャとアプリケーション コードを管理する必要があります。

これらの課題に対応するには、デプロイを自動化し、インフラストラクチャの手法をコードとして使用することができます。 コードでは、デプロイする必要があるインフラストラクチャを定義します。 インフラストラクチャのコードは、プロジェクトの一部になります。 アプリケーションのコードと同様に、インフラストラクチャのコードをソース リポジトリに格納し、そのバージョンを管理します。 チーム内の誰でもコードを実行して、同様の環境をデプロイできます。

Azure ソリューション用にインフラストラクチャをコードとして実装するには、Azure Resource Manager テンプレートを使用します。 テンプレートは JavaScript Object Notation (JSON) ファイルであり、プロジェクトのインフラストラクチャと構成が定義されています。 このテンプレートでは、デプロイしようとしているものを、それを作成する一連のプログラミング コマンドを記述しなくても記述できる、宣言型の構文を使用しています。 このテンプレートでは、デプロイするリソースとそれらのリソースのプロパティを指定します。

## <a name="why-choose-resource-manager-templates"></a>Resource Manager テンプレートを選択する理由とは?

Resource Manager テンプレートを使用するか、他のいずれかのコードとしてのインフラストラクチャ サービスを使用するか決めるとき、テンプレートを使用した場合の以下の長所について検討してください。

* **宣言型の構文**:Resource Manager テンプレートを使用すると、Azure インフラストラクチャ全体を宣言方式で作成し、デプロイできます。 たとえば、仮想マシンだけでなく、ネットワーク インフラストラクチャ、ストレージ システム、場合によっては必要となるその他のリソースもデプロイできます。

* **反復可能結果**:インフラストラクチャを開発のライフサイクル全体で繰り返しデプロイし、常に一貫性のある方法でリソースをデプロイします。 テンプレートはべき等です。つまり、同じテンプレートを何度でもデプロイし、同じリソースの種類を同じ状態で作成できます。 更新を提示する個別のテンプレートをたくさん開発するのではなく、望ましい状態を提示するテンプレートを 1 つ開発できます。

* **オーケストレーション**:複雑な順序付けのことを心配する必要はありません。 Resource Manager では、相互に依存するリソースのデプロイが調整され、正しい順序で作成されます。 Resource Manager では、可能な箇所でリソースが並列デプロイされます。そのため、直列デプロイに比べ、短時間でデプロイが完了します。 テンプレートは複数の命令コマンドではなく、1 つのコマンドを使用してデプロイします。

   ![テンプレートのデプロイの比較](./media/template-deployment-overview/template-processing.png)

* **組み込みの検証**:テンプレートは検証に合格しないとデプロイされません。 Resource Manager によって、デプロイ開始前にテンプレートが調べられ、デプロイが成功することが確認されます。 デプロイが半分完了した状態で停止する可能性が低くなります。

* **モジュール式のファイル**:テンプレートは再利用可能な構成部品に細分化し、デプロイ時にリンクさせることができます。 あるテンプレートを別のテンプレート内の入れ子にすることもできます。

* **Azure リソースの作成**:テンプレートでは、Azure の新しいサービスと機能をすぐに利用できます。 リソース プロバイダーによって新しいリソースが導入されたらすぐに、テンプレートを使用してこれらのリソースをデプロイできます。 新しいサービスは、ツールやモジュールが更新されるまで待たなくても使用することができます。

* **デプロイの追跡**:Azure portal でデプロイ履歴を確認し、テンプレートのデプロイに関する情報を取得できます。 デプロイされたテンプレート、渡されたパラメーター値、およびすべての出力値を確認できます。 他のコードとしてのインフラストラクチャ サービスは、ポータルでは追跡されません。

   ![デプロイ履歴](./media/template-deployment-overview/deployment-history.png)

* **コードとしてのポリシー**:[Azure Policy](../governance/policy/overview.md) は、ガバナンスを自動化するためのコード フレームワークとしてのポリシーです。 Azure ポリシーを使用している場合、テンプレート経由でデプロイすると、非準拠リソースではポリシーが修復されます。

* **デプロイのブループリント**:Microsoft が提供する[ブループリント](../governance/blueprints/overview.md)を活用することで、規制やコンプライアンスの基準を満たすことができます。 これらのブループリントには、さまざまなアーキテクチャ用の既成のテンプレートが含まれています。

* **エクスポート可能なコード**:リソース グループの現在の状態をエクスポートするか、特定のデプロイに使用されたテンプレートを表示することで、既存のリソース グループのテンプレートを取得できます。 [エクスポートしたテンプレート](export-template-portal.md)を表示すると、テンプレートの構文について理解するのに役立ちます。

* **作成ツール**:[Visual Studio Code](resource-manager-tools-vs-code.md) とテンプレート ツール拡張機能でテンプレートを作成できます。 Intellisense、構文の強調表示、インライン ヘルプ、その他の多くの言語機能が与えられます。

## <a name="template-file"></a>テンプレート ファイル

テンプレート内では、JSON の機能を拡張する[テンプレート式](template-expressions.md)を記述できます。 これらの式では、Resource Manager によって提供される[関数](resource-group-template-functions.md)を使用します。

テンプレートには次のセクションがあります。

* [パラメーター](template-parameters.md) - 同じテンプレートを異なる環境で使用できるように、デプロイ時に値を指定します。

* [変数](template-variables.md) - テンプレートで再利用される値を定義します。 パラメーター値から構築できます。

* [ユーザー定義関数](template-user-defined-functions.md) - テンプレートを簡略化するカスタマイズされた関数を作成します。

* [リソース](resource-group-authoring-templates.md#resources) - デプロイするリソースを指定します。

* [出力](template-outputs.md) - デプロイされたリソースから値を返します。

## <a name="template-deployment-process"></a>テンプレートのデプロイ手順

テンプレートをデプロイするときは、Resource Manager によってテンプレートが REST API 操作に変換されます。 たとえば、Resource Manager が次のリソース定義を含むテンプレートを受け取ったとします。

```json
"resources": [
  {
    "apiVersion": "2016-01-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {
    }
  }
]
```

Resource Manager は、この定義を次の REST API 操作に変換し、Microsoft.Storage リソース プロバイダーに送信します。

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "properties": {
  }
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage"
}
```

## <a name="template-design"></a>テンプレートの設計

テンプレートとリソース グループをどのように定義するかは、ソリューションの管理方法に応じてユーザーが自由に決定できます。 たとえば、1 つのリソース グループに 1 つのテンプレートで 3 層のアプリケーションをデプロイできます。

![3 層のテンプレート](./media/template-deployment-overview/3-tier-template.png)

ただし、インフラストラクチャ全体を 1 つのテンプレートで定義する必要はありません。 多くの場合、対象を絞って目的を特化した一連のテンプレートにデプロイの要件を分類することが合理的です。 これらのテンプレートは、さまざまなソリューションで簡単に再利用できます。 特定のソリューションをデプロイするには、必要なすべてのテンプレートをリンクするマスター テンプレートを作成します。 次の画像は、入れ子になった 3 つのテンプレートを含む親テンプレートを利用して 3 層のソリューションをデプロイする方法を示しています。

![入れ子になったテンプレートの層](./media/template-deployment-overview/nested-tiers-template.png)

層ごとに異なるライフサイクルを希望する場合は、3 層を個別のリソース グループにデプロイできます。 リソースは、他のリソース グループ内のリソースにリンクされることもあります。

![テンプレートの層](./media/template-deployment-overview/tier-templates.png)

入れ子になったテンプレートについては、「[Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)」を参照してください。

## <a name="next-steps"></a>次の手順

* テンプレート ファイルのプロパティについては、「[Azure Resource Manager テンプレートの構造と構文の詳細](resource-group-authoring-templates.md)」をご覧ください。
* あるリソースが別のリソースの前にデプロイされるよう、依存関係を明示的に設定するには、[Azure Resource Manager テンプレートで依存関係を定義する](resource-group-define-dependencies.md)方法に関するページを参照してください。
* テンプレートにリソースを追加し、必要に応じてデプロイすることができます。 詳しくは、「[Resource Manager テンプレートでの条件付きデプロイ](conditional-resource-deployment.md)」をご覧ください。
* テンプレートで JSON のブロックを何度も繰り返すのではなく、変数、プロパティ、またはリソースの複数のインスタンスを指定できます。 詳しくは、「[Azure Resource Manager テンプレートでのリソース、プロパティ、または変数の反復](resource-group-create-multiple.md)」をご覧ください。
* テンプレートのエクスポートの詳細については、「[クイックスタート:Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ](./resource-manager-quickstart-create-templates-use-the-portal.md)」を参照してください。
