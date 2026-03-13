
# Requisition in Configuration Portal
This guide documents the Requisition configuration features in the MyHR Configuration Portal, including permission mapping, Templates, Field Library, Job hierarchy, Salary Ranges, and Audit reporting.
*Last updated: 2026-03-06*
---

Background
--------
Build a requisition request/approval workflow enabling managers to request new positions and backfills, and route those requests through a configurable approval process. The feature includes a configuration portal for HR admins to set up templates, fields, job hierarchies, and approval processes.

Overview
--------
This guide documents the Requisition configuration features in the MyHR Configuration Portal, including permission mapping, Templates, Field Library, Job hierarchy, Process configuration, and Audit reporting.

Revision history
----------------

| Version | Date | Author | Notes |
| --- | --- | --- | --- |
| 1.0 Draft | May 19, 2026 | Cathy Cai | Initial draft |


## Account Management 
---------------------------------

When creating roles in the Admin Portal, add a module named `Requisition` under the Configuration Portal permissions. Assign granular permissions: `View`, `Create`, `Edit`, and `Delete`. Those permissions control access and UI actions across the Requisition submodules (Templates, Field Library, Job hierarchy).

### General permission rules (per-submodule)

- `View`: Allows viewing items across Templates, Field Library, Job hierarchy, and Process configuration. When only `View` is granted users can browse lists and open read-only detail pages; no create/edit/delete/import/export action buttons are available.
- `Create`: Grants creation rights in the specific submodule where assigned. Examples:
  - Templates: can create new templates; UI shows `Create`, `Activate`/`Deactivate`, `Copy`.
  - Field Library: can create custom fields; UI shows `Create`, `Enable`/`Disable`.
  - Job hierarchy (Job Code / Salary Range): can add or import items; UI shows `Add`, `Import`, `Export`.
  - Process configuration: can view and edit new process items.
  `View` is implicitly included when `Create` is assigned. If view is unchecked, the create will be unchecked as well since you cannot create without view access.
- `Edit`: Grants edit/update rights in the specific submodule. Examples:
  - Templates: can edit template content; UI shows `Edit`, `Activate`/`Deactivate`.
  - Field Library: can edit custom fields; UI shows `Edit`, `Enable`/`Disable`.
  - Job hierarchy: can edit job/position/salary entries; UI shows `Edit`, `Export`.
  - Process configuration: can view and edit process items.
  `View` is implicitly included when `Edit` is assigned.If view is unchecked, the edit will be unchecked as well since you cannot edit without view access.
- `Delete`: Grants delete rights, subject to submodule rules and object state. Examples:
  - Templates: can delete draft or inactive templates only; `Delete` is disabled for active templates.
  - Field Library: can delete customized fields (supports batch delete).
  - Job hierarchy: can delete job/salary entries; supports batch delete.
  - Process configuration: can view process items but cannot delete.
  `View` is implicitly included when `Delete` is assigned.If view is unchecked, the delete will be unchecked as well since you cannot delete without view access.

  **Data access**
- Configuration portal currently has no scope control; users with the right permissions can create templates, add fields, add job hierarchy items, and configure process configuration for all regions and countries.

### Specific permission rules (Configuration Portal)

**Templates**

Buttons and behavior by permission:

| Permission level | Description | Buttons / UI actions |
| --- | --- | --- |
| View | View all templates | (no action buttons) |
| Create | Create new templates | Create / Activate / Deactivate / Copy |
| Edit | Edit existing templates | Edit / Activate / Deactivate |
| Delete | Delete inactive/draft templates | Delete |


**Field Library**

Manages fields used on Requisition forms.

| Permission level | Description | Buttons / UI actions |
| --- | --- | --- |
| View | View all fields | (no action buttons) |
| Create | Create new fields | Add |
| Edit | Edit custom fields | Edit |
| Delete | Delete custom/disabled fields | Delete |

**Job hierarchy**

Permission mapping is consistent across tabs:

| Permission level | Description | Buttons / UI actions |
| --- | --- | --- |
| View | View items | Export |
| Create | Add, Import, and Copy items | Add / Import/ Copy / Export |
| Edit | Edit items | Edit / Export |
| Delete | Delete items | Delete / Export |

