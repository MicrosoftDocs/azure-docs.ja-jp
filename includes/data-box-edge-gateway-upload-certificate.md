---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 216380cf7069468d13c4e533fc90b2596aa211c4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58125209"
---
適切な SSL 証明書により、暗号化された情報が適切なサーバーに確実に送信されます。 暗号化だけでなく、証明書により認証もできます。 デバイスの PowerShell インターフェイス経由で、信頼された独自の SSL 証明書をアップロードすることができます。

1. [PowerShell インターフェイスに接続する](#connect-to-the-powershell-interface)。
2. `Set-HcsCertificate` コマンドレットを使用して証明書をアップロードします。 入力を求められたら、次のパラメーターを指定します。

   - `CertificateFilePath` - *.pfx* 形式の証明書を含む共有へのパス。
   - `CertificatePassword` - 証明書を保護するために使用するパスワード。
   - `Credentials` - 証明書を含む共有にアクセスするためのユーザー名とパスワード。

     このコマンドレットの使用例を次に示します。

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credentials "Username/Password"
     ```

