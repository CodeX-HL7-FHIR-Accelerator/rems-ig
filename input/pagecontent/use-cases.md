### REMS Participants and Roles
Risk Evaluation and Mitigation Strategies (REMS) programs require providers, patients and others to perform certain actions to ensure the safe use of certain high-risk medications. Each of these medications has a REMS Administrator that makes sure these steps are being taken and reports back to the US Food and Drug Administration (FDA). 

#### Human Roles
Below is an outline of those who play a role in the parts of the REMS process currently covered by this guide:
- The **REMS Administrator** is an organization that establishes a program for maintaining safe use of a particular drug with the FDA, and then ensures that patients, prescribers, and others involved in fulfilling the prescription follow REMS program steps. The Administrator:
  - trains and certifies providers to prescribe or dispense the medication
  - shares educational material that providers must share with the patient
  - enrolls the patient into the program, with their agreement to follow safe use guidelines
  - obtains status information and clinical evidence demonstrating that participants are meeting program requirements
  - reports program participation and outcomes to the FDA.
- The **prescriber** or their agent (e.g., an authorized nurse or other party): 
  - typically receives information about risks associated with the medication from its manufacturer
  - may need to complete training and certification steps with the REMS Administrator to be authorized to prescribe the drug
  - may also need to send the Administrator patient clinical information or assessments at the time of ordering or at later points in the patient's treatment.

  _Note: The guide uses "prescriber" throughout to represent the ordering provider or other authorized party acting on their behalf._
- The **patient:** 
  - receives information or counseling about the risks associated with the medication, actions they need to take to mitigate those risks, symptoms to watch for and report to their provider, etc. The patient may need to formally acknowledge the risks before starting on the medication.

After the prescription is ordered, others participate in REMS steps to fulfill the medication safely. These participants and steps are not addressed in this version of the guide.
- The pharmacist will confirm that enrollment and prescribing steps were completed, and may need to provide education or counseling to the patient, before dispensing the medication.
- A drug distributor may need to follow REMS guidelines when providing the medication to the dispensing pharmacy.
- The patient's insurance company will confirm that REMS steps have been completed before paying for the treatment.

#### System Roles
- The **REMS Administrator** system: 
  - transmits program guidance and requirements to provider systems
  - obtains patient and provider statements and other patient information from prescriber EHRs and using SMART on FHIR apps
  - interacts with other REMS participant systems at pharmacies, drug distributors and payers during activities not currently covered by this guide
- The prescriber's **EHR**:
  - systematically notifies the REMS Administrator and supplies REMS-related information during treatment...
    - prior to the initial medication order
    - after the initial order. Depending on the program, this can include information at the time of re-ordering the drug or periodic treatment information.
- A data exchange **intermediary** may participate in the process on behalf of one or more REMS Administrators. The intermediary may:
  - act as a CDS Server fielding CDS requests related to one or more medications
  - host a SMART app supporting one or more medications
  - perform additional services outside the scope of this guide.

### Use Case Overview
This guide supports a set of scenarios involving interaction between prescribers, patients and REMS Administrators. The list below outlines participants' needs, and later sections further describe the related process scenarios.

* **Prescribers** need to:
  * Certify to prescribe a medication covered by a REMS program
  * Enroll a patient in a REMS program so that they can be given the medication
  * Attest to REMS education requirements 
  * Provide periodic patient updates to the REMS administrator 

* **Patients** need to: 
  * Provide or confirm enrollment information
  * Receive relevant documentation 
  * _[Provide updates (discuss)]_ 

* **REMS Administrators** need to: 
  * Supply program certification, education and enrollment materials to prescribers and patients
  * Obtain information and statements from the prescriber and patient during patient enrollment--prior to starting use of the medication
  * Obtain treatment information and assessments from the prescriber _[and patient (discuss)]_ while the patient is taking the medication
  * _[Alert prescribers about patient treatment, risks or missed REMS requirements while the patient is taking the medication (discuss)]_  

