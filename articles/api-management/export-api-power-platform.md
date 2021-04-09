---
title: Azure API Management から Power Platform に API をエクスポートする | Microsoft Docs
description: API Management から Power Platform に API をエクスポートする方法について説明します。
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/01/2020
ms.author: apimpm
ms.openlocfilehash: 7c4d32dd63120a52fd7351977943574455e5cfad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93146656"
---
# <a name="export-apis-from-azure-api-management-to-the-power-platform"></a>Azure API Management から Power Platform に API をエクスポートする 

Microsoft [Power Platform](https://powerplatform.microsoft.com) を使用する市民開発者が、プロの開発者によって作成されて Azure にデプロイされたビジネス機能を必要とするケースは少なくありません。 [Azure API Management](https://aka.ms/apimrocks) では、プロの開発者がそのバックエンド サービスを API として公開し、それらを市民開発者が利用できるよう、カスタム コネクタとして簡単に Power Platform (Power Apps と Power Automate) にエクスポートすることができます。 

この記事では、API Management から Power Platform に API をエクスポートする手順について説明します。 

## <a name="prerequisites"></a>前提条件

+ 次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)
+ Power Platform にエクスポートしたい API が API Management インスタンスに存在することを確認する
+ Power Apps または Power Automate [環境](/powerapps/powerapps-overview#power-apps-for-admins)があることを確認する 

## <a name="export-an-api"></a>API をエクスポートする

1. Azure portal で API Management サービスに移動し、メニューから **[API]** を選択します。
2. エクスポートする API の横にある 3 つのドットをクリックします。 
3. **[エクスポート]** を選択します。
4. **[Power Apps and Power Automate]\(Power Apps と Power Automate\)** を選択します。
5. API のエクスポート先となる環境を選択します。 
6. 表示名を指定します。この名前が、カスタム コネクタの名前として使用されます。  
7. (省略可) OAuth 2.0 サーバーによって API が保護されている場合は、別途、`Client ID`、`Client secret`、`Authorization URL`、`Token URL`、`Refresh URL` などの情報を指定する必要があります。  
8. **[エクスポート]** を選択します。 

エクスポートが完了したら、Power App または Power Automate 環境に移動します。 API がカスタム コネクタとして表示されます。

## <a name="next-steps"></a>次のステップ

* [Power Platform について理解を深める](https://powerplatform.microsoft.com/)
* [チュートリアルに従って API Management の一般的なタスクを確認する](./import-and-publish.md)
