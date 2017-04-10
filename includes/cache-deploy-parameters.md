
### <a name="cacheskuname"></a>cacheSKUName
新しい Azure Redis Cache の価格レベルです。

    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Redis Cache."
      }
    },

テンプレートでは、このパラメーターに指定できる値 (Basic または Standard) を定義します。値が指定されない場合は既定値 (Basic) が割り当てられます。 Basic では、複数のサイズを最大 53 GB まで使用できる 1 つのノードが提供されます。
Standard では、複数のサイズを最大 53 GB まで使用できる 2 つのノード (プライマリとレプリカ) が提供され、99.9% の SLA が実現されます。

### <a name="cacheskufamily"></a>cacheSKUFamily
SKU のファミリです。

    "cacheSKUFamily": {
      "type": "string",
      "allowedValues": [
        "C"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },


### <a name="cacheskucapacity"></a>cacheSKUCapacity
新しい Azure Redis Cache インスタンスのサイズです。 

    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Redis Cache instance. "
      }
    }


テンプレートには、このパラメーターに指定できる値 (0、1、2、3、4、5、または 6) を定義します。値が指定されない場合は既定値 (1) が割り当てられます。 これらの数値はキャッシュ サイズに対応します (0 = 250 MB、1 = 1 GB、2 = 2.5 GB、3 = 6 GB、4 = 13 GB、5 = 26 GB、6 = 53 GB)。



<!--HONumber=Nov16_HO3-->


