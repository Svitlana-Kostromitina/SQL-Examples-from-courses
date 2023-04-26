# SQL-Examples-from-courses

**1. Додати новий навчальний курс у таблиці Course.**

      Insert into Course (CourseId, Code, Title, Description, Amount, Status, CreateDate)

           Select '500', 'TS500', 'Test', 'Testing', '1000', 'A', CURRENT_TIMESTAMP

               Go
               
**2. Додати опис новоствореного курсу (п.1.) у таблиці CourseContent (мінімум 3 записи).**  

      Insert into CourseContent (CourseId, Chapter, ContentType, Title, Description)
           Select '500', '1', 'HW', 'Testing_1', ''
      Union All
           Select '500', '2', 'HW', 'Testing_2', ''
      Union All
           Select '500', '3', 'HW', 'Testing_3', ''
	             Go
               
**3. Додати нову навчальну групу у таблиці UserGroup.** 

      Insert into UserGroup (CourseId, Name, CreateDate, UpdateDate, Status)
           Select '500', 'TestTest', '2021-10-19', CURRENT_TIMESTAMP, 'A' 
	             Go
               
**4. Додати 2 нових користувача до таблиці UserData зі статусом 'D' та параметром 0 та 1.**

      Insert into UserData (Login, Password, FirstName, MiddleName, LastName, CreateDate, UpdateDate, Status, Parameter)
           Select 'Student1', '1111111', 'Олександр', 'Васильович', 'Шариков', CURRENT_TIMESTAMP, '2021-01-01', 'D', 0
      Union All
           Select 'Student2', '2222222', 'Світлана', 'Юріївна', 'Світлая', CURRENT_TIMESTAMP, '2021-01-01', 'D', 1
               Go
               
**5. Додати ще 2 користувача до таблиці UserData з іншим допустимим статусом та параметром 0 та 1.**      

      Insert into UserData (Login, Password, FirstName, MiddleName, LastName, CreateDate, UpdateDate, Status, Parameter)
           Select 'Student3', '3333333', 'Нікіта', 'Олександрович', 'Петров', CURRENT_TIMESTAMP, '2021-01-01', 'A', 0
	    Union All
           Select 'Student4', '4444444', 'Юлія', 'Олегівна', 'Бондаренко', CURRENT_TIMESTAMP, '2021-01-01', 'A', 1
               Go
               
**6. Змінити статус на 'активний' для своїх новостворених користувачів, у яких статус 'D'.
Змінити параметр на 1 для своїх новостворених користувачів, у яких параметр 0.
Також змінити дату з поля UpdateDate через функцію CURRENT_TIMESTAMP лише для своїх користувачів
у яких відбувалися зміни статусу та параметра.**     

       update UserData 
          set Status = 'A', Parameter = 1, UpdateDate = CURRENT_TIMESTAMP
        where Parameter = '0' and Login in ('Student1', 'Student2', 'Student3', 'Student4')
			                     or Status = 'D' and Login in ('Student1', 'Student2', 'Student3', 'Student4')
	                  Go
                    
                    
**7. Вибрати всіх користувачів зі СВОЄЇ ГРУПИ та свого КУРСУ, відсортувавши на ім'я користувача (FirstName). Вивести тільки ці колонки: Login, FirstName, MiddleName, LastName, [Name] as GroupName (колонка з таблиці UserGroup), Title (таблиця Course).** 

*Варіант 1*
   
       Select u.Login, u.FirstName, u.MiddleName, u.LastName, g.[Name] as GroupName, c.Title
         From UserData u INNER JOIN UserGroupLink l ON u.UserDataId = l.UserDataId
	                     INNER JOIN UserGroup g ON g.UserGroupId = l.UserGroupId
		             INNER JOIN Course c  ON c.CourseId = g.CourseId
        Where  g.[Name] = 'TestTest' AND c.Title = 'Test'
     Order by u.FirstName 
           Go

*Варіант2*

       Select u.Login, u.FirstName, u.MiddleName, u.LastName, g.[Name] as GroupName, c.Title
         From UserData u, UserGroupLink l, UserGroup g, Course c
        Where u.UserDataId = l.UserDataId
	          AND g.UserGroupId = l.UserGroupId
	          AND c.CourseId = g.CourseId
              AND g.[Name] = 'TestTest' AND c.Title = 'Test'
     Order by u.FirstName
           Go
           

**8. Видалити своїх користувачів з БД, створену групу та описники курсу та курс. Потрібно буде зробити роботу у зворотному порядку. Просто так користувача видалити не вийде, тому що він буде пов'язаний із новоствореною групою.**    

       Select *
         From UserGroupLink 
        Where UserDataId in (Select u.UserDataId 
                             From UserData u
		                 Where u.Login in ('Student1', 'Student2', 'Student3', 'Student4'))

       Delete
         From UserGroupLink 
        Where UserDataId in (Select u.UserDataId 
                             From UserData u
		                 Where u.Login in ('Student1', 'Student2', 'Student3', 'Student4'))
