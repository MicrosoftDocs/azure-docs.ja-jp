---
title: クラウド サービス向けの SSL の構成 | Microsoft Docs
description: Web ロールの HTTPS エンドポイントを指定する方法および SSL 証明書をアップロードしてアプリケーションを保護する方法を説明します。 これらの例では、Azure ポータルを使用します。
services: cloud-services
documentationcenter: .net
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 05/26/2017
ms.author: gwallace
ms.openlocfilehash: 9e7b7526f13fa6b9ae648c4ddb4004a627d85154
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359739"
---
# <a name="configuring-ssl-for-an-application-in-azure"></a>Azure でアプリケーションの SSL を構成する

Secure Socket Layer (SSL) の暗号化は、インターネットを介して送信されるデータをセキュリティで保護する際に最もよく使用される方法です。 この一般的なタスクでは、Web ロールの HTTPS エンドポイントを指定する方法および SSL 証明書をアップロードしてアプリケーションを保護する方法を説明します。

> [!NOTE]
> このタスクの手順は、Azure Cloud Services に適用されます。App Services については、[こちら](../app-service/app-service-web-tutorial-custom-ssl.md)をご覧ください。
>

このタスクでは、運用環境のデプロイを使用します。 ステージング環境のデプロイを使用する場合の情報については、このトピックの最後で紹介します。

クラウド サービスを作成していない場合は、まず [こちら](cloud-services-how-to-create-deploy-portal.md) を参照してください。

## <a name="step-1-get-an-ssl-certificate"></a>手順 1:SSL 証明書を取得する
アプリケーションの SSL を構成するには、最初に、セキュリティ保護のための証明書を発行する信頼されたサード パーティである、証明機関 (CA) によって署名された SSL 証明書を取得する必要があります。 まだ SSL 証明書がない場合は、SSL 証明書を販売する会社から取得する必要があります。

証明書は、Azure における SSL 証明書の次の要件を満たす必要があります。

* 証明書は秘密キーを含む必要があります。
* 証明書はキー交換のために作成され、Personal Information Exchange (.pfx) ファイルにエクスポートできる必要があります。
* 証明書の件名はクラウド サービスへのアクセスに使用されるドメインと一致する必要があります。 証明機関 (CA) から cloudapp.net ドメインの SSL 証明書を取得することはできません。 サービスにアクセスするときに使用するカスタム ドメイン名を取得する必要があります。 CA に証明書を要求するときは、証明書の件名がアプリケーションにアクセスするために使用するカスタム ドメイン名と一致している必要があります。 たとえば、カスタム ドメイン名が **contoso.com** の場合は、* **.contoso.com** または **www\.contoso.com** の証明書を CA に要求します。
* 証明書では、2048 ビット以上の暗号化を使用する必要があります。

テスト目的で、自己署名証明書を [作成して](cloud-services-certs-create.md) 使用できます。 自己署名証明書は CA を通じて認証されないため、cloudapp.net ドメインを Web サイト URL として使用できます。 たとえば、次のタスクでは自己署名証明書を使用しますが、証明書で使用される共通名 (CN) は **sslexample.cloudapp.net** です。

次に、この証明書に関する情報を、サービス定義ファイルおよびサービス構成ファイルに含める必要があります。

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>手順 2:サービス定義ファイルとサービス構成ファイルを変更する
アプリケーションは、証明書を使用するように構成する必要があります。また、HTTPS エンドポイントを追加する必要があります。 その結果として、サービス定義ファイルおよびサービス構成ファイルを更新する必要があります。

1. お使いの開発環境で、サービス定義ファイル (CSDEF) を開き、**WebRole** セクション内に  **Certificates** セクションを追加し、証明書 (および中間証明書) に関する次の情報を含めます。

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="My"
                        permissionLevel="limitedOrElevated" />
            <!-- IMPORTANT! Unless your certificate is either
            self-signed or signed directly by the CA root, you
            must include all the intermediate certificates
            here. You must list them here, even if they are
            not bound to any endpoints. Failing to list any of
            the intermediate certificates may cause hard-to-reproduce
            interoperability problems on some clients.-->
            <Certificate name="CAForSampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="CA"
                        permissionLevel="limitedOrElevated" />
        </Certificates>
    ...
    </WebRole>
    ```

   **Certificates** セクションでは、証明書の名前、場所、およびこの証明書があるストアの名前を定義します。

   アクセス許可 (`permissionLevel` 属性) は、次の値のいずれかに設定できます。

   | アクセス許可の値 | 説明 |
   | --- | --- |
   | limitedOrElevated |**(既定)** すべてのロール プロセスが秘密キーにアクセスできます。 |
   | elevated |引き上げられたプロセスだけが秘密キーにアクセスできます。 |

2. サービス定義ファイルで、**Endpoints** セクション内に **InputEndpoint** 要素を追加し、HTTPS を有効にします。

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Endpoints>
            <InputEndpoint name="HttpsIn" protocol="https" port="443"
                certificate="SampleCertificate" />
        </Endpoints>
    ...
    </WebRole>
    ```

