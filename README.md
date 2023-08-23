# BikeStore_DB

**-- In this project, I am creating a database for a bicycle renting company. It will include tables, LOCATION, MANUFACTURER, EMPLOYEE, BIKE, 
-- MEMBER, RENT_INFO, INVOICE.  We also created triggers that will implement two business rule**
**--1. It will show an error 2002 whenever user tries to rent more than two bikes.**
**--2. It will show an error 2004 whenever user has unpaid balance above $500.**


**-- Creating Tables for the entities.**

CREATE TABLE LOCATION (
  Location_Num Varchar2(10) CONSTRAINT location_pk Primary Key,
  Address Varchar2(40),
  Zip Char(5),
  Loc_phn_Number Varchar(10)
);

**--Inserting values into Location Table**

Insert INTO LOCATION VALUES
('101', 'Minneapolis', '56307', '3201234567');
Insert INTO LOCATION VALUES
('102', 'Minneapolis', '56307', '3201234568');
Insert INTO LOCATION VALUES
('103', 'Wisconsinne', '56301', '3201234569');
Insert INTO LOCATION VALUES
('104', 'South Dakota', '56305', '3201234560');






**--Creating Manufacturer table**

CREATE TABLE MANUFACTURER (
  Manu_ID Varchar2(10)  CONSTRAINT Manu_ID_pk Primary Key,
  Manu_Name Varchar2(25),
  Manu_Location Varchar(25),
  Manu_Category Varchar2(25)
);
--Inserting values into Manufacturer table
Insert INTO MANUFACTURER VALUES
('501', 'Toyota', 'Japan', 'Mountain');
Insert INTO MANUFACTURER VALUES
('502', 'Honda', 'Japan', 'terrain');
Insert INTO MANUFACTURER VALUES
('503', 'Atlas', 'Korea', 'Racing');
Insert INTO MANUFACTURER VALUES
('504', 'Tata', 'India', 'Ladies');


**--Creating employee table**

CREATE TABLE EMPLOYEE (
  Emp_Num Varchar2(10) CONSTRAINT Emp_ID_pk Primary Key,
  Emp_Name Varchar2(25),
  Emp_Position Varchar2(25),
  Emp_Address Varchar2(100),
  Emp_phn Number(10),
  Emp_Salary Number(5),
  Emp_Location Varchar2(10) CONSTRAINT FK_EMPLOYEE_loc REFERENCES LOCATION(Location_Num)
);

**--Inserting values into Employee table**

INSERT INTO EMPLOYEE VALUES
('401', 'Ram', 'Employee', '9th street SE', '3201231324', 1000, '101');
INSERT INTO EMPLOYEE VALUES
('402', 'John', 'Employee', '10th street S', '3201231326', 1000, '102');
INSERT INTO EMPLOYEE VALUES
('403', 'Joe', 'Manager', '8th street NE', '3201231327', 2000, '103');
INSERT INTO EMPLOYEE VALUES
('404', 'Smith', 'Supervisor', '5th street SE', '3201231320', 3000, '104');


**--Creating Bike Tables**

CREATE TABLE BIKE (
  Bike_ID Varchar2(10) CONSTRAINT Bike_ID_pk Primary Key,
  Bike_Desc Varchar2(100),
  Bike_Category Varchar2(25),
  Daily_Fee Number(4),
  Bike_Status Varchar(10) CONSTRAINT Bike_Status_C CHECK (Bike_Status IN ('AVAILABLE','UNAVAILABLE')),
  Bike_Location Varchar2(10) CONSTRAINT Loc_Num_FK REFERENCES Location(Location_Num),
  Bike_Manu_ID Varchar2(10) CONSTRAINT Bk_Manu_FK REFERENCES MANUFACTURER(Manu_ID)  
);

**--Inserting values into bike table**

Insert into Bike VALUES
('901', 'high performance', 'Mountain', 100, 'AVAILABLE', '101', '501');
Insert into Bike VALUES
('902', 'many gears', 'terrain', 110, 'AVAILABLE', '102', '502');
Insert into Bike VALUES
('903', 'very fast', 'racing', 100, 'AVAILABLE', '103', '503');
Insert into Bike VALUES
('904', 'smooth', 'ladies', 100, 'AVAILABLE', '101', '504');


**--Creating member table**

