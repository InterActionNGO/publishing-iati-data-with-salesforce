<p>The documentation and example code files are provided here as a resource for other organizations, particularly NGOs, who have published or are considering publishing their data according to the <a href="http://iatistandard.org/" target="_blank">IATI standard</a>.&nbsp; The code provided is non-abstract and will require modifcation of certain particulars for use in another organization&#39;s context.&nbsp; Nonetheless, the workflow and example code files here may still be valuable to other non-profits using Salesforce who want to publish their data in IATI format.&nbsp; Furthermore, this rudimentary code may now be improved and expanded upon by the wider community.</p>

<h1>Salesforce Setup</h1>

<ol>
	<li>
	<p style="background: rgb(238, 238, 238) none repeat scroll 0% 0%; border: 1px solid rgb(204, 204, 204); padding: 5px 10px;">Standard Objects</p>
	</li>
	<li>
	<p style="background: rgb(238, 238, 238) none repeat scroll 0% 0%; border: 1px solid rgb(204, 204, 204); padding: 5px 10px;">Custom Objects</p>
	</li>
	<li>
	<p style="background: rgb(238, 238, 238) none repeat scroll 0% 0%; border: 1px solid rgb(204, 204, 204); padding: 5px 10px;">Apex Class (custom controller)</p>
	</li>
	<li>
	<p style="background: rgb(238, 238, 238) none repeat scroll 0% 0%; border: 1px solid rgb(204, 204, 204); padding: 5px 10px;">Apex Test Class</p>
	</li>
	<li>
	<p style="background: rgb(238, 238, 238) none repeat scroll 0% 0%; border: 1px solid rgb(204, 204, 204); padding: 5px 10px;">VisualForce Page</p>
	</li>
</ol>

<h2>Standard Objects</h2>

<p>We tried to utilize as much native functionality as possible -- standard objects, standard fields, and built-in relationships.&nbsp; This goal was balance by the fact that standard objects are used across the organization and any usage and customization of those objects had to be mindful of other user groups.</p>

<ul>
	<li><strong>Accounts (i.e. Organizations)</strong> - Funders, partners of varying roles, and our own organization are maintained as <em>Account </em>records (which we relabled as <em>Organizations</em>)</li>
	<li><strong>Opportunities</strong> - Our IATI activities correspond to received grants, which are kept as <em>Opportunity</em> records with a distinct record type &quot;grant&quot;</li>
	<li><strong>Contacts</strong> - People in various roles are all managed as <em>Contact </em>records</li>
</ul>

<h3>Custom Fields on Standard Objects</h3>

<p>We used standard fields where appropriate, and created a number of custom fields for managing and publishing our grant (activity) data.</p>

<ul>
	<li><strong>Accounts</strong>

	<ul>
		<li><code>IATI ID</code><em> </em>[text] - IATI identifier for the organization, if available</li>
		<li><code>IATI organization type</code> [picklist] - <a href="http://iatistandard.org/201/codelists/OrganisationType/" target="_blank">IATI codelist</a> of organization types</li>
		<li><code>IATI organization type code</code> [formula(text)] - Auto-assigns the type code from <a href="http://iatistandard.org/201/codelists/OrganisationType/" target="_blank">IATI codelist</a> based on organization type selected</li>
	</ul>
	</li>
	<li><strong>Opportunities</strong>
	<ul>
		<li><code>close date ISO</code> [formula(text)] - ISO date format of the standard field <em>close date</em>, done this way because Apex and VisualForce support for robust date formatting is lacking</li>
		<li><code>cost center</code> [text] - internal grant/activity identifier, used in creating the unique IATI activity identifier, and also reported in an &quot;other-identifier&quot; element</li>
		<li><code>currency</code> [lookup(currency)] - identifies the currency in which the grant award was given by relating to a valid currency in the custom <em>Currency</em> object (described below)</li>
		<li><code>donor grant number</code> [text] - grant identifier used by the donor, reported in an &quot;other-identifier&quot; element</li>
		<li><code>grant end date</code> [date] - end date for grant/activity</li>
		<li><code>grant manager</code> [lookup(contact)] - reported as the contact for this grant/activity</li>
		<li><code>grant new end date</code> [date] - grant extension (modified) end date, if applicable</li>
		<li><code>grant new end date ISO</code> [formula(text)] - ISO date format of the <em>grant new end date</em> custom field</li>
		<li><code>grant start date</code> [date] - grant/activity start date</li>
		<li><code>grant start date ISO</code> [formula(text)] - ISO date format of the <em>grant start date</em> custom field</li>
		<li><code>IATI activity ID</code> [formula(text)] - concatenates our IATI organization identifier with the <em>cost center</em> for this grant/activity</li>
		<li><code>IATI activity status code</code> [formula(text)] - assigns the appropriate <a href="http://iatistandard.org/201/codelists/ActivityStatus/" target="_blank">IATI status code</a> based on the stage selected in the standard field <em>Stage</em></li>
		<li><code>notification date</code> [date] - grant contract date, reported within the &quot;transaction&quot;<em> </em>element</li>
		<li><code>notification date ISO</code> [formula(text)] - ISO date format of the <em>notification date</em> custom field</li>
		<li><code>prime/lead grant number</code> [text] - grant identifier used by lead/prime organization, when award is a subgrant, reported in an &quot;other-identifier&quot; element</li>
		<li><code>prime/lead organization</code> [lookup(Account)] - identifies the prime awardee of donor funds</li>
		<li><code>project website</code> [URL] - website associated with this grant/activity</li>
	</ul>
	</li>
	<li><strong>Contacts </strong>(no custom fields used)</li>
