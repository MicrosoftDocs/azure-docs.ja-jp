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
ms.date: 8/24/2017
ms.author: raiye
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: be0092e62033da249015b211db148da57bb82765
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Azure ゲスト OS リリースと SDK の互換性対応表
Cloud Services 向けの最新の Azure ゲスト OS リリースの最新情報を提供します。 この情報は、ゲスト OS が無効になる前にアップグレード パスを計画する際に役立ちます。 「[Azure ゲスト OS の更新設定][Azure Guest OS Update Settings]」に説明されているゲスト OS の *自動更新*を使用するようにロールを構成している場合は、このページを読むことは必須ではありません。

> [!IMPORTANT]
> このページは、ゲスト OS 上で実行される Cloud Services Web ロールと worker ロールに適用されます。 IaaS Virtual Machines には **適用されません** 。
>
>


> [!NOTE]
> RSS フィードは最近使用されなくなりました。 近日公開予定の新しいフィードに対する更新にご期待ください。
>
>

ゲスト OS が不明か、ゲスト OS リリースのしくみが不明な場合は、 [こちら](#how-it-works)のセクションをお読みください。

## <a name="news-updates"></a>最新情報

###### <a name="august-24-2017"></a>**2017 年 8 月 24 日**
8 月のゲスト OS がリリースされました。

###### <a name="august-3-2017"></a>**2017 年 8 月 3 日**
7 月のゲスト OS がリリースされました。

###### <a name="july-19-2017"></a>**2017 年 7 月 19 日**
7 月のゲスト OS ロールアウトは、7 月 19 日に開始し、8 月 8 日にリリースされる予定です。

###### <a name="july-7-2017"></a>**2017 年 7 月 7 日**
6 月のゲスト OS がリリースされました。

###### <a name="june-16-2017"></a>**2017 年 6 月 16 日**
6 月のゲスト OS ロールアウトは、6 月 16 日に開始し、7 月 11 日にリリースされる予定です。

###### <a name="june-5-2017"></a>**2017 年 6 月 5 日**
5 月のゲスト OS がリリースされました。

###### <a name="may-17-2017"></a>**2017 年 5 月 17 日**
セキュリティ バグがあるため、ポータルからの[解決策]がない 2016 年 12 月と 2017 年 1 月にリリースされた次の OS は無効になっています。WA-GUEST-OS-5.4_201612-01、WA-GUEST-OS-4.39_201612-01、WA-GUEST-OS-3.46_201612-01、WA-GUEST-OS-2.59_201701-01

###### <a name="may-12-2017"></a>**2017 年 5 月 12 日**
5 月のゲスト OS ロールアウトは、5 月 12 日に開始し、6 月 13 日にリリースされる予定です。

###### <a name="april-18-2017"></a>**2017 年 4 月 18 日**
4 月のゲスト OS ロールアウトは、4 月 18 日に開始し、5 月 9 日にリリースされる予定です。

###### <a name="april-10-2017"></a>**2017 年 4 月 10 日**
3 月のゲスト OS ロールアウトは、2017 年 3 月 14 日に開始し、2017 年 4 月 10 日にリリースされました。

###### <a name="january-10-2017"></a>**2017 年 1 月 10 日**
1 月のゲスト OS には、OS ファミリ 2 (Windows 2008 Server R2) のみに影響する修正プログラムが含まれています。 そのため、今月は、OS ファミリ 2 イメージ (WA-GUEST-OS-2.59_201701-01) のみがリリースされています。 その他のすべての OS ファミリについては、12 月の OS (201612-01) が最新です。


## <a name="releases"></a>リリース
## <a name="family-5-releases"></a>ファミリ 5 のリリース
**Windows Server、2016**

インストールされている .NET framework: 4.0、4.5、4.5.1、4.5.2、4.6、4.6.1、4.6.2

> [!NOTE]
> * の付いた日付は変更されることがあります。
>
> OS ファミリ 5 用の RDP パスワードは、10 文字以上にする必要があります。
>

| 構成文字列 | リリース日 | 無効になる日 | 有効期限 |
| --- | --- | --- | --- |
| WA-GUEST-OS-5.10_201708-01 |2017 年 8 月 24 日 |Post 5.12 |TBD |
| WA-GUEST-OS-5.9_201707-01 |2017 年 8 月 3 日 |Post 5.11 |TBD |
| WA-GUEST-OS-5.8_201706-01 |2017 年 7 月 7 日 |Post 5.10 |TBD |
|~~WA-GUEST-OS-5.7_201705-01~~ |2017 年 6 月 5 日 |2017 年 8 月 24 日 |TBD |
|~~WA-GUEST-OS-5.6_201704-01~~ |2017 年 5 月 9 日 |2017 年 8 月 3 日 |TBD |
|~~WA-GUEST-OS-5.5_201703-01~~ |2017 年 4 月 10 日 |2017 年 7 月 7 日 |TBD |
|~~WA-GUEST-OS-5.4_201612-01~~ |2017 年 1 月 10 日 |2017 年 6 月 5 日|TBD |
|~~WA-GUEST-OS-5.3_201611-01~~ |2016 年 12 月 14 日 |2017 年 5 月 9 日 |TBD |
|~~WA-GUEST-OS-5.2_201610-02~~ |2016 年 11 月 1 日 |2017 年 4 月 10 日 |TBD |

## <a name="family-4-releases"></a>ファミリ 4 のリリース
**Windows Server 2012 R2**

.NET 4.0、4.5、4.5.1、4.5.2 をサポートします

> [!NOTE]
> * の付いた日付は変更されることがあります
>
>

| 構成文字列 | リリース日 | 無効になる日 | 有効期限 |
| --- | --- | --- | --- |
| WA-GUEST-OS-4.45_201708-01 |2017 年 8 月 24 日 |Post 4.47 |TBD |
| WA-GUEST-OS-4.44_201707-01 |2017 年 8 月 3 日 |Post 4.46 |TBD |
| WA-GUEST-OS-4.43_201706-01 |2017 年 7 月 7 日 |Post 4.45 |TBD |
|~~WA-GUEST-OS-4.42_201705-01~~ |2017 年 6 月 5 日 |2017 年 8 月 24 日 |TBD |
|~~WA-GUEST-OS-4.41_201704-01~~ |2017 年 5 月 9 日 |2017 年 8 月 3 日 |TBD |
|~~WA-GUEST-OS-4.40_201703-01~~ |2017 年 4 月 10 日 |2017 年 7 月 7 日 |TBD |
|~~WA-GUEST-OS-4.39_201612-01~~ |2017 年 1 月 10 日 |2017 年 6 月 5 日 |TBD |
|~~WA-GUEST-OS-4.38_201611-01~~ |2016 年 12 月 14 日 |2017 年 5 月 9 日 |TBD |
|~~WA-GUEST-OS-4.37_201610-02~~ |2016 年 11 月 16 日 |2017 年 4 月 10 日 |TBD |
|~~WA-GUEST-OS-4.36_201609-01~~ |2016 年 10 月 13 日 |2017 年 1 月 14 日 |TBD |
|~~WA-GUEST-OS-4.35_201608-01~~ |2016 年 9 月 13 日 |2016 年 12 月 16 日 |TBD |
|~~WA-GUEST-OS-4.34_201607-01~~ |2016 年 8 月 8 日 |2016 年 11 月 13 日 |TBD |


## <a name="family-3-releases"></a>ファミリ 3 のリリース
**Windows Server 2012**

.NET 4.0、4.5、4.5.1、4.5.2 をサポートします

> [!NOTE]
> * の付いた日付は変更されることがあります
>
>

| 構成文字列 | リリース日 | 無効になる日 | 有効期限 |
| --- | --- | --- | --- |
| WA-GUEST-OS-3.52_201708-01 |2017 年 8 月 24 日 |Post 3.54 |TBD |
| WA-GUEST-OS-3.51_201707-01 |2017 年 8 月 3 日 |Post 3.53 |TBD |
| WA-GUEST-OS-3.50_201706-01 |2017 年 7 月 7 日 |Post 3.52 |TBD |
|~~WA-GUEST-OS-3.49_201705-01~~ |2017 年 6 月 5 日 |2017 年 8 月 24 日 |TBD |
|~~WA-GUEST-OS-3.48_201704-01~~ |2017 年 5 月 9 日 |2017 年 8 月 3 日 |TBD |
|~~WA-GUEST-OS-3.47_201703-01~~ |2017 年 4 月 10 日 |2017 年 7 月 7 日 |TBD |
|~~WA-GUEST-OS-3.46_201612-01~~ |2017 年 1 月 10 日 |2017 年 6 月 5 日 |TBD |
|~~WA-GUEST-OS-3.45_201611-01~~ |2016 年 12 月 14 日 |2017 年 5 月 9 日 |TBD |
|~~WA-GUEST-OS-3.44_201610-02~~ |2016 年 11 月 16 日 |2017 年 5 月 1 日 |TBD |
|~~WA-GUEST-OS-3.43_201609-01~~ |2016 年 10 月 13 日 |2017 年 1 月 14 日 |TBD |
|~~WA-GUEST-OS-3.42_201608-01~~ |2016 年 9 月 13 日 |2016 年 12 月 16 日 |TBD |
|~~WA-GUEST-OS-3.41_201607-01~~ |2016 年 8 月 8 日 |2016 年 11 月 13 日 |TBD |


## <a name="family-2-releases"></a>ファミリ 2 のリリース
**Windows Server 2008 R2 SP1**

.NET 3.5、4.0、4.5、4.5.1、4.5.2 をサポートします

> [!NOTE]
> * の付いた日付は変更されることがあります
>
>

| 構成文字列 | リリース日 | 無効になる日 | 有効期限 |
| --- | --- | --- | --- |
| WA-GUEST-OS-2.65_201708-01 |2017 年 8 月 24 日 |Post 2.67 |TBD |
| WA-GUEST-OS-2.64_201707-01 |2017 年 8 月 3 日 |Post 2.66 |TBD |
| WA-GUEST-OS-2.63_201706-01 |2017 年 7 月 7 日 |Post 2.65 |TBD |
|~~WA-GUEST-OS-2.62_201705-01~~ |2017 年 6 月 5 日 |2017 年 8 月 24 日 |TBD |
|~~WA-GUEST-OS-2.61_201704-01~~ |2017 年 5 月 9 日 |2017 年 8 月 3 日 |TBD |
|~~WA-GUEST-OS-2.60_201703-01~~ |2017 年 4 月 10 日 |2017 年 7 月 7 日 |TBD |
|~~WA-GUEST-OS-2.59_201701-01~~ |2017 年 1 月 10 日 |2017 年 6 月 5 日 |TBD |
|~~WA-GUEST-OS-2.58_201612-01~~ |2017 年 1 月 10 日 |2017 年 5 月 9 日|TBD |
|~~WA-GUEST-OS-2.57_201611-01~~ |2016 年 12 月 14 日 |2017 年 4 月 10 日 |TBD |
|~~WA-GUEST-OS-2.56_201610-02~~ |2016 年 11 月 16 日 |2017 年 2 月 10 日 |TBD |
|~~WA-GUEST-OS-2.55_201609-01~~ |2016 年 10 月 13 日 |2017 年 1 月 14 日 |TBD |
|~~WA-GUEST-OS-2.54_201608-01~~ |2016 年 9 月 13 日 |2016 年 12 月 16 日 |TBD |
|~~WA-GUEST-OS-2.53_201607-01~~ |2016 年 8 月 8 日 |2016 年 11 月 13 日 |TBD |



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
ゲスト OS リリースに重要な 3 つの日付として、**リリース日**、**無効になる日**、**有効期限**があります。 ゲスト OS は、それがポータルにあり、ターゲット ゲスト OS として選択できる場合に、使用可能と見なされます。 ゲスト OS は、**無効になる**日に達すると Azure から削除されます。 ただし、そのゲスト OS を対象とするクラウド サービスは引き続き通常どおりに動作します。

**無効になる**日から**有効期限**までの間に一定の期間を設けているため、新しいゲスト OS に簡単に切り替えることができます。 ゲスト OS として " *自動* " バージョンを使用していると、常に最新バージョンに維持されるため、期限切れを心配する必要はありません。

**有効期限**を過ぎると、そのゲスト OS を引き続き使用しているクラウド サービスが停止、削除、または強制的にアップグレードされます。 提供終了ポリシーの詳細については、[こちら][retirepolicy]を参照してください。

## <a name="guest-os-family-version-explanation"></a>ゲスト OS ファミリ、バージョンの説明
ゲスト OS ファミリは Microsoft Windows Server のリリース バージョンに基づいています。 ゲスト OS は、Azure Cloud Services が実行される、基になるオペレーティング システムです。 各ゲスト OS には、ファミリ、バージョン、リリース番号があります。

* **Guest OS family**  
  ゲスト OS の基盤となる Windows Server オペレーティング システムのリリース。 たとえば、" *ファミリ 3* " は Windows Server 2012 を基にしています。
* **ゲスト OS バージョン**  
  ゲスト OS ファミリ イメージと、関連する [Microsoft セキュリティ レスポンス センター (MSRC)][msrc] 修正プログラム (新しいゲスト OS バージョンが作成された時点で利用可能になる) の組み合わせに固有のバージョン。 すべての修正プログラムが含まれているわけではありません。

    番号は 0 から始まり、新しい更新プログラムのセットが追加されるたびに 1 ずつ増えます。 末尾のゼロが表示されるのは、重要な場合のみです。 つまり、バージョン 2.10 は、バージョン 2.1 よりかなり後の異なるバージョンです。
* **ゲスト OS リリース**  
  ゲスト OS バージョンの再リリース。 再リリースは、変更を必要とする問題がテスト中に見つかった場合に行われます。 最新のリリースは、公開されているかどうかに関係なく、常に以前のリリースよりも優先されます。 Azure Portal では、ユーザーは特定のバージョンの最新のリリースのみを取得できます。 前のリリースで実行中のデプロイは、通常、バグの重大度に応じてアップグレードが強制されることはありません。

次の例では、2 がファミリ、12 がバージョン、"rel2" がリリースです。

**ゲスト OS リリース** - 2.12 rel2

**このリリースの構成文字列** -WA-GUEST-OS-2.12_201208-02

ゲスト OS の構成文字列には、組み込まれているのと同じ情報に加えて、そのリリースで考慮された MSRC 修正プログラムを示す日付が含まれます。 この例では、2012 年 8 月までに Windows Server 2008 R2 用に作成された MSRC 修正プログラムが含まれることが想定されます。 そのバージョンの Windows Server に特に適用される修正プログラムのみが含まれます。 たとえば、Microsoft Office に適用される MSRC 修正プログラムは、製品が Windows Server 基本イメージに属さないため、ここに含まれません。

## <a name="guest-os-system-update-process"></a>ゲスト OS システムの更新プロセス
このページには、今後予定されているゲスト OS のリリースに関する情報が含まれます。 "自動" 更新に設定している場合はクラウド サービスのロールが再起動されるため、リリースのタイミングを把握する必要があるとお客様から指摘を受けています。 ゲスト OS のリリースは、通常、毎月第 2 火曜日に発生する MSRC 更新プログラムのリリースから少なくとも 5 日以内に発生します。 新しいリリースには、ゲスト OS ファミリごとに関連するすべての MSRC 修正プログラムが含まれます。

Microsoft Azure では、常に更新プログラムがリリースされています。 ゲスト OS は、パイプラインにおいて唯一のこのような更新です。 リリースは、ここに記載できないほどの多く要因の影響を受ける可能性があります。 さらに、Azure は、実際には数十万台のコンピューターで実行されています。 つまり、ロールが再起動される正確な日時を指定することはできません。 Microsoft では、再起動を制限したり時間を決めたりする計画に取り組んでいます。

ゲスト OS の新しいリリースが公開されると、Azure 全体で完全に反映されるまでに時間がかかります。 サービスが新しいゲスト OS に更新されると、更新ドメインに従って再起動されます。 "自動" 更新プログラムを使用するように設定されているサービスで、まずリリースが取得されます。 更新後、Azure Portal には、ご使用のサービスの新しいゲスト OS バージョンの一覧が表示されます。 この期間に再リリースが発生する場合があります。 一部のバージョンは長期間にわたってデプロイされることがあります。また、正式なリリース日以降何週間も自動アップグレードの再起動が行われないこともあります。 ゲスト OS が使用可能になると、ポータルや構成ファイルから明示的にそのバージョンを選択できます。

再起動の関する有益な情報と、ゲスト OS とホスト OS の更新プログラムの技術的な詳細のヒントについては、MSDN の [OS のアップグレードによるロール インスタンスの再起動][restarts]に関するブログ記事を参照してください。

ゲスト OS を手動で更新する場合は、追加情報について[ゲスト OS の提供終了ポリシー][retirepolicy]のページを参照してください。

## <a name="guest-os-supportability-and-retirement-policy"></a>ゲスト OS のサポートと提供終了のポリシー
ゲスト OS のサポートと提供終了のポリシーについては、[こちら][retirepolicy]で説明しています。

[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/en-us/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure.md
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
[解決策]: https://technet.microsoft.com/en-us/library/security/ms17-010.aspx

