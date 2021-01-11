---
title: 'ML Studio (classic): データのエクスポートと削除 - Azure'
description: Azure Machine Learning Studio (クラシック) によって格納された製品内データは、Azure portal 経由のほか、認証済み REST API 経由でもエクスポートおよび削除できます。 テレメトリ データには、Azure Privacy Portal を介してアクセスすることができます。 この記事では、その方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: 80a9fe1afdee820904b07254d317e4d00c7fac71
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691379"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (クラシック) から製品内ユーザー データをエクスポートおよび削除する

**適用対象:** ![はい](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (クラシック)   ![いいえ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)




Azure Machine Learning Studio (クラシック) によって格納された製品内データは、Azure portal、Studio (クラシック) インターフェイス、PowerShell、および認証済み REST API を使用して削除またはエクスポートできます。 この記事では、その方法について説明します。 

テレメトリ データには、Azure Privacy Portal を介してアクセスできます。 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-classic-collect"></a>Studio (クラシック) で収集されるユーザー データの種類

このサービスの対象となるユーザー データは、ワークスペースへのアクセス権限およびユーザーとサービスとの対話のテレメトリ レコードへのアクセス権限を持つユーザーに関する情報で構成されます。

Machine Learning Studio (クラシック) には、次の 2 種類のユーザー データがあります。
- **個人用アカウントのデータ:** アカウントに関連付けられたアカウント ID と電子メール アドレス。
- **顧客データ:** 分析のためにアップロードしたデータ。

## <a name="studio-classic-account-types-and-how-data-is-stored"></a>Studio (クラシック) アカウントの種類とデータの格納方法

Machine Learning Studio (クラシック) には 3 種類のアカウントがあります。 所有しているアカウントの種類によって、データの格納方法と、データの削除またはエクスポートの方法が決まります。

- **ゲスト ワークスペース**は、無料の匿名アカウントです。 電子メール アドレスやパスワードなどの資格情報を指定することなく、サインアップします。
    -  ゲスト ワークスペースの有効期限が切れると、データは削除されます。
    - ゲスト ユーザーは、UI、REST API、または PowerShell パッケージを介して顧客データをエクスポートできます。
- **無料のワークスペース**は、Microsoft アカウントの資格情報 (電子メール アドレスとパスワード) を使用してサインインする無料アカウントです。
    - データ主体の権利 (DSR) 要求の影響を受ける、個人データおよび顧客データをエクスポートおよび削除できます。
    - UI、REST API、または PowerShell パッケージを介して顧客データをエクスポートすることができます。
    - Azure AD アカウントを使用していない無料のワークスペースの場合は、Privacy Portal を使用してテレメトリをエクスポートすることができます。
    - ワークスペースを削除する場合は、個人の顧客データもすべて削除します。
- **標準ワークスペース**は、サインイン資格情報を使用してアクセスする有料アカウントです。
    - DSR 要求の影響を受ける、個人データおよび顧客データをエクスポートおよび削除できます。
    - データには Azure Privacy Portal を介してアクセスできます。
    - UI、REST API、または PowerShell パッケージを介して個人データおよび顧客データをエクスポートすることができます。
    - Azure Portal でデータを削除できます。

## <a name="delete-workspace-data-in-studio-classic"></a><a name="delete"></a>Studio (クラシック) でワークスペース データを削除する 

### <a name="delete-individual-assets"></a>個々の資産を削除する

ユーザーはワークスペース内の資産を削除できます。それには該当する資産を選択し、[削除] ボタンを選択します。

![Machine Learning Studio (クラシック) で資産を削除する](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>ワークスペース全体を削除する

ユーザーはまた、ワークスペース全体を削除することもできます。
- 有料のワークスペース:Azure portal を使用して削除します。
- 無料のワークスペース: **[設定]** ウィンドウの [削除] ボタンを使用します。

![Machine Learning Studio (クラシック) で無料のワークスペースを削除する](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-classic-data-with-powershell"></a>PowerShell を使用して Studio (クラシック) データをエクスポートする
PowerShell では、コマンドを使用して Azure Machine Learning Studio (クラシック) から、すべての情報をポータブル形式にエクスポートできます。 詳細については、[Azure Machine Learning Studio (クラシック) 用の PowerShell モジュール](powershell-module.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

Web サービスや契約プランの課金が説明されているドキュメントについては、[Azure Machine Learning Studio (クラシック) REST API リファレンス](https://docs.microsoft.com/rest/api/machinelearning/)に関するページを参照してください。 
