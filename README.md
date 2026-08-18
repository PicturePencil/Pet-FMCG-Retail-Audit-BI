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

### Additional Analysys
 Questionary time and efficiency, Agents KPI, Outlets performance

## Workflows

### Base Workflow:
[Creating audit base or supplement excisting] 

*Finding outlets -> Register outlet -> Making base audit -> Verification process -> planning next audit*

**Finding outlets** - agents will be searching for new outlets to form base of FMCG information sources. A process includes finding outlets in selected regions, negotiations with store owners, contract signing and registration preparation.

**Register outlet** - found and contracted outlet is sent to register. Agent request verification team to register outlet by sending them necessary data and then start baseline audit in agreed time

Workflow: *Outlets -> audit_plan*

**Base audit** - collecting data of SKU stocks and other information. During the process agent can request new SKU/SCD registration.

Workflow: *audit_plan -> audit_data -> raw_data*

**Verification process** - When a base audit visit is finished a task created for QC team. QC team checks collected data and accept, fix, remove or reject it. 

Workflow: 
* Visit finished: *audit_data -> qc_tasks*
* QC task created: *qc_tasks -> qc_tasks_log*
* QC Manager checked record: *raw_data -> qc_verification*
* All records of visit is checked: *qc_tasks -> qc_verification (all checked?) -> qc_tasks -> qc_tasks_log*
* Verification approved/rejected: *qc_tasks -> qc_tasks_log*

**Planning next audit** - assigning dates of new audit by existing information. Creating plans and contributing dates and agents

Workflow: *outlets -> audit_plan*


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
Collected and stored records will be spread to quality control or verification team members. During the process specialists will be checking raw data of visits. if there is some errors in data that can be changed by using picture or audio evidences QC specialist can add fixes to the raw data. If there is data that cannot be prooved through verification process QC can remove data from visit and apply to add it through new visit.
If there is some critical errors in proofs, such as bad pictures, audio and mismatching between audio and pictures, or there are some cruitial violations of audit rules - QC can excalate the whole visit to rejection process and QC Lead must reject visit if it is confirmed
The visit can be considered as fully verified if all of it records were checked and accepted/fixed except some SKU`s records which could be removed by QC team as exception. No escalated rejection or rejection included! 

## DB structure
### Databases names and specifications
 #### Outlets & Questionary tables
 * outlets
 * audit_plan
 * audit_data
 * raw_data
 * binary_links
 
 #### Verification
 * qc_tasks
 * qc_tasks_log
 * qc_verification
 * qc_evidence_items

 #### Teams
 * departments
 * roles
 * users
 * team_roaster

 #### Dictionaries
 * d_sku_info
 * d_sdc_info
 * d_cycles
 * t_cities
 * t_outlet_types
 * t_outlet_activity
 * t_audit_status
 * t_verification_status
 * t_task_status
 * t_assign_type
 * t_check_plan_type
 * t_file_type
 * t_gender
 * t_movements
 

 #### Calculatable tables (Materialized View)
 * sales_base
 * master_sales
 * business_report

#### Analyst (functional) tables
 * accepted_for_report

### Tables
#### outlets
Table with outlets registered for participating in FMCG retail-audit project. 
| Column names    | data type | comment            |
|-----------------|-----------|--------------------|
|outlet_code (PK) | SERIAL    | 
|registered date  | DATE      |
|base_cycle (FK)  | INTEGER   | d_cycles.id
|city             | t_cities  | 
|location         | GEOGRAPHY |
|adress           | VARCHAR(255)|         
|owner_name       | VARCHAR(255)|      
|type             | t_outlet_types|
|outlet_status    | t_outlet_activity |  active/inactive
|comments         | VARCHAR(255)|

*If Outlet status marked as 0, but after some months started working with us It is necessary to register it as new
*We marking outlet status as 0 only when it is impossible to proceed current or future cycles audit or can`t rely on this outlet data  
*Base cycle is used to show after which cycle audit data will be used for FMCG analysis

#### audit_plan
An upcoming audit tasks assigned by system or manager. 

| Column names  | data type |  comment            |
|---------------|-----------|---------------------|
|id (PK)        | SERIAL    |
|outlet(FK)     | BIGINT    | outlets.outlet_code
|cycle (FK)     | INTEGER   | d_cycles.id
|start_time_plan| TIMESTAMPTZ  |
|end_time_plan  | TIMESTAMPTZ  |
|visits_plan    | SMALLINT  | count of Visits planned
|assigned_for (FK) | BIGINT | Assigned agent for questionary (users.id)
|assigned_by (FK)| BIGINT   | Assigned by user (users.id)
|assigned_time  | TIMESTAMPTZ  |
|assign_type    |t_assign_type|New assigned/Re-assigned/Baseline Audit
|audit_status   |t_audit_status | Pending/In Progress/On Hold/Finished/Failed

