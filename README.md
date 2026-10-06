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


## Database <a id='DB_structure'></a>

PostgreSQL is chosen as the Database Management System (DBMS)
All tables will be created manually first but later it will be automated
All SQL scripts is extracted in separate files:

* [Database structure and specification](Database_structure.md)
* Setting up database
* [Creating main tables for collecting and approval](SQL-main_tables.md)
* Creating reports
* Adding new information (python)
* Calculating and appending new cycle data (python & SQL)



# Power BI and analyzing processes 
still planning