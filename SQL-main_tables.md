# Setting up tables

## Stage 1 - Creating ENUMS and Cycles


```sql
--CREATING ALL ENUMS
CREATE TYPE t_cities AS ENUM (
    'Total', 'Andijan', 'Bukhara', 'Fergana', 'Samarkand', 'Tashkent'
    );
CREATE TYPE t_outlets_type as ENUM (
    'Open Markets', 'Big Grocery', 'Small Grocery', 'Minimarket','Supermarket'
);
CREATE TYPE t_outlet_activity as ENUM ('Active', 'Inactive');
CREATE TYPE t_outlet_types as ENUM ('Open Market', 'Pavillions an Bus stops', 'Big Grocery', 'Small Grocery', 'Minimarket', 'Supermarket')
CREATE TYPE t_audit_status as ENUM (
    'Pending', 'In Progress', 'On Hold', 'Finished', 'Failed', 'Questionable'
    );
CREATE TYPE t_verification_status as ENUM (
    'Accepted', 'Questionable', 'Corrected', 'Escalated to Reject'
);
CREATE TYPE t_task_status as ENUM (
    'Pending', 'Questionable', 'In Progress', 'On Hold', 'Escalated to Reject', 'Finished', 'Rejected', 'Approved'
);
CREATE TYPE t_audit_type as ENUM ('Regular Audit', 'Baseline Audit');
CREATE TYPE t_file_type as ENUM ('Image', 'Video', 'Audio');
CREATE TYPE t_gender AS ENUM ('M', 'F', 'O');
CREATE TYPE t_movements as ENUM (
    'HIRING', 'LEAVING', 'TRANSITION'
);
CREATE TYPE t_pick_status as ENUM (
    'Pending', 'Picked', 'Not Picked'
);
CREATE TYPE t_approve_reason as ENUM (
    'representative', 'quota_gap_fill', 'replasement', 'other_reason'
);
CREATE TYPE t_decline_reason as ENUM (
    'non-representative', 'quota_exceeded', 'repeated_qc_failure', 'duplicate_coverage', 'other_reason'
);
CREATE TYPE t_business_type as ENUM (
    'Coffee', 'Confectionery', 'Infant Nutrition'
);
CREATE TYPE t_measure_level as ENUM (
    'SKU', 'Brand', 'Company'
);

CREATE TYPE t_categories as ENUM (
    'Tablets', 'Bars', 'Jelly', 
    'Pure Soluble Coffee', 'Mixed Coffee',
    'IF&GUM', 'Cereal', 'Puree'
    );

CREATE TYPE t_packages as ENUM (
    'Can', 'Dough pack', 'Glass', 'Carton', 'Etiquet'
);

CREATE TYPE t_pack_category as ENUM (
    'mini', 'midi', 'maxi'
);

CREATE TYPE t_price_categories as ENUM (
    'Economy', 'Mainstream', 'Premium'
);
--Creating cycles table
CREATE TABLE d_cycles (
    id SERIAL PRIMARY KEY,
    cycle_name VARCHAR(14) NOT NULL UNIQUE,
    starts_from DATE NOT NULL UNIQUE,
    ends_in DATE NOT NULL UNIQUE
);
```
## Stage 2 - Creating user tables
``` sql

--Creating users tables
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    first_name VARCHAR(30) NOT NULL,
    second_name VARCHAR(30) NOT NULL,
    register_date TIMESTAMPTZ NOT NULL,
    created_by BIGINT,
    gender t_gender NOT NULL,

    --creating foreign key referencing to user id
    CONSTRAINT fk_creator
        FOREIGN KEY (created_by)
        REFERENCES users (id)
        ON DELETE SET NULL
);
--and then creating first user as grand admin
INSERT INTO users (first_name, second_name, register_date, created_by, gender) 
VALUES ('GRAND', 'ADMIN', NOW(), NULL, 'O');
--now we need to add passwords table 
--first adding extention for to hash passwords
CREATE EXTENSION IF NOT EXISTS pgcrypto;
--then creating passwords table
CREATE TABLE passwords (
    user_id BIGINT NOT NULL,
    password_hash VARCHAR(60) NOT NULL,
    is_current BOOLEAN NOT NULL,
    created_time TIMESTAMPTZ NOT NULL,
    changed_time TIMESTAMPTZ,

    CONSTRAINT fk_user
        FOREIGN KEY (user_id)
        REFERENCES users (id)
        ON DELETE RESTRICT
);
--adding default password for SUPER ADMIN
--select your own password before executing script
INSERT INTO passwords (user_id, password_hash, is_current, created_time)
    VALUES (1, crypt('standart_password', gen_salt('bf')), TRUE, NOW());

--creating departments
CREATE TABLE departments (
    id SERIAL PRIMARY KEY,
    team_name VARCHAR(20),
    registration_date TIMESTAMPTZ,
    registered_by BIGINT,
    is_active BOOLEAN,

    CONSTRAINT fk_users
        FOREIGN KEY (registered_by)
        REFERENCES users (id)
        ON DELETE SET NULL 
);
--adding departments
INSERT INTO departments (
    team_name, registration_date, registered_by, is_active
)
VALUES 
('Audit team', NOW(), 1, TRUE),
('QC team', NOW(), 1, TRUE),
('Management', NOW(), 1, TRUE);

--creating roles
CREATE TABLE roles (
    role_id SERIAL PRIMARY KEY,
    role_name VARCHAR(20),
    created_time TIMESTAMPTZ,
    valid_to_date TIMESTAMPTZ,
    is_active BOOLEAN,
    created_by BIGINT,
    parent_role BIGINT REFERENCES roles(role_id) ON DELETE RESTRICT,

    CONSTRAINT fk_creators
        FOREIGN KEY (created_by)
        REFERENCES users (id)
        ON DELETE SET NULL
);

--Adding base roles
--Grand roles
INSERT INTO roles (
    role_name, created_time, valid_to_date, is_active, created_by, parent_role
    )
VALUES 
--Role with maximal access
('GRAND ADMIN', NOW(), NULL, TRUE, 1, null),
--Can manage projects and see the processes
('Owner', NOW(), NULL, TRUE, 1, null);

--_________________________________________________________
-- Roles under Owner (PM-s & Analysts)
INSERT INTO roles (
    role_name, created_time, valid_to_date, is_active, created_by, parent_role
    )
VALUES 
--Provides analytics based on validation results
('Analyst', NOW(), NULL, TRUE, 1, 2),
--Can manage project teams see audit/validation data, see analytics results
('Project Manager', NOW(), NULL, TRUE, 1, 2);

--__________________________________________________________
-- Operational Managers Roles
INSERT INTO roles (
    role_name, created_time, valid_to_date, is_active, created_by, parent_role
    )
VALUES 
('Audit Manager', NOW(), NULL, TRUE, 1, 4),
--Watches audit/validation results and creates auditor teams (with PM approval)
('QC Manager', NOW(), NULL, TRUE, 1, 4);
--Watches and approves verification

--__________________________________________________________
-- Operational Supervisors` Roles
INSERT INTO roles (
    role_name, created_time, valid_to_date, is_active, created_by, parent_role
    )
