## Bus Reservation
> A bus reservation database system contains the following tables
> - Bus( Bus_no(PK), arrival, departure, seats)
> - Reservation(Bus_no(FK), PassengersName, age, date of travel)

> create the table and insert values

> - write a procedure to display the details when the bus no is specified.
> - write procedure to display the details of passengers travelling in a bus on a particular date.
> - use a trigger to ensure that the reservation procedure will be successfull only if seats are available.

## Solution
1. create tables
```sql
create table bus (
    busno int primary key,
    arrival char(8),
    departure char(8),
    seats int
);
create table reservation(
    busno int references bus(busno),
    pname varchar(20) primary key,
    age int,
    dot Date
);
```
2. Insert values
```sql
insert into bus values (1, '05:00:00','05:10:00',20);
insert into bus values (2, '03:00:00','03:05:00',0);

insert into reservation values (1, 'rohit', 20, '13-DEC-2023');
insert into reservation values (2, 'sam', 25, '12-JAN-2024');
insert into reservation values (1, 'ram', 30, '13-DEC-2023');
```
### PL-SQL procedure to find details of bus using Busno
---

3. Create a new file usinf `edit findbusproc.sql`
```sql
create or replace procedure findbus(id in bus.busno%type )
as
v_details bus%ROWTYPE;
begin
    select * into v_details from bus where busno = id;
    dbms_output.put_line('bus details are: ');
    dbms_output.put_line(RPAD('Busno',15)||': '||v_details.busno);
    dbms_output.put_line(RPAD('Arrival',15)||': '||v_details.arrival);
    dbms_output.put_line(RPAD('Departure',15)||': '||v_details.departure);
    dbms_output.put_line(RPAD('seats',15)||': '||v_details.seats);
	
end;
/
```
4. call the procedure using `call findbus(busno)` or

5. create a new file using `edit findbusexec.sql`
```sql
declare
 id bus.busno%type := &id;
begin
    findbus(id);
end;
/
```
6. execute using `@findbusexec.sql`
---
### PL-SQL procedure to display details or travellers on a particular date
7. Create a new file using `edit findresproc.sql`
```sql
create or replace procedure findbydate( d in reservation.dot%type )
as
v_row reservation%ROWTYPE;
begin       
    dbms_output.put_line(RPAD('BUSNO',10)||RPAD('PASSENGER_NAME',20)||RPAD('AGE',6)||RPAD('DATE_OF_TRAVEL',20));
    for v_row in (select * from reservation where dot = d) loop
        dbms_output.put_line(RPAD(v_row.busno,10)||RPAD(v_row.pname,20)||RPAD(v_row.age,6)||RPAD(v_row.dot,20));
    end loop;
end;
/
```
8. execute using call, ex: `call findbydate('13-DEC-2023');`
---
### Trigger to check if seats are available before Insertion
```sql
delimiter #
create or replace trigger checkseats1
before insert on reservation
for each row
declare
    a integer;

begin
    select seats into a from bus where busno = :NEW.busno;
    if a < 1 then
       RAISE_APPLICATION_ERROR(-20001, 'Insert operation canceled. No seats available for the specified bus.'); 
    end if;
end #
delimiter ;

```