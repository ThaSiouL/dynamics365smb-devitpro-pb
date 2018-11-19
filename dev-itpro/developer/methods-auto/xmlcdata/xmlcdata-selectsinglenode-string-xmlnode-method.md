---
title: "SelectSingleNode Method"
ms.author: solsen
ms.custom: na
ms.date: 10/17/2018
ms.reviewer: na
ms.suite: na
ms.tgt_pltfrm: na
ms.topic: article
ms.service: "dynamics365-business-central"
author: solsen
---
[//]: # (START>DO_NOT_EDIT)
[//]: # (IMPORTANT:Do not edit any of the content between here and the END>DO_NOT_EDIT.)
[//]: # (Any modifications should be made in the .xml files in the ModernDev repo.)
# SelectSingleNode Method
Selects the first XmlNode that matches the XPath expression.

## Syntax
```
[Ok := ]  XmlCData.SelectSingleNode(XPath: String, var Node: XmlNode)
```
## Parameters
*XmlCData*  
&emsp;Type: [XmlCData](xmlcdata-data-type.md)  
An instance of the [XmlCData](xmlcdata-data-type.md) data type.  

*XPath*  
&emsp;Type: [String](../string/string-data-type.md)  
The XPath expression.
        
*Node*  
&emsp;Type: [XmlNode](../xmlnode/xmlnode-data-type.md)  
The first XmlNode that matches the XPath query.  


## Return Value
*Ok*  
&emsp;Type: [Boolean](../boolean/boolean-data-type.md)  
**true** if the operation was successful; otherwise **false**.  If you omit this optional return value and the operation does not execute successfully, a runtime error will occur.    


[//]: # (IMPORTANT: END>DO_NOT_EDIT)
## See Also
[XmlCData Data Type](xmlcdata-data-type.md)  
[Getting Started with AL](../../devenv-get-started.md)  
[Developing Extensions](../../devenv-dev-overview.md)