# Stefan_D_Folio
Stefan's portfolio to supplement public profiles & resume / CV in order to demonstrate a variety of technical projects or exercises.

create or replace view SANDBOX.SANDBOX.SUBSCRIPTION_DATA
(
	OWNER FIELDS
 , A FIELDS
 , C FIELDS
 , SUBSCRIPTION FIELDS
 , P FIELDS
) as 

with CTE_1
as
(
  select distinct OWNER 
  from Table_4
  union
  select distinct OWNER
  from Table_6
)

, CTE_2
as
(
select  tier7.NAME tier7
      , tier6.NAME tier6
      , tier5.NAME tier5
      , tier4.NAME tier4
      , tier3.NAME tier3
      , tier2.NAME tier2
      , tier0.NAME tier0
      , u.NAME NAME
      , u.C_EMAIL EMAIL
      , u.ID Table_1_ID
      , u.E_NUMBER E_NUMBER
      , r.NAME Table_2_ROLE 
  from Data_Warehouse.Schema_1.Table_1 u
  left join Data_Warehouse.Schema_1.Table_2 r on r.ID = u.Table_2ID
  left join Data_Warehouse.Schema_1.Table_2 tier0 on r.PARENTROLEID = tier0.ID and ifnull(tier0.NAME,'') <> 'Sales Finance'
  left join Data_Warehouse.Schema_1.Table_2 tier2 on tier2.ID = tier0.PARENTROLEID and ifnull(tier2.NAME,'') <> 'Sales Finance'
  left join Data_Warehouse.Schema_1.Table_2 tier3 on tier3.ID = tier2.PARENTROLEID and ifnull(tier3.NAME,'') <> 'Sales Finance'
  left join Data_Warehouse.Schema_1.Table_2 tier4 on tier4.ID = tier3.PARENTROLEID and ifnull(tier4.NAME,'') <> 'Sales Finance'
  left join Data_Warehouse.Schema_1.Table_2 tier5 on tier5.ID = tier4.PARENTROLEID and ifnull(tier5.NAME,'') <> 'Sales Finance'
  left join Data_Warehouse.Schema_1.Table_2 tier6 on tier6.ID = tier5.PARENTROLEID and ifnull(tier6.NAME,'') <> 'Sales Finance'
  left join Data_Warehouse.Schema_1.Table_2 tier7 on tier7.ID = tier6.PARENTROLEID and ifnull(tier7.NAME,'') <> 'Sales Finance'
  where u.C_EMAIL is not null
      and u.Name is not null
      and u.ID in (select * from CTE_1)
      and coalesce(tier7.NAME,tier6.NAME,tier5.NAME,tier4.NAME,tier3.NAME,tier2.NAME,tier0.NAME,r.NAME) = 'Chief...'
          and u.ISACTIVE = 'TRUE'
         // and ifnull(u.ID,'x') not in (select ifnull(MANAGERID,'') from Data_Warehouse.Schema_1.Table_1 where ISACTIVE = 'TRUE')
)

, CTE_3
as
(
  select ifnull(rh.tier7 || ',','') 
        || ifnull(rh.tier6 || ',','') 
        || ifnull(rh.tier5 || ',','')
        || ifnull(rh.tier4 || ',','')
        || ifnull(rh.tier3 || ',','')
        || ifnull(rh.tier2 || ',','')
        || ifnull(rh.tier0 || ',','')
        || ifnull(rh.Table_1_ROLE,'') h_string
        , rh.Table_1_ID
        , rh.Table_1_NAME
  from CTE_2 rh
)

