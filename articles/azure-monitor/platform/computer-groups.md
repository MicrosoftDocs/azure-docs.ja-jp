---
title: Azure Monitor ログ クエリでのコンピューター グループ | Microsoft Docs
description: Azure Monitor では、コンピューター グループを使用して、ログ クエリの範囲を特定のコンピューターのセットに限定することができます。  この記事では、コンピューター グループを作成する各種の方法とそれらをログ クエリで使用する方法について説明します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: bwren
ms.openlocfilehash: ae423b6fb141cab4038e65ba85c6067f1c23aee0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320686"
---
# <a name="computer-groups-in-azure-monitor-log-queries"></a>Azure Monitor ログ クエリでのコンピューター グループ
Azure Monitor では、コンピューター グループを使用して、[ログ クエリ](../log-query/log-query-overview.md)の範囲を特定のコンピューターのセットに限定することができます。  それぞれのグループには、自分で定義したクエリを使用するか、さまざまなソースからグループをインポートすることでコンピューターを追加します。  そのグループをログ クエリに含めると、対応するグループ内のコンピューターと一致するレコードに検索結果が限定されます。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="creating-a-computer-group"></a>コンピューター グループの作成
Azure Monitor のコンピューター グループは、以下の表に示した方法のいずれかで作成できます。  それぞれの方法について、以降のセクションで詳しく説明します。 

| Method | 説明 |
|:--- |:--- |
| Log query |コンピューターの一覧を返すログ クエリを作成します。 |
| Log Search API |ログ クエリの結果に基づいてプログラムからコンピューター グループを作成するには、Log Search API を使用します。 |
| Active Directory |Active Directory ドメインに属しているエージェント コンピューターのグループ メンバーシップを自動的にスキャンし、セキュリティ グループごとのグループを Azure Monitor に作成します。 (Windows マシンのみ)|
| 構成マネージャー | System Center Configuration Manager からコレクションをインポートし、Azure Monitor でそれぞれに対してグループを作成します。 |
| Windows Server Update Services |WSUS のサーバーまたはクライアントを自動的にスキャンして WSUS の対象グループを取得し、それぞれのグループを Azure Monitor で作成します。 |

### <a name="log-query"></a>Log query
ログ クエリから作成されたコンピューター グループには、ユーザーが定義したクエリによって返されるすべてのコンピューターが含まれます。  このクエリは、コンピューター グループを使用するたびに実行されます。そうすることで、グループの作成以降に行われた変更が反映されます。  

コンピューター グループにはどのクエリでも使用できますが、クエリは `distinct Computer` を使用して明確な一連のコンピューターを返す必要があります。  コンピューター グループに使用できる一般的なクエリの例を次に示します。

    Heartbeat | where Computer contains "srv" | distinct Computer

Azure Portal でログ検索からコンピューター グループを作成するには、次の手順に従います。

1. Azure portal の **[Azure Monitor]** メニューで **[ログ]** をクリックします。
1. グループに含めるコンピューターが返されるクエリを作成して実行します。
1. 画面の上部にある **[保存]** をクリックします。
1. **[名前を付けて保存]** を **[関数]** に変更し、 **[このクエリをコンピューター グループとして保存します]** を選択します。
1. コンピューター グループに対して表で説明されている各プロパティの値を指定し、 **[保存]** をクリックします。

次の表では、コンピューター グループを定義するプロパティについて説明しています。

| プロパティ | Description |
|:---|:---|
| EnableAdfsAuthentication   | ポータルに表示するクエリの名前。 |
| 関数のエイリアス | クエリ内でコンピューター グループを識別するのに使用される一意のエイリアス。 |
| Category       | ポータル内でクエリを整理するためのカテゴリ。 |


### <a name="active-directory"></a>Active Directory
Active Directory のグループ メンバーシップをインポートするように Azure Monitor を構成すると、Log Analytics エージェントが存在する Windows ドメイン参加済みコンピューターのグループ メンバーシップが分析されます。  Azure Monitor で Active Directory 内のセキュリティ グループごとにコンピューター グループが作成され、各 Windows コンピューターは、メンバーになっているセキュリティ グループに対応したコンピューター グループに追加されます。  このメンバーシップは絶えず 4 時間おきに更新されます。  

> [!NOTE]
> インポートされた Active Directory グループのみが Windows コンピューターを含みます。

Azure portal でお使いの Log Analytics ワークスペースの **[詳細設定]** から、Active Directory のセキュリティ グループをインポートするように Azure Monitor を構成します。  **[コンピューター グループ]** 、 **[Active Directory]** の順に選択し、 **[コンピューターから Active Directory のグループ メンバーシップをインポートします]** を選択します。  さらに手動で構成する必要はありません。

![Active Directory からのコンピューター グループ](media/computer-groups/configure-activedirectory.png)

グループがインポートされると、検出されたグループ メンバーシップを持つコンピューターの数とインポートされたグループの数がメニューに表示されます。  そのいずれかのリンクをクリックすると、**ComputerGroup** のレコードがこの情報と共に返されます。

