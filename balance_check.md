## Question 3 : Balance Check
> 1. Consider the relations:
     Bank(branch_id, branchname, accountno(PK), balance)
     customer(custid, customername, accountno(FK))
> -	Write an SQL query which returns the name of the customer and bank balance whose account balance is less than 1000 Rs.
> -	Display the custid and name of customers whose name ends with ‘A’
> - Display the minimum balance account numbers.
> -	Altering table customer by adding new column place varchar(20)
> -	Display the total number of customers.

> 2. Write an SQL trigger to carry out the following actions.
On deletion of an account for each owner of the account, Whenever the account balance goes below 1000, the account has to be removed from bank table and add to table low_bal with attributes account_no and balance.

## Solution
1. Create table Bank
```sql
create table bank (
    bid int,
    bname varchar(20),
    accno int primary key,
    balance int
);
```
2. Create table Customer
```sql
create table customer(
    cid int primary key,
    cname varchar(20),
    caccno int,
    foreign key(caccno)  references bank(accno) 
);
```
3. Insert values
```sql
insert into bank values (1, 'sbi', 111, 800);
insert into bank values (2, 'fbi', 222, 3000);
insert into bank values (1, 'fbi', 333, 4000);

insert into customer values (1, 'rohit', 111);
insert into customer values (2, 'sam', 222);
insert into customer values (3, 'alan', 333);
```
4. Query which return name of customer and bank balance whose account balance is less than 1000;
```sql
select c.cname, b.balance from customer c join bank b 
on c.caccno = b.accno where b.balance < 1000;
```
5. Query to display the custid and name of customers whose name ends with ‘A’
```sql
select cid, cname from customer where cname like '%A';
```
6. Display the minimum balance account numbers
> *not sure what question meant, displaying accounts with balance greater than or equal to minimum balance*
```sql
select accno as min_balance_accounts from bank where balance >= 1000;
```
7. alter table customer by adding a column place varchar(20)
```sql
alter table customer add  place varchar(20);
```
8. display total number of customers
```sql
select count(*) as no_of_customers from customer; 
```
9. Create a new table to store accounts with less balance.
```sql
create table low_bal(accno int , balance int);
```
10. Create a new file using `edit balcheck.sql`
11. Create and save trigger to check balance.
```sql
create or replace trigger blchk
after insert or update on bank
for each row
begin
    if :NEW.balance < 1000 then
     insert into low_bal(:NEW.accno, :NEW.balance);
     update table customer set caccno = NULL where caccno = :NEW.accno;
    end if;
end;
/
```