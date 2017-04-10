---
title: "Azure RemoteApp でのアプリとリソースの保護 | Microsoft Docs"
description: "Azure RemoteApp でアプリとリソースをロックダウンする方法について説明します"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 7fbade87-a453-426d-bfa5-c72227ea83cd
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: e4d94d3f9736378d93e93be6645ed04ade763ca3
ms.openlocfilehash: fb524efc41b1eaa36319726b3b8b534857e2afc7


---
# <a name="secure-apps-and-resources-in-azure-remoteapp"></a>Azure RemoteApp でのアプリとリソースの保護
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

Azure RemoteApp には、一元管理された Windows アプリにユーザーがアクセスするための機能が用意されており、ユーザーが実行できることと、実行できないことを制御できます。  これは、管理されていないデバイス (個人の Macbook など) からユーザーが接続しているときに、そのユーザーのアクセスまたは操作を制御する必要がある場合に特に便利です。

たとえば、ユーザー認証に Active Directory を使用している場合、ユーザーがアプリからデータをコピーできないようにするには、ユーザーによるデータ コピーをブロックするようにリモート デスクトップのグループ ポリシーを構成します。

また、コレクション内の特定のアプリに対して、インターネット アクセスをブロックする必要があるときにも使用できます。 この場合は、コレクションのイメージを作成するときに、アクセスをブロックする Windows ファイアウォール ルールを作成します。

## <a name="implementation-options"></a>実装オプション
  重要な実装オプションを次に示します。これらのオプションは、必要に応じて個別に、または組み合わせて使用できます。

1. RemoteApp コレクションがドメインに参加している場合は、任意の[グループ ポリシー](https://technet.microsoft.com/library/cc725828.aspx)を適用できます (ただし、[こちら](../azure-subscription-service-limits.md)に記載されているアイドル ポリシーおよび切断時のタイムアウト ポリシーを除く)。
2. (コレクションがドメインに参加していない場合、または AD に適切な権限がない場合は) グループ ポリシーの代わりに、 [ローカル ポリシー](https://technet.microsoft.com/library/cc775702.aspx) をテンプレート イメージとして構成できます。  2 つのポリシーが競合している場合は、グループ ポリシーがローカル ポリシーに優先することに注意してください。
3. OS/アプリの設定の中にはポリシーで構成できないものがありますが、これはテンプレート イメージを構成しているときに、[RegEdit](remoteapp-hybridtrouble.md) ツール を使用してレジストリで構成できます。
4. [Windows ファイアウォール](http://windows.microsoft.com/en-US/windows-8/Windows-Firewall-from-start-to-finish) を使用して、アプリが実行されているコンピューターにおけるネットワーク アクセスを制御できます。 ここで定義されている URL とポートをブロックしないようにするだけです。
5. [AppLocker](https://technet.microsoft.com/library/hh831440.aspx) を使用して、ユーザーがどのアプリケーションおよびファイルを実行できるかを制御できます。 たとえば、あなたが発行していないアプリケーションでも、あなたがコレクションを作成するときに使用したイメージでそのアプリケーションを使用できれば、経験豊富なユーザーは、そのアプリケーションの実行方法を見つけ出すことができます。AppLocker はこれをブロックできます。

## <a name="detailed-information"></a>詳細情報
* 次の RDS ポリシーは、多くの場合、非常に役に立ちます。
  * [デバイスとリソースのリダイレクト](https://technet.microsoft.com/library/ee791794.aspx)
  * [プリンターのリダイレクト](https://technet.microsoft.com/library/ee791784.aspx)
  * [プロファイル](https://technet.microsoft.com/library/ee791865.aspx)。
* RemoteApp PowerShell モジュールによるリダイレクトの構成 ([こちら](remoteapp-redirection.md)を参照) は、クライアント コンピューターによるポリシーの適用を信頼して行われます。このため、セキュリティが主な目的の場合は、テンプレート イメージのローカル ポリシーまたはグループ ポリシーを使用してポリシーを適用します。
* [Windows Server 2012 R2 ポリシー](https://technet.microsoft.com/library/hh831791.aspx)。
* [Office 2013 のポリシー](https://technet.microsoft.com/library/cc178969.aspx) ([Office ツールバーのカスタマイズ方法](https://technet.microsoft.com/library/cc179143.aspx)を含む)。




<!--HONumber=Dec16_HO2-->


