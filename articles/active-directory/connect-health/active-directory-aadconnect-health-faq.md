---
title: "Azure Active Directory Connect Health の FAQ | Microsoft Docs"
description: "この FAQ は、Azure AD Connect Health について寄せられる質問とその回答です。 サービスの課金モデル、機能、制限、サポートなど、その使用に関して多く寄せられる質問を取り上げています。"
services: active-directory
documentationcenter: 
author: billmath
manager: samueld
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 6ad2194a71e0d36ba4a0b9a46ca6dbcd58b619ff
ms.openlocfilehash: 06a8b79f0740e902bb7f9412b449a98b2f0167ea
ms.lasthandoff: 02/16/2017

---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Azure AD Connect Health についてよく寄せられる質問
この記事には、Azure Active Directory (Azure AD) Connect Health に関してよく寄せられる質問 (FAQ) に対する回答が記載されています。 これらの FAQ では、課金モデル、機能、制限、サポートなど、サービスの使用方法に関する質問を取り上げています。

## <a name="general-questions"></a>一般的な質問
**Q: 複数の Azure AD ディレクトリを管理しています。どのようにすれば Azure Active Directory Premium のディレクトリに切り替えることができますか。**

Azure AD テナントを切り替えるには、現在サインインしている**ユーザー名**を右上隅で選択して、適切なアカウントを選択します。 ここにアカウントが表示されていない場合は、**[サインアウト]** を選択し、Azure Active Directory Premium が有効になっているディレクトリのグローバル管理者資格情報を使用してサインインします。

**Q: Azure AD Connect Health では、どのバージョンの ID の役割がサポートされていますか。**

次の表は、役割と、サポートされているオペレーティング システムのバージョンの一覧です。