VALUES 
--Provides deadlines for audit and can see audit/validation data
('Supervisor', NOW(), NULL, TRUE, 1, 5),
--Can create QC teams and see audit/validation data
('QC Lead', NOW(), NULL, TRUE, 1, 6);

--__________________________________________________________
-- Linear Roles
INSERT INTO roles (
    role_name, created_time, valid_to_date, is_active, created_by, parent_role
    )
VALUES 
('Audit Agent', NOW(), NULL, TRUE, 1, 7),
--Can register outlet and upload audit data
('QC Specialist', NOW(), NULL, TRUE, 1, 8);
--Watches and approves audit data


--Creating team roaster
CREATE TABLE team_roaster (
    id SERIAL PRIMARY KEY,
    log_date TIMESTAMPTZ NOT NULL,
    agent BIGINT NOT NULL,
    movement_type t_movements NOT NULL,
    department BIGINT NOT NULL,
    agent_role BIGINT NOT NULL,
    valid_from DATE NOT NULL,
    valid_to DATE NOT NULL,
    is_current BOOLEAN NOT NULL,
    assigned_by BIGINT NOT NULL,
    comment text,

    CONSTRAINT fk_agent
        FOREIGN KEY (agent)
        REFERENCES users (id)
        ON DELETE RESTRICT,
    
    CONSTRAINT fk_departments
        FOREIGN KEY (department)
        REFERENCES departments (id)
        ON DELETE RESTRICT,

    CONSTRAINT fk_roles
        FOREIGN KEY (agent_role)
        REFERENCES roles (role_id)
        ON DELETE RESTRICT,

    CONSTRAINT fk_assigner
        FOREIGN KEY (assigned_by)
        REFERENCES users (id)
        ON DELETE RESTRICT

);
```

## Stage 3 - Dictionaries

``` sql
--companies list
CREATE TABLE d_companies (
    id SERIAL PRIMARY KEY,
    company_name VARCHAR(30) NOT NULL,
    added_by BIGINT NOT NULL,
    added_date TIMESTAMPTZ NOT NULL,

    CONSTRAINT fk_added_by
        FOREIGN KEY (added_by)
        REFERENCES users (id)
        ON DELETE RESTRICT
);

