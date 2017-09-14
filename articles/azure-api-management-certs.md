---
title: "Azure Management API 証明書のアップロード | Microsoft Docs"
description: "Azure クラシック ポータルの Management API 証明書をアップロードする方法について説明します。"
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: na
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: adegeo
ms.translationtype: HT
ms.sourcegitcommit: 99523f27fe43f07081bd43f5d563e554bda4426f
ms.openlocfilehash: 9dc438e927acd9aef38f06807fabf3dda9b021c9
ms.contentlocale: ja-jp
ms.lasthandoff: 08/05/2017

---
# <a name="upload-an-azure-management-api-management-certificate"></a>Azure Management API Management 証明書のアップロード
管理証明書を使用することにより、Azure のクラシック デプロイメント モデルを使用して認証できます。 多くのプログラムとツール (Visual Studio や Azure SDK など) でこれらの証明書が使用され、さまざまな Azure サービスの構成とデプロイメントが自動化されます。 

> [!WARNING]
> ご注意ください。 これらの種類の証明書を使用して認証する場合、関連付けられているサブスクリプションを管理できます。
>
>

Azure の証明書の詳細 (自己署名証明書の作成など) については、[「Azure Cloud Services の証明書の概要」](cloud-services/cloud-services-certs-create.md#what-are-management-certificates)を参照してください。

クライアント コードの認証に [Azure Active Directory](https://azure.microsoft.com/en-us/services/active-directory/) を使用することで自動化を図ることもできます。

## <a name="upload-a-management-certificate"></a>管理証明書のアップロード
管理証明書 (公開キーのみを含んだ .cer ファイル) を作成したら、それをポータルにアップロードできます。 ポータルで証明書が使用可能な場合は、対になる証明書 (秘密キー) を持つすべての人が、Management API を経由して接続し、サブスクリプションに関連付けられているリソースにアクセスすることができます。

1. [Azure ポータル](http://portal.azure.com)にログインします。
2. Azure サービスの一覧の下部にある **[その他のサービス]** をクリックし、_[全般]_ サービス グループの **[サブスクリプション]** を選択します。

    ![サブスクリプション メニュー](./media/azure-api-management-certs/subscriptions_menu.png)

3. 証明書を関連付ける正しいサブスクリプションが選択されていることを確認します。     
4. 正しいサブスクリプションを選択したら、**[管理証明書]** で、_[設定]_ グループを押します。

    ![設定](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. **[アップロード]** ボタンを押します。

    ![[証明書] ページにアップロードします。](./media/azure-api-management-certs/certificates_page.png)
6. ダイアログの情報を入力し、**[アップロード]** を押します。

    ![設定](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>次のステップ
サブスクリプションへの管理証明書の関連付けはこれで完了です。これで、(対になる証明書をローカルにインストールした後) プログラムから[クラシック デプロイメント モデル REST API](https://msdn.microsoft.com/library/azure/mt420159.aspx) に接続して、同じサブスクリプションに関連付けられているさまざまな Azure リソースを自動化できます。

