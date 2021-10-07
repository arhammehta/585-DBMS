ER diagram


Process Flow:


* An employee working at a company has a unique employee_id of the building at which he/she is working. 
* An employee voluntarily does a symptom check using the Symptoms table. If the symptoms exist then the employee has to take a test. Either, the employee takes the test at the on-site or off-site
* The employee is asked to take a test randomly, on some positive symptoms or post coming in contact with an affected person
* Following the test result if it is positive then the employee becomes a patient. The employee can take the test onsite or offsite. We track this using the is_onsite attribute from the test_center
* Contact Tracing - When the employee becomes a patient, we back track it to the employee table and employee_meeting_bridge table to trace the people in contact 
   * If the patient is affected then the employee_is_affected and gets flagged. Then we track the employees in contact by going to the bridge. The meeting_id, meeting_start_time, meeting_end_time and room_no is used to identify the employees present in the meeting at that time
   * The people on the same floor, are tracked using the floor_no attribute and then alerts are sent by creating a trigger on the patient table to backtrack onto the patient table


Tables:


* Company: Table represents the Entity ‘Company’. As the building consists of various tenets, each company will have its unique identifier. 
   * Attributes:
      * company_id [Primary Key] : Stores the unique identifier to identify the company in the building
      * company_name : Stores the name of the company, and cannot be null
      * company_phone : Stores the contact number of the company, and cannot be null
      * company_email : Stores the email address of the company, and cannot be null
      * company_address : Stores the location where the company is situated in the building
   * Relationships:
      * Employee: Company can have 1 or many employees, and each employee will belong to one company. This relationship is a weak relationship since the primary key of Company entity is not a part of the primary key in the ‘Employee’ table


* Employee: Table represents the Entity ‘Company’. As the building consists of various tenets, each company will have its unique identifier. 
   * Assumptions : Employee_id is uniquely generated for all the employees in the building, no two employees can have the same id
   * Attributes:
      * employee_id [Primary Key]: Stores the unique identifier to identify the employee in the building
      * company_id [Foreign Key]: Stores the company id to which the employee works in and cannot be null
      * employee_name: Stores the name of the employee, and cannot be null
      * employee_phone: Stores the contact number of the employee, and cannot be null
      * employee_email: Stores the email address of the employee, and can be null
      * employee_dob: Stores the date of birth of the employee, and cannot be null
      * employee_address: Stores the address of the employee and cannot be null
      * employee_is_affected: Stores a boolean value, which becomes true when the employee was in contact with an affected employee or has built symptoms 
   * Relationships:
      * Company: Each employee works at one company. This relationship is a weak relationship since the primary key of Company entity is not a part of the primary key in the ‘Employee’ table
      * Symptoms: An employee can check the symptoms 0 or multiple times. This relationship is a strong relationship since the primary key of Employee entity is the primary key in the ‘Symptoms’ entity
      * Test: An employee can give the test 0 or many times. It includes the random screens, tests if the employee builds symptoms, when employee was in contact with an affected person or if the employee takes a test at an offsite center and uploads the result on the test portal. It is a weak relationship since the employee_id is not a part of the primary key in the test entity
      * Patient: An employee has a strong relationship with patient entity, that is defined as an employee may or may not be a patient whereas a patient is always an employee
      * Employee_meeting_bridge: Employee is connected to Employee_meeting_bridge in a strong relationship wherein 1 employee can have 0 or many meetings 


* Symptoms: Table represents a weak entity ‘Symptoms’. This keeps track of the symptoms recorded by the employees voluntarily. 
   * Attributes:
      * employee_id [Primary Key]: Stores the unique identifier to identify the employee recording the symptoms
      * symptom_1: Stores the boolean value of the employee having the symptom or not, and cannot be null
      * symptom_2: Stores the boolean value of the employee having the symptom or not, and cannot be null
      * symptom_3: Stores the boolean value of the employee having the symptom or not, and cannot be null
      * symptom_4: Stores the boolean value of the employee having the symptom or not, and cannot be null
      * symptom_5: Stores the boolean value of the employee having the symptom or not, and cannot be null
   * Relationships:
      * Employee: An employee checks the symptoms 0 or multiple times. This relationship is a strong relationship since the primary key of Employee entity is the primary key in the table