-------------------------------
       Select *
         From UserData u
        Where u.Login in ('Student1', 'Student2', 'Student3', 'Student4')
           

       Delete 
         From UserData 
        Where Login in ('Student1', 'Student2', 'Student3', 'Student4')
         	
-------------------------------
       Select *
         From  UserGroup u 
        Where u.Name = 'TestTest' AND u.CourseId = '500'
	

       Delete
         From UserGroup
        Where Name = 'TestTest' AND CourseId = '500'
	
-------------------------------
       Select *
         From CourseContent t
        Where t.Title in ('Testing_1', 'Testing_2', 'Testing_3') AND t.CourseId = '500'
        

       Delete
         From CourseContent 
        Where Title in ('Testing_1', 'Testing_2', 'Testing_3') AND CourseId = '500'
        
-------------------------------
       Select *
         From Course c
        Where c.CourseId = 500
        

       Delete
         From Course 
        Where CourseId = 500


## Практика написання запитів.
Під кожним завданням написати SELECT за умови

**1. Знайти документи в таблиці Payment, де сума (Amount) більша за 26024.92.**

       Select *
         From Payment p
        Where p.Amount >= 26024.92 
           Go
           
**2. Знайти документи у таблиці Payment, де призначення платежу (TXT) має слово 'рахунок' у будь-якому сегменті рядка,
та валюта (Currency) документів може бути '978', '980'**

       Select *
         From Payment p
        Where p.TXT LIKE '%рахунок%'
              and p.Currency in ('978', '980')
           Go
           
**3. Знайти документи в таблиці Payment, де дата документа між 2010-02-11 та 2011-05-22, та валюта документів НЕ дорівнює 980.**      

       Select *
         From Payment p
        Where p.Data_doc BETWEEN '2010-02-11' and '2011-05-22'
              and p.Currency != '980'
           Go
           
**4. Знайти документи в таблиці Payment, де Дебет рахунки (DebAcc) може мати такі рахунки '260070001', '260088791', '260006443',
Статус '+' та валюта НЕ 980.**

        Select *
          From Payment p
         Where p.DebAcc in ('260070001', '260088791', '260006443')
               and p.[Status] = '+'
		   and p.Currency <> '980'
            Go
            
**5. Знайти документи у таблиці Payment, де Дебет рахунки (DebAcc) може мати такі рахунки '260070001', '260088791', '260006443'.
При цьому вивести суму документа Amount помножену на 3 і від цієї суми відібрати 1000.**  

         Select p.Payment_Id, p.Data_doc, p.DebAcc, p.CredAcc, p.Currency, p.Amount *3 -1000 as Amount, p.AmountUAH, p.Status, p.TXT
           From Payment p
          Where p.DebAcc in ('260070001','260088791','260006443')
             Go 
            
 **6. Вивести рахунки з таблиці Account, де "(" Status дорівнює 'O' І дата відкриття OpenDate дорівнює 2016-01-21 ")"
АБО дата закриття CloseDate є НЕ NULL. Тут зверніть увагу на дужки за умови.**           
            
         Select *
           From Account a
          Where a.Status = 'O' and OpenDate = '2016-01-21'
                or a.CloseDate is not null
             Go     
             
**7. Вивести рахунки з таблиці Account, де Status дорівнює 'O' І "(" дата відкриття OpenDate дорівнює 2016-01-21 АБО дата
Закриття CloseDate є НЕ NULL ")".
Тут зверніть увагу на дужки за умови.**

          Select *
            From Account a
           Where a.Status = 'O'
                 and (OpenDate = '2016-01-21'  or  a.CloseDate is not null)      
              Go
              
**8. Вивести користувачів з таблиці Customer та Salary, у яких ДР '1977-09-26',
отримував Бонус та дата ЗП між '2019-03-31' та '2019-09-30'.**  

           Select * 
             From Customer c INNER JOIN Salary s ON c.Cust_Id = s.Cust_Id
            Where c.Birthday = '1977-09-26' AND s.Bonus is not null
                  and s.Date BETWEEN '2019-03-31' and '2019-09-30'
               Go
               
