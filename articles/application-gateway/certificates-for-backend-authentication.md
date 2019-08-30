---
title: Azure Application Gateway でバックエンドをホワイトリスト登録するために必要な証明書
description: この記事では、SSL 証明書を、Azure Application Gateway でバックエンド インスタンスをホワイトリスト登録するために必要な認証証明書と信頼されたルート証明書に変換する方法を説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/23/2019
ms.author: absha
ms.openlocfilehash: ae1ac3df3da4e5c25e5538f0e8cc4cd12f9186c6
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774785"
---
# <a name="create-certificates-to-allow-the-backend-with-azure-application-gateway"></a>Azure Application Gateway でバックエンドを許可する証明書を作成する

エンド ツー エンド SSL を実行するために、Application Gateway では、認証証明書または信頼されたルート証明書をアップロードしてバックエンド インスタンスを許可する必要があります。 証明書を許可するために、v1 SKU の場合は認証証明書が必要ですが、v2 SKU の場合は信頼されたルート証明書が必要です。

この記事では、次のことについて説明します。

> [!div class="checklist"]
>
> - バックエンド証明書から認証証明書をエクスポートする (v1 SKU の場合)
> - バックエンド証明書からの信頼されたルート証明書のエクスポート (v2 SKU の場合)

## <a name="prerequisites"></a>前提条件

Application Gateway に対してバックエンド インスタンスを許可するために必要な認証証明書または信頼されたルート証明書を生成するには、既存のバックエンド証明書が必要です。 バックエンド証明書は、SSL 証明書と同じにすることも、セキュリティを強化するために別のものにすることもできます。 Application Gateway では、SSL 証明書を作成または購入するためのメカニズムは提供されません。 テストの目的で自己署名証明書を作成できますが、運用環境ワークロードでは使用しないでください。 

## <a name="export-authentication-certificate-for-v1-sku"></a>認証証明書をエクスポートする (v1 SKU の場合)

認証証明書は、Application Gateway v1 SKU でバックエンド インスタンスを許可するために必要です。 認証証明書は、Base-64 エンコード X.509(.CER) 形式のバックエンド サーバー証明書の公開キーです。 この例では、バックエンド証明書として SSL 証明書を使用し、認証証明書として使用するためにその公開キーをエクスポートします。 また、この例では、Windows 証明書マネージャー ツールを使用して必要な証明書をエクスポートします。 ご都合に応じて他の任意のツールを使用できます。

SSL 証明書から公開キー .cer ファイルをエクスポートします (秘密キーではありません)。 次の手順で、証明書のために Base-64 エンコード X.509(.CER) 形式の .cer ファイルをエクスポートします。

1. 証明書から .cer ファイルを取得するには、 **[ユーザー証明書の管理]** を開きます。 証明書を探して右クリックします (通常は Current User\Personal\Certificates にあります)。 **[すべてのタスク]** 、 **[エクスポート]** の順にクリックします。 **証明書のエクスポート ウィザード**が開きます。 Current User\Personal\Certificates に証明書が見つからない場合は、誤って "Certificates - Current User" ではなく "Certificates - Local Computer" を開いている可能性があります。 PowerShell を使用して現在のユーザー スコープで証明書マネージャーを開きたい場合は、コンソール ウィンドウで「*certmgr*」と入力します。

   ![エクスポート](./media/certificates-for-backend-authentication/export.png)

2. ウィザードで **[次へ]** をクリックします。

   ![証明書をエクスポートします。](./media/certificates-for-backend-authentication/exportwizard.png)

3. **[いいえ、秘密キーをエクスポートしません]** を選択して、 **[次へ]** をクリックします。

   ![秘密キーをエクスポートしません](./media/certificates-for-backend-authentication/notprivatekey.png)

4. **[エクスポート ファイルの形式]** ページで **[Base-64 encoded X.509 (.CER)]** を選択し、 **[次へ]** をクリックします。

   ![Base-64 エンコード](./media/certificates-for-backend-authentication/base64.png)

5. **[エクスポートするファイル]** で、 **[参照]** をクリックして証明書をエクスポートする場所を選択します。 **[ファイル名]** に証明書ファイルの名前を指定します。 次に、 **[次へ]** をクリックします。

   ![参照](./media/certificates-for-backend-authentication/browse.png)

6. **[完了]** をクリックして、証明書をエクスポートします。

   ![[完了]](./media/certificates-for-backend-authentication/finish.png)

7. 証明書が正常にエクスポートされました。

   ![Success](./media/certificates-for-backend-authentication/success.png)

   エクスポートされた証明書は次のようになります。

   ![エクスポート済み](./media/certificates-for-backend-authentication/exported.png)

8. メモ帳を使ってエクスポートした証明書を開くと、この例のようなものが表示されます。 青で示したセクションには、Application Gateway にアップロードされる情報が含まれます。 証明書をメモ帳で開いてもこのように表示されない場合は、通常、Base-64 エンコードの X.509 (.CER) 形式を使ってエクスポートしなかったことを意味します。 また、別のテキスト エディターを使う場合は、一部のエディターでは意図しない書式設定がバックグラウンドで行われる場合があることに注意してください。 これにより、この証明書から Azure にテキストをアップロードすると問題が発生する可能性があります。

   ![メモ帳で開く](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>信頼されたルート証明書をエクスポートする (V2 SKU の場合)

信頼されたルート証明書は、Application Gateway v2 SKU でバックエンド インスタンスをホワイトリスト登録するために必要です。 このルート証明書は､バックエンド サーバー証明書からの Base-64 エンコード X.509(.CER) 形式のルート証明書です。 この例では、バックエンド証明書の SSL 証明書を使用し、その公開キーをエクスポートし、base64 エンコード形式の公開キーから信頼された CA のルート証明書をエクスポートして、信頼されたルート証明書を取得します。 中間証明書は、サーバー証明書とバンドルして、バックエンド サーバーにインストールする必要があります。

次の手順で、証明書のための .cer ファイルをエクスポートします。

1. 前述の「**バックエンド証明書から認証証明書をエクスポートする (v1 SKU の場合)** 」セクションの手順 1 - 9 に従って、バックエンド証明書から公開キーをエクスポートします。

2. 公開キーがエクスポートされたらファイルを開きます。

   ![認証証明書を開く](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![証明書について](./media/certificates-for-backend-authentication/general.png)

3. [証明のパス] ビューに移動して証明機関を表示します。

   ![証明書の詳細](./media/certificates-for-backend-authentication/certdetails.png)

4. ルート証明書を選択して、 **[証明書の表示]** をクリックします。

   ![証明書のパス](./media/certificates-for-backend-authentication/rootcert.png)

   ルート証明書の詳細が表示されます。

   ![証明書の情報](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. **[詳細]** ビューに移動し、 **[ファイルにコピー...]** をクリックします。

   ![ルート証明書のコピー](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. この時点で、バックエンド証明書からルート証明書の詳細を抽出しました。 **証明書のエクスポート ウィザード**が表示されます。 ここで、前述の「**バックエンド証明書から認証証明書をエクスポートする (v1 SKU の場合)** 」セクションの手順 2 - 9 に従って、Base-64 エンコード X.509(.CER) 形式の信頼されたルート証明書をエクスポートします。

## <a name="next-steps"></a>次の手順

Base-64 エンコード X.509(.CER) 形式の認証証明書/信頼されたルート証明書を入手しました。 これを Application Gateway に追加して、エンド ツー エンド SSL 暗号化に対してバックエンド サーバーをホワイトリスト登録できます。 [エンド ツー エンド SSL 暗号化の構成方法](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)に関するページをご覧ください。

