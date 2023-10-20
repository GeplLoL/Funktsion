create function fn_ILTVF_GetEmployees()
Returns table
AS
Return(select EmployeeKey,FirstName,Cast(BirthDate as Date) as DB
from DimEmployee);
Select * from fn_ILTVF_GetEmployees()

CREATE function fn_MSTVF_GetEmployees()
Returns @Table Table(id int,name varchar(20),DOB Date)
as
begin
insert into @Table
select EmployeeKey,FirstName,Cast(BirthDate as Date)
from DimEmployee
Return
end
select * from fn_MSTVF_GetEmployees();
UPDATE fn_ILTVF_GetEmployees set FirstName='sam1'Where id=1

Create table [dbo].[DimEmployees]
(
[EmployeeKey] [int] Primary Key,
[FirstName][nvarchar](50) NULL,
[BirthDate][datetime]Null,
[Gender][nvarchar](10) NULL
)

create function fn_GetEmployeeNameByld(@id int)
Returns varchar(20)
as
begin
Return(select FirstName from DimEmployee where EmployeeKey=@id)
End

select dbo.fn_GetEmployeeNameByld(5);
alter function fn_GetEmployeeNameByld(@id int)
Returns nvarchar(20)
with encryption
as
begin
return (select FirstName from DimEmployee where EmployeeKey=@id)
End

alter function fn_GetEmployeeNameByld(@id int)
Returns nvarchar(20)
With SchemaBinding
as
Begin
Return(Select FirstName from dbo.DimEmployee Where EmployeeKey = @id)
End

Create table #PersonDetails(
Id int Primary key,
Name nvarchar(20)
);

insert into #PersonDetails values(1,'Tom');
insert into #PersonDetails values(2,'Rob');
insert into #PersonDetails values(3,'Rom');

select name from tempdb.sys.all_objects
where name like '#PersonDetails%'

Create procedure spCreateLocalTempTable
as
begin
Create table #PersonDetails(Id int Primary key,Name nvarchar(20)
)

insert into #PersonDetails values(1,'Tom')
insert into #PersonDetails values(2,'Rob')
insert into #PersonDetails values(3,'Rom')
select * from #PersonDetails
End
exec spCreateLocalTempTable

Create table ##PersonDetails(
id int primary key,
Name varchar(20)
);
select * from ##PersonDetails

select * from DimEmployee

select * from DimEmployee where BaseRate > 35 and BaseRate < 50

Create index IX_DimEmployee_BaseRate
ON DimEmployee(BaseRate ASC)
 

SELECT * FROM DimEmployee
ORDER BY BaseRate ASC;

exec sys.sp_helpindex @objname = 'DimEmployee'

execute sp_helptext DimEmployee

drop index DimEmployee.firstNameLastName

---------------------------------------------------INDEX

Create Clustered index IX_tblEmployee_name
ON tblEmployee(Name)
--индекс ускоряет поиск


Create Clustered index Gender_Salary
ON tblEmployee(Gender DESC,Salary ASC)


 --в порядке убываниия Gender, в порядке возррастания salary так сортрирует этот тригер


Create Unique NonClustered Index UIX_tblEmployee_FirstName_LastName
On tblEmployee(FirstName, LastName)
ALTER TABLE tblEmployee 
ADD CONSTRAINT UQ_tblEmployee_City 
UNIQUE NONCLUSTERED (City)


--не дублируются first name last name, не может быть 2 или более значения в city


  CREATE UNIQUE INDEX IX_tblEmployee_City
ON tblEmployee(City)
WITH IGNORE_DUP_KEY
Create nonClustered index IX_tblEmployee_salary
on tblEmployee (Salary ASC)


--если будет ошибка дублированного ключа то сервер проигрнорирует ее и не вызовет оишбку, упорядочен по возрастанию

 
select * from tblEmployee where Salary > 4000 and Salary < 8000


--значение в колонке Salary больше 4000 и меньше 8000


delete from tblEmployee where salary = 2500 

 --удаляет всех у кого 2500

UPDATE tblEmployee set salary = 9000 where Salary = 7500

