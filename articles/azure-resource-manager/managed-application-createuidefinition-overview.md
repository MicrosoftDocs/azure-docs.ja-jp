---
title: "Azure マネージ アプリケーションに使う UI 定義の作成について | Microsoft Docs"
description: "Azure マネージ アプリケーションに使う UI 定義の作成方法について説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 176b891538f85c5638a2321561c3d8bd377d245b
ms.contentlocale: ja-jp
ms.lasthandoff: 05/11/2017

---
# <a name="getting-started-with-createuidefinition"></a>CreateUiDefinition の基本概念
このドキュメントでは、CreateUiDefinition の基本的な概念について取り上げます。CreateUiDefinition は、マネージ アプリケーション作成用のユーザー インターフェイスが Azure Portal によって生成される際に使われます。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json",
   "handler": "Microsoft.Compute.MultiVm",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { }
   }
}
```

CreateUiDefinition には必ず、次の 3 つのプロパティが存在します。 

* handler
* version
* parameters

マネージ アプリケーションでは、handler は常に `Microsoft.Compute.MultiVm` とする必要があります。また、サポートされている最新のバージョンは `0.1.2-preview` です。

parameters プロパティのスキーマは、指定した handler と version の組み合わせによって異なります。 マネージ アプリケーションでサポートされているプロパティは `basics`、`steps`、`outputs` です。 basics プロパティと steps プロパティには、Azure Portal に表示される "_要素_" (テキスト ボックス、ドロップダウンなど) が指定されます。 outputs プロパティは、指定された要素の出力値を Azure Resource Manager デプロイ テンプレートのパラメーターに対してマッピングする目的で使われます。

`$schema` は、指定することが推奨されますが、必須ではありません。 指定する場合は、`$schema` の URI 内のバージョンと `version` の値とを一致させる必要があります。

## <a name="basics"></a>基本
Azure Portal によって CreateUiDefinition が解析されたときに生成されるウィザードの最初のステップは必ず、basics ステップになります。 Azure Portal は、`basics` に指定された要素を表示することに加え、デプロイに使用するサブスクリプション、リソース グループ、場所をユーザーが選択するための要素を挿入します。 一般に、デプロイ全体にかかわるパラメーター (クラスターの名前、管理者の資格情報など) の入力をユーザーに求めるための要素は、このステップに追加する必要があります。

ユーザーのサブスクリプション、リソース グループ、または場所によって動作が異なるような要素は、basics では使用できません。 たとえば **Microsoft.Compute.SizeSelector** で利用可能な一連のサイズは、ユーザーのサブスクリプションや場所に応じて決まります。 そのため、**Microsoft.Compute.SizeSelector** は steps の中でしか使うことができません。 一般に、basics で使うことができるのは、**Microsoft.Common** 名前空間の要素だけです。 ただしそれ以外の名前空間 (**Microsoft.Compute.Credentials** など) であっても、ユーザーのコンテキストに依存しない一部の要素については使うことができます。

## <a name="steps"></a>手順
basics の後に表示するステップ (0 個以上) は steps プロパティに追加することができ、各ステップに少なくとも 1 つの要素を記述します。 デプロイするアプリケーションのロールまたはレベルごとにステップを追加することを検討してください。 たとえば、クラスターのマスター ノードとワーカー ノードとで分けて入力ステップを追加します。

## <a name="outputs"></a>出力

`outputs` プロパティは、Azure Portal が `basics` と `steps` の要素を Azure Resource Manager デプロイ テンプレートのパラメーターにマッピングする際に使われます。 このディクショナリのキーはテンプレート パラメーターの名前で、ディクショナリの値は、参照された要素の出力オブジェクトのプロパティです。

## <a name="functions"></a>関数
CreateUiDefinition は、Azure Resource Manager の[テンプレート関数](resource-group-template-functions.md)と似た構文と機能を備えており、要素の入力と出力を操作するための関数のほか、条件文などの機能が用意されています。

## <a name="next-steps"></a>次のステップ
CreateUiDefinition には、それ自体に単純なスキーマが存在します。 その本質は、サポートされているすべての要素と機能に深く結び付いており、次のドキュメントで、それらが詳しく解説されています。

- [要素](managed-application-createuidefinition-elements.md)
- [関数](managed-application-createuidefinition-functions.md)

現在 CreateUiDefinition に使われている JSON スキーマは、https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json で入手できます。 

それ以降のバージョンも、同じ場所で提供される予定です。 URL の `0.1.2-preview` の部分と `version` の値は、実際に使うバージョン識別子に置き換えてください。 現在サポートされているバージョン識別子は `0.0.1-preview`、`0.1.0-preview`、`0.1.1-preview`、`0.1.2-preview` です。
