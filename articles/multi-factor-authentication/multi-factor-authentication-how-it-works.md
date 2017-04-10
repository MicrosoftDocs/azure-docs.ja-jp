---
title: "Azure Multi-Factor Authentication - しくみ"
description: "Azure Multi-Factor Authentication を使えば、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護することが可能です。 2 番目の形式の認証を要求することで追加のセキュリティを提供し、次の一連の容易な確認オプションによって強力な認証を提供します。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtland
ms.assetid: d14db902-9afe-4fca-b3a5-4bd54b3d8ec5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: e028130569a1e55834f68bde87f21f6f7505d41f
ms.openlocfilehash: 9132e9bde7403e2868b92cb1149904d926ff5d8e
ms.lasthandoff: 02/23/2017


---
# <a name="how-azure-multi-factor-authentication-works"></a>Azure Multi-Factor Authentication のしくみ
多要素認証のセキュリティは、レイヤー アプローチによってもたらされます。 攻撃者にとって、多要素認証を侵害することはかなり困難です。 攻撃者にユーザーのパスワードを知られても、信頼できるデバイスを手に入れなければ役に立ちません。 ユーザーがデバイスを紛失した場合、そのデバイスを見つけた人は、ユーザーのパスワードを知らなければデバイスを使用することができません。

![追加のセキュリティ確認](./media/multi-factor-authentication-how-it-works/howitworks.png)

Azure Multi-Factor Authentication を使えば、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護することが可能です。  2 番目の形式の認証を要求することで追加のセキュリティを提供し、次の一連の容易な確認オプションによって強力な認証を提供します。

しくみの詳細については、次のビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Events/TechEd/Europe/2014/EM-B313/player]

## <a name="methods-available-for-two-step-verification"></a>2 段階認証で使用できる方法
ユーザーがサインインしようとすると、追加の確認がユーザーに対して行われます。  この&2; 番目の確認のために使用できる方法の一覧を次に示します。

| 確認方法 | Description |
| --- | --- |
| 音声通話 |ユーザーの登録済みの携帯電話が呼び出され、# 記号を押すか PIN を入力してサインインを確認するよう求めるメッセージが再生されます。 |
| テキスト メッセージ |6 桁のコードを含むテキスト メッセージがユーザーの携帯電話に送信されます。  確認プロセスを完了するには、このコードを入力します。 |
| モバイル アプリの通知 |ユーザーのスマートフォンに、モバイル アプリで [確認] を選択して確認を完了するよう求める確認要求が送信されます。 これは、プライマリの確認方法としてアプリの通知を選択した場合に発生します。  サインインしようとしていないときにこの通知を受信した場合、ユーザーはそれを不正アクセスとして報告できます。 |
| モバイル アプリの確認コード |ユーザーのスマートフォンで実行されているモバイル アプリには、30 秒ごとに変更される 6 桁の確認コードが表示されます。 ユーザーは最新のコードを確認し、サインイン ページでそのコードを入力して確認プロセスを完了します。 これは、プライマリの確認方法として確認コードを選択した場合に発生します。 |
| サード パーティの OATH トークン | サード パーティの確認方法を受け入れるように Azure Multi-Factor Authentication を構成できます。 |

Azure Multi-Factor Authentication では、クラウドとサーバーの両方で検証方法を選択できます。 つまり、ユーザーが使用できる方法 (電話、テキスト、アプリの通知、またはアプリのコード) を選択できます。 詳細については、「[選択可能な検証方法](multi-factor-authentication-whats-next.md#selectable-verification-methods)」をご覧ください。

## <a name="next-steps"></a>次のステップ

- [Azure Multi-Factor Authentication のさまざまなバージョンと使用方法](multi-factor-authentication-versions-plans.md)を確認する

- Azure MFA を[クラウドとオンプレミス](multi-factor-authentication-get-started.md)のどちらにデプロイするかを選択する
