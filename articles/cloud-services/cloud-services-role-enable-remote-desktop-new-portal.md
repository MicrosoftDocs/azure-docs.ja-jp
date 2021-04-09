---
title: ポータルを使用してロールのリモート デスクトップを有効にする
description: Azure クラウド サービス アプリケーションを構成してリモート デスクトップ接続を許可する方法
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 8fa0d3c0e29c53e6fe9cb32ddf02168686be1efe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743255"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic"></a>Azure Cloud Services (クラシック) でロールのリモート デスクトップ接続を有効にする

> [!IMPORTANT]
> [Azure Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) は、Azure Cloud Services 製品向けの新しい Azure Resource Manager ベースのデプロイ モデルです。 この変更により、Azure Service Manager ベースのデプロイ モデルで実行されている Azure Cloud Services は Cloud Services (クラシック) という名前に変更されました。そして、すべての新しいデプロイでは [Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) を使用する必要があります。

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

リモート デスクトップを使用して、Azure で実行されているロールのデスクトップにアクセスできます。 リモート デスクトップ接続を使用すると、アプリケーションの実行中にそのアプリケーションの問題のトラブルシューティングや診断を行うことができます。

サービス定義にリモート デスクトップ モジュールを含めることで開発時にロールのリモート デスクトップ接続を有効にするか、リモート デスクトップ拡張機能を使用してリモート デスクトップを有効にすることができます。 アプリケーションのデプロイ後でもアプリケーションを再デプロイすることなくリモート デスクトップを有効にできるため、リモート デスクトップ拡張機能を使用する方法が推奨されます。

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Azure Portal からリモート デスクトップを構成する

Azure Portal では、アプリケーションのデプロイ後でもリモート デスクトップを有効化できるように、リモート デスクトップ拡張機能の方法を使用します。 クラウド サービスの **[リモート デスクトップ]** 設定では、リモート デスクトップの有効化、仮想マシンへの接続に使用するローカル管理者アカウントや認証に使用する証明書の変更、および有効期限の設定を行うことができます。

1. **[クラウド サービス]** をクリックし、クラウド サービスの名前を選択して、 **[リモート デスクトップ]** を選択します。

    ![クラウド サービスのリモート デスクトップを示す画像](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. リモート デスクトップを有効にする対象を個々のロールとすべてのロールのどちらにするかを選択し、スイッチャーの値を **[Enabled (有効)]** に変更します。

3. ユーザー名、パスワード、有効期限、および証明書の各必須フィールドに入力します。

    ![クラウド サービスのリモート デスクトップ](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > 初めてリモート デスクトップを有効にして **[OK]** (チェックマーク) を選択したときは、すべてのロール インスタンスが再起動されます。 再起動を防止するには、パスワードの暗号化に使用した証明書がロールにインストールされている必要があります。 再起動を防止するには、[クラウド サービスの証明書をアップロード](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) して、このダイアログに戻ります。

4. **[ロール]** で、更新するロールを選択します。すべてのロールの場合は、 **[すべて]** をクリックします。

5. 構成の更新が完了したら、 **[保存]** を選択します。 ロール インスタンスが接続を受信する準備が完了するまで数分間かかります。

## <a name="remote-into-role-instances"></a>ロール インスタンスへのリモート接続

ロールに対してリモート デスクトップが有効になったら、Azure Portal から直接接続を開始できます。

1. **[インスタンス]** をクリックして、 **[インスタンス]** 設定を開きます。
2. リモート デスクトップが構成されたロール インスタンスを選択します。
3. **[接続]** をクリックしてロール インスタンス用の RDP ファイルをダウンロードします。

    ![クラウド サービスのリモート デスクトップの画像](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. **[開く]** 、 **[接続]** の順にクリックして、リモート デスクトップ接続を開始します。

>[!NOTE]
> クラウド サービスが NSG の背後にある場合は、ポート **3389** と **20000** でのトラフィックを許可するルールを作成する必要があることがあります。  リモート デスクトップではポート **3389** を使用します。  クラウド サービス インスタンスは負荷分散されるため、接続先のインスタンスを直接制御することはできません。  *RemoteForwarder* および *RemoteAccess* エージェントは、RDP トラフィックを管理し、クライアントが RDP クッキーを送信して接続先の個々のインスタンスを指定できるようにします。  *RemoteForwarder* および *RemoteAccess* エージェントではポート **20000*** を開く必要があります。NSG がある場合、ブロックされる可能性があります。

## <a name="additional-resources"></a>その他のリソース

[Cloud Services の構成方法](cloud-services-how-to-configure-portal.md)
