---
title: "Access Control Service によって返される SAML の表示 (Java)"
description: "Azure でホストされる Java アプリケーションで Access Control Service によって返される SAML を表示する方法について説明します。"
services: active-directory
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 6cd216f9-eb43-46b4-b30d-f194d0ae2d48
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 06d30b8222cd0590809b2fd5a1241394056c6338
ms.openlocfilehash: ac91a1c753611f4002a930dadeaeba2fcd53a324


---
# <a name="how-to-view-saml-returned-by-the-azure-access-control-service"></a>How to view SAML returned by the Azure Access Control Service (Azure の Access Control Service によって返される SAML を表示する方法)
このガイドでは、Azure の Access Control Service (ACS) によって基盤として使用され、アプリケーションに返される SAML (Security Assertion Markup Language) を表示する方法について説明します。 このガイドでは、「[Eclipse を使用して Azure Access Control Service で Web ユーザーを認証する方法](active-directory-java-authenticate-users-access-control-eclipse.md)」で作成したコードを基にして、SAML 情報を表示するコードを完成させます。 完成したアプリケーションは次のようになります。

![SAML の出力例][saml_output]

ACS の詳細については、「 [次のステップ](#next_steps) 」を参照してください。

> [!NOTE]
> Azure Access Control Services Filter は Community Technology Preview 版です。 プレリリース版ソフトウェアとしてマイクロソフトによって正式にサポートされていません。
> 
> 

## <a name="prerequisites"></a>前提条件
このガイド内のタスクを完了するには、「[Eclipse を使用して Azure の Access Control Service で Web ユーザーを認証する方法](active-directory-java-authenticate-users-access-control-eclipse.md)」のサンプル コードを完成させて、このチュートリアルでの出発点として使用します。

## <a name="add-the-jspwriter-library-to-your-build-path-and-deployment-assembly"></a>ビルド パスとデプロイ アセンブリに JspWriter ライブラリを追加する
**javax.servlet.jsp.JspWriter** クラスを格納するライブラリをビルド パスとデプロイ アセンブリに追加します。 Tomcat を使用している場合、ライブラリは Apache の **lib** フォルダー内の **jsp-api.jar** です。

1. Eclipse の Project Explorer で、**MyACSHelloWorld** を右クリックし、**[Build Path]**、**[Configure Build Path]**、**[Libraries]** タブ、**[Add External JARs]** の順にクリックします。
2. **[JAR Selection]** ダイアログ ボックスで、必要な JAR に移動して選択し、**[Open]** をクリックします。
3. **[Properties for MyACSHelloWorld]** ダイアログ ボックスが開いた状態で、**[Deployment Assembly]** をクリックします。
4. **[Web Deployment Assembly]** ダイアログ ボックスで **[Add]** をクリックします。
5. **[New Assembly Directive]** ダイアログ ボックスで、**[Java Build Path Entries]**、**[Next]** の順にクリックします。
6. 該当するライブラリを選択し、 **[Finish]**をクリックします。
7. **[OK]** をクリックして **[Properties for MyACSHelloWorld]** ダイアログ ボックスを閉じます。

## <a name="modify-the-jsp-file-to-display-saml"></a>SAML を表示するように JSP ファイルを変更する
次のコードを使用するように **index.jsp** を変更します。

    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
        <%@ page import="javax.xml.parsers.*"
                 import="javax.xml.transform.*"
                 import="org.w3c.dom.*"
                 import="java.io.*"
                 import="javax.xml.transform.stream.*"
                 import="javax.xml.transform.dom.*"
                 import="javax.xml.xpath.*"
                 import="javax.servlet.jsp.JspWriter" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Sample ACS Filter</title>
    </head>
    <body>
        <h3>SAML information from sample ACS program</h3>
        <%!
        void displaySAMLInfo(Node node, String parent, JspWriter out)
        {

            try
            {
                String nodeName;
                int nChild, i;

                nodeName = node.getNodeName();
                out.println("<br>");
                out.println("<u>Examining <b>" + parent + nodeName + "</b></u><br>");

                   // Attributes.
                   NamedNodeMap attribsMap = node.getAttributes();
                   if (null != attribsMap)
                   {
                         for (i=0; i < attribsMap.getLength(); i++)
                         {
                                Node attrib = attribsMap.item(i);
                                out.println("Attribute: <b>" + attrib.getNodeName() + "</b>: " + attrib.getNodeValue()  + "<br>");
                         }
                   }

                   // Child nodes.
                   NodeList list = node.getChildNodes();
                   if (null != list)
                    {
                          nChild = list.getLength();
                          if (nChild > 0)
                          {                    

                                 // If it is a text node, just print the text.
                                 if (list.item(0).getNodeName() == "#text")
                                 {
                                     out.println("Text value: <b>" + list.item(0).getTextContent() + "</b><br>");
                                 }
                                 else
                                 {
                                     // Print out the child node names.
                                     out.print("Contains " + nChild + " child node(s): ");   
                                        for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);

                                        out.print("<b>" + temp.getNodeName() + "</b>");
                                        if (i < nChild - 1)
                                        {
                                            // Separate the names.
                                            out.print(", ");
                                        }
                                        else
                                        {
                                            // Finish the sentence.
                                            out.print(".");
                                        }

                                     }
                                     out.println("<br>");

                                     // Process the child nodes.
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        displaySAMLInfo(temp, parent + nodeName + "\\", out);
                                     }
                               }
                          }
                      }
                  }
                catch (Exception e)
                {
                    System.out.println("Exception encountered.");
                    e.printStackTrace();            
                }
            }
        %>

        <%
        try
        {
            String data  = (String) request.getAttribute("ACSSAML");

            DocumentBuilder docBuilder;
            Document doc = null;
            DocumentBuilderFactory docBuilderFactory = DocumentBuilderFactory.newInstance();
            docBuilderFactory.setIgnoringElementContentWhitespace(true);
            docBuilder = docBuilderFactory.newDocumentBuilder();
            byte[] xmlDATA = data.getBytes();

            ByteArrayInputStream in = new ByteArrayInputStream(xmlDATA);
            doc = docBuilder.parse(in);
            doc.getDocumentElement().normalize();

            // Iterate the child nodes of the doc.
            NodeList list = doc.getChildNodes();

            for (int i=0; i < list.getLength(); i++)
            {
                displaySAMLInfo(list.item(i), "", out);
            }
        }
        catch (Exception e)
        {
            out.println("Exception encountered.");
            e.printStackTrace();
        }

        %>
    </body>
    </html>

## <a name="run-the-application"></a>アプリケーションの実行
1. アプリケーションをコンピューター エミュレーターで実行するか、「[Eclipse を使用して Azure の Access Control Service で Web ユーザーを認証する方法](active-directory-java-authenticate-users-access-control-eclipse.md)」の手順に従って Azure にデプロイします。
2. ブラウザーを起動し、Web アプリケーションを開きます。 アプリケーションにログオンした後、SAML 情報 (ID プロバイダーから提供されるセキュリティ アサーションなど) が表示されます。

## <a name="next-steps"></a>次のステップ
さらに ACS の機能を調べたり、より洗練されたシナリオを試してみたりする場合は、「[Access Control Service 2.0][Access Control Service 2.0]」を参照してください。

[Prerequisites]: #pre
[Modify the JSP file to display SAML]: #modify_jsp
[Add the JspWriter library to your build path and deployment assembly]: #add_library
[Run the application]: #run_application
[Next steps]: #next_steps
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[How to Authenticate Web Users with Azure Access Control Service Using Eclipse]: active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png



<!--HONumber=Jan17_HO3-->