3. サービス定義ファイルで、**Sites** セクション内に **Binding** 要素を追加します。 この要素は、エンドポイントをサイトにマップするための HTTPS バインドを追加します。

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="HttpsIn" endpointName="HttpsIn" />
                </Bindings>
            </Site>
        </Sites>
    ...
    </WebRole>
    ```

   サービス定義ファイルに対して必要な変更はすべて完了しましたが、サービス構成ファイルに証明書の情報を追加する必要もあります。
4. サービス構成ファイル (CSCFG) ServiceConfiguration.Cloud.cscfg に、対象の証明書の **[証明書]** 値を追加します。 次のコード サンプルは **[証明書]** セクションの詳細を提供します。ただし、拇印の値は提供しません。

   ```xml
    <Role name="Deployment">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff"
                thumbprintAlgorithm="sha1" />
            <Certificate name="CAForSampleCertificate"
                thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc"
                thumbprintAlgorithm="sha1" />
        </Certificates>
    ...
    </Role>
    ```

(この例では、拇印アルゴリズムに **sha1** を使用しています。 証明書の拇印アルゴリズムに適切な値を指定してください。)

サービス定義ファイルとサービス構成ファイルが更新されたので、Azure にアップロードするためにデプロイメントをパッケージ化します。 **cspack** を使用している場合は、 **/generateConfigurationFile** フラグを使用しないでください。このフラグは、先ほど挿入した証明書情報を上書きしてしまいます。

## <a name="step-3-upload-a-certificate"></a>手順 3:証明書のアップロード
Azure Portal に接続して...

1. Portal の **[すべてのリソース]** セクションで、対象のクラウド サービスを選択します。

    ![クラウド サービスの発行](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. **[証明書]** をクリックします。

    ![証明書アイコンをクリック](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. 証明書領域の上部にある **[アップロード]** をクリックします。

    ![[アップロード] メニュー項目をクリックする](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. **ファイル**、**パスワード**を提供し、データ入力領域の下部にある **[アップロード]** をクリックします。

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>手順 4:HTTPS を使用してロール インスタンスに接続する
Azure でデプロイメントを実行できるようになったため、HTTPS を使用して接続できます。

1. **[サイトの URL]** をクリックして、Web ブラウザーを開きます。

   ![サイトの URL をクリックする](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. Web ブラウザーで、**http** ではなく **https** を使用するようにリンクを修正し、ページにアクセスします。

   > [!NOTE]
   > 自己署名証明書を使用している場合は、自己署名証明書に関連付けられている HTTPS エンドポイントを参照すると、ブラウザーで証明書エラーが表示されることがあります。 信頼された証明機関によって署名された証明書を使用すると、この問題は解消されます。 それまでの間、このエラーは無視してかまいません(また、信頼された証明書機関のユーザーの証明書ストアに自己署名証明書を追加する方法もあります)。
   >
   >

   ![サイトのプレビュー](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > 運用環境のデプロイメントではなくステージング環境のデプロイメントに SSL を使用する場合は、最初に、ステージング環境のデプロイメントに使用されている URL を確認する必要があります。 クラウド サービスをデプロイした後、ステージング環境の URL は、形式 `https://deployment-id.cloudapp.net/` の**デプロイ ID** GUID によって決定されます。  
   >
   > GUID ベースの URL と同じ共通名 (CN) で証明書を作成します (たとえば **328187776e774ceda8fc57609d404462.cloudapp.net**)。 ポータルを使用して、この証明書をステージングされたクラウド サービスに追加します。 次に、証明書情報を CSDEF ファイルと CSCFG ファイルに追加し、アプリケーションを再パッケージ化し、新しいパッケージを使用するようにステージング デプロイを更新します。
   >

## <a name="next-steps"></a>次の手順
* [クラウド サービスの一般的な構成](cloud-services-how-to-configure-portal.md)
* 方法: [クラウド サービスをデプロイする](cloud-services-how-to-create-deploy-portal.md)
* [カスタム ドメイン名を構成する](cloud-services-custom-domain-name-portal.md)
* [クラウド サービスを管理する](cloud-services-how-to-manage-portal.md)
