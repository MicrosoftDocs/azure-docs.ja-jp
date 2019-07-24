---
title: Dynamics 365 for Customer Engagement の技術資産を作成する - Azure Marketplace | Microsoft Docs
description: Dynamics 365 for Customer Engagement アプリケーション オファー用の技術資産を作成します。
services: Dynamics 365 for Customer Engagement, Azure, Marketplace, Cloud Partner Portal, AppSource
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 12/29/2018
ms.author: pbutlerm
ms.openlocfilehash: 50c4fd512206cdf17ebb555acb88de2a3f74c2bd
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54082157"
---
# <a name="create-technical-assets-for-azure-application-offer"></a>Azure アプリケーション オファー用の技術資産を作成する

通常、ソリューションの開発には [Dynamics 365 for Customer Engagement アプリ用の SDK](https://docs.microsoft.com/dynamics365/customer-engagement/developer/get-started-sdk) を使用します。  「[Dynamics 365 Customer Engagement のプログラミング モデル](https://docs.microsoft.com/dynamics365/customer-engagement/developer/programming-models)」で説明されているように、ソリューションにはさまざまな形式があります。  ソリューションの要件を満たす最適な形式を選択します。  ソリューションを開発するときは、拡張性の選択、ソリューションのコンポーネント、バージョンの互換性など、多くの問題に対処する必要があります。  詳しくは、「[ソリューションの概要](https://docs.microsoft.com/dynamics365/customer-engagement/developer/introduction-solutions)」をご覧ください。

AppSource に公開されるほとんどの Dynamics 365 ソリューションは、パッケージ ファイルとして配布されるマネージド アプリケーションです。


## <a name="creating-and-storing-the-package"></a>パッケージの作成と格納

Dynamics 365 for Customer Engagement オファーの作成に対応するドキュメントが、「[AppSource 上にアプリを公開する](https://docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource)」セクションで見つかります。  それに含まれる次のトピックでは、ソリューション パッケージ ファイルを作成して Azure ストレージにアップロードする方法が詳しく説明されています。

- [手順 4:アプリの AppSource パッケージを作成する](https://docs.microsoft.com/dynamics365/customer-engagement/developer/create-package-app-appsource) - マネージド アプリケーションを表す圧縮 (zip) ファイルの作成方法が説明されています。パッケージには、ソリューション資産フォルダー、カスタム コード DLL、MIME の種類の情報ファイル、AppSource パッケージ アイコン、ライセンス条件 (HTML) ファイル、コンテンツ ファイル (XML) が含まれます。
- [手順 5:AppSource パッケージを Azure Storage に保存し、SAS キーで URL を生成する](https://docs.microsoft.com/dynamics365/customer-engagement/developer/store-appsource-package-azure-storage) - AppSource パッケージ ファイルを Microsoft Azure Blob Storage アカウントに格納する方法、および Shared Access Signature (SAS) キーを使用してパッケージ ファイルを共有する方法について説明されています。 認定と、その後の AppSource 試用版および公開のために、Azure Storage の場所からパッケージ ファイルが取得されます。


## <a name="next-steps"></a>次の手順

まだ行っていない場合は、[Dynamics 365 for Customer Engagement オファーを作成](./cpp-create-offer.md)します。  そうすれば、[オファーを公開する](./cpp-publish-offer.md)準備ができます。