_[We haven't discussed the bullet below for a while... but should]:_

- **Healthcare institutions _[prescribers]_** need an easy means to validate that the pharmacy a medication request is being sent to is enrolled in the REMS program _[and/or is able to dispense the medication?]_ prior to sending the prescription 

This implementation guide defines FHIR-based approaches that participants in the REMS based workflows described above can use to interact with each other to streamline REMS process.  

_Note regarding existing prescription processes and standards:_ This IG does not redefine the means for transmitting a medication request from the provider to a pharmacy. This activity is currently handled by existing standards and functionality. This guide is focused on the areas that are not handled by or have had inadequate adoption of existing standards. 

### Prescriber and Patient-Focused System Scenarios
As introduced above, prescribers and patients need a way to more easily interact with the REMS Administrator during treatment. The following scenarios describe how those needs fit into patient care events.

Note that not all REMS programs have the same requirements. For instance, some programs don't require patient enrollment and are more geared toward providing educational information to the provider and patient. Others require prescriber training and certification. 

As such, certain aspects below will apply in all cases--for example the initiating step where the prescriber EHR requests program information and unmet requirements from the REMS Administrator. 

Other aspects may or may not apply for a particular circumstance; for example,
- the applicable REMS program may not require patient enrollment
- or a prescriber may have previously completed their training and certification for the program.

Lastly, these scenarios share the same context: a patient care activity during which the EHR connects with the REMS Administrator to notify it of a care event and request program information or requirements.

#### Scenario One: Share Program Information or Requirements Prior to Ordering
During the course of evaluating a patient, the prescriber decides to prescribe a medication that has a REMS program. At an appropriate point in the process (e.g., when the prescriber starts creating the medication request within their EHR, at the start of a related encounter, etc.), it queries the REMS Administration system using CDS Hooks to learn of any unmet program requirements, for example provider certification or patient enrollment.

The REMS Administrator responds in one or more of the following ways:
- No further program information or requirements need to be shared
- The prescriber must complete REMS program certification before prescribing the medication
- The patient must enroll in a REMS program before starting on the medication
- Additional program information is available
- No REMS program applies to the event

##### Prescriber Certification
If the REMS Administrator determines that the prescriber is not currently registered for the REMS program, its CDS Hooks response contains a notification and links to training material and a SMART on FHIR application or other means by which certification can be completed. 

The EHR presents the alert notifying the provider that they will need to certify in the REMS program before the order can be dispensed, along with the supplied information and app links.

If a SMART app link is returned, the prescriber or staff launches returned app from the notification, with data pre-filled from the EHR where available in the prescriber's record. The prescriber reviews the pre-filled data, makes any adjustments required as well as filling in any gaps in the forms and submits the certification information to the REMS Administrator.


##### Patient Enrollment
If the REMS Administrator determines that the patient must enroll in the REMS program, its CDS Hooks response contains a notification and links to patient material and a SMART on FHIR application or other means by which enrollment can be completed. 

The EHR presents the alert notifying the provider that the patient must enroll in the REMS program before they can start the medication, along with the supplied information and app links.

If a SMART app link is returned, the prescriber or staff launches returned app from the notification, with data pre-filled from the EHR where available in the patient's record. The patient reviews the pre-filled data, makes any adjustments required as well as filling in any gaps in the forms and submits the data back to the REMS Administrator to enroll on the REMS program.

##### Additional Program Information Is Available
The REMS Administrator includes links to any additional prescriber or patient-focused materials in its CDS Hooks response, which the EHR presents to the prescriber.

##### REMS Doesn't Apply or No Information Needed
If the REMS Administrator determines that the medication being considered does not have a REMS program, or if all program requirements have been satisfied by the prescriber and patient, it returns a CDS Hooks response indicating that. 

In response, the EHR does not present any alerts or information to the prescriber.

<br/><br/>

#### Scenario Two: Share Information During Patient Treatment  
Upon care events in the patient's treatment, for example when scheduling a follow-up appointment, at the start of an encounter, or at a periodic monitoring interval, the EHR notes that the patient is on a REMS medication and calls the REMS administration system.

The REMS Administrator evaluates the particular event or timing for periodic patient updates and determines whether there is a need for the patient's data to be re-evaluated and responds in one or more of the following ways:
 
- Additional or updated patient clinical information is required, or the prescriber must provide a clinical assessment or status statement
- No additional information is needed

##### Additional Clinical or Status Information is Required
If the REMS Administrator requires patient clinical information or other assessments from the provider, the EHR presents the alert notifying the provider of the needed information.

If a SMART app link is returned, the prescriber or staff launches returned app from the notification, with data pre-filled from the EHR where available in the patient's record. The prescriber reviews, adds any additional information and submits the data back to the REMS Administrator.


##### REMS Doesn't Apply or No Information Needed
If the REMS Administrator determines that the medication being considered does not have a REMS program, or if all program requirements have been satisfied by the prescriber and patient, it returns a CDS Hooks response indicating that. 

In response, the EHR does not present any alerts or information to the prescriber.

<br/><br/>

#### Other Patient-Focused Scenarios
###### Patient Enrollment Via EHR Portal
A provider prescribes a medication that has a REMS for a patient and fills out the required patient enrollment forms. The patient receives a notification to attest that they have received any required education/documentation required by the REMS as well as the accuracy of the information contained on the forms. 

The patient logs into their providers patient portal and launches an application to fill out the required information and submits the results back to the REMS Administration system.

<br/><br/> 

#### HCS/Healthcare institutions
##### Validate Pharmacy is Registered 
A provider prescribes a medication that has a REMS to a patient. The provider asks the patient what their preferred pharmacy is.  Before sending the prescription to the pharmacy, the ERM queries the REMS Administration systems to determine if the pharmacy is enrolled on the REMS if required and can dispense the medication.  The REMS Administration system sends back a notification stating if the pharmacy can dispense the medication.  An alert is displayed in the uI of the EMR if the pharmacy is incapable of dispensing the medication. 

<br/><br/>

_[Frank left off here on Aug 11]_

<br/><br/>

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



