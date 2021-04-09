---
title: Azure HDInsight の Ranger と Apache Ambari での LDAP 同期
description: Ranger と Ambari での LDAP 同期について紹介し、一般的なガイドラインを提供します。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: fb9035b4d816c1af84b15e6865335aa1bdf86f5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933423"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight の Ranger と Apache Ambari での LDAP 同期

HDInsight Enterprise セキュリティ パッケージ (ESP) クラスターでは、承認に Ranger を使用します。 Apache Ambari と Ranger は、どちらもユーザーとグループを別々に同期しますが、少し動作が異なります。 この記事は、Ranger と Ambari での LDAP 同期について紹介することを目的としています。

## <a name="general-guidelines"></a>一般的なガイドライン

* クラスターは、常に 1 つ以上のグループと共にデプロイします。
* クラスター内でより多くのグループを使用する場合は、Azure Active Directory (Azure AD) でグループ メンバーシップを更新することが理にかなっているかどうかを確認します。
* クラスター グループを変更する場合は、Ambari を使用して同期フィルターを変更できます。
* Azure AD でのすべてのグループ メンバーシップの変更は、以降の同期でクラスターに反映されます。 変更は、最初に Azure AD Domain Services (Azure AD DS) に同期され、次にクラスターに同期される必要があります。
* HDInsight クラスターでは、Samba/Winbind を使用して、クラスター ノードのグループ メンバーシップを投影します。
* グループ メンバーは、Ambari と Ranger の両方に推移的 (すべてのサブグループとそのメンバー) に同期されます。 

## <a name="users-are-synced-separately"></a>ユーザーは個別に同期される

 * Ambari と Ranger は、2 つの異なる目的で使用されるため、ユーザー データベースを共有しません。 
   * ユーザーが Ambari UI を使用する必要がある場合は、そのユーザーを Ambari に同期する必要があります。 
   * ユーザーが Ambari に同期されていない場合、Ambari UI または API はこのユーザーを拒否しますが、システムの他の部分は動作します (これらは Ambari によってではなく、Ranger または Resource Manager によって保護されます)。
   * Ranger ポリシーにユーザーまたはグループを含めるには、そのプリンシパルを Ranger で明示的に同期する必要があります。

## <a name="ambari-user-sync-and-configuration"></a>Ambari のユーザー同期と構成

ヘッド ノードから、ユーザー同期をスケジュールするための cron ジョブ `/opt/startup_scripts/start_ambari_ldap_sync.py` が 1 時間ごとに実行されます。この cron ジョブにより、Ambari REST API シリーズが呼び出され、同期が実行されます。このスクリプトにより、同期するユーザーとグループのリストが送信されます (ユーザーは指定されたグループに属していない可能性があるため、どちらも個別に指定されています)。 Ambari によって、ユーザー名としての sAMAccountName およびすべてのグループ メンバーが推移的に同期されます。

ログは `/var/log/ambari-server/ambari-server.log` にあります。 詳細については、「[Ambari のログ レベルの構成](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html)」を参照してください。

Data Lake クラスターでは、ユーザー作成後のフックを使用して、同期されたユーザーのホーム フォルダーが作成され、これらのユーザーがホーム フォルダーの所有者として設定されます。 ユーザーが Ambari に正しく同期されていない場合、ホーム フォルダーが正しくセットアップされていない可能性があるため、ユーザーはジョブの実行に失敗する可能性があります。

## <a name="ranger-user-sync-and-configuration"></a>Ranger ユーザーの同期と構成

Ranger には、ユーザーを同期するために 1 時間ごとに実行される、組み込みの同期エンジンが用意されています。 ユーザー データベースは Ambari と共有されません。 HDInsight により、管理者ユーザー、ウォッチドッグ ユーザー、およびクラスターの作成中に指定されたグループのメンバーを同期するように検索フィルターが構成されます。 グループ メンバーは、推移的に同期されます。

1. 増分同期を無効にします。
1. ユーザー グループ同期マップを有効にします。
1. 推移的なグループ メンバーを含める検索フィルターを指定します。
1. ユーザーの sAMAccountName 属性とグループの name 属性を同期します。

### <a name="group-or-incremental-sync"></a>グループ同期または増分同期

Ranger ではグループ同期オプションがサポートされますが、グループ メンバーシップとユーザー フィルターの和集合としてではなく、ユーザー フィルターとの積として機能します。 Ranger におけるグループ同期フィルターの一般的なユース ケースは、グループ フィルター (dn = clusteradmingroup) とユーザー フィルター (city = seattle) です。

増分同期は、既に (最初の) 同期が行われているユーザーに対してのみ動作します。 増分では、最初の同期後にグループに追加された新しいユーザーは同期されません。

### <a name="update-ranger-sync-filter"></a>Ranger 同期フィルターを更新する

LDAP フィルターは、Ambari UI の Ranger ユーザー同期構成セクションにあります。 既存のフィルターは、`(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))` の形式になります。 必ず述語を末尾に追加し、`net ads` 検索コマンドまたは ldp.exe などを使用してフィルターをテストしてください。

## <a name="ranger-user-sync-logs"></a>Ranger のユーザー同期ログ

Ranger のユーザー同期は、ヘッドノードのいずれかで発生します。 ログは、`/var/log/ranger/usersync/usersync.log` にあります。 ログの詳細度を上げるには、次の手順を実行します。

1. Ambari にログインします。
1. Ranger 構成セクションにアクセスします。
1. 詳細 **usersync-log4j** セクションにアクセスします。
1. `log4j.rootLogger` を `DEBUG` レベルに変更します。 変更すると、`log4j.rootLogger = DEBUG,logFile,FilterLog` のようになります。
1. 構成を保存し、Ranger を再起動します。

## <a name="known-issues-with-ranger-user-sync"></a>Ranger ユーザーの同期に関する既知の問題
* グループ名に Unicode 文字が含まれている場合、Ranger 同期でそのオブジェクトを同期できません。 ユーザーが国際的な文字を持つグループに属している場合、Ranger は部分的なグループのメンバーシップを同期します。
* ユーザー名 (sAMAccountName) とグループ名 (name) の長さは 20 文字以下にする必要があります。 グループ名が長い場合、アクセス許可を計算するときに、ユーザーはグループに属していない場合と同様に扱われます。

## <a name="next-steps"></a>次のステップ

* [Azure HDInsight での認証の問題](./domain-joined-authentication-issues.md)
* [HDInsight クラスターへの Azure AD ユーザーの同期](../hdinsight-sync-aad-users-to-cluster.md)