**Process configuration**

`View or Delete`: With only View or Delete checked, users can view all items; the main page has no buttons. The detail page only shows Export and Cancel.
`Create or Edit`: With Create or Edit checked, the main page still has no buttons/actions because all items are auto-generated from templates. Users can open each process item’s detail page and import or enter users, groups, or roles, then save any changes. Selecting Create or Edit auto-checks View; if View is unchecked, Create is automatically removed.

### Permissions (Employee Portal — Admin View)

Add a `Requisition` module under `Permissions for employee portal – admin view`. For the admin view of Requisition requests and report:
- Only `View` is required to allow admins to view all requests and report within their data scope.
- `Edit`, `Create`, `Delete` are not required for Employee Portal admin view for request overview.
- Data access is governed by the role scope; HR can only view requests for entities/offices in their scope configured in the role settings.

## Requisition — Templates 
------------------------------------
Default list view
- **Default Columns:** Template Name | Apply to | Status | Created by (time) | Modified by (time)
- **Search:** Search by template name
- **Default order:** Modified time (newest → oldest)
- **Filters:** Created by, Status
- View permission shows all templates across legal entities/offices; Configuration portal scope control is not enforced.

### Create Template
- Users with `Create` permission click `Create` to open the template creation form.

**Basic information (Required)**
The Basic Information section collects core metadata for a template. `Name` and `Apply to` are required; `Description` is optional. These fields appear at the top of the template creation form and determine the template scope and identity.
- `Name` (Required)
- `Description` (Optional)
- `Apply to` (Required) — select one or more legal entities and offices. All entities/offices are selectable; no role scope filter is applied in the Configuration portal.

**Form Designer**
- `Manage fields`: configure which fields appear on the application form and the order they will display.
- Default fields typically include: Legal entity, Office, Department, Title, Number of headcounts, Employee type, Full/part-time, Manager name, Target start date, Target end date. `Legal entity`, `Office`, and `Department` are non-removable (no delete button) and cannot be reordered (order is fixed).
- `Add fields`: use `+ Add` to include additional fields from Requisition > Field Library. The picker only lists fields not yet added; already-added fields are omitted from the options.
- `Sort fields`: every added field has a sort control. The control is a dropdown with positions numbered 1 through the total field count; changing the number adjusts the field's order. The visual order on the page updates after save (not immediately); on reload the fields render from smallest to largest sort number.
- `Remove fields`: remove a field via the `Remove` button (confirmation required).
- `Attachments`: enable via `Allow to add attachments` checkbox.

**Approval Workflow Levels**
- Add workflow levels and configure each level's name
- `Manage fields`: add/sort/remove fields shown in this level's form; behavior matches the Form Designer (Add, Sort, Remove logic)，including whether attachments are enabled.
- `Action type`: choices are `Approve and reject` (default) or `Assign task`.
  - `Approve and reject`: requires a Reviewer (People field) to approve.
  - `Assign task`: the flow assigns tasks to assignees who must complete them in the system. The whole workflow advances to the next level only after task completion. 
- `Allow to modify above fields in form`: when checked, the current level's reviewer or operator can edit the application form and any fields added in earlier workflow levels.

- Workflow execution rules:
  - All levels execute in order; the next level starts only after the current level finishes.
  - If a level is `Approve and reject`: an approval moves the request to the next level; a rejection stops the workflow and ends the request. If the Reviewer is a multi-person role or group, any one member approving/rejecting completes the level.
  - If a level is `Assign task`: the Operator assigns tasks; once an assignee completes the task, the workflow advances to the next level. If the assignee is a multi-person role or group, completion by any one member advances the level.
  - Each level’s reviewer and operator must be looked up and assigned from the generated process configuration profile.