, CTE_4
as
(
  select position(',',h_string) POS_COMMA1
    , ifnull(nullif(position(',',h_string,POS_COMMA1+1),0),LEN(h_string)+1) POS_COMMA2
    , ifnull(nullif(position(',',h_string,POS_COMMA2+1),0),LEN(h_string)+1) POS_COMMA3
    , ifnull(nullif(position(',',h_string,POS_COMMA3+1),0),LEN(h_string)+1) POS_COMMA4
    , ifnull(nullif(position(',',h_string,POS_COMMA4+1),0),LEN(h_string)+1) POS_COMMA5
    , ifnull(nullif(position(',',h_string,POS_COMMA5+1),0),LEN(h_string)+1) POS_COMMA6
    , ifnull(nullif(position(',',h_string,POS_COMMA6+1),0),LEN(h_string)+1) POS_COMMA7
    , substring(h_string,0,POS_COMMA1-1) CISCO_NODE_0__C
    , substring(h_string,POS_COMMA1+1,POS_COMMA2-POS_COMMA1-1) CISCO_NODE_1__C
    , substring(h_string,POS_COMMA2+1,POS_COMMA3-POS_COMMA2-1) CISCO_NODE_2__C
    , substring(h_string,POS_COMMA3+1,POS_COMMA4-POS_COMMA3-1) CISCO_NODE_3__C
    , substring(h_string,POS_COMMA4+1,POS_COMMA5-POS_COMMA4-1) CISCO_NODE_4__C
    , substring(h_string,POS_COMMA5+1,POS_COMMA6-POS_COMMA5-1) CISCO_NODE_5__C
    , substring(h_string,POS_COMMA6+1,POS_COMMA7-POS_COMMA6-1) CISCO_NODE_6__C
    , c.h_string
    , c.Table_1_ID ID
    , c.Table_1_NAME NAME
  from CTE_3 c
)

SELECT
DISTINCT 
	OWNER FIELDS
 , A FIELDS
 , C FIELDS
 , SUBSCRIPTION FIELDS
 , P FIELDS
REPLACE (S.SBQQ__PNAME__C, '_xxxxxxx_', ' ') AS "P",
CASE WHEN P LIKE '%Test%' THEN 'TEST/DEV' WHEN P LIKE '%Service%' AND NOT (P LIKE '%Availability%') THEN '_SERVICE' WHEN P LIKE '%University%' THEN '__EDUCATION' ELSE 'PROD' END AS "ENVIRONMENT",
ROUND(S.MONTHS, 0) AS "Months",
ROUND(S.QUANTITY, 0) AS "QTY",
(1-DIV0NULL((S.C_PRICE * S.QUANTITY), (S.QUANTITY * S.REG_PRICE))) AS "Disc b/f ...",
DATEDIFF (DD, START, END)+1 AS "Term in Days",
DIV0NULL((DIV0NULL(S.NET_PRICE,((DATEDIFF (DD, START, END)+1)))*365), S.QUANTITY) AS "Pr/SKU a/f OTD"
FROM "Data_Warehouse"."Schema_1".Table_8 S
INNER JOIN "Data_Warehouse"."Schema_1".Table_7 C
    ON S.SBQQ__CNUMBER__C=C.CNUMBER
LEFT JOIN "Data_Warehouse_2"."SCHEMA_2"."Table_3" End_Cal
    ON C.ENDDATE = End_Cal.THEDATE
JOIN "Data_Warehouse"."Schema_1".Table_4 A
    ON S.SBQQ__A__C=A.ID
INNER JOIN "Data_Warehouse"."Schema_1".Table_5 PR
    ON S.SBQQ__P__C = PR.ID
LEFT JOIN CTE_4 Table_1_CURRENT 
        ON A.OWNERID = Table_1_CURRENT.ID
LEFT JOIN "Data_Warehouse"."Schema_1".Table_6 RENEWAL_OPP
    ON C.SBQQ__RENEWALOPPORTUNITY__C = RENEWAL_OPP.ID
LEFT JOIN "Data_Warehouse"."Schema_1"."Table_1" Table_1_RENEWAL_REP
        ON RENEWAL_OPP.ISR__C = Table_1_RENEWAL_REP.ID

WHERE C.ENDDATE >= dateadd(month, -2, CURRENT_DATE)
  AND A.NAME NOT ILIKE '%_xxxxxxx_%'
  //and C.CNUMBER = '########'
  ORDER BY C.END, A.NAME, C.CNUMBER, S.NAME, QTY DESC;
