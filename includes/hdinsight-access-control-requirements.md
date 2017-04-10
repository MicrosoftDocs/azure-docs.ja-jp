会社が所有するサブスクリプションなど、自分が管理者でも所有者でもない Azure サブスクリプションを使用する場合は、この記事の手順を実行する前に、以下の点を確認してください。

* Azure にサインインするには、少なくとも、Azure リソース グループに対する共同作成者のアクセス権が必要です。 このリソース グループは、Azure HDInsight クラスターおよびその他の Azure リソースを作成するために使用します。
* これから使おうとするリソースのプロバイダーは、Azure サブスクリプションに対する共同作成者以上のアクセス権を持つユーザーによって前もって登録されていることが必要です。 プロバイダーの登録は、サブスクリプションに対する共同作成者アクセス権を持つユーザーが、そのサブスクリプションで初めてリソースを作成したときに行われます。 ただし、[REST を使ってプロバイダーを登録](https://msdn.microsoft.com/library/azure/dn790548.aspx)する方法もあり、その場合、リソースの作成は不要です。

アクセス管理の操作の詳細については、次の記事を参照してください。

* [Azure Portal でのアクセス管理の概要](../articles/active-directory/role-based-access-control-what-is.md)
* [Azure サブスクリプション リソースへのアクセスをロールの割り当てによって管理する](../articles/active-directory/role-based-access-control-configure.md)


<!--HONumber=Jan17_HO1-->


