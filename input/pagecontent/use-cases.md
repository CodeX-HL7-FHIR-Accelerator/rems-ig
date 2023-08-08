### Overview

#### Business Need
There are a number of entities that are involved the overall REMS ecosystem each with their own set of business needs/requirements while participating within the REMS program ecosystem. 

* Providers need to easily:
  * Register for a REMS
  * Register a Patient on a REMS 
  * Attest to REMS education requirements 
  * Provide periodic updates to REMS Administration system for Patient 

* Patients need to easily: 
  * Attest to being enrolled on a REMS 
  * Confirm registration information
  * Receive relevant documentation 
  * Provide updates 


* Pharmacies/Pharmacists need to: 
  * Validate REMS prescriptions 
  * request additional information from a Provider

* Distributors of medications that contain a REMS need an easy means to validate a Pharmacy is enrolled on a REMS prior to shipping the medication

* HCS/Healthcare institutions need an easy means to validate that the pharmacy a medication request is being sent to is enrolled on the REMS prior to sending the prescription 

This implementation guide defines a FHIR base approach that participants in the REMS based workflows described above can use to interact with each other to streamline REMS process.  Worth noting is that this IG does not redefine a FHIR based means to perform existing activities such as sending a medication request from provider to a pharmacy.  These activities are currently handled by existing standards and functionality.  This IG is focused on the areas that are not handled by or have had inadequate uptake through the use of existing standards. 

#### Provider and Patient Focused Scenarios
##### Providers

As mentioned above, providers need a means to easily interact with a REMS administration system to perform various tasks in the REMS ecosystems.  Note that not all REMS require all of the same requirements.  For instance, some REMS do not require enrollment and are more geared toward providing educational information to the provider and patient, while others require not only provider enrollment but specific training.  As there such differing levels of requirements the scenarios below are not representative of all REMS and are simply generic examples of such workflows. 


###### Provider Enrollment 
A provider while evaluating a patient, makes the determination to prescribe a medication with that has a REMS. As the provider begins to create the medication request within their EMR, it queries the REMS Administration system to determine if the provider and or patient is currently enrolled for the REMS.

Seeing that the provider is not currently registered for the REMS, the REMS Administration system returns a notification to the provider. An alert appears at the bottom of the EMR order entry screen notifying the provider that they will need to register for the REMS for the order to be successfully dispensed. 

The provider launches the included forms from the notification through either an embedded EMR functionality or a SMART on FHIR application with data pre-filled from the EMR. The provider reviews the pre-filled data, makes any adjustments required as well as filling in any gaps in the forms and submits the data back to the REMS Administration system to enroll on the REMS.

###### Provider Education Attestation  


###### Patient Enrollment 
A provider while evaluating a patient, makes the determination to prescribe a medication with that has a REMS. As the provider begins to create the medication request within their EMR, it queries the REMS Administration system to determine if the provider and or patient is currently enrolled for the REMS.

Seeing that the provider is currently registered for the REMS but the patient is not, the REMS Administration system returns a notification to the provider. An alert appears at the bottom of the EMR order entry screen notifying the provider that they will need to register the patient for the REMS for the order to be successfully dispensed. 

The provider launches the included forms from the notification through either an embedded EMR functionality or a SMART on FHIR application with data pre-filled from the EMR. The provider reviews the pre-filled data, makes any adjustments required as well as filling in any gaps in the forms and submits the data back to the REMS Administration system to enroll the patient on the REMS.

###### Patient Periodic Update 
A patient that is current on a REMS medication is scheduled for an follow up appointment at the provider. While consulting the patient record the EMR sees that the patient is on a medication that contains a REMS and queries the REMS administration system.  

The REMS administration system evaluates the timing for periodic patient updates and determines that there is a need for the patients data to be re-evaluated and returns a notification to the provider. An alert appears at the bottom of the EMR screen notifying the provider that they will need to re-evaluate the patient for future medication dispenses to be successful. 

The provider launches the included forms from the notification through either an embedded EMR functionality or a SMART on FHIR application with data pre-filled from the EMR. The provider reviews the pre-filled data, makes any adjustments required as well as filling in any gaps in the forms and submits the data back to the REMS Administration system to update the patients information in the REMS Administration system. 


##### Patient Focused Success Scenarios
###### Patient Enrollment 
A provider prescribes a medication that has a REMS for a patient and fills out the required patient enrollment forms. The patient receives a notification to attest that they have received any required education/documentation required by the REMS as well as the accuracy of the information contained on the forms. 

The patient logs into their providers patient portal and launches an application to fill out the required information and submits the results back to the REMS Administration system. 




#### Provider/Patient Workflow
The high-level workflow for CRD is envisioned to work as follows:

{::options parse_block_html="false" /}
<figure>
  <img height="300px" src="overview.png" alt="CRD Workflow diagram"/>
  <figcaption><b>    Figure 1: CRD Workflow</b></figcaption>
  <p></p>
</figure>
{::options parse_block_html="true" /}


**1. Clinical action (potentially) needed**<br/>
A healthcare provider decides that a clinical action is needed or wants to explore the coverage ramifications of taking a clinical action.  Possible clinical actions include:
* Admitting a patient or starting a patient visit
* Ordering a drug, device, procedure, etc.
* Choosing when or where an existing order will be performed
* Making a referral or scheduling a future appointment
* Discharging a patient