--brand names list
CREATE TABLE d_brandnames (
    id SERIAL PRIMARY KEY,
    brand_name VARCHAR(30) NOT NULL,
    added_by BIGINT NOT NULL,
    added_date TIMESTAMPTZ NOT NULL,
    referred_company BIGINT NOT NULL,

    CONSTRAINT fk_added_by
        FOREIGN KEY (added_by)
        REFERENCES users (id)
        ON DELETE RESTRICT,

    CONSTRAINT fk_company
        FOREIGN KEY (referred_company)
        REFERENCES d_companies (id)
        ON DELETE RESTRICT
);

CREATE TABLE d_sku_info (
    sku_id SERIAL PRIMARY KEY,
    created_time TIMESTAMPTZ NOT NULL,
    added_by BIGINT NOT NULL,
    approved_by BIGINT NOT NULL,
    is_active BOOLEAN NOT NULL,
    short_name VARCHAR(30) NOT NULL,
    full_name VARCHAR(90) NOT NULL,
    business t_business_type NOT NULL,
    brand INTEGER NOT NULL,

    CONSTRAINT fk_added_by
        FOREIGN KEY (added_by)
        REFERENCES users (id)
        ON DELETE RESTRICT,

    CONSTRAINT fk_approval
        FOREIGN KEY (approved_by)
        REFERENCES users (id)
        ON DELETE RESTRICT,

    CONSTRAINT fk_brands
        FOREIGN KEY (brand)
        REFERENCES d_brandnames (id)
        ON DELETE RESTRICT
);

--brand lines
CREATE TABLE d_brand_lines (
    id SERIAL PRIMARY KEY,
    brand_line VARCHAR(30) NOT NULL,
    added_by BIGINT NOT NULL,
    added_date TIMESTAMPTZ NOT NULL,
    referred_brand INTEGER NOT NULL,

    CONSTRAINT fk_added_user
        FOREIGN KEY (added_by)
        REFERENCES users (id)
        ON DELETE RESTRICT,

    CONSTRAINT fk_brand_reference
        FOREIGN KEY (referred_brand)
        REFERENCES d_brandnames (id)
        ON DELETE RESTRICT
);