</ul>

<h2>Custom Objects</h2>

<p>Custom objects here have two general functions: to maintain vocabularies, or large codelists, with their metadata and to manage &quot;many-to-many&quot; relationships between other objects.</p>

<h3>Vocabularies</h3>

<ul>
	<li><strong>Countries</strong> - list of valid countries with additional information available for each record</li>
	<li><strong>Sectors </strong>- list of valid sectors divided by record types; in this case, record type is &quot;OECD DAC3&quot; and the records match those in the <a href="http://iatistandard.org/201/codelists/SectorCategory/" target="_blank">IATI codelist</a></li>
	<li><strong>Currencies </strong>- list of valid currencies, matching those in the <a href="http://iatistandard.org/201/codelists/Currency/" target="_blank">IATI codelist</a></li>
</ul>

<h3>Relationships</h3>

<ul>
	<li><strong>Opportunity Countries</strong> - records pairwise relationships between an <em>Opportunity</em> record and a <em>Country</em> record</li>
	<li><strong>Opportunity Sectors</strong> - records pairwise relationships between an <em>Opportunity </em>record and a <em>Sector</em> record</li>
	<li><strong>Grant Partners</strong> - records pairwise relationships between an <em>Opportunity</em> record and an <em>Account</em> record, denoting implementing partners</li>
</ul>

<h3>Custom Fields on Custom Objects</h3>

<ul>
	<li><strong>Countries</strong>

	<ul>
		<li><code>ISO-2 code</code> [text(2)(unique case insensitive)] - the ISO 3166-1 standarized 2-digit code for the country</li>
	</ul>
	</li>
	<li><strong>Sectors</strong>
	<ul>
		<li><code>code</code> [text] - abbreviated code for the sector, in this case matching the <a href="http://iatistandard.org/201/codelists/SectorCategory/" target="_blank">IATI codelist</a> for OECD DAC3 sectors</li>
	</ul>
	</li>
	<li><strong>Currencies</strong>
	<ul>
		<li><code>currency code</code> [text(3)(unique case insensitive)] - the ISO 4217 standarized 3-digit code for the currency, in the <a href="http://iatistandard.org/201/codelists/Currency/" target="_blank">IATI codelist</a></li>
	</ul>
	</li>
	<li><strong>Opportunity Countries</strong>
	<ul>
		<li><code>country </code>[master-detail(country)] - reference to a <em>Country</em></li>
		<li><code>opportunity</code> [master-detail(opportunity)] - reference to an <em>Opportunity </em>(grant)</li>
	</ul>
	</li>
	<li><strong>Opportunity Sectors</strong>
	<ul>
		<li><code>opportunity</code> [master-detail(opportunity)] - reference to an <em>Opportunity </em>(grant)</li>
		<li><code>sector </code>[master-detail(sector)] - reference to a <em>Sector</em></li>
	</ul>
	</li>
	<li><strong>Grant Partners</strong>
	<ul>
		<li><code>grant </code>[master-detail(opportunity)] - reference to an <em>Opportunity </em>(grant)</li>
		<li><code>partner </code>[master-detail(account)] - reference to an <em>Account</em> (implementing partner organization)</li>
	</ul>
	</li>
</ul>

<h2>Apex Class</h2>

<p>We developed a simple, custom apex controller class to power the VisualForce page.&nbsp; The custom SOQL query is the crucial part of this class, allowing access to relation fields not possible using the standard controller.</p>

<h2>Apex Test Class</h2>

<p>Salesforce is strict with its code testing requirements, so the custom class needed its own test class to validate the code and maintain sufficient test code coverage when deploying.</p>

<h2>VisualForce Page</h2>

<p>The VisualForce page uses the custom Apex class as its custom controller, giving it access to relational fields it would not have otherwise.&nbsp; As of writing, Salesforce does not allow direct XML output from a VisualForce page, so it is rendered as a text document download, but in valid XML.&nbsp;</p>

<p>Conditional output is handled using Apex&#39;s <em>rendered</em> attribute.</p>

<p>Variable-length lists are handled using Apex&#39;s <em>repeat</em> element.</p>

<p>Once the page is created, it may be accessed in a number of ways. In our case, we added a new tab to a separate Salesforce App that links the VisualForce page URL, which triggers a download of the formatted XML file.</p>

