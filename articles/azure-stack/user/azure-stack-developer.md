---
title: "Azure Stack 用アプリの開発 | Microsoft Docs"
description: "Azure Stack でのアプリケーションのプロトタイプに関する、開発の考慮事項について"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 062c17173f87eec8e0eaa3f74323cbf8a8f48571
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="develop-for-azure-stack"></a>Azure Stack 向けの開発
Azure Stack 環境へのアクセス権がなくても、アプリケーションの開発をさっそく始めることができます。 Azure Stack は、お使いのデータセンターで実行される Microsoft Azure サービスを提供しているため、Azure で使用するのと同様のツールとプロセスを使用して、Azure Stack 向けの開発ができます。  簡単な準備をして次のトピックの説明に従えば、Azure を使用して Azure Stack 環境をエミュレートできます。

* Azure で、Azure Stack にもデプロイできる Azure Resource Manager テンプレートを作成できます。  移植性が確保されたテンプレートの作成について詳しくは、[テンプレートに関する考慮事項](azure-stack-develop-templates.md)をご覧ください。
* Azure と Azure Stack 間には、サービスの可用性とサービスのバージョン管理において違いがあります。 [Azure Stack ポリシー モジュール](azure-stack-policy-module.md)を使用して、Azure Stack で使用できる Azure サービスの可用性やリソースの種類を制限できます。 利用可能なサービスの制限は、Azure Stack で使用できるサービスにアプリケーションが依存するうえで役立ちます。
* [Azure Stack クイックスタート テンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates)は、Azure と Azure Stack の両方にデプロイできるテンプレートを作成する方法の一般的なシナリオ例です。