**Template actions and lifecycle**
- `Save draft`: saves template as Draft.
- `Save`: validates and activates the template. If no workflow levels exist, show validation error and prevent save. once saved, the system generates a process configuration item based on the template, which is used to drive the requisition workflow. The template status changes to Active after saving.
- `Cancel`: exit without saving.
- `Preview form`: open a preview panel to see the designed form.
- `Edit`: enter edit mode via `Edit` button, hover action, or `Edit` on the template view page. Don't allow editing templates that have incomplete requests.
- `Deactivate` / `Activate`: toggles template active state (edit permission required). Don't allow deactivating templates that have incomplete requests.
- `Delete`: allowed for inactive/draft templates (delete permission required). Active templates show `Delete` disabled.
- `Copy`: users with `Create` may copy a template; copy opens a create form with all fields pre-filled except `Name` and `Apply to`. Templates from any location can be copied because scope control is disabled.
- `View`: template view page is read-only and includes `Preview form`.

**Default template**

Provided before go-live based on AvePoint usage scenarios; system delivers one default template. The default template cannot be edited or deleted, but it can be copied to create new templates. The default template includes the following configuration:
- Form designer:
  - Name: TBD
  - Apply to: Global legal entities except China/Vietnam.
  - Fields: 1) Legal entity 2) Office 3) Department 4) Title 5) Number of headcount 6) Employee type 7) Full/Part time 8) Manager name 9) Target start date 10) Target end date.
  - Attachment: Enabled.
- Workflow levels:
  - Level 1: Name TBD; Fields 1) Job code 2) Other salary rate 3) Other salary Frequency 4) Monthly car allowance 5) Budget hire date; Attachment enabled; Action type Approve and reject; Modify field Enabled.
  - Level 2: Name TBD; Action type Approve and reject; Modify field Disabled.
  - Level 3: Name TBD; Action type Approve and reject; Modify field Disabled.
  - Level 4: Name TBD; Attachment enabled; Action type Assign task; Modify field Disabled.

## Requisition — Field Library 
---------------------------------------
Purpose: central management for all fields used in requisition forms.

- **Default columns:** Name | Type | Required | Description |Category
- **Search:** Search by name
- **Ordering:** Category (System → Built-in → Customized)
- **Total items display and selection counts**

**Field categories**
- `System` — shared fields from Configuration → Employee → All fields (e.g., Legal entity, Office).
- `Built-in` — system-provided fields required by the feature (e.g., Target start date, Job code).
- `Customized` — user-created fields.

**Example system & built-in fields**
| Category | Field | Type |
| --- | --- | --- |
| System | Legal entity | Choice |
| System | Office | Choice |
| System | Department | Choice |
| System | Job level | Choice |
| System | Band | Single Text |
| System | Functional Entity | Choice |
| System | Employee Type | Choice |
| System | Full/Part Time | Choice |
| Built-in | Job Code | Choice |
| Built-in | Manager Name | People or group |
| Built-in | Manager Email | Single Text |
| Built-in | Target Start Date | Date |
| Built-in | Target End Date | Date |
| Built-in | Other Salary Rate | Currency |
| Built-in | Other Salary- Frequency | Choice |
| Built-in | Monthly Car Allowance | Currency |
| Built-in | Budget Hire Date | Date |
| Built-in | Title | Single Text |

**Field actions**
- `Add`: open a creation panel (Create permission required)，and users should select category first.  `Category` field is a dropdown and required. Two categories are supported:
  - `System`: use `Choose fields from all fields` to pick existing fields from the All fields module and import them into Field Library.
  - `Custom`: select a field type, enter a Field name, and create new fields. Other settings align with All fields except the following are hidden: `Hide in employee portal - My profile` and `Hide in employee portal - Team profile`. Field scope (`Applied to`) is not supported when creating custom fields here.

- `Edit`: open an edit panel for the selected customized/built-in field (Edit permission required). You can change metadata (name, required flag, options) and save; field type is locked after creation.
- `Delete`: delete custom fields (Delete permission required). Supports multi-select batch delete with confirmation. System and built-in fields keep `Delete` disabled; fields referenced by templates cannot be deleted and should show a notification instead.
- `View`: users with only View permission can open a read-only panel to see field details. system field cannot be edited or deleted; built-in fields cannot be deleted and have limited editability.

## Requisition — Job hierarchy

The Job hierarchy module allows configuration of job codes and compensation structures.

