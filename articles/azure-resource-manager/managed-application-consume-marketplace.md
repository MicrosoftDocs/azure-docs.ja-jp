---
title: "Marketplace での Azure マネージ アプリケーションの実行 | Microsoft Docs"
description: "Marketplace を介して利用できる Azure マネージ アプリケーションを作成する方法について説明します。"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/11/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: baf456740bddd562391ed64d707f990c8921d710
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="consume-azure-managed-applications-in-the-marketplace"></a>Marketplace での Azure マネージ アプリケーションの実行

[マネージ アプリケーションの概要](managed-application-overview.md)に関する記事で説明したように、マネージ アプリケーションには、その利用形態を全体として見た場合に 2 つのシナリオが存在します。 1 つは、顧客向けにマネージ アプリケーションを作成する発行元 (ベンダー) のシナリオです。 もう 1 つは、そのマネージ アプリケーションのエンド カスタマー (コンシューマー) のシナリオです。 この記事では、2 番目のシナリオについて説明します。 ここでは、Microsoft Azure Marketplace からマネージ アプリケーションをデプロイする方法について説明します。

## <a name="create-from-the-marketplace"></a>Marketplace から作成する

Marketplace からのマネージ アプリケーションのデプロイは、Marketplace からの任意の種類のリソースのデプロイと似ています。 

ポータルで、[**+ 新規**] を選択し、デプロイするソリューションの種類を検索します。 使用可能なオプションから、必要なオプションを選択します。

![検索ソリューション](./media/managed-application-consume-marketplace/search-apps.png)

アプリケーションの概要を確認し、[**作成**] を選択します。

![マネージ アプリケーションの作成](./media/managed-application-consume-marketplace/create-marketplace-managed-app.png)

その他のソリューションと同様に、値を入力するフィールドが表示されます。 これらのフィールドは、作成するマネージ アプリケーションの種類によって異なります。 

## <a name="view-support-information"></a>サポート情報の表示

マネージ アプリケーションを導入した後で、アプリケーションのサポート情報を表示します。 マネージ アプリケーション ブレードに、サポート情報が表示されます。

![support](./media/managed-application-consume-marketplace/support.png)

## <a name="view-publisher-permissions"></a>パブリッシャーのアクセス許可の表示

アプリケーションを管理するベンダーには、リソースへのアクセス権が付与されます。 これらのアクセス許可を表示するには、[**承認**] を選択します。

![承認](./media/managed-application-consume-marketplace/authorizations.png)

## <a name="next-steps"></a>次のステップ

* マネージ アプリケーションを Marketplace に発行する方法については、[Marketplace における Azure マネージ アプリケーション](managed-application-author-marketplace.md)に関するページを参照してください。
* 組織のユーザーのみが使用できるマネージ アプリケーションを発行するには、「[Azure マネージ アプリケーションの作成と発行](managed-application-publishing.md)」を参照してください。
* 組織のユーザーのみが実行できるマネージ アプリケーションを発行するには、「[Azure マネージ アプリケーションの実行](managed-application-consumption.md)」を参照してください。
