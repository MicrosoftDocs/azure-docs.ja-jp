---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.openlocfilehash: 993b0e8cc5b1ec482b2f6041dfc970dc7e7409dd
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229291"
---
[Cognitive Services Vision Containers Request フォーム](https://aka.ms/VisionContainersPreview)に入力して送信し、コンテナーへのアクセスを要求する必要があります。 このフォームでは、ユーザー、会社、コンテナーを使用するユーザー シナリオに関する情報が要求されます。 フォームを送信すると、Azure Cognitive Services チームがそれをレビューして、プライベート コンテナー レジストリにアクセスするための条件を満たしていることを確認します。

> [!IMPORTANT]
> フォームでは、Microsoft アカウント (MSA) または Azure Active Directory (Azure AD) アカウントに関連付けられた電子メール アドレスを使用する必要があります。

要求が承認されると、資格情報を取得してプライベート コンテナー レジストリにアクセスする方法を説明する手順が記載された電子メールを受け取ります。

## <a name="log-in-to-the-private-container-registry"></a>プライベート コンテナー レジストリへのログイン

Cognitive Services コンテナーのプライベート コンテナー レジストリで認証を行うにはいくつかの方法があります。 [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) を使用したコマンドライン メソッドの使用を推奨しています。

次の例のように [docker login](https://docs.docker.com/engine/reference/commandline/login/) コマンドを使用して、Cognitive Services コンテナーのプライベート コンテナー レジストリである `containerpreview.azurecr.io` にログインします。 *\<username\>* と *\<password\>* を Azure Cognitive Services チームから受け取った資格情報に指定されているユーザー名とパスワードにそれぞれ置き換えます。

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

テキスト ファイルに資格情報をセキュリティ保護した場合は、そのテキスト ファイルの内容を `docker login` コマンドに連結することができます。 次の例に示すように、`cat` コマンドを使用します。 *\<passwordFile\>* は、パスワードを含むテキスト ファイルのパスと名前に置き換えてください。 *\<username\>* は、資格情報に指定されているユーザー名に置き換えてください。

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

