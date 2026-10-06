<div style="
    position: fixed; 
    bottom: 0; 
    left: 0; 
    width: 100%; 
    background-color: #20232a; 
    box-shadow: 0 2px 10px rgba(0,0,0,0.2); 
    z-index: 9999; 
    padding: 10px 20px;
    box-sizing: border-box;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 20px;
">
    <span style="color: #ffffff; font-weight: bold; margin-right: 10px;">Navigation:</span>
    <a href="#Agenda" style="color: #61dafb; text-decoration: none; font-size: 14px; font-weight: 500;">📌 Agenda</a>
    <a href="#Workflows" style="color: #61dafb; text-decoration: none; font-size: 14px; font-weight: 500;">⏳ Workflows</a>
    <a href="#DB_structure" style="color: #61dafb; text-decoration: none; font-size: 14px; font-weight: 500;">🧮 Databases</a>
    <a href="#Calculating_reports" style="color: #61dafb; text-decoration: none; font-size: 14px; font-weight: 500;">🗓 Calculating reports</a>
    <a href="#Formulas" style="color: #61dafb; text-decoration: none; font-size: 14px; font-weight: 500;">⚙️ Measurements</a><a href="ROADMAP.md" style="color: #61dafb; text-decoration: none; font-size: 14px; font-weight: 500;">🛣 Roadmap</a>
</div>

<!-- Отступ, чтобы первый заголовок документа не спрятался ПОД панелью -->
<br><br><br>

<a id="Agenda"></a>

# Pet BI FMСG Retail-Audit Analytics


## Agenda 
### Project aim  
 Develop the BI template for FMCG analytics

### What will be analyzed
 Stores` turnover using Nielson statistics measurements

### What platforms will be used
 DBMS - PostgreSQL
 BI platform - Power BI Desktop
 Python - for creating applications that imitade collecting and verification processes

### Analysing business categories
 Confectionery, Coffee, Infant Nutrition
 Each business have it's own dashboard due to the it's categories differences
 There is also will be a dashboard for cross-business analysis

### Additional analysis
 Questionnaire time and efficiency, Agents KPI, Outlets performance

<a id="Workflows"></a>

## Workflows

### Base Workflow (Collection):
When adding new outlets to base
*Finding outlets -> Register outlet -> Making base audit -> Verification process -> planning next audit*

When collecting data for cycle analysis
*Making visit registered stores by plan -> collecting data during audit -> Verification process -> planning next audit*


**Finding outlets** - agents will be searching for new outlets to form base of FMCG information sources. A process includes finding outlets in selected regions, negotiations with store owners, contract signing and registration preparation. The outlet must distribute all business categories (Confectionery, Infant Nutrition and Coffee)

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
Collected and stored records will be spread to quality control or verification team members. During the process specialists will be checking raw data of visits. if there is some errors in data that can be changed by using picture or audio evidences QC specialist can add fixes to the raw data. 
If there is data that cannot be prooved through verification process QC can start questionable process to get from auditors more proofs or correct information about bad data. For this process auditor should update his questionable visit through looking up store again. After getting new data or better evidences QC specialist can update or approve questionable data 
<a id="escalated"></a>If there is some critical errors in proofs, such as bad pictures, audio and mismatching between audio and pictures, or there are some crucial violations of audit rules - QC can escalate the whole visit to rejection process and QC Lead must reject visit if it is confirmed
The visit can be considered as fully verified if all of it records were checked and accepted/fixed except some SKU`s records which could be removed by QC team as exception. No escalated rejection or rejection included! 

### Analyze process
After finishing collecting and verification data the analyzing process starts.
The analyze process consists of next parts: 
1. outlet picking 
    
    1.1. Analyst checks each outlet sales and handling dynamics, checks for statical outlier, trends, variances and deviations
    
    1.2. Depending of results Analyst and manager come to an agreement for selecting suitable retail outlets for the final analysis maintaining quantitive quotas by outlet types in cities

2. accembling reports

    2.1. Current cycle base sales calculation and adding it in base_sales table.
    The values calculating: current stocks, previous stocks, purchasing, facing, prices, unit sales and distribution parameters (Handling, Out of stock) 
