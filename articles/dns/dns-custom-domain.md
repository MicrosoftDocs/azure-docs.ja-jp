---
title: Azure DNS を Azure リソースと統合する - Azure DNS
description: この記事では、Azure DNS を使用して Azure リソースに DNS を提供する方法を説明します。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 7/13/2019
ms.author: rohink
ms.openlocfilehash: 4d8af5815e544698ab833001e5ce6d0f4a30a264
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92487400"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Azure DNS を使用して Azure サービス用のカスタム ドメイン設定を提供する

Azure DNS は、カスタム ドメインをサポートする、または完全修飾ドメイン名 (FQDN) を持つ任意の Azure リソースに対して、カスタム ドメインの DNS を提供します。 たとえば、Azure Web アプリがあり、ユーザーが FQDN として contoso.com または www\.contoso.com を使用してそのアプリにアクセスできるようにします。 この記事では、カスタム ドメインを使用するために Azure サービスと Azure DNS を構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

カスタム ドメインのために Azure DNS を使用するには、まずドメインを Azure DNS に委任する必要があります。 ネーム サーバーを委任のために構成する方法については、[Delegate a domain to Azure DNS](./dns-delegate-domain-azure-dns.md)を参照してください。 Azure DNS ゾーンにドメインを委任した後、必要な DNS レコードを構成できます。

