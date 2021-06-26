CREATE TYPE exchanges_varray AS VARRAY(3)
OF VARCHAR(40)
/

CREATE TYPE stock_type AS OBJECT (
company VARCHAR(20),
currentPrice NUMBER(6,2),
exchanges exchanges_varray,
lastDivident NUMBER(4,2),
eps NUMBER(4,2)
)
/

CREATE TYPE address_type AS OBJECT (
streetNo CHAR(10),
streetName CHAR(15),
subrub CHAR(20),
state CHAR(15),
pin CHAR(10)
)
/

CREATE TYPE investment_type AS OBJECT (
company REF stock_type,
purchasePrice NUMBER(6,2),
purchaseDate DATE,
quantity NUMBER(6)
)
/

CREATE TYPE investment_nestedtbl_type AS TABLE OF investment_type
/

CREATE TYPE client_type AS OBJECT (
name VARCHAR(40),
address address_type,
investments investment_nestedtbl_type
)
/

CREATE TABLE stocks of stock_type(
CONSTRAINT stock_pk PRIMARY KEY(company)
)
/

CREATE TABLE clients of client_type(
CONSTRAINT clients_pk PRIMARY KEY(name)
)
NESTED TABLE investments STORE AS investment_tab
/

ALTER TABLE investment_tab
ADD SCOPE FOR (company) IS stocks
/



//Insert Data into Tables

INSERT INTO stocks VALUES(stock_type('BHP',10.50,exchanges_varray('Sydney','New York'),1.50,3.20));

INSERT INTO stocks VALUES(stock_type('IBM',70.00,exchanges_varray('New York','London','Tokyo'),4.25,10.00));

INSERT INTO stocks VALUES(stock_type('INTEL',76.50,exchanges_varray('New York','London'),5.00,12.40));

INSERT INTO stocks VALUES(stock_type('FORD',40.00,exchanges_varray('New York'),2.00,8.50));

INSERT INTO stocks VALUES(stock_type('GM',60.00,exchanges_varray('New York'),2.50,9.20));

INSERT INTO stocks VALUES(stock_type('INFOSYS',45.00,exchanges_varray('New York'),3.00,7.80));


INSERT INTO clients VALUES(client_type('John Smith',address_type('3','East Av','Bentley','WA','6102'),investment_nestedtbl_type(investment_type((SELECT REF(s) FROM Stocks s WHERE s.company = 'BHP'),12.00,'02-OCT-2001',1000),investment_type((SELECT REF(s) FROM Stocks s WHERE s.company = 'BHP'),10.50,'08-JUN-2002',2000),investment_type((SELECT REF(s) FROM Stocks s WHERE s.company = 'IBM'),58.00,'12-FEB-2000',500),investment_type((SELECT REF(s) FROM Stocks s WHERE s.company = 'IBM'),65.00,'10-APR-2001',1200),investment_type((SELECT REF(s) FROM Stocks s WHERE s.company = 'INFOSYS'),64.00,'11-AUG-2001',1000))));

INSERT INTO clients VALUES(client_type('Jill Brody',address_type('42','Bent St','Perth','WA','6001'),investment_nestedtbl_type(investment_type((SELECT REF(s) FROM Stocks s WHERE s.company = 'INTEL'),35.00,'30-JAN-2000',300),investment_type((SELECT REF(s) FROM Stocks s WHERE s.company = 'INTEL'),54.00,'30-JAN-2001',400),investment_type((SELECT REF(s) FROM Stocks s WHERE s.company = 'INTEL'),60.00,'02-OCT-2001',200),investment_type((SELECT REF(s) FROM Stocks s WHERE s.company = 'FORD'),40.00,'05-OCT-1999',300),investment_type((SELECT REF(s) FROM Stocks s WHERE s.company = 'GM'),55.50,'12-DEC-2000',500))));


Answers

(a)
SELECT c.name, i.company.company, i.company.currentPrice, i.company.lastDivident, i.company.eps
FROM clients c, TABLE(c.investments) i

(b)
SELECT c.name, i.company.company, SUM(i.quantity), (SUM(i.purchasePrice * i.quantity)/SUM(i.quantity))
FROM clients c, TABLE(c.investments) i
GROUP BY c.name, i.company.company


(c)
SELECT DISTINCT i.company.company, c.name, i.quantity, (i.company.currentPrice * i.quantity)
FROM clients c, TABLE(c.investments) i, TABLE(i.company.exchanges) e
WHERE e.COLUMN_VALUE = 'New York'

(d)
SELECT c.name, SUM(i.purchasePrice * i.quantity)
FROM clients c, TABLE(c.investments) i
GROUP BY c.name

(e)
SELECT c.name, (SUM(i.purchasePrice * i.quantity) - SUM(i.company.currentPrice * i.quantity))
from clients c, TABLE(c.investments) i
GROUP BY c.name


4.
DELETE TABLE(SELECT c.investments FROM Clients c WHERE c.name = 'John Smith') i
WHERE i.company.company = 'INFOSYS'
/

DELETE TABLE(SELECT c.investments FROM Clients c WHERE c.name = 'Jill Brody') i
WHERE i.company.company = 'GM'
/

INSERT INTO TABLE(SELECT c.investments FROM Clients c WHERE c.name = 'John Smith') VALUES((SELECT REF(s) FROM Stocks s WHERE s.company = 'GM'),60.00,'13-SEP-2020',500)
/

INSERT INTO TABLE(SELECT c.investments FROM Clients c WHERE c.name = 'Jill Brody') VALUES((SELECT REF(s) FROM Stocks s WHERE s.company = 'INFOSYS'),76.50,'13-SEP-2020',1000)
/



