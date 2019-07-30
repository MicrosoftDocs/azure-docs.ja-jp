---
title: Azure サービス管理証明書のアップロード | Microsoft Docs
description: Azure Portal のサービス管理証明書をアップロードする方法について説明します。
services: cloud-services
documentationcenter: .net
author: georgewallace
manager: carmonm
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: article
ms.date: 08/01/2017
ms.author: gwallace
ms.openlocfilehash: 5ce96796cbfdcefbaf5568ff199cba6a87f65e05
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359875"
---
# <a name="upload-an-azure-service-management-certificate"></a>Azure サービス管理証明書のアップロード
管理証明書を使用することにより、Azure のクラシック デプロイ モデルを使用して認証できます。 多くのプログラムとツール (Visual Studio や Azure SDK など) でこれらの証明書が使用され、さまざまな Azure サービスの構成とデプロイメントが自動化されます。 

> [!WARNING]
> ご注意ください。 これらの種類の証明書を使用して認証する場合、関連付けられているサブスクリプションを管理できます。
>
>

Azure の証明書の詳細 (自己署名証明書の作成など) については、[「Azure Cloud Services の証明書の概要」](cloud-services/cloud-services-certs-create.md#what-are-management-certificates)を参照してください。

クライアント コードの認証に [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) を使用することで自動化を図ることもできます。

**注:** 管理証明書で操作を実行するには、サブスクリプションの共同管理者である必要があります。 新しい Azure Portal で共同管理者を追加または削除する方法の詳細については、[こちら](https://go.microsoft.com/fwlink/?linkid=849300)を参照してください 

## <a name="upload-a-management-certificate"></a>管理証明書のアップロード
管理証明書 (公開キーのみを含んだ .cer ファイル) を作成したら、それをポータルにアップロードできます。 ポータルで証明書が使用可能な場合は、対になる証明書 (秘密キー) を持つすべての人が、Management API を経由して接続し、サブスクリプションに関連付けられているリソースにアクセスすることができます。

1. [Azure Portal](https://portal.azure.com) にログインします。
2. Azure サービスの一覧の下部にある **[すべてのサービス]** をクリックし、 _[全般]_ サービス グループの **[サブスクリプション]** を選択します。

    ![サブスクリプション メニュー](./media/azure-api-management-certs/subscriptions_menu.png)

3. 証明書を関連付ける正しいサブスクリプションが選択されていることを確認します。     
4. 正しいサブスクリプションを選択したら、 **[管理証明書]** で、 _[設定]_ グループを押します。

    ![設定](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. **[アップロード]** ボタンを押します。

    ![[証明書] ページにアップロードします。](./media/azure-api-management-certs/certificates_page.png)
6. ダイアログの情報を入力し、 **[アップロード]** を押します。

    ![設定](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>次の手順
サブスクリプションへの管理証明書の関連付けはこれで完了です。これで、(対になる証明書をローカルにインストールした後) プログラムから[クラシック デプロイ モデル REST API](/azure/#pivot=sdkstools) に接続して、同じサブスクリプションに関連付けられているさまざまな Azure リソースを自動化できます。