Based on whether the provider has decided to perform the action or just wishes to explore, they will proceed to 2a or 2b.

**2a. Provider performs EMR action**<br/>
The provider uses an EMR to initiate the clinical action from step #1, entering required information (e.g. a drug, a type of referral or appointment, etc.) into forms provided by the EMR.

**2b. Provider starts 'CRD what-if'**<br/>
The provider uses an EMR to launch a 'What if?' CRD SMART app to explore payer coverage requirements.  The provider indicates the type of action they're considering into the CRD SMART app which prompts for additional information relevant to coverage determination, such as the proposed drug, type of referral or appointment, etc.

**3. Provider checks Payer CRD needs**<br/>
The EMR or CRD SMART app contacts a CRD Server used by their patient's payer to find out what information is required to perform Coverage Requirements Discovery (CRD) - particularly whether the CRD Server requires protected health information (PHI) to evaluate the patient's coverage requirements, or whether the patient's coverage type and the proposed clinical action is enough.  Optionally, the CRD service might provide the EMR with information about configuration options, such as the option to control the types of coverage requirements returned to the user or the number of requirements returned.

Note:
* Different patients will have coverage from different payers and different payers may use different CRD Servers.
* Payer server requirements are expected to be static. The EMR or CRD SMART app may choose to cache information received.
* Modular EMR systems may need to retrieve the coverage type or other information required by the CRD Server from other systems within the provider's environment.

**4. System starts CRD query**<br/>
The EMR (in the background as the provider is typing) or the CRD SMART app (once enough information has been provided) initiates a query to the CRD Server providing the patient's coverage type and/or identity along with information about the proposed clinical action.  The EMR might also provide the CRD Server with one or more of the following:
* a 'token' to allow the CRD Server to temporarily and securely request additional patient information from the EMR in step #5.
* configuration information that indicates the type of information the EMR user is interested in receiving (e.g. whether prior authorization or clinical documentation is required, or products covered or recommended by the plan.

Note:
* Configuration options - received in step #3 - might be managed by the EMR and information provided could be specific to the context of the request, a user role, or an individual user.

**5. (Optional) Payer service gets additional data**<br/>
If additional information is needed to process the query, the CRD Server may use the EMR's secure API, with the temporary access token provided in step #4, to request additional information from the patient's record.  Examples include requests for information needed to assess whether the action is needed (e.g. an allergy to a first line medication, lab result), whether recommended next steps are in place (e.g. follow-up visits scheduled, lab tests ordered to monitor effectiveness/safety), etc.  The CRD Server might submit multiple queries for different types of data to determine coverage requirements.

Note:
* By requesting additional information directly from the EMR, a CRD Server can determine what documentation already exists and what requirements already exist, using that information to make the most accurate assessment possible before providing cards to the user that suggest additional documentation is necessary or prior authorization needs to be requested.  CRD Servers should always attempt to gather what information they can automatically before providing responses that might require human action, such as completing a Questionnaire or launching DTR.

**6. Payer service returns CRD results**<br/>
Based on the information provided/retrieved, the payer system returns guidance to the provider.  The guidance can be in several forms:
* A simple message indicating that service is covered without additional requirements
* A message describing what coverage requirements exist
* A link to external documentation describing coverage requirements to help inform/educate providers (not as a substitute for electronic prior authorization)
* Links to specific forms or templates that need to be completed
* A link to open a SMART application that allows the provider to provide needed information or additional detail to help guide coverage requirements discovery
* Links with recommendations to substitute the planned action with a different action and/or to add additional actions (e.g. proposals to replace a proposed drug to a required first-line treatment or a drug covered by the patient's plan, to add a concurrent medication, additional diagnostic tests, etc.)

Payer requirements might include the need for prior authorization, forms that must be completed, medical documentation that must exist or be provided, recommendations on alternative therapies, etc.

**7. Provider invokes links**<br/>
If the response includes links to additional information or apps, the provider can direct the EMR to interact further with the payer system by retrieving the linked-to information or launching the provided application.


#### Pharmacy Success Scenarios
##### Validate REMS Prescription
A pharmacy receives a prescription from a provider for a medication that has a REMS. Using the information in the prescription for the provider and patient, the pharmacy system queries the REMS Administration system to determine if the request can be dispensed. 

The REMS administration system receiving the request returns a notification stating whether or not the requirements for the REMS have been met by the provider and patient. If the requirements have been met the pharmacy dispenses the medication, if not , the pharmacy contacts the provider to let them know they cannot dispense the medication. 

#### Distributor Success Scenarios
##### Validate Pharmacy is Registered 
A distributor receives an order from a pharmacy for a medication that contains a REMS.  The distributor sends a query to the REMS administration system to determine if the pharmacy is enrolled on the REMS program and is capable of receiving the medication.

#### HCS/Healthcare institutions
##### Validate Pharmacy is Registered 
A provider prescribes a medication that has a REMS to a patient. The provider asks the patient what their preferred pharmacy is.  Before sending the prescription to the pharmacy, the ERM queries the REMS Administration systems to determine if the pharmacy is enrolled on the REMS if required and can dispense the medication.  The REMS Administration system sends back a notification stating if the pharmacy can dispense the medication.  An alert is displayed in the uI of the EMR if the pharmacy is incapable of dispensing the medication. 


