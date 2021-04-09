---
title: Azure Service Fabric サービスを AD ユーザーまたはグループとして実行する
description: Service Fabric の Windows スタンドアロン クラスターで、Active Directory ユーザーまたはグループとしてサービスを実行する方法を説明します。
ms.topic: conceptual
ms.date: 03/29/2018
ms.openlocfilehash: d4a7afc2ddb0f39014a7cf0fd006d7fe23673a95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91840729"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Active Directory ユーザーまたはグループとしてサービスを実行する
Windows Server のスタンドアロン クラスター上で、RunAs ポリシーを使用してサービスを Active Directory ユーザーまたはグループとして実行できます。  既定では、Service Fabric アプリケーションは、Fabric.exe プロセスを実行しているアカウントで実行されます。 異なるアカウントで実行中のアプリケーションは、共有のホスト環境にある場合でも、互いからより強固に保護されることになります。 これは、ドメイン内のオンプレミスの Active Directory を使用しており、Azure Active Directory (Azure AD) ではないことに注意してください。  また、[グループ管理サービス アカウント (gMSA)](service-fabric-run-service-as-gmsa.md) としてサービスを実行することもできます。

ドメイン ユーザーまたはグループを使用すると、アクセス許可が付与されている、ドメイン内の他のリソース (ファイル共有など) にアクセスできます。

次の例は、*TestUser* という Active Directory ユーザーと、*MyCert* という証明書を使用して暗号化されたドメイン パスワードを示しています。 `Invoke-ServiceFabricEncryptText` PowerShell コマンドを使用して、シークレット暗号化テキストを作成できます。 詳細については、[「Service Fabric アプリケーションでのシークレットの管理」](service-fabric-application-secret-management.md)をご覧ください。

ローカル コンピューターにアウトオブバンド方式でパスワードの暗号化を解除するには、証明書の秘密キーをデプロイする必要があります (Azure では Azure Resource Manager を使用します)。 これで、Service Fabric は、サービス パッケージをマシンにデプロイするときに、シークレットの暗号化を解除し、ユーザー名とこれらの資格情報で実行するように Active Directory による認証を実行できるようになります。

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```

> [!NOTE] 
> サービスに RunAs ポリシーを適用し、サービス マニフェストで HTTP プロトコルのエンドポイント リソースを宣言する場合は、**SecurityAccessPolicy** も指定する必要があります。  詳細については、[HTTP と HTTPS エンドポイントのセキュリティ アクセス ポリシーを割り当てる](service-fabric-assign-policy-to-endpoint.md)をご覧ください。 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
次の手順については、次の記事を参照してください。
* [アプリケーション モデルを理解する](service-fabric-application-model.md)
* [サービス マニフェストにリソースを指定する](service-fabric-service-manifest-resources.md)
* [アプリケーションをデプロイする](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
