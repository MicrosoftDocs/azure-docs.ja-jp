---
title: Cloud Services と管理証明書 | Microsoft Docs
description: Microsoft Azure で証明書を作成し、使用する方法を学習します。
services: cloud-services
documentationcenter: .net
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: gwallace
ms.openlocfilehash: 3c84c6832856986a45be7d275fb94a6c5fc066f0
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359200"
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Azure Cloud Services の証明書の概要
証明書は、Azure でクラウド サービス ([サービス証明書](#what-are-service-certificates))、および管理 API の認証に使われます ([管理証明書](#what-are-management-certificates))。 このトピックでは、両方の種類の証明書の一般的な概要、これらを[作成](#create)する方法、および Azure にデプロイする方法について説明します。

Azure で使用される証明書は x.509 v3 証明書であり、別の信頼された証明書によって署名することも、自己署名することもできます。 自己署名証明書は、作成者自身が署名するため、既定では信頼されません。 ほとんどのブラウザーではこの問題を無視できます。 自己署名証明書は、クラウド サービスを開発し、テストする際にのみ使用することをお勧めします。 

Azure が使用する証明書には、プライベート キーか公開キーを含めることができます。 証明書には、それらを明確な方法で識別する手段を提供する拇印があります。 この拇印は、Azure の [構成ファイル](cloud-services-configure-ssl-certificate-portal.md) で使用され、クラウド サービスで使用すべき証明書を識別します。 

>[!Note]
>Azure Cloud Services は、AES256-SHA256 で暗号化された証明書を受け付けません。

## <a name="what-are-service-certificates"></a>サービス証明書とは何でしょうか。
サービス証明書はクラウド サービスに付属して、サービスとの間のセキュリティで保護された通信を有効にします。 次の例では、Web ロールをデプロイして、公開されている HTTPS エンドポイントを認証できる証明書を指定します。 サービス定義で定義されているサービス証明書は、ロールのインスタンスを実行している仮想マシンに自動的にデプロイされます。 

Azure Portal または Azure クラシック デプロイ モデルを使って、サービス証明書を Azure にアップロードすることができます。 サービス証明書は特定のクラウド サービスに関連付けられ、 サービス定義ファイルのデプロイメントに割り当てられています。

サービス証明書は、サービスとは別に管理することができ、別の人が管理する場合があります。 たとえば、開発者が IT 管理者が 以前 Azure にアップロードした証明書を参照するサービス パッケージをアップロードする場合があります。 IT 管理者は、新しいサービス パッケージをアップロードすることなくサービスの構成を変更する証明書を管理および更新できます。 論理名、ストアの名前、証明書の場所がサービス定義ファイルにあり、証明書の拇印はサービス構成ファイルで指定されているため、新しいサービス パッケージなしで更新することは可能です。 証明書を更新するには、サービス構成ファイルに新しい証明書をアップロードし、拇印値を変更するだけです。

>[!Note]
>[Cloud Services の構成と管理についての FAQ](cloud-services-configuration-and-management-faq.md) に関する記事には、証明書に関して役に立つ情報があります。

## <a name="what-are-management-certificates"></a>管理証明書とは何でしょうか。
管理証明書により、クラシック デプロイ モデルで認証することができます。 多くのプログラムとツール (Visual Studio や Azure SDK など) でこれらの証明書が使用され、さまざまな Azure サービスの構成とデプロイメントが自動化されます。 これらはクラウド サービスには実際には関連がありません。 

> [!WARNING]
> ご注意ください。 これらの種類の証明書を使用して認証する場合、関連付けられているサブスクリプションを管理できます。 
> 
> 

### <a name="limitations"></a>制限事項
各サブスクリプションでは、管理証明書の数は 100 個までに制限されています。 また、特定のサービス管理者のユーザー ID の下にあるすべてのサブスクリプションでも、管理証明書の数は 100 個までに制限されています。 アカウント管理者のユーザー ID が既に 100 の管理証明書の追加に使用されていて、さらに証明書が必要な場合は、証明書を追加する共同管理者を追加できます。 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>新しい自己署名証明書を作成する
次の設定に準拠していれば、使用可能な任意のツールを使用して自己署名証明書を作成することができます。

* X.509 証明書。
* 秘密キーが含まれている。
* キー交換用に作成される (.pfx ファイル)。
* 証明書の件名はクラウド サービスへのアクセスに使用されるドメインと一致する必要があります。

    > cloudapp.net ドメイン (または Azure に関連するドメイン) の SSL 証明書を取得することはできません。証明書の件名は、アプリケーションへの接続に使用されるカスタム ドメイン名と一致している必要があります。 たとえば、**contoso.cloudapp.net** ではなく、**contoso.net** を使います。

* 最大で 2048 ビットの暗号化。
* **サービス証明書のみ**:クライアント側の証明書は "*個人*" 証明書ストアに格納されている必要があります。

Windows で証明書を作成する簡単な方法として、 `makecert.exe` ユーティリティを使用する方法と IIS を使用する方法の 2 つがあります。

### <a name="makecertexe"></a>Makecert.exe
このユーティリティは非推奨になったため、ここに記載されなくなりました。 詳細については、 [こちらの MSDN 記事](/windows/desktop/SecCrypto/makecert)を参照してください。

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> ドメインではなく IP アドレスを持つ証明書を使用する場合は、-DnsName パラメーターで IP アドレスを使用します。


この [証明書を管理ポータルで](../azure-api-management-certs.md)使用する場合は、 **.cer** ファイルにエクスポートしてください。

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>インターネット インフォメーション サービス (IIS)
インターネット上には、IIS を使用した実行方法について説明する多くのページがあります。 [ここ](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) では、わかりやすく説明しているページを紹介します。 

### <a name="linux"></a>Linux
[この](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)記事では、SSH で証明書を作成する方法について説明します。

## <a name="next-steps"></a>次の手順
[サービス証明書を Azure Portal にアップロードします](cloud-services-configure-ssl-certificate-portal.md)。

[管理 API 証明書](../azure-api-management-certs.md) を Azure Portal にアップロードします。

