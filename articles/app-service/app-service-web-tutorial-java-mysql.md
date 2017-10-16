---
title: "Azure で Java と MySQL Web アプリを構築する"
description: "Azure MySQL データベース サービスに接続する Java アプリを Azure App Service で動作させる方法について説明します。"
services: app-service\web
documentationcenter: Java
author: bbenz
manager: jeffsand
editor: jasonwhowell
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 05/22/2017
ms.author: bbenz
ms.custom: mvc
ms.openlocfilehash: 31af39eef3889bebd18baa2be297c433cc733ed3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="build-a-java-and-mysql-web-app-in-azure"></a>Azure で Java と MySQL Web アプリを構築する

このチュートリアルでは、Azure で Java Web アプリを作成し、MySQL データベースに接続する方法について説明します。 完了すると、[Azure App Service Web Apps](app-service-web-overview.md) で実行中の [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/overview) にデータを格納する [Spring Boot](https://projects.spring.io/spring-boot/) アプリケーションが完成します。

![Azure App Service で実行される Java アプリ](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure で MySQL データベースを作成する
> * サンプル アプリをデータベースに接続する
> * Azure にアプリケーションをデプロイする
> * アプリを更新して再デプロイする
> * Azure から診断ログをストリーミングする
> * Azure Portal でアプリを監視する


## <a name="prerequisites"></a>前提条件

1. [Git をダウンロードし、インストールします](https://git-scm.com/)
1. [Java 7 JDK 以降をダウンロードし、インストールします](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
1. [MySQL をダウンロード、インストール、および起動します](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql"></a>ローカル MySQL を準備する 

この手順では、ご利用のローカル コンピューターでのアプリのテストに使用するローカル MySQL サーバーにデータベースを作成します。

### <a name="connect-to-mysql-server"></a>MySQL サーバーに接続する

ターミナル ウィンドウで、ローカル MySQL サーバーに接続します。 このチュートリアルでは、ターミナル ウィンドウを使ってすべてのコマンドを実行します。

```bash
mysql -u root -p
```

パスワードの入力を求められたら、`root` アカウントのパスワードを入力します。 ルート アカウントのパスワードを思い出せない場合は、「[MySQL: root のパスワードをリセットする方法](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html)」を参照してください。

コマンドが正常に実行されれば、MySQL サーバーは既に実行されています。 正常に実行されない場合は、[MySQL のインストール後の手順](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html)に従って、MySQL サーバーが起動されたことを確認してください。

### <a name="create-a-database"></a>データベースを作成する 

`mysql` プロンプトで、データベースと To-Do 項目テーブルを作成します。

```sql
CREATE DATABASE tododb;
```

「`quit`」と入力して、サーバー接続を終了します。

```sql
quit
```

## <a name="create-and-run-the-sample-app"></a>サンプル アプリの作成と実行 

この手順では、サンプル Spring Boot アプリを複製し、ローカルの MySQL データベースを使用するように構成してから、ご利用のコンピューターで実行します。 

### <a name="clone-the-sample"></a>サンプルを複製する

ターミナル ウィンドウで、作業ディレクトリに移動し、サンプル リポジトリを複製します。 

```bash
git clone https://github.com/azure-samples/mysql-spring-boot-todo
```

### <a name="configure-the-app-to-use-the-mysql-database"></a>MySQL データベースを使用するようにアプリを構成する

MySQL プロンプトを開くのに使用したのと同じルート パスワードで、`spring.datasource.password` および *spring-boot-mysql-todo/src/main/resources/application.properties* の値を更新します。

```
spring.datasource.password=mysqlpass
```

### <a name="build-and-run-the-sample"></a>サンプルのビルドと実行

リポジトリに含まれている Maven Wrapper を使用してサンプルをビルドし、実行します。

```bash
cd spring-boot-mysql-todo
mvnw package spring-boot:run
```

ブラウザーで `http://localhost:8080` を開き、サンプルの動作を確認します。 一覧にタスクを追加する際は、MySQL プロンプトで次の SQL コマンドを使用して、MySQL に格納されているデータを表示します。

```SQL
use testdb;
select * from todo_item;
```

ターミナルで `Ctrl`+`C` を押してアプリケーションを停止します。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-mysql-database"></a>Azure MySQL データベースを作成する

この手順では、[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) を使用して [Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-cli.md) インスタンスを作成します。 後のチュートリアルでこのデータベースを使用できるように、サンプル アプリケーションを構成します。

### <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#create) コマンドを使用して、[リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 Azure リソース グループとは、Web アプリ、データベース、ストレージ アカウントなどの関連リソースをデプロイおよび管理される論理コンテナーです。 

次の例は、北ヨーロッパ リージョンにリソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location "North Europe"
```    

`--location` に使用できる値を確認するには、[az appservice list-locations](/cli/azure/appservice#list-locations) コマンドを使用します。

### <a name="create-a-mysql-server"></a>MySQL サーバーを作成する

Cloud Shell で [az mysql server create](/cli/azure/mysql/server#create) コマンドを使用し、Azure Database for MySQL (プレビュー) でサーバーを作成します。    
`<mysql_server_name>` プレースホルダーを独自の一意の MySQL サーバー名に置き換えます。 この名前は、MySQL サーバーのホスト名 (`<mysql_server_name>.mysql.database.azure.com`) の一部であるため、グローバルに一意である必要があります。 `<admin_user>` と `<admin_password>` も独自の値に置き換えます。

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user <admin_user> --admin-password <admin_password>
```

MySQL サーバーが作成されると、Azure CLI によって、次の例のような情報が表示されます。

```json
{
  "administratorLogin": "admin_user",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mysql_server_name.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysql_server_name",
  "location": "northeurope",
  "name": "mysql_server_name",
  "resourceGroup": "mysqlJavaResourceGroup",
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>サーバーのファイアウォールを構成する

Cloud Shell で [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#create) コマンドを使用し、MySQL サーバーのクライアントへの接続を許可するファイアウォール ルールを作成します。 

```azurecli-interactive
az mysql server firewall-rule create --name allIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> 現時点では、Azure Database for MySQL (プレビュー) は、Azure Services からの接続を自動的に有効にすることはできません。 Azure の IP アドレスは動的に割り当てられるため、当面はすべての IP アドレスを有効にしておくことをお勧めします。 サービスは引き続きプレビュー段階にあり、データベースを保護するためのより優れた方法が利用可能になる予定です。

## <a name="configure-the-azure-mysql-database"></a>Azure MySQL データベースを構成する

ローカルのターミナル ウィンドウで、Azure の MySQL サーバーに接続します。 `<admin_user>` と `<mysql_server_name>` に指定した値を使用します。

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-database"></a>データベースを作成する 

`mysql` プロンプトで、データベースと To-Do 項目テーブルを作成します。

```sql
CREATE DATABASE tododb;
```

### <a name="create-a-user-with-permissions"></a>アクセス許可を持つユーザーを作成する

データベース ユーザーを作成し、`tododb` データベースのすべての特権を付与します。 プレースホルダーの `<Javaapp_user>` と `<Javaapp_password>` を独自の一意の名前に置き換えます。

```sql
CREATE USER '<Javaapp_user>' IDENTIFIED BY '<Javaapp_password>'; 
GRANT ALL PRIVILEGES ON tododb.* TO '<Javaapp_user>';
```

「`quit`」と入力して、サーバー接続を終了します。

```sql
quit
```

## <a name="deploy-the-sample-to-azure-app-service"></a>サンプルを Azure App Service にデプロイする

[az appservice plan create](/cli/azure/appservice/plan#create) CLI コマンドを使用して、**Free** 価格レベルの Azure App Service プランを作成します。 App Service プランは、アプリをホストするために使用される物理リソースを定義します。 App Service プランに割り当てられたすべてのアプリケーションは、これらのリソースを共有します。これにより、複数のアプリをホストする際にコストを節約できます。 

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

プランの準備が完了すると、Azure CLI で次の例のような出力が表示されます。

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Azure Web アプリを作成する

 Cloud Shell で [az webapp create](/cli/azure/appservice/web#create) CLI コマンドを使用し、`myAppServicePlan` App Service プランで Web アプリ定義を作成します。 Web アプリ定義によって、アプリケーションにアクセスするための URL が提供され、Azure にコードをデプロイするためのいくつかのオプションが構成されます。 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

`<app_name>` プレースホルダーを独自の一意のアプリ名で置き換えます。 この一意の名前は、Web アプリの既定のドメイン名の一部です。そのため、この名前は Azure のすべてのアプリで一意である必要があります。 Web アプリをユーザーに公開する前に、カスタム ドメイン名エントリを Web アプリにマップできます。

Web アプリ定義の準備が完了すると、Azure CLI によって次の例のような情報が表示されます。 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>Java を構成する 

Cloud Shell で [az appservice web config update](/cli/azure/appservice/web/config#update) コマンドを使用し、アプリで必要な Java ランタイム構成を設定します。

次のコマンドでは、最新の Java 8 JDK および [Apache Tomcat](http://tomcat.apache.org/) 8.0 で動作するように Web アプリを構成します。

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --java-version 1.8 --java-container Tomcat --java-container-version 8.0
```

### <a name="configure-the-app-to-use-the-azure-sql-database"></a>Azure SQL データベースを使用するようにアプリを構成する

サンプル アプリを実行する前に、Azure で作成した Azure MySQL データベースを使用するように Web アプリのアプリケーション設定を行います。 これらのプロパティは環境変数として Web アプリケーションに公開され、パッケージ化された Web アプリ内の application.properties で設定されている値をオーバーライドします。 

Cloud Shell で [az webapp config appsettings](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings) を使用してアプリケーションの設定を行います。

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_URL="jdbc:mysql://<mysql_server_name>.mysql.database.azure.com:3306/tododb?verifyServerCertificate=true&useSSL=true&requireSSL=false" --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_USERNAME=Javaapp_user@mysql_server_name --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_URL=Javaapp_password --resource-group myResourceGroup --name <app_name>
```

### <a name="get-ftp-deployment-credentials"></a>FTP デプロイ資格情報を取得する 
アプリケーションを Azure App Service にデプロイするには、FTP、ローカル Git、GitHub、Visual Studio Team Services、BitBucket など、さまざまな方法があります。 この例では、事前にご利用のローカル コンピューターでビルドした .WAR ファイルを Azure App Service にデプロイするために FTP を使用します。

ftp コマンドで Web アプリに渡す資格情報を確認するには、Cloud Shell で [az appservice web deployment list-publishing-profiles](https://docs.microsoft.com/cli/azure/appservice/web/deployment#az_appservice_web_deployment_list_publishing_profiles) コマンドを次のように使用します。 

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app_name> --resource-group myResourceGroup --query "[?publishMethod=='FTP'].{URL:publishUrl, Username:userName,Password:userPWD}" --output json
```

```JSON
[
  {
    "Password": "aBcDeFgHiJkLmNoPqRsTuVwXyZ",
    "URL": "ftp://waws-prod-blu-069.ftp.azurewebsites.windows.net/site/wwwroot",
    "Username": "app_name\\$app_name"
  }
]
```

### <a name="upload-the-app-using-ftp"></a>FTP を使用してアプリをアップロードする

お好みの FTP ツールを使用して、上記のコマンドで `URL` フィールドから取得したサーバー アドレス上の */site/wwwroot/webapps* フォルダーに .WAR ファイルをデプロイします。 既存の既定 (ROOT) アプリケーション ディレクトリを削除して、既存の ROOT.war を、このチュートリアルでビルド済みの .WAR ファイルで置き換えます。

```bash
ftp waws-prod-blu-069.ftp.azurewebsites.windows.net
Connected to waws-prod-blu-069.drip.azurewebsites.windows.net.
220 Microsoft FTP Service
Name (waws-prod-blu-069.ftp.azurewebsites.windows.net:raisa): app_name\$app_name
331 Password required
Password:
cd /site/wwwroot/webapps
mdelete -i ROOT/*
rmdir ROOT/
put target/TodoDemo-0.0.1-SNAPSHOT.war ROOT.war
```

### <a name="test-the-web-app"></a>Web アプリをテストする

`http://<app_name>.azurewebsites.net/` を参照し、一覧にいくつかのタスクを追加します。 

![Azure App Service で実行される Java アプリ](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

**お疲れさまでした。** データ主導型の Java アプリが Azure App Service で実行されています。

## <a name="update-the-app-and-redeploy"></a>アプリを更新して再デプロイする

ToDo リストに項目の作成日の列を追加するように、アプリケーションを更新します。 Spring Boot では、データ モデルの変更時に、既存のデータベース レコードを変更することなく、データベース スキーマの更新処理を自動で行います。

1. ローカル システムで *src/main/java/com/example/fabrikam/TodoItem.java* を開き、次のインポートをクラスに追加します。   

    ```java
    import java.text.SimpleDateFormat;
    import java.util.Calendar;
    ```

2. `String` プロパティの `timeCreated` を *src/main/java/com/example/fabrikam/TodoItem.java* に追加し、オブジェクト作成時のタイムスタンプで初期化します。 このファイルを編集する際に、新しい `timeCreated` プロパティのゲッターおよびセッターを追加します。

    ```java
    private String name;
    private boolean complete;
    private String timeCreated;
    ...

    public TodoItem(String category, String name) {
       this.category = category;
       this.name = name;
       this.complete = false;
       this.timeCreated = new SimpleDateFormat("MMMM dd, YYYY").format(Calendar.getInstance().getTime());
    }
    ...
    public void setTimeCreated(String timeCreated) {
       this.timeCreated = timeCreated;
    }

    public String getTimeCreated() {
        return timeCreated;
    }
    ```

3. *src/main/java/com/example/fabrikam/TodoDemoController.java* を `updateTodo` メソッドの行で更新し、タイムスタンプを設定します。

    ```java
    item.setComplete(requestItem.isComplete());
    item.setId(requestItem.getId());
    item.setTimeCreated(requestItem.getTimeCreated());
    repository.save(item);
    ```

4. Thymeleaf テンプレートの新しいフィールドのサポートを追加します。 *src/main/resources/templates/index.html* を、タイムスタンプ用の新しいテーブル ヘッダー、および各テーブル データ行にタイムスタンプの値を表示する新しいフィールドで更新します。

    ```html
    <th>Name</th>
    <th>Category</th>
    <th>Time Created</th>
    <th>Complete</th>
    ...
    <td th:text="${item.category}">item_category</td><input type="hidden" th:field="*{todoList[__${i.index}__].category}"/>
    <td th:text="${item.timeCreated}">item_time_created</td><input type="hidden" th:field="*{todoList[__${i.index}__].timeCreated}"/>
    <td><input type="checkbox" th:checked="${item.complete} == true" th:field="*{todoList[__${i.index}__].complete}"/></td>
    ```

5. 次のように、アプリケーションをリビルドします。

    ```bash
    mvnw clean package 
    ```

6. 前記のように更新済みの .WAR を FTP で転送して、既存の *site/wwwroot/webapps/ROOT* ディレクトリと *ROOT.war* を削除し、更新済みの .WAR ファイルを ROOT.war としてアップロードします。 

アプリを更新すると、**Time Created** 列が表示されるようになります。 新しいタスクを追加すると、アプリによってタイムスタンプが自動的に入力されます。 基になるデータ モデルが変更された場合でも、既存のタスクは変更されず、そのアプリで動作します。 

![新しい列で更新された Java アプリ](./media/app-service-web-tutorial-java-mysql/appservice-updates-java.png)
      
## <a name="stream-diagnostic-logs"></a>診断ログをストリーミングする 

Azure App Service でその Java アプリケーションを実行している間、コンソール ログをご利用のターミナルに直接パイプできます。 このようにすると、アプリケーション エラーのデバッグに役立つ同じ診断メッセージを取得できます。

ログのストリーミングを開始するには、Cloud Shell で [az webapp log tail](/cli/azure/appservice/web/log#tail) コマンドを使用します。

```azurecli-interactive 
az webapp log tail --name <app_name> --resource-group myResourceGroup 
``` 

## <a name="manage-your-azure-web-app"></a>Azure Web アプリを管理する

Azure Portal に移動し、作成した Web アプリを表示します。

そのためには、[https://portal.azure.com](https://portal.azure.com) にサインインします。

左側のメニューで **[App Service]** をクリックした後、Azure Web アプリの名前をクリックします。

![Azure Web アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-java-mysql/access-portal.png)

既定では、Web アプリのブレードは **[概要]** ページを表示します。 このページでは、アプリの動作状態を見ることができます。 ここでは、停止、開始、再開、削除のような管理タスクも行うことができます。 ブレードの左側にあるタブは、開くことができるさまざまな構成ページを示しています。

![Azure Portal の App Service ブレード](./media/app-service-web-tutorial-java-mysql/web-app-blade.png)

ブレードのこれらのタブは、Web アプリに追加することができるさまざまな優れた機能を示しています。 次の一覧では、ほんの一部の例を示しています。
* カスタム DNS 名をマップする
* カスタム SSL 証明書をバインドする
* 継続的なデプロイを構成する
* スケールアップとスケールアウトを行う
* ユーザー認証を追加する

## <a name="clean-up-resources"></a>リソースのクリーンアップ

これらのリソースが別のチュートリアルで不要である場合 (「[次のステップ](#next)」を参照)、Cloud Shell で次のコマンドを実行して削除することができます。 
  
```azurecli-interactive
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>次のステップ

> [!div class="checklist"]
> * Azure で MySQL データベースを作成する
> * サンプル Java アプリを MySQL に接続する
> * Azure にアプリケーションをデプロイする
> * アプリを更新して再デプロイする
> * Azure から診断ログをストリーミングする
> * Azure ポータルでアプリを管理する

次のチュートリアルに進み、カスタム DNS 名をアプリにマップする方法を学習してください。

> [!div class="nextstepaction"] 
> [既存のカスタム DNS 名を Azure Web Apps にマップする](app-service-web-tutorial-custom-domain.md)