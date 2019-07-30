---
title: 高可用性のための Azure MFA Server の構成 - Azure Active Directory
description: 高可用性を提供する構成で Azure Multi-Factor Authentication Server の複数のインスタンスをデプロイします。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43154e428c3208f5d990688554407777d09f2f1b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056034"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>高可用性のための Azure Multi-Factor Authentication Server の構成

Azure サーバーの MFA のデプロイで高可用性を実現するには、複数の MFA サーバーをデプロイする必要があります。 このセクションでは、Azure MFS サーバーのデプロイで高可用性の目標を達成するための負荷分散設計について説明します。

> [!IMPORTANT]
> 2019 年 7 月 1 日より、Microsoft では新しいデプロイに対して MFA Server が提供されなくなります。 ユーザーからの多要素認証が必要な新しいお客様は、クラウドベースの Azure Multi-Factor Authentication を使用していただく必要があります。 7 月 1 日より前に MFA Server をアクティブ化した既存のお客様は、最新バージョンの今後の更新プログラムをダウンロードし、アクティブ化資格情報を通常どおり生成することができます。

## <a name="mfa-server-overview"></a>MFA サーバーの概要

Azure MFA サーバーのサービス アーキテクチャには、次の図に示すようにいくつかのコンポーネントが含まれています。

 ![MFA Server アーキテクチャ コンポーネント](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

MFA サーバーは、Azure Multi-Factor Authentication ソフトウェアがインストールされている Windows サーバーです。 MFA サーバーのインスタンスが機能するためには、Azure 上で MFA サービスによってアクティブ化される必要があります。 複数の MFA サーバーをオンプレミスでインストールできます。

既定では、最初にインストールされた MFA サーバーが Azure MFA サービスにアクティブ化された時点で、マスター MFA サーバーとなります。 マスター MFA サーバーには、PhoneFactor.pfdata データベースの書き込み可能コピーがあります。 MFA サーバーのインスタンスの後続のインストールは、下位サーバーと呼ばれます。 MFA 下位サーバーには、PhoneFactor.pfdata データベースのレプリケートされた読み取り専用コピーがあります。 MFA サーバーでは、リモート プロシージャ コール (RPC) を使って情報がレプリケートされます。 情報をレプリケートするには、すべての MFA サーバーを、ドメイン参加済みかスタンドアロンのどちらかにまとめる必要があります。

マスターおよび下位サーバーの両方の MFA サーバーは、2 要素認証が必要な場合に、MFA サービスと通信します。 たとえば、2 要素認証を必要とするアプリケーションにユーザーがアクセスしようとした場合、このユーザーは、まず、Active Directory (AD) などの ID プロバイダーに認証されます。

AD による認証が成功した後、MFA サーバーは MFA サービスと通信します。 MFA サーバーは、MFA サービスからの通知を待って、アプリケーションへのユーザー アクセスを許可または拒否します。

MFA マスター サーバーがオフラインになった場合、認証はまだ処理できますが、MFA データベースへの変更を必要とする操作は処理できません。 (例: ユーザーの追加、セルフサービス PIN の変更、ユーザー情報の変更、ユーザー ポータルへのアクセスなど)

## <a name="deployment"></a>Deployment

Azure MFA サーバーとその関連コンポーネントの負荷分散では、次の重要な点を考慮してください。

* **高可用性を実現するために RADIUS 標準を使うこと**。 Azure MFA サーバーを RADIUS サーバーとして使っている場合は、1 台の MFA サーバーをプライマリ RADIUS 認証対象として構成し、他の Azure MFA サーバーをセカンダリ認証対象として構成できます。 ただし、セカンダリ認証対象を認証する前にプライマリ認証対象で認証が失敗した場合は、タイムアウト期間の経過を待つ必要があるため、この方法によって高可用性を実現するのは実用的でない場合があります。 RADIUS クライアントと RADIUS サーバー (ここでは、RADIUS サーバーとして機能する Azure MFA サーバー) との間で RADIUS トラフィックを負荷分散するほうが効率的です。そうすれば、RADIUS クライアントを、それらが参照できる単一の URL で構成できるようになります。
* **MFA 下位サーバーを手動で昇格させる必要性**。 マスター Azure MFA サーバーがオフラインになった場合、セカンダリ Azure MFA サーバーは MFA の要求を処理し続けます。 ただし、マスター MFA サーバーが使用可能になるまで、管理者はユーザーの追加やMFA 設定の変更を行うことができず、ユーザーはユーザー ポータルを使った変更を行うことができません。 MFA の下位サーバーからマスターの役割への昇格は、必ず手動でのプロセスとなります。
* **コンポーネントの分離可能性**。 Azure MFA サーバーは、同じ Windows Server インスタンス上または異なるインスタンス上にインストールできるいくつかのコンポーネントで構成されています。 これらのコンポーネントには、ユーザー ポータル、モバイル アプリ Web サービス、ADFS アダプター (エージェント) が含まれます。 この分離可能性により、Web アプリケーション プロキシを使って境界ネットワークからユーザー ポータルとモバイル アプリ Web サーバーを公開できるようになります。 このような構成では、次の図に示すように、設計の全体的なセキュリティが向上します。 MFA ユーザー ポータルとモバイル アプリ Web サーバーは、HA 負荷分散構成でデプロイされる場合もあります。

   ![境界ネットワークでの MFA サーバー](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **SMS (単方向 SMS とも呼ばれる) を介したワンタイム パスワード (OTP) では、トラフィックが負荷分散される場合にスティッキー セッションを使う必要があります**。 単方向 SMS は認証オプションの 1 つで、OTP を含むテキスト メッセージが、MFA サーバーからユーザーに送信されます。 ユーザーは、プロンプト ウィンドウに OTP を入力して MFA チャレンジを完了します。 Azure MFA サーバーを負荷分散する場合は、初回認証要求を行った同じサーバーが、ユーザーから OTP メッセージを受信するサーバーである必要があります。別の MFA サーバーが OTP 返信を受信した場合、認証チャレンジは失敗します。 詳細については、「[One Time Password over SMS Added to Azure MFA Server (Azure MFA サーバーに追加された、SMS を介したワン タイム パスワード)](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server)」をご覧ください。
* **ユーザー ポータルとモバイル アプリ Web サービスの負荷分散デプロイには、スティッキー セッションが必要です**。 MFA ユーザー ポータルとモバイル アプリ Web サービスを負荷分散する場合は、各セッションが同じサーバーに留まるようにする必要があります。

## <a name="high-availability-deployment"></a>高可用性のデプロイ

次の図では、Azure MFA とそのコンポーネントについて、HA 負荷分散を行った実装のすべてを示し、参考のために ADFS を示します。

 ![Azure MFA サーバーの HA 実装](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

前の図の対応する番号が付けられた領域では、次の項目に注意してください。

1. 2 台の Azure MFA サーバー (MFA1 および MFA2) は、負荷分散されており (mfaapp.contoso.com)、静的ポート (4443) を使って PhoneFactor.pfdata データベースをレプリケートするように構成されています。 TCP ポート 443 経由で ADFS サーバーと通信できるように、各 MFA サーバー上に Web サービス SDK がインストールされています。 MFA サーバーは、ステートレスな負荷分散構成でデプロイされます。 ただし、SMS 経由で OTP を使う場合は、ステートフルな負荷分散を使う必要があります。
   ![Azure MFA サーバー - アプリケーション サーバーの HA](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > RPC では動的ポートが使われるため、RPC で使われる可能性がある動的ポートの範囲までファイアウォールを開くことはお勧めしません。 MFA アプリケーション サーバー**間**にファイアウォールがある場合は、下位サーバーとマスター サーバーとの間のレプリケーション トラフィックについては静的ポートで通信し、ファイアウォール上でそのポートを開くように、MFA サーバーを構成する必要があります。 ```Pfsvc_ncan_ip_tcp_port``` という ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` で DWORD レジストリ値を作成し、その値を、使用可能な静的ポートに設定すると、静的ポートを強制できます。 接続は必ず下位サーバー MFA サーバーからマスターに対して開始され、静的ポートはマスターのみで必要となりますが、いつでも下位サーバーをマスターに昇格できるため、すべての MFA サーバーで静的ポートを設定する必要があります。

2. 2 台のユーザー ポータル/MFA モバイル アプリ サーバー (MFA-UP-MAS1 および MFA-UP-MAS2) は、**ステートフル**な構成 (mfa.contoso.com) で負荷分散されます。 スティッキー セッションが MFA ユーザー ポータルとモバイル アプリ サービスの負荷分散の要件であることに注意してください。
   ![Azure MFA サーバー - ユーザー ポータルとモバイル アプリ サービスの HA](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. ADFS サーバー ファームは、負荷分散され、境界ネットワーク内の負荷分散された ADFS プロキシを介してインターネットに公開されます。 各 ADFS サーバーは、ADFS エージェントを使い、TCP ポート 443 経由で 1 つの負荷分散された URL (mfaapp.contoso.com) を使って Azure MFA サーバーと通信します。

## <a name="next-steps"></a>次の手順

* [Azure MFA Server のインストールと構成](howto-mfaserver-deploy.md)