3. updating BI reports
    3.1. Uploading data to power BI
    3.2. Checking the colorcoding, graphics and table visuals. Crosschecking with database information


### Closing the cycle
When cycle is closed all visits, plans, verifications and other data should be locked for update and put to backup copy
To close the cycle all audit plans should be finished, all visits checked by QC teams, all Questionable and Escalated to reject statuses on verification process have to be moved to Approved, Fixed or Rejected statuses.


<a id="DB_structure"></a>

## DB structure
### Databases names and specifications
 #### Outlets & Questionnaire tables
 * [outlets](#outlets_table)
 * [audit_plan](#audit_plan)
 * [audit_data](#audit_data)
 * [raw_data](#)
 * [binary_links](#audit_data)
 
 #### Verification
 * [qc_tasks](#qc_tasks)
 * [qc_tasks_log](#qc_tasks_log)
 * [qc_verification](#qc_verification)
 * [qc_evidence_items](#qc_evidence_items)

 #### Teams
 * [departments](#departments)
 * [roles](#roles)
 * [users](#users)
 * [team_roaster](#team_roaster)

 #### Dictionaries
 * [d_sku_info](#d_sku_info)
 * [d_scd_info](#d_scd_info)
 * [d_companies](#companies)
 * [d_brandnames](#brands)
 * [d_brand_lines](#brandlines)
 * [d_cycles](#d_cycles)
 
 #### ENUM:
 
 * **t_cities** - *Andijan, Bukhara, Fergana, Samarkand, Tashkent, Total.* Total won`t be pickable for outlet reistration! analysis only!  
 * **t_outlet_types** - *Open Markets/Big Grocery/Small Grocery/Minimarket/Supermarket*
 * **t_outlet_activity** - *active/inactive*
 * **t_audit_status** - *Pending/In Progress/On Hold/Finished/Failed/Questionable*
 * **t_verification_status** - *Accepted/Questionable/Corrected/Escalated to reject*
 * **t_task_status** - *Pending/Questionable/In progress/On Hold/Escalated reject/Finished/Rejected/Approved*
 * **t_audit_type** - *Regular audit/Baseline Audit* 
 * **t_file_type** - *Image/Video/Audio*
 * **t_gender** - *Male/Female*
 * **t_movements** - *HIRING / LEAVING / TRANSITION*
 * **t_pick_status** - *Pending/Picked/Not Picked*
 * **t_approve_reason** - *representative/quota_gap_fill/replacement/other_reason*
 * **t_decline_reason** - *non-representative/quota_exceeded/repeated_qc_failure/duplicate_coverage/other reason*
 * **t_business_type** - *Coffee/Confectionery/Infant Nutrition*
 * **t_measure_level** - *SKU/Brand/Company*
 * **t_categories** - categorises for each business. Tablets, Bars, Pure Soluble, Infant Cereal and others
 * **t_packages** - *can, dough pack, glass, carton*
 * **t_pack_category** - *mini/midi/maxi*
 * **t_price_categories** - *Economy/Mainstream/Premium*

#### pre-analysis tables
* [outlet_quotes](#outlet_quotes)
* [outlet_picking](#outlet_picking)

 #### Calculatable tables (Materialized View)
 * [sales_base](#sales_base)
 * [business_report](#business_report)
 * [master_sales](#master_sales)



### Tables
#### outlets <a id="outlets_table"></a>
Table with outlets registered for participating in FMCG retail-audit project. 
| Column names    | data type | comment            |
|-----------------|-----------|--------------------|
|outlet_code (PK) | SERIAL    | 
|registered date  | TIMESTAMPTZ|
|base_cycle (FK)  | INTEGER   | d_cycles.id
|city             | t_cities  | 
|outlet_location  | GEOGRAPHY |
|adress           | VARCHAR(255)|         
|owner_name       | VARCHAR(255)|
|owner_phone1     | VARCHAR(12) | For 1 country only (12 digits for Uzbekistan)      
|owner_phone2     | VARCHAR(12) | For 1 country only (12 digits for Uzbekistan)  
|outlet_type             | t_outlet_types|
|outlet_status    | t_outlet_activity |  active/inactive
|comments         | VARCHAR(255)|

*If Outlet status marked as 0, but after some months started working with us It is necessary to register it as new
*We marking outlet status as 0 only when it is impossible to proceed current or future cycles audit or can`t rely on this outlet data  
*Base cycle is used to show after which cycle audit data will be used for FMCG analysis

#### audit_plan <a id="audit_plan"></a>
An upcoming audit tasks assigned by system or manager. 

| Column names  | data type |  comment            |
|---------------|-----------|---------------------|
|id (PK)        | SERIAL    |
|outlet(FK)     | BIGINT    | outlets.outlet_code
|cycle (FK)     | INTEGER   | d_cycles.id
|start_time_plan| TIMESTAMPTZ  |
|end_time_plan  | TIMESTAMPTZ  |
|visits_plan    | SMALLINT  | count of Visits planned
|assigned_for (FK) | BIGINT | Assigned agent for questionnaire (users.id)
|assigned_by (FK)| BIGINT   | Assigned by user (users.id)
|assigned_time  | TIMESTAMPTZ  |
|assign_type    |t_audit_type|Regular audit/Baseline Audit
|audit_status   |t_audit_status | Pending/In Progress/On Hold/Finished/Questionable/Failed

Audit plan record status won't be finished until its last visit status wouldn`t be marked as completed.
visits_plan is an orient value of visits and not constraint. It is used only as forecast
Baseline audit created only when new outlet registered in system. If outlet registered when agent arrived at new outlet audit plan is created automatically with forecasted time. If outlet added by manager he must add parameters of planned time and visits count. 

#### audit_data <a id="audit_data"></a>
A list of visits in planned outlet. 
1 row - 1 visit in 1 outlet.
| Column names | data type |  comment            |
|--------------|-----------|---------------------|
|id (PK)       | SERIAL    |
|plan_id (FK)| BIGINT    | audit_plan.id
|outlet(FK)    | BIGINT    | outlets.outlet_code
|visit_sequence| INTEGER   | starts from 1 each cycle for each outlet
|start_time    | TIMESTAMPTZ  |
|end_time      | TIMESTAMPTZ  |
|audit_status  | BOOLEAN   | 0 - incomplete, 1 - complete
|next_audit_needed| BOOLEAN| 0 - no (always if status = 1), 1 - yes,
|next_cycle_audit| BOOLEAN | 0 - no (we stop working with outlet), 1 - yes
|agent_comment |VARCHAR(255)|


When a visit connected to the audit_plan.id marked as complete it`s audit plan status will be selected as Finished and this visit will be the last in cycle.

#### raw_data <a id="raw_data"></a>
Information about all SKU positions in an outlet taken from visits. 
1 row - 1 SKU & 1 SCD unique combination each cycle visit

| Column names  | data type | comment            |
|---------------|-----------|--------------------|
|id  (PK)       | SERIAL    |
|audit_id (FK)  | BIGINT    | audit_data.id
|sku_code (FK)  | BIGINT    | d_sku_info.id
|scd_code (FK)  | BIGINT    | d_scd_info.id    
|price          | INTEGER   |
|shelf_stock    | INTEGER   | units only, not volume
|warehouse_stock| INTEGER   | units only, not volume
|purchase*      | INTEGER   | units of SKU distributed to the store 
|facing         | INTEGER   |
|planned_distribution| INTEGER |units planned to distribute until next audit
|is_distributed | BOOLEAN   |
|will_distribute| BOOLEAN   |
|comment        | VARCHAR(255)|

If there is an SKU with unregistered SCD or new unregistered SKU agent must request SKU registration from QC-team and approved by QC manager.
*purchase value is the unit sku nubers purchased and distributed to the store from the last audit and before current visit  

#### binary_links <a id="binary_links"></a>
Links on binary (files) used as evidenses of audit performance

| Column names  | data type  | comment            |
|---------------|------------|--------------------|
|id  (PK)       | SERIAL     |           |
|binary_type    | t_file_type|
|binary_link    | TEXT       |
|added_time     | TIMESTAMPTZ|


#### qc_tasks <a id="qc_tasks"></a>
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
|task_status     | t_task_status | Pending/In progress/On Hold/Questionable/Escalated reject/Finished/Rejected/Approved

Finished status - all raw data was checked and approved (or fixed) without escalation to reject whole visit
Only Approved by QC Team Lead visits will be taken to the FMCG analysis
Task cannot get status Finished/Approved until all raw data of checked visit did not get accepted or fixed by QC specialist


#### qc_tasks_log <a id="qc_tasks_log"></a>
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

#### qc_verification <a id="qc_verification"></a>
SKU records verification table. 

| Column names   | data type |  comment            |
|----------------|-----------|---------------------|
|id  (PK)        | SERIAL    |  
|assign_id (FK)  | BIGINT    | qc_tasks.id
|verified_by (FK)| BIGINT    | users.id
|started_time    | TIMESTAMPTZ  |
|applied_time    | TIMESTAMPTZ  |
|pos_id (FK)     | BIGINT    | raw_data.id
|verification_status|t_verification_status|   Accepted/Questionable/Corrected/Escalated to reject
|columns_change  | JSONB     |   {Column: new value, Column: new value}|
|comments        | TEXT      |

QC agent can take a record to check. When he takes raw data from pending to check visit it changes qc_task status
If SKU record has status Escalated to reject this could lead to reject the whole visit. This should trigger escalation in the qc_tasks and qc_tasks_log
If SKU record is on marked to remove status it must be checked by the QC Lead manager and only then lead to the Remove status. Removed status just exclude row from FMCG analysis as insignificant or added as error. 
Technical details for Questionable: when this status hits an SKU, the parent task status must be marked as questionable. It also changes parent audit_plan status to Questionable and audit_data as incomplete. No updates of date and time. All Questionable moments will be checked throug qc_tasks_log. 
If it is impossible to make acceptable evidences with collecting right data the whole audit process is rejected and we stop working with outlet 

#### qc_evidence_items <a id="qc_evidence_items"></a>
Table for liking between binary evidenses, raw data and audit visits
| Column names   | data type | comment            |
|----------------|-----------|--------------------|
|id  (PK)        | SERIAL    |
|binary_id       | BIGINT    | binary_links.id
|assigned_by     | BIGINT    | users.id - agent who uploaded
|assigned_date   | TIMESTAMPTZ  |
|raw_id (FK)     | BIGINT    | raw_data.id 
|audit_id (FK)   | BIGINT    | audit_data.id 

raw_id - for raw data evidence. NULL if evidence for outlet and audit evidence (audit _id is not NULL)
audit_id - for outlet audit evidence (audio, pictures of store). NULL if raw_id is filled 

#### departments <a id="departments"></a>
| Column names    | data type | comment            |
|-----------------|-----------|--------------------|
|id  (PK)         | SERIAL    |           
|team_name        | VARCHAR(20)|
|registration_date| TIMESTAMPTZ|
|registered_by(FK)| BIGINT    | users.id
|is_active        | BOOLEAN   |


#### roles <a id="roles"></a>

Needed for individual access to reports or instruments

| Column names    | data type | comment            |
|-----------------|-----------|--------------------|
| role_id (PK)    | SERIAL    |
| role_name       | VARCHAR(20)| Agent / Supervisor / QC Specialist / QC Lead / QC Manager / Project manager / Analyst / ... etc
| created_time    | TIMESTAMPTZ  |
| valid_to_date   | TIMESTAMPTZ  |
| is_active       | BOOLEAN   |
| created_by      | BIGINT    | users.id    

#### users <a id="users"></a>
| Column names    | data type | comment            |
|-----------------|-----------|--------------------|
| id              | SERIAL    |
| first_name      | VARCHAR(30)|
| second_name     | VARCHAR(30)|
| register_date    | TIMESTAMPTZ  |
| created_by      | BIGINT    | Users.id
| gender          | t_gender  |


#### team_roaster <a id="team_roaster"></a>
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
 #### d_sku_info <a id="d_sku_info"></a>
SKU passport. Contains information that doesn`t change in long terms
| Column names    | data type | comment            |
|-----------------|-----------|--------------------|
| sku_id (PK)     | SERIAL    |
| created_time    | TIMESTAMPTZ  | 
| added_by        | BIGINT    | users.id
| approved_by     | BIGINT    | users.id
| is_active       | BOOLEAN   |
| short_name      | VARCHAR(30)|
| full_name       | VARCHAR(90)|
| business        | t_business_type| Coffee, Confectionery, Infant Nutrition
| brand           | INTEGER    | d_brandnames.id



#### d_scd_info <a id="d_scd_info"></a>
Contains information about SKU`s slow dimensional changes
| Column names    | data type | comment            |
|-----------------|-----------|--------------------|
| id (PK)         | SERIAL    |
| sku_id (FK)     | BIGINT    |
| created_time    | TIMESTAMPTZ |
| added_by        | BIGINT    | users.id
| approved_by     | BIGINT    | users.id
| valid_from_cycle| INTEGER   |
| valid_to_cycle  | INTEGER   |
| sku_code_name   | VARCHAR(100)| include brand, sku name or line, package parameter, volume in grams
| product_line    | INTEGER   | d_brand_lines.id
| category        | TYPE      | t_categories
| pack_volume     | INTEGER   | weight in grams 
| package         | TYPE      | t_packages (can, dough pack, etc)
| pack_category   | TYPE      | t_pack_category (mini/midi/maxi)
| price_segment   | TYPE      | t_price_categories (economy/mainstream/)


#### d_companies <a id="companies"></a>
Contains information about SKU`s slow dimensional changes
| Column names    | data type | comment            |
|-----------------|-----------|--------------------|
| id (PK)         | SERIAL    |
| company_name    | VARCHAR(30)|
| added_by (FK)   | BIGINT    | users.id
| added_date      | TIMESTAMPTZ|


#### d_brandnames <a id="brands"></a>
Contains information about SKU`s slow dimensional changes
| Column names    | data type | comment            |
|-----------------|-----------|--------------------|
| id (PK)         | SERIAL    |
| brand_name      | VARCHAR(30)|
| added_by (FK)   | BIGINT    | users.id
| added_date      | TIMESTAMPTZ|
| referred_company (FK)| INTEGER|d_companies.id

#### d_brand_lines <a id="brandlines"></a>
Contains information about SKU`s slow dimensional changes
| Column names    | data type | comment            |
|-----------------|-----------|--------------------|
| id (PK)         | SERIAL    |
| brand_line      | VARCHAR(30)|
| added_by (FK)   | BIGINT    | users.id
| added_date      | TIMESTAMPTZ|
| referred_brand (FK)| INTEGER|d_brand_lines.id

#### d_cycles <a id="d_cycles"></a>
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

### pre-analysis tables

Important Note!
The combination of cycle, SKU code, SCD code should be strictly unique
#### outlet_quotes <a id="outlet_quotes"></a>
Used to store information about strict quotes of outlet types in each city
| Column names    | data type  | comment            |
|-----------------|------------|--------------------|
|outlet_type      | t_outlet_types
|city             | t_cities   |
|quote            | INTEGER    |

outlet_type & city combination must be unique

#### outlet_picking <a id="outlet_picking"></a>
This is used for picking outlets which audits were approved by QC Team for final analysis in selected cycle
| Column names    | data type  | comment            |
|-----------------|------------|--------------------|
|id (PK)          | SERIAL     |
|outlet_taken (FK)| BIGINT     | outlets.outlet_code
|created_time     | TIMESTAMPTZ| 
|cycle (FK)       | INTEGER    |
|status           | t_pick_status| Pending/Picked/Not Picked
|picked_by        | users.id   |
|approval_reason  | t_approve_reason|
|denial_reason    | t_decline_reason|
|comment          | VARCHAR(200)|

Depending of the selected status one of two fields of reason (approval or denial) must be selected except when status is pending
Comment must be filled if "other" selected in approval or denial reason
Only Lead Analyst and manager can pick/unpick outlets for final analysis. 
When picked outlets hits quotes by city and outlet type other outlets in same city-type group must be selected as unpicked automatically
 
<a id="Calculating_reports"></a>

### Calculatable tables (Materialized View)

#### sales_base <a id="sales_base"></a>
Table almost similar to raw_data, but it have sales and another additional rows
It is calculatable and acceembles for business report when picking process id finished
| Column names            | data type  | comment            |
|-------------------------|------------|--------------------|
| cycle                   | INTEGER    | d_cycles.id, comes from audit_data.cycle
| cycle_name              | VARCHAR(14)| comes from d_cycles.cycle_name
| sku_code  (FK)          | BIGINT     | d_sku_info.sku_id, comes from raw_data.sku_code
| full_name               | VARCHAR(90)| comes from d_sku_info.full_name
| scd_code (FK)           | BIGINT     | d_scd_info.id, comes from raw_data.scd_code
| code_name               | VARCHAR(100)| comes from d_scd_info.sku_code_name
| city                    | t_cities   | from outlets.city
| outlet_code  (FK)       | BIGINT     | outlets.outlet_code, comes from audit_data.outlet
| business        | t_business_type|
| category        | VARCHAR (20) | takes information from category column in d_scd_info
| brand  (FK)     | INTEGER    | d_brandnames.id
| company (FK)    | INTEGER    | d_companies.id
| product_line    | INTEGER    | d_brand_lines.id
| current_shelf_stocks    | INTEGER    | calculated from raw_data.shelf_stocks
| previous_shelf_stocks   | INTGER     | calculated from raw_data.shelf_stocks in previous cycle
| current_warehouse_stocks| INTEGER    | calculated from raw_data.warehouse_stocks
| previous_warehouse_stocks| INTEGER   | calculated from raw_data.warehouse_stocks in previous cycle
| purchase                | INTEGER    | calculated from raw_data.purchase
| facing                  | INTEGER    | calculated from raw_data.facing
| price                   | INTEGER    | calculated from raw_data.price
| unit_sales              | INTEGER    | previous stocks + purchase - current stocks
| sales_value             | BIGINT     | unit_sales * price/1 000 000 - value in mln UZS
| sales_volume            | BIGINT     | unit_sales * sku volume /  1 000 000 - in tons
| is_handling             | BOOLEAN    | TRUE if there is any in current/previous stocks, purchase  
| is_out_of_stock         | BOOLEAN    | TRUE if there is no units in current stock because everything was sold

Upon completion of the fieldwork and selection of the required retail outlets, the cycle calculations are performed and appended to the table
All data calculates from joined raw_data with qc_verification with corrections for right and approved information to collect current and previous numeric information of each visit. Then we join result table with qc_tasks, audit_data, qc_audit_plan, outlets, d_sku_info, d_scd_info, outlet_picking, d_cycles. Аfter that the final table will be filtered by picked and approved information and appended to sales_base. Audit_plan joined as safety filter, in case a visit's qc_tasks got Approved while the parent audit_plan somehow remains not Finished
The combination of outlet, sku, scd codes, cycle and city values must be strictly unique!

#### business_report <a id="business_report"></a>
Calculatable table assembled from sales_base
Contains Nielsen calculations and shows business 
| Column names    | data type  | comment            |
|-----------------|------------|--------------------|
| cycle           | INTEGER    | 
| cycle_name      | VARCHAR(14)| 
| sku_code  (FK)  | BIGINT     | NULL if measure_level <> "SKU" 
| full_name       | VARCHAR(90)| For Each measure level: SKU Name/Brand name/Company Name/Category Name/Price Segment
| scd_code (FK)   | BIGINT     | NULL if measure_level <> "SKU"
| code_name       | VARCHAR(100)| NULL if measure_level <> "SKU"
| city            | t_cities   | value "Total" will be added for all measure levels
| business        | t_business_type|
| category        | VARCHAR (20) | takes information from category column in d_scd_info
| brand  (FK)     | INTEGER    | d_brandnames.id
| company (FK)    | INTEGER    | d_companies.id
| product_line    | INTEGER    | d_brand_lines.id
| price_segment   | VARCHAR (20)|
| age_segment     | VARCHAR (20)|
| package         | VARCHAR (20)|
| pack_category   | VARCHAR (20)|
| volume          | SMALLINT  |
| measurement     | VARCHAR(15)| names of measurements
| measure_level   | t_measure_level| SKU/BRAND/BUSINESS/CATEGORY/PRICE SEGMENT

This is the biggest table in all data because of measurements, total cities and measre levels indicators levels. Some measurements such as distribution cannot be summarized to the brand/company on the SKU level, so it is necessary to make additional levels where aggregated measurements could be shown.
The levels of measurements:
* SKU (all measurements)
* brand (all except price frequency)
* company (all excep prices)
* price_segment (sales, facing_share and distribution)

An important note! Each calculation of new cycle should be appended, no recalculation of this table except cases when it`s necessary!

#### master_sales <a id="master_sales"></a>
Table containing sales&price indicators for each business and category and assembled on the level structure which is explained under the table
| Column names    | data type   | comment            |
|-----------------|-------------|--------------------|
| business_lvl    | VARCHAR (30)| The higher level with business names. Incudes "Total" for cross business analysis. It is intended to add this columnt to power BI matrix in the rows field.
| lvl_01_category | VARCHAR (30)| All categories inside the business + total for intercategorial analysis
| lvl_02_price_segment| VARCHAR (30)| All price segments + total for intersegmentional analysis
| lvl_03_companies| t_companies | All companies
| volume          | INTEGER     | sales volume in tons
| value           | BIGINT      | sales value in MLN UZS
| cycle           | INTEGER     | d_cycles.id


<a id="Formulas"></a>

## Report Indicators (Measurements)

### Indicators & Formulas
 * **Unit Sales** or US - quantity of qoods sold. US = previous stocks + purchase -  current stocks  
 * **Sales value in mln UZS** - summarized value of all sold SKU  
 $$\sum_{i,k}^{n, m} (US*Price)_i$$

Where **i** - Outlet code, **k** - SKU & SCD code combination
* **Sales volume, in tons** - volume of sold SKU in tons.
 $$\sum_{i,k}^{n, m} (US * v)_i$$
 Where **v** is scd_volume

 * **Value/Volume Share** - the share of sales by SKU/Company/Brand etc. Calculates in each area level (Total market, Business, City, Category, etc)

 * **Numeric Handling** - Share of outlets that distributing SKU, Brand or Company. 

<div align="center"> 

$Numeric$ $Distribution$ =  $\frac{Number of handlers}{Total Outlets}$ 

</div>
Note - Total outlets number depends of outlets that distributing a category if we working inside selected category

* **Weighted Handling** - the sales share of SKU/Brand/Company in outlets that distribute tracked SKU/Brand/Company

<div align="center"> 

$Weighted$ $Distribution$ =  $\frac{HV}{TSV}$ 

</div>

**HV** - Total Sales volume of SKU/Brand/Company handlers
**TSV** - Total Sales volume of Business/Category Handlers (for total distribution, distribution inside category)  

* **Numeric Out of Stock** - the share of SKU/Brand/Company handlers with no stocks in the moment of visit. 

<div align="center"> 

$Numeric$ $OOS$ =  $\frac{OOS}{Total Outlets}$ 

</div>

**OOS** - number of handlers with zero stocks in the moment of visit 

**Note** - Total outlets number depends of outlets that distributing a category if we working inside selected category

* **Weighted Out of Stock** - the sales share of outlets with zero stocks tracked SKU/Brand/Company

<div align="center"> 

$Weighted$ $OOS$ =  $\frac{OOSV}{TSV}$ 

</div>

**OOSV** - Total Sales volume of SKU/Brand/Company handlers with zero stocks

**TSV** - Total Sales volume of Business/Category Handlers (for total distribution, distribution inside category)  

* **First Frequent Price** - Most Frequent Price of SKU
* **Price Per gram** - Average Price for the gram of SKU/Brand

<div align="center"> 

$PPG$ =  $\frac{SVal}{SVol}$
 
</div>
SVal - Sales Value of SKU\Brand

SVol - Sales Volume of SKU\Brand

* Price Per Item - Average price for SKU\Brand

<div align="center"> 

$PPI$ =  $\frac{SVal}{US}$

</div>

* **Facing Share** - share of SKU`s\Brand\Company products sharing

<div align="center"> 

$Facing$ $Share$ =  $\frac{Facing}{US}$

</div>

## Additional information

### SQL scrits
All SQL scripts is extracted in separate files:

* [Set up database and create main tables for collecting and approval](SQL-main_tables.md)
* Creating reports
* Adding new information (python)
* Calculating and appending new cycle data (python & SQL)



# Power BI and analyzing processes 