**9. Вивести інформацію про користувачів та його рахунки з таблиць Customer та Account, де:**
- Клієнти нерезиденти
- Рахунок починається на 2650
- друга частина вибірки
- Клієнти нерезиденти
- Код валюти 980.             

           Select c.CustomerLogin, c.NameUser, c.Resident, c.Country, c.Gender, a.Account, a.Currency, a.[Status], a.OpenDate
             From Customer c INNER JOIN Account a ON c.Cust_Id = a.Cust_Id
            Where c.Resident = 'N' and a.Account LIKE ('2650%')
           Union 
           Select c.CustomerLogin, c.NameUser, c.Resident, c.Country, c.Gender, a.Account, a.Currency, a.[Status], a.OpenDate
             From Customer c INNER JOIN Account a ON c.Cust_Id = a.Cust_Id
            Where c.Resident = 'N' and a.Currency = '980'
         Order by a.OpenDate desc
               Go
               
**10. Вибрати тих клієнтів, у яких була сума ЗП, але без бонусів і при цьому вони жодного разу не були оштрафовані,
і статус клієнта "Відкритий".**        

           Select *
             From Customer c LEFT JOIN Salary s  ON c.Cust_Id = s.Cust_Id
	                         LEFT JOIN Penalty p ON p.CustomerId = s.Cust_Id
            Where s.Amount is not NULL and s.Bonus is NULL
                           AND p.AmountPenalty IS NULL
                           AND c.Status = 'O'
               Go
               
**11. Завдання для "Алкоголіка".
Знайти платежі, призначення яких має фрагмент слова "алког" (у будь-якому сегменті призначення платежу).
Дебет DebAcc рахунки цих платежів використовуватиме для пошуку/зв'язку рахунку в таблиці рахунків Account.
А з таблиці Account зробити зв'язок на клієнта-власника із таблиці Customer.
Вивести лише поле NameUser.**

            Select c.NameUser
              From Payment p INNER JOIN Account a ON p.DebAcc = a.Account
	                         INNER JOIN Customer c ON c.Cust_Id = a.Cust_Id
             Where p.TXT LIKE '%алког%'
                Go
                
 **12. Вивести записи з таблиці UserLog, для яких ActionCode може мати значення '201', '401', '524'.**
 
            Select *
              From UserLog u
             Where u.ActionCode IN ('201','401','524')
                Go 
                
 **13. Вивести користувачів з таблиці Customer, які народилися до дати 1978-05-15 (включно) та країна 804.**
 
            Select *
              From Customer c
             Where c.Birthday <= '1978-05-15'
                   And c.Country = '804'
                Go
 
 **14. Знайти користувачів з таблиці Customer та UserLog, у яких код країни не дорівнює 804 та ActionCode починається на 2.
Вивести на еран такі поля: CustomerLogin, NameUser, Country, Resident, DateTime, ActionCode, ActionDescription.
Обов'язково використовувати аліаси**

            Select c.CustomerLogin, c.NameUser, c.Country, c.Resident, u.DateTime, u.ActionCode, u.ActionDescription
              From Customer c INNER JOIN UserLog u  ON  c.Cust_Id = u.IdCust
             Where c.Country <> '804'
                    And u.ActionCode LIKE '2%'
                Go
                
**15. Вивести користувачів із таблиці Customer, для яких немає записів про логування у таблиці UserLog**

            Select *
              From Customer c LEFT JOIN UserLog u  ON  c.Cust_Id = u.IdCust
             Where u.Id is null
                Go
                
**16. Вивести клієнтів з таблиці Customer та Penalty, які народилися раніше дати 1985-04-14 (не включно) або не мали штрафу.
Дані відсортувати за CustomerLogin у зростаючому порядку, а штрафи за датою у зворотному порядку.**       

            Select * 
              From Customer c LEFT JOIN Penalty p ON c.Cust_Id = p.CustomerId
             Where c.Birthday < '1985-04-14' 
                   or p.Id is null
          Order by c.CustomerLogin, p.[Date] desc
                Go
                
**17. Вивести список груп, що пов'язані з курсами (дані шукати в таблицях UserGroup та Course, проаналізувати зв'язок між ними та зв'язати).
Вивести на екран Name, CourseId, Code, Title, Description**

            Select u.Name, u.CourseId, c.Code, c.Title, c.Description 
              From UserGroup u INNER JOIN Course c ON u.CourseId = c.CourseId
                Go
                
**18. Вивести лише ті записи про людей, які мають роль 'Студент'. Проаналізувати таблиці UserData, UserRoleLink, UserRole та зв'язки між ними. Вибірка складатиметься із зв'язку 3-х таблиць і умови по ролі. Вивести колонки login, LastName, FirstName, MiddleName, Name**  

            Select u.Login, u.LastName, u.FirstName, u.MiddleName, r.Name
              From Userdata u INNER JOIN UserRoleLink l ON u.UserDataId = l.UserDataId
                              INNER JOIN UserRole r ON r.UserRoleId = l.UserRoleId
             Where r.[Name] = 'Студент' 
                Go  