CREATE TABLE d_scd_info (
    id SERIAL PRIMARY KEY,
    sku_id BIGINT NOT NULL,
    creatad_time TIMESTAMPTZ NOT NULL,
    added_by BIGINT NOT NULL,
    approved_by BIGINT NOT NULL,
    valid_from_cycle INTEGER NOT NULL,
    valid_to_cycle INTEGER,
    sku_code_name VARCHAR(100) NOT NULL,
    product_line INTEGER NOT NULL,
    category t_categories NOT NULL,
    pack_volume INTEGER NOT NULL,
    package t_packages NOT NULL,
    pack_category t_pack_category NOT NULL,
    price_segment t_price_categories NOT NULL,

    CONSTRAINT fk_sku
        FOREIGN KEY (sku_id)
        REFERENCES d_sku_info (sku_id)
        ON DELETE RESTRICT,

    CONSTRAINT fk_added_user
        FOREIGN KEY (added_by)
        REFERENCES users (id)
        ON DELETE RESTRICT,

    CONSTRAINT fk_approved_user
        FOREIGN KEY (approved_by)
        REFERENCES users (id)
        ON DELETE RESTRICT,

    CONSTRAINT fk_cycle_start
        FOREIGN KEY (valid_from_cycle)
        REFERENCES d_cycles (id)
        ON DELETE RESTRICT,

    CONSTRAINT fk_cycle_end
        FOREIGN KEY (valid_to_cycle)
        REFERENCES d_cycles (id)
        ON DELETE RESTRICT,

    CONSTRAINT fk_product_line
        FOREIGN KEY (product_line)
        REFERENCES d_brand_lines (id)
        ON DELETE RESTRICT
);
```

## Stage 4 - Audit Tables (Outlets, questionnaire)

``` sql
--Outlets table
CREATE TABLE outlets (
    outlet_code SERIAL PRIMARY KEY,
    registered_date TIMESTAMPTZ NOT NULL,
    base_cycle INTEGER NOT NULL,
    city t_cities NOT NULL,
    outlet_location POINT,
    adress VARCHAR(255) NOT NULL,
    owner_name VARCHAR(255) NOT NULL,
    owner_phone1 VARCHAR(12) NOT NULL, --Uzbekistan
    owner_phone2 VARCHAR(12), --Uzbekistan
    outlet_type t_outlet_types NOT NULL,
    outlet_status t_outlet_activity NOT NULL,
    comments VARCHAR(255), 

    CONSTRAINT fk_base_cycle
        FOREIGN KEY (base_cycle)
        REFERENCES d_cycles (id)
        ON DELETE RESTRICT
);

--Planned audits
CREATE TABLE audit_plan (
    id SERIAL PRIMARY KEY,
    outlet BIGINT NOT NULL,
    cycle INTEGER NOT NULL,
    start_time_plan TIMESTAMPTZ NOT NULL,
    end_time_plan TIMESTAMPTZ NOT NULL,
    visits_plan SMALLINT NOT NULL,
    assigned_for BIGINT NOT NULL,
    assigned_by BIGINT NOT NULL,
    assigned_time TIMESTAMPTZ NOT NULL,
    assigned_type t_audit_type NOT NULL,
    audit_status t_audit_status NOT NULL,

    CONSTRAINT fk_outlet
        FOREIGN KEY (outlet)
        REFERENCES outlets (outlet_code)
        ON DELETE RESTRICT,
    
    CONSTRAINT fk_cycles
        FOREIGN KEY (cycle)
        REFERENCES d_cycles (id)
        ON DELETE RESTRICT,

    CONSTRAINT fk_assigned_agent
        FOREIGN KEY (assigned_for)
        REFERENCES users (id)
        ON DELETE RESTRICT,

    CONSTRAINT fk_assigned_by
        FOREIGN KEY (assigned_by)
        REFERENCES users (id)
        ON DELETE RESTRICT
);

--Audits/visits done
CREATE TABLE audit_data (
    id SERIAL PRIMARY KEY,
    plan_id BIGINT,
    outlet BIGINT,
    visit_sequence INTEGER NOT NULL,
    start_time TIMESTAMPTZ NOT NULL,
    end_time TIMESTAMPTZ NOT NULL,
    audit_status BOOLEAN NOT NULL,
    next_audit_needed BOOLEAN NOT NULL,
    next_cycle_audit BOOLEAN NOT NULL,
    agent_comment VARCHAR(255),

    CONSTRAINT fk_plan
        FOREIGN KEY (plan_id)
        REFERENCES audit_plan (id)
        ON DELETE RESTRICT,

    CONSTRAINT fk_outlets
        FOREIGN KEY (outlet)
        REFERENCES outlets (outlet_code)
        ON DELETE RESTRICT
);