* Test_Center: Table represents entity ‘Test_center’. This table has the attributes of the test center conducting tests of employees 
   * Attributes:
      * test_center_id [Primary Key]: Stores the unique identifier to identify the test center
      * is_onsite: Stores the boolean value of the test center being present in the building or not, and cannot be null
      * location: Stores the location coordinates of the test center in the building, and cannot be null
   * Relationships:
      * Test: A test center conducts tests of the employees randomly, based on the symptoms or if the employee has been in contact with an affected person. This weak relationship joins the test center with the test entity


* Test: Table represents entity ‘Test’. This table has the attributes of the tests given by employees
   * Attributes:
      * test_id [Primary Key]: Stores the unique identifier to identify the test conducted for the employees 
      * test_center_id [Foreign Key]: Stores the test_center_id attribute where the test has been conducted and cannot be null
      * employee_id [Foreign Key]: Stores the employee-id attribute to identify the employee that has given the test and cannot be null
      * test_date: Stores the date value of the date on which the test was conducted and cannot be null
      * test_result: Stores the test result as a boolean value if the result is positive then the boolean value changes, and this attribute cannot be null
   * Relationships:
      * Patient: A test result when positive results into the employee turning into a patient. This relationship is a strong relationship as the test_id is used to uniquely identify the patient


* Patient: The table represents the weak entity Patient representing an employee that becomes a patient
   * Attributes:
      * employee_id [Primary Key,Foreign Key]: Stores the unique identifier to identify the employee in the building
      * test_id [Primary Key,Foreign Key]: Stores the unique identifier to identify the test conducted for the employees
      * patient_status: This attribute keeps a track of the status of the patient such as hospitalized, and well on a daily basis. This attribute cannot be a null value
      * patient_is_isolating: If the patient is self-isolating then this boolean value gets flagged and cannot be a null value
      * patient_is_alive: If the patient dies then this attribute gets flagged
      * date: This attribute gives the date at which the last status update was made by the patient
      * latest_temperature: Stores the latest temperature of the employee as an indicator to get on how the patient is doing
   * Relationships:
      * Test: A patient takes a test to get an update if the patient has recovered or not from the disease
      * Employee: An employee has a strong relationship with patient entity, that is defined as an employee may or may not be a patient whereas a patient is always an employee


* Employee_Meeting_Bridge: This table represents the weak entity between employee and meeting
   * Attributes:
      * employee_id [Primary Key,Foreign Key]: Stores the unique identifier to identify the employee in the building
      * meeting_id [Primary Key,Foreign Key]: Stores the unique identifier to identify the meeting in the building
   * Relationships:
      * Employee: An employee can attend 0 or many meetings forming a strong relationship
      * Meeting: A meeting can consists of 1 or more employees forming a strong relationship


* Meeting: The table represents the weak entity ‘meeting’ giving us a list of meetings held in the office with different meeting attributes
   * Attributes:
      * meeting_id[Primary Key]: Stores the unique identifier to identify the meeting in the building
      * floor_no: Consists of the floor in the building where the meeting is being held and cannot be null
      * room_no: Stores the room number where the meeting was conducted and cannot be null
      * meeting_date: Stores the date value of the meeting and cannot be null
      * meeting_start_time: Stores the start time of the meeting to identify the employees present in the meeting and cannot be null
      * meeting_end_time: Stores the end time of the meeting to identify the employees leaving the meeting and cannot be null
   * Relationships: 
      * Employee: A meeting can consists of 1 or more employees forming a strong relationship