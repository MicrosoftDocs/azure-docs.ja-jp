---
title: 'Azure VPN Gateway:P2S 用の証明書を生成してエクスポートする: MakeCert'
description: MakeCert を使用して、自己署名ルート証明書の作成、公開キーのエクスポート、クライアント証明書の生成を行います。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 55e22ebec5853d6b4f10b53be8e24f4dbebe4e1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94659779"
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>MakeCert を使用したポイント対サイト接続の証明書の生成とエクスポート

ポイント対サイト接続では、認証に証明書を使用します。 この記事では、MakeCert を使用した自己署名ルート証明書の作成方法とクライアント証明書の生成方法について説明します。 別の証明書の手順が必要な場合は、[証明書 - Powershell](vpn-gateway-certificates-point-to-site.md) または[証明書 - Linux](vpn-gateway-certificates-point-to-site-linux.md) の記事を参照してください。

[Windows 10 PowerShell の手順](vpn-gateway-certificates-point-to-site.md)を使用して証明書を作成することをお勧めしますが、別の手段としてこれらの MakeCert による手順も提供しています。 いずれかの方法を使用して生成した証明書は、[サポートされている任意のクライアント オペレーティング システム](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)にインストールできます。 ただし、MakeCert には次の制限事項があります。

* MakeCert は非推奨となっています。 これは、任意の時点でこのツールが削除される可能性があることを意味します。 MakeCert が利用できなくなった場合も、MakeCert を使用してすでに生成されたすべての証明書に影響はありません。 MakeCert は、検証メカニズムとしてではなく、証明書の生成にのみ使用されます。

## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>自己署名ルート証明書の作成

次の手順では、MakeCert を使用して自己署名証明書を作成する方法を説明します。 これらの手順は、デプロイ モデル固有のものではありません。 リソース マネージャーとクラシックの両方で有効です。

1. [MakeCert](/windows/win32/seccrypto/makecert) をダウンロードし、インストールします。
2. インストール後、makecert.exe ユーティリティは通常、次のパスで見つかります: 'C:\Program Files (x86)\Windows Kits\10\bin\<arch>'。 ただし、別の場所にインストールされている場合もあります。 管理者としてコマンド プロンプトを開き、MakeCert ユーティリティの場所に移動します。 次の例を使って、適切な場所に調整できます。

   ```cmd
   cd C:\Program Files (x86)\Windows Kits\10\bin\x64
   ```
3. コンピューターの個人証明書ストアで証明書を作成し、インストールします。 次の例は、P2S を構成するときに Azure にアップロードする、対応する *.cer* ファイルを作成します。 'P2SRootCert' と 'P2SRootCert.cer' を証明書に使う名前に置き換えます。 証明書は 'Current User\Personal\Certificates' にあります。

   ```cmd
   makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
   ```

## <a name="export-the-public-key-cer"></a><a name="cer"></a>公開キー (.cer) のエクスポート

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

エクスポートした .cer ファイルは Azure にアップロードする必要があります。 手順については、[ポイント対サイト接続の構成](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile)に関するページをご覧ください。 信頼されたルート証明書を追加するには、記事の[このセクション](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add)をご覧ください。

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>自己署名証明書と証明書を保存するための秘密キーのエクスポート (省略可能)

自己署名ルート証明書をエクスポートし、安全に保管することもできます。 必要に応じて、後から別のコンピューターにインストールして、さらに多くのクライアント証明書を生成したり、別の .cer ファイルをエクスポートしたりできます。 自己署名ルート証明書を .pfx としてエクスポートするには、ルート証明書を選択し、「[クライアント証明書をエクスポートする](#clientexport)」と同じ手順を実行します。

## <a name="create-and-install-client-certificates"></a>クライアント証明書の作成とインストール

自己署名証明書をクライアント コンピューターに直接インストールすることはしません。 自己署名証明書からクライアント証明書を生成し、 生成したクライアント証明書をエクスポートして、クライアント コンピューターにインストールします。 これらの手順は、デプロイ モデル固有のものではありません。 リソース マネージャーとクラシックの両方で有効です。

### <a name="generate-a-client-certificate"></a><a name="clientcert"></a>クライアント証明書の生成

ポイント対サイトで VNet に接続するすべてのクライアント コンピューターには、クライアント証明書がインストールされている必要があります。 自己署名ルート証明書からクライアント証明書を生成し、そのクライアント証明書をエクスポートしてインストールします。 クライアント証明書がインストールされていない場合は、認証が失敗します。 

次の手順では、自己署名ルート証明書からクライアント証明書を生成する方法を示しています。 同じルート証明書から複数のクライアント証明書を生成できます。 以下の手順を使用してクライアント証明書を生成すると、証明書の生成に使用したコンピューターにクライアント証明書が自動的にインストールされます。 クライアント証明書を別のクライアント コンピューターにインストールする場合は、その証明書をエクスポートできます。
 
1. 自己署名証明書の作成に使用した同じコンピューターで、管理者としてコマンド プロンプトを開きます。
2. クライアント証明書を生成するには、サンプルを変更して実行します。
   * *"P2SRootCert"* を、クライアント証明書を生成している自己署名ルートの名前に変更します。 必ずルート証明書の名前を使用してください。これは、自己署名ルートの作成時に指定した 'CN=' の値です。
   * *P2SChildCert* を、生成するクライアント証明書の名前に変更します。

   下の例を変更せずに実行すると、ルート証明書 P2SRootCert から生成された個人用証明書ストアに P2SChildcert という名前のクライアント証明書が作成されます。

   ```cmd
   makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
   ```

### <a name="export-a-client-certificate"></a><a name="clientexport"></a>クライアント証明書のエクスポート

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install-an-exported-client-certificate"></a><a name="install"></a>エクスポートしたクライアント証明書のインストール

クライアント証明書をインストールするには、[クライアント証明書のインストール](point-to-site-how-to-vpn-client-install-azure-cert.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

引き続きポイント対サイト構成を使用します。 

* **Resource Manager** デプロイメント モデルの手順については、[ネイティブ Azure 証明書認証を使用した P2S の構成](vpn-gateway-howto-point-to-site-resource-manager-portal.md)に関するページを参照してください。
* **クラシック** デプロイメント モデルの手順については、[VNet へのポイント対サイト VPN 接続の構成 (クラシック)](vpn-gateway-howto-point-to-site-classic-azure-portal.md) に関する記事を参照してください。

P2S のトラブルシューティング情報については、[Azure ポイント対サイト接続のトラブルシューティング](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)に関するページを参照してください。