# PROJECT ROADMAP
⚪️ - planned
🔵 - in process
🟡 - on hold
🔴 - stopped/make another plan
🟢 - finished

## Stage 1: Setting up database and main tables

> - 🔵 **Step 1:**  Write the SQL query to create table
> - ⚪️ **Step 2:**  Develop basic python scripts to upload base information from initializing file (users, roles, )

## Stage 2: Basic user interface
> - ⚪️ **Step 0:** Basic PC client
> - ⚪️ **Step 0.1** Basic API for PC client
> - ⚪️ **Step 0.2** Basic mobile client
> - ⚪️ **Step 0.3** Basic API for mobile client
> - ⚪️ **Step 1:** Interface for user administration (PC client).
> - ⚪️ **Step 2:** API for user/role administration (API for PC client)
> - ⚪️ **Step 3:** Accemble basic python application and add user administration for PC client
> - ⚪️ **Step 4:** Connecting mobile application with API (Login/Password, API answers) 
 
The PC client must be universal as for agents, as for management, but usersh shouldn`t get access for management interface nor the management for adding information about visits.

Mobile client pretent to be only for auditors. Auditors` visits should be recorded only trough mobile client.

The interface depends of role and be downloaded from API each authorisation.


## Stage 3: Collection & Verification

> - ⚪️ **Step 5:** User management processes:
> - ⚪️ &emsp; **Step 5.1:** User management module
> - ⚪️ &emsp; **Step 5.2:** User management forms - add, redact and delete (restrict access)
> - ⚪️ &emsp; **Step 5.3:** 
<br><br>
> - ⚪️ **Step 6:** QC module:
> - ⚪️ &emsp; **Step 6:0** SKU registration form
> - ⚪️ &emsp; **Step 6:1** SCD registration form
> - ⚪️ &emsp; **Step 6:1** SKU & SCD registration API
> - ⚪️ &emsp; **Step 6:1** SKU & SCD observation form in PC client
<br><br>
> - ⚪️ **Step 7:** Audit processes:
> - ⚪️ &emsp; **Step 7.0:** SKU & SCD observation form in mobile client
> - ⚪️ &emsp; **Step 7.1:** Outlet Registration form in mobile client
> - ⚪️ &emsp; **Step 7.2:** Outlet Registration API 
> - ⚪️ &emsp; **Step 7.3:** Audit planner (API)
> - ⚪️ &emsp; **Step 7.4:** Auditors module 
> - ⚪️ &emsp; **Step 7.5:** Audits interface
> - ⚪️ &emsp; **Step 7.6:** Audit collection form
> - ⚪️ &emsp; **Step 7.7:** Audit collection API
> - ⚪️ &emsp; **Step 7.8:** Audit management form for supervisors
> - ⚪️ &emsp; **Step 7.9:** Audit management API
<br><br>
> - ⚪️ **Step 8:** QC processes:
> - ⚪️ &emsp; **Step 8.0:** QC automatic task planner API
> - ⚪️ &emsp; **Step 8.1:** QC task viewer and management form (plans additional info)
> - ⚪️ &emsp; **Step 8.2:** QC task management and viewer API 
> - ⚪️ &emsp; **Step 8.3:** QC process form (view lists, data and evidences)
> - ⚪️ &emsp; **Step 8.4:** QC process API (Asyncronous lists approvals & sending data to DB)
> - ⚪️ &emsp; **Step 8.5:** QC approval tasks (API & form modification)

## Stage 4: Analyze processes
> - ⚪️ **Step 1:** Outlet Analyze Web Dashboard (python & streamlit) for tracking sales and distribution information
> - ⚪️ **Step 2:** Picklists in web dashboard for genereting final sales information for cycle
> - ⚪️ **Step 3:** Generating final sales information for new cycle (API)
> - ⚪️ **Step 4:** Intermadiate sales report web dashboard for data generation
> - ⚪️ **Step 5:** Generating master sales and sales report for new cycle
> - ⚪️ **Step 6:** Sales report Power BI dashboard (.pbip)  
> - ⚪️ **Step 7:** Master sales Power BI dashboard (.pbip)


<a href="javascript:history.back()" style="
    position: fixed;
    top: 20px;
    left: 20px;
    z-index: 9999;
    background-color: #007bff;
    color: #ffffff !important;
    padding: 12px 18px;
    border-radius: 50px;
    text-decoration: none;
    font-family: sans-serif;
    font-weight: bold;
    box-shadow: 0 4px 10px rgba(0,0,0,0.3);
    transition: background-color 0.3s;
" onmouseover="this.style.backgroundColor='#0056b3'" onmouseout="this.style.backgroundColor='#007bff'">
    ← Back
</a>