CREATE TABLE MEMBER (
  Mem_ID Varchar2(10) CONSTRAINT Mem_ID_pk Primary Key,
  Mem_FN Varchar2(25),
  Mem_LN Varchar2(25),
  Mem_Address Varchar2(100),
  Mem_Phn Varchar2(10),
  Reg_Method Varchar2(25) CONSTRAINT Registration_C CHECK (Reg_Method IN ('ONLINE','OFFLINE')),
  Bikes_Rented Number(2),
  Unpaid_Balance Number(5,2)
);

**--Inserting values into Member table**

INSERT INTO MEMBER VALUES
('1011', 'Ram', 'Tiwari', '16th street', '3201122345', 'ONLINE',  0, 100);
INSERT INTO MEMBER VALUES
('1012', 'Hari', 'gopal', '14th street', '3201122346', 'OFFLINE',  0, 100);
INSERT INTO MEMBER VALUES
('1013', 'Krishna', 'shrestha', '9th street', '3201122347', 'ONLINE',0,  550);
INSERT INTO MEMBER VALUES
('1014', 'john', 'smith', '12th street', '3201122348', 'OFFLINE',  0, 0);



**--Creating Rent_info table** 

CREATE TABLE RENT_INFO (
  Rental_ID Varchar2(10) CONSTRAINT Rent_ID_pk Primary Key,
  Mem_ID Varchar2(10) CONSTRAINT Rent_Mem_FK REFERENCES MEMBER(Mem_ID),
  Rent_Bike_ID Varchar2(10) CONSTRAINT Rent_Bk_FK REFERENCES BIKE(Bike_ID),
  Date_Rented Date,
  Exp_Rtrnh Date,
  Actl_Rtrn_Date Date,
  Loc_Rented Varchar(25) CONSTRAINT Loc_rented_out_FK REFERENCES Location(Location_Num),
  Loc_Returned Varchar(25) CONSTRAINT Loc_returned_in_FK REFERENCES Location(Location_Num));

**--Inserting values into Rent_Info table**

INSERT INTO RENT_INFO VALUES 
('2001', '1011', '901', '12-Nov-2022', '13-Nov-2022', NULL, '101', NULL);
INSERT INTO RENT_INFO VALUES 
('2002', '1011', '902','12-Nov-2022', '13-Nov-2022', NULL, '102', NULL);
  INSERT INTO RENT_INFO VALUES 
('2003', '1012', '903','01-Dec-2022', '02-Dec-2022', '02-Dec-2022', '102', '101');
 INSERT INTO RENT_INFO VALUES 
('2007', '1014', '903','01-Dec-2022', '02-Dec-2022', NULL, '102', NULL);




**--Creating invoice table** 

CREATE TABLE INVOICE (
  Invoice_ID Varchar2(10) CONSTRAINT Inv_ID_pk Primary Key,
  Mem_ID Varchar2(10) CONSTRAINT Inv_Mem_FK REFERENCES MEMBER(Mem_ID),
  Inv_Rental_ID Varchar2(55) CONSTRAINT Inv_Rent_FK REFERENCES RENT_INFO(Rental_ID),
  Rental_Fee Number,
  Late_Fee Number
);

**--Inserting values into invoice table**
INSERT INTO INVOICE VALUES
('201', '1011', '2001', 1,0);
INSERT INTO INVOICE VALUES
('202', '1012', '2002', 2,0);
INSERT INTO INVOICE VALUES
('203', '1013', '2003', 3,0);
INSERT INTO INVOICE VALUES
('204', '1014', '2007', 3,0);




**-- TRIGGER 1
-- BUSINESS RULE 1**

create or replace trigger bike_rule
BEFORE INSERT ON RENT_INFO
For each row
    Declare
    BikeNo binary_integer;
    
    Begin
    Select count(*)
    into BikeNo
    From RENT_INFO
    where Mem_ID = :new.Mem_ID;
    
    if (BikeNo +1) >2 then
    
    Raise_application_error(-20002, 'you cannot rent more than 2 bikes');
    end if;
    end;
    /
    
   

**--Trigger 2--
--Trigger for unpaid balance of more than 500**

create or replace trigger UnpaidBalance
Before insert on Rent_Info
for each row

Declare
Balance_due member.unpaid_balance%type;
Begin
Select unpaid_balance
INTO Balance_due
FROM MEMBER
 WHERE Mem_Id =:new.Mem_Id;
if (Balance_due >=300) THEN
Raise_application_error(-20004, 'You have unpaid balance of 500. Please, pay it before you can rent bikes again');
end if;
end;
/