- **Default columns:** Job code | Legal entity | Currency | Salary Range  | Bonus OTC | Target Equity(USD) | Job level | Band | Notes
- **Search:** fuzzy search by job code
- **Filter:** Legal entity | Job level | Band
- **Total items:** displays the count of all ranges and updates when selections are made
- **Ordering:** by the first letter of the job code (alphabetical)
- Actions: 
  - `Add` (Create permission): opens a panel to create a new item. Users fill required fields and save, after that the new item is added to the list. Don't allow duplicate job code + legal entity combinations; show validation error if the entered combination already exists. 
  - `Edit` (Edit permission): opens a similar panel pre-filled with the selected item's values for modification.
  - `Import` (Create permission): allows bulk creation by uploading a template spreadsheet. A panel presents download/upload instructions.
  - `Copy` (Create permission): users can copy any visible item. Select an item and click Copy to open a create page pre-filled with the source item's values. Require at least one field change before save; prevent creating a completely identical duplicate.
  - `Export` (View permission): immediately downloads all items in the current view as a Excel file for offline analysis. The exported file includes all columns and reflects current filters/search (e.g., if filtered by a specific legal entity, only items for that entity are exported). The file name should include the export date and filter context (e.g., `Job_hierarchy_export_2026-01-01_LE-001.xlsx`).
  - `Delete` (Delete permission): removes selected items; if multiple rows are selected the system asks for confirmation before deletion. The button is enabled only for users with the delete right.

**Job hierarchy item details**
- Fields: Job code (required), Legal entity (required), Based on (Job level or Band), Currency (required), Base salary range min (required), Base salary range max (required), Bonus OTC (optional), Target Equity (USD) (optional), Notes (optional).
  - `Job code` (required) — dropdown sourced from the Job code registry. Show only active codes; if none exist, display a message prompting creation in the registry. Allow adding a new job code directly from the dropdown. Prevent duplicates of job code + legal entity combinations. Job code column is a main column in the list view.
  - `Legal entity` (required) — a choice field tied to the All fields → Legal entity list. All entities are selectable; no role scope filter is applied in the Configuration portal.
  - `Based on` (required) — a radio button selection that determines whether the salary range is based on `Job level` or `Band`. 
	  - If `Job level` is selected, a dropdown appears allowing the user to select one or more job levels from the All fields → Job level list. should sync any updates for the options with the job level field in the All fields; show validation error if no job level is selected when `Job level` is chosen.
	  - If `Band` is selected, a free-text input appears where the user can type in the relevant band information. show validation error if the input is left blank when `Band` is chosen.
  - `Currency` (required) — pulls available currency options from the Annual salary field configuration. Salary range and Bonus OTC share the same currency selection; Target Equity is always in USD. Any add/edit/delete to available currency options syncs here immediately.
  - `Bonus OTC` (optional) — number field; only allow positive integers; support currency separators with commas every three digits (e.g., 12,345).
  - `Target Equity (USD)` (optional) — number field; only allow positive integers; currency fixed to USD; support currency separators with commas every three digits (e.g., 12,345).
  - `Base salary range min` (required, numeric) — lower bound of the salary range.should be the interger number bigger than 1000; show validation error if the value is not a number or less than 1000; support currency separators with commas every three digits (e.g., 12,345).
  - `Base salary range max` (required, numeric) — upper bound of the salary range. should be the interger number bigger than 1000; show validation error if the value is not a number or less than 1000. Additionally, it must be greater than or equal to the min value; show validation error if max is less than min; support currency separators with commas every three digits (e.g., 12,345).
  - `Description` (optional) — a free-text field for additional context about the salary range.
  
**Import items**

Users with create permission can import multiple job hierarchy items at once via an Excel template. The template includes all necessary fields with instructions. 
Before importing, users should select the conflict resolution method for duplicate job code + legal entity combinations: either `Skip` (default) or `Overwrite`.
- If `Skip` is selected, the system ignores any row with a duplicate combination and retains the existing item; the import results report marks these rows as "Skipped".
- If `Overwrite` is selected, the system updates existing items with the new values from the import file when a duplicate combination is found; the import results report marks these rows as "Overwritten".

**Template instructions**

The template name should be `Job hierarchy import template.xlsx`. The columns and formatting requirements are as follows:

| Job code (Required) | Legal entity (Required) | Currency (Required) | Salary range (Required) | Bonus OTC (Optional) | Target Equity (USD) (Optional) | Job level (Optional) | Band (Optional) | Notes (Optional) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Value required; dropdown list; options must match the GUI | Value required; dropdown list; options must match the GUI | Value required; dropdown list; options must match the GUI | Value required; integer; support currency separators with commas | Optional; integer; support currency separators with commas | Optional; integer; support currency separators with commas | Dropdown list; options must match the GUI; enforce that either Job level or Band has a value | Text; enforce that either Job level or Band has a value | Multi-line text |

Add a `Result` column in the import results to indicate whether each row was "Created", "Overwritten", "Skipped", or "Failed" (with error details).

**Form application logic**
- When designing a form, adding the Job code field automatically injects related fields into the request form: Currency, Salary range, Bonus OTC, Target Equity (USD), Job level, and Band.
- When users select a Job code, the system auto-fills predefined values for those related fields based on the Job hierarchy entry.
- Users do not need to create or add these related fields to Field Library.

## Requisition — Process configuration

Purpose: centrally configure each template’s workflow reviewers and operators.

- Profiles are auto-generated from templates; the main list has no action buttons. Configure each profile individually.
- Profile name matches the template name. Clicking the name opens the detail configuration page. All fields in this page are read-only except for the reviewer/operator assignment cells.
- Detail page layout:
  - First column lists all the template’s scope values (Legal entity or Office) exactly as selected in the template. each row represents one scope value (e.g., if a template applies to 2 legal entities and 3 offices, the list will show 5 rows: 2 for legal entities and 3 for offices).
  - Additional columns are generated per workflow level; the column header is the workflow level name (e.g., "Level 1 Reviewer", "Level 2 Operator").
  - Adding a Cc column automatically for each level to allow configuration of users who will receive email notifications but are not reviewers or operators.
  - Each cell is a People or group field that allows one user, group, or role. Each cell can only be added with one active assignee; multiple selections are not allowed. When the existing assignee becomes inactive, the cell should show an inline warning. Changes are saved immediately when a new assignee is selected.Each cell cannot be left blank; if a user tries to remove an assignee without adding a new one, show a validation error and prevent saving until a new active assignee is added.
  - Assignees in this configuration will be the default reviewers and operators for any request created with the associated template.
  - Support importing ane exporting of the configuration in detail page:
    If users with create or edit permission open the detail page, they can see the `Import/Export` buttons at the top right corner. 
    - Exporting generates a file with all current configurations for that profile;
    - Importing allows bulk updates by uploading a file in the same format. The - - system will compare each value in template to GUI, if some users are      different, it will overwrite them in the profile if current users are not in pending requests. If there are pending requests with users in the profile, the system will only overwrite users who are not in pending requests and show a warning message listing the users that cannot be overwritten due to pending requests.

  Detail page import/export template format:

  | Legal entity (Required) | Office | Specific column name | Specific column name | Specific column name | Specific column name | Cc |
  | --- | --- | --- | --- | --- | --- | --- |
  | Each line has one legal entity and the same format as GUI by default; import fails if this cell is null. | List the same value as GUI by default, e.g.Changchun or All; import fails if this cell is null. | Value required; employee ID, role, group input are supported. | Value required; employee ID, role, group input are supported. | Value required; employee ID, role, group input are supported. | Value required; employee ID, role, group input are supported. | Value required; email address, role, group input are supported. |
  

Notes:
Reviewer and Operator fields are People-type: support users, roles, groups, and people-profile fields (e.g., HRBP, Manager).


## Requisition — Audit Report

This document defines the structure of the audit report that records every user action performed within the Requisition configuration features of the HRIS Configuration Portal. It is intended to aid compliance, troubleshooting, and change tracking.

### Audit Report Table

The basic table structure used in exports:

| Data Source | Module | Event | Object | Details |
| --- | --- | --- | --- | --- |



### Column Definitions

