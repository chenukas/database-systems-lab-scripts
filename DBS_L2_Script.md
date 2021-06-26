VARRAY - Example 1

CREATE TYPE ename_t AS OBJECT (
fname VARCHAR2(20),
lname VARCHAR2(20)
)
/

CREATE TYPE phone_arr AS VARRAY(5) OF CHAR(10)
/

CREATE TYPE dept_t AS OBJECT (
dno CHAR(5),
dname VARCHAR2(20)
)
/

CREATE TYPE emp_t AS OBJECT(
eno CHAR(5),
ename ename_t,
phoneNo phone_arr,
workdept REF dept_t
)
/

CREATE TABLE department of dept_t(dno PRIMARY KEY)
/

CREATE TABLE employee of emp_t(eno PRIMARY KEY)
/

INSERT INTO department VALUES(dept_t('d001', 'IT'))
/

INSERT INTO department VALUES(dept_t('d002', 'SE'))
/

INSERT INTO employee VALUES(emp_t('E001', ename_t('Saman','Jayaweera'), phone_arr('0710000000','0219999999'), (SELECT REF(d) FROM department d WHERE d.dno='d001')))
/

INSERT INTO employee VALUES(emp_t('E002', ename_t('Namal','Rajapaksa'), phone_arr('0712525252','0227778881'),(SELECT REF(d) FROM department d WHERE d.dno='d002')))
/

SELECT e.eno, e.ename.fname, e.ename.lname, p.COLUMN_VALUE phone, e.workdept.dname
FROM employee e, TABLE(e.phoneNo) p
/




NESTED TABLE - Example 2

CREATE TYPE proj_t AS OBJECT (
projno NUMBER,
projname VARCHAR(15));
/

CREATE TYPE proj_list AS TABLE OF proj_t;
/

CREATE TYPE employee_t AS OBJECT (
eno number,
projects proj_list);
/

CREATE TABLE employees of employee_t(eno PRIMARY KEY)
NESTED TABLE projects STORE AS emp_projects
/

INSERT INTO employees VALUES(1,proj_list(proj_t(1,'A'),proj_t(2,'B'),proj_t(3,'C')))
/

SELECT e.eno, p.projno, p.projname
FROM employees e, TABLE(e.projects) p
/

SELECT *
FROM TABLE(SELECT e.projects FROM employees e WHERE e.eno = 1);
/

INSERT INTO TABLE(SELECT e.projects FROM employees e WHERE e.eno = 1) VALUES (4, 'D');
/

UPDATE TABLE (SELECT e.projects FROM employees e) p SET p.projname = 'F' WHERE p.projno = 2;
/

DELETE TABLE (SELECT e.projects FROM employees e) p WHERE p.projno = 4;
/