Audit plan record status won't be finished until its last visit status wouldn`t be marked as completed.
visits_plan is an orient value of visits and not constraint. It is used only as forecast
Baseline audit created only when new outlet registered in system. If outlet registered when agent arrived at new outlet audit plan is created automatically with forecasted time. If outlet added by manager he must add parameters of planned time and visits count. 

#### audit_data
A list of visits in planned outlet. 
1 row - 1 visit in 1 outlet.
| Column names | data type |  comment            |
|--------------|-----------|---------------------|
|id (PK)       | SERIAL    |
|assign_id (FK)| BIGINT    | audit_plan.id
|outlet(FK)    | BIGINT    | outlets.outlet_code
|visit_sequence| INTEGER   | 
|start_time    | TIMESTAMPTZ  |
|end_time      | TIMESTAMPTZ  |
|status        | BOOLEAN   | 0 - incomplete, 1 - complete
|next_audit_needed| BOOLEAN| 0 - no (always if status = 1), 1 - yes,
|next_cycle_audit| BOOLEAN | 0 - no (we stop working with outlet), 1 - yes
|agent_comment |VARCHAR(255)|


When a visit connected to the audit_plan.id marked as complete it`s audit plan status will be selected as Finished and this visit will be the last in cycle.

#### raw_data
Information about all SKU positions in an outlet taken from visits. 
1 row - 1 SKU & 1 SDC unique combination each cycle visit

| Column names  | data type | comment            |
|---------------|-----------|--------------------|
|id  (PK)       | SERIAL    |
|audit_id (FK)  | BIGINT    | audit_data.id
|sku_code (FK)  | BIGINT    | d_sku_info.id
|sdc_code (FK)  | BIGINT    | d_sdc_info.id    
|price          | INTEGER   |
|shelf_stock    | INTEGER   | units only, not volume
|warehouse_stock| INTEGER   | units only, not volume
|purchase       | INTEGER   | units of SKU distributed to the store 
|facing         | INTEGER   |
|planned_distribution| INTEGER |units planned to distribute until next audit
|is_distributed | BOOLEAN   |
|will_distribute| BOOLEAN   |
|comment        | VARCHAR(255)|

If there is an SKU with unregistered SDC or new unregistered SKU agent must request SKU registration from QC-team.  

#### binary_links
Links on binary (files) used as evidenses of audit performance

| Column names  | data type  | comment            |
|---------------|------------|--------------------|
|id  (PK)       | SERIAL     |           |
|binary_type    | t_file_type|
|binary_link    | TEXT       |
|added_time     | TIMESTAMPTZ|


#### qc_tasks 
Each row - task to check 1 audit
As the new vizit uploaded on server a record created with automate deadline. 
A manager can change planned date

| Column names   | data type | comment            |
|----------------|-----------|--------------------|
|id  (PK)        | SERIAL    |                    |
|audit_id (FK)   | BIGINT    | audit_data.id
|created_time    | TIMESTAMPTZ |
|start_time      | TIMESTAMPTZ | planned date and time
|end_time        | TIMESTAMPTZ | planned date and time
|assigned_by (FK)| BIGINT    | users.id
|task_status     | t_task_status | Pending/In progress/On Hold/Escalated reject/Finished/Rejected/Approved

Finished status - all raw data was checked and approved (or fixed) without escalation to reject whole visit
Only Approved by QC Team Lead visits will be taken to the FMCG analysis
Task cannot get status accepted until all raw data of checked visit did not get accepted or fixed by QC specialist



#### qc_tasks_log 
Stores each changes of qc_tasks records or its creation such as planned time and status
| Column names   | data type |   comment            |
|----------------|-----------|----------------------|
|id  (PK)        | SERIAL    |       
|task_id (FK)    | BIGINT    | qc_tasks.id
|modified_by (FK)| BIGINT    | users.id
|modified_time   | TIMESTAMPTZ| date & time of modifiing qc_tasks
|start_time_new  | TIMESTAMPTZ|
|end_time_new    | TIMESTAMPTZ|
|task_status_new | t_task_status | Pending/In Progress/Paused/Escalated reject/Finished/Rejected/Approved
 
Before rejection visit must be moved to status "Escalated reject" and then checked by QC Lead Manager. If there is possible to fix information by another visit a new visit task created. This status remains untill new data applied. After - goes to accepted or back to "in progress" status. 

#### qc_verification
SKU records verification table. 

| Column names   | data type |  comment            |
|----------------|-----------|---------------------|
|id  (PK)        | SERIAL    |  
|assign_id (FK)  | BIGINT    | qc_tasks.id
|verified_by (FK)| BIGINT    | users.id
|started_time    | TIMESTAMPTZ  |
|applied_time    | TIMESTAMPTZ  |
|pos_id (FK)     | BIGINT    | raw_data.id
|verification_status|t_verification_status|   Accepted/Corrected/Marked to remove/Removed/Escalated to reject
|columns_change  | JSONB     |   {Column: new value, Column: new value}|
|comments        | TEXT      |

QC agent can take a record to check. When he takes raw data from pending to check visit it changes qc_task status
If SKU record has status Escalated to reject this could lead to reject the whole visit. This should trigger escalation in the qc_tasks and qc_tasks_log
If SKU record is on marked to remove status it must be checked by the QC Lead manager and only then lead to the Remove status. Removed status just exclude row from FMCG analysys as insignificant or added as error. 


#### qc_evidence_items
Table for liking between binary evidenses, raw data and audit visits
| Column names   | data type | comment            |
|----------------|-----------|--------------------|
|id  (PK)        | SERIAL    |
|binary_id       | BIGINT    | binary_links.id
|assigned_by     | BIGINT    | users.id - agent who uploaded
|assigned_date   | TIMESTAMPTZ  |
|raw_id (FK)     | BIGINT    | raw_data.id 
|audit_id (FK)   | BIGINT    | audit.id 

raw_id - for raw data evidence. NULL if evidnce for outlet and audit evidence (audit _id is not NULL)
audit_id - for outlet audit evidence (audio, pictures of store). NULL if raw_id is filled 

#### departments
| Column names    | data type | comment            |
|-----------------|-----------|--------------------|
|id  (PK)         | SERIAL    |           
|team_name        | VARCHAR(20)|
|registration_date| TIMESTAMPTZ|
|registered_by    | BIGINT    |
|is_active        | BOOLEAN   |


#### roles

Needed for individual access to reports or instruments

| Column names    | data type | comment            |
|-----------------|-----------|--------------------|
| role_id (PK)    | SERIAL    |
| role_name       | VARCHAR(20)| Agent / Supervisor / QC Specialist / QC Lead / QC Manager / Project owner / Analyst / ... etc
| created_time    | TIMESTAMPTZ  |
| valid_to_date   | TIMESTAMPTZ  |
| is_active       | BOOLEAN   |
| created_by      | BIGINT    | users.id    

#### Users
| Column names    | data type | comment            |
|-----------------|-----------|--------------------|
| id              | SERIAL    |
| first_name      | VARCHAR(30)|
| second_name     | VARCHAR(30)|
| regster_date    | TIMESTAMPTZ  |
| created_by      | BIGINT    | Users.id
| gender          | t_gender  |


#### team_roaster
| Column names    | data type | comment            |
|-----------------|-----------|--------------------|
| id (PK)         | SERIAL    |
| log_date        | TIMESTAMPTZ  |
| agent (FK)      | BIGINT    | users.id
| movement_type   | t_movements| HIRING / LEAVING / TRANSITION
| department (FK) | BIGINT    | departments.id
| agent_role (FK) | BIGINT    | roles.id
| valid_from      | DATE      |
| valid_to        | DATE      |           
| is_current      | BOOLEAN   |
| assigned_by(FK) | BIGINT    |           | users.id
| comment         | TEXT      |


 #### Dictionaries
 #### d_sku_info
SKU passport. Contains information that doesn`t change in long terms
| Column names    | data type | comment            |
|-----------------|-----------|--------------------|
| sku_id (PK)     | SERIAL    |
| created_time    | TIMESTAMPTZ  | 
| added_by        | BIGINT    | users.id
| is_active       | BOOLEAN   |
| short_name      | VARCHAR(30)|
| full_name       | VARCHAR(90)|
| business        | TYPE      | t_business_type (Coffee, Confectionery, Infant Nutrition)
| brand           | TYPE      | t_brands
| company         | TYPE      | t_companies