| Column | Description |
| --- | --- |
| **Data Source** | Fixed value **Configuration portal**. Indicates the origin of the logged event. |
| **Module** | Specifies the module and, when applicable, the sub‑tab where the action occurred (for example, `Requisition-Templates`, `Requisition-Field Library`, `Requisition-Job hierarchy – Job Code`, `Requisition-Job hierarchy – Salary Range/Bonus OTC/Target equity`). |
| **Event** | The user action or button clicked. Typical values include `Create`, `Edit`, `Delete`, `Import`, `Export`, `Activate`, `Deactivate`, `Copy`, `Enable`, `Disable`, `Add`. The verb should match the UI element text. |
| **Object** | - For create or delete events: the identifying fields of the item (e.g., template name, field name, job code value, salary range description).<br>- For edit events: the object being modified such as a template code or the specific form item containing the changed field(s). |
| **Details** | - A link only for Edit events. The linked detail lists every field changed in that single edit; one audit record covers all fields changed in one save. <br> - Format example: `Field1: oldValue → newValue; Field2: oldValue → newValue`. <br> - For creates/deletes(general logic): no detail link. <br> - A special case for Job hierarchy Add events: should add detail link for this Add event, and the Details should show all fields of that new entry for full visibility of what was created.|

More information about the **Details** column: For `Edit` events, the Details column should contain a link to a detailed change log that lists each modified field along with its previous and new values. 

**Previous Value** : list each modified field along with its value before the change (`Field1: oldValue; Field2: oldValue`)

**Current Value** : list each modified field with its updated value in the same format as Previous Value.



### Actions and Buttons by Module

The table below lists the primary actions/buttons that generate audit entries. Each row represents a single operation and shows which object is affected.

| Data Source| Module| Event| Object |Details |
| --- | --- | --- | --- | --- |
| Configuration portal | Requisition-Templates | Create template | New specific template name |  |
| Configuration portal | Requisition-Templates | Edit template |Specific template name |Detail link|
| Configuration portal | Requisition-Templates | Delete template | Specific template name |  |
| Configuration portal | Requisition-Templates | Activate template | Specific template name |  |
| Configuration portal | Requisition-Templates | Deactivate template | Specific template name |  |
| Configuration portal | Requisition-Templates | Copy template | New specific template name |  |
| Configuration portal | Requisition-Field Library | Create field | New specific field name |  |
| Configuration portal | Requisition-Field Library | Edit field | Specific field name | Detail link |
| Configuration portal | Requisition-Field Library | Delete field | Specific field name(s) |  |
| Configuration portal | Requisition-Field Library | Enable field | Specific field name |  |
| Configuration portal | Requisition-Field Library | Disable field | Specific field name |  |
| Configuration portal | Requisition-Job hierarchy | Add | Specific job code value | Details |
| Configuration portal | Requisition-Job hierarchy | Edit | Specific job code value | Detail link |
| Configuration portal | Requisition-Job hierarchy | Delete | Specific job code value(s) |  |
| Configuration portal | Requisition-Job hierarchy | Import | template name | Log one Import row, then create an Add record for each new job code in the file (one row per code). |
| Configuration portal | Requisition-Job hierarchy | Export | N/A (or report scope) | Detail link |
| Configuration portal | Requisition-Job hierarchy | Copy | New specific job code value | Details |
| Configuration portal | Requisition-Process configuration | Generate | Specific profile name |  | 
| Configuration portal | Requisition-Process configuration | Edit | Specific profile name | Detail link | 
| Configuration portal | Requisition-Process configuration | Import | Specific profile name | Detail link | 
| Configuration portal | Requisition-Process configuration | Export | Specific profile name |  | 

### Detail link content: 
- Clicking a Detail link opens a separate page with three columns: `Fields`, `Previous value`, and `Current value`.
- Each row lists one modified field with its before/after values.
- One detail page corresponds to one audit record; include all fields changed in that save.

Example Detail page for a Job hierarchy edit:

| Fields | Previous value | Current value |
| --- | --- | --- |
| Job code | JC-101 | JC-102 |
| Legal entity | LE-001 | LE-002 |
| Salary min value | 50000 | 55000 |
| Salary max value | 70000 | 75000 |


Example Detail page for a process configuration edit or import:

| Fields | Previous value | Current value |
| --- | --- | --- |
| Column name 1 | User1 | User2 |
| Column name 2 | User3 | User4 |