|役割| オペレーティング システム/バージョン|
|--|--|
|Active Directory フェデレーション サービス (AD FS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|
|Azure AD Connect | バージョン 1.0.9125 以上|
|Active Directory Domain Services (AD DS)| <ul> <li> Windows Server 2008 R2 </li><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> </ul>|

サービスが提供する機能は、役割とオペレーティング システムによって異なる場合があることに注意してください。 つまり、すべての機能をすべてのオペレーティング システムのバージョンで利用できるとは限りません。 詳しくは、機能の説明をご覧ください。

**Q: インフラストラクチャを監視するには、いくつのライセンスが必要ですか。**

* 最初の Connect Health エージェントに、少なくとも&1; つの Azure AD Premium ライセンスが必要です。
* 追加登録されるエージェントごとに、25 個の追加 Azure AD Premium ライセンスが必要です。
* エージェントの数は、監視対象のすべての役割 (AD FS、Azure AD Connect、AD DS) で登録されるエージェントの合計数に相当します。

ライセンスの情報については、[Azure AD の料金に関するページ](https://aka.ms/aadpricing)もご覧ください。

例:

| 登録されているエージェント | 必要なライセンスの数 | 監視構成の例 |
| ------ | --------------- | --- |
| 1 | 1 | Azure AD Connect サーバー ×&1; |
| 2 | 26| Azure AD Connect サーバー × 1、ドメイン コントローラー × 1 |
| 3 | 51 | Active Directory フェデレーション サービス (AD FS) サーバー × 1、AD FS プロキシ × 1、ドメイン コントローラー × 1 |
| 4 | 76 | AD FS サーバー × 1、AD FS プロキシ × 1、ドメイン コントローラー × 2 |
| 5 | 101 | Azure AD Connect サーバー × 1、AD FS サーバー × 1、AD FS プロキシ × 1、ドメイン コントローラー × 2 |


## <a name="installation-questions"></a>インストールに関する質問

**Q: 各サーバーに Azure AD Connect Health エージェントをインストールすると、どのような影響があるでしょうか。**

Microsoft Azure AD Connect Health エージェント、AD FS、Web アプリケーション プロキシ サーバー、Azure AD Connect (同期) サーバー、ドメイン コントローラーをインストールすることによる、CPU、メモリ消費、ネットワーク帯域幅、ストレージへの影響は最小限です。

おおよその数値を以下に示します。

* CPU 消費率: 約&1; ～&5;% 上昇。
* メモリ消費量: システム メモリ合計の最大 10%。

> [!NOTE]
> Azure と通信できない場合、エージェントは定義済みの上限までデータをローカルに保存します。 エージェントは、"最も長く使用されていない" という基準で、"キャッシュされた" データを上書きします。
>
>

* Azure AD Connect Health エージェントのローカル バッファー ストレージ: 約&20; MB。
* AD FS サーバーの場合、Azure AD Connect Health エージェントの AD FS 監査チャネルがすべての監査データを上書き前に処理できるように、1,024 MB (1 GB) のディスク領域をプロビジョニングすることをお勧めします。

**Q: Azure AD Connect Health エージェントのインストール時にサーバーを再起動する必要はありますか。**

いいえ。 エージェントをインストールする場合、サーバーを再起動する必要はありません。 ただし、前提条件の一部のインストール手順では、サーバーの再起動が必要です。

たとえば、Windows Server 2008 R2 では、.NET Framework 4.5 をインストールする際にサーバーの再起動が必要です。

**Q: Azure AD Connect Health はパススルー HTTP プロキシで動作しますか。**

はい。 実行中の操作については、HTTP プロキシを使用して送信 HTTP 要求を転送するように Health エージェントを構成できます。
詳しくは、[Health エージェント対応の HTTP プロキシの構成](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)に関するページをご覧ください。

エージェントの登録時にプロキシを構成する必要がある場合は、Internet Explorer のプロキシ設定を事前に変更する必要があることがあります。

1. Internet Explorer を開いて、**[設定]** > **[インターネット オプション]** > **[接続]** > **[LAN の設定]** の順に移動します。
2. **[LAN にプロキシ サーバーを使用する]** をオンにします。
3. HTTP と HTTPS/Secure でプロキシ ポートが異なる場合は、**[詳細設定]** を選択します。

**Q: Azure AD Connect Health では、HTTP プロキシに接続するときの基本認証がサポートされていますか。**

いいえ。 基本認証に対して任意のユーザー名とパスワードを指定するメカニズムは、現在サポートされていません。

**Q: Azure AD Connect Health エージェントを使用するためには、どのファイアウォール ポートを開放すればよいですか。**

ファイアウォールのポートとその他の接続要件の一覧については、「[必要条件](active-directory-aadconnect-health-agent-install.md#requirements)」セクションをご覧ください。

**Q: Azure AD Connect Health ポータルに同じ名前のサーバーが&2; つ表示されるのはなぜですか。**

サーバーからエージェントを削除しても、サーバーは Azure AD Connect Health ポータルから自動的に削除されません。 サーバーから手動でエージェントを削除したか、サーバー自体を削除した場合は、Azure AD Connect Health ポータルから手動でサーバー エントリを削除する必要があります。

サーバーを再イメージ化したり、詳細情報 (マシン名など) が同じになっているサーバーを新規に作成したりすることがありますが、 Azure AD Connect Health ポータルから登録済みサーバーを削除せずに新しいサーバーにエージェントをインストールした場合、同じ名前の&2; つのエントリが表示される可能性があります。

この場合、古いサーバーに対応するエントリを手動で削除します。 間違いなく、このサーバーのデータは古くなっています。

## <a name="health-agent-registration-and-data-freshness"></a>Health エージェントの登録とデータの新しさ

**Q: Health エージェントの登録が失敗する一般的な理由と、問題をトラブルシューティングする方法を教えてください。**

Health エージェントの登録は、次の理由により失敗する可能性があります。

* ファイアウォールによってトラフィックがブロックされているため、エージェントが必要なエンドポイントと通信できません。 これは特に、Web アプリケーション プロキシ サーバーで起こりがちです。 必要なエンドポイントとポートへの発信通信が許可されていることを確認してください。 詳しくは、「[必要条件](active-directory-aadconnect-health-agent-install.md#requirements)」セクションをご覧ください。
* 発信通信がネットワーク層による SSL 検査の対象になっています。 これが原因で、エージェントによって使われる証明書が検査サーバー/エンティティによって置き換えられ、エージェントの登録を完了する手順が失敗します。
* ユーザーに、エージェントの登録を実行するためのアクセス権がありません。 グローバル管理者には、既定でアクセス権が与えられます。 [ロール ベースのアクセス制御](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)を使って、他のユーザーにアクセスを委任できます。

**Q: "Health サービス データが最新ではありません。" というアラートが通知されます。この問題をトラブルシューティングする方法を教えてください。**

Azure AD Connect Health では、過去&2; 時間でサーバーから一部のデータ ポイントが送られなかった場合に、このアラートが生成されます。 このアラートには複数の理由が考えられます。

* ファイアウォールによってトラフィックがブロックされているため、エージェントが必要なエンドポイントと通信できません。 これは特に、Web アプリケーション プロキシ サーバーで起こりがちです。 必要なエンドポイントとポートへの発信通信が許可されていることを確認してください。 詳しくは、「[必要条件](active-directory-aadconnect-health-agent-install.md#requirements)」セクションをご覧ください。
* 発信通信がネットワーク層による SSL 検査の対象になっています。 これが原因で、エージェントによって使われる証明書が検査サーバー/エンティティによって置き換えられ、Azure AD Connect Health サービスにデータをアップロードするプロセスが失敗します。
* エージェントに組み込まれている接続コマンドを使うことができます。 詳細については、[こちら](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)を参照してください。
* また、エージェントは、認証されていない HTTP プロキシを経由した発信接続もサポートしています。 詳細については、[こちら](active-directory-aadconnect-health-agent-install.md##configure-azure-ad-connect-health-agents-to-use-http-proxy)を参照してください。

## <a name="operations-questions"></a>操作に関する質問
**Q: Web アプリケーション プロキシ サーバーで監査を有効にする必要がありますか。**

いいえ。Web アプリケーション プロキシ サーバーで監査を有効にする必要はありません。

**Q: Azure AD Connect Health アラートはどのように解決すればよいですか。**

Azure AD Connect Health アラートは、成功条件を満たすと解決されます。 Azure AD Connect Health エージェントは、定期的に成功条件を検出してサービスにレポートします。 一部のアラートは、時間に基づいて抑制されます。 つまり、アラートの生成から 72 時間以内に同じエラー条件が観察されない場合、アラートは自動的に解決されます。

## <a name="migration-questions"></a>移行に関する質問

このセクションは、Azure AD Connect Health データの今後の移行が通知されたお客様のみを対象としています。

**Q: 移行後、エージェントを改めて登録したり、通知を構成し直したりする必要はありますか。**

いいえ。エージェントの登録情報と通知の設定は、移行の一環として移動されます。

**Q: 移行後どのくらい経ってから、ポータルにデータが表示されますか。**

データは、移行後&1; 時間以内にポータルに表示されます。

**Q: 既存のアクティブなアラートはどうなりますか。**

該当するすべてのアラートが、移行後&1; 時間以内に有効になります。

## <a name="related-links"></a>関連リンク
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health エージェントのインストール](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health の操作](active-directory-aadconnect-health-operations.md)
* [AD FS での Azure AD Connect Health の使用](active-directory-aadconnect-health-adfs.md)
* [Azure AD Connect Health for Sync の使用](active-directory-aadconnect-health-sync.md)
* [AD DS での Azure AD Connect Health の使用](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health のバージョン履歴](active-directory-aadconnect-health-version-history.md)

