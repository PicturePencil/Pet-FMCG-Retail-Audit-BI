# Pet BI FMСG Retail-Audit Analytics

## Agenda 
### Project aim  
 Develop the BI template for FMCG analytics

### What will be analyzed
 Stores` turnover using Nielson statistics measurements

### What platforms will be used
 DBMS - PostgreSQL
 BI platform - Power BI Desktop
 Python - for creating applications that imitatr collecting and verification processes

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

### Verification process
Collected and stored records will be spread to quality control or verification team members. The process will contribute new received data to the first free or less workloaded QC specialist  

## DB structure
### Databases names and specifications
 #### Outlets & Questionary tables
 * outlets
 * audit_data
 * audit_plan
 * raw_data
 * binary_links
 
 #### Verification
 * qc_tasks
 * qc_verification
 * qc_evidence_items


 #### Dictionaries
 * d_sku_info
 * d_sku_additional
 * t_cities
 * t_outlet_types
 * t_outlet_activity
 * t_audit_status
 * t_verification_status
 * t_assign_type
 * t_check_plan_type
 * t_fyle_type
 

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
#### outlets
Table with outlets registered for participating in FMCG retail-audit project. 
| Column names    | data type |  etc data | comment            |
|-----------------|-----------|-----------|--------------------|
|outlet_code (PK) | varchar(5)| 00102     |3 digits - outlet number, 1 digit - city, 1 - outlet type| 
|registered date  | DATE      | 
|city             | t_cities  | 
|location         | GEOGRAPHY |
|adress           | string    |         
|owner_name       | string    |       
|type             | t_outlet_types|
|outlet_status    | t_outlet_activity |   | active/inactive
|comments         | STRING

*If Outlet status marked as 0, but after some months started working with us It is necessary to register it as new
*We marking outlet status as 0 only when it is impossible to proceed current or future cycles audit or can`t rely on this outlet data  


#### adit_data
| Column names | data type |  etc data | comment            |
|--------------|-----------|-----------|--------------------|
|id (PK)       | SERIAL    |
|assign_id (FK)| BIGINT    |           | connected to Audit_plan
|outlet(FK)    | BIGINT    |           | connected to Outlets
|start_time    | DATETIME  |
|end_time      | DATETIME  |
|status (FK)   | BOOLEAN   | 0         | 0 - incomplete, 1 - complete
|next_audit_needed| BOOLEAN| 0         | 0 - no (always if status = 1), 1 - yes,
|next_cycle_audit| BOOLEAN | 0         | 0 - no (we stop working with outlet), 1 - yes
|agent_comment | STRING    |


#### audit_plan
| Column names  | data type |  etc data | comment            |
|---------------|-----------|-----------|--------------------|
|id (PK)        | SERIAL    |
|outlet(FK)     | BIGINT    |           | Outlets.id
|start_time_plan| DATETIME  |
|end_time_plan  | DATETIME  |
|visits_plan    |
|assigned_for (FK) | BIGINT |           |Assigned agent for questionary (users.id)
|assigned_by (FK)| BIGINT   |           |Assigned by user (users.id)
|assigned_time  | DATTIME   |
|assign_type    |t_assign_type|         |New assigned/Re-assigned


#### raw_data
| Column names  | data type |  etc data | comment            |
|---------------|-----------|-----------|--------------------|
|id  (PK)       | SERIAL    |           |
|audit_id (FK)  | BIGINT    |           | audit_data.id
|sku_code (FK)  | BIGINT    |           | d_sku_info.id
|price          | INTEGER   |
|shelf_stock    | INTEGER   |           |units only, not volume
|warehouse_stock| INTEGER   |           |units only, not volume
|purchase       | INTEGER   |           |units of SKU distributed to the store 
|facing         | INTEGER   |
|planned_distribution| INTEGER |        |units planned to distribute until next audit


#### binary_links
| Column names  | data type  |  etc data | comment            |
|---------------|------------|-----------|--------------------|
|id  (PK)       | SERIAL     |           |
|binary_type    | t_fyle_type|
|audit_id  (FK) | BIGINT     |           |audit_data.id   NOT NULL
|raw_id   (FK)  | BIGINT     |           |raw_data.id


#### qc_tasks 
| Column names   | data type |  etc data | comment            |
|----------------|-----------|-----------|--------------------|
|id  (PK)        | SERIAL    |           |
|assigned_by (FK)| BIGINT    |           |
|assigned_to (FK)| BIGINT    |           |
|raw_id (FK)     | BIGINT    |           | Raw_data.id
|start_time      | DATETIME  |
|end_time        | DATETIME  |
|assigned_by (FK)| BIGINT    |
|plan_status     | t_check_plan_type |   |Raw verification/Reassigned verification/Rechecking raw data/Reassigned rechecking
|recheck_id      | BIGINT    |           |qc_tasks.id, only for re-checkin. In other cases -  NULL

#### qc_verification
| Column names   | data type |  etc data | comment            |
|----------------|-----------|-----------|--------------------|
|id  (PK)        | SERIAL    |           |
|assign_id (FK)  | BIGINT    |           |Verification_plan.id
|verified_by     | BIGINT    |
|started_time    | DATETYPE  |
|applied_time    | DATETIME  |
|pos_id (FK)     | BIGINT    |          |Raw_data.id
|verification_status|t_verification_status|   |Accepted/Corrected/Rejected
|columns_change  | JSONB     |   {Column: new value, Column: new value}|
|comments        | TEXT      |

### qc_evidence_items
| Column names   | data type |  etc data | comment            |
|----------------|-----------|-----------|--------------------|
|id  (PK)        | SERIAL    |           |
|binary_id       | BIGINT    |           |binary_links.id
|verification_id | BIGINT    |           |qc_verification

## Report Indicators
All relative indicators containing denominators will be based on business/category/subcategory.

## Indicators & Formulas
 Unit Sales