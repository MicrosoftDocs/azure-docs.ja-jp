---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448627"
---
適切な SSL 証明書により、暗号化された情報が適切なサーバーに確実に送信されます。 暗号化だけでなく、証明書により認証もできます。 デバイスの PowerShell インターフェイス経由で、信頼された独自の SSL 証明書をアップロードすることができます。

1. [PowerShell インターフェイスに接続する](#connect-to-the-powershell-interface)。
2. `Set-HcsCertificate` コマンドレットを使用して証明書をアップロードします。 入力を求められたら、次のパラメーターを指定します。

   - `CertificateFilePath` - *.pfx* 形式の証明書を含む共有へのパス。
   - `CertificatePassword` - 証明書を保護するために使用するパスワード。
   - `Credentials` - 証明書を含む共有にアクセスするためのユーザー名。 入力を求められたら、ネットワーク共有へのパスワードを指定します。

     このコマンドレットの使用例を次に示します。

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

