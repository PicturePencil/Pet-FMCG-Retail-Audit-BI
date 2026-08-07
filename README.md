# Pet BI FMСG Retail-Audit Analytics

## Agenda 
### Project aim  
 Develop the BI template for analytics

### What will be analyzed
 Goods turnover using Nielson statistics measurements

### What platforms will be used
 DBMS - PostgreSQL
 BI platform - Power BI Desktop

### Analysing business categories
 Confectionery, Coffee, Infant Nutrition
 Each business have it's own dasboard due to the it's categories differences
 There is also will be a dashboard for cross-business analysys

## Additional Analysys
 Questionary time and efficiency, Agents KPI, Outlets performance

### Sampling Methodology
 The study sample was drawn using a stratified sampling approach across 5 regional centers of Uzbekistan:
 1. Andijan;
 2. Bukhara;
 3. Fergana;
 4. Samarkand;
 5. Tashkent;
 
 The retail sample includes the following store types:
 - Open Markets;
 - Big Groceries;
 - Small Groceries;
 - Minimarkets;
 - Supermarkets;

### Collecting metology
Data collection will be conducted through surveys and audits of retail outlets. Two months prior to the first data collection cycle, each retail outlet will undergo an opening (baseline) audit. During this audit, a formal agreement will be signed with the store for the ongoing provision of stock and purchase data, and baseline (initial) figures on the store's stock levels and purchases will be collected.

## DB structure
### Databases names and specifications
 #### Outlets & Questionary tables
 * Outlets
 * Audit_data
 * Audit_plan
 * Raw_data
 
 #### Dictionaries
 * d_sku_info
 * d_sku_additional
 * d_cities
 * d_outlets_types
 * d_audit_statuses
 

 #### Teams
 * teams
 * roles
 * users
 * agent_income
 * agent_outcome

 #### Calculatable tables (Materialized View)
 * sales_base
 * master_sales
 * business_report

#### Analyst (functional) tables
 * accepted_for_report

### Tables
#### Outlets
| Column names | data type |  etc data | comment            |
|--------------|-----------|-----------|--------------------|
|outlet_code (PK) | varchar(5) | 00102 |3 digits - outlet number, 1 digit - city, 1 - outlet type| 
|registered date| date | 01/02/2026 | 
|city_code (fk) | varchar(1)|1| data stored in cities table|
|location       | GEOGRAPHY |
|adress         | string    |  |  |
|owner_name     | string    |  |  |
|type     (FK)  | varchar(1)|
|status         | BOOLEAN   | 1 | 0* - stopped working. 1 - active.
|comments       | STRING

*If Outlet status marked as 0, but after some months started working with us It is necessary to register it as new
*We marking outlet status as 0 only when it is impossible to proceed current or future cycles audit or can`t rely on this outlet data  


#### Audit_data
| Column names | data type |  etc data | comment            |
|--------------|-----------|-----------|--------------------|
|id (PK)       | BIGINT    |
|assign_id (FK)| BIGINT    |           | connected to Audit_plan
|outlet(FK)    | BIGINT    |           | connected to Outlets
|start_time    | DATETIME  |
|end_time      | DATETIME  |
|status (FK)   | BOOLEAN   | 0         | 0 - incomplete, 1 - complete
|next_audit_needed| BOOLEAN| 0         | 0 - no (always if status = 1), 1 - yes,
|next_cycle_audit| BOOLEAN | 0         | 0 - no (we stop working with outlet), 1 - yes
|agent_comment | STRING    |

#### Audit_plan
| Column names  | data type |  etc data | comment            |
|---------------|-----------|-----------|--------------------|
|id (PK)        | BIGINT    |
|outlet(FK)     | BIGINT    |           | Outlets.id
|start_time_plan| DATETIME  |
|end_time_plan  | DATETIME  |
|visits_plan    |
|assigned_for (FK) | BIGINT |           |Assigned agent for questionary (users.id)
|assigned_by (FK)| BIGINT   |           |Assigned by user (users.id)
|assigned_time  | DATTIME   |

#### Raw_data
| Column names  | data type |  etc data | comment            |
|---------------|-----------|-----------|--------------------|
|id  (PK)       | BIGINT    |           |
|audit_id (FK)  | BIGINT    |           | audit_data.id
|sku_code (FK)  | BIGINT    |           | d_sku_info.id
|


## Report Indicators
All relative indicators containing denominators will be based on business/category/subcategory.

## Indicators & Formulas
 Unit Sales