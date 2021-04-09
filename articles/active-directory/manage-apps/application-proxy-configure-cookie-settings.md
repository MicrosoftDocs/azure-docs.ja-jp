---
title: アプリケーション プロキシの Cookie 設定 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) には、アプリケーション プロキシ経由でオンプレミスのアプリケーションにアクセスするためのアクセス権およびセッション Cookie があります。 この記事では、Cookie 設定を使用および構成する方法について説明します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 01/16/2019
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62afe97b44f45bc0b7aa12b33b6a65dd94ecf095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99252204"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Azure Active Directory でオンプレミスのアプリケーションにアクセスするための Cookie 設定

Azure Active Directory (Azure AD) には、アプリケーション プロキシ経由でオンプレミスのアプリケーションにアクセスするためのアクセス権およびセッション Cookie があります。 アプリケーション プロキシの Cookie 設定を使用する方法について説明します。 

## <a name="what-are-the-cookie-settings"></a>Cookie 設定の概要

[アプリケーション プロキシ](application-proxy.md)は、次のアクセス権およびセッション Cookie 設定を使用します。

| Cookie 設定 | Default | 説明 | Recommendations |
| -------------- | ------- | ----------- | --------------- |
| [HTTP 専用 Cookie を使用する] | **いいえ** | **[はい]** を指定すると、アプリケーション プロキシは HTTP 応答ヘッダーに HTTPOnly フラグを含めることができます。 このフラグにより、追加のセキュリティに関する利点が提供されます。たとえば、クライアント側のスクリプト (CSS) による Cookie のコピーや変更が防止されます。<br></br><br></br>HTTP 専用の設定がサポートされる前、アプリケーション プロキシは、変更から保護するために Cookie を暗号化し、セキュリティ保護された TLS チャネル経由で送信していました。 | 追加のセキュリティに関する利点があるため、**[はい]** を使用します。<br></br><br></br>セッション Cookie へのアクセス権が必要なクライアントまたはユーザー エージェントには **[いいえ]** を使用します。 たとえば、アプリケーション プロキシ経由でリモート デスクトップ ゲートウェイ サーバーに接続する RDP または MTSC クライアントには **[いいえ]** を使用します。|
| [Use Secure Cookie] (セキュリティで保護された Cookie を使用する) | **いいえ** | **[はい]** を指定すると、アプリケーション プロキシは HTTP 応答ヘッダーに Secure フラグを含めることができます。 セキュリティで保護された Cookie は、HTTPS などの TLS でセキュリティ保護されたチャネル経由で Cookie を送信することによってセキュリティを拡張します。 これにより、Cookie のクリア テキストでの送信のために、Cookie が承認されていないパーティーによって観察されることが防止されます。 | 追加のセキュリティに関する利点があるため、**[はい]** を使用します。|
| [Use Persistent Cookie] (永続的な Cookie を使用する) | **いいえ** | **[はい]** を指定すると、アプリケーション プロキシは、Web ブラウザーが閉じられても期限切れにならないようにアクセス Cookie を設定できます。 この永続性は、アクセス トークンの期限が切れるか、またはユーザーが永続的な Cookie を手動で削除するまで持続します。 | ユーザーが認証されたままになることに関連したセキュリティ上のリスクがあるため、**[いいえ]** を使用します。<br></br><br></br>**[はい]** は、プロセス間で Cookie を共有できない古いアプリケーションでのみ使用することをお勧めします。 永続的な Cookie を使用するのではなく、プロセス間での Cookie の共有を処理するようにアプリケーションを更新する方が適切です。 たとえば、永続的な Cookie は、ユーザーが SharePoint サイトからエクスプローラー ビューで Office ドキュメントを開けるようにするために必要になることがあります。 永続的な Cookie がない場合、ブラウザー、エクスプローラー プロセス、および Office プロセスの間でアクセス Cookie が共有されていないと、この操作は失敗する可能性があります。 |

## <a name="samesite-cookies"></a>SameSite Cookie
Chrome バージョン 80 以降、ひいては Chromium を利用しているブラウザーでは、[SameSite](https://web.dev/samesite-cookies-explained) 属性を指定していない Cookie は、**SameSite=Lax** が設定されているものとして扱われます。 SameSite 属性は、同一サイト コンテキストに Cookie を制限する方法を宣言します。 Lax に設定されている場合、Cookie は、同一サイト要求またはトップレベルのナビゲーションにのみ送信されます。 ただし、アプリケーション プロキシでは、セッション中にユーザーを適切にサインインしたままにするために、これらの Cookie をサードパーティのコンテキストで保持する必要があります。 このため、この変更による悪影響を防ぐために、アプリケーション プロキシ アクセスとセッション Cookie を更新しています。 更新内容は以下のとおりです。

* **SameSite** 属性を **None** に設定する。 これにより、アプリケーション プロキシ アクセス Cookie およびセッション Cookie をサードパーティのコンテキストで適切に送信できます。
* **[セキュリティで保護された Cookie を使用します]** 設定の既定値として **[はい]** を使用するように設定する。 また、Chrome では、Cookie に Secure フラグを指定する必要があり、そうでない場合は拒否されます。 この変更は、アプリケーション プロキシ経由で公開されるすべての既存のアプリケーションに適用されます。 アプリケーション プロキシ アクセス Cookie は、常に Secure に設定され、HTTPS 経由でのみ送信されることに注意してください。 この変更は、セッション Cookie にのみ適用されます。

アプリケーション プロキシ Cookie に対するこれらの変更は、Chrome 80 のリリース日の前に、今後数週間にわたってロールアウトされます。

さらに、バックエンド アプリケーションに、サードパーティ コンテキストで使用できるようにする必要がある Cookie がある場合は、これらの Cookie に対して SameSite=None を使用するようにアプリケーションを変更することで、明示的にオプトインする必要があります。 アプリケーション プロキシは、Set-Cookie ヘッダーをその URL に変換し、バックエンド アプリケーションによって設定されたそれらの Cookie の設定を優先します。



## <a name="set-the-cookie-settings---azure-portal"></a>Cookie 設定を設定する - Azure Portal
Azure Portal を使用して Cookie 設定を設定するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 
2. **[Azure Active Directory]**  > **[エンタープライズ アプリケーション]**  > **[すべてのアプリケーション]** に移動します。
3. Cookie 設定を有効にするアプリケーションを選択します。
4. **[アプリケーション プロキシ]** をクリックします。
5. **[追加設定]** で、Cookie 設定を **[はい]** または **[いいえ]** に設定します。
6. **[保存]** をクリックして変更を保存します。 

## <a name="view-current-cookie-settings---powershell"></a>現在の Cookie 設定を表示する - PowerShell

アプリケーションの現在の Cookie 設定を確認するには、次の PowerShell コマンドを使用します。  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Cookie 設定を設定する - PowerShell

次の PowerShell コマンドにおいて、```<ObjectId>``` はアプリケーションの ObjectId です。 

**HTTP 専用 Cookie** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**セキュリティで保護された Cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**永続 Cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
