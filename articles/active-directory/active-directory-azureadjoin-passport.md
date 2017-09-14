---
title: "Windows Hello for Business および Azure AD 経由でのパスワードを使用しない ID の認証 | Microsoft Docs"
description: "Windows Hello for Business の概要と、Windows Hello for Business のデプロイに関する詳細を示します。"
services: active-directory
documentationcenter: 
author: femila
manager: femila
editor: 
tags: azure-classic-portal
ms.assetid: f907bb90-8776-46ca-9e12-279949af66ff
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 0b10fc7c51d9fee306b0e71d405f0fd7ec7a6816
ms.openlocfilehash: 9270df5e880cc9a512ae673c1c25449376d8a7b4
ms.contentlocale: ja-jp
ms.lasthandoff: 02/23/2017


---
# <a name="authenticating-identities-without-passwords-through-windows-hello-for-business"></a>Windows Hello for Business 経由でのパスワードを使用しない ID の認証
パスワードだけで認証する現在の方法は、ユーザーの安全を保つうえで十分ではありません。 ユーザーは、パスワードを再利用したり、忘れたりすることがあります。 パスワードは侵害やフィッシングの恐れがあり、解読されたり推測されることもあります。 また、パスワードは、覚えるのが難しく、“[pass the hash](https://technet.microsoft.com/dn785092.aspx)” のような攻撃の対象にもなります。

## <a name="about-windows-hello-for-business"></a>Windows Hello for Business について
Windows Hello for Business は、組織とコンシューマー向けの、パスワードを超える秘密/公開キーまたは証明書ベースの認証方式です。 この認証方法はキーの組の資格情報に依存します。この資格情報はパスワードの代わりに使用でき、侵害、盗難、フィッシングに対する耐性があります。

 Windows Hello for Business を使用すると、ユーザーは Microsoft アカウント、Windows Server Active Directory アカウント、Microsoft Azure Active Directory (Azure AD) アカウントのほか、Fast IDentity Online (FIDO) 認証をサポートする Microsoft 以外のサービスに対する認証を行うことができます。 Windows Hello for Business の登録の際に 2 段階の初期検証を行った後、Windows Hello for Business がユーザーのデバイスに設定され、ユーザーがジェスチャ (Windows Hello または PIN) を設定します。 ユーザーは ID の検証のため、そのジェスチャを提供します。 その後、Windows が Windows Hello for Business を使ってユーザーを認証し、保護されたリソースとサービスにアクセスできるようにします。

秘密キーは、PIN、生体認証、リモート デバイス (ユーザーがデバイスへのサインインに使用したスマート カードなど) などの "ユーザー ジェスチャ" を通じてのみ使用可能になります。 この情報は、証明書または非対称のキーの組にリンクされます。 デバイスにトラステッド プラットフォーム モジュール (TPM) チップが搭載されている場合、秘密キーはハードウェアで証明されます。 秘密キーはデバイスの外部に移動されることはありません。

公開キーは、Azure Active Directory と Windows Server Active Directory (オンプレミスの場合) に登録されます。 ID プロバイダー (IDP) は、ユーザーの公開キーを秘密キーにマップしてユーザーを検証し、ワンタイム パスワード (OTP)、PhoneFactor、その他の通知メカニズムを使ってサインイン情報を提供します。

## <a name="why-enterprises-should-adopt-windows-hello-for-business"></a>エンタープライズに Windows Hello for Business 導入をお勧めする理由
Windows Hello for Business を有効にすると、次の操作によってエンタープライズのリソースのセキュリティを高めることができます。

* ハードウェア推奨オプションで Windows Hello for Business をセットアップします。 つまり、キーは TPM 1.2 または TPM 2.0 で生成されます (使用できる場合)。 TPM が使用できない場合は、ソフトウェアがキーを生成します。
* PIN の複雑さと長さ、および組織で Hello の使用が有効であるかどうかを定義します。
* 証明書ベースの信頼を使って、スマート カードのようなシナリオをサポートするように Windows Hello for Business を構成します。

## <a name="how-windows-hello-for-business-works"></a>Windows Hello for Business の動作
1. キーが TPM によってハードウェアで、またはソフトウェアで生成されます。 多くのデバイスには、暗号化キーをデバイスに統合してハードウェアをセキュリティで保護する、組み込みの TPM チップが搭載されています。 TPM 1.2 または TPM 2.0 がキーを生成します。またはその生成されたキーから証明書が作成されます。
2. TPM がこれらのハードウェア バインド キーを証明します。
3. 1 つのロック解除ジェスチャによって、デバイスのロックが解除されます。 デバイスがドメインまたは Azure AD に参加している場合、このジェスチャを使用して複数のリソースにアクセスできます。

## <a name="how-the-windows-hello-for-business-lifecycle-works"></a>Windows Hello for Business のライフサイクルの仕組み
![Windows Hello for Business のライフサイクル](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

前の図は、秘密/公開キーの組と、ID プロバイダーによる検証を示しています。 これらの各手順について、以下で詳しく説明します。

1. ユーザーが組み込みの複数の証明方法 (ジェスチャ、物理スマート カード、多要素認証) を通じて自分の ID を証明し、Azure Active Directory、オンプレミスの Active Directory などの ID プロバイダー (IDP) にこの情報を送信します。
2. デバイスが、キーを作成し、キーを証明した後、このキーの公開部分を取り出してステーションのステートメントをアタッチします。署名後 IDP に送信して、このキーを登録します。
3. キーの公開部分が IDP に登録されるとすぐに、IDP はデバイスにチャレンジを返信して、キーの秘密部分に署名するよう要求します。
4. 次に、IDP は、ユーザーとデバイスが保護されたリソースにアクセスできるようにする認証トークンを検証し、発行します。 IDP は、クロスプラットフォーム アプリを作成するか、または (JavaScript/Webcrypto API を使用して) ブラウザーのサポートを使用して、ユーザーの Windows Hello for Business の資格情報を作成および使用します。

## <a name="the-deployment-requirements-for-windows-hello-for-business"></a>Windows Hello for Business のデプロイ要件
### <a name="at-the-enterprise-level"></a>エンタープライズ レベル
* エンタープライズが Azure サブスクリプションを持っていること。

### <a name="at-the-user-level"></a>ユーザー レベル
* ユーザーのコンピューターで、Windows 10 Professional または Enterprise が実行されていること。

詳細なデプロイの手順については、[組織での Windows Hello for Business の有効化](active-directory-azureadjoin-passport-deployment.md)に関するページをご覧ください。

## <a name="additional-information"></a>追加情報
* [エンタープライズ向け Windows 10: デバイスを仕事に使用する方法](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory Join を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-user-upgrade.md)
* [Azure AD Join の使用シナリオについて](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 エクスペリエンスのためのドメイン参加済みデバイスの Azure AD への接続](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD Join の設定](active-directory-azureadjoin-setup.md)


