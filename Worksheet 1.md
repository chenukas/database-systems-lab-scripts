CREATE TABLE client(
clno CHAR(3),
name VARCHAR2(12),
address VARCHAR2(30),
CONSTRAINT client_pk PRIMARY KEY(clno)
)
/

CREATE TABLE stock(
company CHAR(7),
price NUMBER(6,2),
dividend NUMBER(4,2),
eps NUMBER(4,2),
CONSTRAINT stock_pk PRIMARY KEY(company)
)
/

CREATE TABLE trading(
company CHAR(7),
exchange VARCHAR2(12),
CONSTRAINT trading_pk PRIMARY KEY(company, exchange),
CONSTRAINT trading_fk FOREIGN KEY(company) REFERENCES stock
)
/

CREATE TABLE purchase(
clno CHAR(3),
company CHAR(7),
pdate DATE,
qty NUMBER(6),
price NUMBER (6,2),
CONSTRAINT purchase_pk PRIMARY KEY(clno, company, pdate),
CONSTRAINT purchase_fk1 FOREIGN KEY(clno) REFERENCES client,
CONSTRAINT purchase_fk2 FOREIGN KEY(company) REFERENCES stock
)
/


INSERT INTO client VALUES('100', 'John Smith', '3 East Av, Bentley, WA 6102');
INSERT INTO client VALUES('101', 'Jill Brody', '42 Bent St, Perth, WA 6001')
/

INSERT INTO stock VALUES('BHP', 10.50, 1.50, 3.20);
INSERT INTO stock VALUES('IBM', 70.00, 4.25, 10.00);
INSERT INTO stock VALUES('INTEL', 76.50, 5.00, 12.40);
INSERT INTO stock VALUES('FORD', 40.00, 2.00, 8.50);
INSERT INTO stock VALUES('GM', 60.00, 2.50, 9.20);
INSERT INTO stock VALUES('INFOSYS', 45.00, 3.00, 7.80);
/


INSERT INTO trading VALUES('BHP', 'Sydney');
INSERT INTO trading VALUES('BHP', 'New York');
INSERT INTO trading VALUES('IBM', 'New York');
INSERT INTO trading VALUES('IBM', 'London');
INSERT INTO trading VALUES('IBM', 'Tokyo');
INSERT INTO trading VALUES('INTEL', 'New York');
INSERT INTO trading VALUES('INTEL', 'London');
INSERT INTO trading VALUES('FORD', 'New York');
INSERT INTO trading VALUES('GM', 'New York');
INSERT INTO trading VALUES('INFOSYS', 'New York');

INSERT INTO purchase VALUES('100','BHP','02-OCT-2001', 1000, 12.00);
INSERT INTO purchase VALUES('100','BHP','08-JUN-2002', 2000, 10.50);
INSERT INTO purchase VALUES('100','IBM','12-FEB-2000', 500, 58.00);
INSERT INTO purchase VALUES('100','IBM','10-APR-2001', 1200, 65.00);
INSERT INTO purchase VALUES('100','INFOSYS','11-AUG-2001', 1000, 64.00);

INSERT INTO purchase VALUES('101','INTEL','30-JAN-2000', 300, 35.00);
INSERT INTO purchase VALUES('101','INTEL','30-JAN-2001', 400, 54.00);
INSERT INTO purchase VALUES('101','INTEL','02-OCT-2001', 200, 60.00);
INSERT INTO purchase VALUES('101','FORD','05-OCT-1999', 300, 40.00);
INSERT INTO purchase VALUES('101','GM','12-DEC-2000', 500, 55.50);


SELECT * FROM client

Answers 1

SELECT DISTINCT c.name, p.company, s.price, s.dividend, s.eps
FROM client c, stock s, purchase p
WHERE c.clno = p.clno AND p.company = s.company
/