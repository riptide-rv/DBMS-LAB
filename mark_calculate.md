## Question 2: Mark Calculate
> Stud_session (regno, s_name, ass1, asss2, mark1, mark2, atten%, univ_marks(out of 100), Total marks(out of 150 and initially 0))
> -	Marks for attendance (>90 then 10, >80 then 9, >75 then 8 others 7). Attendance % should not be less than 65.
> -	Mark 1 and mark 2 will be in 12.5. Sum of marks will be out of 25.
> -	Assignment 1 and Assignment 2 will be in 15. Average of 2 assignments will be taken.

>Write a PL/SQL procedure to calculate total mark of all students for subject DBMS.
## Solution 
1. Create table
```sql
create table Stud_Session (
    regno int primary key,
    ass1 int check (ass1 <= 15),
    ass2 int check (ass2 <= 15),
    mark1 int check (mark1 <= 12.5),
    mark2 int check (mark2 <= 12.5),
    attend int,
    umark int,
    tmark int default 0
);
```
2. Insert values
```sql
insert into Stud_Session(regno, ass1, ass2, mark1, mark2, attend, umark) values (1, 10, 10, 10, 10, 95, 100);
insert into Stud_Session(regno, ass1, ass2, mark1, mark2, attend, umark) values (2, 5, 10, 10, 10, 85, 90);
insert into Stud_Session(regno, ass1, ass2, mark1, mark2, attend, umark) values (3, 10, 10, 5, 10, 79, 80);
insert into Stud_Session(regno, ass1, ass2, mark1, mark2, attend, umark) values (4, 10, 5, 10, 10, 70, 70);
insert into Stud_Session(regno, ass1, ass2, mark1, mark2, attend, umark) values (5, 10, 5, 10, 10, 60, 60);
```
3. Create a new file usind `edit markcal.sql` 
> - *NOTE:  Name of procedure and filename should be **Different**.*
4. Write a **PL-SQL PROCEDURE** to calculate total mark;
```sql
create or replace procedure caltmark
is
v_regno Stud_Session.regno%type;
v_ass1 Stud_Session.ass1%type;
v_ass2 Stud_Session.ass2%type;
v_mark1 Stud_Session.mark1%type;
v_mark2 Stud_Session.mark2%type;
v_attend Stud_Session.attend%type;
v_umark Stud_Session.umark%type;
v_tmark Stud_Session.tmark%type;
cursor c1 is select regno, ass1, ass2, mark1, mark2, attend, umark from Stud_Session;
begin
    open c1;
    loop
        fetch c1 into 
        v_regno, v_ass1, v_ass2, v_mark1, 
        v_mark2, v_attend, v_umark;
        exit when c1%NOTFOUND;
        v_tmark := v_umark + ((v_ass1+v_ass2)/2) + v_mark1 + v_mark2;
        if v_attend > 90 then
            v_tmark := v_tmark + 10;
        elsif v_attend > 80 then
            v_tmark := v_tmark + 9;
        elsif v_attend > 75 then
            v_tmark := v_tmark + 8;
        elsif v_attend > 65 then
            v_tmark := v_tmark + 7;
        else 
            dbms_output.put_line('student has less than 65 attendace: '||v_regno );
        end if;
        update Stud_Session set tmark = v_tmark where regno = v_regno;
    end loop;
    close c1;
end;
/        
```
5. Save the file and compile procedure using `@markcalc.sql`.
5. Create a new file using markcalcexec.sql
```sql
begin
 caltmark;
end;
/
```
6. Save and execute using `@markcalcexec.sql`
7. View result using
```sql
select * from Stud_Session;
```