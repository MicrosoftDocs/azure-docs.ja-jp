---
title: リソース マネージャーのテンプレートの関数 | Microsoft Docs
description: 値の取得、文字列の処理、デプロイ情報の取得のために、Azure リソース マネージャーのテンプレートで使用する関数について説明します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 0644abe1-abaa-443d-820d-1966d7d26bfd
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/08/2019
ms.author: tomfitz
ms.openlocfilehash: b5a1f12a877008a3ce2ff7bd9635b9ed47b379f7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59280317"
---
# <a name="azure-resource-manager-template-functions"></a>Azure Resource Manager テンプレートの関数
この記事では、Azure Resource Manager テンプレートで使用できるすべての関数について説明します。 ご自分のテンプレート内で関数を使用する方法の詳細については、[テンプレートの構文](resource-group-authoring-templates.md#syntax)に関するセクションを参照してください。

独自の関数を作成するには、[ユーザー定義関数](resource-group-authoring-templates.md#functions)に関するページをご覧ください。

<a id="array" />
<a id="coalesce" />
<a id="concatarray" />
<a id="contains" />
<a id="createarray" />
<a id="empty" />
<a id="first" />
<a id="intersection" />
<a id="json" />
<a id="last" />
<a id="length" />
<a id="min" />
<a id="max" />
<a id="range" />
<a id="skip" />
<a id="take" />
<a id="union" />

## <a name="array-and-object-functions"></a>配列とオブジェクトの関数
Resource Manager には、配列とオブジェクトを操作する関数がいくつか用意されています。

* [array](resource-group-template-functions-array.md#array)
* [coalesce](resource-group-template-functions-array.md#coalesce)
* [concat](resource-group-template-functions-array.md#concat)
* [contains](resource-group-template-functions-array.md#contains)
* [createArray](resource-group-template-functions-array.md#createarray)
* [empty](resource-group-template-functions-array.md#empty)
* [first](resource-group-template-functions-array.md#first)
* [intersection](resource-group-template-functions-array.md#intersection)
* [json](resource-group-template-functions-array.md#json)
* [last](resource-group-template-functions-array.md#last)
* [length](resource-group-template-functions-array.md#length)
* [min](resource-group-template-functions-array.md#min)
* [max](resource-group-template-functions-array.md#max)
* [range](resource-group-template-functions-array.md#range)
* [skip](resource-group-template-functions-array.md#skip)
* [take](resource-group-template-functions-array.md#take)
* [union](resource-group-template-functions-array.md#union)

<a id="equals" />
<a id="less" />
<a id="lessorequals" />
<a id="greater" />
<a id="greaterorequals" />

## <a name="comparison-functions"></a>比較関数
Resource Manager には、テンプレートで比較を行うための関数がいくつか用意されています。

* [equals](resource-group-template-functions-comparison.md#equals)
* [less](resource-group-template-functions-comparison.md#less)
* [lessOrEquals](resource-group-template-functions-comparison.md#lessorequals)
* [greater](resource-group-template-functions-comparison.md#greater)
* [greaterOrEquals](resource-group-template-functions-comparison.md#greaterorequals)

<a id="deployment" />
<a id="parameters" />
<a id="variables" />

## <a name="deployment-value-functions"></a>デプロイの値関数
リソース マネージャーには、テンプレートのセクションから値を取得し、デプロイに関連する値を取得する次の関数が用意されています。

* [デプロイ](resource-group-template-functions-deployment.md#deployment)
* [parameters](resource-group-template-functions-deployment.md#parameters)
* [variables](resource-group-template-functions-deployment.md#variables)

<a id="and" />
<a id="bool" />
<a id="if" />
<a id="not" />
<a id="or" />

## <a name="logical-functions"></a>論理関数
リソース マネージャーには、論理の条件を操作する次の関数が用意されています。

* [and](resource-group-template-functions-logical.md#and)
* [bool](resource-group-template-functions-logical.md#bool)
* [if](resource-group-template-functions-logical.md#if)
* [not](resource-group-template-functions-logical.md#not)
* [or](resource-group-template-functions-logical.md#or)

<a id="add" />
<a id="copyindex" />
<a id="div" />
<a id="float" />
<a id="int" />
<a id="minint" />
<a id="maxint" />
<a id="mod" />
<a id="mul" />
<a id="sub" />

## <a name="numeric-functions"></a>数値関数
リソース マネージャーには、整数を操作する次の関数が用意されています。

* [add](resource-group-template-functions-numeric.md#add)
* [copyIndex](resource-group-template-functions-numeric.md#copyindex)
* [div](resource-group-template-functions-numeric.md#div)
* [float](resource-group-template-functions-numeric.md#float)
* [int](resource-group-template-functions-numeric.md#int)
* [min](resource-group-template-functions-numeric.md#min)
* [max](resource-group-template-functions-numeric.md#max)
* [mod](resource-group-template-functions-numeric.md#mod)
* [mul](resource-group-template-functions-numeric.md#mul)
* [sub](resource-group-template-functions-numeric.md#sub)

<a id="listkeys" />
<a id="list" />
<a id="providers" />
<a id="reference" />
<a id="resourcegroup" />
<a id="resourceid" />
<a id="subscription" />

## <a name="resource-functions"></a>リソース関数
リソース マネージャーには、リソース値を取得する次の関数が用意されています。

* [listAccountSas](resource-group-template-functions-resource.md#list)
* [listKeys](resource-group-template-functions-resource.md#listkeys)
* [listSecrets](resource-group-template-functions-resource.md#list)
* [list*](resource-group-template-functions-resource.md#list)
* [providers](resource-group-template-functions-resource.md#providers)
* [reference](resource-group-template-functions-resource.md#reference)
* [resourceGroup](resource-group-template-functions-resource.md#resourcegroup)
* [resourceId](resource-group-template-functions-resource.md#resourceid)
* [サブスクリプション](resource-group-template-functions-resource.md#subscription)

<a id="base64" />
<a id="base64tojson" />
<a id="base64tostring" />
<a id="concat" />
<a id="containsstring" />
<a id="datauri" />
<a id="datauritostring" />
<a id="emptystring" />
<a id="endswith" />
<a id="firststring" />
<a id="guid" />
<a id="indexof" />
<a id="laststring" />
<a id="lastindexof" />
<a id="lengthstring" />
<a id="padleft" />
<a id="replace" />
<a id="skipstring" />
<a id="split" />
<a id="startswith" />
<a id="string" />
<a id="substring" />
<a id="takestring" />
<a id="tolower" />
<a id="toupper" />
<a id="trim" />
<a id="uniquestring" />
<a id="uri" />
<a id="uricomponent" />
<a id="uricomponenttostring" />

## <a name="string-functions"></a>文字列関数
リソース マネージャーには、文字列を操作する次の関数が用意されています。

* [base64](resource-group-template-functions-string.md#base64)
* [base64ToJson](resource-group-template-functions-string.md#base64tojson)
* [base64ToString](resource-group-template-functions-string.md#base64tostring)
* [concat](resource-group-template-functions-string.md#concat)
* [contains](resource-group-template-functions-string.md#contains)
* [dataUri](resource-group-template-functions-string.md#datauri)
* [dataUriToString](resource-group-template-functions-string.md#datauritostring)
* [empty](resource-group-template-functions-string.md#empty)
* [endsWith](resource-group-template-functions-string.md#endswith)
* [first](resource-group-template-functions-string.md#first)
* [format](resource-group-template-functions-string.md#format)
* [guid](resource-group-template-functions-string.md#guid)
* [indexOf](resource-group-template-functions-string.md#indexof)
* [last](resource-group-template-functions-string.md#last)
* [lastIndexOf](resource-group-template-functions-string.md#lastindexof)
* [length](resource-group-template-functions-string.md#length)
* [newGuid](resource-group-template-functions-string.md#newguid)
* [padLeft](resource-group-template-functions-string.md#padleft)
* [replace](resource-group-template-functions-string.md#replace)
* [skip](resource-group-template-functions-string.md#skip)
* [split](resource-group-template-functions-string.md#split)
* [startsWith](resource-group-template-functions-string.md#startswith)
* [string](resource-group-template-functions-string.md#string)
* [substring](resource-group-template-functions-string.md#substring)
* [take](resource-group-template-functions-string.md#take)
* [toLower](resource-group-template-functions-string.md#tolower)
* [toUpper](resource-group-template-functions-string.md#toupper)
* [trim](resource-group-template-functions-string.md#trim)
* [uniqueString](resource-group-template-functions-string.md#uniquestring)
* [uri](resource-group-template-functions-string.md#uri)
* [uriComponent](resource-group-template-functions-string.md#uricomponent)
* [uriComponentToString](resource-group-template-functions-string.md#uricomponenttostring)
* [utcNow](resource-group-template-functions-string.md#utcnow)

## <a name="next-steps"></a>次の手順
* Azure リソース マネージャー テンプレートのセクションの説明については、「 [Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)
* 複数のテンプレートをマージするには、 [Azure リソース マネージャーでのリンクされたテンプレートの使用](resource-group-linked-templates.md)
* 1 種類のリソースを指定した回数分繰り返し作成するには、「 [Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)
* 作成したテンプレートをデプロイする方法を確認するには、[Azure Resource Manager のテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)に関するページを参照してください。
