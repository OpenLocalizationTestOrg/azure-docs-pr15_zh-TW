<properties
    pageTitle="檢視 SAML 所傳回的存取控制服務 (Java)"
    description="瞭解如何檢視 SAML 存取控制服務裝載於 Azure Java 應用程式中傳回。"
    services="active-directory" 
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm" />

# <a name="how-to-view-saml-returned-by-the-azure-access-control-service"></a>如何檢視 SAML 所傳回的 Azure 存取控制服務

本指南會顯示如何檢視基礎安全性判斷提示標記語言 (SAML) 的形式傳回至您的應用程式來 Azure 存取控制服務 (ACS)。 本指南建置[Azure 存取控制服務使用的蝕驗證 Web 使用者如何][]主題中，藉由顯示 SAML 資訊的程式碼。 完成的應用程式看起來類似下列項目。

![範例 SAML 輸出][saml_output]

如需有關 ACS 的詳細資訊，請參閱[下一步](#next_steps)] 區段。

> [AZURE.NOTE]
> Azure Access Services 控制項篩選是社群技術預覽。 測試版軟體，為它不正式支援由 Microsoft。

## <a name="prerequisites"></a>必要條件

完成本指南中的工作完成[驗證 Web 使用者 Azure 存取控制服務使用的蝕][]如何範例，此教學課程中使用的起點。

## <a name="add-the-jspwriter-library-to-your-build-path-and-deployment-assembly"></a>新增組件建立路徑和部署 JspWriter 文件庫

新增包含**javax.servlet.jsp.JspWriter**類別建立路徑和部署組件的文件庫。 如果您使用的 Tomcat，文件庫是**jsp api.jar**，位於 Apache**的文件庫**資料夾中。

1. 蝕的 [專案總管] 中以滑鼠右鍵按一下**MyACSHelloWorld****建立路徑**、 按一下 [**設定建立路徑**、 按一下 [**文件庫**] 索引標籤，然後按一下然後按一下 [**新增外部罐和**。
2. **JAR 選取範圍**] 對話方塊中瀏覽至必要 JAR、 加以選取，然後再按一下 [**開啟**。
3. 使用 [仍開啟**MyACSHelloWorld 的摘要資訊**] 對話方塊，按一下 [**部署組件**]。
4. 在 [ **Web 部署組件**] 對話方塊中，按一下 [**新增**]。
5. 在 [**新的組件指引**] 對話方塊中，按一下**Java 建立路徑項目**，然後按 [**下一步**。
6. 選取適當的文件庫，然後按一下 [**完成**]。
7. 按一下**[確定**] 關閉 [ **MyACSHelloWorld 屬性**] 對話方塊。

## <a name="modify-the-jsp-file-to-display-saml"></a>修改 JSP 檔，以顯示 SAML

修改**index.jsp**使用下列程式碼。

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

## <a name="run-the-application"></a>執行應用程式

1. 在電腦模擬器中執行應用程式或部署至 Azure，使用 [[驗證網站使用者 Azure 存取控制服務使用的蝕][]如何記錄的步驟。
2. 啟動瀏覽器並開啟您的 web 應用程式。 您登入您的應用程式後，您會看到 SAML 資訊，包括身分識別提供者所提供的安全性判斷提示。

## <a name="next-steps"></a>後續步驟

若要進一步瀏覽 ACS 的功能和試驗更複雜的情況，請參閱[Access 控制項服務 2.0][]。

[Prerequisites]: #pre
[Modify the JSP file to display SAML]: #modify_jsp
[Add the JspWriter library to your build path and deployment assembly]: #add_library
[Run the application]: #run_application
[Next steps]: #next_steps
[存取控制服務 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[如何驗證使用蝕 Azure 存取控制服務的網路使用者]: ../active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png
 