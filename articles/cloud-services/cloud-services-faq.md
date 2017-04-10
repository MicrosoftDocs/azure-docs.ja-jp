---
title: "Azure Cloud Services のロールに関する FAQ | Microsoft Docs"
description: "Azure Cloud Services に関してよく寄せられる質問。 証明書、Web ロール、worker ロールについての一般的な質問に対する回答を示します。"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 7287cb1709b7c863cd046edfb995e23455398ec2
ms.lasthandoff: 03/25/2017


---
# <a name="cloud-services-faq"></a>Cloud Services に関する FAQ
この記事では、Microsoft Azure Cloud Services についてよく寄せられる質問 (FAQ) とその回答を紹介します。 Azure の価格およびサポートに関する一般的な情報については、「 [Azure サポートに関する FAQ](http://go.microsoft.com/fwlink/?LinkID=185083) 」も参照してください。 サイズについては、 [Cloud Services VM サイズのページ](cloud-services-sizes-specs.md) を参照してください。

## <a name="certificates"></a>証明書
### <a name="where-should-i-install-my-certificate"></a>証明書はどこにインストールすればいいですか?
* **My**  
  秘密キーを持つアプリケーション証明書 (\*.pfx、\*.p12)。
* **CA**  
  すべての中間証明書をこのストア内に保存します (ポリシー CA および下位 CA)。
* **ROOT**  
  ルート CA ストア。メインのルート CA 証明書をここに保存します。

### <a name="i-cant-remove-expired-certificate"></a>有効期限が切れた証明書を削除できません
Azure では、証明書の使用中にその証明書を削除することはできません。 証明書を使用するデプロイメントを削除するか、別の証明書または更新した証明書でデプロイメントを更新する必要があります。

### <a name="delete-an-expired-certificate"></a>有効期限切れの証明書を削除してください
証明書が使用されていない限りは、 [Remove-AzureCertificate](https://msdn.microsoft.com/library/azure/mt589145.aspx) PowerShell コマンドレットを使って証明書を削除できます。

### <a name="i-have-expired-certificates-named-windows-azure-service-management-for-extensions"></a>Windows Azure Service Management for Extensions という名前の証明書の期限が切れています
リモート デスクトップ拡張機能などの拡張機能がクラウド サービスに追加されるとかならず、これらの証明書が作成されます。 これらの証明書は、拡張機能のプライベート構成を暗号化および復号化するためだけに使用されます。 これらの証明書の有効期限が切れていても問題はありません。 有効期限の日付はチェックされません。

### <a name="certificates-i-have-deleted-keep-reappearing"></a>削除した証明書が再表示され続けます
そのような証明書が再表示され続ける原因は、ほとんどの場合、ご使用になっているツール (Visual Studio など) です。 証明書を使用しているツールに再接続するたびに、ツールが Azure に証明書を再アップロードします。

### <a name="my-certificates-keep-disappearing"></a>証明書が表示されません
仮想マシン インスタンスをリサイクルすると、ローカルのすべての変更は失われます。 ロールが起動するたびに、 [スタートアップ タスク](cloud-services-startup-tasks.md) を使用して、仮想マシンに証明書をインストールしてください。

### <a name="i-cannot-find-my-management-certificates-in-the-portal"></a>ポータルで管理証明書が見つかりません
[管理証明書](../azure-api-management-certs.md) は Azure クラシック ポータルでのみ使用できます。 現在の Azure ポータルでは、管理証明書は使用しません。 

### <a name="how-can-i-disable-a-management-certificate"></a>管理証明書を無効にするにはどうすればよいですか
[管理証明書](../azure-api-management-certs.md) を無効にすることはできません。 使用しなくなった場合は、Azure クラシック ポータルで削除できます。

### <a name="how-do-i-create-an-ssl-certificate-for-a-specific-ip-address"></a>特定の IP アドレスの SSL 証明書を作成するにはどうすればよいですか
[証明書を作成するためのチュートリアル](cloud-services-certs-create.md)の手順に従います。 DNS 名として IP アドレスを使用します。

## <a name="security"></a>セキュリティ
### <a name="disable-ssl-30"></a>SSL 3.0 の無効化
SSL 3.0 を無効にして TLS セキュリティを使用するには、次のブログの投稿に示されているスタートアップ タスクを作成します。https://azure.microsoft.com/en-us/blog/how-to-disable-ssl-3-0-in-azure-websites-roles-and-virtual-machines/

### <a name="add-nosniff-to-your-website"></a>Web サイトに **nosniff** を追加する
クライアントが MIME の種類をスニッフィングできないように、*web.config* ファイルに設定を追加します。

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

これは、IIS に設定として追加することもできます。 [一般的なスタートアップ タスク](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe)に関する記事を参照して、次のコマンドを使用します。

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="customize-iis-for-a-web-role"></a>IIS の Web ロールをカスタマイズする
[一般的なスタートアップ タスク](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe)に関する記事にある IIS スタートアップ スクリプトを使用します。

## <a name="scaling"></a>スケーリング
### <a name="i-cannot-scale-beyond-x-instances"></a>X 個のインスタンスを超えて拡張できない
Azure サブスクリプションには、使用できるコアの数に制限があります。 使用可能なすべてのコアを既に使用している場合、スケーリングは機能しません。 たとえば、コア数が 100 に制限されている場合、これは、クラウド サービスでは A1 サイズの仮想マシン インスタンスを 100 個、または A2 サイズの仮想マシン インスタンスを 50 個使用できることを意味します。

## <a name="networking"></a>ネットワーク
### <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>マルチ VIP クラウド サービスの IP アドレスを予約できません
まず、IP アドレスを予約しようとしている仮想マシン インスタンスが有効であることを確認してください。 次に、ステージング デプロイメントと運用環境デプロイメントの両方で予約済みの IP を使っていることを確認します。 **変更しない** でください。

## <a name="remote-desktop"></a>リモート デスクトップ
### <a name="how-do-i-remote-desktop-when-i-have-an-nsg"></a>NSG がある場合にリモート デスクトップを使用するには
ポート **20000** を転送するルールを NSG に追加します。

