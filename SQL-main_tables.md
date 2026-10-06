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
CREATE TYPE t_outlet_activity as  ('Active', 'Inactive')
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
    id SERIAL RPIMARY KEY,
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
    register_date TIMESTAMPZ NOT NULL,
    created_by BIGINT,
    gender t_gender NOT NULL,

    --creating foreign key referencing to user id
    CONSTRAINT fk_creator
        FOREIGN KEY (created_by)
        REFERENCES users (id)
        ON DELETE SET NULL
);
--and then creating first user as grand admin
INSERT INTO users (first_name, second_name, register_date, created_by gender) 
VALUES ('GRAND', 'ADMIN', NOW(), NULL, 'O');

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
('Audit agents', NOW(), 1, TRUE),
('QC team', NOW(), 1, TRUE),
('Management', NOW(), 1, TRUE);

--creating roles
CREATE TABLE roles (
    role_id SERIAL PRIMARY KEY,
    role_name VARCHAR(20),
    created_time TIMESTAMPTZ,
    valid_to_date, TIMESTAMPTZ,
    is_active BOOLEAN,
    created_by BIGINT,

    CONSTRAINT fk_creators
        FOREIGN KEY (created_by)
        REFERENCES users (id)
        ON DELETE SET NULL
);
--Adding base roles
INSERT INTO roles (
    role_name, created_time, valid_to_date, is_active, created_by
    )
VALUES 
('Agent', NOW(), NULL, TRUE, 1),
--Can register outlet and upload audit data
('Supervisor', NOW(), NULL, TRUE, 1),
--Provides deadlines for audit and can see audit/validation data
('Audit lead', NOW(), NULL, TRUE, 1),
--Watches audit/validation results and creates auditor teams (with PM approval)
('QC Specialist', NOW(), NULL, TRUE, 1),
--Watches and approves audit data
('QC Lead', NOW(), NULL, TRUE, 1),
--Watches and approves verification
('QC Manager', NOW(), NULL, TRUE, 1),
--Can create QC teams and see audit/validation data
('Project Manager', NOW(), NULL, TRUE, 1),
--Can manage project teams see audit/validation data, see analytics results
('Analyst', NOW(), NULL, TRUE, 1),
--Provides analytics based on validation results
('Owner', NOW(), NULL, TRUE, 1),
--Can manage projects and see the processes
('GRAND ADMIN', NOW(), NULL, TRUE, 1);
--Role with maximal access

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
        REFERENCES roles (id)
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
        FOREIGN KEY added_by
        REFERENCES users (id)
        ON DELETE RESTRICT,

    CONSTRAINT fk_approval
        FOREIGN KEY approved_by
        REFERENCES users (id)
        ON DELETE RESTRICT,

    CONSTRAINT fk_brands
        FOREIGN KEY brand
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
    outlet_location GEOGRAPHY,
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
    id SERIAL,
    plan_id BIGINT,
    outlet BIGINT,
    visit_sequence INTEGER NOT NULL,
    start_time TIMESTAMPTZ NOT NULL,
    end_time TIMESTAMPTZ NOT NULL,
    audit_status BOOLEAN NOT NULL,
    next_audit_needed BOOLEAN NOT NULL,
    next_cycle_audit BOOLEAN NOT NULL,
    agent_comment VARCHAR(255),

    CONSTRAINT pk_visits
        PRIMARY KEY (id, plan_id, outlet),

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
        REFERENCES d_sku_info (id)
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
