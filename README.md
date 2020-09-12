# SQLite
practice!!

수학연산  
query = """
            SELECT 
                state,
                address,
                AVG(price),
                COUNT(price),
                MAX(price),
                MIN(price),
                SUM(price)
            FROM orders
            
            GROUP BY state, address
            HAVING COUNT(price) > 1

"""
### WHERE 는 수학연산자 조건을 같이 사용할 수 없어서
### GROUP BY 와 함께 사용하는 HAVING 에서 사용할 수 있다.
data = pd.read_sql(query, connection)

data

문자열, CAST AS  

query = """
            SELECT 
            CAST(SUBSTR(date, 1 , 4) AS INT)  || '년' AS year,
            CAST(SUBSTR(date, 6 , 2) AS INT)  || '월' AS month,
            CAST(SUBSTR(date, 9 , 2) AS INT)  || '일' AS day,
            price, 
            state
                    FROM orders
    """
### || 이 표시로 문자열을 붙일 수 있다.
data = pd.read_sql(query, connection)

data

기타 문자열 관련

query = """
            SELECT 
            date,
            price, 
            UPPER(state) AS state_upper, #대문자
            LOWER(state) AS state_lower, #소문자
            LENGTH(state) AS state_length #문자길이
                    FROM orders
    """
### || 이 표시로 문자열을 붙일 수 있다.
data = pd.read_sql(query, connection)

data

## 날짜 (년, 월, 일) 모두 가능
query = "SELECT DATE('2018-01-01', '+7 days') AS date" 

data = pd.read_sql(query, connection)

data


## 날짜시간 모두표시  
query = "SELECT DATETIME('2018-01-01 12:31:57') AS date" 

data = pd.read_sql(query, connection)

data

## 시간만 표시  
query = "SELECT TIME('12:31:57') AS date" 

data = pd.read_sql(query, connection)

data

## 현재시간
query = "SELECT TIME('now', '+9 hours') AS date" 

data = pd.read_sql(query, connection)

data
## STRFTIME
query = """SELECT 
                STRFTIME('%m월 %d일', date) AS date, 
                price, 
                state 
            FROM orders """

data = pd.read_sql(query, connection)

data


## CASE WHEN
query = """SELECT   
                state,
                CASE WHEN state = "confirmed" THEN '결제완료'
                    ELSE '결제 취소'
                END AS 결제상태,
                address,
                CASE address
                    WHEN 'Seoul' THEN '서울'
                    WHEN 'Busan' THEN '부산'
                    WHEN 'Incheon' THEN '인천'
                    ELSE '기타'
                END AS 주소

            FROM orders """

data = pd.read_sql(query, connection)

data

## WINDOW FUNCTION


query = """SELECT   
                date,
                state,
                price,
                ROW_NUMBER() OVER (PARTITION BY date, state ORDER BY price DESC) AS price_order
            FROM orders 
            
            
"""
### row_number() : 정렬해서 순서대로 번호를 부여 
### rank() : 정렬해서 순서대로 순위를 부여 (동점자는 같은 값)
### order by 는 정렬 partition by 는 group by와 비슷
data = pd.read_sql(query, connection)

data

### FIRST_VALUE()

query = """ SELECT user_id, FIRST_VALUE(STRFTIME("%Y-%m-01", date)) 
                                OVER(PARTITION BY user_id ORDER BY date) first_month,
                           STRFTIME("%Y-%m-01", date) month
                        FROM kt
"""

pd.read_sql(query, connection)

### 이 예시에서  FIRST_VALUE(STRFTIME("%Y-%m-01", date)) OVER(PARTITION BY user_id ORDER BY date) first_month는 
### user_id 별로 date순서에 따라 STRFTIME("%Y-%m-01", date)의 첫번째 값만 모두 출력하는 컬럼 생성


## subquery

query = """SELECT   
             *
          FROM orders 
           WHERE orders.product_id IN (
                                       SELECT 
                                           id
                                        FROM products
                                        WHERE price >= 500
                                        )           
          
"""

data = pd.read_sql(query, connection)

data