-- обновляет у всех у кого было 7500 на 9000

select * from tblEmployee order by salary

 --сортировка по столбу salary

select * from tblEmployee order by salary Desc

 --сортировка по столбцу salary по убыванию

select salary, COUNT(salary) as Total
from tblEmployee
Group by salary

-- покажет сколько сотрудников имеют каждый из уровней заработной платы


---------------------------------------------------VIEW

-- Показывает данные сотрудников, включая их имя, зарплату, пол и отдел
Create view vWEmployeeByDepartment
as
Select id, name, salary, gender, deptname
from tblEmployee
join tblDepartment
on tblEmployee.DepartmentId = tblDepartment.DeptId

select * from vWEmployeeByDepartment

-- Показывает данные сотрудников в отделе IT, включая имя, зарплату, пол и отдел
Create view vWITDepartment_Employees
as
Select id, name, salary, gender, deptname
from tblEmployee
join tblDepartment
on tblEmployee.DepartmentId = tblDepartment.DeptId
where tblDepartment.DeptName = 'IT'

select * from vWITDepartment_Employees

-- Показывает данные сотрудников, включая их имя, пол и отдел, исключая конфиденциальные данные
Create view vWEmployeesNonConfidentialData
as
Select id, name, gender, deptname
from tblEmployee
join tblDepartment
on tblEmployee.DepartmentId = tblDepartment.DeptId

select * from vWEmployeesNonConfidentialData

-- Показывает количество сотрудников в каждом отделе
create view vWemployeesCountByDepartment
as
Select DeptName, COUNT(ID) as totalEmployees
from tblEmployee
join tblDepartment
on tblEmployee.DepartmentId = tblDepartment.DeptId
Group by DeptName

select * from vWemployeesCountByDepartment


---------------------------------------------------TRIGGER


CREATE TRIGGER tr_DatabaseScopeTrigger
ON DATABASE
FOR CREATE_TABLE,ALTER_TABLE,DROP_TABLE
AS
BEGIN
ROLLBACK
PRINT'You cannot create, alter or drop a table in thee current database'
END
 --тригер который не дает создать изменить или удалить таблицу в этой базе данных
CREATE TRIGGER tr_serverScopeTrigger
ON ALL SERVER 
FOR CREATE_TABLE,ALTER_TABLE,DROP_TABLE
AS
BEGIN
ROLLBACK
PRINT'You cannot create alter or drop a table in any database in the server'
END
 --тригер который не дает изменит удалить или добавить таблицу на сервере
Disable TRIGGER tr_serverScopeTrigger ON ALL SERVER
 
ENABLE TRIGGER tr_serverScopeTrigger ON ALL SERVER --отключить на всем сервере
 
DROP TRIGGER tr_ServerScopeTrigger ON ALL SERVER 
 
Create trigger tr_DatabaseScopeTrigger
ON DATABASE
FOR CREATE_TABLE
AS
BEGIN
Print 'Database scope trigger'
END 
GO
--тригер реагирует на создание таблицы в базе данных
CREATE TRIGGER tr_ServerScopeTrigger
On all server
FOR CREATE_TABLE
AS
BEGIN
Print'Server Scope trigger'
END 
GO
 --тригер реагирует на создание таблицы в сервере

 

EXEC sp_settriggerorder
@triggername='tr_DatabaseScopeTrigger',
@order='none',
@stmttype='CREATE_TABLE',
@namespace='DATABASE'
GO
--запустить тригер который реагирует на создание таблицы и в базе данных
CREATE TRIGGER tr_LogonAuditTriggers
ON all server 
FOR LOGON
AS 
BEGIN


declare @loginName NVARCHAR(100)
SET @loginName = ORIGINAL_LOGIN()
IF (SELECT COUNT(*) FROM sys.dm_exec_sessions
WHERE is_user_process=1
AND original_login_name = @loginName)>3
BEGIN
Print 'Fourth connection of'+@LoginName+'Blocked'
ROLLBACK
END
END
  --триггер для логона запрещающий зайти с 4 раза такому же никнейму

Execute sp_readerrorlog
