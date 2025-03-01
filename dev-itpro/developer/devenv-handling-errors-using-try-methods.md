---
title: "Handling Errors by Using Try Methods"
description: Try methods in AL enable you to handle errors that occur in the application during code execution.
ms.custom: na
ms.date: 09/28/2022
ms.reviewer: na
ms.suite: na
ms.tgt_pltfrm: na
ms.topic: conceptual
author: SusanneWindfeldPedersen
---

# Handling Errors using Try Methods

Try methods in AL enable you to handle errors that occur in the application during code execution. For example, with try methods, you can provide more user-friendly error messages to the end user than those that are thrown by the system.  

> [!NOTE]
> Try Methods are available from runtime version 2.0.

## Behavior and usage

The main purpose of try methods is to catch errors/exceptions that are thrown by the [!INCLUDE[prod_short](includes/prod_short.md)] AL platform or exceptions that are thrown during .NET Framework interoperability operations (only for on-premises). Try methods catch errors similar to a conditional Codeunit. Except for the try method, the Run method call doesn't require that write transactions are committed to the database, and changes to the database that are made with a try method aren't rolled back.

### <a name="DbWriteTransactions"></a>Database write transactions in try methods

Because changes made to the database by a try method aren't rolled back, you shouldn't include database write transactions within a try method. By default, the [!INCLUDE[server](includes/server.md)] configuration prevents you from doing this. If a try method contains a database write transaction, a runtime error occurs.

### Handling errors with a return value

A method that is designated as a try method has a Boolean return value (**true** or **false**), and has the construction `OK:= MyTrymethod`. A try method can't have a user-defined return value.

- If a try method call doesn't use the return value, the try method operates like an ordinary method, and errors are exposed as usual.  

- If a try method call uses the return value in an `OK:=` statement or a conditional statement such as `if-then`, errors are caught. The try method returns `true` if no error occurs; `false` if an error occurs. 

> [!NOTE]  
> The return value isn't accessible within the try method itself.  

### Getting details about errors

You can use the [GetLastErrorText method](methods-auto/system/system-getlasterrortext--method.md) to obtain errors that are generated by [!INCLUDE[prod_short](includes/prod_short.md)]. To get details of exceptions that are generated by the AL platform or by .NET objects, you can use the [GetLastErrorObject method](methods-auto/system/system-getlasterrorobject-method.md) to inspect the `Exception.InnerException` property.

<!--
> [!TIP]  
> The [!INCLUDE[demolong](includes/demolong_md.md)] includes codeunit 1291 **DotNet Exception Handler** that includes several global methods for handling exceptions similar to a try-catch capability in C\#. You can use this codeunit together with try methods to handle exceptions and maximize the reuse of code.     -->

## Creating a try method

To create a try method, add a method in the AL code of an object such as a codeunit, and then set the [TryFunction Attribute](/dynamics365/business-central/dev-itpro/developer/attributes/devenv-tryfunction-attribute). 

<!-- A try method has the following restrictions:  

In test and upgrade codeunits, you can only use a try method on a normal method type.-->  

## Example

The following simple example illustrates how the try method works. First, create a codeunit that has a local method `MyTrymethod`. Add the following code on the `OnRun` trigger and `MyTrymethod` method.

```AL
trigger OnRun()
begin
    MyTrymethod;
    message('Everything went well');
end;
```

```AL
local procedure MyTryMethod()
begin
    error('An error occurred during the operation');
end;
```

When you run this codeunit, the execution of the `OnRun` trigger stops. The error message `An error occurred during the operation` is thrown in the UI.

Now, set the [TryFunction Attribute](/dynamics365/business-central/dev-itpro/developer/attributes/devenv-tryfunction-attribute) of the  `MyTrymethod` method. Then, add code to the `OnRun` trigger to handle the return value of the try method: 

```AL
[TryFunction]
local procedure MyTryMethod()
begin
    error('An error occurred during the operation');
end;

trigger OnRun()
begin
    if MyTryMethod then
        message('Everything went well')
    else
        message('Something went wrong')
end;
```

When you run the codeunit, instead of stopping the execution of the `OnRun` trigger when the error occurs, the error is caught and the message `Something went wrong` is returned.

<!--
## Example 2 

The following example illustrates how to use a try method with .NET interoperabilty. The example uses the [System.Decimal.Divide method](/dotnet/api/system.decimal.divide) to divide two decimals. 

First, create a codeunit that has a local method `MyTrymethod`, and add the following text constants and variables:

|Text constant name|ConstValue|
|----|----------|
|Text000|%1 divided by %2 equals %3.|
|Text001|You cannot divide by %1.|


|Variable name|DataType|Subtype|
|----|----------|----|----------|
|divide|DotNet|System.Decimal.'mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089'|
|d1|Decimal||
|d2|Decimal| |
|result|Decimal||

Then, add the following code on the `OnRun` trigger and `MyTrymethod` method.

**OnRun()**
```al
IF MyTrymethod THEN
  MESSAGE(Text000, d1, d2, result)
ELSE
  MESSAGE(Text001, d2);
```

**LOCAL MyTrymethod()**
```al
d1 := 3;
d2 := 0;
result := divide.Divide(d1,d2);
```

When you run this codeunit, an error occurs because you are not allowed to divide by `0`. The message `You cannot divide by 0.` is displayed in the client. 
-->
<!-- 
The following example illustrates the use of a try method together with codeunit 1291 **DotNet Exception Handler** to handle .NET Framework Interoperability exceptions. The code is in text file format and has been simplified for illustration. The `CallTryPostingDotNet` method runs the try method `TryPostSomething` in a conditional statement to catch .NET Framework Interoperability exceptions. Errors other than `IndexOutOfRangeException` type are re-thrown.  

```al
[Trymethod]  
PROCEDURE TryPostingSomething@1();  
BEGIN  
  CODEUNIT.RUN(CODEUNIT::"Purch.-Post");  
END;  

PROCEDURE CallTryPostingDotNet @2();  
VAR  
  MyPostingCodeunit@1 : Codeunit 90;  
  MyDotNetExceptionHandler@2 : Codeunit 1291;  
  IndexOutOfRangeException@3 : DotNet 'mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089'.System.IndexOutOfRangeException'  
BEGIN  
  IF TryPostingSomething THEN  
    MESSAGE('Posting succeeded.')  
  ELSE BEGIN  
    MyDotNetExceptionHandler.Collect;  
    IF MyDotNetExceptionHandler.TryCastToType(IndexOutOfRangeException) THEN  
      MESSAGE('The index used to find the value was not valid.')  
    ELSE  
      MyDotNetExceptionHandler.Rethrow;  
  END;  
END;  
```  
-->

## See Also  

[Failure modeling and robust coding practices](devenv-robust-coding-practices.md)  
[AL error handling](devenv-al-error-handling.md)   
[AL Simple Statements](devenv-al-simple-statements.md)