#### d_sdc_info
Contains information about SKU`s slow dimensional changes
| Column names    | data type | comment            |
|-----------------|-----------|--------------------|
| id (PK)         | SERIAL    |
| sku_id (FK)     | BIGINT    |
| created_time    | TIMESTAMPTZ |
| valid_from_cycle| INTEGER   |
| valid_to_cycle  | INTEGER   |
| product_line    | TYPE      | t_product_lines
| bus_category    | TYPE      | t_bus_categ (depends on business)
| sub_category    | TYPE      | t_sub_categories
| pack_volume     | SMALLINT  | weight in grams 
| package         | TYPE      | t_packages (can, dough pack, etc)
| pack_category   | TYPE      | t_pack_category (mini/midi/maxi)
| price_segment   | TYPE      | t_price_categories (economy/mainstream/)
| age_segment     | TYPE      | t_age_segments
| sub_segment_1   | TYPE      | t_other_segments
| sub_segment_2   | TYPE      | t_other_segments


#### d_cycles
Contains information about cycles
| Column names    | data type  | comment            |
|-----------------|------------|--------------------|
|id (PK)          | SERIAL     | 
|cycle_name       | VARCHAR(14)| example: 2026, 02 - Feb. Usually the month when data is collected (Feb, Mar, May etc.)
|starts_from      | DATE       |
|ends_in          | DATE       |

Each cycle created automatically after finalizing last cycle
Each cycle - 2 months
Cycle starts from 1-st day of odd and last day of even month

## Report Indicators
All relative indicators containing denominators will be based on business/category/subcategory.

## Indicators & Formulas
 Unit Sales