1.

(a)
ALTER TYPE stock_type
ADD MEMBER FUNCTION yeild RETURN FLOAT
CASCADE;
/

CREATE OR REPLACE TYPE BODY stock_type AS
MEMBER FUNCTION yeild RETURN FLOAT IS
	BEGIN
		RETURN ((self.lastDivident / self.currentPrice) * 100);
	END;
END;
/

SELECT s.company, s.yeild()
FROM Stocks s

(b)
ALTER TYPE stock_type
ADD MEMBER FUNCTION currentPriceInUSD(rate FLOAT) RETURN FLOAT
CASCADE;
/

CREATE OR REPLACE TYPE BODY stock_type AS
MEMBER FUNCTION yeild RETURN FLOAT IS
	BEGIN
		RETURN ((self.lastDivident / self.currentPrice) * 100);
	END yeild;
MEMBER FUNCTION currentPriceInUSD(rate FLOAT) RETURN FLOAT IS
	BEGIN
		RETURN (self.currentPrice * rate);
	END currentPriceInUSD;
END;
/

SELECT s.company, s.currentPriceInUSD(100)
FROM Stocks s
/

(c)
ALTER TYPE stock_type
ADD MEMBER FUNCTION noOfExchanges RETURN INTEGER
CASCADE;
/

CREATE OR REPLACE TYPE BODY stock_type AS
MEMBER FUNCTION yeild RETURN FLOAT IS
	BEGIN
		RETURN ((self.lastDivident / self.currentPrice) * 100);
	END yeild;
MEMBER FUNCTION currentPriceInUSD(rate FLOAT) RETURN FLOAT IS
	BEGIN
		RETURN (self.currentPrice * rate);
	END currentPriceInUSD;
MEMBER FUNCTION noOfExchanges RETURN INTEGER IS
	total INTEGER;
	BEGIN
		SELECT COUNT(e.COLUMN_VALUE) INTO total
		FROM TABLE(self.exchanges) e;
		RETURN total;
	END noOfExchanges;
END;
/

SELECT s.company, s.noOfExchanges()
FROM Stocks s
/

(d)
ALTER TYPE client_type
ADD MEMBER FUNCTION totalPurchaseValue RETURN FLOAT
CASCADE;
/

CREATE OR REPLACE TYPE BODY client_type AS
MEMBER FUNCTION totalPurchaseValue RETURN FLOAT IS
	purchaseVal FLOAT;
	BEGIN
		SELECT SUM(i.purchasePrice * i.quantity) INTO purchaseVal
		FROM TABLE(self.investments) i;
		RETURN purchaseVal;
	END;
END;
/

SELECT c.name, c.totalPurchaseValue()
FROM Clients c
/
		
(e)
ALTER TYPE client_type
ADD MEMBER FUNCTION totalProfit RETURN FLOAT
CASCADE;
/

CREATE OR REPLACE TYPE BODY client_type AS
MEMBER FUNCTION totalPurchaseValue RETURN FLOAT IS
	purchaseVal FLOAT;
	BEGIN
		SELECT SUM(i.purchasePrice * i.quantity) INTO purchaseVal
		FROM TABLE(self.investments) i;
		RETURN purchaseVal;
	END totalPurchaseValue;
MEMBER FUNCTION totalProfit RETURN FLOAT IS
	totProfit FLOAT;
	BEGIN
		SELECT SUM((i.company.currentPrice-i.purchasePrice) * i.quantity) INTO totProfit
		FROM TABLE(self.investments) i;
		RETURN totProfit;
	END totalProfit;
END;
		
SELECT c.name, c.totalProfit()
FROM Clients c
/


2.

(a)
SELECT s.company, e.COLUMN_VALUE, s.yeild(), s.currentPriceInUSD(0.74)
FROM Stocks s, TABLE(s.exchanges) e

(b)
SELECT s.company, s.currentPrice, s.noOfExchanges()
FROM Stocks s
WHERE s.noOfExchanges() > 1

(c)
SELECT DISTINCT c.name, i.company.company, i.company.yeild(), i.company.currentPrice, i.company.eps
FROM Clients c, TABLE(c.investments) i

(d)
SELECT c.name, c.totalPurchaseValue()
FROM Clients c

(e)
SELECT c.name, c.totalProfit()
FROM Clients c



