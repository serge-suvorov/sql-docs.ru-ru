---
title: "EXISTS (Transact-SQL) | Документы Microsoft"
ms.custom: 
ms.date: 03/15/2017
ms.prod: sql-non-specified
ms.reviewer: 
ms.suite: 
ms.technology:
- database-engine
ms.tgt_pltfrm: 
ms.topic: language-reference
f1_keywords:
- EXISTS_TSQL
- EXISTS
dev_langs:
- TSQL
helpviewer_keywords:
- existence testing [SQL Server]
- testing existence
- EXISTS keyword
- subqueries [SQL Server], EXISTS keyword
- queries [SQL Server], comparing
- comparing queries
- NOT EXISTS keyword
- row existence testing [SQL Server]
ms.assetid: b6510a65-ac38-4296-a3d5-640db0c27631
caps.latest.revision: 44
author: BYHAM
ms.author: rickbyh
manager: jhubbard
ms.translationtype: MT
ms.sourcegitcommit: 876522142756bca05416a1afff3cf10467f4c7f1
ms.openlocfilehash: c620baae23d9cab28142ee890ee103edbe2ee114
ms.contentlocale: ru-ru
ms.lasthandoff: 09/01/2017

---
# <a name="exists-transact-sql"></a>EXISTS (Transact-SQL)
[!INCLUDE[tsql-appliesto-ss2008-all_md](../../includes/tsql-appliesto-ss2008-all-md.md)]

  Указывает вложенный запрос для проверки существования строк.  
  
 ![Значок ссылки на раздел](../../database-engine/configure-windows/media/topic-link.gif "Значок ссылки на раздел") [Синтаксические обозначения в Transact-SQL](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>Синтаксис  
  
```  
EXISTS ( subquery )  
```  
  
## <a name="arguments"></a>Аргументы  
 *вложенный запрос*  
 Ограниченная инструкция SELECT. Ключевое слово INTO не допускается. Дополнительные сведения см. в разделе о вложенных запросах в [SELECT &#40; Transact-SQL &#41; ](../../t-sql/queries/select-transact-sql.md).  
  
## <a name="result-types"></a>Типы результата  
 **Логическое значение**  
  
## <a name="result-values"></a>Результирующие значения  
 Возвращает значение TRUE, если вложенный запрос содержит хотя бы одну строку.  
  
## <a name="examples"></a>Примеры  
  
### <a name="a-using-null-in-a-subquery-to-still-return-a-result-set"></a>A. Использование значения NULL во вложенном запросе для возвращения результирующего набора  
 В следующем примере возвращается результирующий набор со значением `NULL`, указанным во вложенном запросе, и устанавливается значение TRUE с помощью ключевого слова `EXISTS`.  
  
```  
-- Uses AdventureWorks  
  
SELECT DepartmentID, Name   
FROM HumanResources.Department   
WHERE EXISTS (SELECT NULL)  
ORDER BY Name ASC ;  
```  
  
### <a name="b-comparing-queries-by-using-exists-and-in"></a>Б. Сравнение запросов с помощью ключевых слов EXISTS и IN  
 В следующем примере сравниваются два семантически эквивалентных запроса. В первом запросе используется ключевое слово `EXISTS`, а во втором — ключевое слово `IN`.  
  
```  
-- Uses AdventureWorks  
  
SELECT a.FirstName, a.LastName  
FROM Person.Person AS a  
WHERE EXISTS  
(SELECT *   
    FROM HumanResources.Employee AS b  
    WHERE a.BusinessEntityID = b.BusinessEntityID  
    AND a.LastName = 'Johnson');  
GO  
```  
  
 В следующем запросе используется ключевое слово `IN`.  
  
```  
-- Uses AdventureWorks  
  
SELECT a.FirstName, a.LastName  
FROM Person.Person AS a  
WHERE a.LastName IN  
(SELECT a.LastName  
    FROM HumanResources.Employee AS b  
    WHERE a.BusinessEntityID = b.BusinessEntityID  
    AND a.LastName = 'Johnson');  
GO  
```  
  
 Здесь приведен результирующий набор для каждого запроса.  
  
 `FirstName                                          LastName`  
  
 `-------------------------------------------------- ----------`  
  
 `Barry                                              Johnson`  
  
 `David                                              Johnson`  
  
 `Willis                                             Johnson`  
  
 `(3 row(s) affected)`  
  
### <a name="c-comparing-queries-by-using-exists-and--any"></a>В. Сравнение запросов с помощью ключевых слов EXISTS и = ANY  
 В следующем примере показаны два запроса для поиска магазинов, названия которых совпадают с названием поставщика. В первом запросе используется `EXISTS` и во втором методе используется `=``ANY`.  
  
```  
-- Uses AdventureWorks  
  
SELECT DISTINCT s.Name  
FROM Sales.Store AS s   
WHERE EXISTS  
(SELECT *  
    FROM Purchasing.Vendor AS v  
    WHERE s.Name = v.Name) ;  
GO  
```  
  
 В следующем запросе используется ключевое слово `= ANY`.  
  
```  
-- Uses AdventureWorks  
  
SELECT DISTINCT s.Name  
FROM Sales.Store AS s   
WHERE s.Name = ANY  
(SELECT v.Name  
    FROM Purchasing.Vendor AS v ) ;  
GO  
```  
  
### <a name="d-comparing-queries-by-using-exists-and-in"></a>Г. Сравнение запросов с помощью ключевых слов EXISTS и IN  
 В следующем примере показаны запросы для поиска сотрудников подразделений, имена которых начинаются на `P`.  
  
```  
-- Uses AdventureWorks  
  
SELECT p.FirstName, p.LastName, e.JobTitle  
FROM Person.Person AS p   
JOIN HumanResources.Employee AS e  
   ON e.BusinessEntityID = p.BusinessEntityID   
WHERE EXISTS  
(SELECT *  
    FROM HumanResources.Department AS d  
    JOIN HumanResources.EmployeeDepartmentHistory AS edh  
       ON d.DepartmentID = edh.DepartmentID  
    WHERE e.BusinessEntityID = edh.BusinessEntityID  
    AND d.Name LIKE 'P%');  
GO  
```  
  
 В следующем запросе используется ключевое слово `IN`.  
  
```  
-- Uses AdventureWorks  
  
SELECT p.FirstName, p.LastName, e.JobTitle  
FROM Person.Person AS p JOIN HumanResources.Employee AS e  
   ON e.BusinessEntityID = p.BusinessEntityID   
JOIN HumanResources.EmployeeDepartmentHistory AS edh  
   ON e.BusinessEntityID = edh.BusinessEntityID   
WHERE edh.DepartmentID IN  
(SELECT DepartmentID  
   FROM HumanResources.Department  
   WHERE Name LIKE 'P%');  
GO  
```  
  
### <a name="e-using-not-exists"></a>Д. Использование ключевого слова NOT EXISTS  
 Работа ключевого слова NOT EXISTS противоположна работе ключевого слова EXISTS. Предложение WHERE в ключевом слове NOT EXISTS удовлетворяется, если вложенный запрос не возвратил никаких строк. В следующем примере выполняется поиск сотрудников, не входящих в состав подразделений, имена которых начинаются на `P`.  
  
```  
SELECT p.FirstName, p.LastName, e.JobTitle  
FROM Person.Person AS p   
JOIN HumanResources.Employee AS e  
   ON e.BusinessEntityID = p.BusinessEntityID   
WHERE NOT EXISTS  
(SELECT *  
   FROM HumanResources.Department AS d  
   JOIN HumanResources.EmployeeDepartmentHistory AS edh  
      ON d.DepartmentID = edh.DepartmentID  
   WHERE e.BusinessEntityID = edh.BusinessEntityID  
   AND d.Name LIKE 'P%')  
ORDER BY LastName, FirstName  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
 `FirstName                      LastName                       Title`  
  
 `------------------------------ ------------------------------ ------------`  
  
 `Syed                           Abbas                          Pacific Sales Manager`  
  
 `Hazem                          Abolrous                       Quality Assurance Manager`  
  
 `Humberto                       Acevedo                        Application Specialist`  
  
 `Pilar                          Ackerman                       Shipping & Receiving Superviso`  
  
 `François                       Ajenstat                       Database Administrator`  
  
 `Amy                            Alberts                        European Sales Manager`  
  
 `Sean                           Alexander                      Quality Assurance Technician`  
  
 `Pamela                         Ansman-Wolfe                   Sales Representative`  
  
 `Zainal                         Arifin                         Document Control Manager`  
  
 `David                          Barber                         Assistant to CFO`  
  
 `Paula                          Barreto de Mattos              Human Resources Manager`  
  
 `Shai                           Bassli                         Facilities Manager`  
  
 `Wanida                         Benshoof                       Marketing Assistant`  
  
 `Karen                          Berg                           Application Specialist`  
  
 `Karen                          Berge                          Document Control Assistant`  
  
 `Andreas                        Berglund                       Quality Assurance Technician`  
  
 `Matthias                       Berndt                         Shipping & Receiving Clerk`  
  
 `Jo                             Berry                          Janitor`  
  
 `Jimmy                          Bischoff                       Stocker`  
  
 `Michael                        Blythe                         Sales Representative`  
  
 `David                          Bradley                        Marketing Manager`  
  
 `Kevin                          Brown                          Marketing Assistant`  
  
 `David                          Campbell                       Sales Representative`  
  
 `Jason                          Carlson                        Information Services Manager`  
  
 `Fernando                       Caro                           Sales Representative`  
  
 `Sean                           Chai                           Document Control Assistant`  
  
 `Sootha                         Charncherngkha                 Quality Assurance Technician`  
  
 `Hao                            Chen                           HR Administrative Assistant`  
  
 `Kevin                          Chrisulis                      Network Administrator`  
  
 `Pat                            Coleman                        Janitor`  
  
 `Stephanie                      Conroy                         Network Manager`  
  
 `Debra                          Core                           Application Specialist`  
  
 `Ovidiu                         Crãcium                        Sr. Tool Designer`  
  
 `Grant                          Culbertson                     HR Administrative Assistant`  
  
 `Mary                           Dempsey                        Marketing Assistant`  
  
 `Thierry                        D'Hers                         Tool Designer`  
  
 `Terri                          Duffy                          VP Engineering`  
  
 `Susan                          Eaton                          Stocker`  
  
 `Terry                          Eminhizer                      Marketing Specialist`  
  
 `Gail                           Erickson                       Design Engineer`  
  
 `Janice                         Galvin                         Tool Designer`  
  
 `Mary                           Gibson                         Marketing Specialist`  
  
 `Jossef                         Goldberg                       Design Engineer`  
  
 `Sariya                         Harnpadoungsataya              Marketing Specialist`  
  
 `Mark                           Harrington                     Quality Assurance Technician`  
  
 `Magnus                         Hedlund                        Facilities Assistant`  
  
 `Shu                            Ito                            Sales Representative`  
  
 `Stephen                        Jiang                          North American Sales Manager`  
  
 `Willis                         Johnson                        Recruiter`  
  
 `Brannon                        Jones                          Finance Manager`  
  
 `Tengiz                         Kharatishvili                  Control Specialist`  
  
 `Christian                      Kleinerman                     Maintenance Supervisor`  
  
 `Vamsi                          Kuppa                          Shipping & Receiving Clerk`  
  
 `David                          Liu                            Accounts Manager`  
  
 `Vidur                          Luthra                         Recruiter`  
  
 `Stuart                         Macrae                         Janitor`  
  
 `Diane                          Margheim                       Research & Development Enginee`  
  
 `Mindy                          Martin                         Benefits Specialist`  
  
 `Gigi                           Matthew                        Research & Development Enginee`  
  
 `Tete                           Mensa-Annan                    Sales Representative`  
  
 `Ramesh                         Meyyappan                      Application Specialist`  
  
 `Dylan                          Miller                         Research & Development Manager`  
  
 `Linda                          Mitchell                       Sales Representative`  
  
 `Barbara                        Moreland                       Accountant`  
  
 `Laura                          Norman                         Chief Financial Officer`  
  
 `Chris                          Norred                         Control Specialist`  
  
 `Jae                            Pak                            Sales Representative`  
  
 `Wanda                          Parks                          Janitor`  
  
 `Deborah                        Poe                            Accounts Receivable Specialist`  
  
 `Kim                            Ralls                          Stocker`  
  
 `Tsvi                           Reiter                         Sales Representative`  
  
 `Sharon                         Salavaria                      Design Engineer`  
  
 `Ken                            Sanchez                        Chief Executive Officer`  
  
 `José                           Saraiva                        Sales Representative`  
  
 `Mike                           Seamans                        Accountant`  
  
 `Ashvini                        Sharma                         Network Administrator`  
  
 `Janet                          Sheperdigian                   Accounts Payable Specialist`  
  
 `Candy                          Spoon                          Accounts Receivable Specialist`  
  
 `Michael                        Sullivan                       Sr. Design Engineer`  
  
 `Dragan                         Tomic                          Accounts Payable Specialist`  
  
 `Lynn                           Tsoflias                       Sales Representative`  
  
 `Rachel                         Valdez                         Sales Representative`  
  
 `Garrett                        Vargar                         Sales Representative`  
  
 `Ranjit                         Varkey Chudukatil              Sales Representative`  
  
 `Bryan                          Walton                         Accounts Receivable Specialist`  
  
 `Jian Shuo                      Wang                           Engineering Manager`  
  
 `Brian                          Welcker                        VP Sales`  
  
 `Jill                           Williams                       Marketing Specialist`  
  
 `Dan                            Wilson                         Database Administrator`  
  
 `John                           Wood                           Marketing Specialist`  
  
 `Peng                           Wu                             Quality Assurance Supervisor`  
  
 `(91 row(s) affected)`  
  
## <a name="examples-includesssdwfullincludessssdwfull-mdmd-and-includesspdwincludessspdw-mdmd"></a>Примеры: [!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)] и[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="f-using-exists"></a>Е. Использование ключевого слова EXISTS  
 Следующий пример определяет, могут ли строки в `ProspectiveBuyer` таблицы может быть соответствует строкам в `DimCustomer` таблицы. Запрос будет возвращать строки только если как `LastName` и `BirthDate` значения в двух таблицах совпадают.  
  
```  
-- Uses AdventureWorks  
  
SELECT a.LastName, a.BirthDate  
FROM DimCustomer AS a  
WHERE EXISTS  
(SELECT *   
    FROM dbo.ProspectiveBuyer AS b  
    WHERE (a.LastName = b.LastName) AND (a.BirthDate = b.BirthDate));  
```  
  
### <a name="g-using-not-exists"></a>Ж. Использование ключевого слова NOT EXISTS  
 Оператор NOT EXISTS работает как EXISTS противоположно. Предложение WHERE в ключевом слове NOT EXISTS удовлетворяется, если вложенный запрос не возвратил никаких строк. В следующем примере вычисляется строк в `DimCustomer` таблицу, куда `LastName` и `BirthDate` не найдено ни одной записи в `ProspectiveBuyers` таблицы.  
  
```  
-- Uses AdventureWorks  
  
SELECT a.LastName, a.BirthDate  
FROM DimCustomer AS a  
WHERE NOT EXISTS  
(SELECT *   
    FROM dbo.ProspectiveBuyer AS b  
    WHERE (a.LastName = b.LastName) AND (a.BirthDate = b.BirthDate));  
```  
  
## <a name="see-also"></a>См. также:  
 [Выражения &#40; Transact-SQL &#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [Встроенные функции (Transact-SQL)](~/t-sql/functions/functions.md)   
 [ГДЕ &#40; Transact-SQL &#41;](../../t-sql/queries/where-transact-sql.md)  
  
  