### <a name="windows-server-update-service"></a>Windows Server Update Service
WSUS グループのメンバーシップをインポートするように Azure Monitor を構成すると、Log Analytics エージェントが存在するすべてのコンピューターの対象グループ メンバーシップが分析されます。  クライアント側のターゲット指定方式を使用している場合、Azure Monitor に接続されていて、かつ WSUS の対象グループに属しているすべてのコンピューターのグループ メンバーシップが、Azure Monitor にインポートされます。 サーバー側のターゲット指定方式を使用している場合、グループ メンバーシップ情報を Azure Monitor にインポートするためには、WSUS サーバーに Log Analytics エージェントがインストールされている必要があります。  このメンバーシップは絶えず 4 時間おきに更新されます。 

Azure portal でお使いの Log Analytics ワークスペースの **[詳細設定]** から、WSUS グループをインポートするように Azure Monitor を構成します。  **[コンピューター グループ]** 、 **[WSUS]** の順に選択し、 **[WSUS のグループ メンバーシップをインポートします]** を選択します。  さらに手動で構成する必要はありません。

![WSUS のコンピューター グループ](media/computer-groups/configure-wsus.png)

グループがインポートされると、検出されたグループ メンバーシップを持つコンピューターの数とインポートされたグループの数がメニューに表示されます。  そのいずれかのリンクをクリックすると、**ComputerGroup** のレコードがこの情報と共に返されます。

### <a name="system-center-configuration-manager"></a>System Center Configuration Manager
Configuration Manager のコレクション メンバーシップをインポートするように Azure Monitor を構成すると、各コレクションのコンピューター グループが作成されます。  コンピューター グループを最新に保つために、コレクション メンバーシップ情報は 3 時間ごとに取得されます。 

Configuration Manager のコレクションをインポートするには、[Azure Monitor に Configuration Manager を接続する](collect-sccm.md)必要があります。  

![SCCM のコンピューター グループ](media/computer-groups/configure-sccm.png)

コレクションがインポートされると、検出されたコンピューターおよびグループ メンバーシップの数と、インポートされたグループの数がメニューに表示されます。  そのいずれかのリンクをクリックすると、**ComputerGroup** のレコードがこの情報と共に返されます。

## <a name="managing-computer-groups"></a>コンピューター グループの管理
ログ クエリまたは Log Search API から作成されたコンピューター グループは、Azure portal でお使いの Log Analytics ワークスペースの **[詳細設定]** から表示できます。  **[コンピューター グループ]** を選択してから、 **[保存済みグループ]** を選択します。  

**[削除]** 列の **[x]** をクリックすると、コンピューター グループが削除されます。  グループの **[メンバーの表示]** アイコンをクリックすると、グループのログ検索が実行されて、そのメンバーが返されます。  コンピューター グループは変更できませんが、コンピューター グループを削除し、変更された設定で再度作成する必要があります。

![保存されているコンピューター グループ](media/computer-groups/configure-saved.png)


## <a name="using-a-computer-group-in-a-log-query"></a>ログ クエリでのコンピューター グループの使用
ログ クエリから作成されたコンピューター グループをクエリで使用するには、そのエイリアスを関数として扱います。通常は、次の構文を使用します。

  `Table | where Computer in (ComputerGroup)`

たとえば、以下を使用して、mycomputergroup という名前のコンピューター グループ内のコンピューターのみを対象とした UpdateSummary レコードを返すことができます。
 
  `UpdateSummary | where Computer in (mycomputergroup)`


インポートされたコンピュータ グループとそれらに含まれるコンピューターは、**ComputerGroup** テーブルに格納されます。  たとえば、次のクエリは、Active Directory の Domain Computers グループにコンピューターの一覧を返します。 

  `ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer`

次のクエリは、Domain Computers 内のコンピューターの UpdateSummary レコードのみを返します。

  ```
  let ADComputers = ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "Domain Computers" | distinct Computer;
  UpdateSummary | where Computer in (ADComputers)
  ```




## <a name="computer-group-records"></a>コンピューター グループのレコード
Active Directory または WSUS から作成されたコンピューター グループでは、そのメンバーシップごとのレコードが Log Analytics ワークスペースに作成されます。  これらは **ComputerGroup** タイプのレコードとして、次の表に示すプロパティを持ちます。  ログ クエリに基づくコンピューター グループにはレコードが作成されません。

| プロパティ | 説明 |
|:--- |:--- |
| `Type` |*ComputerGroup* |
| `SourceSystem` |*SourceSystem* |
| `Computer` |メンバー コンピューターの名前。 |
| `Group` |グループの名前。 |
| `GroupFullName` |ソースとソース名を含んだグループの完全パス。 |
| `GroupSource` |グループの収集元となったソース。 <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| `GroupSourceName` |グループの収集元となったソースの名前。  Active Directory の場合はドメイン名になります。 |
| `ManagementGroupName` |SCOM エージェントの管理グループの名前。  その他のエージェントの場合、これは AOI-\<workspace ID\> です。 |
| `TimeGenerated` |コンピューター グループが作成または更新された日時。 |

## <a name="next-steps"></a>次の手順
* [ログ クエリ](../log-query/log-query-overview.md)について学習し、データ ソースとソリューションから収集されたデータを分析します。  