[Azure Function App](#azure-function-app)、[パブリック IP アドレス](#public-ip-address)、[App Service (Web Apps)](#app-service-web-apps)、[BLOB ストレージ](#blob-storage)、および [Azure CDN](#azure-cdn) 用のバニティまたはカスタム ドメインを構成できます。

## <a name="azure-function-app"></a>Azure Function App

Azure Function App 用のカスタム ドメインを構成するために、Function App 自体での構成に加えて CNAME レコードが作成されます。
 
**[Function App]** に移動して関数アプリを選択します。 **[プラットフォーム機能]** をクリックし、 **[ネットワーク]** で **[カスタム ドメイン]** をクリックします。

![Function App ブレード](./media/dns-custom-domain/functionapp.png)

**[Custom domains] \(カスタム ドメイン)** ブレードの現在の URL をメモしてください。このアドレスは、作成される DNS レコードの別名として使用されます。

![カスタム ドメイン ブレード](./media/dns-custom-domain/functionshostname.png)

DNS ゾーンに移動して **[+ Record set] \(レコード セットの追加)** をクリックします。 **[Add record set] \(レコード セットの追加)** ブレードで次の情報を入力し、 **[OK]** をクリックして作成します。

|プロパティ  |値  |説明  |
|---------|---------|---------|
|名前     | myfunctionapp        | この値とドメイン名ラベルを合わせたものが、カスタム ドメイン名の FQDN です。        |
|Type     | CNAME        | CNAME レコードを別名として使用します。        |
|TTL     | 1        | 1 時間には 1 を使用します        |
|TTL の単位     | 時間        | 時間数は時間の単位として使用されます         |
|エイリアス     | adatumfunction.azurewebsites.net        | 別名を作成している DNS 名。この例では、既定で Function App によって提供される adatumfunction.azurewebsites.net という DNS 名です。        |

関数アプリに戻って **[プラットフォーム機能]** をクリックし、 **[ネットワーク]** で **[カスタム ドメイン]** をクリックし、 **[カスタム ホスト名]** で **[+ ホスト名の追加]** をクリックします。

**[Add hostname] \(ホスト名の追加)** ブレードで、CNAME レコードを **[hostname] \(ホスト名)** テキスト フィールドに入力して **[Validate] \(検証)** をクリックします。 レコードが見つからなかった場合、 **[ホスト名の追加]** ボタンが表示されます。 **[Add hostname] \(ホスト名の追加)** をクリックして別名を追加します。

![Function App のホスト名追加ブレード](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>パブリック IP アドレス

Application Gateway、Load Balancer、Cloud Service、Resource Manager VM、Classic VM などの、パブリック IP アドレスを使用するサービス用のカスタム ドメインを構成するために、A レコードが使用されます。

**[Networking] \(ネットワーク)**  >  **[Public IP address] \(パブリック IP アドレス)** に移動し、パブリック IP リソースを選択して **[Configuration] \(構成)** をクリックします。 表示されている IP address をメモします。

![パブリック IP ブレード](./media/dns-custom-domain/publicip.png)

DNS ゾーンに移動して **[+ Record set] \(レコード セットの追加)** をクリックします。 **[Add record set] \(レコード セットの追加)** ブレードで次の情報を入力し、 **[OK]** をクリックして作成します。


|プロパティ  |値  |説明  |
|---------|---------|---------|
|名前     | mywebserver        | この値とドメイン名ラベルを合わせたものが、カスタム ドメイン名の FQDN です。        |
|Type     | A        | リソースは IP アドレスなので、A レコードを使用します。        |
|TTL     | 1        | 1 時間には 1 を使用します        |
|TTL の単位     | 時間        | 時間数は時間の単位として使用されます         |
|IP アドレス     | `<your ip address>`       | パブリック IP アドレス。|

![A レコードを作成する](./media/dns-custom-domain/arecord.png)

A レコードが作成されたら、`nslookup` を実行してレコードの解決を検証します。

![パブリック IP DNS 参照](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>App Service (Web Apps)

次の手順では、App Service Web アプリ用のカスタム ドメインを構成する方法を説明します。

**[App Service]** に移動し、カスタム ドメイン名を構成しているリソースを選択して、 **[カスタム ドメイン]** をクリックします。

**[Custom domains] \(カスタム ドメイン)** ブレードの現在の URL をメモしてください。このアドレスは、作成される DNS レコードの別名として使用されます。

![カスタム ドメイン ブレード](./media/dns-custom-domain/url.png)

DNS ゾーンに移動して **[+ Record set] \(レコード セットの追加)** をクリックします。 **[Add record set] \(レコード セットの追加)** ブレードで次の情報を入力し、 **[OK]** をクリックして作成します。


|プロパティ  |値  |説明  |
|---------|---------|---------|
|名前     | mywebserver        | この値とドメイン名ラベルを合わせたものが、カスタム ドメイン名の FQDN です。        |
|Type     | CNAME        | CNAME レコードを別名として使用します。 リソースが IP アドレスを使用していた場合、A レコードが使用されます。        |
|TTL     | 1        | 1 時間には 1 を使用します        |
|TTL の単位     | 時間        | 時間数は時間の単位として使用されます         |
|エイリアス     | webserver.azurewebsites.net        | 別名を作成している DNS 名。この例では、既定で Web アプリに与えられる webserver.azurewebsites.net という DNS 名です。        |


![CNAME レコードを作成する](./media/dns-custom-domain/createcnamerecord.png)

カスタム ドメイン名が構成されているアプリ サービスに戻ります。 **[Custom domains] \(カスタム ドメイン)** をクリックし、 **[Hostnames] \(ホスト名)** をクリックします。 作成した CNAME レコードを追加するには、 **[+ Add hostname] \(ホスト名の追加)** をクリックします。

![[+ ホスト名の追加] ボタンが強調表示されているスクリーンショット。](./media/dns-custom-domain/figure1.png)

プロセスが完了したら、**nslookup** を実行して、名前解決が機能していることを検証します。

![図 1](./media/dns-custom-domain/finalnslookup.png)

カスタム ドメインを App Service にマッピングする方法について、詳しくは[既存のカスタム DNS 名を Azure Web Apps にマップする](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json)を参照してください。

アクティブな DNS 名を移行する方法については、「[Azure App Service へのアクティブな DNS 名の移行](../app-service/manage-custom-dns-migrate-domain.md)」を参照してください。

カスタム ドメインを購入する必要がある場合、App Service ドメインの詳細について[Buy a custom domain name for Azure Web Apps](../app-service/manage-custom-dns-buy-domain.md)を参照してください。

## <a name="blob-storage"></a>BLOB ストレージ

次の手順では、asverify の手法を使用して BLOB ストレージ アカウント用の CNAME レコードを構成する方法を説明します。 この手法はダウンタイムがないことを保証します。

**[Storage] \(ストレージ)**  >  **[Storage Accounts] \(ストレージ アカウント)** に移動し、ストレージ アカウントを選択し、 **[Custom domain] \(カスタム ドメイン)** をクリックします。 ステップ 2 の FQDN をメモします。この値は、最初の CNAME レコードを作成するために使用されます

![BLOB ストレージのカスタム ドメイン](./media/dns-custom-domain/blobcustomdomain.png)

DNS ゾーンに移動して **[+ Record set] \(レコード セットの追加)** をクリックします。 **[Add record set] \(レコード セットの追加)** ブレードで次の情報を入力し、 **[OK]** をクリックして作成します。


|プロパティ  |値  |説明  |
|---------|---------|---------|
|名前     | asverify.mystorageaccount        | この値とドメイン名ラベルを合わせたものが、カスタム ドメイン名の FQDN です。        |
|Type     | CNAME        | CNAME レコードを別名として使用します。        |
|TTL     | 1        | 1 時間には 1 を使用します        |
|TTL の単位     | 時間        | 時間数は時間の単位として使用されます         |
|エイリアス     | asverify.adatumfunctiona9ed.blob.core.windows.net        | 別名を作成している DNS 名。この例では、既定でストレージ アカウントに与えられる asverify.adatumfunctiona9ed.blob.core.windows.net という DNS 名です。        |

**[Storage] \(ストレージ)**  >  **[Storage Accounts] \(ストレージ アカウント)** をクリックしてストレージ アカウントに戻り、ストレージ アカウントを選択して **[Custom domain] \(カスタム ドメイン)** をクリックします。 作成した別名から asverify プレフィックスを除いたものをテキスト ボックスに入力し、 **[間接 CNAME 検証を使用する]** をオンにし、 **[保存]** をクリックします。 この手順が完了したら、DNS ゾーンに戻り、asverify プレフィックスなしで CNAME レコードを作成します。  それ以降は、cdnverify プレフィックスの付いた CNAME レコードを削除しても安全です。

![[カスタム ドメイン] ページを示しているスクリーンショット。](./media/dns-custom-domain/indirectvalidate.png)

`nslookup` を実行して DNS 解決を検証する

カスタム ドメインを BLOB ストレージ エンドポイントにマップする方法について、詳しくは[BLOB ストレージ エンドポイントのカスタム ドメイン名の構成](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)を参照してください。

## <a name="azure-cdn"></a>Azure CDN

次の手順では、cdnverify の手法を使用して、CDN エンドポイント用の CNAME レコードを構成する方法を説明します。 この手法はダウンタイムがないことを保証します。

**[ネットワーク]**  >  **[CDN Profiles]\(CDN プロファイル\)** の順に移動し、CDN プロファイルを選択します。

対象のエンドポイントを選択して **[+ Custom domain] \(カスタム ドメインの追加)** をクリックします。 **[Endpoint hostname] \(エンドポイントのホスト名)** の値は CNAME レコードが指すレコードであるため、これをメモします。

![CDN カスタム ドメイン](./media/dns-custom-domain/endpointcustomdomain.png)

DNS ゾーンに移動して **[+ Record set] \(レコード セットの追加)** をクリックします。 **[Add record set] \(レコード セットの追加)** ブレードで次の情報を入力し、 **[OK]** をクリックして作成します。

|プロパティ  |値  |説明  |
|---------|---------|---------|
|名前     | cdnverify.mycdnendpoint        | この値とドメイン名ラベルを合わせたものが、カスタム ドメイン名の FQDN です。        |
|Type     | CNAME        | CNAME レコードを別名として使用します。        |
|TTL     | 1        | 1 時間には 1 を使用します        |
|TTL の単位     | 時間        | 時間数は時間の単位として使用されます         |
|エイリアス     | cdnverify.adatumcdnendpoint.azureedge.net        | 別名を作成している DNS 名。この例では、既定でストレージ アカウントに与えられる cdnverify.adatumcdnendpoint.azureedge.net という DNS 名です。        |

**[Networking] \(ネットワーク)**  >  **[CDN Profiles] \(CDN プロファイル)** をクリックして CDN エンドポイントに戻り、CDN プロファイルを選択します。 **[+ Custom domain] \(カスタム ドメインの追加)** をクリックし、CNAME レコードの別名から cdnverify プレフィックスを除いたものを入力して **[Add] \(追加)** をクリックします。

この手順が完了したら、DNS ゾーンに戻り、cdnverify プレフィックスなしで CNAME レコードを作成します。  それ以降は、cdnverify プレフィックスの付いた CNAME レコードを削除しても安全です。 CDN の詳細と、中間登録手順なしでカスタム ドメインを構成する方法については、[カスタム ドメインへの Azure CDN コンテンツのマッピング](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json)を参照してください。

## <a name="next-steps"></a>次のステップ

[Azure でホストされているサービスの逆引き DNS を構成する](dns-reverse-dns-for-azure-services.md)方法について説明します。
