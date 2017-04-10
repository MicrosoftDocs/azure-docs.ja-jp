---
title: "Web アプリのカスタム DNS レコードの作成 | Microsoft Docs"
description: "Azure DNS を使用して Web アプリのカスタム ドメイン DNS レコードを作成する方法。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 6c16608c-4819-44e7-ab88-306cf4d6efe5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: dd020bf625510eb90af2e1ad19c155831abd7e75
ms.openlocfilehash: a2a429873c30f526a0de05d4018f53f3a83bbe28
ms.lasthandoff: 02/10/2017

---

# <a name="create-dns-records-for-a-web-app-in-a-custom-domain"></a>カスタム ドメインにおける Web アプリの DNS レコードの作成

Azure DNS を使用すると、Web アプリ用にカスタム ドメインをホストすることができます。 たとえば、Azure Web アプリを作成し、ユーザーが FQDN として contoso.com または www.contoso.com を使用してそのアプリにアクセスできるようにします。

これを行うには、2 つのレコードを作成する必要があります。

* contoso.com を指すルート "A" レコード
* その A レコードを指す www 名の "CNAME" レコード

Azure の Web アプリ用に A レコードを作成する場合、Web アプリの基になる IP アドレスが変更されると、A レコードを手動で更新する必要があることに注意してください。

## <a name="before-you-begin"></a>開始する前に

開始する前に、最初に Azure DNS に DNS ゾーンを作成し、レジストラーのゾーンを Azure DNS に委任する必要があります。

1. DNS ゾーンを作成するには、「 [DNS ゾーンの作成](dns-getstarted-create-dnszone.md)」の手順に従います。
2. DNS を Azure DNS に委任するには、「 [DNS ドメインの委任](dns-domain-delegation.md)」の手順に従います。

ゾーンを作成し、それを Azure DNS に委任したら、カスタム ドメインのレコードを作成できます。

## <a name="1-create-an-a-record-for-your-custom-domain"></a>1.カスタム ドメインの A レコードの作成

A レコードは、名前をその IP アドレスに対応付けるために使用されます。 次の例では、A レコードとして @ を IPv4 アドレスに割り当てます。

### <a name="step-1"></a>手順 1

A レコードを作成して $rs 変数に割り当てます。

```powershell
$rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600
```

### <a name="step-2"></a>手順 2.

割り当てられた $rs 変数を使用して、前に作成したレコード セット "@" に IPv4 の値を追加します。 割り当てられた IPv4 の値は、Web アプリの IP アドレスになります。

Web アプリの IP アドレスを確認するには、[Azure App Service でのカスタム ドメイン名の構成](../app-service-web/web-sites-custom-domain-name.md#vip)に関するページの手順に従います。

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address "<your web app IP address>"
```

### <a name="step-3"></a>手順 3.

レコード セットへの変更をコミットします。 レコード セットへの変更を Azure DNS にアップロードするには、次のように `Set-AzureRMDnsRecordSet` を使用します。

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="2-create-a-cname-record-for-your-custom-domain"></a>手順&2;.カスタム ドメインの CNAME レコードの作成

ドメインが既に Azure DNS で管理されている場合 ( [DNS ドメインの委任](dns-domain-delegation.md)に関するページを参照)、次の例を使用して contoso.azurewebsites.net の CNAME レコードを作成できます。

### <a name="step-1"></a>手順 1

PowerShell を開き、新しい CNAME レコード セットを作成して $rs 変数に割り当てます。 この例では、"Time To Live" が 600 秒で、CNAME という種類のレコード セットが "contoso.com" という名前の DNS ゾーンに作成されます。

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600
```

次の例は応答です。

```
Name              : www
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>手順 2.

CNAME レコード セットが作成されたら、Web アプリを指すエイリアス値を作成する必要があります。

既に割り当て済みの "$rs" 変数を使用すると、次の PowerShell コマンドを使用して、Web アプリ contoso.azurewebsites.net のエイリアスを作成できます。

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"
```

次の例は応答です。

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>手順 3.

`Set-AzureRMDnsRecordSet` コマンドレットを使用して変更をコミットします。

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

次のように nslookup を使用して "www.contoso.com" にクエリを実行することによって、レコードが正しく作成されたかどうかを検証できます。

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net
```

## <a name="create-an-awverify-record-for-web-apps"></a>Web アプリの "awverify" レコードの作成

Web アプリの A レコードを使用する場合は、カスタム ドメインを所有していることを確認できるように、確認プロセスを実行する必要があります。 この確認手順は、"awverify" という名前の特別な CNAME レコードを作成することで実行されます。 このセクションは A レコードのみに適用されます。

### <a name="step-1"></a>手順 1

"awverify" レコードを作成します。 下の例では、カスタム ドメインの所有権を確認するために、contoso.com の "awverify" レコードが作成されます。

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "myresourcegroup" -Name "awverify" -RecordType "CNAME" -Ttl 600
```

次の例は応答です。

```
Name              : awverify
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>手順 2.

レコード セット "awverify" が作成されたら、CNAME レコード セット エイリアスを割り当てます。 下の例では、CNAME レコード セット エイリアスを awverify.contoso.azurewebsites.net に割り当てます。

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"
```

次の例は応答です。

```
    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {awverify.contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>手順 3.

次のコマンドに示すように、 `Set-AzureRMDnsRecordSet cmdlet`コマンドレットを使用して変更をコミットします。

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="next-steps"></a>次のステップ

[App Service のカスタム ドメイン名の構成](../app-service-web/web-sites-custom-domain-name.md) に関するページの手順に進み、カスタム ドメインを使用するように Web アプリを構成します。

