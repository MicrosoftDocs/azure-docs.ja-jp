---
title: Azure AD での Azure AD Connect クラウド プロビジョニングの前提条件
description: この記事では、クラウド プロビジョニングに必要な前提条件とハードウェア要件について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/06/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cf072ae9544cd479aeca02d9b9fcd670b8eb5fe
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226898"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Azure AD Connect クラウド プロビジョニングの前提条件
この記事では、ID ソリューションとして Azure Active Directory (Azure AD) クラウド プロビジョニングを選択して使用する方法に関するガイダンスを示します。



## <a name="cloud-provisioning-agent-requirements"></a>クラウド プロビジョニング エージェントの要件
Azure AD Connect クラウド プロビジョニングを使用するには、次のものが必要です。
    
- ゲスト ユーザーではない、Azure AD テナントのハイブリッド ID 管理者アカウント。
- Windows 2012 R2 以降を搭載した、プロビジョニング エージェント用のオンプレミス サーバー  このサーバーは、[Active Directory 管理層モデル](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)に基づいた階層 0 のサーバーである必要があります。
- オンプレミスのファイアウォールの構成

>[!NOTE]
>現在、プロビジョニング エージェントは、英語の言語サーバーにのみインストールできます。 英語以外のサーバーに英語の言語パックをインストールすることは有効な回避策ではなく、エージェントのインストールが失敗します。 

ここからは、これらの前提条件に関する詳細な手順について説明します。

### <a name="in-the-azure-active-directory-admin-center"></a>Azure Active Directory 管理センター

1. Azure AD テナントで、クラウド専用のハイブリッド ID 管理者アカウントを作成します。 その方法を採用すると、オンプレミス サービスが利用できなくなった場合にテナントの構成を管理できます。 [クラウド専用のハイブリッド ID 管理者アカウントを追加する](../fundamentals/add-users-azure-active-directory.md)方法について確認してください。 テナントからロックアウトされないようにするには、この手順を実行する必要があります。
1. 1 つ以上の[カスタム ドメイン名](../fundamentals/add-custom-domain.md)を Azure AD テナントに追加します。 ユーザーは、このドメイン名のいずれかを使用してサインインできます。

### <a name="in-your-directory-in-active-directory"></a>Active Directory のディレクトリ内

[IdFix ツール](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)を実行して、同期用にディレクトリ属性を準備します。

### <a name="in-your-on-premises-environment"></a>オンプレミスの環境の場合

1. 4 GB 以上の RAM と .NET 4.7.1 以降のランタイムを搭載した、Windows Server 2012 R2 以降が実行されているドメイン参加済みホスト サーバーを特定します。

1. ローカル サーバーの PowerShell 実行ポリシーを、Undefined または RemoteSigned に設定する必要があります。

1. サーバーと Azure AD の間にファイアウォールがある場合は、次の項目を構成します。
   - エージェントが次のポートを介して Azure AD に "*送信*" 要求を発行できるようにします。

        | ポート番号 | 用途 |
        | --- | --- |
        | **80** | TLS/SSL 証明書を検証する際に、証明書失効リスト (CRL) をダウンロードします。  |
        | **443** | サービスを使用したすべての送信方向の通信を処理します。 |
        | **8080** (省略可能) | ポート 443 が使用できない場合、エージェントは、ポート 8080 経由で 10 分おきにその状態をレポートします。 この状態は Azure AD ポータルに表示されます。 |
     
   - ご利用のファイアウォールが送信元ユーザーに応じて規則を適用している場合は、ネットワーク サービスとして実行されている Windows サービスを送信元とするトラフィックに対してこれらのポートを開放します。
   - ファイアウォールまたはプロキシで安全なサフィックスの指定が許可されている場合は、\*.msappproxy.net および \*.servicebus.windows.net への接続を追加します。 そうでない場合は、毎週更新される [Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)へのアクセスを許可します。
   - エージェントは、初期登録のために login.windows.net と login.microsoftonline.com にアクセスする必要があります。 これらの URL にもファイアウォールを開きます。
   - 証明書の検証のために、URL mscrl.microsoft.com:80、crl.microsoft.com:80、ocsp.msocsp.com:80、www\.microsoft.com:80 のブロックを解除します。 他の Microsoft 製品でもこれらの URL を証明書の検証に使用しているので、URL のブロックを既に解除している可能性もあります。

>[!NOTE]
> Windows Server Core へのクラウド プロビジョニング エージェントのインストールはサポートされていません。


### <a name="additional-requirements"></a>その他の要件
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>TLS の要件

>[!NOTE]
>トランスポート層セキュリティ (TLS) は、セキュリティで保護された通信を規定するプロトコルです。 TLS 設定を変更すると、フォレスト全体に影響します。 詳細については、「[Windows の WinHTTP における既定の安全なプロトコルとして TLS 1.1 および TLS 1.2 を有効化する更新プログラム](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)」を参照してください。

Azure AD Connect クラウド プロビジョニング エージェントをホストする Windows サーバーでは、インストールする前に TLS 1.2 を有効にする必要があります。

TLS 1.2 を有効にするには、次の手順に従います。

1. 次のレジストリ キーを設定します。
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. サーバーを再起動します。


## <a name="next-steps"></a>次のステップ 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド プロビジョニングとは](what-is-cloud-provisioning.md)