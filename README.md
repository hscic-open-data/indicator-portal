# Process for updating NHS Digital Indicator Portal datasets for data.gov.uk

At present the following domains are included: NHSOF, CCGOIS, POMI.
Each month, the portal is updated. DGU can harvest the metadata for these domains, as long as there is an associated json file.
The indicator portal produces xml schemas for each indicator, so the general process is to convert the xml schemas into a json file capable of being read by DGU.
This is acheived by the following steps:
1. Combine the xml schemas for the individual indicators within each domain , so that there is one schema per domain
2. Utilise Google Refine to convert this xml to a json file, using the data.json format
3. Save the json file to github
4. Refresh the harvests within DGU.


## 1. Combine the xml schemas

- Utilise a combination of the portal indicator list (https://indicators.hscic.gov.uk/download/Indicator%20Portal%20indicators.xls) to get a list of the indicator IDs (eg. P02154 etc).

- Use Excel to append ".xml" to each and create a string as follows: "P02154.xml+P02155.xml+P02156.xml......." so that all the IDs for each domain are included.

- Use the command prompt (In windows explorer, shift+right click on te folder containing the xmls files, open command window here) to copy all of the xml files into one new, combined xml file. The synatx is:

	copy P00001.xml+P00002.xml+.... P_NHSOF.xml

	where P_OF.xml is the new file to be created (P_NHSOF.xml, P_CCGOIS.xml, P_POMI.xml etc)

- This creates a concatenation of all the files, however this is not a valid xml file - there are 2 further transformations required:

	In a text editor (I prefer Notepad++):
	1. find and replace all instances of **\<?xml version="1.0" encoding="UTF-8" ?\>** with ""
	
	This removes each individual header. However, a single header is required for the overall file, so **\<?xml version="1.0" encoding="UTF-8" ?\>\<portal\>** should be added back at the very start. (Note the addition of \<portal\>)

	2. Add **\</portal\>** to the very end of the file

- Finally, check that the new file is a valid xml using an xml validator (such as https://www.w3schools.com/xml/xml_validator.asp)

- Save the file.


## 2. Convert to a json file (using Google Refine)

Google refine is useful for test and cell conversion, and the ability to apply saved steps (recipes). The xml schema can be opened in Excel as a tabular representation, however nested elements (items in lists) make the file difficult to work with.

- In Google Refine, create a new project. Import the combined xml file and hit Next to proceed. On the next screen, click on the first level (**\<portal\>**) to ensure that the entire file is loaded. Then click Create Project. 

	- There should be one record per indicator, and several rows per record.

- Under the Undo / Redo menu, select Apply... and paste the appropriate recipe. Select Perform Operations to apply the steps.

	- This changes the original format by compressing the records, and transforming various columns. There should now be one row per record
	
- The records are now ready to be exported. Under the Export menu (top right), select Templating... This section defines how the json output will be structured.

	- Replace the Row Template with the appropriate template file
	- Prefix should be **\[** (open square bracket)
	- Row Separator should be **,** (comma)
	- Suffix should be **\]** (close square bracket)

- Click **Export** to create a json file. 

- Finally, validate the file using https://jsonlint.com/


## 3. Saving the new josn file to Github

The new json file needs to be saved to github (so that the DGU can read it).

- sign in to github and find the Indicator Portal project (https://github.com/hscic-open-data/indicator-portal)

- open the appropriate harvest source file:
	- portal-ccgois.json
	- portal-nhsof.json
	- portal-pomi.json

- click on the pen icon to edit the file

- replace the entire contents with the text from the new josn file created in step 2.

- click Commit to save changes.

The link should be updated with the latest data. To see the clean version of the file, select the **Raw** tab - this the file without the github page furniture, and is the version that DGU reads.

## 4. Refreshing the DGU harvest

The harvests are already set up:

	* https://data.gov.uk/harvest/hscic-pomi
	* https://data.gov.uk/harvest/hscic-ccgois
	* https://data.gov.uk/harvest/hscic-nhsof

To refresh, it is simply a case of clicking **Refresh Source**. This tells DGU to go back to the json file and re-import the metadata. All previous indicators should be overwritten, with the DGU dataset links remaining the same.


## 5. Support and Guidance

If any errors occur, the data.gov.uk team provide support: https://data.gov.uk/contact

Guidance on the harvest process is available on the DGU pages: 

	- http://guidance.data.gov.uk/
	- http://guidance.data.gov.uk/harvesting.html
