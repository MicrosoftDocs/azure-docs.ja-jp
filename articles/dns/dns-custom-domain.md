---
title: "Azure DNS を Azure リソースと統合する | Microsoft Docs"
description: "Azure DNS を使用して Azure リソースに DNS を提供する方法を説明します。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: gwallace
ms.openlocfilehash: 41c1649bfff035bc641d7c1f5d7803cd105e8297
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Azure DNS を使用して Azure サービス用のカスタム ドメイン設定を提供する

Azure DNS は、カスタム ドメインをサポートする、または完全修飾ドメイン名 (FQDN) を持つ任意の Azure リソースに対して、カスタム ドメインの DNS を提供します。 たとえば、Azure Web アプリがあり、ユーザーが FQDN として contoso.com または www.contoso.com を使用してそのアプリにアクセスできるようにします。 この記事では、カスタム ドメインを使用するために Azure サービスと Azure DNS を構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

カスタム ドメインのために Azure DNS を使用するには、まずドメインを Azure DNS に委任する必要があります。 ネーム サーバーを委任のために構成する方法については、[Delegate a domain to Azure DNS](./dns-delegate-domain-azure-dns.md)を参照してください。 Azure DNS ゾーンにドメインを委任した後、必要な DNS レコードを構成できます。

[Azure Function App](#azure-function-app)、[Azure IoT](#azure-iot)、[パブリック IP アドレス](#public-ip-address)、[App Service (Web Apps)](#app-service-web-apps)、[BLOB ストレージ](#blob-storage)、および [Azure CDN](#azure-cdn) 用のバニティまたはカスタム ドメインを構成できます。

## <a name="azure-function-app"></a>Azure Function App

Azure Function App 用のカスタム ドメインを構成するために、Function App 自体での構成に加えて CNAME レコードが作成されます。
 
**[Other] \(その他)** > **[Function App]** に移動して Function App を選択します。 **[Platform features] \(プラットフォーム機能)** をクリックし、**[NETWORKING]** で **[Custom domains] \(カスタム ドメイン)** をクリックします。

![Function App ブレード](./media/dns-custom-domain/functionapp.png)

**[Custom domains] \(カスタム ドメイン)** ブレードの現在の URL をメモしてください。このアドレスは、作成される DNS レコードの別名として使用されます。

![カスタム ドメイン ブレード](./media/dns-custom-domain/functionshostname.png)

DNS ゾーンに移動して **[+ Record set] \(レコード セットの追加)** をクリックします。 **[Add record set] \(レコード セットの追加)** ブレードで次の情報を入力し、**[OK]** をクリックして作成します。

|プロパティ  |値  |Description  |
|---------|---------|---------|
|名前     | myfunctionapp        | この値とドメイン名ラベルを合わせたものが、カスタム ドメイン名の FQDN です。        |
|型     | CNAME        | CNAME レコードを別名として使用します。        |
|TTL     | 1        | 1 時間には 1 を使用します        |
|TTL の単位     | 時間        | 時間数は時間の単位として使用されます         |
|エイリアス     | adatumfunction.azurewebsites.net        | 別名を作成している DNS 名。この例では、既定で Function App によって提供される adatumfunction.azurewebsites.net という DNS 名です。        |

Function App に戻り、**[Platform features] \(プラットフォーム機能)** をクリックし、**[NETWORKING] \(ネットワーク)** で **[Custom domains] \(カスタム ドメイン)** をクリックし、**[Hostnames] \(ホスト名)** で **[+ Add hostname] \(ホスト名の追加)** をクリックします。

**[Add hostname] \(ホスト名の追加)** ブレードで、CNAME レコードを **[hostname] \(ホスト名)** テキスト フィールドに入力して **[Validate] \(検証)** をクリックします。 レコードが見つからなかった場合、**[Add hostname] \(ホスト名の追加)** ボタンが表示されます。 **[Add hostname] \(ホスト名の追加)** をクリックして別名を追加します。

![Function App のホスト名追加ブレード](./media/dns-custom-domain/functionaddhostname.png)

## <a name="azure-iot"></a>Azure IoT

Azure IoT には、サービス自体で必要なカスタマイズは何もありません。 IoT Hub でカスタム ドメインを使用するには、リソースを指示した CNAME レコードだけが必要です。

**[Internet of Things] \(モノのインターネット)** > **[IoT Hub]** に移動し、IoT Hub を選択します。 **[Overview] \(概要)** ブレードで、IoT Hub の FQDN をメモします。

![IoT hub blade](./media/dns-custom-domain/iot.png)

次に、DNS ゾーンに移動して **[+ Record set] \(レコード セットの追加)** をクリックします。 **[Add record set]\(レコード セットの追加)** ブレードで次の情報を入力し、**[OK]** をクリックして作成します。


|プロパティ  |値  |Description  |
|---------|---------|---------|
|名前     | myiothub        | この値とドメイン名ラベルを合わせたものが、IoT Hub の FQDN です。        |
|型     | CNAME        | CNAME レコードを別名として使用します。
|TTL     | 1        | 1 時間には 1 を使用します        |
|TTL の単位     | 時間        | 時間数は時間の単位として使用されます         |
|エイリアス     | adatumIOT.azure-devices.net        | 別名を作成している DNS 名。この例では、IoT Hub によって提供される adatumIOT.azure-devices.net というホスト名です。

レコードが作成されたら、`nslookup` を使用して CNAME レコードによる名前解決をテストします。

## <a name="public-ip-address"></a>パブリック IP アドレス

Application Gateway、Load Balancer、Cloud Service、Resource Manager VM、Classic VM などの、パブリック IP アドレスを使用するサービス用のカスタム ドメインを構成するために、CNAME レコードが使用されます。

**[Networking] \(ネットワーク)** > **[Public IP address] \(パブリック IP アドレス)** に移動し、パブリック IP リソースを選択して **[Configuration] \(構成)** をクリックします。 表示されている IP address をメモします。

![パブリック IP ブレード](./media/dns-custom-domain/publicip.png)

DNS ゾーンに移動して **[+ Record set] \(レコード セットの追加)** をクリックします。 **[Add record set] \(レコード セットの追加)** ブレードで次の情報を入力し、**[OK]** をクリックして作成します。


|プロパティ  |値  |Description  |
|---------|---------|---------|
|名前     | mywebserver        | この値とドメイン名ラベルを合わせたものが、カスタム ドメイン名の FQDN です。        |
|型     | A        | リソースは IP アドレスなので、A レコードを使用します。        |
|TTL     | 1        | 1 時間には 1 を使用します        |
|TTL の単位     | 時間        | 時間数は時間の単位として使用されます         |
|IP アドレス     | <your ip address>       | パブリック IP アドレス。|

![A レコードを作成する](./media/dns-custom-domain/arecord.png)

A レコードが作成されたら、`nslookup` を実行してレコードの解決を検証します。

![パブリック IP DNS 参照](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>App Service (Web Apps)

次の手順では、App Service Web アプリ用のカスタム ドメインを構成する方法を説明します。

**[Web & Mobile] \(Web とモバイル)** > **[App Service] \(アプリ サービス)** に移動し、カスタム ドメイン名を構成しているリソースを選択し、**[Custom domains] \(カスタム ドメイン)** をクリックします。

**[Custom domains] \(カスタム ドメイン)** ブレードの現在の URL をメモしてください。このアドレスは、作成される DNS レコードの別名として使用されます。

![カスタム ドメイン ブレード](./media/dns-custom-domain/url.png)

DNS ゾーンに移動して **[+ Record set] \(レコード セットの追加)** をクリックします。 **[Add record set] \(レコード セットの追加)** ブレードで次の情報を入力し、**[OK]** をクリックして作成します。


|プロパティ  |値  |Description  |
|---------|---------|---------|
|名前     | mywebserver        | この値とドメイン名ラベルを合わせたものが、カスタム ドメイン名の FQDN です。        |
|型     | CNAME        | CNAME レコードを別名として使用します。 リソースが IP アドレスを使用していた場合、A レコードが使用されます。        |
|TTL     | 1        | 1 時間には 1 を使用します        |
|TTL の単位     | 時間        | 時間数は時間の単位として使用されます         |
|エイリアス     | webserver.azurewebsites.net        | 別名を作成している DNS 名。この例では、既定で Web アプリに与えられる webserver.azurewebsites.net という DNS 名です。        |


![CNAME レコードを作成する](./media/dns-custom-domain/createcnamerecord.png)

カスタム ドメイン名が構成されているアプリ サービスに戻ります。 **[Custom domains] \(カスタム ドメイン)** をクリックし、**[Hostnames] \(ホスト名)** をクリックします。 作成した CNAME レコードを追加するには、**[+ Add hostname] \(ホスト名の追加)** をクリックします。

![図 1](./media/dns-custom-domain/figure1.png)

プロセスが完了したら、**nslookup** を実行して、名前解決が機能していることを検証します。

![図 1](./media/dns-custom-domain/finalnslookup.png)

カスタム ドメインを App Service にマッピングする方法について、詳しくは[既存のカスタム DNS 名を Azure Web Apps にマップする](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json)を参照してください。

カスタム ドメインを購入する必要がある場合、App Service ドメインの詳細について[Buy a custom domain name for Azure Web Apps](../app-service/custom-dns-web-site-buydomains-web-app.md)を参照してください。

## <a name="blob-storage"></a>BLOB ストレージ

次の手順では、asverify の手法を使用して BLOB ストレージ アカウント用の CNAME レコードを構成する方法を説明します。 この手法はダウンタイムがないことを保証します。

**[Storage] \(ストレージ)** > **[Storage Accounts] \(ストレージ アカウント)** に移動し、ストレージ アカウントを選択し、**[Custom domain] \(カスタム ドメイン)** をクリックします。 ステップ 2 の FQDN をメモします。この値は、最初の CNAME レコードを作成するために使用されます

![BLOB ストレージのカスタム ドメイン](./media/dns-custom-domain/blobcustomdomain.png)

DNS ゾーンに移動して **[+ Record set] \(レコード セットの追加)** をクリックします。 **[Add record set] \(レコード セットの追加)** ブレードで次の情報を入力し、**[OK]** をクリックして作成します。


|プロパティ  |値  |Description  |
|---------|---------|---------|
|名前     | asverify.mystorageaccount        | この値とドメイン名ラベルを合わせたものが、カスタム ドメイン名の FQDN です。        |
|型     | CNAME        | CNAME レコードを別名として使用します。        |
|TTL     | 1        | 1 時間には 1 を使用します        |
|TTL の単位     | 時間        | 時間数は時間の単位として使用されます         |
|エイリアス     | asverify.adatumfunctiona9ed.blob.core.windows.net        | 別名を作成している DNS 名。この例では、既定でストレージ アカウントに与えられる asverify.adatumfunctiona9ed.blob.core.windows.net という DNS 名です。        |

**[Storage] \(ストレージ)** > **[Storage Accounts] \(ストレージ アカウント)** をクリックしてストレージ アカウントに戻り、ストレージ アカウントを選択して **[Custom domain] \(カスタム ドメイン)** をクリックします。 作成した別名から asverify プレフィックスを除いたものをテキスト ボックスに入力し、[**Use indirect CNAME] \(**間接 CNAME を使用) 検証をチェックし、**[Save] \(保存)** をクリックします。 この手順が完了したら、DNS ゾーンに戻り、asverify プレフィックスなしで CNAME レコードを作成します。  それ以降は、cdnverify プレフィックスの付いた CNAME レコードを削除しても安全です。

![BLOB ストレージのカスタム ドメイン](./media/dns-custom-domain/indirectvalidate.png)

`nslookup` を実行して DNS 解決を検証する

カスタム ドメインを BLOB ストレージ エンドポイントにマップする方法について、詳しくは[BLOB ストレージ エンドポイントのカスタム ドメイン名の構成](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)を参照してください。

## <a name="azure-cdn"></a>Azure CDN

次の手順では、cdnverify の手法を使用して、CDN エンドポイント用の CNAME レコードを構成する方法を説明します。 この手法はダウンタイムがないことを保証します。

**[Networking] \(ネットワーク)** > **[CDN Profiles] \(CDN プロファイル)** に移動し、CDN プロファイルを選択し、**[General] \(全般)** の **[Endpoints] \(エンドポイント)** をクリックします。

対象のエンドポイントを選択して **[+ Custom domain] \(カスタム ドメインの追加)** をクリックします。 **[Endpoint hostname] \(エンドポイントのホスト名)** の値は CNAME レコードが指すレコードであるため、これをメモします。

![CDN カスタム ドメイン](./media/dns-custom-domain/endpointcustomdomain.png)

DNS ゾーンに移動して **[+ Record set] \(レコード セットの追加)** をクリックします。 **[Add record set] \(レコード セットの追加)** ブレードで次の情報を入力し、**[OK]** をクリックして作成します。

|プロパティ  |値  |Description  |
|---------|---------|---------|
|名前     | cdnverify.mycdnendpoint        | この値とドメイン名ラベルを合わせたものが、カスタム ドメイン名の FQDN です。        |
|型     | CNAME        | CNAME レコードを別名として使用します。        |
|TTL     | 1        | 1 時間には 1 を使用します        |
|TTL の単位     | 時間        | 時間数は時間の単位として使用されます         |
|エイリアス     | cdnverify.adatumcdnendpoint.azureedge.net        | 別名を作成している DNS 名。この例では、既定でストレージ アカウントに与えられる cdnverify.adatumcdnendpoint.azureedge.net という DNS 名です。        |

**[Networking] \(ネットワーク)** > **[CDN Profiles] \(CDN プロファイル)** をクリックして CDN エンドポイントに戻り、CDN プロファイルを選択します。 **[+ Custom domain] \(カスタム ドメインの追加)** をクリックし、CNAME レコードの別名から cdnverify プレフィックスを除いたものを入力して **[Add] \(追加)** をクリックします。

この手順が完了したら、DNS ゾーンに戻り、cdnverify プレフィックスなしで CNAME レコードを作成します。  それ以降は、cdnverify プレフィックスの付いた CNAME レコードを削除しても安全です。 CDN の詳細と、中間登録手順なしでカスタム ドメインを構成する方法については、[カスタム ドメインへの Azure CDN コンテンツのマッピング](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json)を参照してください。

## <a name="next-steps"></a>次のステップ

[Azure でホストされているサービスの逆引き DNS を構成する](dns-reverse-dns-for-azure-services.md)方法について説明します。