---
title: "最新の Azure ゲスト OS のリリースについて | Microsoft Docs"
description: "Azure Cloud Services のゲスト OS の最新のリリース情報と SDK との互換性について説明します。"
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: 
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 3/8/2017
ms.author: raiye
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 182b03d7075c3161f09f27f265b624897cb7a9cc
ms.lasthandoff: 03/09/2017


---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Azure ゲスト OS リリースと SDK の互換性対応表
Cloud Services 向けの最新の Azure ゲスト OS リリースの最新情報を提供します。 この情報は、ゲスト OS が無効になる前にアップグレード パスを計画する際に役立ちます。 「[Azure ゲスト OS の更新設定][Azure Guest OS Update Settings]」に説明されているゲスト OS の *自動更新*を使用するようにロールを構成している場合は、このページを読むことは必須ではありません。

> [!IMPORTANT]
> このページは、ゲスト OS 上で実行される Cloud Services Web ロールと worker ロールに適用されます。 IaaS Virtual Machines には **適用されません** 。 
> 
> 

<!-- -->

> [!TIP]
> [ゲスト OS 更新 RSS フィード][rss]をサブスクライブして、ゲスト OS のすべての変更に関する通知をタイムリーに受信してください。
> 
> 

ゲスト OS が不明か、ゲスト OS リリースのしくみが不明な場合は、 [こちら](#how-it-works)のセクションをお読みください。

## <a name="news-updates"></a>最新情報
###### <a name="january-10-2017"></a>**2017 年 1 月 10 日**
1 月のゲスト OS には、OS ファミリ 2 (Windows 2008 Server R2) のみに影響する修正プログラムが含まれています。 そのため、今月は、OS ファミリ 2 イメージ (WA-GUEST-OS-2.59_201701-01) のみがリリースされています。 その他のすべての OS ファミリについては、12 月の OS (201612-01) が最新です。

###### <a name="december-14-2016"></a>**2016 年 12 月 14 日**
12 月のゲスト OS ロールアウトは、2016 年 12 月 14 日に開始し、2017 年 1 月 13 日にリリースされる予定です。

###### <a name="november-20-2016"></a>**2016 年 11 月 20 日**
11 月のゲスト OS ロールアウトは、2016 年 11 月 8 日に開始し、2016 年 12 月 8 日にリリースされる予定です。

###### <a name="october-23-2016"></a>**2016 年 10 月 23 日**
Windows Server 2016 は、2016 年 11 月 1 日に .NET 4.6 のサポート付きで OS ファミリ 5 としてリリースされる予定です。


## <a name="releases"></a>リリース
## <a name="family-5-releases"></a>ファミリ 5 のリリース
**Windows Server 2016**

インストールされている .NET framework: 4.0、4.5、4.5.1、4.5.2、4.6、4.6.1、4.6.2

> [!NOTE]
> * の付いた日付は変更されることがあります。 
> 
> OS ファミリ 5 用の RDP パスワードは、10 文字以上にする必要があります。
>

| 構成文字列 | リリース日 | 無効になる日 | 有効期限 |
| --- | --- | --- | --- |
| WA-GUEST-OS-5.4_201612-01 |2017 年 1 月 10 日 |Post 5.6 |TBD | 
| WA-GUEST-OS-5.3_201611-01 |2016 年 12 月 14 日 |Post 5.5 |TBD |
| WA-GUEST-OS-5.2_201610-02 |2016 年 11 月 1 日 |Post 5.4 |TBD |

## <a name="family-4-releases"></a>ファミリ 4 のリリース
**Windows Server 2012 R2**

.NET 4.0、4.5、4.5.1、4.5.2 をサポートします

> [!NOTE]
> * の付いた日付は変更されることがあります
> 
> 

| 構成文字列 | リリース日 | 無効になる日 | 有効期限 |
| --- | --- | --- | --- |
| WA-GUEST-OS-4.39_201612-01 |2017 年 1 月 10 日 |Post 4.41 |TBD |
| WA-GUEST-OS-4.38_201611-01 |2016 年 12 月 14 日 |Post 4.40 |TBD |
| WA-GUEST-OS-4.37_201610-02 |2016 年 11 月 16 日 |Post 4.39 |TBD |
|~~WA-GUEST-OS-4.36_201609-01~~ |2016 年 10 月 13 日 |2017 年 1 月 14 日 |TBD |
|~~WA-GUEST-OS-4.35_201608-01~~ |2016 年 9 月 13 日 |2016 年 12 月 16 日 |TBD |
|~~WA-GUEST-OS-4.34_201607-01~~ |2016 年 8 月 8 日 |2016 年 11 月 13 日 |TBD |
|~~WA-GUEST-OS-4.33_201606-01~~ |2016 年 7 月 13 日 |2016 年 10 月 13 日 |TBD |
|~~WA-GUEST-OS-4.32_201605-01~~ |2016 年 6 月 10 日 |2016 年 9 月 8 日 |TBD |
|~~WA-GUEST-OS-4.31_201604-01~~ |2016 年 5 月 2 日 |2016 年 8 月 13 日 |TBD |
|~~WA-GUEST-OS-4.30_201603-01~~ |2016 年 4 月 7 日 |2016 年 7 月 10 日 |TBD |
|~~WA-GUEST-OS-4.29_201602-02~~ |2016 年 3 月 12 日 |2016 年 6 月 2 日 |TBD |
|~~WA-GUEST-OS-4.28_201601-01~~ |2016 年 2 月 12 日 |2016 年 5 月 7 日 |TBD |
|~~WA-GUEST-OS-4.27_201512-01~~ |2016 年 1 月 12 日 |2016 年 4 月 12 日 |TBD |

## <a name="family-3-releases"></a>ファミリ 3 のリリース
**Windows Server 2012**

.NET 4.0、4.5、4.5.1、4.5.2 をサポートします

> [!NOTE]
> * の付いた日付は変更されることがあります
> 
> 

| 構成文字列 | リリース日 | 無効になる日 | 有効期限 |
| --- | --- | --- | --- |
| WA-GUEST-OS-3.46_201612-01 |2017 年 1 月 10 日 |Post 3.48 |TBD |
| WA-GUEST-OS-3.45_201611-01 |2016 年 12 月 14 日 |Post 3.47 |TBD |
| WA-GUEST-OS-3.44_201610-01 |2016 年 11 月 16 日 |Post 3.46 |TBD |
| ~~WA-GUEST-OS-3.43_201609-01~~ |2016 年 10 月 13 日 |2017 年 1 月 14 日 |TBD |
| ~~WA-GUEST-OS-3.42_201608-01~~ |2016 年 9 月 13 日 |2016 年 12 月 16 日 |TBD |
| ~~WA-GUEST-OS-3.41_201607-01~~ |2016 年 8 月 8 日 |2016 年 11 月 13 日 |TBD |
| ~~WA-GUEST-OS-3.40_201606-01~~ |2016 年 7 月 13 日 |2016 年 10 月 13 日 |TBD |
| ~~WA-GUEST-OS-3.39_201605-01~~ |2016 年 6 月 10 日 |2016 年 9 月 8 日 |TBD |
| ~~WA-GUEST-OS-3.38_201604-01~~ |2016 年 5 月 2 日 |2016 年 8 月 13 日 |TBD |
| ~~WA-GUEST-OS-3.37_201603-01~~ |2016 年 4 月 7 日 |2016 年 7 月 10 日 |TBD |
| ~~WA-GUEST-OS-3.36_201602-02~~ |2016 年 3 月 12 日 |2016 年 6 月 2 日 |TBD |
| ~~WA-GUEST-OS-3.35_201601-01~~ |2016 年 2 月 12 日 |2016 年 5 月 7 日 |TBD |
| ~~WA-GUEST-OS-3.34_201512-01~~ |2016 年 1 月 12 日 |2016 年 4 月 12 日 |TBD |

## <a name="family-2-releases"></a>ファミリ 2 のリリース
**Windows Server 2008 R2 SP1**

.NET 3.5、4.0、4.5、4.5.1、4.5.2 をサポートします

> [!NOTE]
> * の付いた日付は変更されることがあります
> 
> 

| 構成文字列 | リリース日 | 無効になる日 | 有効期限 |
| --- | --- | --- | --- |
| WA-GUEST-OS-2.59_201701-01 |2017 年 1 月 10 日 |Post 2.61 |TBD |
| WA-GUEST-OS-2.58_201612-01 |2017 年 1 月 10 日 |Post 2.60 |TBD |
| WA-GUEST-OS-2.57_201611-01 |2016 年 12 月 14 日 |Post 2.59 |TBD |
| WA-GUEST-OS-2.56_201610-01 |2016 年 11 月 16 日 |2017 年 2 月 10 日 |TBD |
|~~WA-GUEST-OS-2.55_201609-01~~ |2016 年 10 月 13 日 |2017 年 1 月 14 日 |TBD |
|~~WA-GUEST-OS-2.54_201608-01~~ |2016 年 9 月 13 日 |2016 年 12 月 16 日 |TBD |
|~~WA-GUEST-OS-2.53_201607-01~~ |2016 年 8 月 8 日 |2016 年 11 月 13 日 |TBD |
|~~WA-GUEST-OS-2.52_201606-01~~ |2016 年 7 月 13 日 |2016 年 10 月 13 日 |TBD |
|~~WA-GUEST-OS-2.51_201605-01~~ |2016 年 6 月 10 日 |2016 年 9 月 8 日 |TBD |
|~~WA-GUEST-OS-2.50_201604-01~~ |2016 年 5 月 2 日 |2016 年 8 月 13 日 |TBD |
|~~WA-GUEST-OS-2.49_201603-01~~ |2016 年 4 月 7 日 |2016 年 7 月 10 日 |TBD |
|~~WA-GUEST-OS-2.48_201602-02~~|2016 年 3 月 12 日 |2016 年 6 月 2 日 |TBD |
|~~WA-GUEST-OS-2.47_201601-01~~|2016 年 2 月 12 日 |2016 年 5 月 7 日 |TBD |
|~~WA-GUEST-OS-2.46_201512-01~~|2016 年 1 月 12 日 |2016 年 4 月 12 日 |TBD |

## <a name="msrc-patch-updates"></a>MSRC 修正プログラム
毎月のゲスト OS リリースに含まれる修正プログラムの一覧は、[こちら][patches]で入手できます。

## <a name="sdk-support"></a>SDK のサポート
[Azure SDK の提供終了ポリシー][retire policy sdk]には 2.2 以降のバージョンのみがサポートされると示されていますが、特定のゲスト OS ファミリではそれより前のバージョンも使用できます。 常に、最新のサポートされている SDK を使用する必要があります。 

| ゲスト OS ファミリ | 互換性のある SDK のバージョン |
| --- | --- |
| 5 |バージョン 2.9.5.1 以上 |
| 4 |バージョン 2.1 以上 |
| 3 |バージョン 1.8 以上 |
| 2 |バージョン 1.3 以上 |
| 1 |バージョン 1.0 以上 |

## <a name="guest-os-release-information"></a>ゲスト OS のリリース情報
ゲスト OS リリースに重要な&3; つの日付として、**リリース日**、**無効になる日**、**有効期限**があります。 ゲスト OS は、それがポータルにあり、ターゲット ゲスト OS として選択できる場合に、使用可能と見なされます。 ゲスト OS は、 **無効になる** 日に達すると Azure から削除されます。 ただし、そのゲスト OS を対象とするクラウド サービスは引き続き通常どおりに動作します。 

**無効になる日**から**有効期限**までの間に一定の期間を設けているため、新しいゲスト OS に簡単に切り替えることができます。 ゲスト OS として " *自動* " バージョンを使用していると、常に最新バージョンに維持されるため、期限切れを心配する必要はありません。 

**有効期限** を過ぎると、そのゲスト OS を引き続き使用しているクラウド サービスが停止、削除、または強制的にアップグレードされます。 提供終了ポリシーの詳細については、[こちら][retirepolicy]を参照してください。

## <a name="guest-os-family-version-explanation"></a>ゲスト OS ファミリ、バージョンの説明
ゲスト OS ファミリは Microsoft Windows Server のリリース バージョンに基づいています。 ゲスト OS は、Azure Cloud Services が実行される、基になるオペレーティング システムです。 各ゲスト OS には、ファミリ、バージョン、リリース番号があります。 

* **Guest OS family**  
  ゲスト OS の基盤となる Windows Server オペレーティング システムのリリース。 たとえば、" *ファミリ 3* " は Windows Server 2012 を基にしています。
* **ゲスト OS バージョン**  
  ゲスト OS ファミリ イメージと、関連する [Microsoft セキュリティ レスポンス センター (MSRC)][msrc] 修正プログラム (新しいゲスト OS バージョンが作成された時点で利用可能になる) の組み合わせに固有のバージョン。 すべての修正プログラムが含まれているわけではありません。 
  
    番号は 0 から始まり、新しい更新プログラムのセットが追加されるたびに 1 ずつ増えます。 末尾のゼロが表示されるのは、重要な場合のみです。 つまり、バージョン 2.10 は、バージョン 2.1 よりかなり後の異なるバージョンです。
* **ゲスト OS リリース**  
  ゲスト OS バージョンの再リリース。 再リリースは、変更を必要とする問題がテスト中に見つかった場合に行われます。 最新のリリースは、公開されているかどうかに関係なく、常に以前のリリースよりも優先されます。 Azure クラシック ポータルでは、ユーザーは指定したバージョンの最新のリリースのみを取得できます。 前のリリースで実行中のデプロイは、通常、バグの重大度に応じてアップグレードが強制されることはありません。 

次の例では、2 がファミリ、12 がバージョン、"rel2" がリリースです。

**ゲスト OS リリース** - 2.12 rel2

**このリリースの構成文字列** -WA-GUEST-OS-2.12_201208-02

ゲスト OS の構成文字列には、組み込まれているのと同じ情報に加えて、そのリリースで考慮された MSRC 修正プログラムを示す日付が含まれます。 この例では、2012 年 8 月までに Windows Server 2008 R2 用に作成された MSRC 修正プログラムが含まれることが想定されます。 そのバージョンの Windows Server に特に適用される修正プログラムのみが含まれます。 たとえば、Microsoft Office に適用される MSRC 修正プログラムは、製品が Windows Server 基本イメージに属さないため、ここに含まれません。 

## <a name="guest-os-system-update-process"></a>ゲスト OS システムの更新プロセス
このページには、今後予定されているゲスト OS のリリースに関する情報が含まれます。 "自動" 更新に設定している場合はクラウド サービスのロールが再起動されるため、リリースのタイミングを把握する必要があるとお客様から指摘を受けています。 ゲスト OS のリリースは、通常、毎月第 2 火曜日に発生する MSRC 更新プログラムのリリースから少なくとも 5 日以内に発生します。 新しいリリースには、ゲスト OS ファミリごとに関連するすべての MSRC 修正プログラムが含まれます。 

Microsoft Azure では、常に更新プログラムがリリースされています。 ゲスト OS は、パイプラインにおいて唯一のこのような更新です。 リリースは、ここに記載できないほどの多く要因の影響を受ける可能性があります。 さらに、Azure は、実際には数十万台のコンピューターで実行されています。 つまり、ロールが再起動される正確な日時を指定することはできません。 [ゲスト OS の更新 RSS フィード][rss]は最新の情報で更新されていますが、その時刻はおおよその時間帯であると考えてください。 これは問題視されているため、再起動の制限や時間の指定をできるように取り組んでいます。 

ゲスト OS の新しいリリースが公開されると、Azure 全体で完全に反映されるまでに時間がかかります。 サービスが新しいゲスト OS に更新されると、更新ドメインに従って再起動されます。 "自動" 更新プログラムを使用するように設定されているサービスで、まずリリースが取得されます。 更新後、Azure クラシック ポータルでは、ご使用のサービスの新しいゲスト OS バージョンの一覧が表示されます。 この期間に再リリースが発生する場合があります。 一部のバージョンは長期間にわたってデプロイされることがあります。また、正式なリリース日以降何週間も自動アップグレードの再起動が行われないこともあります。 ゲスト OS が使用可能になると、ポータルや構成ファイルから明示的にそのバージョンを選択できます。 

再起動の関する有益な情報と、ゲスト OS とホスト OS の更新プログラムの技術的な詳細のヒントについては、MSDN の [OS のアップグレードによるロール インスタンスの再起動][restarts]に関するブログ記事を参照してください。

ゲスト OS を手動で更新する場合は、[ゲスト OS の提供終了ポリシー][retirepolicy]に関するページを参照してください。

## <a name="guest-os-supportability-and-retirement-policy"></a>ゲスト OS のサポートと提供終了のポリシー
ゲスト OS のサポートと提供終了のポリシーについては、[こちら][retirepolicy]で説明しています。

[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/en-us/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure.md
[rss]: http://sxp.microsoft.com/feeds/3.0/msdntn/WindowsAzureOSUpdates
[ssl3 announcement]: http://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: http://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: http://azure.microsoft.com/support/options/
[net install pkg]: http://www.microsoft.com/download/details.aspx?id=42643
[msrc]: http://www.microsoft.com/security/msrc/default.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md


