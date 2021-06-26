CREATE TYPE student_ty AS OBJECT (
sno CHAR(5),
sname VARCHAR2(50),
gpa FLOAT
)
/

CREATE TABLE students_tbl OF student_ty (sno PRIMARY KEY)
/

SELECT * FROM students_tbl;

INSERT INTO students_tbl VALUES (student_ty('s2189','Saman',3.7))
/


//s is alias for students table
SELECT VALUE(s) FROM students_tbl s
/


//Creating relationships between tables

CREATE TYPE course_ty AS OBJECT (
ccode CHAR(5),
cname VARCHAR2(20)
)
/

CREATE TYPE stud_ty AS OBJECT (
sno CHAR(5),
sname VARCHAR2(50),
gpa FLOAT,
course REF course_ty
)
/

CREATE TABLE courses_tbl of course_ty(ccode PRIMARY KEY)
/

CREATE TABLE studs_tbl of stud_ty(sno PRIMARY KEY)
/

INSERT INTO courses_tbl VALUES(course_ty('3060', 'DBS'))
/

INSERT INTO courses_tbl VALUES(course_ty('3061', 'SPM'))
/

INSERT INTO studs_tbl VALUES(stud_ty('S1000', 'Saman', 3.5, (SELECT ref(c) FROM courses_tbl c WHERE c.ccode = '3060') ))
/

INSERT INTO studs_tbl VALUES(stud_ty('S1001', 'Kamal', 3.2, (SELECT ref(c) FROM courses_tbl c WHERE c.ccode = '3061') ))
/

INSERT INTO studs_tbl VALUES(stud_ty('S1002', 'Sunil', 2.5, (SELECT ref(c) FROM courses_tbl c WHERE c.ccode = '3061') ))
/

SELECT s.sno, s.sname, s.course.cname
FROM studs_tbl s
/


SELECT * FROM courses_tbl
/

SELECT * FROM studs_tbl
/