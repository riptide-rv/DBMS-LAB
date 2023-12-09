## Question 1 : Salary Update


> Write a program in PL/SQL to update the salary of a specific employee by  <li>15% if the salary is greater than 20000 </li><li> by 10% if the salary is greater than 10000</li> <li> otherwise by 5%.</li></br>
>

## Solution
1. Create a table employee with id primary key,name and salary. 
```sql
create table employee (id int primary key, name varchar(20), salary int);
```
2. Insert values  into table
```sql
insert into employee values (1, 'rohit', 1000);
insert into employee values (2, 'sam', 30000);
insert into employee values (3, 'rose', 15000);
```
3. Create a new sql file using `edit  salupd.sql` .
4. Write **PL-SQL** to fetch and update rows and save file
```sql
declare
    v_id employee.id%type;
    v_name employee.name%type;
    v_salary employee.salary%type;
    v_new_salary employee.salary%type;
    cursor c1 is select id,name,salary from employee;
begin
    open c1;
    loop
        fetch c1 into v_id,v_name,v_salary;
        exit when c1%NOTFOUND;
        v_new_salary := v_salary;
        if v_salary > 20000 then
            v_new_salary := v_salary * 1.15; -- 15% increase
        elsif v_salary > 10000 then
            v_new_salary := v_salary * 1.10; -- 10% increase
        else
            v_new_salary := v_salary * 1.05; -- 5% increase
        end if;
        update employee set salary = v_new_salary where id = v_id;
    end loop;
    close c1;
end;
/
```
5. save the file and execute using `@salupd.sql`
6. display output using
```sql
select * from employee;
```