--information gathered
CREATE TABLE raw_data (
    id SERIAL PRIMARY KEY,
    audit_id BIGINT NOT NULL,
    sku_code BIGINT NOT NULL,
    scd_code BIGINT NOT NULL,
    price INTEGER NOT NULL CHECK (price > 0),
    shelf_stock INTEGER NOT NULL CHECK (shelf_stock >= 0),
    warehouse_stock INTEGER NOT NULL CHECK (shelf_stock >= 0), 
    purchase INTEGER NOT NULL CHECK (purchase >= 0),
    facing INTEGER NOT NULL CHECK (facing >= 0),
    planned_distribution INTEGER,
    is_distributed BOOLEAN NOT NULL,
    will_distribute BOOLEAN NOT NULL,
    comment VARCHAR(255),

    CONSTRAINT fk_audit
        FOREIGN KEY (audit_id)
        REFERENCES audit_data (id)
        ON DELETE RESTRICT,

    CONSTRAINT fk_sku
        FOREIGN KEY (sku_code)
        REFERENCES d_sku_info (sku_id)
        ON DELETE RESTRICT,

    CONSTRAINT fk_scd
        FOREIGN KEY (scd_code)
        REFERENCES d_scd_info (id)
        ON DELETE RESTRICT 
);

--evidences links
CREATE TABLE binary_links (
    id SERIAL PRIMARY KEY,
    binary_type t_file_type NOT NULL,
    binary_link TEXT NOT NULL,
    added_time TIMESTAMPTZ NOT NULL
);
```

## Stage 5 - Quality Control tables
``` sql
--Audit check tasks
CREATE TABLE qc_tasks (
    id SERIAL PRIMARY KEY,
    audit_id BIGINT NOT NULL,
    created_time TIMESTAMPTZ NOT NULL,
    start_time TIMESTAMPTZ NOT NULL,
    assigned_by BIGINT NOT NULL,
    task_status t_task_status NOT NULL,

    CONSTRAINT fk_audit
        FOREIGN KEY (audit_id)
        REFERENCES audit_data (id)
        ON DELETE RESTRICT,

    CONSTRAINT fk_assignee
        FOREIGN KEY (assigned_by)
        REFERENCES users (id)
        ON DELETE RESTRICT
);

--verification status log
CREATE TABLE qc_tasks_log (
    id SERIAL PRIMARY KEY,
    task_id BIGINT NOT NULL,
    modified_by BIGINT NOT NULL,
    mofified_time TIMESTAMPTZ NOT NULL,
    start_time_new TIMESTAMPTZ NOT NULL,
    end_time_nem TIMESTAMPTZ,
    task_status_new t_task_status NOT NULL,

    CONSTRAINT fk_modified 
        FOREIGN KEY (modified_by)
        REFERENCES users (id)
        ON DELETE RESTRICT
);

--raw data with check status
CREATE TABLE qc_verification (
    id SERIAL PRIMARY KEY,
    assign_id BIGINT NOT NULL,
    verified_by BIGINT NOT NULL,
    started_time TIMESTAMPTZ NOT NULL,
    applied_time TIMESTAMPTZ,
    pos_id BIGINT NOT NULL,
    verification_status t_verification_status,
    columns_change jsonb,
    comments TEXT,

    CONSTRAINT fk_task
        FOREIGN KEY (assign_id)
        REFERENCES qc_tasks (id)
        ON DELETE RESTRICT,

    CONSTRAINT fk_verified
        FOREIGN KEY (verified_by)
        REFERENCES users (id)
        ON DELETE RESTRICT,

    CONSTRAINT fk_raw
        FOREIGN KEY (pos_id)
        REFERENCES raw_data (id)
        ON DELETE RESTRICT
);

--evidences
CREATE TABLE qc_evidence_items (
    id SERIAL PRIMARY KEY,
    binary_id BIGINT NOT NULL,
    assigned_by BIGINT NOT NULL,
    assigned_date TIMESTAMPTZ NOT NULL,
    raw_id BIGINT,
    audit_id BIGINT,

    CONSTRAINT fk_binary
        FOREIGN KEY (binary_id)
        REFERENCES binary_links (id)
        ON DELETE RESTRICT,

    CONSTRAINT fk_assigned
        FOREIGN KEY (assigned_by)
        REFERENCES users (id)
        ON DELETE RESTRICT,

    CONSTRAINT fk_raw
        FOREIGN KEY (raw_id)
        REFERENCES raw_data (id)
        ON DELETE RESTRICT,

    CONSTRAINT fk_audit
        FOREIGN KEY (audit_id)
        REFERENCES audit_data (id)
        ON DELETE RESTRICT
);

```