---
title: "Azure MFA における&2; 段階認証について | Microsoft Docs"
description: "Multi-Factor Authentication とは、MFA を使用する理由、Multi-factor Authentication クライアントに関する詳細、使用可能なさまざまな認証方法とバージョン。 "
keywords: "MFA の概要, mfaの概要, mfa とは"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: c40d7a34-1274-4496-96b0-784850c06e9b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: e028130569a1e55834f68bde87f21f6f7505d41f
ms.openlocfilehash: 7a47c0278d42d62cc273c6f0671f0613671db4f3
ms.lasthandoff: 02/23/2017


---
# <a name="what-is-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication とは
2 段階認証は、複数の確認方法を要求することで、ユーザーのサインインとトランザクションにさらなる重要なセキュリティ レイヤーを追加する認証方法です。 これらは、次の確認方法のうち&2; つ以上を要求することで機能します。

* ユーザーが知っているもの (通常はパスワード)
* ユーザーが持っているもの (携帯電話など、簡単には複製できない信頼できるデバイス)
* ユーザー自身 (生体認証)

<center>![ユーザー名とパスワード](./media/multi-factor-authentication/pword.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![証明書](./media/multi-factor-authentication/phone.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![スマート フォン](./media/multi-factor-authentication/hware.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![スマート カード](./media/multi-factor-authentication/smart.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![仮想スマート カード](./media/multi-factor-authentication/vsmart.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![ユーザー名とパスワード](./media/multi-factor-authentication/cert.png)</center>

Azure Multi-Factor Authentication (MFA) は、Microsoft の&2; 段階認証ソリューションです。 Azure MFA では、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護することができます。 電話、テキスト メッセージ、モバイル アプリによる確認など、一連の照合方法を通じて確実な認証を行うことができます。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/WA-MFA-Overview/player]
>
>

## <a name="why-use-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication を使う理由
インターネットの接続人口は今やかつてない水準にまで増加しています。 スマート フォン、タブレット、ノート PC、デスクトップ PC など、インターネットへの接続手段は多様化し、常時接続していることが当たり前の時代となりました。 人々は、どこにいても、自分のアカウントやアプリケーションにアクセスすることができます。そのことが生産性とサービスの質の向上につながっているといえるでしょう。

Azure Multi-Factor Authentication は、認証手段の多層化によってユーザーを絶えず保護することができる、使いやすくスケーラブルで信頼性の高いソリューションです。

| ![使いやすい](./media/multi-factor-authentication/simple.png) | ![拡張性](./media/multi-factor-authentication/scalable.png) | ![常時保護](./media/multi-factor-authentication/protected.png) | ![信頼性](./media/multi-factor-authentication/reliable.png) |
|:---:|:---:|:---:|:---:|
| **使いやすい** |**拡張性** |**常時保護** |**信頼性** |

* **使いやすい** -Azure Multi-Factor Authentication は、簡単にセットアップして使用することができます。 ユーザーは、Azure Multi-Factor Authentication に付属している追加の保護を使用して自分のデバイスを管理できます。 多くの場合、セットアップは数回のクリックで簡単に行うことができます。
* **拡張性** - Azure Multi-Factor Authentication はクラウドの強みを活かし、オンプレミスの AD やカスタム アプリと連携します。 その保護の範囲は、ハイボリュームのミッション クリティカルなシナリオにまで広げることができます。
* **常時保護** - Azure Multi-Factor Authentication は、きわめて高い業界標準に基づく強力な認証手段です。
* **信頼性** - Azure Multi-Factor Authentication には、99.9% の可用性が確保されています。 確認要求を受信することも処理することもできなくなったときに、2 段階認証のサービスは利用不可と見なされます。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]


## <a name="next-steps"></a>次のステップ

- [Azure Multi-Factor Authentication のしくみ](multi-factor-authentication-how-it-works.md)を確認する

- [Azure Multi-Factor Authentication のさまざまなバージョンと使用方法](multi-factor-authentication-versions-plans.md)を確認する

